---
title: NAT ゲートウェイを作成する - Resource Manager テンプレート
titleSuffix: Azure Virtual Network NAT
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、NAT ゲートウェイを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 728c382d68c739cba927db985e16d3679f69f0d5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439294"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>クイックスタート: NAT ゲートウェイの作成 - ARM テンプレート

Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Virtual Network NAT の使用を開始します。 このテンプレートは、仮想ネットワーク、NAT ゲートウェイ リソース、および Ubuntu 仮想マシンをデプロイします。 Ubuntu 仮想マシンは、NAT ゲートウェイ リソースに関連付けられているサブネットにデプロイされます。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fnat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/nat-gateway-1-vm)からのものです。

このテンプレートは、以下のリソースを作成するように構成されています。

* 仮想ネットワーク
* NAT ゲートウェイ リソース
* Ubuntu 仮想マシン

Ubuntu VM は、NAT ゲートウェイ リソースに関連付けられているサブネットにデプロイされます。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/nat-gateway-1-vm/azuredeploy.json":::

このテンプレートには、次の 9 つの Azure リソースが定義されています。

* **[Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)** : ネットワーク セキュリティ グループを作成します。
* **[Microsoft.Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : セキュリティ規則を作成します。
* **[Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)** : パブリック IP アドレスを作成します。
* **[Microsoft.Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)** : パブリック IP プレフィックスを作成します。
* **[Microsoft.Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)** : 仮想マシンを作成します。
* **[Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)** : 仮想ネットワークを作成します。
* **[Microsoft.Network/natGateways](/azure/templates/microsoft.network/natgateways)** : NAT ゲートウェイ リソースを作成します。
* **[Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)** : 仮想ネットワーク サブネットを作成します。
* **[Microsoft.Network/networkinterfaces](/azure/templates/microsoft.network/networkinterfaces)** : ネットワーク インターフェイスを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fnat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウから **[リソース グループ]** を選択します。

1. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は **myResourceGroupNAT** です

1. 次のリソースがリソース グループ内に作成されていることを確認します。

    ![Virtual Network NAT リソースグループ](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

**Azure CLI**

リソース グループとそこに含まれているすべてのリソースは、不要になったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して削除できます。

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループと、その内部に含まれているすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

必要がなくなったら、リソース グループ、NAT ゲートウェイ、およびすべての関連リソースを削除します。 NAT ゲートウェイを含むリソース グループ **[myResourceGroupNAT]** を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

* NAT ゲートウェイ リソース
* 仮想ネットワーク
* Ubuntu 仮想マシン

仮想マシンは、NAT ゲートウェイに関連付けられている仮想ネットワーク サブネットにデプロイされました。

Virtual Network NAT と Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

* [Virtual Network NAT の概要](nat-overview.md)を読む
* [NAT Gateway リソース](nat-gateway-resource.md)について読む
* [Azure Resource Manager](../../azure-resource-manager/management/overview.md) の詳細を確認する
