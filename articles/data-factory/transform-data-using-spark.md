---
title: Spark アクティビティを使用してデータを変換する
titleSuffix: Azure Data Factory & Azure Synapse
description: Spark アクティビティを使用して、Azure Data Factory または Synapse Analytics パイプラインから Spark プログラムを実行することによってデータを変換する方法について説明します。
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: d802b911a462ef077fa69d62d524e763ab13efc2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016590"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory と Synapse Analytics で Spark アクティビティを使用してデータを変換する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-spark.md)
> * [現在のバージョン](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ ファクトリと Synapse [パイプライン](concepts-pipelines-activities.md)の Spark アクティビティでは、[独自の](compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンドの](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight クラスターで Spark プログラムを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。 オンデマンドの Spark のリンクされたサービスを使用すると、サービスは自動的に Spark クラスターを作成し、Just-In-Time でデータを処理し、処理が完了するとクラスターを削除します。 


## <a name="spark-activity-properties"></a>Spark アクティビティのプロパティ
Spark アクティビティのサンプルの JSON 定義を次に示します。    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

| プロパティ              | 説明                              | 必須 |
| --------------------- | ---------------------------------------- | -------- |
| name                  | パイプラインのアクティビティの名前。    | はい      |
| description           | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                  | Spark アクティビティの場合、アクティビティの種類は HDInsightSpark です。 | はい      |
| linkedServiceName     | Spark プログラムが実行されている HDInsight Spark のリンクされたサービスの名前。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | はい      |
| SparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。 ここでは **[Azure Blob Storage](./connector-azure-blob-storage.md)** および **[ADLS Gen2](./connector-azure-data-lake-storage.md)** にリンクされたサービスのみがサポートされています。 指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 このプロパティの値には、Azure Storage のリンクされたサービスのみを指定できます。 | いいえ       |
| rootPath              | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 このフォルダーの構造の詳細については、「フォルダー構造」(次のセクション) をご覧ください。 | はい      |
| entryFilePath         | Spark コード/パッケージのルート フォルダーへの相対パス。 エントリ ファイルは、Python ファイルまたは .jar ファイルのいずれかにする必要があります。 | はい      |
| className             | アプリケーションの Java/Spark のメイン クラス      | いいえ       |
| 引数             | Spark プログラムのコマンドライン引数の一覧です。 | いいえ       |
| proxyUser             | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ       |
| sparkConfig           | 「[Spark Configuration - Application properties (Spark 構成 - アプリケーションのプロパティ)](https://spark.apache.org/docs/latest/configuration.html#available-properties)」と題するトピックに示されている Spark 構成プロパティの値を指定します。 | いいえ       |
| getDebugInfo          | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値は以下の通りです。None、Always、または Failure。 既定値:[なし] : | いいえ       |

## <a name="folder-structure"></a>フォルダー構造
Spark ジョブは、Pig/Hive ジョブよりも拡張性に優れています。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Azure Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時にサービスに必要な Azure Blob Storage のフォルダー構造を次に示します。     

| パス                  | 説明                              | 必須 | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (ルート)            | ストレージのリンクされたサービスにおける Spark ジョブのルート パス | はい      | Folder |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス | はい      | ファイル   |
| ./jars                | このフォルダーのすべてのファイルがアップロードされ、クラスターの java classpath に配置されます | いいえ       | Folder |
| ./pyFiles             | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます | いいえ       | Folder |
| ./files               | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます | いいえ       | Folder |
| ./archives            | このフォルダーのファイルは圧縮されていません | いいえ       | Folder |
| ./logs                | Spark クラスターのログが格納されているフォルダー。 | いいえ       | Folder |

次の例のストレージには、HDInsight のリンクされたサービスによって参照される Azure Blob Storage に 2 つの Spark ジョブ ファイルが含まれています。

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs
    
    archives
    
    pyFiles

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
    
    archives
    
    jars
    
    files
    
```
## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
