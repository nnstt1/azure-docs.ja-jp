---
title: 特殊化されたイメージからスケール セットを作成する
description: Azure Compute Gallery 内の特殊化されたイメージを使用してスケール セットを作成します。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e881c400eeaea9202e5ef32cee7900d8065f8051
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459959"
---
# <a name="create-a-scale-set-from-a-specialized-image-using-powershell"></a>PowerShell を使用して特殊化されたイメージからスケール セットを作成する 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: ユニフォーム スケール セット

Azure PowerShell を使用して、[Azure Compute Gallery](../virtual-machines/shared-image-galleries.md) に格納されている特殊化されたイメージ バージョンから VM を作成します。 一般化されたイメージ バージョンを使用してスケール セットを作成する場合は、[一般化されたイメージ バージョンからのスケール セット インスタンスの作成](instance-generalized-image-version-powershell.md)に関する記事を参照してください。

特殊化されたイメージがギャラリーに用意できたら、[New-AzVmss](/powershell/module/az.compute/new-azvmss) コマンドレットを使用して仮想マシン スケール セットを作成できます。 

この例では、イメージ定義 ID を使用して、新しい VM でイメージの最新バージョンが使用されるようにしています。 `-ImageReferenceId` にイメージ バージョン ID を使用して、特定のバージョンを使用することも可能です。 たとえば、イメージ バージョン *1.0.0* を使用するには、次のように入力します: `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`。 

特定のイメージ バージョンを使用すると、その特定のイメージ バージョンが使用できない場合 (リージョンから削除されるなどして)、オートメーションが失敗する可能性があることに注意してください。 特定のイメージ バージョンが必要な場合を除き、新しい VM の作成にはイメージ定義 ID の使用をお勧めします。

次の例では、*SouthCentralUS* という場所の *myVMSSRG* リソース グループに *myScaleSet* という名前のスケール セットを作成します。 スケール セットは、*myGalleryRG* リソース グループの *myGallery* イメージ ギャラリーにある *myImageDefinition* イメージから作成されます。 メッセージが表示されたら、スケール セット内の VM インスタンスに使用する自分の管理者資格情報を設定してください。



```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。

## <a name="next-steps"></a>次のステップ
[Azure Image Builder (プレビュー)](../virtual-machines/image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](../virtual-machines/linux/image-builder-gallery-update-image-version.md)に使用することさえできます。  

Azure Compute Gallery リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [Azure Compute Gallery の作成](https://azure.microsoft.com/resources/templates/sig-create/)
- [Azure Compute Gallery でのイメージ定義の作成](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Azure Compute Gallery でのイメージ バージョンの作成](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

共有イメージ ギャラリーの詳細については、[概要](../virtual-machines/shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[共有イメージ ギャラリーのトラブルシューティング](../virtual-machines/troubleshooting-shared-images.md)」を参照してください。
