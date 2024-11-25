---
title: Azure 仮想マシン スケール セットでカスタム スケールイン ポリシーを使用する
description: Azure 仮想マシン スケール セットで、自動スケーリング構成を使用してインスタンス数を管理するカスタム スケールイン ポリシーを使用する方法について説明します
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: scale-in-policy
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: af1aa7ceb0784b58f9878befeae7c6ee26742061
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696637"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットでカスタム スケールイン ポリシーを使用する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: ユニフォーム スケール セット

仮想マシン スケール セットのデプロイは、プラットフォームのメトリックやユーザー定義のカスタム メトリックなど、メトリックの配列に基づいて、スケールアウトまたはスケールインできます。 スケールアウトではスケール セット モデルに基づいて新しい仮想マシンが作成されるのに対し、スケールインでは、スケール セットのワークロードの変化に応じて、構成や機能が異なる可能性のある実行中の仮想マシンが調整されます。 

スケールイン ポリシー機能を使用すると、ユーザーは、3 つのスケールイン構成によって、仮想マシンがスケールインされる順序を構成できます。 

1. Default
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Default スケールイン ポリシー

既定では、スケールインするインスタンスを決定するためにこのポリシーが仮想マシン スケール セットで適用されます。 *Default* ポリシーでは、次の順序で VM がスケールイン対象に選択されます。

1. 可用性ゾーン間で仮想マシンを均衡させます (スケール セットがゾーン構成でデプロイされている場合)
2. 障害ドメイン間で仮想マシンを均衡させます (ベスト エフォート)
3. インスタンス ID が最も大きい仮想マシンを削除します

既定の順序に従うだけの場合、ユーザーはスケールイン ポリシーを指定する必要はありません。

可用性ゾーン間または障害ドメイン間で均衡させる場合、可用性ゾーンまたは障害ドメインの間でインスタンスは移動されないことに注意してください。 均衡は、仮想マシンの分散が均衡になるまで、不均衡な可用性ゾーンまたは障害ドメインから仮想マシンを削除することによって実現されます。

### <a name="newestvm-scale-in-policy"></a>NewestVM スケールイン ポリシー

このポリシーでは、可用性ゾーン間で VM が均衡された後 (ゾーン デプロイの場合)、スケール セット内で最も新しく作成された仮想マシンが削除されます。 このポリシーを有効にするには、仮想マシン スケール セット モデルで構成を変更する必要があります。

### <a name="oldestvm-scale-in-policy"></a>OldestVM スケールイン ポリシー

このポリシーでは、可用性ゾーン間で VM が均衡された後 (ゾーン デプロイの場合)、スケール セット内で最も古く作成された仮想マシンが削除されます。 このポリシーを有効にするには、仮想マシン スケール セット モデルで構成を変更する必要があります。

## <a name="enabling-scale-in-policy"></a>スケールイン ポリシーを有効にする

スケールイン ポリシーは、仮想マシン スケール セット モデルで定義されています。 前のセクションで説明したように、NewestVM ポリシーと OldestVM ポリシーを使用する場合は、スケールイン ポリシーの定義が必要です。 スケール セット モデルにスケールイン ポリシーの定義が見つからない場合、仮想マシン スケール セットでは "Default" スケールイン ポリシーが自動的に使用されます。 

スケールイン ポリシーは、次の方法を使用して仮想マシン スケール セット モデルで定義できます。

### <a name="azure-portal"></a>Azure portal
 
次の手順で、新しいスケール セットを作成するときのスケールイン ポリシーを定義します。 
 
1. **[仮想マシン スケール セット]** に移動します。
1. **[+ 追加]** を選択して、新しいスケール セットを作成します。
1. **[スケーリング]** タブにアクセスします。 
1. **[スケールイン ポリシー]** セクションを見つけます。
1. ドロップダウンからスケールイン ポリシーを選択します。
1. 新しいスケール セットの作成が完了したら、 **[確認と作成]** ボタンを選択します。

### <a name="using-api"></a>API の使用

API 2019-03-01 を使用して、仮想マシン スケール セットで PUT を実行します。

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

リソース グループを作成した後、スケールイン ポリシーを *OldestVM* に設定して新しいスケール セットを作成します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

