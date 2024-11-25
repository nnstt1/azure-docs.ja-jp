---
title: 接続できない Azure サイト間 VPN 接続のトラブルシューティング
titleSuffix: Azure VPN Gateway
description: 突然停止して再接続できないサイト間 VPN 接続をトラブルシューティングする方法を説明します。
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 878c63b090510124fe03848d4f5aa1b3243f78d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729466"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>トラブルシューティング: Azure サイト間 VPN が動作を停止して接続できない

オンプレミスのネットワークと Azure 仮想ネットワークの間にサイト間 VPN 接続を構成した後、VPN 接続が突然動作を停止して再接続できません。 この記事では、この問題の解決に役立つトラブルシューティング手順について説明します。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

問題を解決するには、まず、[Azure VPN ゲートウェイをリセット](./reset-gateway.md)し、オンプレミス VPN デバイスからのトンネルをリセットしてみてください。 問題が解決しない場合は、以降の手順に従って問題の原因を特定します。

### <a name="prerequisite-step"></a>事前に必要な手順

Azure VPN ゲートウェイの種類を確認します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. VPN ゲートウェイの **[概要]** ページで、その種類の情報を確認します。
    
    ![ゲートウェイの概要](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>手順 1. オンプレミス VPN デバイスが検証済みであるかどうかを確認する

1. 使っている [VPN デバイスとオペレーティング システム バージョンが検証済み](vpn-gateway-about-vpn-devices.md#devicetable)であるかどうかを確認します。 検証済みの VPN デバイスではない場合、互換性の問題があるかどうかをデバイスの製造元に問い合わせてください。

2. VPN デバイスが正しく構成されていることを確認します。 詳しくは、「[デバイス構成のサンプルの編集](vpn-gateway-about-vpn-devices.md#editing)」をご覧ください。

### <a name="step-2-verify-the-shared-key"></a>手順 2. 共有キーを確認する

オンプレミスの VPN デバイスと Azure 仮想ネットワーク VPN の共有キーを比較し、両者が一致することを確認します。 

Azure VPN 接続の共有キーを確認するには、次のいずれかの方法を使います。

**Azure Portal**

1. 作成した VPN ゲートウェイ サイト間接続に移動します。

2. **[設定]** セクションで **[共有キー]** をクリックします。
    
    ![共有キー](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)の場合:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

クラシック デプロイ モデルの場合:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>手順 3. VPN ピア IP を確認する

-   Azure の **[ローカル ネットワーク ゲートウェイ]** オブジェクトの IP 定義が、オンプレミス デバイスの IP と一致している必要があります。
-   オンプレミス デバイスに設定されている Azure ゲートウェイの IP 定義が、Azure ゲートウェイの IP と一致している必要があります。

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>手順 4. ゲートウェイ サブネットで UDR と NSG を確認する

ゲートウェイ サブネットのユーザー定義ルート (UDR) またはネットワーク セキュリティ グループ (NSG) があれば削除し、その結果をテストします。 問題が解決した場合は、適用されている UDR または NSG の設定を確認します。

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>手順 5. オンプレミス VPN デバイスの外部インターフェイスのアドレスを確認する

VPN デバイスのインターネット接続 IP アドレスが Azure の **[ローカル ネットワーク]** の定義に含まれていると、散発的に接続が切れることがあります。

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>手順 6. サブネットが完全に一致することを確認する (Azure のポリシー ベースのゲートウェイ)

-   Azure 仮想ネットワークとオンプレミスの定義で、仮想ネットワーク アドレス空間が完全に一致することを確認します。
-   **ローカル ネットワーク ゲートウェイ** とオンプレミス ネットワークのオンプレミス定義との間でサブネットが完全に一致することを確認します。

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>手順 7. Azure ゲートウェイの正常性プローブを確認する

1. 次の URL を参照して正常性プローブを開きます。

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. 証明書の警告を無視して続行します。
3. 応答を受け取った場合、VPN ゲートウェイは正常であると考えられます。 応答を受け取らない場合、ゲートウェイが正常な状態にないか、またはゲートウェイ サブネット上の NSG が問題の原因になっている可能性があります。 応答のサンプル テキストを次に示します。

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>手順 8. オンプレミス VPN デバイスで Perfect Forward Secrecy 機能が有効になっているかどうかを確認する

接続切断の問題は、Perfect Forward Secrecy 機能によって引き起こされる可能性があります。 VPN デバイスで Perfect Forward Secrecy が有効になっている場合は、その機能を無効にしてください。 その後、VPN ゲートウェイの IPsec ポリシーを更新します。

## <a name="next-steps"></a>次のステップ

-   [仮想ネットワークへのサイト間接続を構成する](./tutorial-site-to-site-portal.md)
-   [サイト間 VPN 接続の IPsec/IKE ポリシーを構成する](vpn-gateway-ipsecikepolicy-rm-powershell.md)