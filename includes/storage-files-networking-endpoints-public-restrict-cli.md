---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 1793420f69bf3f56d7ae593167052eee9454986e
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893187"
---
サービス エンドポイントを使用して、ストレージ アカウントのパブリック エンドポイントへのアクセスを特定の仮想ネットワークに制限するには、まず、ストレージ アカウントと仮想ネットワークに関する情報を収集する必要があります。 この情報を収集するには、`<storage-account-resource-group>`、`<storage-account-name>`、`<vnet-resource-group-name>`、`<vnet-name>`、`<subnet-name>` を指定します。

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

仮想ネットワークからのトラフィックが、ストレージ アカウントのパブリック エンドポイントへのアクセスを Azure ネットワーク ファブリックによって許可されるためには、仮想ネットワークのサブネットで `Microsoft.Storage` サービス エンドポイントが公開されている必要があります。 次の CLI コマンドを実行すると、まだ存在しない場合は、`Microsoft.Storage` サービス エンドポイントがサブネットに追加されます。

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

ストレージ アカウントへのトラフィックを制限する最後のステップでは、ネットワーク ルールを作成し、ストレージ アカウントのネットワーク ルール セットに追加します。

```azurecli
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```