次の例では、新しいスケール セットを作成するときにスケールイン ポリシーが定義されます。 まずリソース グループを作成し、その後、スケールイン ポリシーを *OldestVM* に設定して新しいスケール セットを作成します。 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>テンプレートの使用

テンプレートの "properties" に、以下を追加します。

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

上のブロックでは、(自動スケーリングまたは手動削除によって) スケールインがトリガーされたら、仮想マシン スケール セットでは、ゾーン均衡になっているスケール セット内の最も古い VM を削除するように、指定されています。

仮想マシン スケール セットがゾーン均衡になっていない場合は、最初に不均衡なゾーン間での VM の削除が行われます。 不均衡なゾーン内では、上で指定されているスケールイン ポリシーを使用して、スケールインする VM が決定されます。 この場合、不均衡なゾーン内では、そのゾーンで最も古い VM が削除対象として選択されます。

非ゾーン仮想マシン スケール セットの場合は、スケール セット全体で最も古い VM が削除対象に選択されます。

上のスケールイン ポリシーで NewestVM を使用したときも、同じプロセスが適用されます。

## <a name="modifying-scale-in-policies"></a>スケールイン ポリシーの変更

スケールイン ポリシーの変更は、スケールイン ポリシーの適用と同じプロセスで行います。 たとえば、上の例でポリシーを OldestVM から NewestVM に変更する場合は、次の方法で実行できます。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、既存のスケール セットのスケールイン ポリシーを変更できます。 
 
1. 既存の仮想マシン スケール セットで、左側のメニューから **[スケーリング]** を選択します。
1. **[スケールイン ポリシー]** タブを選択します。
1. ドロップダウンからスケールイン ポリシーを選択します。
1. 完了したら、 **[保存]** を選択します。 

### <a name="using-api"></a>API の使用

API 2019-03-01 を使用して、仮想マシン スケール セットで PUT を実行します。

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

既存のスケール セットのスケールイン ポリシーを更新します。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

次に示すのは、既存のスケール セットのスケールイン ポリシーを更新する例です。 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>テンプレートの使用

テンプレートの "properties" を次のように変更して再デプロイします。 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

NewestVM を Default または OldestVM に変更する場合も、同じプロセスが適用されます

## <a name="instance-protection-and-scale-in-policy"></a>インスタンスの保護とスケールイン ポリシー

仮想マシン スケール セットには、2 種類の[インスタンス保護](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)があります。

1. スケールインから保護する
2. スケール セット アクションから保護する

保護された仮想マシンは、スケールイン ポリシーが適用されるかどうかに関係なく、スケールイン アクションによって削除されません。 たとえば、VM_0 (スケール セット内の最も古い VM) がスケールインから保護されていて、スケール セットで OldestVM スケールイン ポリシーが有効になっている場合、VM_0 は、スケール セット内で最古の VM ですが、スケールインには考慮されません。 

ユーザーは、スケール セットで有効になっているスケールイン ポリシーに関係なく、保護された仮想マシンをいつでも手動で削除できます。 

## <a name="usage-examples"></a>使用例 

次の例では、スケールイン イベントがトリガーされたときに、仮想マシン スケール セットによって削除対象の VM が選択される方法を示します。 インスタンス ID が最も大きい仮想マシンは、スケール セット内で最も新しい VM と見なされ、インスタンス ID が最も小さい VM は、スケール セット内で最も古い VM と見なされます。 

### <a name="oldestvm-scale-in-policy"></a>OldestVM スケールイン ポリシー

