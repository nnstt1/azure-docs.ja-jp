---
title: Azure Cosmos DB の MongoDB 用 API のデータを管理するための MongoDB 拡張コマンド
description: この記事では、MongoDB 拡張コマンドを使用して、Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理する方法について説明します。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 07/30/2021
ms.custom: devx-track-js
ms.openlocfilehash: f3f865d28452c6930ef53f5882e59570b07ef551
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016608"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理するために MongoDB 拡張コマンドを使用する 
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

次のドキュメントには、MongoDB 用の Azure Cosmos DB の API に固有のカスタム アクション コマンドが含まれています。 これらのコマンドを使用して、[Azure Cosmos DB 容量モデル](../account-databases-containers-items.md)に固有のデータベース リソースを作成および取得できます。

Azure Cosmos DB の MongoDB 用 API を使用することで、MongoDB アプリへの投資を保持しながら、グローバル配布、自動シャーディング、高可用性、低待機時間の保証、自動化、保存時の暗号化、バックアップなど多くの Cosmos DB の利点を活用できます。 Azure Cosmos DB の MongoDB 用 API との通信は、オープン ソースで公開されている任意の [MongoDB クライアント ドライバー](https://docs.mongodb.org/ecosystem/drivers)を使って行うことができます。 Azure Cosmos DB の MongoDB 用 API では、MongoDB [ワイヤ プロトコル](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)に従うことにより、既存のクライアント ドライバーを利用できます。

## <a name="mongodb-protocol-support"></a>MongoDB のプロトコル サポート

Azure Cosmos DB の MongoDB 用 API は、MongoDB サーバー バージョン 4.0、3.6、3.2 と互換性があります。 詳細については、[4.0](feature-support-40.md)、[3.6](feature-support-36.md)、[3.2](feature-support-32.md) でサポートされている機能と構文を参照してください。 

次の拡張コマンドを使用すると、データベース要求を使用して Azure Cosmos DB 固有のリソースを作成および変更できます。

* [データベースの作成](#create-database)
* [データベースの更新](#update-database)
* [データベースの取得](#get-database)
* [コレクションの作成](#create-collection)
* [コレクションの更新](#update-collection)
* [コレクションの取得](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>データベースの作成

データベースの作成拡張コマンドは、新しい MongoDB データベースを作成します。 データベース名は、`use database` コマンドによって設定されたデータベース コンテキストから使用できます。 次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| `customAction`   |  `string`  |   カスタム コマンドの名前。"CreateDatabase" にする必要があります。      |
| `offerThroughput` | `int`  | データベースに設定したプロビジョニング済みスループット。 このパラメーターは省略可能です。 |
| `autoScaleSettings` | `Object` | [自動スケーリング モード](../provision-throughput-autoscale.md)の場合は必須です。 このオブジェクトには、自動スケーリング容量モードに関連付けられている設定が含まれています。 `maxThroughput` の値を設定できます。これにより、コレクションがその量まで動的に増加する、要求ユニットの最大量が示されます。 |

### <a name="output"></a>出力

コマンドが成功した場合は、次の応答が返されます。

```javascript
{ "ok" : 1 }
```

出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

#### <a name="create-a-database"></a>データベースを作成する

すべての既定値を使用する `"test"` という名前のデータベースを作成するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

このコマンドにより、データベースレベルのスループットを指定せずにデータベースが作成されます。 これは、このデータベース内のコレクションで、使用する必要があるスループットの量を指定する必要があることを意味します。

#### <a name="create-a-database-with-throughput"></a>スループットを指定してデータベースを作成する

`"test"` という名前でデータベースを作成し、[データベースレベルの](../set-throughput.md#set-throughput-on-a-database)プロビジョニング済みスループットを 1000 RU に指定するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

これによってデータベースが作成され、スループットが設定されます。 コレクションが[特定のスループット レベル](../set-throughput.md#set-throughput-on-a-database-and-a-container)で作成される場合を除き、このデータベース内のすべてのコレクションは、設定されたスループットを共有します。

#### <a name="create-a-database-with-autoscale-throughput"></a>自動スケーリング スループットのデータベースを作成する

`"test"` という名前のデータベースを作成し、[データベースレベルで](../set-throughput.md#set-throughput-on-a-database)自動スケーリング最大スループットを 20,000 RU/秒に指定するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a>データベースの更新

データベースの更新拡張コマンドは、指定したデータベースに関連付けられているプロパティを更新します。 プロビジョニングされたスループットから自動スケーリングへのデータベースの変更やその逆の変更は、Azure Portal でのみサポートされています。 次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| `customAction`    |    `string`     |   カスタム コマンドの名前。 "UpdateDatabase" にする必要があります。      |
|  `offerThroughput`   |  `int`       |     データベースで[データベースレベルのスループット](../set-throughput.md#set-throughput-on-a-database)が使用されている場合に、データベースに対して設定する新しいプロビジョニング済みスループット  |
| `autoScaleSettings` | `Object` | [自動スケーリング モード](../provision-throughput-autoscale.md)の場合は必須です。 このオブジェクトには、自動スケーリング容量モードに関連付けられている設定が含まれています。 `maxThroughput` の値を設定できます。これにより、データベースがその量まで動的に増加する、要求ユニットの最大量が示されます。 |

このコマンドは、セッションのコンテキストで指定されたデータベースを使用します。 これは、`use <database>` コマンドで使用したデータベースです。 現時点では、このコマンドを使用してデータベース名を変更することはできません。

### <a name="output"></a>出力

コマンドが成功した場合は、次の応答が返されます。

```javascript
{ "ok" : 1 }
```

出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>データベースに関連付けられているプロビジョニング済みスループットを更新する

`"test"` という名前のデータべースのプロビジョニング済みスループットを 1200 RU に更新するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>データベースに関連付けられている自動スケーリング スループットを更新する

`"test"` という名前のデータベースのプロビジョニング済みスループットを 20,000 RU に更新したり、これを[自動スケーリング スループット レベル](../provision-throughput-autoscale.md)に変換したりするには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a>データベースの取得

データベースの取得拡張コマンドは、データベース オブジェクトを返します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。

```javascript
{
  customAction: "GetDatabase"
}
```

次の表では、コマンド内のパラメーターについて説明します。


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   カスタム コマンドの名前。 "GetDatabase" にする必要があります|
        
### <a name="output"></a>出力

コマンドが成功すると、応答には次のフィールドを持つドキュメントが含まれます。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `database`    |    `string`        |   データベースの名前です。      |
|   `provisionedThroughput`  |    `int`      |    データベースで[手動のデータベースレベルのスループット](../set-throughput.md#set-throughput-on-a-database)が使用されている場合に、データベースに対して設定するプロビジョニング済みスループット     |
| `autoScaleSettings` | `Object` | このオブジェクトには、[自動スケーリング モード](../provision-throughput-autoscale.md)を使用している場合に、データベースに関連付けられている容量パラメーターが格納されます。 `maxThroughput` の値は、データベースがその量まで動的に増加する、要求ユニットの最大量が示されます。 |

コマンドが失敗すると、既定のカスタム コマンド応答が返されます。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

#### <a name="get-the-database"></a>データベースを取得する

`"test"` という名前のデータベースのデータベース オブジェクトを取得するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

データベースにスループットが関連付けられていない場合、出力は次のようになります。

```javascript
{ "database" : "test", "ok" : 1 }
```

データベースに [データベースレベルの手動スループット](../set-throughput.md#set-throughput-on-a-database)が関連付けられている場合、出力には `provisionedThroughput` 値が表示されます。

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

データベースに[データベースレベルの自動スケーリングのスループット](../provision-throughput-autoscale.md)が関連付けられている場合、出力には、データベースの最小 RU/秒を説明する `provisionedThroughput` と、データベースの最大 RU/秒を示す `maxThroughput` を含む `autoScaleSettings` オブジェクトが表示されます。

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a>コレクションの作成

コレクションの作成拡張コマンドは、新しい MongoDB コレクションを作成します。 データベース名は、`use database` コマンドによって設定されたデータベース コンテキストから使用されます。 CreateCollection コマンドの形式は次のとおりです。

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" to use Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set.
  indexes: [{key: {_id: 1}, name: "_id_1"}, ... ] // Optional indexes (3.6+ accounts only).
}
```

次の表では、コマンド内のパラメーターについて説明します。

| **フィールド** | **Type** | **必須** | **説明** |
|---------|---------|---------|---------|
| `customAction` | `string` | 必須 | カスタム コマンドの名前。 必ず "CreateCollection" にします。|
| `collection` | `string` | 必須 | コレクションの名前。 特殊文字およびスペースは使用できません。|
| `offerThroughput` | `int` | 省略可能 | データベースに設定するプロビジョニング済みスループット。 このパラメーターが指定されていない場合、既定値は 400 RU/秒に設定されます。 * 10,000 RU/秒を超えるスループットを指定するには、`shardKey` パラメーターが必要です。|
| `shardKey` | `string` | スループットの高いコレクションの場合は必須です | シャード コレクション用シャード キーへのパス。 `offerThroughput` で 10,000 RU/秒を超える値を設定する場合、このパラメーターは必須です。  この値が指定されている場合は、挿入されたすべてのドキュメントでこのキーと値が必要になります。 |
| `autoScaleSettings` | `Object` | [自動スケーリング モード](../provision-throughput-autoscale.md)の場合は必須です | このオブジェクトには、自動スケーリング容量モードに関連付けられている設定が含まれています。 `maxThroughput` の値を設定できます。これにより、コレクションがその量まで動的に増加する、要求ユニットの最大量が示されます。 |
| `indexes` | `Array` | 必要に応じてインデックスを構成します。 このパラメーターは、3.6 以降のアカウントでのみサポートされています。 | 存在する場合は、_id にインデックスが必要です。 配列内の各エントリには、1 つ以上のフィールドのキーと名前を含める必要があります。また、インデックス オプションを含めることもできます。 たとえば、フィールドに複合一意インデックスを作成し、`a`と`b` というエントリを使用します `{key: {a: 1, b: 1}, name:"a_1_b_1", unique: true}`。

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

#### <a name="create-a-collection-with-the-minimum-configuration"></a>最小構成でコレクションを作成する

名前 `"testCollection"` と既定値を持つ新しいコレクションを作成するには、次のコマンドを使用します。 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

これにより、400RU/秒のシャード化されていない新しい固定のコレクションが作成され、`_id` フィールドにインデックスが自動的に作成されます。 この種類の構成は、`insert()` 関数を使用して新しいコレクションを作成するときにも適用されます。 次に例を示します。 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>シャード化されていないコレクションを作成する

`"testCollection"` という名前で、プロビジョニング済みスループットが 1000 RU のシャード化されていないコレクションを作成するには、次のコマンドを使用します。 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

シャード キーを指定しなくても、最大で 10,000 RU/秒のコレクションを `offerThroughput` として作成できます。 スループットの高いコレクションについては、次のセクションをご覧ください。

#### <a name="create-a-sharded-collection"></a>シャード コレクションを作成する

`"testCollection"` という名前で、プロビジョニング済みスループットが 11,000 RU で、`shardkey` プロパティ "a.b" を持つシャード コレクションを作成するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

このコマンドでは、`offerThroughput` に 10,000 RU/秒以上が指定されたため、`shardKey` パラメーターが必要になります。

#### <a name="create-an-unsharded-autoscale-collection"></a>シャード化されていない自動スケーリング コレクションを作成する

`'testCollection'` という名前で、4,000 RU/秒に設定された[自動スケーリング スループット容量](../provision-throughput-autoscale.md)を使用する のシャード化されていないコレクションを作成するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

`autoScaleSettings.maxThroughput` 値には、シャード キーを使用せずに 4,000 RU/秒から 10,000 RU/秒の範囲を指定できます。 自動スケーリング スループットがさらに高い場合、`shardKey` パラメーターを指定する必要があります。

#### <a name="create-a-sharded-autoscale-collection"></a>シャード化された自動スケーリング コレクションを作成する

`'testCollection'` という名前で、`'a.b'` と呼ばれるシャード キーを持ち、20,000 RU/秒に設定された[自動スケーリング スループット容量](../provision-throughput-autoscale.md)を使用するシャード コレクションを作成するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a>コレクションの更新

コレクションの更新拡張コマンドは、指定したコレクションに関連付けられているプロパティを更新します。 プロビジョニングされたスループットから自動スケーリングへのコレクションの変更やその逆の変更は、Azure Portal でのみサポートされています。

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" if using Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting. Changing between Autoscale and Provisioned throughput is only supported in the Azure Portal.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set.
  indexes: [{key: {_id: 1}, name: "_id_1"}, ... ] // Optional indexes (3.6+ accounts only).
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   カスタム コマンドの名前。 "UpdateCollection" にする必要があります。      |
|  `collection`   |   `string`      |   コレクションの名前。       |
| `offerThroughput` | `int` |   コレクションに設定するプロビジョニング済みスループット。|
| `autoScaleSettings` | `Object` | [自動スケーリング モード](../provision-throughput-autoscale.md)の場合は必須です。 このオブジェクトには、自動スケーリング容量モードに関連付けられている設定が含まれています。 `maxThroughput` の値は、コレクションがその量まで動的に増加する、要求ユニットの最大量が示されます。 |
| `indexes` | `Array` | 必要に応じてインデックスを構成します。 このパラメーターは、3.6 以降のアカウントでのみサポートされています。 存在する場合、コレクションの既存のインデックスは、指定されたインデックスのセットに置き換えられます (インデックスの削除を含む)。 _id にインデックスが必要です。 配列内の各エントリには、1 つ以上のフィールドのキーと名前を含める必要があります。また、インデックス オプションを含めることもできます。 たとえば、フィールド a と b に複合一意インデックスを作成するには、`{key: {a: 1, b: 1}, name: "a_1_b_1", unique: true}` というエントリを使用します。

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>コレクションに関連付けられているプロビジョニング済みスループットを更新する

`"testCollection"` という名前のコレクションのプロビジョニング済みスループットを 1200 RU に更新するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>コレクションの取得

コレクションの取得カスタム コマンドは、コレクション オブジェクトを返します。

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

次の表では、コマンド内のパラメーターについて説明します。


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| `customAction`    |   `string`      |   カスタム コマンドの名前。 "GetCollection" にする必要があります。      |
| `collection`    |    `string`     |    コレクションの名前。     |

### <a name="output"></a>出力

コマンドが成功すると、応答には次のフィールドを持つドキュメントが含まれます


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `database`    |    `string`     |   データベースの名前です。      |
| `collection`    |    `string`     |    コレクションの名前。     |
|  `shardKeyDefinition`   |   `document`      |  シャード キーとして使用されるインデックス仕様ドキュメント。 これは省略可能な応答パラメーターです。       |
|  `provisionedThroughput`   |   `int`      |    コレクションに設定するプロビジョニング済みスループット。 これは省略可能な応答パラメーターです。     |
| `autoScaleSettings` | `Object` | このオブジェクトには、[自動スケーリング モード](../provision-throughput-autoscale.md)を使用している場合に、データベースに関連付けられている容量パラメーターが格納されます。 `maxThroughput` の値は、コレクションがその量まで動的に増加する、要求ユニットの最大量が示されます。 |

コマンドが失敗すると、既定のカスタム コマンド応答が返されます。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

#### <a name="get-the-collection"></a>コレクションを取得する

`"testCollection"` という名前のコレクションのコレクション オブジェクトを取得するには、次のコマンドを使用します。

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

コレクションにスループット容量が関連付けられている場合、これに `provisionedThroughput` 値が含まれ、出力は次のようになります。

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

コレクションに自動スケーリング スループットが関連付けられている場合、これには `maxThroughput` パラメーターを持つ `autoScaleSettings` オブジェクトが含まれ、このパラメーターは、コレクションがその量まで動的に増加する最大スループットを定義します。 また、これには `provisionedThroughput` 値も含まれます。この値は、コレクションに要求がない場合に、このコレクションがその量まで減少する最小スループットを定義します。 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

コレクションが自動スケーリング モードまたは手動のいずれかで、[データベースレベルのスループット](../set-throughput.md#set-throughput-on-a-database)を共有している場合、出力は次のようになります。

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="parallelizing-change-streams"></a><a id="parallel-change-stream"></a> 変更ストリームの並列化 
[変更ストリーム](change-streams.md)を大規模に使用する場合は、負荷を一様に分散させるのが最適です。 次のコマンドは、1 つまたは複数の変更ストリーム再開トークンを返します。それぞれ 1 つの物理シャードまたはパーティションのデータに対応します (複数の論理シャードまたはパーティションを 1 つの物理パーティションに存在させることができます)。 各再開トークンは、watch() によって、その物理シャードまたはパーティションからのデータのみが返されます。

各再開トークン (トークンごとに 1 つのスレッド) で db.collection.watch() を呼び出すと、変更ストリームが効率的にスケーリングされます。

```javascript
{
        customAction: "GetChangeStreamTokens", 
        collection: "<Name of the collection>", 
        startAtOperationTime: "<BSON Timestamp>" // Optional. Defaults to the time the command is run.
} 
```

### <a name="example"></a>例
カスタム コマンドを実行して、各物理シャードまたはパーティションの再開トークンを取得します。

```javascript
use test
db.runCommand({customAction: "GetChangeStreamTokens", collection: "<Name of the collection>"})
```

GetChangeStreamTokens カスタム コマンドから返された再開トークンごとに watch () スレッドまたはプロセスを実行します。 次に、1 つのスレッドの例を示します。

```javascript
db.test_coll.watch([{ $match: { "operationType": { $in: ["insert", "update", "replace"] } } }, { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }], 
{fullDocument: "updateLookup", 
resumeAfter: { "_data" : BinData(0,"eyJWIjoyLCJSaWQiOiJQeFVhQUxuMFNLRT0iLCJDb250aW51YXRpb24iOlt7IkZlZWRSYW5nZSI6eyJ0eXBlIjoiRWZmZWN0aXZlIFBhcnRpdGlvbiBLZXkgUmFuZ2UiLCJ2YWx1ZSI6eyJtaW4iOiIiLCJtYXgiOiJGRiJ9fSwiU3RhdGUiOnsidHlwZSI6ImNvbnRpbndkFLbiIsInZhbHVlIjoiXCIxODQ0XCIifX1dfQ=="), "_kind" : NumberInt(1)}})
```

resumeAfter フィールドのドキュメント (値) は、再開トークンを表します。 watch () は、GetChangeStreamTokens カスタム コマンドが実行された後にその物理パーティションから挿入、更新、または置換されたすべてのドキュメントに対して、カーソルを返します。 返されるデータのサンプルは次のとおりです。

```javascript
{ "_id" : { "_data" : BinData(0,"eyJWIjoyLCJSaWQiOiJQeFVhQUxuMFNLRT0iLCJDfdsfdsfdsft7IkZlZWRSYW5nZSI6eyJ0eXBlIjoiRWZmZWN0aXZlIFBhcnRpdGlvbiBLZXkgUmFuZ2UiLCJ2YWx1ZSI6eyJtaW4iOiIiLCJtYXgiOiJGRiJ9fSwiU3RhdGUiOnsidHlwZSI6ImNvbnRpbnVhdGlvbiIsInZhbHVlIjoiXCIxOTgwXCIifX1dfQ=="), "_kind" : 1 },
 "fullDocument" : { "_id" : ObjectId("60da41ec9d1065b9f3b238fc"), "name" : John, "age" : 6 }, "ns" : { "db" : "test-db", "coll" : "test_coll" }, "documentKey" : { "_id" : ObjectId("60da41ec9d1065b9f3b238fc") }}
```

返される各ドキュメントには再開トークンが含まれていることに注意してください (すべてのページで同じです)。 この再開トークンは、スレッドまたはプロセスが停止した場合に保存して再利用する必要があります。 この再開トークンは、中断した場所からデータを取得し、その物理パーティションからのみデータを受信します。


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>カスタム コマンドの既定の出力

指定しない場合、カスタムの応答には次のフィールドを持つドキュメントが含まれます。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `code`    |   `int`      |   コマンドが失敗した場合 (ok == 0) のみ返されます。 MongoDB のエラー コードが含まれます。 これは省略可能な応答パラメーターです。      |
|  `errMsg`   |  `string`      |    コマンドが失敗した場合 (ok == 0) のみ返されます。 わかりやすいエラー メッセージが含まれます。 これは省略可能な応答パラメーターです。      |

次に例を示します。

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>次のステップ

次は、以下の Azure Cosmos DB の概念について学習することができます。 

* [Azure Cosmos DB のインデックス作成](../index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../time-to-live.md)
