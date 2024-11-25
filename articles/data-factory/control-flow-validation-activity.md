---
title: Validation アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Synapse Analytics の Validation アクティビティは、ユーザーが定義した条件でデータセットが研修されるまでパイプラインの実行を遅らせます。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: fa98ef27b5dbcc7949f37bf548c414d015224e4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750618"
---
# <a name="validation-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory と Azure Synapse Analytics パイプラインの Validation アクティビティ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

パイプラインで検証を使用すると、アタッチされたデータセット参照が存在していて、指定された条件を満たしていること、またはタイムアウトに達していることを検証した後でのみ、パイプラインが実行を継続することを確認できます。


## <a name="syntax"></a>構文

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | '検証' アクティビティの名前 | String | はい |
type | **Validation** に設定する必要があります。 | String | はい |
dataset | アクティビティは、このデータセット参照が存在していて指定された条件を満たしていること、またはタイムアウトに達していることを検証するまで、実行をブロックします。 提供するデータセットは、"MinimumSize" または "ChildItems" プロパティをサポートする必要があります。 | データセット参照 | はい |
timeout | アクティビティの実行に関するタイムアウトを指定します。 値が指定されていない場合は、既定値の 7 日 ("7.00:00:00") が使用されます。 形式は d.hh:mm:ss です | String | いいえ |
sleep | 検証の試行間の遅延 (秒単位)。 値が指定されていない場合は、既定値の 10 秒が使用されます。 | Integer | いいえ |
childItems | フォルダーに子項目があるかどうかチェックします。 true に設定すると、フォルダーが存在していること、およびそこに項目があることが検証されます。 フォルダー内に少なくとも 1 つの項目が存在するまで、またはタイムアウト値に達するまでブロックします。false に設定すると、フォルダーが存在し、それが空であることが検証されます。 フォルダーが空になるまで、またはタイムアウト値に達するまで、ブロックします。 値が指定されていない場合、フォルダーが存在するかタイムアウトに達するまで、アクティビティはブロックされます。 | ブール型 | いいえ |
minimumSize | ファイルの最小サイズ (バイト単位)。 値が指定されていない場合は、既定値の 0 バイトが使用されます | Integer | いいえ |


## <a name="next-steps"></a>次のステップ
サポートされている他の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
