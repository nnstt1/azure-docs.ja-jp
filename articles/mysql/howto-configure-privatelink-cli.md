---
title: プライベート リンク - Azure CLI - Azure Database for MySQL
description: Azure CLI から Azure Database for MySQL 用のプライベート リンクを構成する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 01/09/2020
ms.openlocfilehash: 0f0910f72685108db28cbbc43daa30610e584370
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842095"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>CLI を使用して Azure Database for MySQL 用のプライベート リンクを作成および管理する

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 この記事では、Azure CLI を使用して Azure Virtual Network 内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for MySQL サーバーを作成する方法について説明します。

> [!NOTE]
> プライベート リンク機能は、General Purpose またはメモリ最適化の価格レベルの Azure Database for MySQL サーバーにのみ使用可能です。 データベース サーバーがこれらの価格レベルのいずれであることを確実にします。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソースを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*westeurope* の場所に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します

[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 この例では、*mySubnet* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>サブネットのプライベート エンドポイント ポリシーを無効にする

Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントの[ネットワーク ポリシー](../private-link/disable-private-endpoint-network-policy.md)を無効にするようにサブネットを作成または更新する必要があります。 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使用して  *mySubnet*  という名前のサブネット構成を更新します。

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```

## <a name="create-the-vm"></a>VM の作成

az vm create を使用して VM を作成します。 メッセージが表示されたら、VM のサインイン資格情報として使用するパスワードを入力します。 この例では、*myVm* という名前の VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

> [!Note]
> VM のパブリック IP アドレス。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成

az mysql server create コマンドを使用して Azure Database for MySQL を作成します。 MySQL サーバーの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダー値を独自の一意の値に置き換えることを忘れないでください。

```azurecli-interactive
# Create a server in the resource group 

az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> Azure Database for MySQL と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
>
> - 両方のサブスクリプションに **Microsoft.DBforMySQL** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する

Virtual Network 内に MySQL サーバーのプライベート エンドポイントを作成します。 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMySQL/servers" --query "id" -o tsv) \    
    --group-id mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

MySQL サーバー ドメイン用のプライベート DNS ゾーンを作成し、Virtual Network との関連付けリンクを作成します。

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

# Query for the network interface ID  
$networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)

az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 

# Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE]
> お客様の DNS 設定の FQDN は、構成されている非公開 IP では解決されません。 [こちら](../dns/dns-operations-recordsets-portal.md)で示すように、構成された FQDN の DNS ゾーンを設定する必要があります。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. *downloaded.rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>VM から MySQL サーバーにプライベートにアクセスする

1.  *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`」と入力します。 

    次のようなメッセージが返されます。

    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. 利用可能な任意のクライアントを使用して、MySQL サーバーのプライベート リンク接続をテストします。 次の例では、[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) を使用して操作を行いました。

4. **[新しい接続]** で、この情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 接続名| ご自身で選んだ接続の名前を選択します。|
    | hostname | *mydemoserver.privatelink.mysql.database.azure.com* を選択します。 |
    | ユーザー名 | MySQL サーバーの作成時に指定したユーザー名を *username@servername* 形式で入力します。 |
    | Password | MySQL サーバーの作成時に指定したパスワードを入力します。 |
    ||

5. [接続] を選択します。

6. 左側のメニューでデータベースを参照します。

7. (省略可能) MySQL データベースから情報を作成または照会します。

8. myVm へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、az group delete を使用して、リソース グループとそのすべてのリソースを削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>次のステップ

- [Azure プライベート エンドポイントの概要](../private-link/private-endpoint-overview.md)について学習します。

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
