---
title: Azure Application Gateway の証明書を更新する
description: アプリケーション ゲートウェイ リスナーに関連付けられている証明書を更新する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ea5512f3b454edc2edd66c546629cc5f0b8f465c
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108320891"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway の証明書を更新する

TLS または SSL 暗号化対応にアプリケーション ゲートウェイを構成した場合、いつかは、証明書の更新が必要になります。

Azure portal、Azure PowerShell、または Azure CLI を使って、リスナーに関連付けられている証明書を更新することができます。

## <a name="azure-portal"></a>Azure portal

ポータルからリスナー証明書を更新するには、アプリケーション ゲートウェイ リスナーに移動します。 更新が必要な証明書のあるリスナーを選択して、 **[選択した証明書の更新または編集]** を選択します。

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="証明書の更新":::

新しい PFX 証明書をアップロードし、名前を指定し、パスワードを入力してから、 **[保存]** を選択します。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell を使用して証明書を更新するには、次のスクリプトを使用します。

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>次のステップ

Azure Application Gateway で TLS オフロードを構成する方法を学習するには、[TLS オフロードの構成](./create-ssl-portal.md)に関するページをご覧ください。
