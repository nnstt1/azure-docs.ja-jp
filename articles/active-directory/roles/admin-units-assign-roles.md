---
title: 管理単位スコープを使用してロールを割り当ておよび一覧表示する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位を使用してロールの割り当てのスコープを制限します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b34eafac248bc0fd06076550e784a061573a712
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734500"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>スコープ付きロールを管理単位に割り当てる

Azure Active Directory (Azure AD) では、きめ細かい管理制御を行うために、1 つ以上の管理単位に制限されたスコープでユーザーを Azure AD ロールに割り当てることができます。

## <a name="prerequisites"></a>前提条件

- 管理単位の各管理者に対する Azure AD Premium P1 または P2 ライセンス
- 管理単位のメンバーに対する Azure AD Free ライセンス
- 特権ロール管理者または全体管理者
- PowerShell を使用する場合は、AzureAD モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。


## <a name="available-roles"></a>使用可能なロール

Role  |  説明
----- |  -----------
認証管理者  |  割り当てられた管理単位内でのみ、管理者以外のユーザーの認証方法の情報を表示、設定、リセットするためにアクセスできます。
グループ管理者  |  割り当てられた管理単位内でのみ、グループとグループ設定 (名前付けポリシーや有効期限ポリシーなど) のすべての側面を管理できます。
ヘルプデスク管理者  |  割り当てられた管理単位内でのみ、管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。
ライセンス管理者  |  管理単位内でのみ、ライセンスの割り当て、削除、更新を行うことができます。
パスワード管理者  |  割り当てられた管理単位内でのみ、管理者以外のユーザーとパスワード管理者のパスワードをリセットできます。
ユーザー管理者  |  割り当てられた管理単位内でのみ、ユーザーとグループのすべての側面 (制限付き管理者のパスワードのリセットを含む) を管理できます。

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>スコープ付きロールに割り当てることができるセキュリティ プリンシパル

次のセキュリティ プリンシパルは、管理単位スコープ付きのロールに割り当てることができます。

* ユーザー
* ロール割り当て可能な Azure AD グループ
* サービス プリンシパル名 (SPN)

## <a name="assign-a-scoped-role"></a>スコープ付きロールを割り当てる

Azure portal、PowerShell、または Microsoft Graph を使用して、スコープ付きロールを割り当てることができます。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[管理単位]** を選択し、ユーザー ロール スコープの割り当て先にする管理単位を選択します。 

1. 左側のペインで、 **[ロールと管理者]** を選択して、利用可能なすべてのロールを一覧表示します。

   ![ロール スコープを割り当てる管理単位を選択するための [ロールと管理者] ペインのスクリーンショット。](./media/admin-units-assign-roles/select-role-to-scope.png)

1. 割り当てるロールを選択し、 **[割り当ての追加]** を選択します。 

1. **[割り当ての追加]** ペインで、ロールに割り当てられる 1 人以上のユーザーを選択します。

   ![スコープを設定するロールを選択してから、[割り当ての追加] を選択する](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Azure AD Privileged Identity Management (PIM) を使用して管理単位でロールを割り当てるには、「[PIM で Azure AD ロールを割り当てる](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)」を参照してください。

### <a name="powershell"></a>PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

強調表示されているセクションを、特定の環境で必要なように変更できます。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

要求

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
本文

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>管理単位内のスコープ付き管理者の一覧を表示する

Azure portal、PowerShell、または Microsoft Graph を使用して、スコープ付き管理者の一覧を表示できます。

### <a name="azure-portal"></a>Azure portal

[Azure AD の管理単位セクション](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)に、管理単位スコープを使用して作成されたすべてのロール割り当てを表示できます。 

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[管理単位]** を選択し、表示するロール割り当ての一覧の管理単位を選択します。 

1. **[ロールと管理者]** を選択し、ロールを開いて管理単位内の割り当てを表示します。

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

強調表示されているセクションを、ご利用の環境で必要なように変更できます。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

要求

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Body

```http
{}
```

## <a name="next-steps"></a>次のステップ

- [Azure AD グループを使用してロールの割り当てを管理する](groups-concept.md)
- [グループに割り当てられている Azure AD ロールをトラブルシューティングする](groups-faq-troubleshooting.yml)
