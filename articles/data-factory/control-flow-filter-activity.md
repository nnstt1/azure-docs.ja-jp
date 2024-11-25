---
title: Filter アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: Filter アクティビティは、入力をフィルター処理して Azure Data Factory および Azure Synapse Analytics パイプラインに入力します。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d5a78ca89841abc1d6f060a2f84b7db5ec3758e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831632"
---
# <a name="filter-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory と Azure Synapse Analytics パイプラインの Filter アクティビティ
パイプラインでフィルター アクティビティを使用して、入力配列にフィルター式を適用することができます。 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="syntax"></a>構文

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | `Filter` アクティビティの名前。 | String | はい
type | **filter** に設定する必要があります。 | String | はい
condition | 入力のフィルター処理に使用する条件。 | Expression | はい
items | フィルターを適用する必要がある入力配列。 | Expression | はい

## <a name="example"></a>例

この例では、パイプラインに **Filter** および **ForEach** という 2 つのアクティビティが含まれています。 フィルター アクティビティは、3 より大きい値を持つ項目の入力配列をフィルター処理するように構成されています。 ForEach アクティビティは、フィルター処理された値を反復処理し、変数 **test** を現在の値に設定します。

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされている他の制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
