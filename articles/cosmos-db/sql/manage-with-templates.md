---
title: Resource Manager テンプレートを使用して Azure Cosmos DB を作成および管理する
description: Azure Resource Manager テンプレートを使用してコア (SQL) 用 Azure Cosmos DB API を作成および構成する
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0f511f51183e167c4ad42c6c2f5a8425b49bcbb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113935"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB コア (SQL) API リソースを管理する

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では、ご利用の Azure Cosmos DB アカウント、データベース、およびコンテナーのデプロイと管理に役立つ Azure Resource Manager テンプレートの使用方法について説明します。

この記事では、コア (SQL) API アカウント用の Azure Resource Manager テンプレートの例のみを示します。 [Cassandra](../cassandra/templates-samples.md)、[Gremlin](../graph/resource-manager-template-samples.md)、[MongoDB](../mongodb/resource-manager-template-samples.md)、[Table](../table/resource-manager-templates.md) の API の例もあります。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。
> * Azure Cosmos DB リソースの名前を変更することはできません。これは、Azure Resource Manager とリソース URI が連携する方法に違反するためです。
> * データベース単位でスループットをプロビジョニングし、すべてのコンテナー間で共有するには、データベースのオプションのプロパティにスループットの値を設定します。

以下の Azure Cosmos DB リソースを作成するには、次のサンプル テンプレートを新しい json ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター json ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure portal](../../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)、および [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md) を含むさまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>自動スケーリング スループットを備える Azure Cosmos アカウント

このテンプレートでは、一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。データベースとコンテナーは、ほとんどのポリシー オプションが有効になっている自動スケーリング スループット用に構成されています。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>分析ストアを使用する Azure Cosmos アカウント

このテンプレートを使用すると、Analytical TTL が有効なコンテナーと手動または自動スケール スループットのオプションを使用して、1 つのリージョンに Azure Cosmos アカウントを作成できます。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える Azure Cosmos アカウント

このテンプレートでは、一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。データベースとコンテナーは、ほとんどのポリシー オプションが有効になっている標準スループット用に構成されています。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>サーバー側機能を備える Azure Cosmos DB コンテナー

このテンプレートでは、Azure Cosmos アカウント、データベース、およびストアド プロシージャ、トリガー、ユーザー定義関数を含むコンテナーを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure AD と RBAC を使用した Azure Cosmos DB アカウント

このテンプレートは、AAD ID 用の SQL Cosmos アカウント、ネイティブに管理されるロールの定義、およびネイティブに管理されるロールの割り当てを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-rbac%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Free レベルの Azure Cosmos DB アカウント

このテンプレートでは、Free レベルの Azure Cosmos アカウントと、最大 25 個のコンテナーで共有できる共有スループットを備えるデータベースを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Azure Resource Manager のドキュメント](../../azure-resource-manager/index.yml)
* [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../../azure-resource-manager/templates/common-deployment-errors.md)