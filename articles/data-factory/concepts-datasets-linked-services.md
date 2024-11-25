---
title: データセット
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory パイプラインおよび Azure Synapse Analytics パイプライン内のデータセットについて説明します。 データセットは、入力/出力データを表します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: dff54916007046d3d0d8d6741ca6fce7409c5d99
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787984"
---
# <a name="datasets-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory および Azure Synapse Analytics 内のデータセット
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [現在のバージョン](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


この記事では、データセットの詳細、データセットを JSON 形式で定義する方法、Azure Data Factory および Azure Synapse Analytics パイプラインで使用する方法について説明します。

Azure Data Factory を初めて使用する場合は、[Azure Data Factory の概要](introduction.md)に関するページを参照してください。  Azure Synapse の詳細については、「[Azure Synapse とは](../synapse-analytics/overview-what-is.md)」を参照してください。

## <a name="overview"></a>概要
Data Factory または Synapse ワークスペースには、1 つ以上のパイプラインを設定できます。 **パイプライン** は、1 つのタスクを連携して実行する **アクティビティ** の論理的なグループです。 パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 ここで、**データセット** とは、**アクティビティ** で入力と出力として使用するデータを単に指定または参照するデータの名前付きビューです。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 たとえば、Azure Blob データセットは、アクティビティによってデータが読み取られる、Blob Storage 内の BLOB コンテナーと BLOB フォルダーを示しています。

データセットを作成する前に、[**リンクされたサービス**](concepts-linked-services.md)を作成して、データ ストアをそのサービスにリンクする必要があります。 リンクされたサービスは、接続文字列によく似ており、サービスが外部リソースに接続するために必要な接続情報を定義します。 データセットはリンクされたデータ ストア内のデータの構造を表すもので、リンクされたサービスはデータ ソースへの接続を定義するもの、と捉えることができます。 たとえば、Azure Storage のリンクされたサービスは、ストレージ アカウントをリンクします。 Azure Blob データセットは、処理対象の入力 BLOB を含む Azure Storage アカウント内の BLOB コンテナーとフォルダーを表します。

シナリオの例を次に示します。 Blob Storage のデータを Azure SQL Database にコピーするために、2 つのリンクされたサービスを作成します。Azure Blob Storage と Azure SQL Database。 次に、2 つのデータセットを作成します。区切りテキスト データセット (ソースとしてテキスト ファイルを使用していることを前提とした、Azure Blob Storage のリンクされたサービスを参照するデータセット) と、Azure SQL Table データセット (Azure SQL Database のリンクされたサービスを参照するデータセット) です。 Azure Blob Storage と Azure SQL Database のリンクされたサービスには、そのサービスが Azure Storage と Azure SQL Database にそれぞれ接続するために実行時に使用する接続文字列が含まれています。 区切りテキスト データセットは、Blob Storage 内の入力 BLOB が含まれた BLOB コンテナーと BLOB フォルダー、およびフォーマットに関する設定を指定します。 Azure SQL Table データセットは、データのコピー先である SQL Database 内の SQL テーブルを示しています。

次の図は、パイプライン、アクティビティ、データセット、リンクされたサービスの関係を示しています。

:::image type="content" source="media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png" alt-text="パイプライン、アクティビティ、データセット、リンクされたサービスの関係":::


## <a name="dataset-json"></a>データセットの JSON
データセットは JSON 形式では次のように定義されます。

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
次の表では、上記の JSON のプロパティについて説明します。

プロパティ | 説明 | 必須 |
-------- | ----------- | -------- |
name | データセットの名前。 「[キューの名前付け規則](naming-rules.md)」を参照してください。 |  はい |
type | データセットの型。 Data Factory でサポートされている型のいずれかを指定します (たとえば、DelimitedText、AzureSqlTable)。 <br/><br/>詳細については、[データセットの型](#dataset-type)を参照してください。 | はい |
schema | データセットのスキーマは、実際のデータ型と形状を表します。 | いいえ |
typeProperties | 型のプロパティは型によって異なります。 サポートされている型とそのプロパティの詳細については、「[データセットの型](#dataset-type)」セクションを参照してください。 | はい |

データセットのスキーマをインポートするときに、 **[スキーマのインポート]** ボタンを選択し、ソースまたはローカル ファイルのどちらからインポートするかを選択します。 ほとんどの場合、ソースからスキーマを直接インポートします。 ただしローカル スキーマ ファイルが既にある (Parquet ファイルまたはヘッダー付きの CSV) 場合は、そのファイルのスキーマを基に、サービスに指示できます。

コピー アクティビティのデータセットは、ソースとシンクで使用されます。 データセットで定義されているスキーマは省略可能で、参照用です。 ソースとシンクの間に列/フィールド マッピングを適用する場合は、[スキーマと型のマッピング](copy-activity-schema-and-type-mapping.md)に関するページを参照してください。

データ フロー内のデータセットは、ソースとシンクの変換で使用されます。 データセットは、基本的なデータ スキーマを定義します。 データがスキーマを持たない場合は、ソースとシンクのスキーマ ドリフトを使用できます。 データセットから取得されたメタデータは、ソース変換でソース プロジェクションとして表示されます。 ソースの変換におけるプロジェクションは、定義された名前と型のデータ フローのデータを表します。

## <a name="dataset-type"></a>データセットの型

サービスでは、使用するデータ ストアに応じて、さまざまな種類のデータセットがサポートされています。 サポートされているデータ ストアの一覧については、[コネクタの概要](connector-overview.md)に関する記事を参照してください。 データ ストアをクリックすると、それに対応するリンクされたサービスとデータセットの作成方法を確認できます。

たとえば、区切りテキスト データセットの場合、次の JSON サンプルに示すように、データセットの型は **DelimitedText** に設定されます。

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>データセットを作成する
データセットの作成には、[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure Resource Manager テンプレート、Azure Portal のいずれかのツール、またはいずれかの SDK を使用できます

## <a name="current-version-vs-version-1-datasets"></a>最新バージョンとバージョン 1 のデータセットの比較

Azure Data Factory の現在のバージョン (および Azure Synapse Analytics) 内のデータセットと、従来の Azure Data Factory バージョン 1 内のデータセットの違いを次に示します。

- 外部プロパティは最新バージョンではサポートされません。 [トリガー](concepts-pipeline-execution-triggers.md)に置き換えらます。
- ポリシーと可用性のプロパティは、最新バージョンではサポートされません。 パイプラインの開始時刻は、[トリガー](concepts-pipeline-execution-triggers.md)によって異なります。
- 範囲指定されたデータセット (パイプラインで定義されたデータセット) は、最新バージョンではサポートされません。

## <a name="next-steps"></a>次のステップ
これらのツールや SDK のいずれかを使用してパイプラインとデータセットを作成する詳しい手順については、次のチュートリアルを参照してください。

- [Quickstart: create a data factory using .NET (クイック スタート: .NET を使用してデータ ファクトリを作成する)](quickstart-create-data-factory-dot-net.md)
- [クイック スタート: PowerShell を使用してデータ ファクトリを作成する](quickstart-create-data-factory-powershell.md)
- [クイック スタート: REST API を使用してデータ ファクトリを作成する](quickstart-create-data-factory-rest-api.md)
- [クイック スタート: Azure portal を使用したデータ ファクトリの作成](quickstart-create-data-factory-portal.md)
