---
title: 'VNet 間 VPN ゲートウェイ接続の構成: Azure portal'
titleSuffix: Azure VPN Gateway
description: Vnet 間で VPN ゲートウェイ接続を作成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2021
ms.author: cherylmc
ms.openlocfilehash: 92ec3349f5e2e2f06fdbe7f56468a7145452276d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667067"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Azure ポータルを使用して VNet 間 VPN ゲートウェイ接続を構成する

この記事は、Azure portal を使用して VNet 間接続の種類を使用して、仮想ネットワーク (VNets) を接続する際に役立ちます。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションの VNet を接続する場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。 この種類の構成では、2 つの仮想ネットワーク ゲートウェイ間の接続が作成されます。 この記事の内容は、VNet ピアリングには当てはまりません。 VNet ピアリングについては、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」記事を参照してください。 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="VNet 間の図":::

この構成は、VNet のデプロイ モデルに応じて、さまざまなツールを使用して作成できます。 この記事の手順は、[Azure Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md) と Azure portal に適用されます。 別のデプロイ モデルまたはデプロイ方法の記事に切り替えるには、ドロップダウンを使用します。 

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>VNet の接続について

以下のセクションでは、仮想ネットワークを接続するさまざまな方法について説明します。

### <a name="vnet-to-vnet"></a>VNet 間

VNet 間接続の構成は、VNet を接続するためのシンプルな方法です。 VNet 間接続 (VNet2VNet) の種類を使用して仮想ネットワークどうしを接続する場合、それはオンプレミスの場所へのサイト間 IPsec 接続を作成することに似ています。 どちらの接続の種類でも、VPN ゲートウェイを使用して IPsec/IKE を使った安全なトンネルが提供され、通信時には同じように機能します。 しかし、ローカル ネットワーク ゲートウェイの構成方法は異なります。 

VNet 間接続を作成するときに、ローカル ネットワーク ゲートウェイのアドレス空間は自動的に作成され、設定されます。 一方の VNet のアドレス空間を更新すると、更新されたアドレス空間へのルーティングがもう一方の VNet で自動的に行われます。 VNet 間接続の作成は、通常、サイト間接続の場合よりも高速で簡単です。 ただし、この構成では、ローカル ネットワーク ゲートウェイは表示されません。 
* ローカル ネットワーク ゲートウェイに追加のアドレス空間を指定する場合、または後で接続を追加する予定でローカル ネットワーク ゲートウェイを調整する必要がある場合は、サイト間の手順を使用して構成を作成する必要があります。 
* VNet 間接続には、ポイント対サイト クライアント プールのアドレス空間は含まれません。 ポイント対サイト クライアントの推移的ルーティングが必要な場合は、仮想ネットワーク ゲートウェイ間にサイト間接続を作成するか、VNet ピアリングを使用します。

### <a name="site-to-site-ipsec"></a>サイト間 (IPsec)

複雑なネットワーク構成で作業している場合は、代わりに[サイト間接続](./tutorial-site-to-site-portal.md)を使用して VNet を接続する方がよい場合もあります。 サイト間 IPsec の手順に従う場合は、ローカル ネットワーク ゲートウェイを手動で作成して構成します。 各 VNet のローカル ネットワーク ゲートウェイは、他方の VNet をローカル サイトとして扱います。 これらの手順では、トラフィックをルーティングするための追加のアドレス空間をローカル ネットワーク ゲートウェイに指定することができます。 VNet のアドレス空間が変更されたら、対応するローカル ネットワーク ゲートウェイを手動で更新する必要があります。

### <a name="vnet-peering"></a>VNET ピアリング

VNet ピアリングを使用して VNet を接続することもできます。
* VNet ピアリングは VPN ゲートウェイを使用せず、さまざまな制約があります。
* [VNet ピアリングの料金](https://azure.microsoft.com/pricing/details/virtual-network)は、[VNet 間 VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway)とは計算方法が異なります。
* VNet ピアリングの詳細については、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」記事を参照してください。

## <a name="why-create-a-vnet-to-vnet-connection"></a>VNet 間接続を作成する理由

VNet 間接続を使用する仮想ネットワークの接続が望ましいのは、次のような場合です。

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>リージョン間の geo 冗長性および geo プレゼンス

* インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや同期を設定することができます。
* Azure Traffic Manager および Azure Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードを設定することができます。 たとえば、複数の Azure リージョン間で SQL Server Always On 可用性グループを設定できます。

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>特定のリージョン内で分離または管理境界を備えた多層アプリケーション

* 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションを設定することができます。

マルチサイト構成と VNet 間通信を組み合わせることができます。 これらの構成では、クロスプレミス接続と仮想ネットワーク間接続を組み合わせたネットワーク トポロジを確立することができます (下図参照)。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="VNet 接続の図。":::

この記事では、VNet 間という接続の種類を使用して、VNet を接続する方法を示します。 演習として以下の手順に従う場合は、次の例の設定値を使用できます。 この例では、仮想ネットワークは同じサブスクリプション内にありながら、異なるリソース グループに含まれます。 対象となる VNet がそれぞれ異なるサブスクリプションに存在する場合、ポータルで接続を作成することはできません。 代わりに、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) または [CLI](vpn-gateway-howto-vnet-vnet-cli.md) を使用します。 VNet 間接続の詳細については、「[VNet 間接続に関してよく寄せられる質問](#vnet-to-vnet-faq)」を参照してください。

### <a name="example-settings"></a>設定例

**VNet1 の値:**

* **仮想ネットワークの設定**
  * **Name**:VNet1
  * **[アドレス空間]** : 10.1.0.0/16
  * **サブスクリプション**:使用するサブスクリプションを選択します。
  * **[リソース グループ]** :TestRG1
  * **[場所]** :米国東部
  * **サブネット**
    * **Name**:FrontEnd
    * **アドレス範囲**:10.1.0.0/24

* **仮想ネットワーク ゲートウェイの設定**
  * **Name**:VNet1GW
  * **[リソース グループ]** :米国東部
  * **世代:** 第 2 世代
  * **[ゲートウェイの種類]** : **[VPN]** を選択します。
  * **[VPN の種類]** : **[ルート ベース]** を選択します。
  * **SKU**: VpnGw2
  * **仮想ネットワーク**:VNet1
  * **[ゲートウェイ サブネットのアドレス範囲]** : 10.1.255.0/27
  * **[パブリック IP アドレス]** : 新規作成
  * **パブリック IP アドレス名**:VNet1GWpip

* **接続**
  * **Name**:VNet1toVNet4
  * **共有キー**:独自の共有キーを作成できます。 VNet 間の接続を作成する際に、値が一致する必要があります。 この演習では、abc123 を使用します。

**VNet4 の値:**

* **仮想ネットワークの設定**
  * **Name**:VNet4
  * **[アドレス空間]** : 10.41.0.0/16
  * **サブスクリプション**:使用するサブスクリプションを選択します。
  * **[リソース グループ]** :TestRG4
  * **[場所]** :米国西部
  * **サブネット**
  * **Name**:FrontEnd
  * **アドレス範囲**:10.41.0.0/24

* **仮想ネットワーク ゲートウェイの設定**
  * **Name**:VNet4GW
  * **[リソース グループ]** :米国西部
  * **世代:** 第 2 世代
  * **[ゲートウェイの種類]** : **[VPN]** を選択します。
  * **[VPN の種類]** : **[ルート ベース]** を選択します。
  * **SKU**: VpnGw2
  * **仮想ネットワーク**:VNet4
  * **[ゲートウェイ サブネットのアドレス範囲]** : 10.41.255.0/27
  * **[パブリック IP アドレス]** : 新規作成
  * **パブリック IP アドレス名**:VNet4GWpip

* **接続**
  * **Name**:VNet4toVNet1
  * **共有キー**:独自の共有キーを作成できます。 VNet 間の接続を作成する際に、値が一致する必要があります。 この演習では、abc123 を使用します。

## <a name="create-and-configure-vnet1"></a>VNet1 を作成して構成する

既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。 サブネットの重複があると、接続が適切に動作しません。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>VNet1 ゲートウェイを作成する

この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。 演習としてこの構成を作成する場合は、「[設定例](#example-settings)」を参照してください。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成するには

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

デプロイの状態は、ゲートウェイの [概要] ページで確認できます。 ゲートウェイの作成とデプロイが完了するまでに 45 分以上かかることがあります。 ゲートウェイの作成後は、ポータルの仮想ネットワークを調べることで、ゲートウェイに割り当てられている IP アドレスを確認できます。 ゲートウェイは、接続されたデバイスとして表示されます。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>VNet4 を作成して構成する

VNet1 を構成した後、前の手順を繰り返し、値を VNet4 の値に置き換えて、VNet4 と VNet4 ゲートウェイを作成します。 VNet4 を構成する前に、VNet1 用の仮想ネットワーク ゲートウェイの作成が完了するまで待つ必要はありません。 独自の値を使用する場合は、接続先とするどの VNet ともアドレス空間が重複しないようにしてください。

## <a name="configure-the-vnet1-gateway-connection"></a>VNet1 ゲートウェイ接続を構成する

VNet1 と VNet4 の仮想ネットワーク ゲートウェイの作成が両方とも完了したら、仮想ネットワーク ゲートウェイの接続を作成できます。 このセクションでは、VNet1 から VNet4 への接続を作成します。 これらの手順は、同じサブスクリプションに存在する VNet でのみ使用できます。 VNet が異なるサブスクリプションにある場合は、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) を使用して接続する必要があります。 しかし、VNet が同じサブスクリプション内の異なるリソース グループにある場合は、ポータルを使用して接続できます。

1. Azure ポータルで、 **[すべてのリソース]** を選択し、検索ボックスに *仮想ネットワーク ゲートウェイ* を入力してから、VNet の仮想ネットワーク ゲートウェイに移動します。 たとえば、 **[VNet1GW]** に移動します。 ゲートウェイを選択して、**仮想ネットワーク ゲートウェイ** のページを開きます。
1. [ゲートウェイ] ページで、 **[設定] > [接続]** の順にアクセスします。 次に、 **[+ 追加]** を選択します。

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="[接続] ページを示すスクリーンショット。" border="false":::
1. **[接続の追加]** ページが開きます。

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="[接続の追加] ページを示すスクリーンショット。":::

   **[接続の追加]** ページで、接続の値を入力します。

   * **Name**:接続の名前を入力します。 たとえば、「*VNet1toVNet4*」と入力します。

   * **[接続の種類]** : ドロップダウンから **[VNet 間]** を選択します。

   * **最初の仮想ネットワーク ゲートウェイ**:この接続は指定した仮想ネットワーク ゲートウェイから作成しているため、このフィールドの値は自動的に入力されます。

   * **2 番目の仮想ネットワーク ゲートウェイ**:このフィールドでは、接続先として作成する VNet の仮想ネットワーク ゲートウェイを設定します。 **[別の仮想ネットワーク ゲートウェイを選択する]** を選び、 **[仮想ネットワーク ゲートウェイの選択]** ページを開きます。

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="別のゲートウェイが選択されている [仮想ネットワーク ゲートウェイの選択] ページを示すスクリーンショット。":::

     * このページに一覧表示されている仮想ネットワーク ゲートウェイを確認します。 自分のサブスクリプションに属している仮想ネットワーク ゲートウェイのみが表示されていることがわかります。 自分のサブスクリプションに属していない仮想ネットワーク ゲートウェイに接続する場合は、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) を使用します。

     * 接続先となる仮想ネットワーク ゲートウェイを選択します。

   * **共有キー (PSK)** :このフィールドには、接続の共有キーを入力します。 このキーは、自分で生成または作成できます。 サイト間接続では、使用するキーは、オンプレミス デバイスと仮想ネットワーク ゲートウェイの接続の場合と同じになります。 VPN デバイスではなく、別の仮想ネットワーク ゲートウェイに接続するという点を除けば、概念は同じです。
