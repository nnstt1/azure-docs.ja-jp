---
title: Azure Table Storage との間でデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインを使用して、サポートされるソース ストアから Azure Table Storage にデータをコピーしたり、Table Storage からサポートされるシンク ストアにコピーしたりする方法を説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: devx-track-azurepowershell, synapse
ms.date: 09/09/2021
ms.openlocfilehash: e88b4cfc2d49aa072021ed81dc4c1eb7105c6112
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811959"
---
# <a name="copy-data-to-and-from-azure-table-storage-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用した Azure Table Storage との間でのデータのコピー

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-table-connector.md)
> * [現在のバージョン](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、Azure Table Storage との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Table Storage コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Table Storage には、サポートされているソース データ ストアからデータをコピーすることができます。 サポートされているシンク データ ストアに対して、Table Storage からデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Azure Table コネクタは、アカウント キーとサービスの Shared Access Signature 認証を使用して、データのコピーをサポートします。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-table-storage-linked-service-using-ui"></a>UI を使用して Azure Table Storage のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Azure Table Storage のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Azure Table を検索し、Azure Table Storage コネクタを選択します。

    :::image type="content" source="media/connector-azure-table-storage/azure-table-storage-connector.png" alt-text="Azure Table Storage コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-azure-table-storage/configure-azure-table-storage-linked-service.png" alt-text="Azure Table Storage のリンク サービスの構成を示すスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Azure Table Storage に固有のエンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

### <a name="use-an-account-key"></a>アカウント キーの使用

Azure Storage のリンクされたサービスは、アカウント キーを使用して作成できます。 これにより、サービスは Storage にグローバルにアクセスすることができます。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureTableStorage** に設定する必要があります。 |はい |
| connectionString | connectionString プロパティのために Storage に接続するために必要な情報を指定します。 <br/>アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>"AzureStorage" タイプのリンクされたサービスを使用していた場合は、まだそのままサポートされていますが、今後は新しい "AzureTableStorage" のリンクされたサービス タイプを使用することをお勧めします。

**例:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
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

### <a name="use-shared-access-signature-authentication"></a>Shared Access Signature 認証を使用する

Shared Access Signature を使用して、Storage のリンクされたサービスを作成することもできます。 これによって、サービスは、ストレージ内のすべてまたは特定のリソースへのアクセスが制限付きまたは期限付きになります。

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。 アカウントのアクセス キーを共有する必要はありません。 Shared Access Signature とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、Shared Access Signature 内で適切なコンストラクターまたはメソッドに渡すだけで、Shared Access Signature でストレージ リソースにアクセスできます。 Shared Access Signature について詳しくは、[Shared Access Signature のモデルの概要](../storage/common/storage-sas-overview.md)に関するページをご覧ください。

> [!NOTE]
> **サービスの Shared Access Signature** と **アカウントの Shared Access Signature** の両方がサポートされるようになりました。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../storage/common/storage-sas-overview.md)」を参照してください。 

> [!TIP]
> ストレージ アカウントに使用するサービスの Shared Access Signature を生成するには、次の PowerShell コマンドを実行します。 プレースホルダーを置き換えたうえで、必要なアクセス許可を付与してください。
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Shared Access Signature 認証を使用するために、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureTableStorage** に設定する必要があります。 |はい |
| sasUri | テーブルへの共有アクセス署名 URI の SAS URI を指定します。 <br/>安全に格納するには、このフィールドを SecureString とマークします。 自動ローテーションを活用してトークン部分を削除するために、SAS トークンを Azure Key Vault に配置することもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>"AzureStorage" タイプのリンクされたサービスを使用していた場合は、まだそのままサポートされていますが、今後は新しい "AzureTableStorage" のリンクされたサービス タイプを使用することをお勧めします。

**例:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
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

Shared Access Signature の URI を作成する際は、次の点に注意してください。

- リンク サービス (読み取り、書き込み、読み取り/書き込み) がどのように使用されているかに応じて、オブジェクトに対する適切な読み取り/書き込みアクセス許可を設定します。
- **有効期限** を適切に設定します。 Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
- URI は、必要に応じて、適切なテーブル レベルで作成する必要があります。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Azure Table データセットでサポートされるプロパティの一覧を示します。

Azure Table をコピー先またはコピー元としてデータをコピーするには、データセットの type プロパティを **AzureTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureTable** に設定する必要があります。 |はい |
| tableName |リンクされたサービスが参照する Table Storage データベース インスタンスのテーブルの名前です。 |はい |

**例:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-inference-by-the-service"></a>サービスによるスキーマの推論

Azure Table などのスキーマのないデータ ストアの場合、サービスは次のいずれかの方法でスキーマの推論を行います。

* Copy アクティビティで列マッピングを指定した場合、サービスはソース側の列の一覧を使用してデータの取得を行います。 この場合、行に列の値が含まれていないと、null 値が指定されます。
* Copy アクティビティで列マッピングを指定しない場合、サービスは、データの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていない場合 (たとえば、一部の列に null 値がある場合)、コピー操作の結果で一部の列が欠落します。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Table のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-table-as-a-source-type"></a>ソースの種類として Azure Table を設定する

