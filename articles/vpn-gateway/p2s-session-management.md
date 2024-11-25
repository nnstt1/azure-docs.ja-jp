---
title: ポイント対サイト VPN セッションの管理
titleSuffix: Azure VPN Gateway
description: ポイント対サイト VPN セッションを表示および切断する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/26/2021
ms.author: cherylmc
ms.openlocfilehash: 79432bfd65feeae79017a883be990d88134cbb10
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202535"
---
# <a name="point-to-site-vpn-session-management"></a>ポイント対サイト VPN セッションの管理

Azure 仮想ネットワーク ゲートウェイを使用すると、現在のポイント対サイト VPN セッションを簡単に表示したり切断したりすることができます。 この記事は、現在のセッションの表示と切断に役立ちます。 セッションの状態は、5 分ごとに更新されます。 すぐには更新されません。 


## <a name="portal"></a>ポータル

>[!NOTE]
> 接続ソースの情報を利用できるのは、IKEv2 および OpenVPN 接続のみです。
> 

ポータルでセッションを表示および切断するには、次の操作を行います。

1. VPN ゲートウェイに移動します。
1. **[監視]** セクションで、 **[ポイント対サイト セッション]** を選択します。

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="ポータルの例":::
1. ウィンドウ ペインで現在のセッションをすべて確認できます。
1. 切断するセッションで **[...]** を選択してから、 **[切断]** を選択します。

現在、VpnGw4 SKU および VpnGw5 SKE のポータルでこの機能を使用することはできません。 これらのゲートウェイのいずれかがある場合は、次のセクションで説明する PowerShell メソッドを使用してください。

## <a name="powershell"></a>PowerShell

PowerShell を使用してセッションを表示および切断するには、次の操作を行います。

1. 次の PowerShell コマンドを実行して、アクティブなセッションの一覧を表示します。

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. 切断するセッションの **VpnConnectionId** をコピーします。

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell の例":::
1. セッションを切断するには、次のコマンドを実行します。

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>次のステップ

ポイント対サイト接続について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」をご覧ください。
