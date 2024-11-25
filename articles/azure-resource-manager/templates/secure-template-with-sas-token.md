---
title: SAS トークンを使用して ARM テンプレートをデプロイする - Azure Resource Manager | Microsoft Docs
description: Azure CLI または Azure PowerShell を使用して SAS トークンでプライベート ARM テンプレートを安全にデプロイする方法について説明します。 テンプレートへのアクセスを保護して管理します。
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli, seo-azure-cli
keywords: プライベート テンプレート, SAS トークン テンプレート, ストレージ アカウント, テンプレート セキュリティ, Azure ARM テンプレート, Azure Resource Manager テンプレート
ms.openlocfilehash: 0e6a680e0344ed5a03715c35e99394aead756501
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584944"
---
# <a name="how-to-deploy-private-arm-template-with-sas-token"></a>SAS トークンを使用してプライベート ARM テンプレートをデプロイする方法

Azure Resource Manager テンプレート (ARM テンプレート) がストレージ アカウントに配置されている場合、それが公開されないようにテンプレートへのアクセスを制限できます。 セキュリティで保護されたテンプレートにアクセスするには、そのテンプレート用に SAS (Shared Access Signature) トークンを作成し、デプロイ時にそのトークンを提供します。 この記事では、Azure PowerShell または Azure CLI を使用して SAS トークンで ARM テンプレートを安全にデプロイする方法について説明します。

プライベート ARM テンプレートへのアクセスを保護および管理する方法について、以下を行う方法が記載されています。

* セキュリティで保護されたコンテナーでのストレージ アカウントの作成
* ストレージ アカウントへのテンプレートのアップロード
* デプロイ時に SAS トークンを指定する

> [!IMPORTANT]
> SAS トークンを使用してプライベート テンプレートをセキュリティで保護する代わりに、[テンプレート スペック](template-specs.md)を使用することを検討してください。 テンプレート スペックを使用すると、組織内の他のユーザーとテンプレートを共有し、Azure RBAC 経由でテンプレートへのアクセスを管理できます。

## <a name="create-storage-account-with-secured-container"></a>セキュリティで保護されたコンテナーでのストレージ アカウントの作成

次のスクリプトでは、テンプレートのセキュリティを確保するためにパブリック アクセスがオフになっているストレージ アカウントとコンテナーを作成できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-private-template-to-storage-account"></a>ストレージ アカウントにプライベートのテンプレートをアップロードする

これで、お使いのテンプレートをストレージ アカウントにアップロードする準備ができました。 使用するテンプレートへのパスを指定します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する

ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを生成してテンプレートの URI に含めます。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。

> [!IMPORTANT]
> プライベート テンプレートを含む BLOB は、アカウント オーナーのみがアクセスできます。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンはお使いのテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の例は、Cloud Shell の Bash 環境で動作します。 その他の環境では、SAS トークンの有効期限を作成するために異なる構文が必要になる場合があります。

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](linked-templates.md)」を参照してください。


## <a name="next-steps"></a>次のステップ
* テンプレートのデプロイ方法については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](./syntax.md#parameters)に関する記事を参照してください。