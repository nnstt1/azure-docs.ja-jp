---
title: 'ゲートウェイの IP アドレスの設定を変更する: Azure CLI'
titleSuffix: Azure VPN Gateway
description: Azure CLI を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/28/2021
ms.author: cherylmc
ms.openlocfilehash: 5a11221cc11e99cf989f1fa50f851b51754c7071
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474271"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Azure CLI を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイのアドレス プレフィックスまたはゲートウェイ IP アドレスの設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

>[!NOTE]
> 接続があるローカル ネットワーク ゲートウェイを変更すると、トンネルの切断とダウンタイムが発生する可能性があります。
>

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」をご覧ください。

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更する

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>ゲートウェイの IP アドレスを変更する

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>次の手順

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。