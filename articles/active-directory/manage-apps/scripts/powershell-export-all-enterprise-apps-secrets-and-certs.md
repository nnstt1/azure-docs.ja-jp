---
title: PowerShell のサンプル - Azure Active Directory テナントのエンタープライズ アプリのシークレットと証明書をエクスポートします。
description: Azure Active Directory テナントで指定したエンタープライズ アプリのすべてのシークレットと証明書をエクスポートする PowerShell の例。
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: mtillman
ms.reviewer: mifarca
ms.openlocfilehash: 08fc5558cfe7b3459189f168e465ee2fa88d992a
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076800"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>エンタープライズ アプリのシークレットと証明書をエクスポートする
この PowerShell スクリプトの例では、指定したエンタープライズ アプリのすべてのシークレット、証明書、所有者をディレクトリから CSV ファイルにエクスポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>スクリプトの説明

"Add-Member" コマンドは、CSV ファイル内に列を作成する役割を担います。
エクスポートを非対話的に行う場合は、PowerShell で CSV ファイル パスを使用して "$Path" 変数を直接変更できます。

| コマンド | メモ |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | ディレクトリからエンタープライズ アプリケーションを取得します。 |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | ディレクトリからエンタープライズ アプリケーションの所有者を取得します。 |


## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション管理のためのその他の PowerShell の例については、[アプリケーション管理のための Azure AD PowerShell の例](../app-management-powershell-samples.md)に関する記事を参照してください。
