---
title: Netezza からデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインで Copy アクティビティを使用して、Netezza からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 588700126d87361e4530c073cdcaf1a5a9339511
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828171"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Netezza からデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、Netezza からデータをコピーする方法について説明します。 この記事は、Copy アクティビティの概要を説明する [Copy アクティビティ](copy-activity-overview.md)に関する記事に基づいています。

>[!TIP]
>Netezza から Azure へのデータ移行のシナリオの詳細については、「[オンプレミスの Netezza サーバーから Azure へのデータの移行](data-migration-guidance-netezza-azure-sqldw.md)」をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Netezza コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)


Netezza から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

Netezza コネクタでは、ソースからの並列コピーがサポートされています。 詳細については、「[Netezza からの並列コピー](#parallel-copy-from-netezza)」セクションを参照してください。

このサービスでは、接続を行うための組み込みのドライバーが提供されます。 このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

コピー アクティビティを使用するパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 Copy アクティビティを使用したパイプライン作成の詳細な手順については、[Copy アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)を参照してください。

## <a name="create-a-linked-service-to-netezza-using-ui"></a>UI を使用して Netezza のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Netezza のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Netezza を検索し、Netezza コネクタを選択します。

   :::image type="content" source="media/connector-netezza/netezza-connector.png" alt-text="Netezza コネクタのスクリーンショット。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

   :::image type="content" source="media/connector-netezza/configure-netezza-linked-service.png" alt-text="Netezza のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Netezza コネクタに固有のエンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Netezza のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **Netezza** に設定する必要があります。 | はい |
| connectionString | Netezza に接続するための ODBC 接続文字列。 <br/>パスワードを Azure Key Vault に格納して、接続文字列から `pwd` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

一般的な接続文字列は `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` です。 次の表では、設定できる他のプロパティについて説明します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| SecurityLevel | ドライバーがデータ ストアへの接続に使用するセキュリティのレベル。 ドライバーは、SSL バージョン 3 を使用する一方向認証での SSL 接続をサポートしています。 <br>例: `SecurityLevel=preferredSecured`. サポートされる値は次のとおりです。<br/>- **セキュリティで保護されていない接続のみ** (**onlyUnSecured**):ドライバーで SSL を使用しません。<br/>- **セキュリティで保護されていない接続を優先 (preferredUnSecured) (既定値)** :サーバーによって選択肢が提供される場合、ドライバーでは SSL を使用しません。 <br/>- **セキュリティで保護されている接続を優先 (preferredSecured)** :サーバーによって選択肢が提供される場合、ドライバーで SSL を使用します。 <br/>- **セキュリティで保護されている接続のみ (onlySecured)** :SSL 接続を利用できない場合、ドライバーは接続しません。 | いいえ |
| CaCertFile | サーバーによって使用される SSL 証明書の完全パス。 例: `CaCertFile=<cert path>;`| はい (SSL が有効になっている場合) |

**例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: パスワードを Azure Key Vault に格納する**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

このセクションでは、Netezza データセットでサポートされているプロパティの一覧を示します。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関するページをご覧ください。

Netezza からデータをコピーするには、データセットの **type** プロパティを **NetezzaTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**NetezzaTable** | はい |
| schema | スキーマの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| table | テーブルの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| tableName | スキーマがあるテーブルの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Netezza ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="netezza-as-source"></a>ソースとしての Netezza

>[!TIP]
>データのパーティション分割を使用して Netezza からデータを効率的に読み込む方法の詳細については、「[Netezza からの並列コピー](#parallel-copy-from-netezza)」セクションを参照してください。

Netezza からデータをコピーするには、コピー アクティビティの **source** のタイプを **NetezzaSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **NetezzaSource** に設定する必要があります。 | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"` | いいえ (データセットの "tableName" が指定されている場合) |
| partitionOptions | Netezza からのデータの読み込みに使用されるデータ パーティション分割オプションを指定します。 <br>指定できる値は、**None** (既定値)、**DataSlice**、**DynamicRange** です。<br>パーティション オプションが有効になっている場合 (つまり、`None` ではない場合)、Netezza データベースから同時にデータを読み込む並列処理の次数は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用される **整数型** のソース列の名前を指定します。 指定されていない場合は、テーブルの主キーが自動検出され、パーティション列として使用されます。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionColumnName` をフックします。 例については、「[Netezza からの並列コピー](#parallel-copy-from-netezza)」セクションを参照してください。 | いいえ |
| partitionUpperBound | データをコピーするパーティション列の最大値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionUpbound` をフックします。 例については、「[Netezza からの並列コピー](#parallel-copy-from-netezza)」セクションを参照してください。 | いいえ |
| partitionLowerBound | データをコピーするパーティション列の最小値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionLowbound` をフックします。 例については、「[Netezza からの並列コピー](#parallel-copy-from-netezza)」セクションを参照してください。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Netezza からの並列コピー

Data Factory の Netezza コネクタは、Netezza からデータを並列でコピーするために、組み込みのデータ パーティション分割を提供します。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** テーブルにあります。

:::image type="content" source="./media/connector-netezza/connector-netezza-partition-options.png" alt-text="パーティションのオプションのスクリーンショット":::

パーティション分割されたコピーを有効にすると、サービスによって Netezza ソースに対する並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリでは、Netezza データベースからデータの一部を取得します。

特に、Netezza データベースから大量のデータを読み込む場合は、データ パーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大きなテーブル全体を読み込む。                                   | **パーティション オプション**: データ スライス。 <br><br/>実行中に、サービスによって [Netezza の組み込みデータ スライス](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)に基づいてデータが自動的にパーティション分割され、パーティションごとにデータがコピーされます。 |
| カスタム クエリを使用して大量のデータを読み込む。                 | **パーティション オプション**: データ スライス。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`<br>実行中にサービスによって `?AdfPartitionCount` (コピー アクティビティで並列コピー番号が設定されています) と `?AdfDataSliceCondition` がデータ スライス パーティション ロジックに置き換えられ、Netezza に送信されます。 |
| カスタム クエリを使用して大量のデータを読み込む (範囲パーティション分割のために値が均等に分散されている整数列がある場合)。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。 整数データ型の列に対してパーティション分割を実行できます。<br>**パーティションの上限** と **パーティションの下限**: パーティション列に対してフィルター処理を実行して、下限から上限までの範囲内のデータのみを取得する場合に指定します。<br><br>実行中に、サービスによって `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`、`?AdfRangePartitionLowbound` が各パーティションの実際の列名および値の範囲に置き換えられ、Netezza に送信されます。 <br>たとえば、パーティション列 "ID" で下限が 1、上限が 80 に設定され、並列コピーが 4 に設定されている場合、サービスは 4 つのパーティションでデータを取得します。 これらの ID の範囲はそれぞれ [1, 20]、[21, 40]、[41, 60]、[61, 80] です。 |

**例: データ スライス パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
