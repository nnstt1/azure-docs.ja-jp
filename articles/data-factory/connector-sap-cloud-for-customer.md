---
title: SAP Cloud for Customer との間でデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインを使用して、SAP Cloud for Customer からサポートされるシンク データ ストアにデータをコピーしたり、サポートされるソース データ ストアから SAP Cloud for Customer にデータをコピーしたりする方法を説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e3a09400b3ebf8fb0f49a46677f7d0dc815d4937
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836032"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して SAP Cloud for Customer (C4C) からデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、SAP Cloud for Customer (C4C) との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!TIP]
>SAP データ統合シナリオでのサービスの全体的なサポートについては、各 SAP コネクタの詳細な説明、比較、ガイダンスが含まれた、[Azure Data Factory を使用した SAP データ統合に関するホワイトペーパー](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この SAP Cloud for Customer コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

SAP Cloud for Customer のデータを、サポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを SAP Cloud for Customer にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、サービスはこのコネクタを使用して、SAP Cloud for Sales、SAP Cloud for Service、SAP Cloud for Social Engagement ソリューションを含む SAP Cloud for Customer との間でデータをコピーできます。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-cloud-for-customer-using-ui"></a>UI を使用して SAP Cloud for Customer にリンクされたサービスを作成する

次の手順を使用して、Azure portal UI で SAP Cloud for Customer にリンクされたサービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンクされたサービスを作成します。":::

2. SAP を検索して、SAP Cloud for Customer コネクタを選択します。

   :::image type="content" source="media/connector-sap-cloud-for-customer/sap-cloud-for-customer-connector.png" alt-text="SAP Cloud for Customer コネクタを選択します。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンクされたサービスを作成します。

   :::image type="content" source="media/connector-sap-cloud-for-customer/configure-sap-cloud-for-customer-linked-service.png" alt-text="SAP Cloud for Customer にリンクされたサービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、SAP Cloud for Customer コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Cloud for Customer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapCloudForCustomer**。 | はい |
| url | SAP C4C OData サービスの URL。 | はい |
| username | SAP C4C に接続するためのユーザー名を指定します。 | はい |
| password | username に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ |

**例:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SAP Cloud for Customer データセットでサポートされるプロパティの一覧を示します。

SAP Cloud for Customer からデータをコピーするには、データセットの type プロパティを **SapCloudForCustomerResource** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**SapCloudForCustomerResource** |はい |
| path | SAP C4C OData エンティティのパスを指定します。 |はい |

**例:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP Cloud for Customer ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-c4c-as-source"></a>ソースとしての SAP C4C

SAP Cloud for Customer からデータをコピーするには、コピー アクティビティのソースの種類を **SapCloudForCustomerSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapCloudForCustomerSource**  | はい |
| query | データを読み取るカスタム OData クエリを指定します。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 指定しない場合は、既定値の **00:30:00** (30 分) が使用されます。 | いいえ |

特定の日のデータを取得するサンプル クエリ: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>シンクとしての SAP C4C

SAP Cloud for Customer にデータをコピーするには、コピー アクティビティのシンクの種類を **SapCloudForCustomerSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapCloudForCustomerSink**  | はい |
| writeBehavior | 操作の書き込み動作。 “Insert” または “Update” を指定できます。 | いいえ。 既定値: "Insert"。 |
| writeBatchSize | 書き込み操作のバッチ サイズ。 最適なパフォーマンスを得るバッチ サイズは、テーブルまたはサーバーによって異なることがあります。 | いいえ。 既定値: 10。 |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>SAP Cloud for Customer のデータ型マッピング

SAP Cloud for Customer からデータをコピーするとき、SAP Cloud for Customer のデータ型からサービス内で内部的に使用される中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP C4C OData のデータ型 | 中間サービス データ型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
