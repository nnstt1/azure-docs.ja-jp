---
title: ルックアップ アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics でルックアップ アクティビティを使用して、外部ソースから値を検索する方法について説明します。 この出力は、後続のアクティビティによってさらに参照できます。
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 5a605177c741f3da23c5cce2b3ecf3e757a8af32
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758087"
---
# <a name="lookup-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory および Azure Synapse Analytics でのルックアップ アクティビティ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ルックアップ アクティビティは、Azure Data Factory および Azure Synapse Analytics パイプラインでサポートされている任意のデータ ソースからデータセットを取得できます。 それを使用して、オブジェクト名をハード コーディングする代わりに、後続のアクティビティで操作するオブジェクトを動的に決定できます。 オブジェクトの例として、ファイルとテーブルがあります。

ルックアップ アクティビティは、構成ファイルやテーブルの内容を読み取って返します。 また、クエリまたはストアド プロシージャの実行結果を返します。 出力には、シングルトン値または属性の配列を指定できます。これは、ForEach アクティビティなどの後続のコピー、変換、または制御フロー アクティビティで使用できます。

## <a name="supported-capabilities"></a>サポートされる機能

次のことを考慮してください。

- ルックアップ アクティビティでは、最大 **5,000 行** を返すことができます。結果セットにそれを超えるレコードが含まれている場合は、最初の 5,000 行が返されます。
- ルックアップ アクティビティの出力でサポートされる最大サイズは **4 MB** で、サイズがこの制限を超えるとアクティビティは失敗します。 
- タイムアウト前のルックアップ アクティビティの最長期間は **24 時間** です。
- クエリまたはストアド プロシージャを使用してデータを検索する場合は、1 つの結果セットのみが返されるようにしてください。 そうしないと、ルックアップ アクティビティは失敗します。

次のデータ ソースがルックアップ アクティビティでサポートされています。 

[!INCLUDE [data-factory-v2-supported-data-stores](includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>構文

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>型のプロパティ

名前 | 説明 | Type | 必須
---- | ----------- | ---- | --------
dataset | ルックアップ用のデータセット参照を提供します。 対応する各コネクタの記事の 「**データセットのプロパティ**」セクションから詳細を取得します。 | キーと値のペア | はい
source | データセット固有のソース プロパティを含みます (コピー アクティビティ ソースと同じ)。 対応する各コネクタの記事の「**コピー アクティビティのプロパティ**」セクションから詳細を取得します。 | キーと値のペア | はい
firstRowOnly | 最初の行のみまたはすべての行のどちらを返すかを示します。 | Boolean | いいえ。 既定では、 `true`です。

> [!NOTE]
> 
> * **ByteArray** 型のソース列はサポートされていません。
> * データセット定義内の **構造体** はサポートされていません。 テキスト形式のファイルの場合は、ヘッダー行を使用して列名を指定できます。
> * ルックアップ ソースが JSON ファイルの場合、JSON オブジェクトを整形するための `jsonPathDefinition` 設定はサポートされていません。 オブジェクト全体が取得されます。

## <a name="use-the-lookup-activity-result"></a>ルックアップ アクティビティの結果の使用

ルックアップ結果は、アクティビティ実行結果の `output` セクションに返されます。

* **`firstRowOnly` が `true` (既定値) に設定されているときは**、出力形式は次のコードに示すとおりです。 ルックアップ結果は固定の `firstRow` キーの下にあります。 後続のアクティビティで結果を使用するには、パターン `@{activity('LookupActivity').output.firstRow.table}` を使用します。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **`firstRowOnly` が `false` に設定されているときは**、出力形式は次のコードに示すとおりです。 `count` フィールドは、返されたレコードの数を示します。 固定された `value` 配列の下に詳細な値が表示されます。 このような場合は、ルックアップ アクティビティの後ろに [Foreach アクティビティ](control-flow-for-each-activity.md)が続きます。 `value` 配列は、パターン `@activity('MyLookupActivity').output.value` を使用して ForEach アクティビティの `items` フィールドに渡します。 `value` 配列の要素にアクセスするには、構文 `@{activity('lookupActivity').output.value[zero based index].propertyname}` を使用します。 たとえば `@{activity('lookupActivity').output.value[0].schema}` です。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>例

この例では、パイプラインに 2 つのアクティビティ、**ルックアップ** と **コピー** が含まれています。 コピー アクティビティは、お使いの Azure SQL Database インスタンスの SQL テーブルから Azure Blob Storage にデータをコピーします。 SQL テーブルの名前は、Blob Storage 内の JSON ファイルに格納されます。 ルックアップ アクティビティは、実行時にテーブル名を検索します。 JSON は、この方法を使用して動的に変更されます。 パイプラインやデータセットを再デプロイする必要はありません。 

この例では、最初の行のみのルックアップを示します。 すべての行のルックアップについて、および ForEach アクティビティで結果をチェーンするには、[複数のテーブルの一括コピー](tutorial-bulk-copy.md)に関する記事のサンプルを参照してください。


### <a name="pipeline"></a>パイプライン

- ルックアップ アクティビティは、Azure Blob Storage 内の場所を表す **LookupDataset** を使用するように設定されています。 ルックアップ アクティビティは、この場所にある JSON ファイルから SQL テーブルの名前を読み取ります。 
- コピー アクティビティは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 **SourceDataset** 内の **tableName** プロパティは、ルックアップ アクティビティからの出力を使用するように設定されています。 コピー アクティビティは、SQL テーブルから Azure Blob Storage 内の場所にデータをコピーします。 場所は **SinkDataset** プロパティによって指定されます。 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>ルックアップ データセット

**ルックアップ** データセットは、**AzureBlobStorageLinkedService** 型で指定された Azure Storage ルックアップ フォルダー内の **sourcetable.json** ファイルです。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>コピー アクティビティの **ソース** データセット

**ソース** データセットは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 コピー アクティビティは、SQL テーブルから Azure Blob Storage 内の場所にデータをコピーします。 場所は **sink** データセットによって指定されます。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>コピー アクティビティの **シンク** データセット

コピー アクティビティは、SQL テーブルから、Azure Storage の **csv** フォルダー内の **filebylookup.csv** ファイルにデータをコピーします。 このファイルは、**AzureBlobStorageLinkedService** プロパティで指定されています。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

**sourcetable.json** ファイルには、次の 2 種類の形式を使用できます。

#### <a name="set-of-objects"></a>オブジェクトのセット

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>オブジェクトの配列

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>制限事項と回避策

Lookup アクティビティと提案される回避策のいくつかの制限を次に示します。

| 制限事項 | 回避策 |
|---|---|
| ルックアップ アクティビティの最大行数は 5,000 行で、最大サイズは 4 MB です。 | 最大行数またはサイズを超えないデータを取得する、内側パイプライン上で外側パイプラインが反復される 2 段のパイプラインを設計します。 |
| | |

## <a name="next-steps"></a>次のステップ
Azure Data Factory パイプラインおよび Azure Synapse Analytics パイプラインでサポートされているその他の制御フロー アクティビティを参照してください。 

- [パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
