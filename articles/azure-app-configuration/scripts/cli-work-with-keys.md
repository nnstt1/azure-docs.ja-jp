---
title: Azure CLI のサンプル スクリプト - Azure App Configuration ストアに格納されているキー/値を操作する
titleSuffix: Azure App Configuration
description: Azure CLI スクリプトを使用して、App Configuration ストアのキー値を作成、表示、更新、および削除します
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 144b324dcde0c0bdcbaf0a64840b56c6c618a19e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441587"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Azure App Configuration ストアに格納されているキー/値を操作する

このサンプル スクリプトは、次の方法を示しています。
* 新しいキーと値のペアを作成する
* 既存のすべてのキーと値のペアを一覧表示する
* 新しく作成されたキーの値を更新する
* 新しいキーと値のペアを削除する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。
## <a name="sample-script"></a>サンプル スクリプト

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set-keyvault  --name $appConfigName --key $refKey --secret-identifier $uri

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set-keyvault --name $appConfigName --key $refKey --secret-identifier $uri2

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

次の表に、サンプル スクリプトに使用されているコマンドの一覧を示します。 

| command | Notes |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az_appconfig_kv_set) | キーと値のペアを作成または更新します。 |
| [az appconfig kv list](/cli/azure/appconfig/kv#az_appconfig_kv_list) | App Configuration ストア内のキーと値のペアを一覧表示します。 |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az_appconfig_kv_delete) | キーと値のペアを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Configuration の CLI サンプル スクリプトは、[Azure App Configuration の CLI サンプル](../cli-samples.md)のページにあります。
