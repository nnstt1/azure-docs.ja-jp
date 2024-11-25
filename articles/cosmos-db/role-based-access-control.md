---
title: Azure Cosmos DB での Azure ロールベースのアクセス制御
description: Azure Cosmos DB が Active Directory 統合 (Azure RBAC) を使用してデータベース保護を提供する方法について説明します。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/17/2021
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4925120adc86eb193e4b031ee7cebb23e7a71f45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247571"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB での Azure ロールベースのアクセス制御
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Azure Cosmos DB での Azure RBAC サポートは、管理プレーン操作にのみ適用されます。 この記事では、Azure Cosmos DB での管理プレーン操作に対するロールベースのアクセス制御について説明します。 データ プレーン操作を使用している場合、データは主キー、リソース トークン、Azure Cosmos DB RBAC のいずれかを使って保護されます。 データ プレーン操作に適用されるロールベースのアクセス制御の詳細については、[データ アクセスのセキュリティ保護](secure-access-to-data.md)および [Azure Cosmos DB RBAC](how-to-setup-rbac.md) に関する記事をご覧ください。

Azure Cosmos DB には、Azure Cosmos DB の一般的な管理シナリオに対応する Azure ロールベースのアクセス制御 (Azure RBAC) が組み込まれています。 Azure Active Directory にプロファイルを持つ個人は、これらの Azure ロールをユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てて、Azure Cosmos DB リソース上のリソースと操作へのアクセスを許可または拒否できます。 ロールの割り当ては、Azure Cosmos アカウント、データベース、コンテナー、およびオファー (スループット) へのアクセスを含む、コントロールプレーン アクセスのみに限定されています。

## <a name="built-in-roles"></a>組み込みのロール

Azure Cosmos DB でサポートされている組み込みのロールは次のとおりです。

|**組み込みのロール**  |**説明**  |
|---------|---------|
|[DocumentDB Account Contributor](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB アカウントを管理できます。|
|[Cosmos DB アカウント閲覧者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Cosmos DB アカウントのデータを読み取ることができます。|
|[Cosmos バックアップ オペレーター](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| Azure portal の定期的バックアップ対応データベースまたはコンテナーについての復元要求を送信できます。 Azure portal でバックアップ間隔と保有期間を変更できます。 データにアクセスしたり、Data Explorer を使用したりすることはできません。  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | 継続的バックアップモードで Azure Cosmos DB アカウントの復元操作を実行できます。|
|[Cosmos DB オペレーター](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos のアカウント、データベース、コンテナーをプロビジョニングできます。 データにアクセスしたり、Data Explorer を使用したりすることはできません。|

## <a name="identity-and-access-management-iam"></a>ID およびアクセス管理 (IAM)

Azure portal の **[アクセス制御 (IAM)]** ウィンドウは、Azure Cosmos リソースに対して Azure ロールベースのアクセス制御を構成するために使用されます。 ロールは、Active Directory 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。 組み込みのロールまたはカスタム ロールは、個人とグループに使用できます。 次のスクリーンショットは、Azure portal のアクセス制御 (IAM) を使用した Active Directory 統合 (Azure RBAC) を示しています。

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure portal のアクセス制御 (IAM) - データベース セキュリティを示しています。":::

## <a name="custom-roles"></a>カスタム ロール

組み込みのロールに加えて、ユーザーは Azure で[カスタム ロール](../role-based-access-control/custom-roles.md)を作成し、それらのロールを Active Directory テナント内のすべてのサブスクリプションにわたるサービス プリンシパルに適用することもできます。 カスタム ロールによって、ユーザーは、カスタムのリソース プロバイダー操作セットを使用して Azure ロール定義を作成できるようになります。 Azure Cosmos DB のカスタム ロールを構築するために使用できる操作の詳細については、「[Azure Cosmos DB のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)」を参照してください。

> [!TIP]
> Cosmos DB に格納されているデータにアクセスしたり、Azure portal でデータ エクスプローラーを使用したりする必要があるカスタム ロールには、`Microsoft.DocumentDB/databaseAccounts/listKeys/*` アクションが必要です。

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Azure Cosmos DB SDK からの変更の防止

アカウント キーを使用して接続しているクライアント (つまり Azure Cosmos SDK 経由で接続しているアプリケーション) がリソースを変更しないように、Azure Cosmos DB リソース プロバイダーをロックできます。 この機能は、運用環境における制御とガバナンスの強化を必要とするユーザーに適している可能性があります。 SDK からの変更を防止すると、コントロール プレーン操作に対するリソース ロックや診断ログなどの機能も有効になります。 Azure Cosmos DB SDK から接続しているクライアントは、Azure Cosmos アカウント、データベース、コンテナー、スループットのプロパティを変更できません。 Cosmos コンテナー自体に対するデータの読み取りと書き込みに関連する操作は影響を受けません。

この機能を有効にした場合、リソースへの変更を行うことができるのは、適切な Azure ロールを持つユーザーと、管理サービス ID を含む Azure Active Directory 資格情報だけです。

> [!WARNING]
> この機能を有効にすると、アプリケーションに影響を与える可能性があります。 有効にする前に、その影響について理解しておいてください。

### <a name="check-list-before-enabling"></a>有効にする前のチェック リスト

この設定により、アカウント キーを使用して接続するすべてのクライアント (すべての Cosmos DB SDK を含む)、アカウント キーを介して接続するすべてのツール、または Azure portal から、Cosmos リソースに変更を加えることができなくなります。 この機能を有効にした後にアプリケーションからの問題またはエラーを回避するには、この機能を有効にする前に、アプリケーションまたは Azure portal ユーザーが次の操作を実行するかどうかを確認します。

- プロパティを含めたり、リージョンを追加または削除したりする Cosmos アカウントに対する変更。

- データベースやコンテナーなどの子リソースの作成、削除。 これには、Cassandra、MongoDB、Gremlin、テーブル リソースなど、他の API のリソースが含まれます。

- データベースまたはコンテナー レベルのリソースでのスループットの更新。

- コンテナー プロパティ (インデックス ポリシー、TTL、一意キーなど) の変更。

- ストアド プロシージャ、トリガー、またはユーザー定義関数の変更。

このような操作がアプリケーションにより (または Azure portal 経由でユーザーにより) 実行される場合は、[ARM テンプレート](sql/manage-with-templates.md)、[PowerShell](sql/manage-with-powershell.md)、[Azure CLI](sql/manage-with-cli.md)、REST または [Azure 管理ライブラリ](https://github.com/Azure-Samples/cosmos-management-net)経由で実行されるように移行する必要があります。 Azure の管理は、[複数の言語](../index.yml?product=featured)で使用できることに留意してください。

### <a name="set-via-arm-template"></a>ARM テンプレートを使用して設定する

ARM テンプレートを使用してこのプロパティを設定するには、既存のテンプレートを更新するか、現在のデプロイ用の新しいテンプレートをエクスポートしてから、`databaseAccounts` リソースのプロパティに `"disableKeyBasedMetadataWriteAccess": true` を含めます。 次に、このプロパティ設定を含む Azure Resource Manager テンプレートの基本的な例を示します。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> このプロパティを使用して再デプロイする場合は、アカウントと子リソースの他のプロパティを必ず含めてください。 このテンプレートをそのままデプロイしないでください。すべてのアカウント プロパティがリセットされます。

### <a name="set-via-azure-cli"></a>Azure CLI を使用して設定する

Azure CLI を使用して有効にするには、次のコマンドを使用します。

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>PowerShell を使用して設定する

Azure PowerShell を使用して有効にするには、次のコマンドを使用します。

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)
- [Azure カスタム ロール](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