1. **[OK]** を選択して変更を保存します。

## <a name="configure-the-vnet4-gateway-connection"></a>VNet4 ゲートウェイ接続を構成する

次に、VNet4 から VNet1 への接続を作成します。 ポータルで、VNet4 に関連付けられている仮想ネットワーク ゲートウェイを探します。 前のセクションの手順に従います。その際、VNet4 から VNet1 への接続を作成するように値を置き換えます。 必ず同一の共有キーを使用してください。

## <a name="verify-your-connections"></a>接続の確認

1. Azure ポータルで仮想ネットワーク ゲートウェイを探します。 
1. **仮想ネットワーク ゲートウェイ** のページで、 **[接続]** を選択して仮想ネットワーク ゲートウェイの **[接続]** ページを表示します。 接続が確立された後、 **[状態]** の値が **[接続済み]** に変わります。

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="接続を確認する [接続] ページを示すスクリーンショット。" border="false":::
1. **[名前]** 列で、いずれかの接続を選択して詳細を表示します。 データのフローが開始されると、 **[データ入力]** と **[データ出力]** に値が表示されます。

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="リソース グループを示すスクリーンショット。[データ入力] と [データ出力] に値が含まれています。" border="false":::

## <a name="add-additional-connections"></a>さらに接続を追加する

さらに接続を追加する場合は、接続の作成元となる仮想ネットワーク ゲートウェイに移動し、 **[接続]** を選択します。 別の VNet 間接続を作成することも、オンプレミスの場所への IPsec サイト間接続を作成することもできます。 作成する接続の種類に合わせて、 **[接続の種類]** を調整してください。 追加の接続を作成する前に、仮想ネットワークのアドレス空間が、接続先のアドレス空間と重複していないことを確認してください。 サイト間接続を作成する手順については、[サイト間接続の作成](./tutorial-site-to-site-portal.md)に関するページを参照してください。

## <a name="vnet-to-vnet-faq"></a>VNet 間接続に関してよく寄せられる質問

VNet 間接続に関するその他の情報についてよく寄せられる質問の詳細を示します。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>次のステップ

* 仮想ネットワーク内のリソースへのネットワーク トラフィックを制限する方法については、[ネットワーク セキュリティ](../virtual-network/network-security-groups-overview.md)に関するページを参照してください。

* Azure がトラフィックを Azure、オンプレミス、インターネット リソースの間でどのようにルーティングするかについては、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。
