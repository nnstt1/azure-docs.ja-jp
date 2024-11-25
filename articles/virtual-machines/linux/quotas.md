---
title: vCPU のクォータ
description: Azure の vCPU クォータについて説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 48bba54f8dae565a2af53c068b382f10b4b3b0f8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215607"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>Azure CLI を使用して vCPU クォータを確認する

**適用対象:** :heavy_check_mark: Linuｘ VM :heavy_check_mark: 柔軟なスケール セット :heavy_check_mark: 均一のスケール セット 

仮想マシンの vCPU クォータと仮想マシン スケール セットは、各リージョンでサブスクリプションごとに 2 つのレベルで決められています。 1 番目のレベルはリージョンの vCPU の合計で、2 番目のレベルは D シリーズの vCPU などのさまざまな VM サイズ ファミリ コアです。 新しい VM をデプロイするときは常に、VM の vCPU がその VM サイズ ファミリの vCPU クォータ、またはリージョンの vCPU クォータの合計を超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。 リージョン内の仮想マシンの全体的な数にもクォータがあります。 各クォータの詳細については、[Azure Portal](https://portal.azure.com) の **[サブスクリプション]** ページの **[使用量 + クォータ]** セクションで確認したり、Azure CLI を使用して値を照会することができます。

> [!NOTE]
> クォータは、使用されているコアの合計数に基づいて、割り当て済みと割り当て解除済みの両方で計算されます。 追加のコアが必要な場合は、[クォータの増加を要求する](../../azure-portal/supportability/regional-quota-requests.md)か、または不要になった VM を削除します。 


## <a name="check-usage"></a>使用量を確認する

クォータの使用量は、[az vm list-usage](/cli/azure/vm) を使用して確認できます。

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

出力は次のようになります。


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>予約 VM インスタンス
予約 VM インスタンスは、VM サイズの柔軟性はなく、1 つのサブスクリプションを対象とし、vCPU クォータに新しい側面を追加するものです。 これらの値は、そのサブスクリプションにデプロイできる必要がある、規定サイズのインスタンス数を示します。 これらは、確実に Azure の予約をサブスクリプションにデプロイできるようにするための、クォータ システムのプレースホルダーとして機能します。 たとえば、特定のサブスクリプションに 10 個の Standard_D1 予約がある場合、Standard_D1 予約の使用量の上限は 10 になります。 これにより、Azure では、Standard_D1 インスタンスで使用される、リージョンの vCPU の合計クォータで利用できる vCPU が常に少なくとも 10 個確保され、Standard_D1 インスタンスで使用される Standard D ファミリ vCPU クォータで利用できる vCPU が少なくとも 10 個確保されます。

単一サブスクリプション RI を購入するためにクォータの引き上げが必要な場合は、そのサブスクリプションの[クォータの引き上げを要求](../../azure-portal/supportability/regional-quota-requests.md)できます。

## <a name="next-steps"></a>次のステップ

課金とクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json)」を参照してください。