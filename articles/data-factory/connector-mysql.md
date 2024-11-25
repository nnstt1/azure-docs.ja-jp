---
title: MySQL からデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: MySQL データベースからシンクとしてサポートされているデータ ストアにデータをコピーできる Azure Data Factory および Synapse Analytics の MySQL コネクタについて説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 98211fd52546f0301552641fc57badddc92fcae3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831703"
---
# <a name="copy-data-from-mysql-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して MySQL からデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-onprem-mysql-connector.md)
> * [現在のバージョン](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Synapse Analytics パイプラインでコピー アクティビティを使用して、MySQL データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!NOTE]
>[Azure Database for MySQL](../mysql/overview.md) サービスとの間でデータをコピーするには、専用の [Azure Database for MySQL コネクタ](connector-azure-database-for-mysql.md)を使用します。

## <a name="supported-capabilities"></a>サポートされる機能

この MySQL コネクタは、以下のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

MySQL データベースのデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この MySQL コネクタは MySQL **バージョン 5.6、5.7、8.0** をサポートします。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime のバージョン 3.7 以降には MySQL ドライバーが組み込まれているため、ドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mysql-using-ui"></a>UI を使用して MySQL のリンク サービスを作成する

次の手順を使用して、Azure portal UI で MySQL へのリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI で新しいリンク サービスを作成する。":::

2. MySQL を検索し、MySQL コネクタを選択します。

    :::image type="content" source="media/connector-mysql/mysql-connector.png" alt-text="MySQL コネクタを選択します。":::    

1. サービスの詳細を設定し、接続をテストし、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-mysql/configure-mysql-linked-service.png" alt-text="MySQL のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、MySQL コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

MySQL のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**MySql** | はい |
| connectionString | Azure Database for MySQL インスタンスに接続するために必要な情報を指定します。<br/> パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

一般的な接続文字列は `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` です。 ケースごとにさらに多くのプロパティを設定できます。

| プロパティ | 説明 | Options | 必須 |
|:--- |:--- |:--- |:--- |
| SSLMode | このオプションは、MySQL に接続するときにドライバーで TLS 暗号化および検証を使用するかどうかを指定します。 例: `SSLMode=<0/1/2/3/4>`。| DISABLED (0) / PREFERRED (1) **(既定)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | いいえ |
| SSLCert | クライアントの ID を証明するために使用される SSL 証明書を含む、.pem ファイルの完全パスと名前。 <br/> この証明書をサーバーに送信する前に暗号化するための秘密キーを指定するには、`SSLKey` プロパティを使用します。| | はい、双方向 SSL 検証を使用する場合。 |
| SSLKey | 双方向 SSL 検証中にクライアント側の証明書を暗号化するために使用される秘密キーを含むファイルの完全パスと名前。|  | はい、双方向 SSL 検証を使用する場合。 |
| UseSystemTrustStore | このオプションは、システムの信頼ストアと指定した PEM ファイルのどちらの CA 証明書を使用するかを指定します。 例: `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(既定)** | いいえ |

**例:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
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

以下のペイロードで MySQL のリンクされたサービスを使用していた場合、まだそのままサポートされていますが、今後は新しいものを使用することをお勧めします。

**以前のペイロード:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、MySQL データセット でサポートされるプロパティの一覧を示します。

MySQL からデータをコピーする場合、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**MySqlTable** | はい |
| tableName | MySQL データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 型のデータセットを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、MySQL ソースでサポートされるプロパティの一覧を示します。

### <a name="mysql-as-source"></a>ソースとしての MySQL

MySQL からデータをコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**MySqlSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 型のソースを使用していた場合は現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="data-type-mapping-for-mysql"></a>MySQL のデータ型マッピング

MySQL からデータをコピーするとき、MySQL のデータ型から、サービスによって内部的に使用される中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| MySQL のデータ型 | 中間サービス データ型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
