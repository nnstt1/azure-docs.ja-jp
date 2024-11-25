---
title: テンプレート スペックの作成とデプロイ
description: テンプレート スペックを作成し、組織内の他のユーザーと共有する方法について説明します。
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
author: tfitzmac
ms.openlocfilehash: 8d8b582cdae8b387774402869eccf903a1b394b2
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613436"
---
# <a name="azure-resource-manager-template-specs"></a>Azure Resource Manager テンプレート スペック

テンプレート スペックは、後でデプロイするために Azure に Azure Resource Manager テンプレート (ARM テンプレート) を格納するためのリソースの種類です。 このリソースの種類を使用すると、ARM テンプレートを組織内の他のユーザーと共有できます。 他の Azure リソースと同じように、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してテンプレート スペックを共有できます。

**Microsoft.Resources/templateSpecs** は、テンプレート スペックのリソースの種類です。 これは、メイン テンプレートと、リンクされた任意の数のテンプレートで構成されます。 Azure によって、テンプレート スペックはリソース グループに安全に格納されます。 Template Specs では、[バージョン管理](#versioning)がサポートされています。

テンプレート スペックをデプロイするには、PowerShell、Azure CLI、Azure portal、REST およびその他のサポートされている SDK およびクライアントなど、標準の Azure ツールを使用します。 テンプレートの場合と同じコマンドを使用します。

> [!NOTE]
> Azure PowerShell でテンプレート スペックを使用するには、[バージョン 5.0.0 以降](/powershell/azure/install-az-ps)をインストールする必要があります。 Azure CLI でこれを使用するには、[バージョン 2.14.2 以降](/cli/azure/install-azure-cli)を使用します。

デプロイを計画するときは常に、リソースのライフサイクルを考慮し、ライフサイクルが同じようなリソースを 1 つのテンプレート仕様にグループ化します。たとえば、デプロイに複数の Cosmos DB インスタンスが含まれ、各インスタンスにそれ自体のデータベースとコンテナーが含まれています。 データベースとコンテナーにほとんど変化がないときは、Cosmo DB とその基礎となるデータベースとコンテナーを含めるテンプレート仕様を 1 つ作成します。 次に、それらのリソースのインスタンスを複数作成するコピー ループと共に、条件付きステートメントをテンプレートの中で使用できます。

### <a name="microsoft-learn"></a>Microsoft Learn

テンプレート スペックの詳細とハンズオン ガイダンスについては、**Microsoft Learn** の [テンプレート スペックを使用した再利用可能なインフラストラクチャ コードのライブラリの発行](/learn/modules/arm-template-specs)に関するページを参照してください。

## <a name="why-use-template-specs"></a>テンプレート スペックを使用する理由は何ですか。

テンプレート スペックには、次のような利点があります。

* テンプレート スペック用に標準 ARM テンプレートを使用する。
* SAS トークンではなく、Azure RBAC を使用してアクセスを管理する。
* ユーザーは、テンプレートへの書き込みアクセス権がなくてもテンプレート スペックをデプロイできる。
* テンプレート スペックは、PowerShell スクリプトや DevOps パイプラインなどの既存のデプロイ プロセスに統合できる。

テンプレート スペックを使用すると、正規のテンプレートを作成し、それらを組織内のチームと共有できます。 テンプレート スペックは、デプロイのために Azure Resource Manager で使用できますが、正しいアクセス許可がないユーザーはアクセスできないため、安全です。 ユーザーがテンプレートをデプロイするのに必要なのは、そのテンプレート スペックへの読み取りアクセス権のみです。そのため、他のユーザーに変更を許可することなくテンプレートを共有できます。

現在、GitHub リポジトリまたはストレージ アカウントにテンプレートがある場合に、テンプレートを共有して使用しようとすると、いくつかの問題が発生します。 テンプレートをデプロイするには、テンプレートへのパブリック アクセスを許可するか、SAS トークンを使用してアクセスを管理する必要があります。 この制限に対処するために、ユーザーはローカル コピーを作成する場合があります。これは、最終的に元のテンプレートとは異なったものになります。 テンプレート スペックにより、テンプレートの共有が簡単になります。

テンプレート スペックに含まれるテンプレートは、組織の要件とガイダンスに従うように組織の管理者が検証する必要があります。

## <a name="create-template-spec"></a>テンプレート スペックを作成する

次の例は、Azure でストレージ アカウントを作成するための簡単なテンプレートを示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

テンプレート スペックを作成するときは、PowerShell コマンドまたは CLI コマンドにメイン テンプレート ファイルが渡されます。 メイン テンプレートがリンクされたテンプレートを参照している場合は、コマンドでそれらのテンプレートが検索されてパッケージ化され、テンプレート スペックが作成されます。詳細については、「[リンクされたテンプレートを使用してテンプレート スペックを作成する](#create-a-template-spec-with-linked-templates)」を参照してください。

テンプレート スペックを作成するには、次のコマンドを使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

サブスクリプション内のすべてのテンプレート スペックを表示するには、次のコマンドを使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

テンプレート スペックの詳細 (そのバージョンを含む) を表示するには、次のコマンドを使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>テンプレート スペックをデプロイする

テンプレート スペックを作成すると、そのテンプレート スペックへの **読み取り** アクセス権を持つユーザーは、それをデプロイできます。 アクセスの許可については、「[チュートリアル: Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)」を参照してください。

Template Specs は、ポータル、PowerShell、Azure CLI を通じて、または大規模なテンプレートのデプロイでリンクされたテンプレートとしてデプロイできます。 組織内のユーザーは、Azure 内の任意のスコープ (リソース グループ、サブスクリプション、管理グループ、またはテナント) にテンプレート スペックをデプロイできます。

テンプレートのパスまたは URI を渡す代わりに、リソース ID を指定してテンプレート スペックをデプロイします。 リソース ID の形式は次のとおりです。

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

リソース ID にテンプレート スペックのバージョン名が含まれていることに注目してください。

たとえば、次のコマンドを使用してテンプレート スペックをデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

実際には、通常は `Get-AzTemplateSpec` または `az ts show` を実行して、デプロイするテンプレート スペックの ID を取得します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

また、次の形式で URL を開いて、テンプレート仕様をデプロイすることもできます。

```url
https://portal.azure.com/#create/Microsoft.Template/templateSpecVersionId/%2fsubscriptions%2f{subscription-id}%2fresourceGroups%2f{resource-group-name}%2fproviders%2fMicrosoft.Resources%2ftemplateSpecs%2f{template-spec-name}%2fversions%2f{template-spec-version}
```

## <a name="parameters"></a>パラメーター

テンプレート スペックにパラメーターを渡すことは、ARM テンプレートにパラメーターを渡すこととまったく同じです。 パラメーターの値は、インラインで、またはパラメーター ファイルに追加します。

パラメーターをインラインで渡すには、以下を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

ローカル パラメーター ファイルを作成するには、以下を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

また、そのパラメーター ファイルを以下を使用して渡します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>バージョン管理

テンプレート スペックを作成するときは、そのバージョン名を指定します。 テンプレート コードを反復処理するときは、既存のバージョンを更新する (修正プログラムの場合) か、または新しいバージョンを発行することができます。 バージョンはテキスト文字列です。 セマンティック バージョン管理など、任意のバージョン管理システムに従うことを選択できます。 テンプレート スペックのユーザーは、デプロイ時に使用するバージョン名を指定できます。

## <a name="use-tags"></a>タグを使用する

[タグ](../management/tag-resources.md)は、リソースを論理的に整理するために役立ちます。 Azure PowerShell と Azure CLI を使用して、テンプレート スペックにタグを追加できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

テンプレート スペックを作成または変更するときに、version パラメーターは指定するが、tag/tags パラメーターは指定しない場合:

- テンプレート スペックが存在し、タグはあるがバージョンが存在しない場合、新しいバージョンでは既存のテンプレート スペックと同じタグが継承されます。

テンプレート スペックを作成または変更するときに、tag/tags パラメーターと version パラメーターの両方を指定する場合:

- テンプレート スペックとバージョンの両方が存在しない場合、新しいテンプレート スペックと新しいバージョンの両方にタグが追加されます。
- テンプレート スペックは存在するが、バージョンが存在しない場合、タグは新しいバージョンにのみ追加されます。
- テンプレート スペックとバージョンの両方が存在する場合、タグはバージョンにのみ適用されます。

テンプレートを変更するときに、tag/tags パラメーターは指定するが、version パラメーターは指定しない場合、タグはテンプレート スペックにのみ追加されます。

## <a name="create-a-template-spec-with-linked-templates"></a>リンクされたテンプレートを使用してテンプレート スペックを作成する

テンプレート スペックのメイン テンプレートがリンクされたテンプレートを参照している場合は、PowerShell コマンドと CLI コマンドで、リンクされたテンプレートをローカル ドライブから自動的に検索してパッケージ化することができます。 テンプレート スペックをホストするために、ストレージ アカウントまたはリポジトリを手動で構成する必要はありません。テンプレート スペック リソースにすべて含まれています。

次の例は、メイン テンプレートと、リンクされた 2 つのテンプレートで構成されています。 この例は、テンプレートの抜粋にすぎません。 `relativePath` という名前のプロパティを使用して、他のテンプレートにリンクしていることに注目してください。 デプロイ リソースには、`2020-06-01` 以降の `apiVersion` を使用する必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

前の例で、テンプレート スペックを作成する PowerShell コマンドまたは CLI コマンドを実行すると、メイン テンプレート、Web アプリ テンプレート (`webapp.json`)、およびデータベース テンプレート (`database.json`) の 3 つのファイルが検索され、テンプレート スペックにパッケージ化されます。

詳細については、[リンクされたテンプレートを使用してテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。

## <a name="deploy-template-spec-as-a-linked-template"></a>テンプレート スペックをリンクされたテンプレートとしてデプロイする

テンプレート スペックを作成すると、ARM テンプレートや別のテンプレート スペックから簡単に再利用できます。テンプレートにリソース ID を追加して、テンプレート スペックにリンクします。 リンクされたテンプレート スペックは、メイン テンプレートのデプロイ時に自動的にデプロイされます。 この動作により、モジュール テンプレート スペックを開発し、必要に応じて再利用することができます。

たとえば、ネットワーク リソースをデプロイするテンプレート スペックと、ストレージ リソースをデプロイする別のテンプレート スペックを作成することができます。 ARM テンプレートでは、ネットワーク リソースまたはストレージ リソースを構成する必要があるときはいつでも、これらの 2 つのテンプレート スペックにリンクします。

次の例は、前の例と似ていますが、ローカル テンプレートにリンクする `relativePath` プロパティではなく、テンプレート スペックにリンクする `id` プロパティを使用します。 デプロイ リソースの API バージョンには `2020-06-01` を使用します。 この例では、テンプレート スペックは **templateSpecsRG** という名前のリソース グループにあります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

テンプレート スペックのリンクの詳細については、「[チュートリアル: テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* テンプレート スペックを作成してデプロイするには、「[クイックスタート: テンプレート スペックの作成とデプロイ](quickstart-create-template-specs.md)」を参照してください。

* テンプレート スペックでテンプレートをリンクする方法の詳細については、「[チュートリアル: リンクされたテンプレートを使用してテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。

* テンプレート スペックをリンクされたテンプレートとしてデプロイする方法の詳細については、「[チュートリアル: テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。
