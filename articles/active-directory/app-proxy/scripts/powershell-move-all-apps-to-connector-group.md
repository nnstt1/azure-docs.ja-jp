---
title: PowerShell サンプル - Azure Active Directory アプリケーション プロキシ アプリを別のグループに移動する
description: Azure Active Directory (Azure AD) アプリケーション プロキシの PowerShell の例は、現在コネクタグループに割り当てられているすべてのアプリケーションを別のコネクタグループに移動するために使用されます。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 0f40a4bbc6f3fabe3644e5a58ab7065ad40ba9b0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988508"
---
# <a name="move-all-azure-active-directory-application-proxy-apps-assigned-to-a-connector-group-to-another-connector-group"></a>コネクタ グループに割り当てられているすべての Azure Active Directory アプリケーション プロキシ アプリを別のコネクタ グループに移動する

この PowerShell スクリプトの例では、コネクタグループに、現在割り当てられているすべての Azure Active Directory (Azure AD) アプリケーション プロキシのアプリケーションを別のコネクタ グループに移動します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD アプリケーションを取得します。 |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | すべてのコネクタ グループの一覧を取得するか、指定した場合には、指定されたコネクタ グループの詳細を取得します。 |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | 指定されたコネクタ グループを、指定したアプリケーションに割り当てます。|

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
