---
title: Azure Route Server の問題のトラブルシューティング
description: Azure Route Server の問題をトラブルシューティングする方法について説明します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 09/23/2021
ms.author: duau
ms.openlocfilehash: abece146cb2394046b7f46aa96ea70dc124cade0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649059"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Azure Route Server の問題のトラブルシューティング

## <a name="connectivity-issues"></a>接続に関する問題

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Azure Route Server への既定のルート (0.0.0.0/0) を NVA がアドバタイズした後に NVA でインターネット接続が失われるのはなぜですか?
NVA が既定のルートをアドバタイズすると、Azure Route Server では、NVA 自体を含む仮想ネットワーク内のすべての VM に対してそれをプログラムします。 この既定のルートは、すべてのインターネットにバインドされたトラフィックのネクスト ホップとして NVA を設定します。 NVA にインターネット接続が必要な場合は、[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md)を構成して、この NVA からの既定のルートをオーバーライドし、NVA がホストされているサブネットに UDR をアタッチする必要があります (下記の例を参照)。 そうしないと、NVA ホスト コンピューターは、NVA によって送信されたものを含め、インターネットにバインドされたトラフィックを NVA 自身に送り返し続けます。

| ルート | 次ホップ |
|-------|----------|
| 0.0.0.0/0 | インターネット |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-or-why-is-the-bgp-peering-flapping"></a>NVA から Azure Route Server の BGP ピア IP に ping を実行できますが、BGP ピアリングをそれらの間に設定すると、同じ IP に ping を実行できなくなるのはなぜですか。 また、BGP ピアリングがフラッピングするのはなぜですか。

一部の NVA では、Azure Route Server サブネットに静的ルートを追加する必要があります。 たとえば、Azure Route Server が 10.0.255.0/27 にあり、お使いの NVA が 10.0.1.0/24 にある場合は、NVA のルーティング テーブルに次のルートを追加する必要があります。

| ルート | 次ホップ |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 は、お使いの NVA (より正確には NIC の 1 つ) がホストされているサブネットの既定のゲートウェイ IP です。

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>ExpressRoute ゲートウェイや Azure VPN ゲートウェイが既に存在している仮想ネットワークに Azure Route Server をデプロイしているときに、ExpressRoute や Azure VPN を経由するオンプレミス ネットワークへの接続が失われるのはなぜですか?
Azure Route Server を仮想ネットワークにデプロイするときは、ゲートウェイと仮想ネットワークの間のコントロール プレーンを更新する必要があります。 この更新中、仮想ネットワーク内の VM がオンプレミス ネットワークへの接続を失う期間があります。 運用環境に Azure Route Server をデプロイするためのメンテナンスをスケジュールすることを強くお勧めします。  

## <a name="control-plane-issues"></a>コントロール プレーンの問題

### <a name="why-does-my-on-premises-network-connected-to-azure-vpn-gateway-not-receive-the-default-route-advertised-by-azure-route-server"></a>Azure VPN ゲートウェイに接続されているオンプレミス ネットワークが、Azure Route Server によってアドバタイズされた既定のルートを受信しないのはなぜですか?

Azure VPN ゲートウェイは、Azure Route Server を含む BGP ピアから既定のルートを受け取りますが、他のピアには[既定のルートをアドバタイズしません](../vpn-gateway/vpn-gateway-vpn-faq.md#what-address-prefixes-will-azure-vpn-gateways-advertise-to-me)。 

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>BGP ピアリングが稼働しているのに、NVA が Azure Route Server からルートを受信しないのはなぜですか。

Azure Route Server が使用する ASN は 65515 です。 ルート伝達が自動的に行われるように、お使いの NVA に異なる ASN を構成して、NVA と Azure Route Server の間に "eBGP" セッションを確立してください。 NVA と Azure Route Server が仮想ネットワーク内の異なるサブネットにあるため、必ず、BGP 構成で "マルチホップ" を有効にしてください。

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>NVA と Azure Route Server の間で BGP ピアリングが稼働しています。 これらの間で正しく交換されたルートが表示されます。 NVA ルートが、VM の有効なルーティング テーブルにないのはなぜですか。 

* VM がお使いの NVA と Azure Route Server と同じ VNet 内にある場合は、次のようにします。

     Azure Route Server によって公開される 2 つの BGP ピア IP は、仮想ネットワーク内で実行されている他のすべての VM にルートを送信する責任を共有する 2 つの VM でホストされます。 お使いの各 NVA で、2 つの同一の BGP セッションを 2 つの VM に設定 (同じ AS 番号、同じ AS パスを使用し、同じルートのセットをアドバタイズするなど) して、仮想ネットワーク内の VM が Azure Route Server からの一貫したルーティング情報を取得できるようにする必要があります。 下の図を参照してください。

    ![ネットワーク仮想アプライアンスと Route Server の図。](./media/faq/network-virtual-appliances.png)

    NVA のインスタンスが 2 つ以上ある場合は、1 つの NVA インスタンスをアクティブ、その他をパッシブとして指定したければ、異なる NVA インスタンスから同じルートの異なる AS パスをアドバタイズ "*できます*"。

* VM が存在する仮想ネットワークが、お使いの NVA と Azure Route Server をホストするものとは異なる場合は、次のようにします。 2 つの VNet 間で VNet ピアリングが有効になっているか、"*かつ*" VM の VNet で [Use Remote Route Server]\(リモート ルート サーバーを使用する\) が有効になっているかを確認します。

### <a name="why-is-the-equal-cost-multi-path-ecmp-function-of-my-expressroute-turned-off-after-i-deploy-azure-route-server-to-the-virtual-network"></a>Azure Route Server を仮想ネットワークにデプロイすると、ExpressRoute の等コスト マルチパス (ECMP) 機能がオフになるのはなぜですか?

複数の ExpressRoute 接続を介してオンプレミスのネットワークから Azure に同じルートをアドバタイズする場合、通常 ECMP は、Azure からご自分のプレミスに送信されるトラフィックに対して、既定で有効になります。 ただし、ルート サーバーをデプロイした後は、ExpressRoute と Azure Route Server 間の BGP 交換でマルチパス情報が失われ、その結果、Azure からのトラフィックは ExpressRoute 接続の 1 つのみでスキャンされるようになります。 この制限は、今後の Azure Route Server のリリースではなくなる予定です。  

## <a name="next-steps"></a>次のステップ

[Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認する
