---
title:Media Services アカウントの ARM テンプレートAzure Media Services の説明:この記事では、ARM テンプレートを使用してメディア サービス アカウントを作成する方法について説明します。
services: media-services documentationcenter: '' author:IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: quickstart ms.date: 03/23/2021 ms.author: inhenkel ms.custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>クイックスタート: Media Services アカウントの ARM テンプレート

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用してメディア サービス アカウントを作成する方法について説明します。

## <a name="introduction"></a>はじめに

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

ARM テンプレートの使用経験がある読者は、[デプロイのセクション](#deploy-the-template)に進むことができます。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.media%2Fmedia-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

以前に ARM テンプレートをデプロイしたことがない場合は、[Azure ARM テンプレート](../../azure-resource-manager/templates/index.yml)に関するページを読み、[チュートリアル](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)を実行すると役に立ちます。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/media-services-create/)からのものです。

JSON コード フェンスの構文は次のとおりです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.media/media-services-create/azuredeploy.json":::

テンプレートには、次の 3 つの Azure リソースの種類が定義されています。

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): ストレージ アカウントを作成します
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices): Media Services アカウントを作成します

## <a name="set-the-account"></a>アカウントを設定する

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>リソース グループを作成する

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>デプロイ ファイルに変数を割り当てる

便宜上、テンプレート ファイルへのパスを格納する変数を作成します。 この変数により、デプロイのたびにパスを再入力する必要がないため、デプロイ コマンドの実行が簡単になります。

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

メディア サービス アカウント名を入力するように求められます。

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

次のような JSON 応答が表示されます。

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Azure portal で、リソースが作成されたことを確認します。

![作成されたクイックスタート リソース](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを使用する予定がない場合は、リソース グループを削除できます。

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>次のステップ

パラメーター、変数などがあるテンプレートを作成するプロセスに従って ARM テンプレートの使用方法をさらに学習するには、以下を参照してください

> [!div class="nextstepaction"]
> [チュートリアル:初めての ARM テンプレートを作成してデプロイする](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
