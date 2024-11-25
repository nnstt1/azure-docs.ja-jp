---
title: Azure CLI を使用した Azure デプロイ テンプレート– Azure Resource Manager |Microsoft Docs
description: Azure Resource Manager と Azure CLI を使用してリソース グループを作成し、Azure にデプロイします。 リソースは Azure デプロイ テンプレートで定義されます。
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: azure cli デプロイ arm テンプレート, リソースグループの作成 azure, azure デプロイ テンプレート, デプロイ リソース, arm テンプレート, azure arm テンプレート
ms.openlocfilehash: 5b7734e3b91f7e842f17888f3f3c67d05655fc97
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585016"
---
# <a name="how-to-use-azure-resource-manager-arm-deployment-templates-with-azure-cli"></a>Azure CLI で Azure Resource Manager (ARM) のデプロイ テンプレートを使用する方法

この記事では、Azure CLI と Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure にリソースをデプロイする方法について説明します。  Azure ソリューションのデプロイと管理の概念について詳しくない場合、「[テンプレートのデプロイの概要](overview.md)」をご覧ください。

デプロイ コマンドは Azure CLI バージョン 2.2.0 で変更されました。 この記事の例には、[Azure CLI バージョン 2.20.0 以降](/cli/azure/install-azure-cli)が必要です。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Azure CLI がインストールされていない場合は、Azure Cloud Shell を使用できます。 詳細については、「[Azure Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)」を参照してください。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、サブスクリプション、管理グループ、またはテナントを Azure デプロイのターゲットにすることができます。 使用するコマンドは、デプロイのスコープに応じて異なります。

