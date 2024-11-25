---
title: 'Azure ExpressRoute: 高可用性を実現する設計'
description: このページでは、Azure ExpressRoute を使用しつつ高可用性を実現するためのアーキテクチャ上の推奨事項を示します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 5dcf58dce7b87862c2f01ad76db8aff66366ee4b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733872"
---
# <a name="designing-for-high-availability-with-expressroute"></a>ExpressRoute を使用した高可用性のための設計

ExpressRoute は、Microsoft リソースへのキャリア グレード プライベート ネットワーク接続を提供する高可用性のために設計されています。 つまり、Microsoft ネットワーク内の ExpressRoute パスには単一障害点はありません。 可用性を最大化するには、ExpressRoute 回線の顧客およびサービス プロバイダー セグメントも高可用性のために設計する必要があります。 この記事では、まず、ExpressRoute を使用して堅牢なネットワーク接続を構築するためのネットワーク アーキテクチャの考慮事項について説明します。次に、ExpressRoute 回線の高可用性を向上させるために役立つ微調整機能について説明します。

>[!NOTE]
>この記事で説明する概念は、Virtual WAN で、またはその外部で ExpressRoute 回線を作成する場合にも同様に適用されます。
>

## <a name="architecture-considerations"></a>アーキテクチャの考慮事項

次の図は、ExpressRoute 回線の可用性を最大化するための、ExpressRoute 回線を使用した推奨接続方法を示しています。

 [![1]][1]

高可用性のためには、エンドツーエンド ネットワーク全体で ExpressRoute 回線の冗長性を維持することが重要です。 つまり、オンプレミス ネットワーク内の冗長性を維持する必要があり、サービス プロバイダー ネットワーク内の冗長性を低下させてはなりません。 冗長性を最小限に維持することは、ネットワークの単一障害点を回避することを意味します。 ネットワーク デバイスの電源と冷却装置を冗長化すると、高可用性がさらに向上します。

### <a name="first-mile-physical-layer-design-considerations"></a>ファースト マイル物理層設計の考慮事項

 同じ顧客構内設備 (Customer Premises Equipment: CPE) 上で ExpressRoute 回線のプライマリ接続とセカンダリ接続の両方を終端化すると、オンプレミス ネットワーク内での高可用性が低下します。 さらに、プライマリ接続とセカンダリ接続の両方を、CPE の同じポートを介して (2 つの接続を異なるサブインターフェイス下で終端化するか、パートナー ネットワーク内で 2 つの接続をマージすることによって) 構成する場合、パートナーのネットワーク セグメントで高可用性が低下することをパートナーに強要することになります。 このような低下を次の図に示します。

[![2]][2]

一方、ExpressRoute 回線のプライマリ接続とセカンダリ接続を異なる地理的位置で終端化すると、接続のネットワーク パフォーマンスが低下する可能性があります。 異なる地理的位置で終端化されているプライマリ接続とセカンダリ接続にまたがってトラフィックの積極的な負荷分散を図る場合、2 つのパス間でネットワーク待ち時間に潜在的に大きな差があり、結果的に準最適なネットワーク パフォーマンスとなります。 

地理冗長設計の考慮事項については、[ExpressRoute を使用したディザスター リカバリーの設計][DR]に関する記事を参照してください。

### <a name="active-active-connections"></a>アクティブ/アクティブ接続

Microsoft ネットワークは、ExpressRoute 回線のプライマリ接続とセカンダリ接続がアクティブ/アクティブ モードで動作するように構成されています。 ただし、ルート アドバタイズを通じて、ExpressRoute 回線の冗長接続を強制的にアクティブ/パッシブ モードで動作させることができます。 より具体的なルートをアドバタイズすること、また BGP AS パスを先頭に追加することは、あるパスを他のパスよりも優先させるために使われる一般的な手法です。

高可用性を向上させるために、ExpressRoute 回線の両方の接続をアクティブ/アクティブ モードで動作させることをお勧めします。 接続をアクティブ/アクティブ モードで動作させると、Microsoft ネットワークはフローごとにトラフィックの負荷を接続間で分散させます。

ExpressRoute 回線のプライマリ接続とセカンダリ接続をアクティブ/パッシブ モードで実行すると、アクティブ パスで障害が発生した後に両方の接続が失敗するリスクがあります。 スイッチ オーバー失敗の一般的な原因は、パッシブ接続のアクティブな管理の欠如と、パッシブ接続による古いルートのアドバタイズです。

または、ExpressRoute 回線のプライマリ接続とセカンダリ接続をアクティブ/アクティブ モードで実行すると、ExpressRoute 接続が失敗した後、フローの約半分だけが失敗して再ルーティングされる結果になります。 したがって、アクティブ/アクティブ モードは平均復旧時間 (Mean Time To Recover: MTTR) の大幅な改善に役立ちます。

> [!NOTE]
> Microsoft では、メンテナンス アクティビティの期間、またはいずれかの接続に影響を与える計画外のイベント時に、AS パス プリペンディングを使用して、正常な接続にトラフィックをドレインする方法を優先的に使用します。 Microsoft によりパス プリペンドが構成されている場合にトラフィックを正常なパスにルーティングできること、およびサービスの中断が発生しないように、必要なルート アドバタイズが適切に構成されていること確認する必要があります。 
> 

