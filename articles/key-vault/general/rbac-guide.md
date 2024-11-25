---
title: Azure RBAC を使用して Azure キー コンテナーへのアクセス許可をアプリケーションに付与する | Microsoft Docs
description: Azure ロールベースのアクセス制御を使用して、キー、シークレット、および証明書へのアクセス権を付与する方法について説明します。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c92b17158beee9d1f6c60becb858564555bf56bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048446"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Azure のロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する

> [!NOTE]
> Key Vault リソース プロバイダーでは、**コンテナー** と **マネージド HSM** という 2 種類のリソースがサポートされています。 この記事で説明するアクセス制御は、**コンテナー** にのみ適用されます。 マネージド HSM のアクセス制御の詳細については、「[マネージド HSM アクセス制御](../managed-hsm/access-control.md)」を参照してください。

Azure ロールベースのアクセス制御 (Azure RBAC) は [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 上に構築された承認システムであり、Azure リソースに対するアクセスをきめ細かく管理できます。

Azure RBAC を使用すると、キー、シークレット、および証明書のアクセス許可を管理できます。 すべてのキー コンテナーにわたるすべてのアクセス許可を管理できる 1 つの場所が用意されています。 

Azure RBAC モデルには、さまざまなスコープ レベル (管理グループ、サブスクリプション、リソース グループ、または個別のリソース) にアクセス許可を設定する機能があります。  また、Key Vault 用の Azure RBAC には、個々のキー、シークレット、および証明書に対して個別のアクセス許可を持たせる機能もあります

詳細については、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) に関するページを参照してください。

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>個々のキー、シークレット、および証明書のベスト プラクティス

環境 (開発、実稼働前、および実稼働) ごとにアプリケーションごとのコンテナーを使用することをお勧めします。

個々のキー、シークレット、および証明書のアクセス許可は、次のように特定のシナリオにのみ使用するようにします。

-   複数のアプリケーションなど間に個々のシークレットを共有するアプリケーションが他のアプリケーションのデータにアクセスする必要がある
-   顧客キーを使用したテナント間の暗号化  にーコンテナーのキー顧客キやISVなどを使用してデータを暗号化する

Azure Key Vault 管理ガイドラインの詳細については、以下を参照してください。

- [Azure Key Vault のベスト プラクティス](best-practices.md)
- [Azure Key Vault サービスの制限](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Key Vault データ プレーン操作のための Azure の組み込みロール

> [!NOTE]
> `Key Vault Contributor` ロールは、キー コンテナーを管理するための管理プレーン操作用です。 キー、シークレット、証明書へのアクセスは許可されていません。

| 組み込みのロール | 説明 | id |
| --- | --- | --- |
| Key Vault Administrator| キー コンテナーとその内部にあるすべてのオブジェクト (証明書、キー、シークレットを含む) に対して、すべてのデータ プレーン操作を実行します。 キー コンテナー リソースの管理やロール割り当ての管理はできません。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault Certificates Officer | キーコンテナーの証明書に対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer | キーコンテナーのキーに対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault Crypto Service Encryption User | キーのメタデータを読み取り、wrap および unwrap 操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault Crypto User  | キーを使用した暗号化操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault Reader | キー コンテナーとその証明書、キー、シークレットのメタデータを読み取ります。 シークレット コンテンツやキー マテリアルなどの機密値を読み取ることはできません。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer| キーコンテナーのシークレットに対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault Secrets User | シークレット コンテンツを読み取ります。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 4633458b-17de-408a-b874-0445c86b69e6 |

Azure 組み込みロールの定義の詳細については、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Key Vault での Azure RBAC シークレット、キー、および証明書のアクセス許可の使用

Key Vault の新しい Azure RBAC アクセス許可モデルには、コンテナー アクセス ポリシーのアクセス許可モデルに代わるものです。 

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションが必要です。 これがない場合、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。

ロールの割り当てを追加するには、`Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)や[所有者](../../role-based-access-control/built-in-roles.md#owner)など) が必要です。

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Key Vault で Azure RBAC アクセス許可を有効にする

> [!NOTE]
> アクセス許可モデルを変更するには、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールの一部である、"Microsoft.Authorization/roleAssignments/write" アクセス許可が必要です。 "サービス管理者" や "共同管理者" などの従来のサブスクリプション管理者ロールはサポートされていません。

1.  新しいキー コンテナーで Azure RBAC アクセス許可を有効にします。

    ![Azure RBAC アクセス許可を有効にする - 新しいコンテナー](../media/rbac/image-1.png)

2.  既存のキー コンテナーで Azure RBAC アクセス許可を有効にします。

    ![Azure RBAC アクセス許可を有効にする - 既存のコンテナー](../media/rbac/image-2.png)

> [!IMPORTANT]
> Azure RBAC アクセス許可モデルを設定すると、すべてのアクセス ポリシーのアクセス許可が無効になります。 同等の Azure ロールが割り当てられていない場合、停止が発生する可能性があります。

### <a name="assign-role"></a>ロールを割り当てる

> [!Note]
> スクリプトでは、ロール名ではなく一意のロール ID を使用することをお勧めします。 こうすると、ロールの名前が変更されても、スクリプトは引き続き機能します。 このドキュメントでは、読みやすいようにロール名を使用しています。

ロールの割り当てを作成するには、次のコマンドを実行します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

詳細については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```

詳細については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

---

Azure portal を使用してロールを割り当てるには、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。  Azure portal では、[アクセス制御 (IAM)] タブのすべてのリソースに [Azure ロールの割り当て] 画面を使用できます。

### <a name="resource-group-scope-role-assignment"></a>リソース グループのスコープ ロールの割り当て

1. 自分のキー コンテナーが含まれているリソース グループに移動します。

    ![ロールの割り当て - リソース グループ](../media/rbac/image-4.png)

1. **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | Key Vault Reader |
    | アクセスの割り当て先 | 現在のユーザー |
    | メンバー | メール アドレスで検索 |

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

詳細については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
詳細については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

---

前述のロール割り当てには、Key Vault 内の Key Vault オブジェクトを一覧表示する機能があります。

### <a name="key-vault-scope-role-assignment"></a>Key Vault スコープのロールの割り当て

1. Key Vault \> [アクセス制御 (IAM)] タブに移動します
1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | Key Vault Secrets Officer |
    | アクセスの割り当て先 | 現在のユーザー |
    | メンバー | メール アドレスで検索 |

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

詳細については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

詳細については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

---

### <a name="secret-scope-role-assignment"></a>シークレット スコープのロールの割り当て

1. 以前に作成したシークレットを開きます。

1. [アクセス制御 (IAM)] タブをクリックします

    ![ロールの割り当て - シークレット](../media/rbac/image-8.png)

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | Key Vault Secrets Officer |
    | アクセスの割り当て先 | 現在のユーザー |
    | メンバー | メール アドレスで検索 |

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

詳細については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

詳細については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

---

### <a name="test-and-verify"></a>テストして検証する

> [!NOTE]
> ブラウザーにはキャッシュが使用されているため、ロールの割り当てを削除した後はページの更新が必要です。<br>
> ロールの割り当てが更新されるまで数分かかります

1. Key Vault レベルで "Key Vault Secrets Officer" ロールを使用せずに新しいシークレットの追加を検証します。

キー コンテナーの [アクセス制御 (IAM)] タブに移動し、このリソースへの "Key Vault Secrets Officer" ロールの割り当てを削除します。

![割り当ての削除 - Key Vault](../media/rbac/image-9.png)

以前に作成したシークレットに移動します。 すべてのシークレットのプロパティを表示できます。

![アクセス権を持つシークレット ビュー](../media/rbac/image-10.png)

新しいシークレットを作成します ([シークレット] \> [+Generate/Import]\(生成またはインポート\))。次のエラーが表示されます。

   ![新しいシークレットを作成する](../media/rbac/image-11.png)

2.  シークレット レベルで "Key Vault Secret Officer" ロールを使用せずにシークレットの編集を検証します。

-   以前に作成したシークレットの [アクセス制御 (IAM)] タブに移動し、このリソースへの "Key Vault Secrets Officer" ロールの割り当てを削除します。

-   以前に作成したシークレットに移動します。 シークレットのプロパティが表示されます。

   ![アクセス権のないシークレット ビュー](../media/rbac/image-12.png)

3. Key Vault レベルで Reader ロールを使用せずにシークレットの閲覧を検証します。

-   キー コンテナーのリソース グループの [アクセス制御 (IAM)] タブに移動し、"Key Vault Reader" ロールの割り当てを削除します。

-   Key Vault の [シークレット] タブに移動すると、次のエラーが表示されます。

   ![[シークレット] タブ - エラー](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>カスタム ロールの作成 

[az role definition create コマンド](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

カスタム ロールの作成方法の詳細については、以下を参照してください。

[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>既知の制限とパフォーマンス

-   Key Vault データ プレーン RBAC は、Azure Lighthouse のようなマルチ テナント シナリオではサポートされていません
-   サブスクリプションごとに 2,000 件の Azure ロールの割り当て
-   ロールの割り当ての待機時間: 現在想定されているパフォーマンスでは、ロールの割り当てが変更されてから、ロールが適用されるまでに最大 10 分 (600 秒) かかります

## <a name="learn-more"></a>詳細情報
1. [MANAGEMENTGROUP | SUBSCRIPTION | RESOURCEGROUP | RESOURCE] スコープで [ROLENAME] ロールを [USER | GROUP | SERVICEPRINCIPAL | MANAGEDIDENTITY] に割り当てます。


- [Azure RBAC の概要](../../role-based-access-control/overview.md)
- [Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)
- [カスタム ロールのチュートリアル](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Azure Key Vault のベスト プラクティス](best-practices.md)
