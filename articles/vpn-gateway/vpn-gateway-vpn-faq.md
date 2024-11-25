---
title: Azure VPN Gateway に関する FAQ
description: VPN Gateway のクロスプレミス接続、ハイブリッド構成接続、および仮想ネットワーク ゲートウェイに関してよく寄せられる質問について説明します。 この FAQ には、ポイント対サイト、サイト間、VNet 間の構成設定に関する包括的な情報が含まれています。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: yushwang
ms.openlocfilehash: b3619ba68338e40773cdd962298b01806bde5b2b
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660630"
---
# <a name="vpn-gateway-faq"></a>VPN Gateway に関する FAQ

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>仮想ネットワークへの接続

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>仮想ネットワークは異なる Azure リージョン間でも接続できますか。

はい。 リージョンにより制限されることはありません。 特定の仮想ネットワークから、同一リージョン内の別の仮想ネットワーク、または別の Azure リージョンに存在する別の仮想ネットワークに接続できます。

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>異なるサブスクリプションの仮想ネットワークに接続することはできますか。

はい。

### <a name="can-i-specify-private-dns-servers-in-my-vnet-when-configuring-vpn-gateway"></a>VPN Gateway を構成するときに、自分の VNet にプライベート DNS サーバーを指定できますか。

VNet の作成時に DNS サーバーを指定した場合、VPN Gateway では指定した DNS サーバーが使用されます。 DNS サーバーを指定する場合は、DNS サーバーが Azure に必要なドメイン名を解決できることを確認してください。

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>1 つの仮想ネットワークから複数のサイトに接続できますか。

