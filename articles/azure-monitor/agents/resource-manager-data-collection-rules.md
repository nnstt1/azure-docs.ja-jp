---
title: データ収集ルールのための Resource Manager テンプレート サンプル
description: Azure Monitor でデータ収集ルールと仮想マシンとの間に関連付けを作成するためのサンプル Azure Resource Manager テンプレート。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 24bc9b0f92a82354f511f534eb9b0e86d59d336b
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201268"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Azure Monitor のデータ収集ルールのための Resource Manager テンプレート サンプル
この記事には、[データ収集ルール](data-collection-rule-overview.md)と [Azure Monitor エージェント](./azure-monitor-agent-overview.md)との関連付けを作成するサンプル [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/syntax.md)が含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-rule-sample"></a>ルールを作成する (サンプル)

[テンプレートの形式](/azure/templates/microsoft.insights/datacollectionrules)を表示します。

## <a name="create-association-with-azure-vm"></a>Azure VM との関連付けを作成する

次のサンプルによって、Azure 仮想マシンとデータ収集ルールとの間に関連付けが作成されます。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Azure Arc との関連付けを作成する

次のサンプルでは、Azure Arc 対応サーバーとデータ収集ルールとの間に関連付けを作成します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](../resource-manager-samples.md)。
* [Log Analytics の詳細情報](./log-analytics-agent.md)。
* [診断拡張機能の詳細情報](./diagnostics-extension-overview.md)。
