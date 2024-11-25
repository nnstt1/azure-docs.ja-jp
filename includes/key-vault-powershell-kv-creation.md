---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 26e4a9e8ba849bb25bad904934a434e92c633acd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659848"
---
Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) コマンドレットを使用して、前の手順で作成したリソース グループにキー コンテナーを作成します。 いくつかの情報を指定する必要があります。

- キー コンテナー名:数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列

  > [!Important]
  > 各キー コンテナーには一意の名前が必要です。 次の例の \<your-unique-keyvault-name\> は、ご自分のキー コンテナーの名前に置き換えてください。

- リソース グループ名: **myResourceGroup**
- 場所: **EastUS**。

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

このコマンドレットの出力では、新しく作成したキー コンテナーのプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

- **Vault Name**:上の --name パラメーターに指定した名前です。
- **Vault URI (コンテナー URI)** :この例では、これは https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。
