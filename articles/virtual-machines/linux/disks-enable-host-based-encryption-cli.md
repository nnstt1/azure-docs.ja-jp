---
title: ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure CLI - マネージド ディスク
description: ホストでの暗号化を使用して、Azure マネージド ディスクでエンドツーエンドの暗号化を有効にします。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c5bdbcf37818cba66b9eaf7ba4f5f95deb785fe4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691916"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure CLI を使用して、ホストでの暗号化を使用したエンドツーエンドの暗号化を有効にする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化とその他のマネージド ディスクの暗号化の概要については、「[ホストでの暗号化 - VM データのエンドツーエンドの暗号化](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)」を参照してください。

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

サポートされている VM サイズの完全な一覧は、プログラムを使用してプルできます。 プログラムによって取得する方法については、「[サポートされている VM のサイズを確認する](#finding-supported-vm-sizes)」セクションを参照してください。
VM サイズをアップグレードすると、新しい VM サイズで EncryptionAtHost 機能がサポートされているかどうかが確認されます。

## <a name="prerequisites"></a>前提条件

VM または VMSS に対して EncryptionAtHost プロパティを使用する前に、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、お使いのサブスクリプションに対してこの機能を有効にしてください。

1.  次のコマンドを実行して、お使いのサブスクリプションにこの機能を登録します

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  この機能を試す前に、下のコマンドを使用して登録状態が Registered であることを確認してください (数分かかります)。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault と DiskEncryptionSet の作成

機能が有効になったら、まだ行っていない場合は、Azure Key Vault と DiskEncryptionSet を設定する必要があります。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>例

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>カスタマー マネージド キーを使用して、ホストでの暗号化が有効な VM を作成します。 

前に作成した DiskEncryptionSet のリソース URI を使用して、マネージド ディスクを持つ VM を作成し、カスタマー マネージド キーを使用して、OS ディスクとデータ ディスクのキャッシュを暗号化します。 一時ディスクは、プラットフォーム マネージド キーを使用して暗号化されます。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>プラットフォーム マネージド キーを使用して、ホストでの暗号化が有効な VM を作成します。 

ホストでの暗号化が有効な VM を作成し、プラットフォーム マネージド キーを使用して OS/データ ディスクのキャッシュと一時ディスクを暗号化します。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>VM を更新してホストでの暗号化を有効にします。 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>特定の VM について、ホストでの暗号化の状態を確認します

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```


### <a name="update-a-vm-to-disable-encryption-at-host"></a>VM を更新してホストでの暗号化を無効にします。 

ホストでの暗号化を無効にするには、あらかじめ VM の割り当てを解除しておく必要があります。

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=false
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>カスタマー マネージド キーを使用して、ホストでの暗号化が有効な仮想マシン スケール セットを作成します。 

前に作成した DiskEncryptionSet のリソース URI を使用して、マネージド ディスクを持つ仮想マシン スケール セットを作成し、カスタマー マネージド キーを使用して、OS ディスクとデータ ディスクのキャッシュを暗号化します。 一時ディスクは、プラットフォーム マネージド キーを使用して暗号化されます。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>プラットフォーム マネージド キーを使用して、ホストでの暗号化が有効な仮想マシン スケール セットを作成します。 

ホストでの暗号化が有効な仮想マシン スケール セットを作成し、プラットフォーム マネージド キーを使用して OS/データ ディスクのキャッシュと一時ディスクを暗号化します。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>仮想マシン スケール セットを更新し、ホストでの暗号化を有効にします。 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>特定の仮想マシン スケール セットについて、ホストでの暗号化の状態を確認します

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

### <a name="update-a-virtual-machine-scale-set-to-disable-encryption-at-host"></a>仮想マシン スケール セットを更新し、ホストでの暗号化を無効にします。 

ホストでの暗号化は仮想マシン スケール セットで無効にできますが、それが作用するのは、ホストでの暗号化を無効にした後で作成した VM のみです。 既存の VM については、VM の割り当てを解除し、[ホストでの暗号化をその個々の VM で無効](#update-a-vm-to-disable-encryption-at-host)にした後、再度 VM を割り当てる必要があります。

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=false
```

## <a name="finding-supported-vm-sizes"></a>サポートされている VM のサイズを確認する

レガシ VM のサイズはサポートされていません。 サポートされている VM サイズの一覧は、次のいずれかで見つけることができます。

[リソースの SKU API](/rest/api/compute/resourceskus/list) を呼び出し、`EncryptionAtHostSupported` 機能が **True** に設定されていることを確認しています。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

または、[Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell コマンドレットを呼び出します。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>次のステップ

これらのリソースを作成し、構成したので、これらを使用してマネージド ディスクをセキュリティで保護することができます。 次のリンクには、個々のシナリオごとにスクリプトの例が含まれています。これは、マネージド ディスクをセキュリティで保護するために使用できます。

[Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
