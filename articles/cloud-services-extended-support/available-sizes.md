---
title: Azure Cloud Services (延長サポート) に利用可能なサイズ
description: Azure Cloud Services (延長サポート) のデプロイに利用可能なサイズ
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8075176302e81788b3b180501c951c27ee4105eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434162"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) に利用可能なサイズ

この記事では、Cloud Services (延長サポート) インスタンスで使用可能な仮想マシンのサイズについて説明します。   

| SKU ファミリ |  ACU/コア | 
|---|---|
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 ～ 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 ～ 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190* |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180 から 240* |
|[H](../virtual-machines/h-series.md) | 290 ～ 300* | 

>[!NOTE]
> \* が付いている ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを増強します。 増強量は、VM のサイズ、ワークロード、および同じホストで実行されている他のワークロードによって変化します。

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Cloud Services (延長サポート) のサイズを構成する

ロール インスタンスの仮想マシンのサイズを、サービス定義ファイル内のサービス モデルの一部として指定できます。 ロールのサイズによって、CPU コアの数、メモリ容量、ローカル ファイル システムのサイズが決まります。

たとえば、Web ロール インスタンスのサイズを `Standard_D2` に設定します。 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>既存のロールのサイズを変更する

既存のロールのサイズを変更するには、サービス定義ファイル (csdef) 内の仮想マシンのサイズを変更し、クラウド サービスを再パッケージ化して再デプロイします。 

## <a name="get-a-list-of-available-sizes"></a>利用可能なサイズの一覧を取得する 

利用可能なサイズの一覧を取得するには、「[リソース SKU - 一覧](/rest/api/compute/resourceskus/list)」を参照し、次のフィルターを適用します。

```powershell
    # Update the location
    $location = 'WestUS2'
    # Get all Compute Resource Skus
    $allSkus = Get-AzComputeResourceSku
    # Filter virtualMachine skus for given location
    $vmSkus = $allSkus.Where{$_.resourceType -eq 'virtualMachines' -and $_.LocationInfo.Location -like $location}
    # From filtered virtualMachine skus, select PaaS Skus
    $passVMSkus = $vmSkus.Where{$_.Capabilities.Where{$_.name -eq 'VMDeploymentTypes'}.Value.Contains("PaaS")}
    # Optional step to format and sort the output by Family
    $passVMSkus | Sort-Object Family, Name | Format-Table -Property Family, Name, Size
```

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) の[よく寄せられる質問](faq.yml)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
