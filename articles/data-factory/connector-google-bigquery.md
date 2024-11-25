---
title: Google BigQuery からデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインで Copy アクティビティを使用して、Google BigQuery からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: aa59787675870483941f271778bbf5b907467668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815150"
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して Google BigQuery からデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、Google BigQuery からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Google BigQuery コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Google BigQuery から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

このサービスでは、接続を可能にする組み込みのドライバーが提供されます。 そのため、このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

>[!NOTE]
>この Google BigQuery コネクタは、BigQuery API 上に構築されます。 BigQuery では着信要求の最大数を制限し、プロジェクトごとに適切なクォータを強制することに注意してください。[割り当てと制限 - API リクエスト](https://cloud.google.com/bigquery/quotas#api_requests)を参照してください。 アカウントに対してあまり多くの同時要求をトリガーしないようにしてください。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-google-bigquery-using-ui"></a>UI を使用して Google BigQuery のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Google BigQuery のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Google を検索し、Google BigQuery コネクタを選択します。

    :::image type="content" source="media/connector-google-bigquery/google-bigquery-connector.png" alt-text="Google BigQuery コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-google-bigquery/configure-google-bigquery-linked-service.png" alt-text="Google BigQuery のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Google BigQuery コネクタに固有のエンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Google BigQuery のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **GoogleBigQuery** に設定する必要があります。 | はい |
| project | クエリ対象の既定の BigQuery プロジェクトのプロジェクト ID。  | はい |
| additionalProjects | アクセス対象のパブリック BigQuery プロジェクトのプロジェクト ID のコンマ区切りリスト。  | いいえ |
| requestGoogleDriveScope | Google Drive へのアクセスを要求するかどうか。 Google Drive のアクセスを許可すると、BigQuery データと Google Drive のデータを結合するフェデレーション テーブルのサポートが有効になります。 既定値は **false** です。  | いいえ |
| authenticationType | 認証に使用される OAuth 2.0 認証メカニズム。 ServiceAuthentication はセルフホステッド統合ランタイムのみで使用できます。 <br/>使用可能な値は、**UserAuthentication** および **ServiceAuthentication** です。 これらの認証の種類それぞれのプロパティと JSON の使用例については、この表の後のセクションを参照してください。 | はい |

### <a name="using-user-authentication"></a>ユーザー認証の使用

"authenticationType" プロパティを **UserAuthentication** に設定し、前のセクションに説明されている汎用プロパティと共に次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| clientId | 更新トークンの生成に使用されるアプリケーションの ID。 | いいえ |
| clientSecret | 更新トークンの生成に使用されるアプリケーションのシークレット。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| refreshToken | BigQuery へのアクセスを承認するために使用される、Google から取得した更新トークン。 取得方法については、「[Obtaining OAuth 2.0 access tokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens)」(OAuth 2.0 アクセス トークンの取得) および[こちらのコミュニティ ブログ](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59)をご覧ください。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |

**例:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>サービス認証の使用

"authenticationType" プロパティを **ServiceAuthentication** に設定し、前のセクションに説明されている汎用プロパティと共に次のプロパティを指定します。 この認証の種類は、セルフホステッド統合ランタイムのみで使用できます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| email | ServiceAuthentication で使用されるサービス アカウントの電子メール ID。 これはセルフホステッド統合ランタイムのみで使用できます。  | いいえ |
| keyFilePath | サービス アカウントの電子メール アドレスを認証するために使用される .p12 キー ファイルへの完全なパス。 | いいえ |
| trustedCertPath | TLS 経由で接続するときにサーバーを検証するために使用される信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド統合ランタイムで TLS を使用している場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ |
| useSystemTrustStore | システムの信頼ストアと指定した .pem ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は **false** です。  | いいえ |

**例:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Google BigQuery データセットでサポートされるプロパティの一覧を示します。

Google BigQuery からデータをコピーするには、データセットの type プロパティを **GoogleBigQueryObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**GoogleBigQueryObject** | はい |
| dataset | Google BigQuery データセットの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| table | テーブルの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| tableName | テーブルの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`dataset` と `table` を使用します。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Google BigQuery ソース タイプでサポートされるプロパティの一覧を示します。

### <a name="googlebigquerysource-as-a-source-type"></a>ソース タイプとしての GoogleBigQuerySource

Google BigQuery からデータをコピーするには、コピー アクティビティのソースの種類を **GoogleBigQuerySource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **GoogleBigQuerySource** に設定する必要があります。 | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 たとえば `"SELECT * FROM MyTable"` です。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
