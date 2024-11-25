---
title: Amazon Redshift からデータをコピーする
description: Azure Data Factory または Azure Synapse Analytics パイプラインを使用して、Amazon Redshift からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ff1aa2fdb5f5adca47130cd3ff00d09e5f2f0649
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828266"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Amazon Redshift からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-amazon-redshift-connector.md)
> * [現在のバージョン](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Synapse Analytics パイプラインで Copy アクティビティを使用して、Amazon Redshift からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Amazon Redshift コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Amazon Redshift から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Amazon Redshift コネクタは、クエリまたは組み込みの Redshift UNLOAD サポートを使用して Redshift からデータの取得をサポートします。

> [!TIP]
> Redshift から大量のデータをコピーするときに最適なパフォーマンスを得るには、Amazon S3 経由で組み込みの Redshift UNLOAD を使用することを検討します。 詳細については、「[Amazon Redshift からのデータ コピーでの UNLOAD の使用](#use-unload-to-copy-data-from-amazon-redshift)」セクションをご覧ください。

## <a name="prerequisites"></a>前提条件

* オンプレミスのデータ ストアに[自己ホスト型統合ランタイム](create-self-hosted-integration-runtime.md)を使用してデータをコピーする場合は、統合ランタイム (コンピューターの IP アドレスを使用) に Amazon Redshift クラスターへのアクセスを許可します。 手順については、「 [クラスターへのアクセスを承認する](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 」を参照してください。
* Azure データ ストアにデータをコピーする場合、Azure データ センターで使用されるコンピューティング IP アドレスと SQL 範囲については、「[Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)」をご覧ください。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-redshift-using-ui"></a>UI を使用して Amazon Redshift にリンク サービスを作成する

次の手順を使用して、Azure portal の UI で Amazon Redshift にリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. Amazon を検索し、Amazon Redshift コネクタを選択します。

    :::image type="content" source="media/connector-amazon-redshift/amazon-redshift-connector.png" alt-text="Amazon Redshift コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-amazon-redshift/configure-amazon-redshift-linked-service.png" alt-text="Amazon Redshift へのリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、Amazon Redshift コネクターに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Amazon Redshift のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AmazonRedshift** | はい |
| server |Amazon Redshift サーバーの IP アドレスまたはホスト名。 |はい |
| port |Amazon Redshift サーバーがクライアント接続のリッスンに使用する TCP ポートの数。 |いいえ (既定値は 5439 です) |
| database |Amazon Redshift データベースの名前。 |はい |
| username |データベースへのアクセスを持つユーザーの名前。 |はい |
| password |ユーザー アカウントのパスワード。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Amazon Redshift データセットでサポートされるプロパティの一覧を示します。

Amazon Redshift からのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**AmazonRedshiftTable** | はい |
| schema | スキーマの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| table | テーブルの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| tableName | スキーマがあるテーブルの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 型のデータセットを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Amazon Redshift ソースでサポートされるプロパティの一覧を示します。

### <a name="amazon-redshift-as-source"></a>ソースとしての Amazon Redshift

Amazon Redshift からデータをコピーするには、コピー アクティビティでソースの型を **AmazonRedshiftSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**AmazonRedshiftSource** | はい |
| query |カスタム クエリを使用してデータを読み取ります。 例: Select * from MyTable。 |いいえ (データセットの "tableName" が指定されている場合) |
| redshiftUnloadSettings | Amazon Redshift の UNLOAD を使用する場合のプロパティ グループ。 | いいえ |
| s3LinkedServiceName | リンクされた AmazonS3 型のサービス名を指定することで、中間ストアとして使用される Amazon S3 を参照します。 | アンロードを使用する場合は はい |
| bucketName | 中間データを格納する S3 バケットを指定します。 指定しない場合、サービスによって自動的に生成されます。  | アンロードを使用する場合は はい |

**例:UNLOAD を使用したコピー アクティビティでの Amazon Redshift ソース**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

次のセクションから、UNLOAD を使用して、効率的に Amazon Redshift からデータをコピーする方法の詳細について説明します。

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Amazon Redshift からのデータ コピーで UNLOAD を使用する

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) は、Amazon Redshift が提供するメカニズムであり、Amazon Simple Storage Service (Amazon S3) 上に 1 つまたは複数のファイルへのクエリの結果をアンロードできます。 これは、Redshift から大きなデータ セットをコピーするために、Amazon から推奨されている方法です。

**例: UNLOAD、段階的コピーおよび PolyBase を使用して、Amazon Redshift から Azure Synapse Analytics にデータをコピーする**

このサンプルのユース ケースでは、コピー アクティビティは "redshiftUnloadSettings" での構成に従って Amazon Redshift から Amazon S3 へデータをアンロードした後、Amazon S3 から "stagingSettings" に指定された Azure Blob へデータをコピーし、最後に、PolyBase を使用して Azure Synapse Analytics にデータを読み込みます。 すべての中間形式が、コピー アクティビティによって正しく処理されます。

:::image type="content" source="media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png" alt-text="Redshift から Azure Synapse Analytics へのコピー ワークフロー":::

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Amazon Redshift のデータ型マッピング

Amazon Redshift からデータをコピーするとき、Amazon Redshift のデータ型からサービス内部で使用される中間データ型へのマッピングは、以下を使用して行われます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Amazon Redshift のデータ型 | 中間サービス データ型 |
|:--- |:--- |
| bigint |Int64 |
| BOOLEAN |String |
| CHAR |String |
| DATE |DateTime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| real |Single |
| SMALLINT |Int16 |
| [TEXT] |String |
| timestamp |DateTime |
| VARCHAR |String |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
