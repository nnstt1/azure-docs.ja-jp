---
title: Impala からデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインで Copy アクティビティを使用して、Impala からサポートされているシンク データ ストアにデータをコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: f6d9c4cb468ce5d8e7bed173564d4d5a135f5bcb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767716"
---
# <a name="copy-data-from-impala-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Impala からデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、Impala からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Impala コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Impala から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

このサービスでは、接続を可能にする組み込みのドライバーが提供されます。 そのため、このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-impala-using-ui"></a>UI を使用して Impala のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Impala のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Impala を検索し、Impala コネクタを選択します。

   :::image type="content" source="media/connector-impala/impala-connector.png" alt-text="Impala コネクタのスクリーンショット。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

   :::image type="content" source="media/connector-impala/configure-impala-linked-service.png" alt-text="Impala のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、Impala コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Impala のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Impala** に設定する必要があります。 | はい |
| host | Impala サーバーの IP アドレスまたはホスト名 (192.168.222.160)。  | はい |
| port | Impala サーバーがクライアント接続のリッスンに使用する TCP ポート。 既定値は 21050 です。  | いいえ |
| authenticationType | 使用する認証の種類。 <br/>使用できる値は、**Anonymous**、**SASLUsername**、および **UsernameAndPassword** です。 | はい |
| username | Impala サーバーへのアクセスに使用するユーザー名。 既定値は anonymous です (SASLUsername を使用するとき)。  | いいえ |
| password | ユーザー名に対応するパスワード (UsernameAndPassword を使用するとき)。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| enableSsl | サーバーへの接続が TLS を使用して暗号化されるかどうかを指定します。 既定値は **false** です。  | いいえ |
| trustedCertPath | TLS 経由で接続するときにサーバーを検証するために使用される信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド統合ランタイムで TLS を使用している場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ |
| useSystemTrustStore | システムの信頼ストアと指定した PEM ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は **false** です。  | いいえ |
| allowHostNameCNMismatch | TLS 経由で接続するときに、CA が発行した TLS/SSL 証明書名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は **false** です。  | いいえ |
| allowSelfSignedServerCert | サーバーからの自己署名証明書を許可するかどうかを指定します。 既定値は **false** です。  | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Impala データセットでサポートされるプロパティの一覧を示します。

Impala からデータをコピーするには、データセットの type プロパティを **ImpalaObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**ImpalaObject** | はい |
| schema | スキーマの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| table | テーブルの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| tableName | スキーマがあるテーブルの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Impala ソース タイプでサポートされるプロパティの一覧を示します。

### <a name="impala-as-a-source-type"></a>ソース タイプとしての Impala

Impala からデータをコピーするには、コピー アクティビティのソース タイプを **ImpalaSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **ImpalaSource** に設定する必要があります。 | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 たとえば `"SELECT * FROM MyTable"` です。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
コピー アクティビティによってソース、シンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
