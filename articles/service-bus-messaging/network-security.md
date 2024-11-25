---
title: Azure Service Bus のネットワーク セキュリティ
description: この記事では、サービス タグ、IP ファイアウォール規則、サービス エンドポイント、プライベート エンドポイントなどのネットワークセキュリティ機能について説明します。
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: db09ca367d228100eb344c63cca31047fd5e3624
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575955"
---
# <a name="network-security-for-azure-service-bus"></a>Azure Service Bus のネットワーク セキュリティ 
この記事では、Azure Service Bus で次のセキュリティ機能を使用する方法について説明します。 

- サービス タグ
- IP ファイアウォール規則
- ネットワーク サービス エンドポイント
- プライベート エンドポイント


## <a name="service-tags"></a>サービス タグ
サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。これにより、ネットワーク セキュリティ規則に対する頻繁な更新の複雑さを最小限に抑えられます。 サービス タグの詳細については、[サービス タグの概要](../virtual-network/service-tags-overview.md)に関する記事を参照してください。

サービス タグを使用して、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義できます。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用します。 規則の適切な "*ソース*" または "*ターゲット*" フィールドにサービス タグ名 (たとえば **ServiceBus**) を指定すると、対応するサービスのトラフィックを許可または拒否できます。

| サービス タグ | 目的 | 受信または送信で使用できるか | リージョン別か | Azure Firewall と共に使用できるか |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Premium サービス レベルを使用する Azure Service Bus トラフィック。 | 送信 | はい | はい |


> [!NOTE]
> サービス タグは、**Premium** 名前空間に対してのみ使用できます。 **Standard** 名前空間を使用している場合は、`nslookup <host name for the namespace>` コマンドを実行したときに表示される IP アドレスを使用します。 (例: `nslookup contosons.servicebus.windows.net`)。 

## <a name="ip-firewall"></a>IP ファイアウォール 
既定では、要求が有効な認証と承認を受けている限り、Service Bus 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

この機能は、Azure Service Bus へのアクセスを特定の既知のサイトからのみに制限したいシナリオで役立ちます。 ファイアウォール規則を使用すると、特定の IPv4 アドレスから送信されたトラフィックを受け入れる規則を構成できます。 たとえば、[Azure Express Route][express-route] で Service Bus を使用すると、お使いのオンプレミス インフラストラクチャ IP アドレスまたは会社の NAT ゲートウェイからのトラフィックのみ許可する **ファイアウォール規則** を作成できます。 

この IP ファイアウォール規則は、Service Bus 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Service Bus 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

詳細については、[Service Bus の名前空間の IP ファイアウォールを構成する方法](service-bus-ip-filtering.md)に関する記事を参照してください。

## <a name="network-service-endpoints"></a>ネットワーク サービス エンドポイント
Service Bus と[仮想ネットワーク (VNet) サービス エンドポイント](service-bus-service-endpoints.md)の統合により、メッセージング機能へのアクセスを、仮想ネットワークにバインドされている仮想マシンなどのワークロードから保護し、ネットワーク トラフィック パスを両端で保護できます。

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドするように構成した後、それぞれの Service Bus 名前空間は、承認された仮想ネットワークを除くどこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Service Bus 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。

その結果、メッセージング サービス エンドポイントの監視可能なネットワーク アドレスがパブリック IP 範囲内にあるにもかかわらず、サブネットにバインドされたワークロードとそれぞれの Service Bus 名前空間の間にプライベートな分離された関係が確立されます。

> [!IMPORTANT]
> 仮想ネットワークは、[Premium レベル](service-bus-premium-messaging.md)の Service Bus 名前空間でのみサポートされます。
> 
> Service Bus で VNet サービス エンドポイントを使用するときには、Standard レベルと Premium レベルの Service Bus 名前空間が混在するアプリケーションでこれらのエンドポイントを有効にしないでください。 これは Standard レベルでは VNet がサポートされないためです。 エンドポイントは、Premium レベルの名前空間のみに制限されます。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet の統合によって有効になる高度なセキュリティのシナリオ 

