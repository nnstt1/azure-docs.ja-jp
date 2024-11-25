---
title: IPv6 デュアル スタック アプリケーションのデプロイ - Basic Load Balancer - PowerShell
titlesuffix: Azure Virtual Network
description: この記事では、Azure Powershell を使用して Azure 仮想ネットワーク内に IPv6 デュアル スタック アプリケーションをデプロイする方法を説明します。
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65716763f632845545dc2cf8de23aabd85678be7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367770"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>Basic Load Balancer を使用する IPv6 デュアル スタック アプリケーションをデプロイする - PowerShell

この記事では、Basic Load Balancer を使用するデュアル スタック (IPv4 と IPv6) アプリケーションを、Azure PowerShell を使用してデプロイする方法を示します。これには、デュアル スタック仮想ネットワークとサブネット、デュアル (IPv4 と IPv6) フロントエンド構成の Basic Load Balancer、デュアル IP 構成を持つ NIC、ネットワーク セキュリティ グループ、およびパブリック IP を使用する VM が含まれています。

Standard Load Balancer を使用するデュアル スタック (IPV4 と IPv6) アプリケーションをデプロイするには、[Azure PowerShell による Standard Load Balancer を使用する IPv6 デュアル スタック アプリケーションのデプロイ](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)に関する記事を参照してください。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用することを選択する場合、この記事では Azure PowerShell モジュール バージョン 6.9.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。


## <a name="create-a-resource-group"></a>リソース グループを作成する

デュアル スタック仮想ネットワークを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) でリソース グループを作成する必要があります。 次の例では、*myRGDualStack* という名前のリソース グループを *east us* の場所に作成します。

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 および IPv6 パブリック IP アドレスを作成する
インターネットから仮想マシンにアクセスするには、ロード バランサーの IPv4 および IPv6 パブリック IP アドレスが必要です。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用してパブリック IP アドレスを作成します。 次の例では、*dsRG1* リソース グループ内に *dsPublicIP_v4* および *dsPublicIP_v6* という名前の IPv4 および IPv6 パブリック IP アドレスを作成します。

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
RDP 接続を使用して仮想マシンにアクセスするには、[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) で仮想マシンの IPv4 パブリック IP アドレスを作成します。

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Basic Load Balancer を作成する

このセクションでは、ロード バランサーのデュアル フロントエンド IP (IPv4 および IPv6) とバックエンド アドレス プールを構成してから、Basic Load Balancer を作成します。

### <a name="create-front-end-ip"></a>フロントエンド IP を作成する

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、フロントエンド IP を作成します。 次の例では、*dsLbFrontEnd_v4* および *dsLbFrontEnd_v6* という名前の IPv4 および IPv6 フロントエンド IP 構成を作成します。

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。 残りの手順では、VM をこのバックエンド プールにアタッチします。 次の例では、IPV4 と IPv6 の両方の NIC 構成を持つ VM を含めるための、*dsLbBackEndPool_v4* および *dsLbBackEndPool_v6* という名前のバックエンド アドレス プールを作成します。

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>正常性プローブの作成
[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) を使用して正常性プローブを作成し、VM の正常性を監視します。
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 確実に正常な VM のみでトラフィックを受信するために、必要に応じて、正常性プローブを定義できます。 Basic Load Balancer では IPv4 プローブを使用して、VM 上の IPv4 と IPv6 の両方のエンドポイントの正常性を評価します。 Standard Load Balancer には、明示的な IPv6 正常性プローブのサポートが含まれます。

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。 次の例では、*dsLBrule_v4* および *dsLBrule_v6* という名前のロード バランサー規則を作成し、IPv4 および IPv6 フロントエンド IP 構成に応じて、*TCP* ポート *80* のトラフィックを負荷分散します。

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>ロード バランサーの作成

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、基本ロード バランサーを作成します。 次の例では、前述の手順で作成した IPv4 と IPv6 フロントエンド IP の構成、バックエンド プール、および負荷分散規則を使用して、*myLoadBalancer* という名前のパブリック Basic Load Balancer を作成します。

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
VM をいくつかデプロイしてから、バランサーをテストできるようになる前に、サポート ネットワーク リソース (可用性セット、ネットワーク セキュリティ グループ、仮想ネットワーク、および仮想 NIC) を作成する必要があります。 
### <a name="create-an-availability-set"></a>可用性セットの作成
アプリの高可用性を高めるには、可用性セットに VM を配置します。

可用性セットを作成するには、[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) を使用します。 次の例では、*myAvailabilitySet* という名前の可用性セットを作成します。

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成

VNET でのインバウンドおよびアウトバウンド通信を管理する規則のネットワーク セキュリティ グループを作成します。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>ポート 3389 のネットワーク セキュリティ グループの規則を作成する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用してポート 3389 経由の RDP 接続を許可するネットワーク セキュリティ グループ規則を作成します。

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、ポート 80 経由のインターネット接続を許可するネットワーク セキュリティ グループ規則を作成します。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*mySubnet* と共に *myVnet* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>NIC の作成

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して仮想 NIC を作成します。 次の例では、IPv4 と IPv6 の両方の構成を持つ 2 つの仮想 NIC を作成します。 (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次のように、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成できるようになりました。 次の例では、2 つの VM と必要な仮想ネットワーク コンポーネントがまだ存在しない場合にそれらを作成します。 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>IPv4 および IPv6 エンドポイントの IP アドレスを確認する
リソース グループのネットワーク インターフェイス オブジェクトをすべて取得して、`get-AzNetworkInterface` でこのデプロイで使用される IP の概要を示します。 また、`get-AzpublicIpAddress` を使用して、Load Balancer の IPv4 および IPv6 エンドポイントのフロントエンド アドレスを取得します。

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
次の図には、2 つの VM のプライベート IPv4 および IPv6 アドレスと、Load Balancer のフロントエンド IPv4 および IPv6 IP アドレスをリストするサンプル出力が示されています。

![Azure におけるデュアル スタック (IPv4/IPv6) アプリケーションのデプロイの IP に関する概要](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal で IPv6 デュアル スタック仮想ネットワークを表示する
次のようにして、Azure portal で IPv6 デュアル スタック仮想ネットワークを表示することができます。
1. ポータルの検索バーで、「*dsVnet*」と入力します。
2. 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。 これにより、*dsVnet* という名前のデュアル スタック仮想ネットワークの **[概要]** ページが起動します。 デュアル スタック仮想ネットワークには、*dsSubnet* という名前のデュアル スタック サブネットにある、IPv4 と IPv6 の両方の構成を持つ 2 つの NIC が表示されます。

  ![Azure の IPv6 デュアル スタック仮想ネットワーク](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>次のステップ

この記事では、デュアル フロントエンド IP 構成 (IPv4 および IPv6) を持つ Basic Load Balancer を作成しました。 また、NIC を含む 2 つの仮想マシンも作成しました。この NIC のデュアル IP 構成 (IPV4 および IPv6) は、ロード バランサーのバックエンド プールに追加されました。 Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](../virtual-network/ip-services/ipv6-overview.md)に関するページを参照してください
