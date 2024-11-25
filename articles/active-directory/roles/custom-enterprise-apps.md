---
title: Azure Active Directory でエンタープライズ アプリを管理するためのカスタム ロールを作成する
description: Azure Active Directory でエンタープライズ アプリ アクセス用のカスタム Azure AD ロールを作成して割り当てる
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf49cfd4d1e0e9b3e65354c9f6c89dd7efd71970
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739952"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリを管理するためのカスタム ロールを作成する

この記事では、Azure Active Directory (Azure AD) でユーザーとグループのエンタープライズ アプリの割り当てを管理するためのアクセス許可を持つカスタム ロールを作成する方法について説明します。 ロールの割り当ての要素と、サブタイプ、アクセス許可、プロパティ セットなどの用語の意味については、「[カスタムロールの概要](custom-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P1 または P2 ライセンス
- 特権ロール管理者または全体管理者
- PowerShell を使用する場合の AzureADPreview モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、「[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)」をご覧ください。

## <a name="enterprise-app-role-permissions"></a>エンタープライズ アプリ ロールのアクセス許可

この記事では、2 つのエンタープライズ アプリのアクセス許可について説明します。 すべての例で更新アクセス許可が使用されます。

* スコープでユーザーとグループの割り当てを読み取るには、`microsoft.directory/servicePrincipals/appRoleAssignedTo/read` アクセス許可を付与します
* スコープでユーザーとグループの割り当てを管理するには、`microsoft.directory/servicePrincipals/appRoleAssignedTo/update` アクセス許可を付与します

更新アクセス許可を付与すると、担当者はエンタープライズ アプリへのユーザーとグループの割り当てを管理できるようになります。 ユーザーやグループの割り当てのスコープは、単一のアプリケーションに対して付与することも、すべてのアプリケーションに対して付与することもできます。 組織全体のレベルで付与された場合、担当者はすべてのアプリケーションの割り当てを管理できます。 アプリケーション レベルで行われた場合、担当者は、指定されたアプリケーションのみの割り当てを管理できます。

更新アクセス許可の付与は、次の 2 つの手順で行われます。

1. `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` アクセス許可を持つカスタム ロールを作成します
1. エンタープライズ アプリへのユーザーとグループの割り当てを管理するためのアクセス許可を、ユーザーまたはグループに付与します。 これは、スコープを組織全体のレベルまたは単一のアプリケーションに設定できる場合です。

## <a name="azure-portal"></a>Azure portal

### <a name="create-a-new-custom-role"></a>新しいカスタム ロールを作成する

>[!NOTE]
> カスタム ロールは組織全体のレベルで作成および管理され、組織の [概要] ページからのみ使用できます。

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択してから、 **[新しいカスタム ロール]** を選択します。

    ![Azure AD のロール一覧から新しいカスタム ロールを追加する](./media/custom-enterprise-apps/new-custom-role.png)

1. **[基本]** タブで、ロールの名前として「ユーザーとグループの割り当てを管理する」、ロールの説明として「ユーザーとグループの割り当てを管理するためのアクセス許可を付与する」などと入力して、 **[次へ]** を選択します。

    ![カスタム ロールの名前と説明を指定する](./media/custom-enterprise-apps/role-name-and-description.png)

1. **[アクセス許可]** タブで、検索ボックスに「microsoft.directory/servicePrincipals/appRoleAssignedTo/update」と入力し、必要なアクセス許可の横にあるチェックボックスをオンにしてから、 **[次へ]** を選択します。

    ![カスタム ロールのアクセス許可を追加する](./media/custom-enterprise-apps/role-custom-permissions.png)

1. **[確認と作成]** タブでアクセス許可を確認し、 **[作成]** を選択します。

    ![これでカスタム ロールを作成できるようになりました](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-portal"></a>Azure portal を使用してユーザーにロールを割り当てる

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択します。

1. **[ユーザーとグループの割り当てを管理するためのアクセス許可を付与する]** ロールを選択します。

    ![[ロールと管理者] を開き、カスタム ロールを検索する](./media/custom-enterprise-apps/select-custom-role.png)

1. **[割り当ての追加]** を選択し、目的のユーザーを選びます。その後、 **[選択]** をクリックして、ユーザーにロールの割り当てを追加します。

    ![ユーザーにカスタム ロールの割り当てを追加する](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>割り当てのヒント

* すべてのエンタープライズ アプリの組織全体のユーザーとグループのアクセスを管理するためのアクセス許可を担当者に付与するには、組織の Azure AD の **[概要]** ページにある組織全体の **[ロールと管理者]** 一覧から開始します。
* 特定のエンタープライズ アプリのユーザーとグループのアクセスを管理するためのアクセス許可を担当者に付与するには、Azure AD でそのアプリに移動し、そのアプリの **[ロールと管理者]** 一覧で開きます。 新しいカスタム ロールを選択し、ユーザーまたはグループの割り当てを完了します。 担当者は、特定のアプリのユーザーとグループのアクセスのみを管理できます。
* カスタム ロールの割り当てをテストするには、担当者としてサインインし、アプリケーションの **[ユーザーとグループ]** ページを開いて **[ユーザーの追加]** オプションが有効になっていることを確認します。

    ![ユーザーのアクセス許可を確認する](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="powershell"></a>PowerShell

詳細については、[カスタム ロールの作成と割り当て](custom-create.md)および [PowerShell を使用するリソース スコープでのカスタム ロールの割り当て](custom-assign-powershell.md)に関するページを参照してください。

### <a name="create-a-custom-role"></a>カスタム ロールを作成する

次の PowerShell スクリプトを実行して、新しいロールを作成します。

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>カスタム ロールを割り当てる

この PowerShell スクリプトを使用して、ロールを割り当てます。

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API で提供された例を使用して、カスタム ロールを作成します。 詳細については、[カスタム ロールの作成と割り当て](custom-create.md)および [Microsoft Graph API を使用するカスタム ロールの割り当て](custom-assign-graph.md)に関するページを参照してください。

カスタム ロールを作成するための HTTP 要求。

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-the-microsoft-graph-api"></a>Microsoft Graph API を使用してカスタム ロールを割り当てる

ロールの割り当てでは、セキュリティ プリンシパル ID (ユーザーまたはサービス プリンシパルにすることができます)、ロール定義 ID、および Azure AD リソース スコープを組み合わせます。 ロールの割り当ての要素について詳しくは、「[カスタム ロールの概要](custom-overview.md)」を参照してください

カスタム ロールを割り当てるための HTTP 要求。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>次のステップ

* [エンタープライズ アプリで使用可能なカスタム ロールのアクセス許可を確認する](custom-enterprise-app-permissions.md)
