---
title: Azure Machine Learning パイプラインを実行する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Machine Learning パイプラインを Azure Data Factory および Synapse Analytics パイプラインで実行する方法について説明します。
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 09/09/2021
ms.openlocfilehash: 0d7fe523b7300634df6c876525b1ffe47a49c205
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806338"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory-and-synapse-analytics"></a>Azure Machine Learning パイプラインを Azure Data Factory および Synapse Analytics で実行する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Machine Learning パイプラインを Azure Data Factory および Synapse Analytics パイプラインのステップとして実行します。 Machine Learning パイプラインの実行アクティビティにより、ローンの債務不履行の可能性の特定、センチメントの判定、顧客行動パターンの分析といったバッチ予測シナリオが可能になります。

次の動画では、この機能の概要とデモを 6 分間で紹介しています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>構文

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | パイプラインのアクティビティの名前。 | String | はい
type | アクティビティの種類は "AzureMLExecutePipeline" です | String | はい
linkedServiceName | Azure Machine Learning に対するリンクされたサービス | リンクされたサービスの参照 | はい
mlPipelineId | 発行された Azure Machine Learning パイプラインの ID | 文字列 (または文字列の resultType を含む式) | はい
experimentName | Machine Learning パイプラインの実行の実行履歴実験名 | 文字列 (または文字列の resultType を含む式) | いいえ
mlPipelineParameters | 発行された Azure Machine Learning パイプライン エンドポイントに渡されるキーと値のペア。 キーは、発行された Machine Learning パイプラインで定義されているパイプラインのパラメーターの名前と一致する必要があります | キーと値のペアが含まれるオブジェクト (resultType オブジェクトの式) | いいえ
mlParentRunId | 親の Azure Machine Learning パイプラインの実行 ID | 文字列 (または文字列の resultType を含む式) | いいえ
dataPathAssignments | Azure Machine Learning でデータパスを変更するために使用される辞書。 データパスの切り替えを有効にします | キーと値のペアが含まれるオブジェクト | いいえ
continueOnStepFailure | ステップが失敗した場合に、Machine Learning パイプライン実行の他のステップの実行を続けるかどうか | boolean | いいえ

> [!NOTE]
> Machine Learning パイプライン名と ID のドロップダウン項目を設定するには、ML パイプラインを一覧表示するためのアクセス許可が必要です。 UI は、ログインしているユーザーの資格情報を使用して、AzureMLService API を直接呼び出します。  

## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。

* [データ フローの実行アクティビティ](control-flow-execute-data-flow-activity.md)
* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
