---
title: MongoDB 用 Azure Cosmos DB API リソース スループットのプロビジョニング
description: MongoDB 用 Azure Cosmos DB API リソースでコンテナー、データベース、自動スケールのスループットをプロビジョニングする方法について説明します。 Azure portal、CLI、PowerShell、その他のさまざまな SDK を使用します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: b30ac109a8186f39e29ff96ba797d0b8c98ea41c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033367"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>MongoDB 用 Azure Cosmos DB API リソースのデータベース、コンテナー、または自動スケール スループットのプロビジョニング
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

この記事では、MongoDB 用 Azure Cosmos DB API のスループットをプロビジョニングする方法について説明します。 標準 (手動) または自動スケーリングのスループットは、コンテナーを対象にプロビジョニングできるほか、データベースを対象にプロビジョニングして、それをデータベース内の複数のコンテナーで共有することもできます。 スループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

別の API を使用している場合は、[SQL API](../how-to-provision-container-throughput.md)、[Cassandra API](../cassandra/how-to-provision-throughput-cassandra.md)、[Gremlin API](../how-to-provision-throughput-gremlin.md) のスループット プロビジョニングに関する記事を参照してください。

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure portal

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-mongodb-dotnet.md#create-a-database-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。 データベース レベルでスループットをプロビジョニングする場合は、 **[データベースのスループットのプロビジョニング]** オプションを選択します。
   * コレクション ID を入力します。
   * パーティション キーの値 (`/ItemID` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="データベース レベルのスループットで新しいコレクションを作成しているときの、データ エクスプローラーのスクリーンショット":::

> [!Note]
> MongoDB 用 Azure Cosmos DB API を使用して構成された Azure Cosmos DB アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myShardKey` を使用します。

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager テンプレートを使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure Resource Manager テンプレート](resource-manager-template-samples.md)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [Azure Cosmos DB における要求ユニットとスループット](../request-units.md)
* Azure Cosmos DB に移行する容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 既存のデータベース クラスター内の仮想コアとサーバー数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください