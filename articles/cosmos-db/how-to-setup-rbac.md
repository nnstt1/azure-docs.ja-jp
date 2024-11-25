---
title: Azure AD を使用して Azure Cosmos DB アカウントのロールベースのアクセス制御を構成する
description: Azure Active Directory を使用して Azure Cosmos DB アカウントのロールベースのアクセス制御を構成する方法について説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: thweiss
ms.openlocfilehash: a659b4cdd404e9090fee177d383f26733aa4496b
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371849"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Active Directory を使用して Azure Cosmos DB アカウントのロールベースのアクセス制御を構成する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> この記事では、Azure Cosmos DB でのデータ プレーン操作に対するロールベースのアクセス制御について説明します。 管理プレーン操作を使用している場合は、管理プレーン操作に適用される[ロールベースのアクセス制御](role-based-access-control.md)に関する記事を参照してください。

Azure Cosmos DB では、次のことを可能にする組み込みのロールベースのアクセス制御 (RBAC) システムを公開しています。

- Azure Active Directory (Azure AD) の ID を使用してデータ要求を認証する。
- きめ細かいロールベースのアクセス許可モデルを使用してデータ要求を認可する。

## <a name="concepts"></a>概念

Azure Cosmos DB データ プレーン RBAC は、[Azure RBAC](../role-based-access-control/overview.md) などの他の RBAC システムでよく見られる概念に基づいて構築されています。

