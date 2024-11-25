---
title: クイック スタート:Azure DB for MySQL を作成する - ARM テンプレート
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用して、Azure Database for MySQL サーバーと仮想ネットワーク統合を作成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 05/19/2020
ms.openlocfilehash: 3d2750c607c8fe370122988d3836e00230ef0f21
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423990"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>クイック スタート:ARM テンプレートを使用して Azure Database for MySQL サーバーを作成する

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL は、高可用な MySQL データベースをクラウドで実行し、管理し、スケーリングするために使用されるマネージド サービスです。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Database for MySQL サーバーと仮想ネットワーク統合を作成します。 サーバーの作成には、Azure portal、Azure CLI、Azure PowerShell を使用できます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.dbformysql%2Fmanaged-mysql-with-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* [Azure PowerShell](/powershell/azure/) (コードをローカルで実行したい場合)。

# <a name="cli"></a>[CLI](#tab/CLI)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* [Azure CLI](/cli/azure/) (コードをローカルで実行したい場合)。

---

## <a name="review-the-template"></a>テンプレートを確認する

Azure Database for MySQL サーバーは、定義済みの一連のコンピューティング リソースとストレージ リソースを使って作成します。 詳細については、「[Azure Database for MySQL の価格レベル](concepts-pricing-tiers.md)」を参照してください。 サーバーは、[Azure リソース グループ](../azure-resource-manager/management/overview.md)内に作成します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/managed-mysql-with-vnet/)からのものです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.dbformysql/managed-mysql-with-vnet/azuredeploy.json":::

このテンプレートには、次の 5 つの Azure リソースが定義されています。

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft.DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft.DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

その他の Azure Database for MySQL テンプレートのサンプルについては、[クイックスタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular)を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で Azure Database for MySQL サーバーのテンプレートをデプロイするには、次のリンクを選択します。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.dbformysql%2Fmanaged-mysql-with-vnet%2Fazuredeploy.json)

**[Deploy Azure Database for MySQL with VNet]\(Azure Database for MySQL と VNet のデプロイ\)** ページで、次の作業を行います。

1. **[リソース グループ]** の **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** を選択します。

2. 新しいリソース グループを作成した場合は、リソース グループと新しいサーバーの **場所** を選択します。

3. **サーバー名**、**管理者のログイン**、**管理者のログイン パスワード** を入力します。

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="[Deploy Azure Database for MySQL with VNet]\(Azure Database for MySQL と VNet のデプロイ\) ウィンドウ、Azure クイックスタート テンプレート、Azure portal":::

4. 必要に応じて、他の既定の設定を変更します。

    * **[サブスクリプション]** : サーバーに使用する Azure サブスクリプション。
    * **[SKU 容量]** : 仮想コア容量。*2* (既定)、*4*、*8*、*16*、*32*、*64* のいずれかを指定できます。
    * **[SKU 名]** : SKU レベル プレフィックス、SKU ファミリー、SKU 容量をアンダースコアで結合したもの (例: *B_Gen5_1*、*GP_Gen5_2* (既定)、*MO_Gen5_32*)。
    * **[Sku Size MB]\(SKU サイズ (MB)\)** : Azure Database for MySQL サーバーのメガバイト単位のストレージ サイズ (既定値: *5120*)。
    * **[SKU レベル]** : デプロイ レベル (例: *Basic*、*GeneralPurpose* (既定)、*MemoryOptimized*)。
    * **[SKU ファミリ]** : *Gen4* または *Gen5* (既定)。サーバーのデプロイに使用するハードウェアの世代を指定します。
    * **[MySQL バージョン]** : デプロイする MySQL サーバーのバージョン (例: *5.6* または *5.7* (既定))。
    * **[Backup Retention Days]\(バックアップ保持期間の日数\)** : geo 冗長バックアップの保持期間の日数を指定します (既定値: *7*)。
    * **[Geo Redundant Backup]\(geo 冗長バックアップ\)** : geo ディザスター リカバリー (Geo-DR) の要件に応じて "*有効*" または "*無効*" (既定) を選択します。
    * **[仮想ネットワーク名]** : 仮想ネットワークの名前 (既定値: *azure_mysql_vnet*)。
    * **[サブネット名]** : サブネットの名前 (既定値: *azure_mysql_subnet*)。
    * **[Virtual Network Rule Name]\(仮想ネットワーク規則名\)** : サブネットを許可する仮想ネットワーク規則の名前 (既定値: *AllowSubnet*)。
    * **[Vnet Address Prefix]\(VNet のアドレス プレフィックス\)** : 仮想ネットワークのアドレス プレフィックス (既定値: *10.0.0.0/16*)。
    * **[Subnet Prefix]\(サブネット プレフィックス\)** : サブネットのアドレス プレフィックス (既定値: *10.0.0.0/16*)。

5. 使用条件を読み、 **[上記の使用条件に同意する]** をオンにします。

6. **[購入]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

テンプレートを使用して新しい Azure Database for MySQL サーバーを作成するには、以下の対話型コードを使用します。 このコードを実行すると、新しいサーバー名、新しいリソース グループの名前と場所、管理者のアカウント名とパスワードを入力するよう求められます。

Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.dbformysql/managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

テンプレートを使用して新しい Azure Database for MySQL サーバーを作成するには、以下の対話型コードを使用します。 このコードを実行すると、新しいサーバー名、新しいリソース グループの名前と場所、管理者のアカウント名とパスワードを入力するよう求められます。

Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.dbformysql/managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

新しい Azure Database for MySQL サーバーの概要を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、 **[Azure Database for MySQL サーバー]** を選択します。

2. データベース一覧から新しいサーバーを選択します。 新しい Azure Database for MySQL サーバーの **[概要]** ページが表示されます。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure Database for MySQL サーバーの詳細を確認するには、以下の対話型コードを実行します。 新しいサーバーの名前を入力する必要があります。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure Database for MySQL サーバーの詳細を確認するには、以下の対話型コードを実行します。 新しいサーバーの名前とリソース グループを入力する必要があります。

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="exporting-arm-template-from-the-portal"></a>ポータルから ARM テンプレートをエクスポートする
Azure portal から [ARM テンプレートをエクスポート](../azure-resource-manager/templates/export-template-portal.md)できます。 テンプレートをエクスポートするには、次の 2 とおりの方法があります。

- [リソース グループまたはリソースからのエクスポート](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource)。 このオプションでは、既存のリソースから新しいテンプレートを生成します。 エクスポートされたテンプレートは、リソース グループの現在の状態の "スナップショット" です。 リソース グループ全体、またはそのリソース グループ内の特定のリソースをエクスポートできます。
- [デプロイ前または履歴からのエクスポート](../azure-resource-manager/templates/export-template-portal.md#download-template-before-deployment)。 このオプションでは、デプロイに使用されたテンプレートのそのままのコピーを取得します。

テンプレートをエクスポートすると、MySQL サーバー リソースの ```"properties":{ }``` セクションに、セキュリティ上の理由から ```administratorLogin``` と ```administratorLoginPassword``` が含まれていないことがわかります。 テンプレートをデプロイする前に、これらのパラメーターをテンプレートに追加する **必要があります**。そうしないと、テンプレートは失敗します。

```
"resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除してください。リソース グループを削除すれば、リソース グループ内のリソースが削除されます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を検索して選択します。

2. リソース グループの一覧で、リソース グループの名前を選択します。

3. リソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。

4. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>次のステップ

Resource Manager テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [ チュートリアル: 初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
