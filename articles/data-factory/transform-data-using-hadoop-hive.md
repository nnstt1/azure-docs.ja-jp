---
title: Hadoop Hive アクティビティを使用してデータを変換する
description: Azure Data Factory または Synapse Analytics パイプラインで Hive アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hive クエリを実行する方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 2a7330c799111fbb0271aedacd3daaf939db8f38
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005748"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics で Hadoop Hive アクティビティを使用してデータを変換する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-hive-activity.md)
> * [現在のバージョン](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory または Synapse Analytics [パイプライン](concepts-pipelines-activities.md)の HDInsight Hive アクティビティでは、[独自の](compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンドの](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight クラスターで Hive クエリを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。

Azure Data Factory と Synapse Analytics の使用経験がない場合は、この記事を読む前に、[Azure Data Factory](introduction.md) または [Synapse Analytics](../synapse-analytics/overview-what-is.md) の概要に関する記事を参照し、[データ変換のチュートリアル](tutorial-transform-data-spark-powershell.md)を実行してください。 

## <a name="syntax"></a>構文

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>構文の詳細
| プロパティ            | 説明                                                  | 必須 |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | アクティビティの名前                                         | はい      |
| description         | アクティビティの用途を説明するテキストです。                | いいえ       |
| type                | Hive アクティビティの場合、アクティビティの種類は HDinsightHive です        | はい      |
| linkedServiceName   | リンクされたサービスとして登録されている HDInsight クラスターへの参照。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | はい      |
| scriptLinkedService | 実行する Hiveスクリプトの格納に使用される Azure Storage のリンクされたサービスへの参照。 ここでは **[Azure Blob Storage](./connector-azure-blob-storage.md)** および **[ADLS Gen2](./connector-azure-data-lake-storage.md)** にリンクされたサービスのみがサポートされています。 このリンクされたサービスを指定していない場合は、HDInsight のリンクされたサービスで定義されている Azure Storage のリンクされたサービスが使用されます。  | いいえ       |
| scriptPath          | scriptLinkedService で参照される Azure Storage に格納されているスクリプト ファイルへのパスを指定します。 ファイル名は大文字と小文字が区別されます。 | はい      |
| getDebugInfo        | HDInsight クラスターで使用されている Azure Storage または scriptLinkedService で指定された Azure Storage にログ ファイルがコピーされるタイミングを指定します。 使用できる値は以下の通りです。None、Always、または Failure。 既定値:[なし] : | いいえ       |
| 引数           | Hadoop ジョブの引数の配列を指定します。 引数はコマンド ライン引数として各タスクに渡されます。 | いいえ       |
| defines             | Hive スクリプト内で参照するキーと値のペアとしてパラメーターを指定します。 | いいえ       |
| queryTimeout        | クエリのタイムアウト値 (分単位)。 HDInsight クラスターで Enterprise セキュリティ パッケージが有効になっているときに適用できます。 | いいえ       |

>[!NOTE]
>queryTimeout の既定値は 120 分です。 

## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