- [アクセス許可モデル](#permission-model)は、一連の **アクション** で構成されます。これらの各アクションは、1 つまたは複数のデータベース操作にマップされます。 アクションの例としては、アイテムの読み取り、アイテムの書き込み、クエリの実行などがあります。
- Azure Cosmos DB ユーザーは、許可されたアクションの一覧を含む **[ロールの定義](#role-definitions)** を作成します。
- ロールの定義は、 **[ロールの割り当て](#role-assignments)** によって特定の Azure AD ID に割り当てられます。 ロールの割り当てでは、ロールの定義が適用されるスコープも定義されます。現在、3 つのスコープがあります。
    - Azure Cosmos DB アカウント、
    - Azure Cosmos DB データベース、
    - Azure Cosmos DB コンテナー。

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC の概念":::

## <a name="permission-model"></a><a id="permission-model"></a> 権限モデル

> [!IMPORTANT]
> この権限モデルでは、データの読み書きをともなうデータベース操作のみが扱われます。 管理リソースに対する管理操作のようなものは "*扱われません*"。たとえば、次のようなものです。
> - データベースの作成、置換、削除
> - コンテナーの作成、置換、削除
> - コンテナー スループットの置換
> - ストアド プロシージャの作成、置換、削除、読み取り
> - トリガーの作成、置換、削除、読み取り
> - ユーザー定義関数の作成、置換、削除、読み取り
>
> Azure AD ID で管理操作を認証するために、"*Azure Cosmos DB データ プレーン SDK を使用することはできません*"。 代わりに、次のいずれかのオプションを使用して [Azure RBAC](role-based-access-control.md) を使用する必要があります。
> - [Azure Resource Manager テンプレート (ARM テンプレート)](./sql/manage-with-templates.md)
> - [Azure PowerShell スクリプト](./sql/manage-with-powershell.md)
> - [Azure CLI スクリプト](./sql/manage-with-cli.md)
> - 次で利用可能な Azure 管理ライブラリ:
>   - [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.CosmosDB/)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)
>   
> データベースの読み取りとコンテナーの読み取りは[メタデータ要求](#metadata-requests)と見なされます。 これらの操作へのアクセスは、次のセクションで説明するように許可できます。

次の表では、アクセス許可モデルによって公開されるすべてのアクションを示します。

| 名前 | 対応するデータベース操作 |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | アカウント メタデータの読み取り。 詳細については、[メタデータの要求](#metadata-requests)を参照してください。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | 新しい項目を作成します。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | 個々の項目をその ID とパーティション キーによって読み取ります (ポイント読み取り)。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | 既存のアイテムを置換します。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | アイテムを「Upsert」します。これは、存在しない場合は作成し、存在する場合は置き換えることを意味します。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | アイテムを削除します。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | [SQL クエリ](sql-query-getting-started.md)を実行します。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | コンテナーの[変更フィード](read-change-feed.md)から読み取ります。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | [ストアド プロシージャ](stored-procedures-triggers-udfs.md)を実行します。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | 複数の書き込みリージョン アカウントの[競合](conflict-resolution-policies.md)を管理します (つまり、競合フィードからのアイテムの一覧表示と削除を行います)。 |

ワイルドカードは、*コンテナー* レベルと *アイテム* レベルの両方でサポートされます。

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> メタデータの要求数

Azure Cosmos DB SDK を使用する場合、SDK は初期化中に読み取り専用のメタデータ要求を発行し、特定のデータ要求を処理します。 これらのメタデータ要求では、次のようなさまざまな構成の詳細がフェッチされます。 

- アカウントのグローバル構成。アカウントが使用できる Azure リージョンが含まれます。
- コンテナーのパーティション キーまたはそのインデックス作成ポリシー。
- コンテナーとそのアドレスを作成する物理パーティションの一覧。

アカウントに格納されているデータは取得 *されません*。

アクセス許可モデルで最大限の透明度を確保するため、これらのメタデータ要求は `Microsoft.DocumentDB/databaseAccounts/readMetadata` アクションによって明示的にカバーされます。 このアクションは、Azure Cosmos DB SDK のいずれかを使用して Azure Cosmos DB アカウントにアクセスするすべての状況で許可する必要があります。 これは、Azure Cosmos DB 階層内の任意のレベル (つまり、アカウント、データベース、またはコンテナー) で (ロールの割り当てによって) 割り当てることができます。

アクションによって許可される実際のメタデータ要求は、`Microsoft.DocumentDB/databaseAccounts/readMetadata` アクションが割り当てられているスコープによって異なります。

| Scope | アクションによって許可された要求 |
|---|---|
| Account | - アカウントのデータベースの一覧表示<br>- アカウントの各データベースについて、データベース スコープで許可されるアクション |
| データベース | - データベース メタデータの読み取り<br>- データベースのコンテナーの一覧表示<br>- データベースの各コンテナーについて、コンテナー スコープで許可されるアクション |
| コンテナー | - コンテナー メタデータの読み取り<br>- コンテナーの物理パーティションの一覧表示<br>- 各物理パーティションのアドレスの解決 |

## <a name="built-in-role-definitions"></a>組み込みのロールの定義

Azure Cosmos DB では、次の 2 つの組み込みロール定義が公開されています。

| id | 名前 | 含まれるアクション |
|---|---|---|
| 00000000-0000-0000-0000-000000000001 | Cosmos DB 組み込みデータ リーダー | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` |
| 00000000-0000-0000-0000-000000000002 | Cosmos DB 組み込みデータ共同作成者 | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*` |

## <a name="create-custom-role-definitions"></a><a id="role-definitions"></a> カスタム ロールの定義の作成

カスタム ロールの定義を作成するときは、次の情報を指定する必要があります。

- Azure Cosmos DB アカウントの名前。
- アカウントを含むリソース グループ。
- ロールの定義の種類: `CustomRole`。
- ロールの定義名。
- ロールで許可する [アクション](#permission-model) の一覧。
- ロールの定義を割り当てることができる 1 つまたは複数のスコープ。サポートされるスコープは次のとおりです。
    - `/` (アカウントレベル)、
    - `/dbs/<database-name>` (データベースレベル)、
    - `/dbs/<database-name>/colls/<container-name>` (コンテナーレベル)。

> [!NOTE]
> 後述する操作は、以下で使用できます。
> - Azure PowerShell: [Az.CosmosDB バージョン 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) 以降
> - [Azure CLI](/cli/azure/install-azure-cli): バージョン 2.24.0 以上

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

読み取りアクションのみを含む *MyReadOnlyRole* という名前のロールを作成します。

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

すべてのアクションを含む *MyReadWriteRole* という名前のロールを作成します。

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

ID を取得するために作成したロールの定義を一覧表示します。

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

読み取りアクションのみを含む *MyReadOnlyRole* という名前のロールを作成します。

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

すべてのアクションを含む *MyReadWriteRole* という名前のロールを作成します。

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

ID を取得するために作成したロールの定義を一覧表示します。

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

Azure Resource Manager テンプレートを使用してロールの定義を作成する方法のリファレンスと例については、[こちらのページ](/rest/api/cosmos-db-resource-provider/2021-04-15/sql-resources/create-update-sql-role-definition)を参照してください。

## <a name="create-role-assignments"></a><a id="role-assignments"></a> ロールの割り当ての作成

組み込みまたはカスタムのロールの定義を、自身の Azure AD に関連付けることができます。 ロールの割り当てを作成するときは、次の情報を指定する必要があります。

- Azure Cosmos DB アカウントの名前。
- アカウントを含むリソース グループ。
- 割り当てるロール定義の ID。
- ロールの定義を割り当てる必要がある ID のプリンシパル ID。
- ロールの割り当てのスコープ。サポートされるスコープは次のとおりです。
    - `/` (アカウントレベル)
    - `/dbs/<database-name>` (データベースレベル)
    - `/dbs/<database-name>/colls/<container-name>` (コンテナーレベル)

  スコープは、いずれかのロール定義を割り当て可能なスコープと一致しているか、そのサブスコープである必要があります。

> [!NOTE]
> サービス プリンシパルのロール割り当てを作成する場合は、**Azure Active Directory** portal ブレードの **[エンタープライズ アプリケーション]** セクションにある **[オブジェクト ID]** を使用してください。

> [!NOTE]
> 後述する操作は、以下で使用できます。
> - Azure PowerShell: [Az.CosmosDB バージョン 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) 以降
> - [Azure CLI](/cli/azure/install-azure-cli): バージョン 2.24.0 以上

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

ID にロールを割り当てます。

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" # as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

ID にロールを割り当てます。

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' # as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

Azure Resource Manager テンプレートを使用してロールの割り当てを作成する方法のリファレンスと例については、[こちらのページ](/rest/api/cosmos-db-resource-provider/2021-04-15/sql-resources/create-update-sql-role-assignment)を参照してください。

## <a name="initialize-the-sdk-with-azure-ad"></a>Azure AD を使用して SDK を初期化する

Azure Cosmos DB RBAC をアプリケーションで使用するには、Azure Cosmos DB SDK を初期化する方法を更新する必要があります。 アカウントのプライマリ キーを渡す代わりに、`TokenCredential` クラスのインスタンスを渡す必要があります。 このインスタンスは、使用する ID に代わって、AAD トークンをフェッチするために必要なコンテキストを Azure Cosmos DB SDK に提供します。

`TokenCredential` インスタンスの作成方法は、この記事では取り扱いません。 使用する AAD ID の種類 (ユーザー プリンシパル、サービス プリンシパル、グループなど) に応じて、このようなインスタンスを作成するにはさまざまな方法があります。 最も重要なのは、`TokenCredential` インスタンスが自分のロールを割り当てた ID (プリンシパル ID) を解決できる必要があることです。 `TokenCredential` クラスを作成する例を次に示します。

- [.NET の場合](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java の場合](/java/api/overview/azure/identity-readme#credential-classes)
- [JavaScript の場合](/javascript/api/overview/azure/identity-readme#credential-classes)

次の例では、`ClientSecretCredential` インスタンスでサービス プリンシパルを使用しています。

### <a name="in-net"></a>.NET の場合

Azure Cosmos DB RBAC は現在、[.NET SDK V3](sql-api-sdk-dotnet-standard.md) でサポートされています。

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>Java の場合

Azure Cosmos DB RBAC は現在、[Java SDK V4](sql-api-sdk-java-v4.md) でサポートされています。

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>JavaScript の場合

Azure Cosmos DB RBAC は現在、[JavaScript SDK V3](sql-api-sdk-node.md) でサポートされています。

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="authenticate-requests-on-the-rest-api"></a>REST API で要求を認証

[REST API 承認ヘッダー](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)を構築するときは、次の例に示すように、**type** パラメーターを **aad** に設定し、ハッシュ署名 **(sig)** を **OAuth トークン** に設定します。

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="use-data-explorer"></a>データ エクスプローラーの使用

> [!NOTE]
> Azure portal で公開されているデータ エクスプローラーでは、まだ Azure Cosmos DB RBAC がサポートされていません。 データの探索に Azure AD ID を使用するには、代わりに [Azure Cosmos DB Explorer](https://cosmos.azure.com/?feature.enableAadDataPlane=true) を使用する必要があります。

[Azure Cosmos DB Explorer](https://cosmos.azure.com/?feature.enableAadDataPlane=true) に特定の `?feature.enableAadDataPlane=true` クエリ パラメーターでアクセスしてサインインすると、データにアクセスする際に次のロジックが使用されます。

1. アカウントの主キーをフェッチする要求が、サインインした ID に代わって試行されます。 この要求が成功すると、アカウントのデータは主キーを使用してアクセスされます。
1. サインインした ID がアカウントの主キーのフェッチを許可されていない場合、この ID がデータ アクセスを認証するために直接使用されます。 このモードでは、ID に[適切なロールの定義を割り当て](#role-assignments)てデータにアクセスできるようにしなければなりません。

## <a name="audit-data-requests"></a>監査データの要求

Azure Cosmos DB RBAC を使用する場合、[診断ログ](cosmosdb-monitor-resource-logs.md)は各データ操作の ID と認証情報によって拡張されます。 これにより、詳細な監査を実行し、Azure Cosmos DB アカウントに送信されるすべてのデータ要求に使用される AAD ID を取得できます。

この追加情報は **DataPlaneRequests** ログ カテゴリにあり、2 つの追加列で構成されています。

- `aadPrincipalId_g` は、要求を認証するために使用された AAD ID のプリンシパル ID を表示します。
- `aadAppliedRoleAssignmentId_g` は、要求を認可するときに使用された[ロール割り当て](#role-assignments)を表示します。

## <a name="enforcing-rbac-as-the-only-authentication-method"></a><a id="disable-local-auth"></a>唯一の認証方法としての RBAC の適用

RBAC を介して排他的に Azure Cosmos DB に接続するようにクライアントに強制するには、アカウントの主キーまたはセカンダリ キーを無効にすることができます。 その場合、主キーまたはセカンダリ キーまたはリソース トークンを使用する着信要求はアクティブに拒否されます。

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

Azure Resource Manager テンプレートを使用して Azure Cosmos DB アカウントを作成または更新する場合は、`disableLocalAuth` プロパティを `true` に設定します。

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "properties": {
            "disableLocalAuth": true,
            // ...
        },
        // ...
    },
    // ...
 ]
```

## <a name="limits"></a>制限

- Azure Cosmos DB アカウントごとに、最大 100 のロール定義と 2000 のロール割り当てを作成できます。
- ロールの定義は、Azure Cosmos DB アカウントと同じ Azure AD テナントに属する Azure AD ID にのみ割り当てることができます。
- Azure AD グループの解決は、200 を超えるグループに属している ID については現在サポートされていません。
- Azure AD トークンは、現在、Azure Cosmos DB サービスに送信される個々の要求と共にヘッダーとして渡されるため、全体的なペイロード サイズが増加します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>RBAC では、どの Azure Cosmos DB API がサポートされていますか。

現在は、SQL API のみがサポートされています。

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Azure portal からロールの定義とロールの割り当てを管理することはできますか。

ロール管理の Azure portal サポートはまだ利用できません。

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Azure Cosmos DB SQL API のどの SDK が RBAC をサポートしていますか。

現在は、[.NET V3](sql-api-sdk-dotnet-standard.md)、[Java V4](sql-api-sdk-java-v4.md)および [JavaScript V3](sql-api-sdk-node.md) SDK がサポートされています。

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Azure AD トークンは、有効期限が切れると Azure Cosmos DB SDK によって自動的に更新されますか。

はい。

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primarysecondary-keys-when-using-rbac"></a>RBAC を使用する場合、アカウントのプライマリまたはセカンダリ キーの使用を無効にすることはできますか。

はい。「[唯一の認証方法としての RBAC の適用](#disable-local-auth)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Cosmos DB 内のデータへのセキュリティで保護されたアクセス](secure-access-to-data.md)の概要を理解します。
- 詳細については、[Azure Cosmos DB 管理の RBAC](role-based-access-control.md) に関するページを参照してください。
