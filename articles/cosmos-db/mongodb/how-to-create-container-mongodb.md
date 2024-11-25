---
title: MongoDB 用 Azure Cosmos DB API でコンテナーを作成する
description: Azure portal、.NET、Java、Python、Node.js、およびその他の SDK を使用して、MongoDB 用 Azure Cosmos DB API で コンテナーを作成する方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-csharp
ms.openlocfilehash: 40458740092c7b462683512e3c419eff417019da
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038525"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB API でコンテナーを作成する
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

この記事では、MongoDB 用 Azure Cosmos DB API でコンテナーを作成するさまざまな方法について説明します。 Azure portal、Azure CLI、PowerShell、またはサポートされている SDK を使用してコンテナーを作成する方法を示します。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

この記事では、MongoDB 用 Azure Cosmos DB API でコンテナーを作成するさまざまな方法について説明します。 異なる API を使用している場合は、[SQL API](../how-to-create-container.md)、[Cassandra API](../cassandra/how-to-create-container-cassandra.md)、[Gremlin API](../how-to-create-container-gremlin.md)、[Table API](../table/how-to-create-container.md) に関する記事を参照してコンテナーを作成してください。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Azure portal を使用した作成

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-mongodb-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * シャード キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="MongoDB 用 Azure Cosmos DB API の [コンテナーの追加] ダイアログ ボックスのスクリーンショット":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>.NET SDK を使用して作成する

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB ワイヤ プロトコルでは、[要求ユニット](../request-units.md)という概念は理解されません。 プロビジョニングされたスループットを持つ新しいコレクションを作成するには、Azure portal または SQL API 用の Cosmos DB SDK を使用します。

コレクションの作成時にタイムアウト例外が発生した場合は、コレクションが正常に作成されたかどうかを確認する読み取り操作を実行します。 この読み取り操作では、コレクションの作成操作が成功するまで例外がスローされます。 作成操作でサポートされている状態コードの一覧については、記事「[Azure Cosmos DB の HTTP 状態コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」を参照してください。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLI を使用して作成する

[Azure CLI を使用して Azure Cosmos DB for MongoDB API のコレクションを作成します](../scripts/cli/mongodb/create.md)。 すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="create-using-powershell"></a>PowerShell を使用して作成する

[Powershell を使用して MongoDB 用 Azure Cosmos DB API のコレクションを作成します](../scripts/powershell/mongodb/create.md)。 すべての Azure Cosmos DB API にわたる PowerShell の全サンプルの一覧については、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してコンテナーを作成する

[Azure Resource Manager テンプレートを使用して Azure Cosmos DB for MongoDB API のコレクションを作成します](../manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput)。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB でのパーティション分割](../partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](../request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](../set-throughput.md)
* [Azure Cosmos アカウントの使用](../account-databases-containers-items.md)
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードの通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください