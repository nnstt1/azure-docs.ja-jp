---
title: コンピューティング クラスターを作成する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ワークスペースでコンピューティング クラスターを作成する方法について説明します。 コンピューティング クラスターは、トレーニングや推論のコンピューティング先として使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 07/09/2021
ms.openlocfilehash: e658a8ed30b15327a68ce1671c19e55253ad6b06
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026712"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning コンピューティング クラスターの作成

Azure Machine Learning ワークスペースで[コンピューティング クラスター](concept-compute-target.md#azure-machine-learning-compute-managed)を作成および管理する方法について説明します。

Azure Machine Learning コンピューティング クラスターを使用して、クラウド内の CPU または GPU コンピューティング ノードのクラスター全体にトレーニングまたはバッチ推論のプロセスを分散させることができます。 GPU を含む VM サイズの詳細については、「[GPU 最適化済み仮想マシンのサイズ](../virtual-machines/sizes-gpu.md)」を参照してください。 

この記事では、次のことについて説明します:

* コンピューティング クラスターを作成する
* コンピューティング クラスターのコストを削減する
* クラスターの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を設定する

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-setup-vs-code.md)。

* Python SDK を使用する場合、[ワークスペースを使用して開発環境を設定](how-to-configure-environment.md)します。  環境の設定後、Python スクリプトでワークスペースにアタッチします。

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>コンピューティング クラスターとは

Azure Machine Learning コンピューティング クラスターは、シングルノードまたはマルチノードのコンピューティングを簡単に作成できるマネージド コンピューティング インフラストラクチャです。 コンピューティング クラスターは、ワークスペース内の他のユーザーと共有できるリソースです。 コンピューティングはジョブが送信されると自動的にスケールアップされ、Azure 仮想ネットワークに配置できます。 コンピューティング クラスターでは、仮想ネットワークでも **パブリック IP なし (プレビュー)** のデプロイはサポートされません。 コンピューティングはコンテナー化環境で実行され、モデルの依存関係が [Docker コンテナー](https://www.docker.com/why-docker)にパッケージ化されます。

コンピューティング クラスターを使用することで、企業で SSH ポートを開かなくても、[仮想ネットワーク環境](how-to-secure-training-vnet.md)でジョブを安全に実行できます。 ジョブはコンテナー化された環境で実行され、モデルの依存関係が Docker コンテナーにパッケージ化されます。 

## <a name="limitations"></a>制限事項

* このドキュメントに記載されている一部のシナリオは __プレビュー__ としてマークされています。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

* コンピューティング クラスターは、ワークスペースとは別のリージョンに作成できます。 この機能は __プレビュー__ 段階であり、__コンピューティング クラスター__ でのみ利用できます。コンピューティング インスタンスでは利用できません。 このプレビューは、プライベート エンドポイントが有効になっているワークスペースを使用している場合には使用できません。 

    > [!WARNING]
    > ワークスペースやデータストアと異なるリージョンでコンピューティング クラスターを使用すると、ネットワークの待機時間とデータ転送コストが増大する可能性があります。 待機時間とコストは、クラスターの作成時や、クラスターでのジョブの実行時に発生する可能性があります。

* 現在、[ARM テンプレート](/azure/templates/microsoft.machinelearningservices/workspaces/computes) を使用したクラスターの作成 (更新ではなく) のみがサポートされています。 コンピューティングを更新する場合は、現時点では SDK、Azure CLI、または UX を使用することをお勧めします。

* Azure Machine Learning コンピューティングには、割り当て可能なコア数などの既定の制限があります。 詳細については、「[Azure リソースのクォータの管理と要求](how-to-manage-quotas.md)」を参照してください。

* Azure では、リソースに "_ロック_" を設定して、削除できないようにしたり、読み取り専用にしたりすることができます。 __ワークスペースが含まれているリソース グループにリソース ロックを適用しないでください。__ ワークスペースが含まれているリソース グループにロックを適用すると、Azure ML コンピューティング クラスターのスケーリング操作ができなくなります。 リソースのロックの詳細については、「[リソースのロックによる予期せぬ変更の防止](../azure-resource-manager/management/lock-resources.md)」を参照してください。

> [!TIP]
> 必要なコア数に十分に対応するクォータを備えている限り、クラスターは一般に、最大で 100 ノードまでスケールアップすることができます。 既定では、たとえば、MPI ジョブをサポートするために、クラスターは、そのノード間でノード間通信を有効にした状態でセットアップされます。 ただし、[サポート チケットを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)し、ご利用のサブスクリプション、ワークスペース、またはノード間通信を無効にする特定のクラスターをリストに登録することを要求するだけで、ご利用のクラスターを数千のノードにスケーリングすることができます。


## <a name="create"></a>作成

**推定所要時間**: 約 5 分です。

Azure Machine Learning コンピューティングは、複数回の実行で再利用できます。 コンピューティングは、ワークスペース内の他のユーザーと共有することができ、複数回の実行の間で保持されます。この場合、送信された実行の回数およびクラスター上で設定された max_nodes に基づいてノードは自動的にスケールアップまたはスケールダウンされます。 min_nodes 設定では、使用可能な最小ノード数が制御されます。

コンピューティング クラスターの作成に適用されるリージョンあたりの専用コア数は、VM ファミリ クォータ別およびリージョン合計クォータ別に、Azure Machine Learning トレーニングのコンピューティング インスタンスのクォータと統合され、共有されます。 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

コンピューティングは、使用されていないときは、0 ノードまで自動的にスケールダウンされます。   必要に応じて、ジョブ実行専用の VM が作成されます。
    
# <a name="python"></a>[Python](#tab/python)


Python で永続的な Azure Machine Learning コンピューティング リソースを作成するには、**vm_size** および **max_nodes** プロパティを指定します。 その後、Azure Machine Learning では他のプロパティに対してスマート既定値が使用されます。
    
* **vm_size**:Azure Machine Learning コンピューティングによって作成されるノードの VM ファミリ。
* **max_nodes**:Azure Machine Learning コンピューティングでジョブを実行中に自動スケールアップする最大ノード数。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Azure Machine Learning コンピューティングを作成するときに、いくつかの詳細プロパティも設定できます。 これらのプロパティを使用すると、永続的なクラスターを固定サイズで、またはサブスクリプションの既存の Azure 仮想ネットワーク内に作成できます。  詳しくは、「[AmlCompute クラス](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)」をご覧ください。

> [!WARNING]
> ワークスペースやデータストアと異なるリージョンで `location` パラメーターを設定すると、ネットワークの待機時間とデータ転送コストが増大する可能性があります。 待機時間とコストは、クラスターの作成時や、クラスターでのジョブの実行時に発生する可能性があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2 --location westus2
```

> [!WARNING]
> ワークスペースやデータストアと異なるリージョンでコンピューティング クラスターを使用すると、ネットワークの待機時間とデータ転送コストが増大する可能性があります。 待機時間とコストは、クラスターの作成時や、クラスターでのジョブの実行時に発生する可能性があります。

詳しくは、「[az ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute)」をご覧ください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

スタジオでコンピューティング クラスターを作成する方法については、[Azure Machine Learning スタジオでのコンピューティング先の作成](how-to-create-attach-compute-studio.md#amlcompute)に関するページを参照してください。

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a>コンピューティング クラスターのコストを削減する

[優先順位の低い VM](how-to-manage-optimize-cost.md#low-pri-vm) を使用して、一部または全部のワークロードを実行することもできます。 これらの VM では、可用性が保証されず、使用中に割り込まれる可能性があります。 割り込まれたジョブを再開する必要があります。 

優先順位の低い VM を指定するには、次のいずれかの方法を使用します。
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

`vm-priority` を設定します。
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

VM を作成するときに、スタジオで **[低優先度]** を選択します。

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> マネージド ID を設定する

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* プロビジョニング構成でマネージド ID を構成します。  

    * `ws` という名前のワークスペースで作成された、システム割り当てマネージド ID
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * `ws` という名前のワークスペースで作成された、ユーザー割り当てマネージド ID
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* `cpu_cluster` という名前の既存のコンピューティング クラスターにマネージド ID を追加します
    
    * システム割り当てマネージド ID:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * ユーザー割り当てマネージド ID:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* マネージド ID を使用して、新しいマネージド コンピューティング クラスターを作成する

  * ユーザー割り当てマネージド ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * システム割り当てマネージド ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* 既存のクラスターにマネージド ID を追加します。

    * ユーザー割り当てマネージド ID
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * システム割り当てマネージド ID

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

[スタジオでマネージド ID を設定する](how-to-create-attach-compute-studio.md#managed-identity)方法に関するページを参照してください。

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>マネージド ID の使用

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>トラブルシューティング

GA リリースの前に Azure portal から Azure Machine Learning ワークスペースを作成したユーザーが、そのワークスペースに AmlCompute を作成できないことがあります。 サービスにサポート リクエストを送るか、ポータルまたは SDK を使って新しいワークスペースを作成することで、すぐにブロックを解除することができます。

ノードの状態に応じてサイズ変更 (0-> 0) を実行しているときに Azure Machine Learning のコンピューティング クラスターが停止したと表示される場合は、Azure リソースのロックが原因である可能性があります。

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>次のステップ

コンピューティング クラスターを使用して以下を行います。

* [トレーニングの実行を送信する](how-to-set-up-training-targets.md) 
* [バッチ推論を実行する](./tutorial-pipeline-batch-scoring-classification.md)
