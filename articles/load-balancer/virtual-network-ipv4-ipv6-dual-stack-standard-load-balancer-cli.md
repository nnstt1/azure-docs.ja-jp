---
title: IPv6 デュアル スタック アプリケーションのデプロイ - Standard Load Balancer - CLI
titlesuffix: Azure Virtual Network
description: この記事では、Azure CLI を使用して Azure 仮想ネットワーク内に IPv6 デュアル スタック アプリケーションをデプロイする方法を説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bee159e01cd97e7480f2f00caae5c96ab660e938
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367479"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli"></a>Azure 仮想ネットワーク内に IPv6 デュアル スタック アプリケーションをデプロイする - CLI

この記事では、Azure で Standard Load Balancer を使用してデュアル スタック (IPv4 および IPv6) アプリケーションをデプロイする方法を説明します。Azure 内には、デュアル スタック サブネットを持つデュアル スタック仮想ネットワーク、デュアル (IPv4 および IPv6) フロントエンド構成を持つ Standard ロード バランサー、および NIC を持つ VM が含まれています。NIC は、デュアル IP 構成、デュアル ネットワーク セキュリティ グループのルール、およびデュアル パブリック IP を備えています。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.49 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

デュアル スタック仮想ネットワークを作成する前に、[az group create](/cli/azure/group) でリソース グループを作成する必要があります。 次の例では、*DsResourceGroup01* という名前のリソース グループを *eastus* という場所に作成します。

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>ロード バランサーの IPv4 および IPv6 パブリック IP アドレスの作成
インターネット上の IPv4 および IPv6 エンドポイントにアクセスするには、ロード バランサーの IPv4 および IPv6 パブリック IP アドレスが必要です。 パブリック IP アドレスは、[az network public-ip create](/cli/azure/network/public-ip) で作成します。 次の例では、*DsResourceGroup01* リソース グループ内に *dsPublicIP_v4* と *dsPublicIP_v6* という名前の IPv4 および IPv6 パブリック IP アドレスを作成します。

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>VM のパブリック IP アドレスの作成

インターネット上の VM にリモート アクセスするには、VM の IPv4 パブリック IP アドレスが必要です。 パブリック IP アドレスは、[az network public-ip create](/cli/azure/network/public-ip) で作成します。

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer を作成する

このセクションでは、ロード バランサーのデュアル フロントエンド IP (IPv4 および IPv6) とバックエンド アドレス プールを構成してから、Standard ロード バランサーを作成します。

### <a name="create-load-balancer"></a>ロード バランサーの作成

[az network lb create](/cli/azure/network/lb) を使用して、**dsLB** という名前の Standard Load Balancer を作成します。これには、**dsLbFrontEnd_v4** という名前のフロントエンド プールと **dsLbBackEndPool_v4** という名前のバックエンド プールが含まれています。このバックエンド プールは、前のステップで作成した IPv4 パブリック IP アドレス **dsPublicIP_v4** に関連付けられています。 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6 フロントエンドの作成

IPV6 フロントエンド IP を作成するには、[az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) を使用します。 次の例では、*dsLbFrontEnd_v6* という名前のフロントエンド IP 構成を作成して、*dsPublicIP_v6* アドレスをアタッチします。

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6 バックエンド アドレス プールの構成

[az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) を使用して、IPv6 バックエンド アドレス プールを作成します。 次の例では、IPv6 NIC が構成された VM を含めるための *dsLbBackEndPool_v6* という名前のバックエンド アドレス プールを作成します。

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>正常性プローブの作成
[az network lb probe create](/cli/azure/network/lb/probe) を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 

ロード バランサー規則は、[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) で作成します。 次の例では、*dsLBrule_v4* および *dsLBrule_v6* という名前のロード バランサー規則を作成し、IPv4 および IPv6 フロントエンド IP 構成に応じて、*TCP* ポート *80* のトラフィックを負荷分散します。

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
複数の VM をデプロイする前に、サポートするネットワーク リソース (可用性セット、ネットワーク セキュリティ グループ、仮想ネットワーク、および仮想 NIC) を作成する必要があります。 
### <a name="create-an-availability-set"></a>可用性セットの作成
アプリの可用性を高めるには、可用性セットに VM を配置します。

可用性セットを作成するには、[az vm availability-set create](/cli/azure/vm/availability-set) を使用します。 次の例では、*dsAVset* という名前の可用性セットを作成します。

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成

VNet でのインバウンドおよびアウトバウンド通信を管理する規則のネットワーク セキュリティ グループを作成します。

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

ネットワーク セキュリティ グループを作成するには、[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) を使用します。


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>インバウンドおよびアウトバウンド接続に関するネットワーク セキュリティ グループ規則の作成

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) を使用して、ポート 3389 経由の RDP 接続と、ポート 80 経由のインターネット接続を許可するネットワーク セキュリティ グループ規則を作成します。また、アウトバウンド接続用のネットワーク セキュリティ グループ規則を作成します。

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、*dsVNET* という名前の仮想ネットワークと、サブネット *dsSubNET_v4* および *dsSubNET_v6* を作成します。

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC の作成

各 VM の仮想 NIC を作成するには、[az network nic create](/cli/azure/network/nic#az_network_nic_create) を使用します。 次の例では、各 VM の仮想 NIC を作成します。 各 NIC には、2 つの IP 構成があります (1 つは IPv4 構成、1 つは IPv6 構成)。 IPV6 構成を作成するには、[az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) を使用します。
 
```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

VM を作成するには、[az vm create](/cli/azure/vm#az_vm_create) を使用します。 次の例では、2 つの VM と必要な仮想ネットワーク コンポーネントがまだ存在しない場合にそれらを作成します。 

仮想マシン *dsVM0* を以下のように作成します。

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

仮想マシン *dsVM1* を以下のように作成します。

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal で IPv6 デュアル スタック仮想ネットワークを表示する
次のようにして、Azure portal で IPv6 デュアル スタック仮想ネットワークを表示することができます。
1. ポータルの検索バーで、「*dsVnet*」と入力します。
2. 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。 これにより、*dsVnet* という名前のデュアル スタック仮想ネットワークの **[概要]** ページが起動します。 デュアル スタック仮想ネットワークには、*dsSubnet* という名前のデュアル スタック サブネットにある、IPv4 と IPv6 の両方の構成を持つ 2 つの NIC が表示されます。

  ![Azure の IPv6 デュアル スタック仮想ネットワーク](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>次のステップ

この記事では、デュアル フロントエンド IP 構成 (IPv4 および IPv6) を持つ Standard ロード バランサーを作成しました。 また、NIC を含む 2 つの仮想マシンも作成しました。この NIC のデュアル IP 構成 (IPV4 および IPv6) は、ロード バランサーのバックエンド プールに追加されました。 Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](../virtual-network/ip-services/ipv6-overview.md)に関するページを参照してください
