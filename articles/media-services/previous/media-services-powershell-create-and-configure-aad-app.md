---
title: Azure Media Services API にアクセスする Azure AD アプリを PowerShell で作成する | Microsoft Docs
description: PowerShell で Azure Active Directory (Azure AD) アプリを作成し、Azure Media Services API にアクセスするよう設定する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 47797a071484f01a012af8ff937d5c4deaa93987
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712512"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Azure Media Services API と共に使用する Azure AD アプリを PowerShell で作成する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

PowerShell スクリプトを使用して、Azure Media Services リソースにアクセスする Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成する方法について説明します。  

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 アカウントをお持ちでない場合は、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)で作業を開始してください。 
- Media Services アカウント。 詳細については、[Azure Portal での Azure Media Services アカウントの作成](media-services-portal-create-account.md)に関するページを参照してください。

- Azure PowerShell。 詳細については、[Azure PowerShell の使用方法](/powershell/azure/)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>PowerShell を使用した Azure AD アプリの作成  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

詳細については、次の記事を参照してください。

- [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Azure PowerShell を使用して Azure でのロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-powershell.md)
- [証明書を使用してデーモン アプリを手動で構成する方法](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>次のステップ

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。
