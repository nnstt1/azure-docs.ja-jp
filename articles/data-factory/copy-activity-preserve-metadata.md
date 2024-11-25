---
title: コピー アクティビティを使用してメタデータと ACL を保持する
description: Azure Data Factory および Synapse Analytics パイプラインのコピー アクティビティを使用する際に、メタデータと ACL を保持する方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 64608834c5d5b22383242f6739747d955e2feb3a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767377"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics のコピー アクティビティを使用してメタデータと ACL を保持する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

次のシナリオでは、Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用してソースからシンクにデータをコピーするときに、メタデータと ACL を一緒に保持することもできます。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Lake 移行用にメタデータを保持する

[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure Files](connector-azure-file-storage.md) などのデータ レイク間でデータを移行する場合は、データと共にファイルのメタデータを保持することもできます。

コピー アクティビティでは、データのコピー中に次の属性を保持できます。

- **お客様が指定したすべてのメタデータ** 
- 次の **5 つのデータ ストア組み込みシステム プロパティ**: `contentType`、`contentLanguage` (Amazon S3 を除く)、`contentEncoding`、`contentDisposition`、`cacheControl`

**メタデータの違いに対処する:** Amazon S3 と Azure Storage では、顧客が指定したメタデータのキーにさまざまな文字セットが許可されています。 コピー アクティビティを使用してメタデータを保持することを選択すると、サービスによって無効な文字が自動的に "_" に置き換えられます。

バイナリ形式を使用して Amazon S3/Azure Data Lake Storage Gen2/Azure Blob storage/Azure Files から /Azure Files to Azure Data Lake Storage Gen2/Azure Blob storage/Azure Files にファイルをそのままコピーする場合は、アクティビティ作成のための **[コピー アクティビティ]**  >  **[設定]** タブに、または [データのコピー] ツールの **[設定]** ページに、 **[保持]** オプションが表示されます。

:::image type="content" source="./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png" alt-text="コピー アクティビティでのメタデータの保持":::

コピー アクティビティの JSON 構成の例を次に示します (`preserve` を参照)。 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Data Lake Storage Gen1/Gen2 から Gen2 に ACL を保持する

Azure Data Lake Storage Gen1 から Gen2 にアップグレードするときに、または ADLS Gen2 間でデータをコピーするときに、データ ファイルと共に POSIX アクセス制御リスト (ACL) を保持することもできます。 アクセス制御の詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」および「[Azure Data Lake Storage Gen2 のアクセス制御](../storage/blobs/data-lake-storage-access-control.md)」をご覧ください。

コピー アクティビティでは、データのコピー中に次の種類の ACL を保持できます。 1 つ以上の種類を選択できます。

- **ACL**: ファイルおよびディレクトリの POSIX アクセス制御リストがコピーされ、保持されます。 既存の完全な ACL がソースからシンクにコピーされます。 
- **所有者**:ファイルおよびディレクトリの所有ユーザーがコピーされ、保持されます。 シンク Data Lake Storage Gen2 に対するスーパー ユーザーのアクセス権が必要です。
- **グループ**: ファイルおよびディレクトリの所有グループがコピーされ、保持されます。 シンク Data Lake Storage Gen2 に対するスーパー ユーザーのアクセス権、または所有ユーザー (所有ユーザーがターゲット グループのメンバーでもある場合) が必要です。

フォルダーからのコピーを指定すると、指定したフォルダーとその下にあるファイルおよびディレクトリの ACL が、サービスによってレプリケートされます (`recursive` が true に設定されている場合)。 1 つのファイルからのコピーを指定すると、そのファイルの ACL がコピーされます。

>[!NOTE]
>コピー アクティビティを使用して Data Lake Storage Gen1 または Gen2 から Gen2 に ACL を保持すると、シンク Gen2 に対応するフォルダーまたはファイルの既存の ACL が上書きされます。

>[!IMPORTANT]
>ACL を保持する場合は、サービスがシンク Data Lake Storage Gen2 アカウントに対して処理を実行できる十分なアクセス許可を付与してください。 たとえば、アカウント キー認証を使用するか、サービス プリンシパルまたはマネージド ID にストレージ BLOB データ所有者ロールを割り当てます。

バイナリ形式またはバイナリ コピー オプションを使用してソースを Data Lake Storage Gen1/Gen2 として構成し、バイナリ形式またはバイナリ コピー オプションを使用してシンクを Data Lake Storage Gen2 として構成すると、[データのコピー] ツールの **[設定]** ページまたはアクティビティ作成のための **[アクティビティのコピー]**  >  **[設定]** タブに、 **[保持]** オプションが表示されます。

:::image type="content" source="./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png" alt-text="Data Lake Storage Gen1/Gen2 から Gen2 に ACL を保持":::

コピー アクティビティの JSON 構成の例を次に示します (`preserve` を参照)。 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>次のステップ

コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)