### <a name="nat-for-microsoft-peering"></a>Microsoft ピアリング用の NAT 

Microsoft ピアリングは、パブリック エンドポイント間の通信のために設計されています。 そのため、オンプレミスのプライベート エンドポイントは、Microsoft ピアリングを使用して通信する前に、顧客またはパートナー ネットワーク上のパブリック IP を使用してネットワーク アドレス変換 (NAT) が行われるのが一般的です。 プライマリ接続とセカンダリ接続の両方をアクティブ/アクティブ モードで使用するとした場合、NAT の場所と方法は、ExpressRoute 接続の一方で障害が発生した後の復旧速度に影響を及ぼします。 2 つの異なる NAT オプションを次の図に示します。

[![3]][3]

#### <a name="option-1"></a>オプション 1: 

ExpressRoute 回線のプライマリ接続とセカンダリ接続の間でトラフィックを分割した後に NAT が適用されます。 NAT のステートフル要件を満たすために、独立した NAT プールがプライマリとセカンダリのデバイスに使用されます。 返されるトラフィックは、フローの送信に使用されたものと同じエッジ デバイスに到着します。

ExpressRoute 接続が失敗した場合、対応する NAT プールに到達する機能が切断されます。 そのため、対応するウィンドウのタイムアウト後、すべての失われたネットワーク フローを TCP またはアプリケーション層によって再確立する必要があります。 障害発生時には、ExpressRoute 回線のプライマリまたはセカンダリの接続のいずれかに対する接続が復元されるまで、Azure は対応する NAT を使用してオンプレミス サーバーに到達できません。

#### <a name="option-2"></a>オプション 2:

ExpressRoute 回線のプライマリ接続とセカンダリ接続の間でトラフィックを分割する前に、共通 NAT プールが使用されます。 トラフィックを分割する前の共通 NAT プールが単一障害点となって高可用性を低下させることはない、という点を明確にしておくことが重要です。

プライマリまたはセカンダリの接続に失敗した後でも、NAT プールに到達できます。 そのため、ネットワーク層自体がパケットを再ルーティングし、障害が発生した後に迅速に回復することができます。 

> [!NOTE]
> * NAT オプション 1 (プライマリとセカンダリの ExpressRoute 接続で別々の NAT プール) を使用し、どちらかの NAT プールからの IP アドレスのポートをオンプレミス サーバーにマップする場合、対応する接続が失敗すると、サーバーは ExpressRoute 回線経由で到達できなくなります。
> * ステートフル デバイスで ExpressRoute BGP 接続を終了すると、Microsoft または ExpressRoute プロバイダーによる計画または計画外のメンテナンス期間にフェールオーバーの問題が発生する可能性があります。 設定をテストして、トラフィックが適切にフェールオーバーされることを確認し、可能な場合は、ステートレス デバイスで BGP セッションを終了する必要があります。

## <a name="fine-tuning-features-for-private-peering"></a>プライベート ピアリングのための微調整機能

このセクションでは、ExpressRoute 回線の高可用性向上に役立つオプションの (Azure のデプロイの様態や MTTR の重視度合いに応じた) 機能について確認します。 具体的には、ExpressRoute 仮想ネットワーク ゲートウェイのゾーン対応デプロイと、双方向フォワーディング検出 (Bidirectional Forwarding Detection: BFD) について見ていきます。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性ゾーン対応 ExpressRoute 仮想ネットワーク ゲートウェイ

Azure リージョン内の可用性ゾーンは、障害ドメインと更新ドメインを組み合わせたものです。 ゾーン冗長 Azure IaaS デプロイを選択した場合、ExpressRoute プライベート ピアリングを終端化するゾーン冗長仮想ネットワーク ゲートウェイを構成することもできます。 詳細については、「[Azure Availability Zones でのゾーン冗長仮想ネットワーク ゲートウェイについて][zone redundant vgw]」を参照してください。 ゾーン冗長仮想ネットワーク ゲートウェイを構成するには、「[Azure Availability Zones にゾーン冗長仮想ネットワーク ゲートウェイを作成する][conf zone redundant vgw]」を参照してください。

### <a name="improving-failure-detection-time"></a>障害検出時間の改善

ExpressRoute はプライベート ピアリングを介した BFD をサポートします。 Microsoft Enterprise Edge (MSEE) とオンプレミス側の BGP ネイバーの間で、BFD により、レイヤー 2 ネットワークでの障害検出時間が約 3 分 (既定) から 1 秒未満に短縮されます。 高速な障害検出時間は、障害復旧の迅速化に寄与します。 詳細については、「[ExpressRoute 経由の BFD の構成][BFD]」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、ExpressRoute 回線接続の高可用性を設計する方法について説明しました。 ExpressRoute 回線のピアリング ポイントは地理的位置に固定されるため、位置全体に影響を及ぼす壊滅的な障害の影響を受ける可能性があります。 

リージョン全体に影響する壊滅的な障害にも耐えられる Microsoft バックボーンへの地理冗長ネットワーク接続を構築するための設計上の考慮事項については、[ExpressRoute プライベート ピアリングを使用してディザスター リカバリーの設計][DR]に関する記事を参照してください。

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "ExpressRoute を使用した推奨接続方法"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "準最適なラスト マイル接続"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT オプション"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
