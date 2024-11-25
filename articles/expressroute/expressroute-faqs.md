---
title: よくあるご質問 (FAQ) - Azure ExpressRoute | Microsoft Docs
description: ExpressRoute の FAQ には、サポートされている Azure サービス、料金、データと接続、SLA、プロバイダーと提供地域、帯域幅、およびその他の技術的な詳細に関する情報が記載されています。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: 7f1787d8b1d074350ce42a98635bb8bb89c4aa75
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501478"
---
# <a name="expressroute-faq"></a>ExpressRoute の FAQ

## <a name="what-is-expressroute"></a>ExpressRoute とは何ですか。

ExpressRoute は、Microsoft のデータセンターとオンプレミスや共用施設にあるインフラストラクチャの間にプライベート接続を作成できる Azure サービスです。 ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>ExpressRoute とプライベート ネットワーク接続を使用する利点は何ですか。

ExpressRoute 接続はパブリックなインターネットを経由しません。 この接続は、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も一貫して短く、高速です。 オンプレミスのデバイスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合があります。

### <a name="where-is-the-service-available"></a>このサービスはどこで使用できますか。

サービスの場所と可用性については、[ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事をご覧ください。

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>ExpressRoute のパートナー通信会社のいずれとも契約していない場合は、どのようにして ExpressRoute を使用して Microsoft に接続できますか。

地域の通信会社と地上イーサネット接続を選択して、Exchange プロバイダーの所在地のいずれかに接続します。 その後、このプロバイダーの所在地で Microsoft とピアリングできます。 [ExpressRoute パートナーと場所](expressroute-locations.md) に関するページの最後のセクションで、サービス プロバイダーが Exchange の提供地域のいずれかに存在するかどうかを確認してください。 存在すれば、サービス プロバイダーを通じて ExpressRoute 回線を Azure に接続するように依頼できます。

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute の料金はいくらですか。

料金情報については、「 [ExpressRoute 料金](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>特定の帯域幅の ExpressRoute 回線を購入した場合、この帯域幅はイングレス トラフィックとエグレス トラフィックに個別に割り当てられますか？

はい、ExpressRoute 回線の帯域幅は二重です。 たとえば、200 Mbps の ExpressRoute 回線を購入した場合、イングレス トラフィックの場合は 200 Mbps、エグレス トラフィックの場合は 200 Mbps を調達していることになります。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、ネットワーク サービス プロバイダーから購入するプライベート接続は同じ速度である必要がありますか。

いいえ。 サービス プロバイダーから任意の速度のプライベート接続を購入できます。 ただし、Azure への接続は、購入した ExpressRoute 回線の帯域幅に制限されます。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、購入した帯域幅を超えて使用することはできますか。

はい。ExpressRoute 回線のセカンダリ接続で使用可能な帯域幅を使用して、購入した帯域幅上限の 2 倍まで使用できます。 回線の組み込みの冗長化は、2 つの Microsoft Enterprise Edge ルーター (MSEE) への、それぞれ購入した帯域幅であるプライマリ接続とセカンダリ接続を使用して構成されます。 セカンダリ接続経由で使用できる帯域幅は、必要に応じて追加のトラフィックに使用できます。 ただし、セカンダリ接続は冗長性を目的としているため、それは保証されず、長期間の追加トラフィックには使用しないでください。 両方の接続を使用して、トラフィックを送信する方法の詳細については、「[AS PATH プリペンドの使用](./expressroute-optimize-routing.md#solution-use-as-path-prepending)」を参照してください。

プライマリ接続のみを使用してトラフィックを送信する予定である場合は、接続の帯域幅が固定され、オーバーサブスクライブしようとすると、パケットのドロップが増加します。 トラフィックが ExpressRoute ゲートウェイを通過する場合、ゲートウェイ SKU の帯域幅は固定され、かつバーストに対応していません。 各ゲートウェイ SKU の帯域幅については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md#aggthroughput)」を参照してください。

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>同じプライベート ネットワーク接続を、仮想ネットワークや他の Azure サービスに同時に使用できますか。

はい。 ExpressRoute 回線のセットアップが完了すると、1 本の回線で仮想ネットワーク内のサービスや他の Azure サービスに同時にアクセスできるようになります。 仮想ネットワークにはプライベート ピアリング パス経由で接続し、他のサービスには Microsoft ピアリング パス経由で接続します。

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>ExpressRoute プライベート ピアリングで VNet はどのようにアドバタイズされますか?

ExpressRoute ゲートウェイでは、Azure VNet の "*アドレス空間*" がアドバタイズされます。サブネット レベルで含めたり除外したりすることはできません。 アドバタイズされるのは、常に VNet アドレス空間です。 また、VNet ピアリングが使用され、ピアリングされた VNet で [リモート ゲートウェイを使用する] が有効な場合、ピアリングされた VNet のアドレス空間もアドバタイズされます。

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>ExpressRoute プライベート ピアリングで VNet からオンプレミスにアドバタイズできるプレフィックスの数はどのくらいですか?

1 つの ExpressRoute 接続で、またはゲートウェイ転送を使用する VNet ピアリングによって、最大 1000 の IPv4 プレフィックスがアドバタイズされます。 たとえば、ExpressRoute 回線に接続された 1 つの VNet に 999 個のアドレス空間がある場合、それらのプレフィックスの 999 個すべてがオンプレミスにアドバタイズされます。 または、"リモート ゲートウェイを許可する" オプションを使用して、1 つのアドレス空間と 500 個のスポーク VNet が有効になったゲートウェイ転送を許可するように VNet が有効になっている場合は、ゲートウェイでデプロイされた VNet は 501 個のプレフィックスをオンプレミスにアドバタイズします。

デュアルスタック回線を使用している場合は、1 つの ExpressRoute 接続で、またはゲートウェイ転送を使用する VNet ピアリングによって、最大 100 の IPv6 プレフィックスがアドバタイズされます。 これは、上記の制限に加えて必要となります。

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>ExpressRoute 接続のプレフィックス制限を超えるとどうなりますか?

ExpressRoute 回線とゲートウェイ (および該当する場合、ゲートウェイ転送を使用するピアリングされた Vnet) との間の接続が停止します。 これは、プレフィックスの制限を超えなくなると、再確立されます。  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>オンプレミス ネットワークからのルートをフィルター処理できますか?

ルートをフィルター処理する、または含める唯一の方法は、オンプレミスのエッジ ルーター上にあります。 VNet にユーザー定義ルートを追加して特定のルーティングに影響を与えることはできますが、これは静的であり、BGP アドバタイズの一部ではありません。

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute ではサービス レベル アグリーメント (SLA) は提供されますか。

詳しくは、[ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) ページをご覧ください。

## <a name="supported-services"></a>サポートされているサービス

ExpressRoute では、プライベート ピアリング、Microsoft ピアリング、パブリック ピアリングといったさまざまな種類のサービスについて、[3 つのルーティング ドメイン](expressroute-circuit-peerings.md)がサポートされています (非推奨)。

### <a name="private-peering"></a>プライベート ピアリング

**サポート対象:**

* すべての仮想マシンとクラウド サービス ([Azure Virtual Desktop RDP Shortpath](../virtual-desktop/shortpath.md) など) を含む仮想ネットワーク

### <a name="microsoft-peering"></a>Microsoft ピアリング

ExpressRoute 回線が Azure Microsoft ピアリングに対して有効になっている場合は、Azure 内で使用されている[パブリック IP アドレスの範囲](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses)に回線経由でアクセスできます。 Azure Microsoft ピアリングを使用すると、現在 Azure でホストされているサービスにアクセスできます (ご利用の回線の SKU によっては geo 制限があります)。 特定のサービスの可用性を検証するには、そのサービスに関するドキュメントを調べて、そのサービスに対して発行された予約済みの範囲があるかどうかを確認します。 次に、ターゲット サービスの IP 範囲を参照し、[Azure IP 範囲とサービス タグ – パブリック クラウド XML ファイル](https://www.microsoft.com/download/details.aspx?id=56519)に関するページに一覧されている範囲と比較します。 あるいは、問題のサービス用のサポート チケットを開いて、明確にすることもできます。

**サポート対象:**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI - Azure リージョン コミュニティを介して使用できます。Power BI テナントのリージョンを確認する方法については、[こちら](/power-bi/service-admin-where-is-my-tenant-located)をご覧ください。
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure Global Services コミュニティ)
* IaaS (Virtual Machine、Virtual Network ゲートウェイ、Load Balancer など) の Azure パブリック IP アドレス  
* 他のほとんどの Azure サービスもサポートされています。 サポートの確認に使うサービスで直接確認してください。

**サポート対象外:**

* CDN
* Azure Front Door
* Multi-Factor Authentication Server (レガシ)
* Traffic Manager
* Logic Apps

### <a name="public-peering"></a>パブリック ピアリング

新しい ExpressRoute 回線では、パブリック ピアリングは無効になっています。 Azure サービスは、Microsoft ピアリングで利用できるようになりました。 パブリック ピアリングが非推奨となる前に作成された回線の場合は、必要なサービスに応じて、Microsoft ピアリングまたはパブリック ピアリングのどちらを使用するかを選択できます。

パブリック ピアリングの詳細と構成の手順については、[ExpressRoute パブリック ピアリング](about-public-peering.md)に関するページを参照してください。

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Microsoft のピアリングの構成中に、[アドバタイズされたパブリック プレフィックス] の状態が [検証が必要です] と表示されるのはなぜですか。

Microsoft は、指定された 'アドバタイズされたパブリック プレフィックス' と 'ピア ASN' (または '顧客 ASN') がインターネット ルーティング レジストリでユーザーに割り当てられているかどうかを確認します。 別のエンティティからパブリック プレフィックスを取得している場合、およびルーティング レジストリに割り当てが記録されていない場合、自動検証は完了せず、手動検証が必要になります。 自動検証が失敗した場合は、[検証が必要です] というメッセージが表示されます。

'検証が必要です' というメッセージが表示された場合は、ルーティング レジストリにプレフィックスの所有者として一覧表示されているエンティティによって組織にパブリック プレフィックスが割り当てられていることを示すドキュメントを収集し、下に示すようにサポート チケットを開くことにより、これらのドキュメントを手動検証のために送信してください。

!["Proof of ownership for public prefixes" の新しいサポート リクエスト (サポート チケット) を示すスクリーンショット。](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Dynamics 365 は ExpressRoute でサポートされていますか。

Dynamics 365 および Common Data Service (CDS) 環境は Azure でホストされているため、お客様はその基礎となっている Azure リソース向け ExpressRoute のサポートを利用できます。 このサービス エンドポイントに接続できるのは、Dynamics 365/CD(CDS) 環境がホストされている Azure リージョンが、ルーター フィルターに含まれている場合です。

> [!NOTE]
> ExpressRoute 回線が同じ [地政学的リージョン](./expressroute-locations-providers.md#expressroute-locations)内にデプロイされている場合、Azure ExpressRoute 経由の Dynamics 365 接続には [ExpressRoute Premium](#expressroute-premium) は必要 **ありません**。

## <a name="data-and-connections"></a>データおよび接続

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>ExpressRoute を使用して転送できるデータの量に制限はありますか。

データ転送量に対する制限はありません。 帯域幅ごとの料金については、「 [ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute でサポートされる接続速度はどれくらいですか。

サポートされる帯域幅:

50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps

### <a name="which-service-providers-are-available"></a>どのサービス プロバイダーを使用できますか。

サービス プロバイダーとサービスの場所の一覧については、 [ExpressRoute のパートナーと提供地域](expressroute-locations.md) に関するページを参照してください。

## <a name="technical-details"></a>技術的な詳細

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>オンプレミスの場所から Azure に接続するための技術的な要件は何ですか。

要件については、 [ExpressRoute の前提条件に関するページ](expressroute-prerequisites.md)を参照してください。

### <a name="are-connections-to-expressroute-redundant"></a>ExpressRoute への接続は冗長化されていますか。

はい。 各 ExpressRoute 回線にクロス接続の冗長ペアが設定され、高可用性を実現するように構成されています。

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>ExpressRoute リンクのいずれかに障害が発生すると接続できなくなりますか。

クロス接続の一方に障害が発生しても、接続が失われることはありません。 ネットワークの負荷をサポートし、ExpressRoute 回線の高可用性を得るために、冗長接続を利用できます。 さらに、回線を別のピアリング場所に作成して、回線レベルの回復力を実現できます。

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>プライベート ピアリングに冗長性を確保するにはどうすればよいですか。

異なるピアリングの場所から複数の ExpressRoute 回線を、または同じピアリングの場所からの最大 4 つの接続を同じ仮想ネットワークに接続することで、1 つの回線が使用できない状態になった場合の高可用性を確保することができます。 さらに、ローカル接続のいずれかに、[より大きな重み](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection)を割り当てることで特定の回線を優先指定できます。 単一障害点を回避するために、少なくとも 2 つの ExpressRoute 回線をセットアップすることを強くお勧めします。 

高可用性のための設計については[こちら](./designing-for-high-availability-with-expressroute.md)を、ディザスター リカバリーのための設計については[こちら](./designing-for-disaster-recovery-with-expressroute-privatepeering.md)をご覧ください。  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Microsoft ピアリングに冗長性を確保するにはどうすればよいですか。

Microsoft ピアリングを使用して Azure のパブリック サービス (Azure Storage、Azure SQL など) にアクセスしているお客様や、Microsoft 365 向け Microsoft ピアリングを使用しているお客様には、単一障害点を回避するために、異なるピアリングの場所に複数の回線を導入することを強くお勧めします。 両方の回線で同じプレフィックスをアドバタイズして [AS PATH プリペンド](./expressroute-optimize-routing.md#solution-use-as-path-prepending)を使用するか、または異なるプレフィックスをアドバタイズしてオンプレミスからのパスを特定することができます。

高可用性のための設計については[こちら](./designing-for-high-availability-with-expressroute.md)をご覧ください。

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>ExpressRoute に接続されている仮想ネットワークで高可用性を確保する方法

同じピアリング場所にある最大 4 つの ExpressRoute 回線を仮想ネットワークに接続するか、異なるピアリング場所 (例: Singapore、Singapore2) にある最大 16 の ExpressRoute 回線を仮想ネットワークに接続することで、高可用性を実現できます。 1 つの ExpressRoute サイトがダウンした場合、接続は別の ExpressRoute サイトにフェールオーバーされます。 仮想ネットワークを離れるトラフィックは、既定で Equal Cost Multi-path Routing (ECMP) に基づいてルーティングされます。 接続の重みを使用して、ある回線を別の回線よりも優先することができます。 詳細については、「[ExpressRoute ルーティングの最適化](expressroute-optimize-routing.md)」を参照してください。

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Microsoft ピアリングまたはパブリック ピアリングで Azure Storage や Azure SQL などの Azure パブリック サービス宛てのトラフィックが、ExpressRoute パスで確実に優先されるようにするにはどうしたらよいですか?

オンプレミスから Azure へのパスが ExpressRoute 回線で確実に優先されるようにするには、お使いのルーターに *Local Preference* 属性を実装する必要があります。

BGP パスの選択と一般的なルーター構成に関する追加情報については、[こちら](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings)を参照してください。 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>クラウド エクスチェンジで併置しておらず、サービス プロバイダーがポイント ツー ポイント接続を提供している場合は、オンプレミス ネットワークと Microsoft 間の物理接続を 2 つ注文する必要がありますか。

サービス プロバイダーが物理接続経由で 2 つのイーサネット仮想回線を確立できる場合は、必要な物理接続は 1 つだけです。 物理接続 (光ファイバーなど) は、レイヤー 1 (L1) デバイスで終端します (図を参照)。 2 つのイーサネット仮想回線は、異なる VLAN ID (プライマリ回線とセカンダリ回線の VLAN ID) でタグ付けされます。 これらの VLAN ID は、外部 802.1Q イーサネット ヘッダーに含まれます。 内部 802.1Q イーサネット ヘッダー (ここでは示されていません) は、特定の [ExpressRoute ルーティング ドメイン](expressroute-circuit-peerings.md)にマップされます。

![お客様のサイトのスイッチと ExpressRoute の場所の間の物理的な接続を構成するレイヤー 1 (L1) のプライマリとセカンダリの仮想回線が強調表示されている図。](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>自社の VLAN を、ExpressRoute を使用して Azure に拡張することはできますか。

いいえ。 Azure へのレイヤー 2 接続の拡張はサポートされません。

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>1 つのサブスクリプションで複数の ExpressRoute 回線を使用できますか。

はい。 1 つのサブスクリプションで、複数の ExpressRoute 回線をご利用いただけます。 既定の上限は 10 に設定されています。 上限を増やす必要がある場合は、Microsoft サポートにご連絡ください。

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>別のサービス プロバイダーから ExpressRoute 回線を使用することはできますか。

はい。 多くのサービス プロバイダーで ExpressRoute 回線をご利用いただけます。 各 ExpressRoute 回線は、1 つのサービス プロバイダーのみに関連付けられます。 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>同じ都市圏に 2 つの ExpressRoute のピアリング場所 (例: Singapore と Singapore2) があります。 ExpressRoute 回線を作成するのに、どちらのピアリング場所を選択する必要がありますか。
サービス プロバイダーが両方のサイトで ExpressRoute を提供している場合は、プロバイダーと協力して ExpressRoute をセットアップするサイトを選択できます。 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>同じ都市圏に複数の ExpressRoute 回線を配置することはできますか。 同じ仮想ネットワークにリンクすることはできますか。

はい。 複数の ExpressRoute 回線を配置できます。サービス プロバイダーは同じでも違っていてもかまいません。 都市圏に複数の ExpressRoute ピアリング場所があり、異なる場所で回線が作成されている場合は、同じ仮想ネットワークにそれらをリンクすることができます。 回線が同じピアリング場所で作成されている場合は、最大 4 つの回線を同じ仮想ネットワークに接続できます。

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>ExpressRoute 回線に仮想ネットワークを接続するにはどうすればいいですか。

基本的な手順は次のとおりです。

* ExpressRoute 回線を確立し、サービス プロバイダーに依頼してその回線を有効にします。
* お客様またはプロバイダーは、BGP ピアリングを構成する必要があります。
* 仮想ネットワークを ExpressRoute 回線に接続します。

詳細については、「[回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>当社の ExpressRoute 回線に接続境界はありますか。

はい。 [ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事に、ExpressRoute 回線の接続境界に関する概要が記載されています。 ExpressRoute 回線の接続は、1 つの地理的リージョンに制限されます。 ExpressRoute Premium 機能を有効にすると、地理的リージョンを越えて接続を拡張できます。

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>複数の仮想ネットワークを ExpressRoute 回線に接続できますか。

はい。 標準の ExpressRoute 回線では最大 10 の仮想ネットワークを、[ExpressRoute Premium 回線](#expressroute-premium)では最大 100 の仮想ネットワークを接続できます。 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>仮想ネットワークを含む複数の Azure サブスクリプションがあります。 個別のサブスクリプション内の仮想ネットワークを 1 つの ExpressRoute 回線に接続できますか。

はい。 回線と同じサブスクリプションまたは異なるサブスクリプションにある最大 10 個の仮想ネットワークを 1 つの ExpressRoute 回線を使用してリンクすることができます。 ExpressRoute Premium 機能を有効にすると、この上限を増やすことができます。 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになることに注意してください。すべての仮想ネットワークは同じ帯域幅を共有します。

詳細については、「[複数のサブスクリプションの間で ExpressRoute 回線を共有する](expressroute-howto-linkvnet-arm.md)」を参照してください。

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>異なる Azure Active Directory Windows Update または Enterprise Agreement 登録に関連付けられた Azure サブスクリプションが複数あります。 別のテナントおよび登録にある仮想ネットワークを、同じテナントまたは登録内にない 1 つの ExpressRoute 回線に接続することはできますか。

はい。 ExpressRoute の承認は、サブスクリプション、テナント、および登録の複数の境界にまたがることができます。追加の構成は必要ありません。 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになることに注意してください。すべての仮想ネットワークは同じ帯域幅を共有します。

詳細については、「[複数のサブスクリプションの間で ExpressRoute 回線を共有する](expressroute-howto-linkvnet-arm.md)」を参照してください。

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>同じ回線に接続されている仮想ネットワークは、互いに分離されていますか。

いいえ。 ルーティングの観点から見た場合、同じ ExpressRoute 回線に接続されているすべての仮想ネットワークは、同じルーティング ドメインに属しているため、互いに分離されていません。 ルートの分離が必要な場合は、ExpressRoute 回線を別に作成する必要があります。

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>1 つの仮想ネットワークを複数の ExpressRoute 回線に接続できますか。

はい。 1 つの仮想ネットワークを、同じ場所の最大 4 つの ExpressRoute 回線に、または異なるピアリングの場所の最大 16 の ExpressRoute 回線に接続できます。 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 回線に接続された仮想ネットワークから、インターネットにアクセスできますか。

はい。 BGP セッションを介して既定ルート (0.0.0.0/0) またはインターネット ルート プレフィックスをアドバタイズしていなければ、ExpressRoute 回線に接続された仮想ネットワークからインターネットに接続できます。

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 回線に接続されている仮想ネットワークに対するインターネット接続をブロックできますか。

はい。 既定ルート (0.0.0.0/0) をアドバタイズして仮想ネットワーク内にデプロイされた仮想マシンへのインターネット接続をすべてブロックし、すべてのトラフィックが ExpressRoute 回線を経由するようにルーティングすることができます。

> [!NOTE]
> アドバタイズされたルート 0.0.0.0/0 が (障害や設定ミスなどにより) アドバタイズされたルートから削除された場合、Azure によって、インターネットへ接続するための[システム ルート](../virtual-network/virtual-networks-udr-overview.md#system-routes)が接続された仮想ネットワーク上のリソースに提供されます。  インターネットへのエグレス トラフィックがブロックされていることを確認するため、インターネット トラフィックの送信拒否規則を持つネットワーク セキュリティ グループをすべてのサブネットに配置することをお勧めします。

ただし、既定ルートをアドバタイズすると、Microsoft ピアリング経由で提供されるサービス (Azure ストレージや SQL DB など) へのトラフィックが強制的にオンプレミスにルーティングされます。 Microsoft ピアリング パス経由またはインターネット経由でトラフィックを Azure に返すようにルーターを構成する必要があります。 サービスのサービス エンドポイントを有効にした場合、サービスへのトラフィックはオンプレミスに強制されません。 トラフィックは Azure のバックボーン ネットワーク内にとどまります。 サービス エンドポイントの詳細については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)」を参照してください。

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>同じ ExpressRoute 回線に接続されている仮想ネットワークは互いに通信できますか。

はい。 同じ ExpressRoute 回線に接続されている仮想ネットワークにデプロイされた仮想マシンは互いに通信できます。 この通信を容易にするために、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を設定することをお勧めします。

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>ExpressRoute と組み合わせて、仮想ネットワークへのサイト間 VPN 接続を設定できますか。

はい。 ExpressRoute は、サイト間 VPN と共存できます。 「[ExpressRoute 接続とサイト間接続の共存を構成する](expressroute-howto-coexist-resource-manager.md)」をご覧ください。

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>サイト間 VPN 接続と ExpressRoute の間のルーティングを有効にするにはどうすればいいですか。

ExpressRoute に接続されているブランチと、サイト間 VPN 接続に接続されているブランチとの間のルーティングを有効にするには、[Azure Route Server](../route-server/expressroute-vpn-support.md) をセットアップする必要があります。

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>仮想ネットワークで ExpressRoute ゲートウェイに関連付けられているパブリック IP アドレスが存在するのはなぜですか。

パブリック IP アドレスは内部管理にのみ使われ、仮想ネットワークのセキュリティを損なうことはありません。

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>アドバタイズできるルートの数に上限はありますか。

はい。 プライベート ピアリング用に最大 4,000 個、Microsoft ピアリング用に 200 個のルート プレフィックスを使用できます。 ExpressRoute Premium 機能を有効にすると、プライベート ピアリング用のこの上限を 10,000 ルートに増やすことができます。

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>BGP セッションを介してアドバタイズできる IP 範囲に制限はありますか。

Microsoft ピアリング BGP セッションでは、プライベート プレフィックス (RFC1918) は受け付けられません。 Microsoft とプライベートの両方の ピアリングで、任意のプレフィックス サイズ (最大 /32) を受け入れることができます。

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>BGP の上限を超えるとどうなりますか。

BGP セッションが切断されます。 プレフィックス数が上限未満になると、BGP セッションはリセットされます。

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP ホールド時間とは何ですか。 それは調整できますか。

ホールド時間は 180 です。 キープ アライブ メッセージは、60 秒ごとに送信されます。 これらは Microsoft 側の固定設定であり、変更することはできません。 異なる時間を構成できます。この構成に応じて、BGP セッションのパラメーターはネゴシエートされます。

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 回線の帯域幅を変更できますか。

はい、Azure Portal または PowerShell を使用して、ExpressRoute 回線の帯域幅を増やすことを試すことができます。 回線が作成された物理ポートで使用可能な容量があれば、変更は成功します。 

変更が失敗した場合は、現在のポートに十分な容量が残っていないために帯域幅が大きい新しい ExpressRoute 回線を作成する必要がある、ということ、またはその場所には追加の容量がないために帯域幅を増やすことはできない、ということになります。 

また、接続プロバイダーに連絡して、帯域幅の増加をサポートするようにネットワーク内の調整を更新してもらう必要があります。 ただし、ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅が小さい新しい ExpressRoute 回線を作成し、古い回線を削除する必要があります。

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 回線の帯域幅を変更するには、どうすればいいですか。

Azure Portal、REST API、PowerShell、Azure CLI を使用して、ExpressRoute 回線の帯域幅を更新できます。

### <a name="i-received-a-notification-about-maintenance-on-my-expressroute-circuit-what-is-the-technical-impact-of-this-maintenance"></a>ExpressRoute 回線でメンテナンスに関する通知を受け取りました。 このメンテナンスによってどのような技術的影響がありますか。

[アクティブ/アクティブ モード](./designing-for-high-availability-with-expressroute.md#active-active-connections)で回線を操作する場合は、メンテナンス中の影響は最小限かまったくありません。 回線のプライマリ接続とセカンダリ接続については、個別にメンテナンスを実行します。 通常、予定メンテナンスは、ピアリングの場所のタイム ゾーンで営業時間外に実行されるため、メンテナンス時間を選択することはできません。

### <a name="i-received-a-notification-about-a-software-upgrade-or-maintenance-on-my-expressroute-gateway-what-is-the-technical-impact-of-this-maintenance"></a>Azure ExpressRoute ゲートウェイでソフトウェアのアップグレードまたはメンテナンスに関する通知を受け取りました。 このメンテナンスによってどのような技術的影響がありますか。

ゲートウェイでのソフトウェアのアップグレードやメンテナンス中の影響は最小限かまったくありません。 Azure ExpressRoute ゲートウェイは複数のインスタンスで構成され、アップグレード中にインスタンスは一度に 1 つずつオフラインになります。 ゲートウェイでサポートされる仮想ネットワークへのネットワーク スループットが一時的に低下する可能性がありますが、ゲートウェイ自体にダウンタイムが発生することはありません。


## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>ExpressRoute Premium とは何ですか。

ExpressRoute Premium は、次の機能のコレクションです。

* プライベート ピアリング用ルートの上限が 4,000 から 10,000 に増加されたルーティング テーブル。
* ExpressRoute 回線で有効にできる VNet と ExpressRoute Global Reach の接続数の増加 (既定は 10)。 詳しくは、「[ExpressRoute の制限](#limits)」の表をご覧ください。
* Microsoft 365 への接続
* Microsoft のコア ネットワーク経由のグローバル接続。 ある地理的リージョンにある VNET を別のリージョン内の ExpressRoute 回線に接続できるようになりました。<br>
    **例:**

    *  西ヨーロッパで作成された VNET をシリコン バレーで作成された ExpressRoute 回線に接続できます。 
    *  Microsoft ピアリングで、他の地理的リージョンのプレフィックスがアドバタイズされ、たとえばシリコン バレーの回線から西ヨーロッパの SQL Azure に接続できるようになります。


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>ExpressRoute プレミアムを有効にした場合、ExpressRoute 回線で有効にできる VNet と ExpressRoute Global Reach の接続数はいくつですか。

次の表に、ExpressRoute の上限と、ExpressRoute 回線ごとの VNet と ExpressRoute Global Reach の数を示します。

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>ExpressRoute Premium はどのようにして有効にしますか。

ExpressRoute Premium の機能は、ExpressRoute 機能を有効にする際に有効にでき、回線の状態を更新することで無効にできます。 ExpressRoute Premium の有効化は、回線の作成時のほか、REST API や PowerShell コマンドレットを呼び出して行うこともできます。

### <a name="how-do-i-disable-expressroute-premium"></a>ExpressRoute Premium はどのようにして無効にしますか。

ExpressRoute Premium を無効にするには、REST API や PowerShell コマンドレットを呼び出します。 ExpressRoute Premium を無効にする前に、接続ニーズが既定の上限を超えて増大していないことを確認する必要があります。 使用量が既定の上限を超えて増大している場合は、ExpressRoute Premium を無効にする要求が失敗します。

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Premium の機能セットから必要な機能だけを選択できますか。

いいえ。 機能を選択することはできません。 ExpressRoute Premium を有効にすると、すべての機能が有効になります。

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute Premium の料金はいくらですか。

料金については、「 [ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>ExpressRoute Premium の料金は、標準の ExpressRoute 料金に追加して支払うのですか。

はい。 ExpressRoute Premium 料金は、ExpressRoute 回線の料金と接続プロバイダーに必要な料金に追加する形で適用されます。

## <a name="expressroute-local"></a>ExpressRoute Local

### <a name="what-is-expressroute-local"></a>ExpressRoute Local とは何ですか。

ExpressRoute Local とは、Standard SKU と Premium SKU 以外の ExpressRoute 回線の SKU のことです。 Local の主な機能は、ExpressRoute ピアリングの場所の Local 回線で、ユーザーが、同じ都市圏内またはその近くにある 1 つまたは 2 つの Azure リージョンにのみアクセスできるようにすることです。 これに対し Standard 回線では地政学的領域内のすべての Azure リージョンに、Premium 回線では世界中のすべての Azure リージョンに、ユーザーはアクセスすることができます。 具体的には、ローカルの SKU では、ExpressRoute 回線の対応するローカル リージョンから (Microsoft とプライベート ピアリング経由で) ルートをアドバタイズすることのみ可能です。 定義されたローカル リージョンとは異なる他のリージョンのルートを受け取ることはできません。

### <a name="what-are-the-benefits-of-expressroute-local"></a>ExpressRoute Local のメリットは何ですか。

お使いの ExpressRoute 回線が Standard または Premium の場合、エグレス データ転送に対して料金が発生しますが、ExpressRoute Local 回線ではエグレス データ転送に対して個別に料金が発生することはありません。 つまり、ExpressRoute Local の価格には、データ転送料金が含まれます。 転送するデータが大量にある場合、ExpressRoute Local はコスト効率の高いソリューションです。このソリューションではご自身のデータをプライベート接続経由で、必要な Azure リージョンの近くにある ExpressRoute ピアリングの場所に送信できます。 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>ExpressRoute Local ではどのような機能を使用できますか。また、何を使用できませんか。

Standard ExpressRoute 回線の機能セットとほぼ同じですが、次の機能が異なります。
* 上述した Azure リージョンへのアクセス範囲
* ExpressRoute Global Reach が Local では使用不可

ExpressRoute Local では、リソースの制限 (回線あたりの VNet 数など) も Standard と同じです。 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>ExpressRoute Local はどこで使用できますか。また、ピアリングの場所はそれぞれどの Azure リージョンにマッピングされていますか。

ExpressRoute Local は、1 つまたは 2 つの Azure リージョンが近くにあるピアリングの場所で使用できます。 ピアリングの場所の州、都道府県、国/地域に Azure リージョンがない場合、そこで使用することはできません。 正確なマッピングについては、[場所のページ](expressroute-locations-providers.md)をご覧ください。  

## <a name="expressroute-for-microsoft-365"></a>Microsoft 365 の ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Microsoft 365 サービスに接続する ExpressRoute 回線はどのようにして作成しますか。

1. [ExpressRoute の前提条件のページ](expressroute-prerequisites.md)を参照して、要件を満たしていることを確認します。
2. 接続ニーズが満たされることを確認するには、[ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事でサービス プロバイダーとサービスの場所の一覧を確認します。
3. 「[Microsoft 365 のネットワーク計画とパフォーマンス チューニング](/microsoft-365/enterprise/network-planning-and-performance)」を参照して、容量の要件を計画します。
4. [「回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー」](expressroute-workflows.md)に示されている手順に従って接続をセットアップします。

> [!IMPORTANT]
> Microsoft 365 サービスへの接続を構成するときは、ExpressRoute Premium アドオンを有効にしていることを確認します。
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>既存の ExpressRoute 回線で Microsoft 365 サービスへの接続をサポートすることはできますか。

はい。 既存の ExpressRoute 回線を、Microsoft 365 サービスへの接続をサポートするように構成できます。 Microsoft 365 サービスに接続するための十分な容量があり、Premium アドオンを有効にしていることを確認します。 「[Microsoft 365 のネットワーク計画とパフォーマンス チューニング](/microsoft-365/enterprise/network-planning-and-performance)」が、接続ニーズを計画するのに役立ちます。 「[ExpressRoute 回線の作成と変更](expressroute-howto-circuit-classic.md)」も参照してください。

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 接続経由でどの Microsoft 365 サービスにアクセスできますか。

ExpressRoute でサポートされているサービスの最新の一覧については、「[Microsoft 365 URL および IP アドレス範囲](/microsoft-365/enterprise/urls-and-ip-address-ranges)」ページを参照してください。

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Microsoft 365 サービスに対応した ExpressRoute の料金はいくらですか。

Microsoft 365 サービスでは、Premium アドオンを有効にする必要があります。 料金については、[料金詳細のページ](https://azure.microsoft.com/pricing/details/expressroute/)を参照してください。

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Microsoft 365 の ExpressRoute はどのリージョンでサポートされていますか。

詳しくは、[ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事をご覧ください。

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>自社で ExpressRoute が構成されている場合でも、インターネット経由で Microsoft 365 にアクセスできますか。

はい。 自社のネットワークで ExpressRoute が構成されている場合でも、インターネット経由で Microsoft 365 サービスのエンドポイントにアクセスできます。 お客様の所在地のネットワークが ExpressRoute を介して Microsoft 365 サービスに接続するように構成されている場合は、組織のネットワーク チームに問い合わせてください。

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Azure ExpressRoute で Microsoft 365 ネットワーク トラフィックの高可用性を計画するにはどうすればよいですか。
「[Azure ExpressRoute の高可用性とフェールオーバー](/microsoft-365/enterprise/network-planning-with-expressroute)」に記載の推奨事項を参照してください。

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Azure US Government ExpressRoute 回線を通じて Office 365 US Government Community (GCC) サービスにアクセスすることはできますか。

はい。 Office 365 GCC サービス エンドポイントに、Azure US Government ExpressRoute を通してアクセスすることができます。 ただし、まず、Azure Portal でサポート チケットを開いて、Microsoft にアドバタイズするプレフィックスを提供する必要があります。 サポート チケットが解決された後に、Office 365 GCC サービスへの接続が確立されます。 

## <a name="route-filters-for-microsoft-peering"></a>Microsoft ピアリングのルート フィルター

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>初めて Microsoft ピアリングを有効にしています。どのルートが表示されますか。

ルートは表示されません。 ルート フィルターを回線に接続し、プレフィックスのアドバタイズを開始する必要があります。 手順については、[Microsoft ピアリングのルート フィルターを構成する](how-to-routefilter-powershell.md)をご覧ください。

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Microsoft ピアリングを有効にし、Exchange Online を選択しようとしていますが、この操作を行う権限がないというエラーが発生します。

ルート フィルターを使用すると、どのユーザーでも場合 Microsoft ピアリングを有効にできます。 ただし、Microsoft 365 サービスを使用するには、Microsoft 365 で承認される必要があります。

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>2017 年 8 月 1 日より前に Microsoft ピアリングを有効にしました。どうすればルート フィルターを利用できますか。

既存の回線では、Microsoft 365 のプレフィックスのアドバタイズが継続されます。 同じ Microsoft ピアリング上に Azure パブリック プレフィックスのアドバタイズを追加する場合、ルート フィルターを作成し、アドバタイズが必要なサービス (必要な Microsoft 365 サービスなど) を選択し、フィルターを Microsoft ピアリングにアタッチすることができます。 手順については、[Microsoft ピアリングのルート フィルターを構成する](how-to-routefilter-powershell.md)をご覧ください。

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>ある場所で Microsoft ピアリングを持っています。別の場所でこれを有効にしようとしていますが、プレフィックスが表示されません。

* 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。

* 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。 既定ではプレフィックスは表示されません。

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>プライバシー

### <a name="does-the-expressroute-service-store-customer-data"></a>ExpressRoute サービスは顧客データを格納しますか。

いいえ。