| Event                 | ゾーン 1 でのインスタンス ID  | ゾーン 2 でのインスタンス ID  | ゾーン 3 でのインスタンス ID  | スケールインの選択                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3、4、5、10            | 2、6、9、11            | 1、7、8                |                                                                                                                                  |
| スケールイン              | 3、4、5、10            | ***2***、6、9、11      | 1、7、8                | 最も古い VM があるのはゾーン 3 ですが、ゾーン 1 と 2 のどちらかが選択されます。 ゾーン 2 で最も古い VM2 が削除されます。   |
| スケールイン              | ***3***、4、5、10      | 6、9、11               | 1、7、8                | 最も古い VM があるのはゾーン 3 ですが、ゾーン 1 が選択されます。 ゾーン 1 で最も古い VM3 が削除されます。                  |
| スケールイン              | 4、5、10               | 6、9、11               | ***1***、7、8          | ゾーンは均衡しています。 スケール セット内で最も古い VM であるゾーン 3 の VM1 が削除されます。                                               |
| スケールイン              | ***4***、5、10         | 6、9、11               | 7、8                   | ゾーン 1 とゾーン 2 のどちらかが選択されます。 2 つのゾーンで最も古い VM であるゾーン 1 の VM4 が削除されます。                              |
| スケールイン              | 5、10                  | ***6***、9、11         | 7、8                   | 最も古い VM があるのはゾーン 1 ですが、ゾーン 2 が選択されます。 ゾーン 2 で最も古い VM6 が削除されます。                    |
| スケールイン              | ***5***、10            | 9、11                  | 7、8                   | ゾーンは均衡しています。 スケール セット内で最も古い VM であるゾーン 1 の VM5 が削除されます。                                                |

非ゾーン仮想マシン スケール セットの場合は、スケール セット全体で最も古い VM が削除対象に選択されます。 "保護されている" VM は削除からスキップされます。

### <a name="newestvm-scale-in-policy"></a>NewestVM スケールイン ポリシー

| Event                 | ゾーン 1 でのインスタンス ID  | ゾーン 2 でのインスタンス ID  | ゾーン 3 でのインスタンス ID  | スケールインの選択                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3、4、5、10            | 2、6、9、11            | 1、7、8                |                                                                                                                                  |
| スケールイン              | 3、4、5、10            | 2、6、9、***11***      | 1、7、8                | ゾーン 1 と 2 のどちらかが選択されます。 2 つのゾーンで最も新しい VM であるゾーン 2 の VM11 が削除されます。                                |
| スケールイン              | 3、4、5、***10***      | 2、6、9                | 1、7、8                | 他の 2 つのゾーンより多くの VM があるため、ゾーン 1 が選択されます。 ゾーン 1 で最も新しい VM10 が削除されます。          |
| スケールイン              | 3、4、5                | 2、6、***9***          | 1、7、8                | ゾーンは均衡しています。 スケール セット内で最も新しい VM であるゾーン 2 の VM9 が削除されます。                                                |
| スケールイン              | 3、4、5                | 2、6                   | 1、7、***8***          | ゾーン 1 とゾーン 3 のどちらかが選択されます。 ゾーン 3 で最も新しい VM8 が削除されます。                                      |
| スケールイン              | 3、4、***5***          | 2、6                   | 1、7                   | 最も新しい VM があるのはゾーン 3 ですが、ゾーン 1 が選択されます。 ゾーン 1 で最も新しい VM5 が削除されます。                    |
| スケールイン              | 3、4                   | 2、6                   | 1、***7***             | ゾーンは均衡しています。 スケール セット内で最も新しい VM であるゾーン 3 の VM7 が削除されます。                                                |

非ゾーン仮想マシン スケール セットの場合は、スケール セット全体で最も新しい VM が削除対象に選択されます。 "保護されている" VM は削除からスキップされます。 

## <a name="troubleshoot"></a>[トラブルシューティング]

1. スケールイン ポリシーを有効にできない: "タイプ 'properties' のオブジェクトにメンバー 'scaleInPolicy' が見つかりませんでした" でエラー メッセージが始まる "BadRequest" エラーが発生する場合は、仮想マシン スケール セットに使用されている API のバージョンを確認します。 この機能では、API バージョン 2019-03-01 以降が必要です。

2. 間違った VM がスケールインに選択される: 上記の例を参照してください。 仮想マシン スケール セットがゾーン デプロイの場合、スケールイン ポリシーは、最初に不均衡なゾーンに適用され、ゾーンが均衡された後でスケール セット全体に適用されます。 スケールインの順序が上記の例と一致しない場合は、仮想マシン スケール セット チームにトラブルシューティングのためのクエリを提出してください。

## <a name="next-steps"></a>次のステップ

仮想マシン スケール セットに[ご自身のアプリケーションをデプロイする](virtual-machine-scale-sets-deploy-app.md)方法を学習します。