Azure Table からデータをコピーする場合は、コピー アクティビティのソースの種類を **AzureTableSource** を設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **AzureTableSource** に設定する必要があります。 |はい |
| azureTableSourceQuery |カスタム Table Storage クエリを使用してデータを読み取ります。<br/>ソース クエリは、Azure Table Storage でサポートされている `$filter` クエリ オプションからの直接マップです。[このドキュメント](/rest/api/storageservices/querying-tables-and-entities#supported-query-options)の構文の詳細を確認し、次の [azureTableSourceQuery の例に関するセクション](#azuretablesourcequery-examples)で、例を参照してください。 |いいえ |
| azureTableSourceIgnoreTableNotFound |テーブルが存在しないという例外を受け入れるかどうかを示します。<br/>使用可能な値: **True**、および **False** (既定値)。 |いいえ |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery の例

>[!NOTE]
>Azure Table のクエリ操作は、[Azure Table サービスによって適用される](/rest/api/storageservices/setting-timeouts-for-table-service-operations)とおり、30 秒でタイムアウトとなります。 クエリの最適化方法については、「[クエリに対応した設計](../storage/tables/table-storage-design-for-query.md)」という記事をご覧ください。

datetime 型の列に対してデータをフィルタリングする場合は、この例を参照してください。

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

文字列型の列に対してデータをフィルタリングする場合、この例を参照してください。

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

パイプラインのパラメーターを使用している場合は、前のサンプルに従って、datetime 値を適切なフォーマットにキャストします。

### <a name="azure-table-as-a-sink-type"></a>シンクの種類として Azure Table を設定する

Azure Table にデータをコピーする場合は、コピー アクティビティのシンクの種類を **AzureTableSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティを **AzureTableSink** に設定する必要があります。 |はい |
| azureTableDefaultPartitionKeyValue |シンクで使用できる既定のパーティション キー値です。 |いいえ |
| azureTablePartitionKeyName |値をパーティション キーとして使用する列の名前を指定します。 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。 |いいえ |
| azureTableRowKeyName |値を行キーとして使用する列の名前を指定します。 指定しない場合、各行に GUID を使用します。 |いいえ |
| azureTableInsertType |Azure Table にデータを挿入する方法です。 このプロパティは、一致するパーティションと列キーを持つ出力テーブル内の既存の行で、値を置換するか結合するかを制御します。 <br/><br/>使用可能な値: **マージ** (既定値) および **置換**。 <br/><br> この設定は、テーブル レベルではなく、行レベルで適用されます。 どちらのオプションでも、出力テーブル内の、入力内に存在しない行は削除されません。 結合と置換の設定の機能については、「[Insert or Merge Entity (エンティティの挿入または結合)](/rest/api/storageservices/Insert-Or-Merge-Entity)」および「[Insert or Replace Entity (エンティティの挿入または置換)](/rest/api/storageservices/Insert-Or-Replace-Entity)」をご覧ください。 |いいえ |
| writeBatchSize |writeBatchSize または writeBatchTimeout に達したときに、Azure Table にデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10,000) |
| writeBatchTimeout |writeBatchSize または writeBatchTimeout に達したときに、Azure Table にデータを挿入します。<br/>使用可能な値: 期間。 たとえば "00:20:00" (20 分) を指定できます。 |No (既定値は 90 秒 - ストレージ クライアントの既定のタイムアウト値) |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

azureTablePartitionKeyName として宛先列を使用する前に、 **"translator"** プロパティを使用して、ソース列を宛先列にマップします。

次の例では、ソース列の DivisionID が宛先列の DivisionID にマップされます。

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" は、パーティション キーとして指定されます。

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure Table のデータ型のマッピング

Azure Table をコピー元またはコピー先としてデータをコピーするとき、Azure Table のデータ型からサービス内部で使用される中間データ型へのマッピングは、以下を使用して行われます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

Azure テーブル間でデータの移動時に、次の [Azure Table により定義されたマッピング](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)が Azure Table の OData 型と .NET 型との間の移動に利用されます。

| Azure Table のデータ型 | 中間サービス データ型 | 詳細 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |バイトの配列 (最大 64 KB)。 |
| Edm.Boolean |[bool] |ブール値です。 |
| Edm.DateTime |DateTime |世界協定時刻 (UTC) を表す 64 ビット値。 サポートされている DateTime 範囲は西暦 1601 年 1 月 1 日 UTC 深夜 12:00 から 始まります。 この範囲は 9999 年 12 月 31 日に終了します。 |
| Edm.Double |double |64 ビットの浮動小数点値。 |
| Edm.Guid |Guid |グローバルで一意となる 128 ビットの識別子。 |
| Edm.Int32 |Int32 |32 ビットの整数。 |
| Edm.Int64 |Int64 |64 ビットの整数。 |
| Edm.String |String |UTF-16 エンコードの値。 文字列値は最大 64 KB になります。 |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
コピー アクティビティによってソース、シンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
