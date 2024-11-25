---
title: クイックスタート - Azure Resource Manager テンプレートを使用して Linux 仮想マシン スケール セットを作成する
description: サンプル アプリのデプロイと自動スケール ルールの構成を行う Azure Resource Manager テンプレートを使用して Linux 仮想マシン スケール セットをすばやく作成する方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: f205354b0a27404adaec83e071934c2cea4d4ba0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693226"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Linux 仮想マシン スケール セットを作成する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: ユニフォーム スケール セット

仮想マシン スケール セットを使用すると、自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 その後、Azure ロード バランサーがトラフィックをスケール セット内の VM インスタンスに分散します。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、仮想マシン スケール セットを作成し、サンプル アプリケーションをデプロイします。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM テンプレートを使用して、関連するリソースのグループをデプロイできます。 1 つのテンプレートで、仮想マシン スケール セットの作成、アプリケーションのインストール、および自動スケール ルールの構成を実行できます。 変数やパラメーターを使用してこのテンプレートを再利用することで、既存のスケール セットを更新したり、追加のスケール セットを作成したりできます。 テンプレートは、Azure portal、Azure CLI、Azure PowerShell からデプロイすることも、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインから呼び出すこともできます。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fpython%2Fvmss-bottle-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/vmss-bottle-autoscale/)からのものです。

:::code language="json" source="~/quickstart-templates/application-workloads/python/vmss-bottle-autoscale/azuredeploy.json":::

テンプレートでは、次のリソースが定義されています。

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>スケール セットを定義する

テンプレートを使用してスケールを作成するには、適切なリソースを定義します。 仮想マシン スケール セットのリソースの種類のコア部分は次のとおりです。

| プロパティ                     | プロパティの説明                                  | テンプレート値の例                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 作成する Azure リソースの種類                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | スケール セットの名前                                       | myScaleSet                                |
| location                     | スケール セットを作成する場所                     | 米国東部                                   |
| sku.name                     | 各スケール セット インスタンスの VM サイズ                  | Standard_A1                               |
| sku.capacity                 | 最初に作成する VM インスタンスの数           | 2                                         |
| upgradePolicy.mode           | 変更が発生した場合の VM インスタンスのアップグレード モード              | 自動                                 |
| imageReference               | VM インスタンスに使用するプラットフォームまたはカスタム イメージ | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | 各 VM インスタンス名のプレフィックス                     | myvmss                                    |
| osProfile.adminUsername      | 各 VM インスタンスのユーザー名                        | azureuser                                 |
| osProfile.adminPassword      | 各 VM インスタンスのパスワード                        | P@ssw0rd!                                 |

スケール セット テンプレートをカスタマイズするために、VM サイズや初期容量を変更することができます。 また、別のプラットフォームやカスタム イメージを使用することもできます。

### <a name="add-a-sample-application"></a>サンプル アプリケーションを追加する

スケール セットをテストするには、基本的な Web アプリケーションをインストールします。 スケール セットをデプロイするとき、VM 拡張機能によって、デプロイ後の構成とオートメーション タスク (アプリのインストールなど) を提供できます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 拡張機能をスケール セットに適用するには、上記のリソース例に *extensionProfile* セクションを追加します。 拡張機能プロファイルは、通常、次のプロパティを定義します。

- 拡張機能の種類
- 拡張機能の発行元
- 拡張機能のバージョン
- 構成またはインストール スクリプトの場所
- VM インスタンスで実行するコマンド

テンプレートでは、カスタム スクリプト拡張機能を使用して、[Bottle](https://bottlepy.org/docs/dev/)、Python Web フレームワーク、および単純な HTTP サーバーをインストールします。

**fileUris** には 2 つのスクリプト  - *installserver.sh* と *workserver.py* が定義されます。 これらのファイルが GitHub からダウンロードされた後、*commandToExecute* によって、アプリをインストールして構成する `bash installserver.sh` が実行されます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートは、次の **[Deploy to Azure]\(Azure へのデプロイ\)** ボタンを選択することでデプロイできます。 このボタンは、Azure ポータルを開き、完全なテンプレートを読み込み、スケール セットの名前、インスタンス数、管理者の資格情報などのいくつかのパラメーターの入力を求めます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fpython%2Fvmss-bottle-autoscale%2Fazuredeploy.json)

Resource Manager テンプレートは、Azure CLI を使用してデプロイすることもできます。

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az deployment group create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/python/vmss-bottle-autoscale/azuredeploy.json
```

プロンプトに応答して、VM インスタンスのスケール セットの名前、インスタンスの数、および 管理者の資格情報を指定します。 スケール セットとサポート リソースを作成するのに数分かかります。

## <a name="validate-the-deployment"></a>デプロイの検証

動作中のスケール セットを表示するには、Web ブラウザーでサンプル Web アプリケーションにアクセスします。 次のように [az network public-ip list](/cli/azure/network/public-ip) を使用してロード バランサーのパブリック IP アドレスを取得します。

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

ロード バランサーのパブリック IP アドレスを *http:\//publicIpAddress:9000/do_work* 形式で Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![NGINX の既定の Web ページ](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、次のように [az group delete](/cli/azure/group) を使用して、リソース グループ、スケール セット、およびすべての関連リソースを削除できます。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Linux スケール セットを作成し、カスタム スクリプト拡張機能を使用して基本的な Python Web サーバーを VM インスタンスにインストールしました。 さらに学習するには、Azure 仮想マシン スケール セットを作成および管理する方法についてのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure 仮想マシン スケール セットの作成と管理](tutorial-create-and-manage-cli.md)
