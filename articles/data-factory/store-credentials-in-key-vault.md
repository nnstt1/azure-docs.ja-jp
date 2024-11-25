---
title: Azure Key Vault への資格情報の格納
description: Azure Data Factory で実行時に自動的に取得できる、Azure Key Vault で使用されたデータ ストアの資格情報を格納する方法を説明します。
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: abnarain
ms.openlocfilehash: 2ac261adeade46b14651583cf28803cab039d754
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743168"
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault への資格情報の格納

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ ストアや計算のための資格情報を [Azure Key Vault](../key-vault/general/overview.md) に格納することができます。 Azure Data Factory は、データ ストア/計算を使うアクティビティの実行時に、資格情報を取得します。

現時点では、カスタム アクティビティを除くすべてのアクティビティの種類が、この機能をサポートしています。 具体的なコネクタの構成について詳しくは、[各コネクタ トピック](copy-activity-overview.md#supported-data-stores-and-formats)の「リンクされたサービスのプロパティ」セクションをご覧ください。

## <a name="prerequisites"></a>前提条件

この機能は、データ ファクトリのマネージド ID に依存しています。 [データ ファクトリのマネージド ID](data-factory-service-identity.md) からの使用方法と、データ ファクトリに関連付けられていることを確認する方法について説明します。

## <a name="steps"></a>手順

Azure Key Vault に格納されている資格情報を参照するには、次の手順に従う必要があります。

1. ファクトリと共に生成された "マネージド ID オブジェクト ID" の値をコピーして、**データ ファクトリのマネージド ID を取得** します。 ADF オーサリング UI を使用する場合、マネージド ID オブジェクト ID が Azure Key Vault のリンクされたサービスの作成ウィンドウに表示されます。Azure portal から取得することもできます。「[Retrieve data factory service IDentity](data-factory-service-identity.md#retrieve-managed-identity)」(データ ファクトリのマネージド ID の取得) を参照してください。
2. **マネージド ID に、Azure Key Vault へのアクセス権を付与します。** キー コンテナーで、[アクセス ポリシー]、[アクセス ポリシーの追加] の順に選択し、このマネージド ID を検索して、[シークレットのアクセス許可] ドロップダウンで **GET** アクセス許可を付与します。 この指定されたファクトリで、キー コンテナー内のシークレットにアクセスできます。
3. **Azure Key Vault をポイントするリンクされたサービスを作成します。** 「[Azure Key Vault のリンクされたサービス](#azure-key-vault-linked-service)」をご覧ください。
4. **データ ストアのリンクされたサービスを作成します。その内部で、キー コンテナーに格納されている対応するシークレットを参照します。** 「[キー コンテナーに格納されたシークレットの参照](#reference-secret-stored-in-key-vault)」をご覧ください。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault のリンクされたサービス

Azure Key Vault のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzureKeyVault**。 | はい |
| baseUrl | Azure Key Vault の URL を指定します。 | はい |

**オーサリング UI の使用:**

**[接続]**  ->  **[リンクされたサービス]**  ->  **[新規]** を選択します。 新しいリンクされたサービスで、"Azure Key Vault" を検索して選択します。

:::image type="content" source="media/store-credentials-in-key-vault/search-akv.png" alt-text="Azure Key Vault の検索":::

資格情報が格納されるプロビジョニングされた Azure Key Vault を選択します。 **テスト接続** を実行し、AKV 接続が有効なことを確認します。 

:::image type="content" source="media/store-credentials-in-key-vault/configure-akv.png" alt-text="Azure Key Vault を構成する":::

**JSON の例:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>キー コンテナーに格納されたシークレットの参照

キー コンテナーのシークレットを参照するリンクされたサービスのフィールドを構成する場合は、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | フィールドの type プロパティは、**AzureKeyVaultSecret** に設定する必要があります。 | はい |
| secretName | Azure Key Vault 内のシークレットの名前。 | はい |
| secretVersion | Azure Key Vault 内のシークレットのバージョン。<br/>指定しない場合は、常に最新バージョンのシークレットが使用されます。<br/>指定した場合は、その特定のバージョンに固定されます。| いいえ |
| store | 資格情報の格納に使用する Azure Key Vault のリンクされたサービスを表します。 | はい |

**オーサリング UI の使用:**

データストア/コンピューティングへの接続を作成するときに、シークレット フィールドに対して **Azure Key Vault** を選択します。 プロビジョニングされた Azure Key Vault のリンクされたサービスを選択し、**シークレット名** を指定します。 シークレット バージョンも必要に応じて指定できます。 

>[!TIP]
>SQL Server や Blob ストレージなどのリンクされたサービスにおける接続文字列を使用した接続については、AKV でのパスワードなどの secret フィールドのみを格納するか、AKV の接続文字列全体を確認するかを選択できます。 どちらのオプションも UI で入手できます。

:::image type="content" source="media/store-credentials-in-key-vault/configure-akv-secret.png" alt-text="Azure Key Vault のシークレットの構成":::

**JSON の例: ("password" セクションをご覧ください)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