Windows PowerShell および Azure REST API を使用して複数のサイトに接続することができます。 詳細については、「 [マルチサイトと VNet 間接続](#V2VMulti) 」の FAQ を参照してください。

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>VPN ゲートウェイをアクティブ/アクティブとして設定する場合、追加のコストがかかりますか。

いいえ。

### <a name="what-are-my-cross-premises-connection-options"></a>クロスプレミス接続にはどのようなオプションがありますか。

次のようなクロスプレミス接続がサポートされています。

* サイト間接続 - IPsec (IKE v1 および IKE v2) 経由での VPN 接続。 この種類の接続には、VPN デバイスまたは RRAS が必要です。 詳細については、[サイト間接続](./tutorial-site-to-site-portal.md)に関するページを参照してください。
* ポイント対サイト接続 – SSTP (Secure Socket トンネリング プロトコル) 経由での VPN 接続または IKE v2。 この接続では、VPN デバイスは不要です。 詳細については、[ポイント対サイト接続](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。
* VNet 間接続 - この種類の接続は、サイト間構成の場合と同じです。 VNet 間接続では IPsec (IKE v1 および IKE v2) 経由で VPN 接続を確立します。 VPN デバイスは不要です。 詳細については、[VNet 間接続](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関するページを参照してください。
* マルチサイト接続 - これはサイト間構成の一種で、複数のオンプレミス サイトから仮想ネットワークに接続するものです。 詳細については、[マルチサイト接続](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関するページを参照してください。
* ExpressRoute 接続 - ExpressRoute 接続は、パブリックなインターネットを経由した VPN 接続ではなく、WAN から Azure へのプライベート接続です。 詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」および「[ExpressRoute の FAQ](../expressroute/expressroute-faqs.md)」をご覧ください。

VPN Gateway の接続の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください。

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>サイト間接続とポイント対サイト接続の違いを教えてください。

**サイト間** (IPsec/IKE VPN トンネル) 構成は、オンプレミスの場所と Azure の間で行われます。 つまり、ルーティングとアクセス許可の構成に従って、オンプレミスの場所に存在する任意のコンピューターと仮想ネットワーク内に存在する任意の仮想マシンやロール インスタンスを接続できます。 このオプションはクロスプレミス接続として常に使用可能で、ハイブリッド構成にも適しています。 この種類の接続は IPsec VPN アプライアンス (ハードウェア デバイスまたはソフト アプライアンス) に依存します。このアプライアンスは、ネットワークの境界にデプロイされる必要があります。 この種類の接続を作成するには、外部に公開された IPv4 アドレスが必要です。

**ポイント対サイト** (VPN over SSTP) 構成では、任意の場所に存在する 1 台のコンピューターから仮想ネットワーク内に存在する任意のデバイスに接続できます。 この接続では、Windows に組み込み済みの VPN クライアントを使用します。 ポイント対サイト構成の一部として、証明書と VPN クライアント構成パッケージをインストールします。このパッケージには、コンピューターを仮想ネットワーク内の任意の仮想マシンまたはロール インスタンスに接続できるようにする設定が含まれています。 これは仮想ネットワークに接続する場合には適していますが、オンプレミスに存在している場合は適していません。 また、このオプションは VPN ハードウェアにアクセスできない場合や外部に公開された IPv4 アドレスが存在しない場合にも便利ですが、このどちらでもサイト間接続が必須となります。

VPN の種類がルート ベースのゲートウェイを使用してサイト間接続を作成すれば、サイト間接続とポイント対サイト接続の両方を同時に使用するように仮想ネットワークを構成できます。 VPN の種類がルート ベースのゲートウェイは、クラシック デプロイ モデルでは動的ゲートウェイと呼ばれます。

## <a name="privacy"></a><a name="privacy"></a>プライバシー

### <a name="does-the-vpn-service-store-or-process-customer-data"></a>VPN サービスによって顧客データは保存または処理されますか?

いいえ。

## <a name="virtual-network-gateways"></a><a name="gateways"></a>仮想ネットワーク ゲートウェイ

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>VPN ゲートウェイは仮想ネットワーク ゲートウェイですか。

VPN ゲートウェイは仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイは、仮想ネットワークとオンプレミスの場所の間でパブリック接続を使って暗号化されたトラフィックを送信します。 仮想ネットワーク間のトラフィックの送信に VPN Gateway を使うこともできます。 VPN ゲートウェイを作成するときは、GatewayType に値 'Vpn' を使用します。 詳細については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)」をご覧ください。

### <a name="what-is-a-policy-based-static-routing-gateway"></a>ポリシーベース (静的ルーティング) ゲートウェイとは何ですか。

ポリシー ベースのゲートウェイとは、ポリシー ベースの VPN を実装したものです。 ポリシー ベースの VPN では、パケットを暗号化し、オンプレミス ネットワークと Azure VNet の間でアドレスのプレフィックスの組み合わせに基づいて IPsec トンネル経由でそのパケットを送信します。 ポリシー (またはトラフィック セレクター) は、通常、VPN の構成でアクセス リストとして定義されます。

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>ルートベース (動的ルーティング) ゲートウェイとは何ですか。

ルート ベースのゲートウェイは、ルート ベースの VPN を実装したものです。 ルート ベースの VPN は、「ルート」を使用して、IP 転送やルーティング テーブルを使用してパケットを対応するトンネル インターフェイスに直接送信します。 その後、トンネル インターフェイスではトンネルの内部または外部でパケットを暗号化または復号します。 ルート ベースの VPN に向けたポリシーまたはトラフィック セレクターは任意の環境間 (またはワイルドカード) として構成されます。

### <a name="can-i-specify-my-own-policy-based-traffic-selectors"></a>独自のポリシー ベースのトラフィック セレクターを指定できますか?

はい。トラフィック セレクターは、[New-AzIpsecTrafficSelectorPolicy](/powershell/module/az.network/new-azipsectrafficselectorpolicy) PowerShell コマンドを使用して、接続の *trafficSelectorPolicies* 属性で定義できます。 指定したトラフィック セレクターを有効にする場合は、[ポリシー ベースのトラフィック セレクターを使用する](vpn-gateway-connect-multiple-policybased-rm-ps.md#enablepolicybased)オプションが有効になっている必要があります。

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>ポリシー ベースの VPN ゲートウェイをルート ベースに更新できますか。

いいえ。 ゲートウェイの種類は、ポリシー ベースからルート ベースに変更することも、ルート ベースからポリシー ベースに変更することもできません。 ゲートウェイの種類を変更するには、ゲートウェイを削除してから再作成する必要があります。 このプロセスには約 60 分かかります。 新しいゲートウェイを作成するときに、元のゲートウェイの IP アドレスを保持することはできません。

1. ゲートウェイに関連付けられているすべての接続を削除してください。

1. ゲートウェイを削除するには、次のいずれかの記事を利用します。

   * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
   * [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Azure PowerShell - クラシック](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. 目的のゲートウェイの種類を使用して新しいゲートウェイを作成してから、VPN の設定を行います。 手順については、[サイト間のチュートリアル](./tutorial-site-to-site-portal.md#VNetGateway)を参照してください。

### <a name="do-i-need-a-gatewaysubnet"></a>'GatewaySubnet' は必要ですか。

はい。 ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する IP アドレスが含まれます。 仮想ネットワーク ゲートウェイを構成するには、VNet のゲートウェイ サブネットを作成する必要があります。 すべてのゲートウェイ サブネットを正常に動作させるには、'GatewaySubnet' という名前を付ける必要があります。 ゲートウェイ サブネットに他の名前を付けないでください。 また、ゲートウェイ サブネットには VM などをデプロイしないでください。

ゲートウェイ サブネットを作成するときに、サブネットに含まれる IP アドレスの数を指定します。 ゲートウェイ サブネット内の IP アドレスは、ゲートウェイ サービスに割り当てられます。 一部の構成では、他の構成よりも多くの IP アドレスをゲートウェイ サービスに割り当てる必要があります。 ゲートウェイ サブネットには、将来の拡大や新しい接続構成の追加に対応できる十分な IP アドレスが含まれるようにしてください。 そのため、/29 のような小さいゲートウェイ サブネットを作成できますが、/27 以上 (/27、/26、/25 など) のゲートウェイ サブネットを作成することをお勧めします。 作成する構成の要件を調べて、ゲートウェイ サブネットがその要件を満たしていることを確認してください。

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>ゲートウェイ サブネットに仮想マシンやロール インスタンスをデプロイできますか。

いいえ。

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>ゲートウェイを作成する前に VPN ゲートウェイの IP アドレスを取得できますか。

ゾーン冗長とゾーン ゲートウェイ (名前に _AZ_ が入っているゲートウェイ SKU) は、どちらも _Standard SKU_ の Azure のパブリック IP リソースに依存しています。 Azure の Standard SKU のパブリック IP リソースでは、静的な割り当て方法を使用する必要があります。 そのため、使用する Standard SKU のパブリック IP リソースを作成するとすぐに、VPN ゲートウェイ用のパブリック IP アドレスが作成されます。

ゾーン冗長および非ゾーン ゲートウェイ (名前に _AZ_ が入って _いない_ ゲートウェイ SKU) の場合、VPN ゲートウェイの IP アドレスを作成する前に取得することはできません。 IP アドレスが変更されるのは、VPN ゲートウェイを削除してもう一度作成した場合だけです。

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>VPN Gateway に静的なパブリック IP アドレスを指定することはできますか。

前述のとおり、ゾーン冗長とゾーン ゲートウェイ (名前に _AZ_ が入っているゲートウェイ SKU) は、どちらも _Standard SKU_ の Azure のパブリック IP リソースに依存しています。 Azure の Standard SKU のパブリック IP リソースでは、静的な割り当て方法を使用する必要があります。

ゾーン冗長および非ゾーン ゲートウェイ (名前に _AZ_ が入って _いない_ ゲートウェイ SKU) の場合、動的 IP アドレスの割り当てのみがサポートされます。 ただし、これは IP アドレスがご使用の VPN ゲートウェイに割り当てられた後に変更されるという意味ではありません。 VPN ゲートウェイの IP アドレスが変更されるのは、ゲートウェイが削除されてから、再度作成された場合だけです。 VPN ゲートウェイのパブリック IP アドレスは、VPN ゲートウェイのサイズ変更、リセット、またはその他の内部メンテナンスまたはアップグレードを完了しても変更されません。

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>VPN トンネルの認証を取得する方法を教えてください。

Azure の VPN では PSK (事前共有キー) の認証を使用します。 事前共有キー (PSK) は VPN トンネルを作成したときに生成されます。 自分で使用するために自動生成した PSK は、PowerShell コマンドレットまたは REST API の Set Pre-Shared Key を使用して変更できます。

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>ポリシー ベース (静的ルーティング) ゲートウェイ VPN を構成する際に Set Pre-Shared Key API を使用できますか。

はい。Set Pre-Shared Key API および PowerShell コマンドレットは、Azure のポリシー ベース (静的ルーティング) VPN とルート ベース (動的ルーティング) VPN のどちらを構成する場合にも使用できます。

### <a name="can-i-use-other-authentication-options"></a>ほかの認証オプションを使用することはできますか

事前共有キー (PSK) による認証のみに制限されています。

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>VPN ゲートウェイを通過するトラフィックの種類は、どのようにすれば指定できますか。

#### <a name="resource-manager-deployment-model"></a>Resource Manager デプロイ モデル

* PowerShell: "AddressPrefix" を使用してローカル ネットワーク ゲートウェイのトラフィックを指定します。
* Azure Portal: ローカル ネットワーク ゲートウェイ、[構成]、[アドレス空間] の順に移動します。

#### <a name="classic-deployment-model"></a>クラシック デプロイ モデル

* Azure Portal: クラシック仮想ネットワーク、[VPN 接続]、[サイト対サイト VPN 接続]、ローカル サイト名、[ローカル サイト]、[クライアント アドレス空間] の順に移動します。

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>VPN 接続で NAT-T を使用できますか。

はい。NAT トラバーサル (NAT-T) がサポートされています。 Azure VPN Gateway によって、NAT に類似する機能が、内部パケットで IPsec トンネルに対して実行されることはありません。  この構成では、オンプレミスのデバイスによって IPSec トンネルが開始されるようにします。

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Azure で自社の VPN サーバーをセットアップし、オンプレミス ネットワークへの接続に使用することはできますか。

はい。Azure Marketplace から、または自社の VPN ルーターを作成して、自社の VPN ゲートウェイやサーバーを Azure 内にデプロイできます。 仮想ネットワーク内でユーザー定義ルートを構成して、オンプレミス ネットワークと仮想ネットワークのサブネットの間でトラフィックが適切にルーティングされるようにする必要があります。

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>仮想ネットワーク ゲートウェイで特定のポートが開かれているのはなぜですか。

Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 対象のゲートウェイの顧客を含め、適切な証明書を持たない外部エンティティは、これらのエンドポイントに影響を与えることはできません。

仮想ネットワーク ゲートウェイは基本的に、1 枚の NIC が顧客のプライベート ネットワークに接続され、1 枚の NIC がパブリック ネットワークに面しているマルチホーム デバイスです。 Azure インフラストラクチャのエンティティは、コンプライアンス上の理由から顧客のプライベート ネットワークに接続できないため、インフラストラクチャの通信用にパブリック エンドポイントを使用する必要があります。 パブリック エンドポイントは、Azure のセキュリティ監査によって定期的にスキャンされます。

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>ゲートウェイの種類、要件、およびスループットの詳細について教えてください。

詳細については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)」をご覧ください。

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>サイト間接続と VPN デバイス

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>VPN デバイスを選択する場合の考慮事項について教えてください。

Microsoft では、デバイス ベンダーと協力して複数の標準的なサイト間 VPN デバイスを検証しました。 互換性が確認されている VPN デバイス、それに対応する構成の手順またはサンプル、デバイスの仕様の一覧は [VPN デバイス](vpn-gateway-about-vpn-devices.md)に関する記事で確認できます。 互換性が確認されているデバイス ファミリに属するすべてのデバイスも仮想ネットワークで動作します。 VPN デバイスを構成するには、デバイスの構成サンプル、または適切なデバイス ファミリに対応するリンクを参照してください。

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>VPN デバイスの構成設定はどこにありますか。

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>VPN デバイス構成サンプルは、どのように編集すればよいですか。

デバイス構成サンプルの編集については、[サンプルの編集](vpn-gateway-about-vpn-devices.md#editing)に関するセクションを参照してください。

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>IPsec および IKE パラメーターはどこにありますか。

IPsec/IKE のパラメーターについては、[パラメーター](vpn-gateway-about-vpn-devices.md#ipsec)に関するセクションを参照してください。

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>トラフィックがアイドル状態のときにポリシー ベースの VPN トンネルがダウンするのはなぜですか。

これは、ポリシー ベースの (静的ルーティングとも呼ばれる) VPN ゲートウェイの予期される動作です。 トンネル経由のトラフィックが 5 分以上アイドル状態になると、トンネルは破棄されます。 どちらかの方向のトラフィック フローが開始されると、トンネルはすぐに再び確立されます。

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>ソフトウェア VPN を使用して Azure に接続することはできますか。

Windows Server 2012 ルーティングとリモート アクセス (RRAS) サーバーでは、クロスプレミス構成のサイト間接続をサポートしています。

その他のソフトウェア VPN ソリューションについては、業界標準の IPsec の実装に準拠していればマイクロソフトのゲートウェイで動作します。 構成とサポートの手順については、ソフトウェアのベンダーにお問い合わせください。

### <a name="can-i-connect-to-azure-gateway-via-point-to-site-when-located-at-a-site-that-has-an-active-site-to-site-connection"></a>サイト間接続がアクティブなサイトにあるとき、ポイント対サイトを利用して Azure ゲートウェイに接続できますか。

はい。ただし、ポイント対サイト クライアントのパブリック IP アドレスと、サイト間 VPN デバイスで使用されるパブリック IP アドレスが異なる必要があります。同じ場合、ポイント対サイト接続は機能しません。 IKEv2 によるポイント対サイト接続は、同じ Azure VPN ゲートウェイでサイト間 VPN 接続が構成されている同じパブリック IP アドレスから開始できません。 

## <a name="point-to-site---certificate-authentication"></a><a name="P2S"></a>ポイント対サイト - 証明書認証

このセクションは、Resource Manager デプロイ モデルに適用されます。

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site---radius-authentication"></a><a name="P2SRADIUS"></a>ポイント対サイト - RADIUS 認証

このセクションは、Resource Manager デプロイ モデルに適用されます。

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>VNet 間接続とマルチサイト接続

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>サイト間 VPN 接続と ExpressRoute の間のルーティングを有効にするにはどうすればいいですか。

ExpressRoute に接続されているブランチと、サイト間 VPN 接続に接続されているブランチとの間のルーティングを有効にするには、[Azure Route Server](../route-server/expressroute-vpn-support.md) をセットアップする必要があります。

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>オンプレミス サイトや別の仮想ネットワークに向けてトラフィックを通過させるときに、Azure VPN Gateway を使用できますか。

**Resource Manager デプロイ モデル**<br>
はい。 詳細については、「[BGP](#bgp)」セクションを参照してください。

**クラシック デプロイ モデル**<br>
クラシック デプロイ モデルを使用して Azure VPN Gateway 経由でトラフィックを通過させることは可能です。この場合、ネットワーク構成ファイル内の静的に定義されたアドレス空間が使用されます。 BGP はまだ、クラシック デプロイ モデルを使用した Azure Virtual Network と VPN Gateway ではサポートされていません。 BGP が使用できない場合、手動で通過アドレス空間を定義すると非常にエラーが発生しやすいため、これは推奨していません。

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure では、同一仮想ネットワークのすべての VPN 接続に対して同一の IPsec/IKE 事前共有キーが生成されるのですか。

いいえ、既定では、Azure は異なる VPN 接続に対してそれぞれ異なる事前共有キーを生成します。 ただし、Set VPN Gateway Key REST API または PowerShell コマンドレットを使用すると、お好みのキー値を設定することができます。 キーは印刷可能な ASCII 文字でなければなりません。

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>1 つの仮想ネットワークのよりも多くのサイト間 VPN を確立した方がより広い帯域幅を得られるのでしょうか。

いいえ、ポイント対サイト VPN を含むすべての VPN トンネルは、同一の Azure VPN Gateway とそこで利用可能な帯域幅を共有します。

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>仮想ネットワークとオンプレミス サイトの間に、マルチサイト VPN を使用して複数のトンネルを構成できますか。

はい。ただし、両方のトンネルの BGP を同じ場所に構成する必要があります。

### <a name="does-azure-vpn-gateway-honor-as-path-prepending-to-influence-routing-decisions-between-multiple-connections-to-my-on-premises-sites"></a>Azure VPN Gateway では、AS パス プリペンドが優先され、これがオンプレミス サイトへの複数の接続間でのルーティング決定に影響しますか。

はい。Azure VPN Gateway では、BGP が有効になっているとき、ルーティングの決定を支援するために AS パス プリペンドが優先されます。 BGP パスの選択では、より短い AS パスが優先されます。

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>仮想ネットワークを持つポイント対サイト VPN を複数の VPN トンネルで使用できますか。

はい、ポイント対サイト (P2S) VPN は複数のオンプレミス サイトおよび他の仮想ネットワークに接続されている VPN ゲートウェイで使用できます。

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>IPsec VPN を使用している仮想ネットワークを自社の ExpressRoute 回線に接続できますか。

はい、これはサポートされています。 詳細については、 [共存する ExpressRoute とサイト間 VPN の接続の構成](../expressroute/expressroute-howto-coexist-classic.md)を参照してください。

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec/IKE ポリシー

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="bgp-and-routing"></a><a name="bgp"></a>BGP とルーティング

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

### <a name="can-i-configure-forced-tunneling"></a>強制トンネリングを構成できますか。

はい。 [強制トンネリングについて](vpn-gateway-about-forced-tunneling.md)を参照してください。

## <a name="nat"></a><a name="nat"></a>NAT

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>クロスプレミス接続と VM

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>仮想ネットワーク内に仮想マシンが存在し、クロスプレミス接続が使用できる場合、その VM にはどのように接続できますか。

いくつかの方法を使用できます。 ご利用の VM で RDP を有効にしている場合、プライベート IP アドレスを使って自分の仮想マシンに接続することができます。 この場合、接続先のプライベート IP アドレスとポート (通常 3389) を指定します。 このため、このトラフィックで使用するポートを仮想マシンで構成する必要があります。

また、同一仮想ネットワークに存在する別の仮想マシンからプライベート IP アドレスで仮想マシンに接続することもできます。 仮想ネットワークの外部から接続している場合は、プライベート IP アドレスを使用して仮想マシンに RDP 接続することはできません。 たとえば、ポイント対サイト仮想ネットワークが構成済みで、使用中のコンピューターからの接続が確立されていない場合は、プライベート IP アドレスを使用して仮想マシンに接続することはできません。

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>クロスプレミス接続が確立されている仮想ネットワーク内に仮想マシンが存在する場合、VM からのトラフィックはすべてその接続を経由しますか。

いいえ。 仮想ネットワーク ゲートウェイを経由して送信されるのは、仮想ネットワークのローカル ネットワークの IP アドレス範囲内に存在する送信先 IP が指定されているトラフィックのみです。 送信先 IP が同一仮想ネットワーク内に存在する場合は、その仮想ネットワーク内のみで通信が行われます。 それ以外のトラフィックは、ロード バランサーを経由してパブリック ネットワークに送信されます。ただし、強制トンネリングを使用する場合は Azure VPN ゲートウェイを経由して送信されます。

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>VM に対する RDP 接続をトラブルシューティングする方法

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]

## <a name="virtual-network-faq"></a><a name="faq"></a>Virtual Network FAQ

「[Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)」で、仮想ネットワークの情報をさらに詳しく参照できます。

## <a name="next-steps"></a>次の手順

* VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください。
* VPN Gateway の構成設定の詳細については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)」をご覧ください。

**"OpenVPN" は OpenVPN Inc. の商標です。**
