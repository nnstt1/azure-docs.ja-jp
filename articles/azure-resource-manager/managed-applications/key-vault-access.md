---
title: マネージド アプリのデプロイ時に Key Vault を使用する
description: Managed Applications のデプロイ時に Azure Key Vault でアクセス シークレットを使用する方法を示します。
author: tfitzmac
ms.custom: subject-rbac-steps
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: tomfitz
ms.openlocfilehash: d5a6dc1de2ee574a69b8dab746a24bcf82b44d64
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253167"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../../key-vault/general/overview.md) からその値を取得できます。 Managed Applications のデプロイ時に Key Vault にアクセスするには、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 マネージド アプリケーション サービスでは、この ID を使用して操作が実行されます。 デプロイ時にキー コンテナーから正常に値を取得するには、サービス プリンシパルでキー コンテナーにアクセスできる必要があります。

この記事では、Managed Applications を使用するように Key Vault を構成する方法について説明します。

## <a name="enable-template-deployment"></a>テンプレートのデプロイを有効にする

1. ポータルで Key Vault を選択します。

1. **[アクセス ポリシー]** を選択します。   

   ![[アクセス ポリシー] の選択](./media/key-vault-access/select-access-policies.png)

1. **[クリックして高度なアクセス ポリシーを表示する]** を選択します。

   ![高度なアクセス ポリシーの表示](./media/key-vault-access/advanced.png)

1. **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** を選択します。 次に、 **[保存]** を選択します。

   ![テンプレートのデプロイを有効にする](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>サービスを共同作成者として追加する

**共同作成者** ロールを、キー コンテナー スコープで **アプライアンス リソース プロバイダー** ユーザーに割り当てます。

詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="reference-key-vault-secret"></a>Key Vault シークレットを参照する

マネージド アプリケーションで Key Vault からテンプレートにシークレットを渡すには、[リンクされたテンプレートまたは入れ子になったテンプレート](../templates/linked-templates.md)を使用して、そのテンプレートのパラメーター内の Key Vault を参照する必要があります。 Key Vault のリソース ID とシークレットの名前を指定してください。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>次のステップ

マネージド アプリケーションのデプロイ時にアクセスできるように Key Vault を構成しました。

* Key Vault からテンプレート パラメーターとして値を渡す方法については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../templates/key-vault-parameter.md)」を参照してください。
* マネージド アプリケーションの例については、[Azure マネージド アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
* マネージド アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
