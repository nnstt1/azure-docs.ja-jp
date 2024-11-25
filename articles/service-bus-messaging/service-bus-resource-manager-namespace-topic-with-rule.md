---
title: Azure テンプレートを使用して Service Bus のトピック、サブスクリプション、およびルールを作成する
description: トピック、サブスクリプション、ルールを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成する
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 09/27/2021
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d1a9d6d910b9a74630d1f4c0693f0f87bc15a730
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131055"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>トピック、サブスクリプション、ルールを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成します。

この記事では、Azure Resource Manager テンプレートを使用して、トピック、サブスクリプション、ルール (フィルター) を含んだ Service Bus の名前空間を作成する方法について説明します。 この記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを指定する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「 [Azure Resource Manager のテンプレートの作成][Authoring Azure Resource Manager templates]」を参照してください。

Azure リソースの名前付け規則のプラクティスとパターンについて詳しくは、「[Azure リソースの推奨される名前付け規則][Recommended naming conventions for Azure resources]」をご覧ください。

完全なテンプレートについては、[Service Bus の名前空間にトピック、サブスクリプション、ルールを追加する][Service Bus namespace with topic, subscription, and rule]テンプレートを参照してください。

> [!NOTE]
> 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。
>
> * [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
> * [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
> * [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
>
> 最新のテンプレートを確認する場合は、「 [Azure クイックスタート テンプレート][Azure Quickstart Templates] 」ギャラリーで "Service Bus" を検索してください。

## <a name="what-do-you-deploy"></a>デプロイ対象

このテンプレートでデプロイされるのは、トピック、サブスクリプション、ルール (フィルター) を含んだ Service Bus 名前空間です。

[Service Bus のトピックとサブスクリプション](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)は、"*発行とサブスクライブ*" のパターンで一対多の形式の通信を実現します。 トピックとサブスクリプションを使用しているとき、分散アプリケーションのコンポーネントは互いに直接通信するのではなく、仲介者として機能するトピックを介してメッセージをやり取りします。トピックのサブスクリプションとは、トピックに送信されたメッセージのコピーを受け取るキューと実質的には考えることができます。 サブスクリプションにフィルターを設定すると、トピックに送信されたメッセージのうち、その特定のサブスクリプションで取得できるメッセージを指定することができます。

## <a name="what-are-rules-filters"></a>ルール (フィルター) とは

多くのシナリオでは、特性のあるメッセージは、異なる方法で処理する必要があります。 このカスタム プロセスを有効にするために、特定のプロパティを持つメッセージを検索し、該当するプロパティに変更を行うようにサブスクリプションを構成できます。 Service Bus のサブスクリプションには、トピックに送信されたすべてのメッセージが表示されますが、仮想サブスクリプション キューにコピーできるのは、これらのメッセージの一部のみです。 これを行うには、サブスクリプション フィルターを使用します。 ルール (フィルター) の詳細については、「[Rules and actions](service-bus-queues-topics-subscriptions.md#rules-and-actions)」を参照してください。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター

Azure Resource Manager を使用して、テンプレートのデプロイ時に指定する値用のパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 これらの値用のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトあるいはデプロイ先の環境によって異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

作成する Service Bus 名前空間の名前。

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Service Bus 名前空間に作成するトピックの名前。

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Service Bus 名前空間に作成するサブスクリプションの名前。

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Service Bus 名前空間に作成するルール (フィルター) の名前。

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

テンプレートの Service Bus API バージョン。

```json
"serviceBusApiVersion": {
       "type": "string",
       "defaultValue": "2017-04-01",
       "metadata": {
           "description": "Service Bus ApiVersion used by the template"
       }
```

## <a name="resources-to-deploy"></a>デプロイ対象のリソース

**Messaging** タイプの標準的な Service Bus 名前空間を作成し、トピックとサブスクリプションとルールを追加します。

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

JSON の構文とプロパティについては、[namespaces](/azure/templates/microsoft.servicebus/namespaces)、[topics](/azure/templates/microsoft.servicebus/namespaces/topics)、[subscriptions](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)、および [rules](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules) を参照してください。

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az deployment group create -g \<my-resource-group\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>次のステップ

次の記事を参照して、これらのリソースの管理方法を確認してください。

* [Azure Service Bus を管理する](service-bus-management-libraries.md)
* [PowerShell で Service Bus を管理する](service-bus-manage-with-ps.md)
* [Service Bus リソースを Service Bus Explorer で管理する](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/resources/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.servicebus/servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