* **リソース グループ** にデプロイするには、[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用します。

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* **サブスクリプション** にデプロイするには、[az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) を使用します。

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

* **管理グループ** にデプロイするには、[az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create) を使用します。

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

* **テナント** にデプロイするには、[az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create) を使用します。

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

各スコープに対して、テンプレートをデプロイするユーザーにはリソースを作成するためのアクセス許可が必要です。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

ローカル コンピューターからの ARM テンプレートまたは外部に格納されているものをデプロイできます。 ここでは、ローカル テンプレートのデプロイについて説明します。

存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 名前の末尾をピリオドにすることはできません。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

ローカル テンプレートをデプロイするには、デプロイ コマンドで `--template-file` パラメーターを使用します。 次の例では、パラメーター値を設定する方法も示しています。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template> \
  --parameters storageAccountType=Standard_GRS
```

Azure デプロイ テンプレートが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

ARM テンプレートをローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 名前の末尾をピリオドにすることはできません。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

外部テンプレートをデプロイするには、`template-uri` パラメーターを使用します。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートへのアクセスを管理する場合は、[テンプレート スペック](#deploy-template-spec)を使用することを検討してください。

離れた場所にあり、リンクされているテンプレートを、ストレージ アカウントに格納されている相対パスでデプロイするには、`query-string` を使用して SAS トークンを指定します。

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

詳細については、[リンクされているテンプレートの相対パスを使用する](./linked-templates.md#linked-template)方法に関するページを参照してください。

## <a name="azure-deployment-template-name"></a>Azure デプロイ テンプレート名

ARM テンプレートをデプロイするときに、Azure デプロイ テンプレートに名前を付けることができます。 この名前は、デプロイ履歴からデプロイを取得するのに役立ちます。 デプロイの名前を指定しない場合は、テンプレート ファイルの名前が使用されます。 たとえば、_azuredeploy.json_ という名前のテンプレートをデプロイするときにデプロイ名を指定しない場合、デプロイの名前は `azuredeploy` になります。

デプロイを実行するたびに、リソース グループのデプロイ履歴にデプロイ名のエントリが追加されます。 別のデプロイを実行するときに同じ名前を付けると、現在のデプロイによって前のエントリが置き換えられます。 デプロイ履歴に一意のエントリを保持する場合は、デプロイごとに一意の名前を付けます。

一意の名前を作成するために、ランダムな数値を割り当てることができます。

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

または、日付の値を追加します。

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

同じリソース グループに対して同じ名前のデプロイを同時に実行した場合は、最後のデプロイのみが完了します。 完了していない同じ名前のデプロイは、最後のデプロイによって置き換えられます。 たとえば、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを同時に実行した場合は、1 つのストレージ アカウントのみがデプロイされます。 結果のストレージ アカウントの名前は `storage2` になります。

ただし、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、その完了直後に、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを実行した場合は、ストレージ アカウントは 2 つになります。 1 つは `storage1` という名前に、もう 1 つは `storage2` という名前になります。 ただし、デプロイ履歴にはエントリが 1 つだけ存在します。

デプロイごとに一意の名前を指定すると、競合なしでそれらを同時に実行できます。 `storage1` という名前のストレージ アカウントをデプロイする `newStorage1` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage2` という名前の別のデプロイを同時に実行した場合は、2 つのストレージ アカウントがデプロイされ、デプロイ履歴には 2 つのエントリが存在します。

同時デプロイによる競合を回避し、デプロイ履歴に一意のエントリが確実に存在するようにするには、各デプロイに一意の名前を付けます。

## <a name="deploy-template-spec"></a>テンプレート スペックのデプロイ

ローカルまたはリモートのテンプレートをデプロイする代わりに、[テンプレート スペック](template-specs.md)を作成できます。テンプレート スペックは、ARM テンプレートが含まれる Azure サブスクリプションのリソースです。 これにより、組織内のユーザーとテンプレートを安全に共有することが容易になります。 テンプレート スペックへのアクセス権を付与するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。現在、この機能はプレビュー段階にあります。

次の例では、テンプレート スペックの作成とデプロイの方法を示します。

まず、ARM テンプレートを指定して、テンプレート スペックを作成します。

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

次に、テンプレート スペックの ID を取得して、デプロイします。

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

詳細については、「[Azure Resource Manager テンプレート スペック](template-specs.md)」を参照してください。

## <a name="preview-changes"></a>変更のプレビュー

ARM テンプレートをデプロイする前に、テンプレートが環境に与える変更をプレビューすることができます。 [what-if 操作](./deploy-what-if.md)を使用して、テンプレートによって必要な変更が行われるかどうかを確認します。 What-if はまた、テンプレートのエラーも検証します。

## <a name="parameters"></a>パラメーター

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`parameters` に値を指定します。 たとえば、Bash シェルで文字列と配列をテンプレートに渡すには、以下を使用します。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Windows コマンド プロンプト (CMD) または PowerShell で Azure CLI を使用している場合は、`exampleArray="['value1','value2']"` という形式で配列を渡します。

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../../virtual-machines/linux/using-cloud-init.md)を指定できます。

_arrayContent.json_ 形式は次のようになります。

```json
[
    "value1",
    "value2"
]
```

タグの設定などを目的にオブジェクトを渡すには、JSON を使用します。 たとえば、テンプレートには、次のようなパラメーターを含めることができます。

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

この場合は、次の Bash スクリプトに示すように、JSON 文字列を渡してパラメーターを設定できます。

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

オブジェクトに渡す JSON を二重引用符で囲みます。

変数を使用してパラメーター値を格納できます。 Bash では、変数をすべてのパラメーター値に設定し、それをデプロイ コマンドに追加します。

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters $params
``` 

しかし、Windows コマンド プロンプト (CMD) または PowerShell で Azure CLI を使用する場合は、変数を JSON 文字列に設定します。 引用符をエスケープします (`$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`)。

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルはローカル ファイルである必要があります。 外部パラメーター ファイルは、Azure CLI ではサポートされていません。

パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](parameter-files.md)」を参照してください。

ローカル パラメーター ファイルを渡すには、`@` を使用して _storage.parameters.json_ という名前のローカル ファイルを指定します。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters '@storage.parameters.json'
```

## <a name="handle-extended-json-format"></a>拡張 JSON 形式を処理する

バージョン 2.3.0 以下の Azure CLI を使用して、複数行の文字列またはコメントを含むテンプレートをデプロイするには、`--handle-extended-json-format` スイッチを使用する必要があります。  次に例を示します。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>次のステップ

* エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
* リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./syntax.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。