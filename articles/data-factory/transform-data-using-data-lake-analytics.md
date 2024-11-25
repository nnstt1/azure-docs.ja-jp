---
title: U-SQL スクリプトを使用してデータを変換する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Synapse Analytics を使用して Azure Data Lake Analytics コンピューティング サービスで U-SQL スクリプトを実行してデータを処理または変換する方法について説明します。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 3cf224f3b24d318818e53a8c6342edc9f56063e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077821"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics-with-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory と Synapse Analytics を使用して Azure Data Lake Analytics で U-SQL スクリプトを実行してデータを処理する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-usql-activity.md)
> * [現在のバージョン](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory または Synapse Analytics ワークスペースのパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。 パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。 この記事では、**Azure Data Lake Analytics** コンピューティング リンク サービスで **U-SQL** スクリプトを実行する **Data Lake Analytics U-SQL アクティビティ** について説明します。 

Data Lake Analytics U-SQL アクティビティでパイプラインを作成する前に、Azure Data Lake Analytics アカウントを作成します。 Azure Data Lake Analytics の詳細については、 [Azure Data Lake Analytics の使用開始](../data-lake-analytics/data-lake-analytics-get-started-portal.md)に関するページをご覧ください。


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory または Synapse Analytics ワークスペースにリンクします。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

次の表では、JSON 定義で使用される一般的なプロパティを説明しています。 

| プロパティ                 | 説明                              | 必須                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | type プロパティは次の値に設定されます。**AzureDataLakeAnalytics**。 | はい                                      |
| **accountName**          | Azure Data Lake Analytics アカウント名。  | はい                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI。           | いいえ                                       |
| **subscriptionId**       | Azure サブスクリプション ID                    | いいえ                                       |
| **resourceGroupName**    | Azure リソース グループ名                | いいえ                                       |

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証
Azure Data Lake Analytics のリンクされたサービスには、Azure Data Lake Analytics サービスに接続するためのサービス プリンシパル認証が必要です。 サービス プリンシパル認証を使うには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録し、Data Lake Analytics およびそれを使用する Data Lake Store の両方へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

* アプリケーション ID
* アプリケーション キー 
* テナント ID

[ユーザー追加ウィザード](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)を使用して、Azure Data Lake Analytics へのサービス プリンシパル アクセス許可を付与します。

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ                | 説明                              | 必須 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | アプリケーションのクライアント ID を取得します。     | はい      |
| **servicePrincipalKey** | アプリケーションのキーを取得します。           | はい      |
| **tenant**              | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい      |

**例:サービス プリンシパルの認証**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
次の JSON のスニペットでは、Data Lake Analytics U-SQL アクティビティを使用してパイプラインを定義します。 このアクティビティ定義には、先ほど作成した Azure Data Lake Analytics リンク サービスへの参照が含まれています。 Data Lake Analytics U-SQL スクリプトを実行するために、指定したスクリプトがサービスによって Data Lake Analytics に送信されます。必要な入力と出力は、Data Lake Analytics がフェッチおよび出力するためのスクリプトで定義されています。 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

次の表は、このアクティビティに固有のプロパティの名前と説明です。 

| プロパティ            | 説明                              | 必須 |
| :------------------ | :--------------------------------------- | :------- |
| name                | パイプラインのアクティビティの名前。     | はい      |
| description         | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                | Data Lake Analytics U-SQL アクティビティの場合、アクティビティの種類は **DataLakeAnalyticsU-SQL** です。 | はい      |
| linkedServiceName   | Azure Data Lake Analytics にリンクされたサービス。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。  |はい       |
| scriptPath          | U-SQL スクリプトを含むフォルダーのパス。 ファイル名は大文字と小文字が区別されます。 | はい      |
| scriptLinkedService | スクリプトを含む **Azure Data Lake Store** または **Azure Storage** をリンクするリンク サービス | はい      |
| degreeOfParallelism | ジョブを実行するために同時に使用される最大ノード数。 | いいえ       |
| priority            | キューされているすべてのジョブのうち、先に実行するジョブを決定します。 数値が小さいほど、優先度は高くなります。 | いいえ       |
| parameters          | U-SQL スクリプトに渡すパラメーター。    | いいえ       |
| runtimeVersion      | 使用する U-SQL エンジンのランタイム バージョン。 | いいえ       |
| compilationMode     | <p>U-SQL のコンパイル モード。 次のいずれかの値を指定する必要があります。**Semantic:** セマンティック チェックと必要なサニティ チェックのみを実行します。**Full:** 構文チェック、最適化、コード生成などを含めた完全コンパイルを実行します。**SingleBox:** TargetType を SingleBox に設定して完全コンパイルを実行します。 このプロパティの値を指定しない場合、サーバーが最適なコンパイル モードを決定します。 | いいえ |

スクリプト定義については、[SearchLogProcessing.txt](#sample-u-sql-script) をご覧ください。 

## <a name="sample-u-sql-script"></a>U-SQL スクリプトのサンプル

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

前述のスクリプト例では、スクリプトの入力と出力は **\@in** パラメーターと **\@out** パラメーターで定義されます。 U-SQL スクリプトの **\@in** および **\@out** パラメーターの値は、"parameters" セクションを使用してサービスによって動的に渡されます。 

Azure Data Lake Analytics サービスで実行されるジョブのパイプライン定義で、他のプロパティ (degreeOfParallelism など) や優先度も指定できます。

## <a name="dynamic-parameters"></a>動的パラメーター
パイプライン定義のサンプルでは、in パラメーターと out パラメーターにハード コーディングされた値が割り当てられています。 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

代わりに、動的パラメーターを使用することもできます。 次に例を示します。 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

この場合、入力ファイルは引き続き /datalake/input フォルダーから取得され、出力ファイルは /datalake/output フォルダーに生成されます。 ファイル名は、パイプラインのトリガー時に渡される期間の開始時間に基づいて動的に付けられます。  

## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