厳格でコンパートメント化されたセキュリティを要求し、仮想ネットワーク サブネットがコンパートメント化されたサービス間のセグメント化を提供するソリューションでも、一般に、それらのコンパートメント内に存在するサービス間の通信パスが必要です。

TCP/IP 上で HTTPS を搬送するものを含め、コンパートメント間の直接の IP ルートには、ネットワーク レイヤーから上のレイヤーの脆弱性を悪用されるリスクがあります。 メッセージング サービスは完全に隔離された通信パスを提供し、そこではメッセージがパーティ間を移動するときにディスクにも書き込まれます。 両方とも同じ Service Bus インスタンスにバインドされている 2 つの異なる仮想ネットワーク内のワークロードは、それぞれのネットワークの分離境界の整合性を維持しながら、メッセージを介して効率よく確実に通信できます。
 
つまり、セキュリティ対策されたクラウド ソリューションは、信頼性が高くスケーラブルな Azure の業界をリードする非同期メッセージング機能にアクセスできるだけでなく、メッセージングを使用して、セキュリティで保護されたソリューション コンパートメント間に通信パスを作成できます。この通信パスは、HTTPS やその他の TLS で保護されたソケット プロトコルを含むピアツーピア通信モードよりも本質的に安全です。

### <a name="bind-service-bus-to-virtual-networks"></a>Service Bus を仮想ネットワークにバインドする

"*仮想ネットワーク規則*" は、Azure Service Bus サーバーが特定の仮想ネットワーク サブネットからの接続を許可するかどうかを制御するファイアウォール セキュリティ機能です。

仮想ネットワークへの Service Bus 名前空間のバインドは、2 ステップのプロセスです。 まず、仮想ネットワーク サブネットに **仮想ネットワーク サービス エンドポイント** を作成し、[サービス エンドポイントの概要](service-bus-service-endpoints.md)に関する記事で説明しているように、そのエンドポイントを **Microsoft.ServiceBus** に対して有効にする必要があります。 サービス エンドポイントを追加した後、Service Bus 名前空間を "**仮想ネットワーク規則**" にバインドします。

仮想ネットワーク規則は、Service Bus 名前空間と仮想ネットワーク サブネットの関連付けです。 ルールが存在する間、サブネットにバインドされているすべてのワークロードには、Service Bus 名前空間へのアクセス権が付与されます。 Service Bus 自体は送信接続を確立することはなく、アクセス許可を取得する必要はないので、このルールを有効にすることでサブネットへのアクセス権が付与されることはありません。

詳細については、[Service Bus の名前空間に仮想ネットワーク サービス エンドポイントを構成する方法](service-bus-service-endpoints.md)に関する記事を参照してください。

## <a name="private-endpoints"></a>プライベート エンドポイント

Azure Private Link サービスを使用すると、自分の仮想ネットワーク内の **プライベート エンドポイント** 経由で、Azure サービス (Azure Service Bus、Azure Storage、Azure Cosmos DB など) と、Azure でホストされている顧客またはパートナー サービスにアクセスできます。

プライベート エンドポイントとは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 最高レベルの細分性でアクセスを制御しながら Azure リソースのインスタンスに接続できます。

詳細については、「[Azure Private Link とは](../private-link/private-link-overview.md)」を参照してください。

> [!NOTE]
> この機能は、Azure Service Bus の **Premium** レベルでサポートされています。 Premium レベルの詳細については、「[Service Bus の Premium および Standard メッセージング レベル](service-bus-premium-messaging.md)」の記事を参照してください。


詳細については、[Service Bus の名前空間のプライベート エンドポイントを構成する方法](private-link-service.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [Service Bus の名前空間の IP ファイアウォールを構成する方法](service-bus-ip-filtering.md)
- [Service Bus の名前空間に仮想ネットワーク サービス エンドポイントを構成する方法](service-bus-service-endpoints.md)
- [Service Bus の名前空間のプライベート エンドポイントを構成する方法](private-link-service.md)