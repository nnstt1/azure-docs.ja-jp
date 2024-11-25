---
title: Azure クイックスタート - Azure Resource Manager テンプレートを使用して Azure キー コンテナーとキーを作成する | Microsoft Docs
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure キー コンテナーを作成し、コンテナーにキーを追加する方法について説明するクイックスタート。
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs, devx-track-azurepowershell
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 8e8a0e931a2c33883bf18ad92a367110d9be093e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572965"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure キー コンテナーとキーを作成する 

[Azure Key Vault](../general/overview.md) は、キー、パスワード、証明書、その他のシークレットなど、シークレットのための安全な保管場所を提供するクラウド サービスです。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイしてキー コンテナーとキーを作成する過程を中心に取り上げます。


## <a name="prerequisites"></a>前提条件

この記事を完了するには:

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- ユーザーには、Azure の組み込みロールが割り当てられている必要があります (**共同作成者** ロールが推奨)。 [詳細については、こちらを参照してください](../../role-based-access-control/role-assignments-portal.md)
- Azure AD ユーザーオブジェクト ID は、
テンプレートによるアクセス許可の設定で必要です。 次の手順を使用してオブジェクト ID (GUID) を取得します。

    1. **[使ってみる]** を選択し、シェル ウィンドウにスクリプトを貼り付けて、次の Azure PowerShell または Azure CLI コマンドを実行します。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. オブジェクト ID を書き留めます。 このクイック スタートの次のセクションで必要になります。

## <a name="review-the-template"></a>テンプレートを確認する

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

テンプレートでは、2 つのリソースが定義されています。

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

その他の Azure Key Vault テンプレートのサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)のページから入手できます。

## <a name="parameters-and-definitions"></a>パラメーターと定義

|パラメーター  |定義  |
|---------|---------|
|**keyOps**  | 実行できる操作をキーを使用して指定します。 このパラメーターを指定しない場合、すべての操作を実行できます。 このパラメーターの値には、[JSON Web Key (JWK) 仕様](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)で定義されたキー操作のリストをコンマ区切りで指定できます。 <br> `["sign", "verify", "encrypt", "decrypt", " wrapKey", "unwrapKey"]` |
|**CurveName**  |  EC キー タイプに使用される楕円曲線の名前。 「[JsonWebKeyCurveName](/rest/api/keyvault/createkey/createkey#jsonwebkeycurvename)」を参照してください |
|**Kty**  |  作成するキーの種類。 有効な値については、「[JsonWebKeyType](/rest/api/keyvault/createkey/createkey#jsonwebkeytype)」を参照してください。 |
|**タグ** | キーと値のペアの形式による、アプリケーション固有のメタデータ。  |
|**nbf**  |  キーが使用できるようになる日時を DateTime オブジェクトとして指定します。 形式は Unix タイム スタンプになります (UTC の 1970 年 1 月 1 日の Unix エポックを起点とする秒数)。  |
|**exp**  |  有効期限を DateTime オブジェクトとして指定します。 形式は Unix タイム スタンプになります (UTC の 1970 年 1 月 1 日の Unix エポックを起点とする秒数)。 |

## <a name="deploy-the-template"></a>テンプレートのデプロイ
[Azure portal](../../azure-resource-manager/templates/deploy-portal.md)、Azure PowerShell、Azure CLI、または REST API を使用できます。 デプロイ方法の詳細については、「[テンプレートのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用し、キー コンテナーとキーを確認するか、次の Azure CLI または Azure PowerShell スクリプトを使用し、作成されたキーを一覧表示できます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のクイック スタートとチュートリアルは、このクイック スタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、Key Vault と関連リソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次を実行します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用してキー コンテナーとキーを作成し、デプロイを検証しました。 Key Vault と Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) の詳細を確認する
- [Key Vault のセキュリティの概要](../general/security-features.md)を確認する
