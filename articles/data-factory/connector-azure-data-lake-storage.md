---
title: Azure Data Lake Storage Gen2 でのデータのコピーと変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインを使用して、Azure Data Lake Storage Gen2 との間でデータをコピーしたり、Azure Data Lake Storage Gen2 でデータを変換したりする方法について説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6db84461df49a335ce8a3c427fe1408b00034dcd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058219"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用した Azure Data Lake Storage Gen2 でのデータのコピーと変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) は、ビッグ データ分析専用の機能セットであり、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) に組み込まれています。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。

この記事では、コピー アクティビティを使用して、Azure Data Lake Storage Gen2 との間でデータをコピーし、Data Flow を使用して Azure Data Lake Storage Gen2 でデータを変換する方法について説明します。 詳細については、[Azure Data Factory](introduction.md) または [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) の概要記事を参照してください。

>[!TIP]
>データ レイクまたはデータ ウェアハウスの移行シナリオの詳細については、[データ レイクまたはデータ ウェアハウスから Azure にデータを移行する](data-migration-guidance-overview.md)方法に関する記事を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Data Lake Storage Gen2 コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

コピー アクティビティの場合、このコネクタを使用して次のことができます。

- アカウント キー認証、サービス プリンシパル認証、または Azure リソースのマネージド ID 認証を使用して Azure Data Lake Storage Gen2 との間でデータをコピーする。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用してファイルを解析または生成する。
- [コピー中にファイルのメタデータを保持する](#preserve-metadata-during-copy)。
- Azure Data Lake Storage Gen1/Gen2 からコピーするときに [ACL を保持する](#preserve-acls)。

## <a name="get-started"></a>はじめに

>[!TIP]
>Data Lake Storage Gen2 コネクタの使用方法のチュートリアルについては、[Azure Data Lake Storage Gen2 へのデータの読み込み](load-azure-data-lake-storage-gen2.md)に関する記事をご覧ください。

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-data-lake-storage-gen2-linked-service-using-ui"></a>UI を使用して Azure Data Lake Storage Gen2 のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Azure Data Lake Storage Gen2 のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI で新しいリンク サービスを作成するスクリーンショット。":::

2. Data Lake を検索し、Azure Data Lake Storage Gen2 コネクタを選択します。

    :::image type="content" source="media/connector-azure-data-lake-storage/azure-data-lake-storage-connector.png" alt-text="Azure Data Lake Storage Gen2 コネクタを選択する。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-azure-data-lake-storage/configure-data-lake-storage-linked-service.png" alt-text="Azure Data Lake Storage Gen2 リンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Data Lake Storage Gen2 に固有の Data Factory および Synapse パイプライン エンティティの定義に使用されるプロパティについて説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Lake Storage Gen2 コネクタでは、次の認証の種類がサポートされています。 詳細については、対応するセクションをご覧ください。

- [アカウント キー認証](#account-key-authentication)
- [サービス プリンシパルの認証](#service-principal-authentication)
- [システム割り当てマネージド ID 認証](#managed-identity)
- [ユーザー割り当てマネージド ID 認証](#user-assigned-managed-identity-authentication)
- 
>[!NOTE]
>- Azure Storage ファイアウォールで有効にした **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** オプションを利用することで、パブリック Azure 統合ランタイムを使用して Data Lake Storage Gen2 に接続する場合は、[マネージド ID 認証](#managed-identity)を使用する必要があります。
>- PolyBase または COPY ステートメントを使用して Azure Synapse Analytics にデータを読み込むときに、ソースまたはステージング Data Lake Storage Gen2 が Azure Virtual Network エンドポイントで構成されている場合は、Azure Synapse の要求に従ってマネージド ID 認証を使用する必要があります。 構成の前提条件の詳細については、[マネージド ID 認証](#managed-identity)セクションを参照してください。

### <a name="account-key-authentication"></a>アカウント キー認証

ストレージ アカウント キー認証の使用には、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| accountKey | Data Lake Storage Gen2 のアカウント キー。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

>[!NOTE]
>アカウント キー認証を使用する場合、セカンダリ ADLS ファイル システム エンドポイントはサポートされません。 ほかの認証の種類を使用できます。

**例:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパル認証を使用するには、次の手順に従います。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」の手順に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. サービス プリンシパルに適切なアクセス許可を付与します。 Data Lake Storage Gen2 におけるアクセス許可の動作例については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

    - **ソースとして**: Storage Explorer で、すべての上位フォルダーとファイル システムについて、少なくとも **実行** アクセス許可を与えると共に、コピーするファイルの **読み取り** アクセス許可を与えます。 または、[アクセス制御 (IAM)] で、少なくとも **ストレージ BLOB データ閲覧者** ロールを付与します。
    - **シンクとして**: Storage Explorer で、すべての上位フォルダーとファイル システムについて、少なくとも **実行** アクセス許可を与えると共に、シンク フォルダーに対する **書き込み** アクセス許可を与えます。 または、[アクセス制御 (IAM)] で、少なくとも **ストレージ BLOB データ共同作成者** ロールを付与します。

>[!NOTE]
>UI を使用して作成する場合で、なおかつ IAM でサービス プリンシパルに "ストレージ BLOB データ閲覧者またはストレージ BLOB データ共同作成者" ロールが設定されていない場合、テスト接続時またはフォルダーの参照 (または移動) 時は、[Test connection to file path]\(ファイル パスへのテスト接続\) または [Browse from specified path]\(指定したパスから参照\) を選択し、**読み取りおよび実行** アクセス許可のあるパスを指定して続行してください。

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalCredentialType | サービス プリンシパル認証に使用する資格情報の種類。 使用できる値は **ServicePrincipalKey** と **ServicePrincipalCert** です。 | Yes |
| servicePrincipalCredential | サービス プリンシパルの資格情報。 <br/> 資格情報の種類として **ServicePrincipalKey** を使用する場合は、アプリケーションのキーを指定します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 <br/> 資格情報として **ServicePrincipalCert** を使用する場合、Azure Key Vault 内の証明書を参照します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 <br/> このプロパティは `servicePrincipalId` + `servicePrincipalKey` でもそのままサポートされています。 ADF により、新しいサービス プリンシパル証明書認証が追加されると、サービス プリンシパル認証の新しいモデルは `servicePrincipalId` + `servicePrincipalCredentialType` + `servicePrincipalCredential` になります。 | いいえ |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 これは、Azure portal の右上隅をマウスでポイントすることで取得できます。 | はい |
| azureCloudType | サービス プリンシパル認証の場合は、Azure Active Directory アプリケーションの登録先である Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、データ ファクトリまたは Synapse パイプラインのクラウド環境が使用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例: サービス プリンシパル キー認証の使用**

サービス プリンシパル キーを Azure Key Vault に格納することもできます。

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: サービス プリンシパル認証の使用**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> システム割り当てマネージド ID 認証

データ ファクトリまたは Synapse ワークスペースは、[システム割り当てマネージド ID](data-factory-service-identity.md) に関連付けることができます。  独自のサービス プリンシパルを使用する場合と同様に、Data Lake Storage Gen2 の認証にこのシステム割り当てマネージド ID を直接使用できます。 これにより、この指定されたファクトリまたはワークスペースは、Data Lake Storage Gen2 にアクセスしてデータをコピーできます。

システム割り当てマネージド ID 認証を使用するには、次の手順に従います。

1. データ ファクトリまたは Synapse ワークスペースと共に生成された **マネージド ID オブジェクト ID** の値をコピーして、[システム割り当てマネージド ID 情報を取得](data-factory-service-identity.md#retrieve-managed-identity)します。

2. システム割り当てマネージド ID に適切なアクセス許可を付与します。 Data Lake Storage Gen2 におけるアクセス許可の動作例については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

    - **ソースとして**: Storage Explorer で、すべての上位フォルダーとファイル システムについて、少なくとも **実行** アクセス許可を与えると共に、コピーするファイルの **読み取り** アクセス許可を与えます。 または、[アクセス制御 (IAM)] で、少なくとも **ストレージ BLOB データ閲覧者** ロールを付与します。
    - **シンクとして**: Storage Explorer で、すべての上位フォルダーとファイル システムについて、少なくとも **実行** アクセス許可を与えると共に、シンク フォルダーに対する **書き込み** アクセス許可を与えます。 または、[アクセス制御 (IAM)] で、少なくとも **ストレージ BLOB データ共同作成者** ロールを付与します。

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>ユーザー割り当てマネージド ID 認証

データ ファクトリは、1 つ以上の[ユーザー割り当てマネージド ID](data-factory-service-identity.md#user-assigned-managed-identity) に割り当てることができます。 このユーザー割り当てマネージド ID を BLOB ストレージ認証に使用できます。これにより、Data Lake Storage Gen2 にアクセスしてデータをコピーできます。 Azure リソース用マネージド ID の詳細については、[Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

ユーザー割り当てマネージド ID 認証を使用するには、次の手順に従います。

1. [1 つ以上のユーザー割り当てマネージド ID を作成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)して、Azure Data Lake Storage Gen2 に対するアクセスを許可します。 Data Lake Storage Gen2 におけるアクセス許可の動作例については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

    - **ソースとして**: Storage Explorer で、すべての上位フォルダーとファイル システムについて、少なくとも **実行** アクセス許可を与えると共に、コピーするファイルの **読み取り** アクセス許可を与えます。 または、[アクセス制御 (IAM)] で、少なくとも **ストレージ BLOB データ閲覧者** ロールを付与します。
    - **シンクとして**: Storage Explorer で、すべての上位フォルダーとファイル システムについて、少なくとも **実行** アクセス許可を与えると共に、シンク フォルダーに対する **書き込み** アクセス許可を与えます。 または、[アクセス制御 (IAM)] で、少なくとも **ストレージ BLOB データ共同作成者** ロールを付与します。
    
2. 1 つ以上のユーザー割り当てマネージド ID をデータ ファクトリに割り当てて、ユーザー割り当てマネージド ID ごとに[資格情報を作成](credentials.md)します。 

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| 資格情報 | ユーザー割り当てマネージド ID を資格情報オブジェクトとして指定します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
                },
            },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

>[!NOTE]
>Data Factory の UI を使用して作成する場合で、なおかつ IAM でマネージド ID に "ストレージ BLOB データ閲覧者またはストレージ BLOB データ共同作成者" ロールが設定されていない場合、テスト接続時またはフォルダーの参照 (または移動) 時は、[Test connection to file path]\(ファイル パスへのテスト接続\) または [Browse from specified path]\(指定したパスから参照\) を選択し、**読み取りおよび実行** アクセス許可のあるパスを指定して続行してください。

>[!IMPORTANT]
>PolyBase または COPY ステートメントを使用して Data Lake Storage Gen2 から Azure Synapse Analytics にデータを読み込む場合に、Data Lake Storage Gen2 に対してマネージド ID 認証を使用するときは、必ず[こちらのガイダンス](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)の手順 1 から 3 にも従ってください。 これらの手順により、サーバーが Azure AD に登録され、ストレージ BLOB データ共同作成者のロールがサーバーに割り当てられます。 残りの処理は Data Factory によって行われます。 Azure Virtual Network エンドポイントを使用して BLOB ストレージを構成する場合は、さらに Azure Synapse の要求に従って、Azure Storage アカウントで、 **[ファイアウォールと仮想ネットワーク]** 設定メニューの **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにする必要があります。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Data Lake Storage Gen2 では、形式ベースのデータセットの `location` 設定で次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の type プロパティは、**AzureBlobFSLocation** に設定する必要があります。 | はい      |
| fileSystem | Data Lake Storage Gen2 ファイル システム名。                              | いいえ       |
| folderPath | 指定されたファイル システムの下のフォルダーのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定で指定します。 | いいえ       |
| fileName   | 指定された fileSystem + folderPath の下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定で指定します。 | いいえ       |

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に使用できるセクションとプロパティの一覧については、[コピー アクティビティの構成](copy-activity-overview.md#configuration)および[パイプラインとアクティビティ](concepts-pipelines-activities.md)に関する記事をご覧ください。 このセクションでは、Data Lake Storage Gen2 のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>ソースの種類としての Azure Data Lake Storage Gen2

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

ADLS Gen2 からデータをコピーするには、いくつかのオプションがあります。

- データセットに指定されている特定のパスからコピーします。
- フォルダー パスまたはファイル名に対するワイルドカード フィルターについては、`wildcardFolderPath` および `wildcardFileName` を確認してください。
- 特定のテキスト ファイルで定義されているファイルをファイル セットとしてコピーします。`fileListPath` を確認してください。

Data Lake Storage Gen2 では、形式ベースのコピー ソースの `storeSettings` 設定で次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の type プロパティは **AzureBlobFSReadSettings** に設定する必要があります。 | はい                                           |
| ***コピーするファイルを特定する:*** |  |  |
| オプション 1: 静的パス<br> | データセットに指定されている特定のファイル システムまたはフォルダーかファイル パスからコピーします。 ファイル システムまたはフォルダーからすべてのファイルをコピーする場合は、さらに `*` として `wildcardFileName` を指定します。 |  |
| オプション 2: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するためにデータセットで構成されている、特定のファイル システムの下のワイルドカード文字を含むフォルダーのパス。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| オプション 2: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、特定のファイル システム + folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい |
| オプション 3: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスであるファイルを 1 行につき 1 つずつ指定します。<br/>このオプションを使用する場合は、データ セットにファイル名を指定しないでください。 その他の例については、[ファイル リストの例](#file-list-examples)を参照してください。 |いいえ |
| ***追加の設定:*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 <br>使用可能な値: **true** (既定値) および **false**。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 |いいえ |
| deleteFilesAfterCompletion | 宛先ストアに正常に移動した後、バイナリ ファイルをソース ストアから削除するかどうかを示します。 ファイルの削除はファイルごとに行われるので、コピー操作が失敗した場合、一部のファイルが既に宛先にコピーされソースからは削除されているが、他のファイルはまだソース ストアに残っていることがわかります。 <br/>このプロパティは、バイナリ ファイルのコピー シナリオでのみ有効です。 既定値: false。 |いいえ |
| modifiedDatetimeStart    | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 <br>最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| enablePartitionDiscovery | パーティション分割されているファイルの場合は、ファイル パスのパーティションを解析し、それを追加のソース列として追加するかどうかを指定します。<br/>指定できる値は **false** (既定値) と **true** です。 | いいえ                                            |
| partitionRootPath | パーティション検出が有効になっている場合は、パーティション分割されたフォルダーをデータ列として読み取るための絶対ルート パスを指定します。<br/><br/>これが指定されていない場合は、既定で次のようになります。<br/>- ソース上のデータセットまたはファイルの一覧内のファイル パスを使用する場合、パーティションのルート パスはそのデータセットで構成されているパスです。<br/>- ワイルドカード フォルダー フィルターを使用する場合、パーティションのルート パスは最初のワイルドカードの前のサブパスです。<br/><br/>たとえば、データセット内のパスを "root/folder/year=2020/month=08/day=27" として構成するとします。<br/>- パーティションのルート パスを "root/folder/year=2020" として指定した場合は、コピー アクティビティによって、ファイル内の列とは別に、それぞれ "08" と "27" の値を持つ `month` と `day` という 2 つの追加の列が生成されます。<br/>- パーティションのルート パスが指定されない場合、追加の列は生成されません。 | いいえ                                            |
| maxConcurrentConnections | アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ                                            |

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>シンクの種類としての Azure Data Lake Storage Gen2

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)]

Data Lake Storage Gen2 では、形式ベースのコピー シンクの `storeSettings` 設定で次のプロパティがサポートされます。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **AzureBlobFSWriteSettings** に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| blockSizeInMB | ADLS Gen2 にデータを書き込むために使用するブロック サイズを MB 単位で指定します。 詳細は、[ブロック BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) に関するページを参照してください。 <br/>指定できる値は、**4 MB から 100 MB** です。 <br/>既定では、ソース ストアの種類とデータに基づいて、ADF によってブロック サイズが自動的に決定されます。 ADLS Gen2 への非バイナリ コピーの場合、既定のブロック サイズは 100 MB なので、最大 4.95 TB のデータに収まります。 データが大きくない場合、特に、ネットワークが不十分な状態でセルフホステッド統合ランタイムを使用し、操作のタイムアウトやパフォーマンスの問題が発生する場合は、最適ではない可能性があります。 ブロック サイズは明示的に指定できますが、blockSizeInMB*50000 が確実にデータを格納するのに十分な大きさであるようにします。そうしないと、コピー アクティビティの実行は失敗します。 | いいえ |
| maxConcurrentConnections | アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ       |
| metadata |シンクにコピーするときにカスタム メタデータを設定します。 `metadata` 配列の各オブジェクトは追加列を表します。 `name` ではメタデータ キー名を定義し、`value` では、そのキーのデータ値を指定します。 [属性の保持機能](./copy-activity-preserve-metadata.md#preserve-metadata)が使用されている場合は、ソース ファイルのメタデータを使用して、指定されたメタデータの和集合の作成や上書きが行われます。<br/><br/>使用できるデータ値:<br/>- `$$LASTMODIFIED`: 予約済みの変数によって、ソース ファイルの最終更新時刻を格納することを指定します。 バイナリ形式のファイルベースのソースにのみ適用されます。<br/><b>- 式<b><br/>- <b>静的な値<b>| いいえ       |

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy",
                    "metadata": [
                        {
                            "name": "testKey1",
                            "value": "value1"
                        },
                        {
                            "name": "testKey2",
                            "value": "value2"
                        },
                        {
                            "name": "lastModifiedKey",
                            "value": "$$LASTMODIFIED"
                        }
                    ]
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| folderPath | fileName | recursive | ソースのフォルダー構造とフィルターの結果 (**太字** のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空、既定値を使用) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空、既定値を使用) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

このセクションでは、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。

次のソース フォルダー構造があり、太字のファイルをコピーするとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | ADF 構成                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| filesystem<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- ファイル システム: `filesystem`<br>- フォルダー パス: `FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `filesystem/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスで 1 行につき 1 つのファイルを指定します。 |


### <a name="some-recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例

このセクションでは、recursive 値と copyBehavior 値のさまざまな組み合わせでのコピー操作の動作について説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3、File4、File5 を含む Subfolder1 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>File3、File4、File5 を含む Subfolder1 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>File3、File4、File5 を含む Subfolder1 は取得されません。 |

## <a name="preserve-metadata-during-copy"></a>コピー中にメタデータを保持する

Amazon S3、Azure Blob、Azure Data Lake Storage Gen2 から Azure Data Lake Storage Gen2、Azure Blob にファイルをコピーする場合、ファイルのメタデータをデータと共に保持することもできます。 詳細については、[メタデータの保存](copy-activity-preserve-metadata.md#preserve-metadata)に関する記事を参照してください。

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Data Lake Storage Gen1/Gen2 の ACL を保持する

Azure Data Lake Storage Gen1/Gen2 から Gen2 にファイルをコピーするときに、データと共に POSIX アクセス制御リスト (ACL) を保持することもできます。 詳細については、[Data Lake Storage Gen1/Gen2 から Gen2 への ACL の保持](copy-activity-preserve-metadata.md#preserve-acls)に関するページをご覧ください。

>[!TIP]
>Azure Data Lake Storage Gen1 から Gen2 への一般的なデータ コピーに関するチュートリアルとベスト プラクティスについては、[Azure Data Lake Storage Gen1 から Gen2 へのデータのコピー](load-azure-data-lake-storage-gen2-from-gen1.md)に関する記事を参照してください。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換するときには、Azure Data Lake Storage Gen2 にある次の形式のファイルの読み取りと書き込みが可能です。
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common Data Model (プレビュー)](format-common-data-model.md#mapping-data-flow-properties)
* [区切りテキスト](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

形式固有の設定は、各形式のドキュメントに記載されています。 詳細については、「[マッピング データ フローのソース変換](data-flow-source.md)」および「[マッピング データ フローでのシンク変換](data-flow-sink.md)」を参照してください。

### <a name="source-transformation"></a>ソース変換

ソース変換では、Azure Data Lake Storage Gen2 内のコンテナー、フォルダー、または個々のファイルから読み取ることができます。 **[Source options]\(ソース オプション\)** タブで、ファイルの読み取り方法を管理できます。 

:::image type="content" source="media/data-flow/sourceOptions1.png" alt-text="ソース オプション":::

**[Wildcard path]\(ワイルドカード パス\)** : ワイルドカード パターンを使用すると、ADF は、単一のソース変換で一致する各フォルダーとファイルをループ処理するよう指示されます。 これは、単一のフロー内の複数のファイルを処理するのに効果的な方法です。 既存のワイルドカード パターンをポイントしたときに表示される + 記号を使って複数のワイルドカード一致パターンを追加します。

ソース コンテナーから、パターンに一致する一連のファイルを選択します。 データセット内で指定できるのはコンテナーのみです。 そのため、ワイルドカード パスには、ルート フォルダーからのフォルダー パスも含める必要があります。

ワイルドカードの例:

* ```*``` - 任意の文字セットを表します。
* ```**``` - ディレクトリの再帰的な入れ子を表します。
* ```?``` - 1 文字を置き換えます。
* ```[]``` - 角カッコ内の文字のいずれか 1 つと一致します。

* ```/data/sales/**/*.csv``` - /data/sales の下のすべての csv ファイルを取得します。
* ```/data/sales/20??/**/``` - 20 世紀のすべてのファイルを取得します。
* ```/data/sales/*/*/*.csv``` - /data/sales の 2 レベル下の csv ファイルを取得します。
* ```/data/sales/2004/*/12/[XY]1?.csv``` - 前に 2 桁の数字が付いた X または Y で始まる、2004 年 12 月のすべての csv ファイルを取得します。

**[Partition root path]\(パーティションのルート パス\):** ```key=value``` 形式 (例: year=2019) のファイル ソース内のフォルダーをパーティション分割した場合、そのパーティション フォルダー ツリーの最上位をデータ フロー データ ストリーム内の列名に割り当てることができます。

最初に、ワイルドカードを設定して、パーティション分割されたフォルダーと読み取るリーフ ファイルのすべてのパスを含めます。

:::image type="content" source="media/data-flow/partfile2.png" alt-text="パーティション ソース ファイルの設定":::

パーティションのルート パス設定を使用して、フォルダー構造の最上位レベルを定義します。 データ プレビューを使用してデータの内容を表示すると、ADF によって、各フォルダー レベルで見つかった解決済みのパーティションが追加されることがわかります。

:::image type="content" source="media/data-flow/partfile1.png" alt-text="パーティションのルート パス":::

**[List of files]:** これはファイル セットです。 処理する相対パス ファイルの一覧を含むテキスト ファイルを作成します。 このテキスト ファイルをポイントします。

**[Column to store file name]\(ファイル名を格納する列\):** ソース ファイルの名前をデータの列に格納します。 ファイル名文字列を格納するための新しい列名をここに入力します。

**[After completion]\(完了後\):** データ フローの実行後にソース ファイルに何もしないか、ソース ファイルを削除するか、またはソース ファイルを移動することを選択します。 移動のパスは相対パスです。

後処理でソース ファイルを別の場所に移動するには、まず、ファイル操作の "移動" を選択します。 次に、"移動元" ディレクトリを設定します。 パスにワイルドカードを使用していない場合、"移動元" 設定はソース フォルダーと同じフォルダーになります。

ワイルドカードを含むソース パスがある場合、構文は次のようになります。

`/data/sales/20??/**/*.csv`

"移動元" は次のように指定できます。

`/data/sales`

"移動先" は次のように指定できます。

`/backup/priorSales`

この場合、ソースとして指定された /data/sales の下のすべてのファイルは /backup/priorSales に移動されます。

> [!NOTE]
> ファイル操作は、パイプライン内のデータ フローの実行アクティビティを使用するパイプライン実行 (パイプラインのデバッグまたは実行) からデータ フローを開始する場合にのみ実行されます。 データ フロー デバッグ モードでは、ファイル操作は実行 *されません*。

**[Filter by last modified]\(最終更新日時でフィルター処理\):** 最終更新日時の範囲を指定することで、処理するファイルをフィルター処理できます。 日時はすべて UTC 形式です。 

### <a name="sink-properties"></a>シンク プロパティ

シンク変換では、Azure Data Lake Storage Gen2 内のコンテナーまたはフォルダーに書き込むことができます。 **[設定]** タブで、ファイルへの書き込み方法を管理できます。

:::image type="content" source="media/data-flow/file-sink-settings.png" alt-text="シンクのオプション":::

**Clear the folder**\(フォルダーのクリア\):データが書き込まれる前に、書き込み先のフォルダーをクリアするかどうかを決定します。

**File name option**\(ファイル名のオプション\):書き込み先のフォルダー内の書き込み先ファイルの命名方法を決定します。 ファイル名のオプションを次に示します。
   * **既定**:Spark は PART の既定値に基づいて、ファイルに名前を付けることができます。
   * **パターン**:パーティションごとに出力ファイルを列挙するパターンを入力します。 たとえば、**loans[n].csv** と入力すると、loans1.csv、loans2.csv のように作成されます。
   * **Per partition**(パーティションあたり):パーティションごとに 1 つのファイル名を入力します。
   * **As data in column**(列内のデータとして):出力ファイルを列の値に設定します。 パスは、書き込み先フォルダーではなく、データセット コンテナーからの相対パスです。 データセット内にフォルダー パスがある場合は、上書きされます。
   * **Output to a single file**(1 つのファイルに出力する):パーティション分割された出力ファイルを単一の名前付きファイルに結合します。 パスは、データセット フォルダーからの相対パスです。 このマージ操作は、ノード サイズによっては失敗する可能性があることに注意してください。 このオプションは、大規模なデータセットに対しては推奨されません。

**Quote all**\(すべてを引用符で囲む\):すべての値を引用符で囲むかどうかを決定します
    
### ```umask```

必要に応じて、所有者、ユーザー、グループに POSIX の read、write、execute フラグを使用して、ファイルの ```umask``` を設定できます。
    
### <a name="pre-processing-and-post-processing-commands"></a>前処理および後処理コマンド
    
必要に応じて、ADLS Gen2 シンクへの書き込み前または書き込み後に、Hadoop ファイルシステム コマンドを実行できます。 次のコマンドがサポートされています。
    
* ```cp```
* ```mv```
* ```rm```
* ```mkdir```

例:

* ```mkdir /folder1```
* ```mkdir -p folder1```
* ```mv /folder1/*.* /folder2/```
* ```cp /folder1/file1.txt /folder2```
* ```rm -r /folder1```

式ビルダーを介して、パラメーターもサポートされています。次に例を示します。

`mkdir -p {$tempPath}/commands/c1/c2`
`mv {$tempPath}/commands/*.* {$tempPath}/commands/c1/c2`

既定では、フォルダーは user/root として作成されます。 "/" を使用して最上位のコンテナーを参照します。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

プロパティの詳細については、[Delete アクティビティ](delete-activity.md)に関するページを参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 今後は、上のセクションで説明した新しいモデルを使用することをお勧めします。ADF オーサリング UI は、新しいモデルを生成するように切り替えられています。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureBlobFSFile** に設定する必要があります。 |はい |
| folderPath | Data Lake Storage Gen2 のフォルダーのパス。 指定しないと、ルートが参照されます。 <br/><br/>ワイルドカード フィルターがサポートされています。 使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: filesystem/folder/。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 |いいえ |
| fileName | 指定された "folderPath" の下にあるファイルの名前またはワイルドカード フィルター。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティは、"*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* " というパターンでファイル名が自動的に生成されます (例: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz")。 クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合、名前のパターンは " *[table name].[format].[compression if configured]* " になります (例: "MyTable.csv")。 |いいえ |
| modifiedDatetimeStart | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 大量のファイルでファイル フィルターを実行する場合、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 大量のファイルでファイル フィルターを実行する場合、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、「[テキスト形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)」、「[JSON 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)」、「[AVRO 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)」、「[ORC 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)」、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)」の各セクションをご覧ください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、```**GZip**, **Deflate**, **BZip2**, and **ZipDeflate**``` です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一のファイルをコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

**例:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>レガシ コピー アクティビティ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **AzureBlobFSSource** に設定する必要があります。 |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされません。<br/>使用可能な値: **true** (既定値) および **false**。 | いいえ |
| maxConcurrentConnections | アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>レガシ コピー アクティビティ シンク モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **AzureBlobFSSink** に設定する必要があります。 |はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ |
| maxConcurrentConnections | アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>次のステップ

コピー アクティビティによってソース、シンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
