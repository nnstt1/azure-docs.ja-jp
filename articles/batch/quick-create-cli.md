---
title: クイックスタート - Azure CLI で最初の Batch ジョブを実行する
description: このクイックスタートでは、Azure CLI で Batch アカウントを作成し、Batch ジョブを実行する方法を示します。
ms.topic: quickstart
ms.date: 05/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: eba9bf9fd290c4483fc9caa0efa4f05adbeb08ef
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110476143"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>クイック スタート:Azure CLI で最初の Batch ジョブを実行する

Azure CLI を使用して Batch アカウント、コンピューティング ノード (仮想マシン) のプール、そのプールでタスクを実行するジョブを作成することによって、Azure Batch の使用を開始します。 各サンプル タスクでは、プール ノードの 1 つに対して基本的なコマンドが実行されます。

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートを完了すると、Batch サービスの主要な概念を理解し、より大規模でより現実的なワークロードで Batch を試せるようになります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group#az_group_create) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*QuickstartBatch-rg* という名前のリソース グループを *eastus2* に作成します。

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure ストレージ アカウントを Batch アカウントとリンクできます。 このクイック スタートには必要ありませんが、ストレージ アカウントは、アプリケーションをデプロイしたり、ほとんどの実際のワークロードの入力データと出力データを保存したりする際に役立ちます。 [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用して、リソース グループ内にストレージ アカウントを作成します。

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Batch アカウントを作成する

[az batch account create](/cli/azure/batch/account#az_batch_account_create) コマンドを使用して Batch アカウントを作成します。 コンピューティング リソース (コンピューティング ノードのプール) や Batch ジョブを作成するには、アカウントが必要です。

次の例では、*mybatchaccount* という名前の Batch アカウントを *QuickstartBatch-rg* に作成し、作成したアカウントをリンクします。  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

コンピューティング プールとジョブを作成および管理するには、Batch による認証が必要です。 [az batch account login](/cli/azure/batch/account#az_batch_account_login) コマンドを使用してアカウントにログインします。 ログインしたら、`az batch` コマンドでこのアカウントのコンテキストを使用します。

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>コンピューティング ノードのプールの作成

Batch アカウントが用意できたら、[az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) コマンドを使用して、Linux コンピューティング ノードのサンプル プールを作成します。 次の例では、Ubuntu 16.04 LTS を実行している 2 つの *Standard_A1_v2* ノードで構成される、*mypool* という名前のプールを作成します。 推奨されるノード サイズは、この簡単な例についてパフォーマンスとコストのバランスが取れています。
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

Batch によってすぐにプールが作成されますが、コンピューティング ノードを割り当てて開始するには数分かかります。 この間、プールの状態は `resizing` になります。 プールの状態を確認するには、[az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) コマンドを実行します。 このコマンドによってプールのすべてのプロパティが表示されるため、特定のプロパティを照会できます。 次のコマンドは、プールの割り当ての状態を取得します。

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

プールの状態が変化している間にジョブとタスクを作成するには、次の手順を続行します。 割り当ての状態が `steady` となり、すべてのノードが実行されていると、プールはタスクを実行できるようになります。

## <a name="create-a-job"></a>ジョブの作成

プールを作成できたら、そこで実行するジョブを作成します。 Batch ジョブは、1 つ以上のタスクの論理グループです。 ジョブには、優先度やタスクの実行対象プールなど、タスクに共通する設定が含まれています。 [az batch job create](/cli/azure/batch/job#az_batch_job_create) コマンドを使用して、Batch ジョブを作成します。 次の例では、プール *mypool* 上にジョブ *myjob* を作成します。 最初、ジョブにはタスクがありません。

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>タスクの作成

ここで、[az batch task create](/cli/azure/batch/task#az_batch_task_create) コマンドを使用して、ジョブで実行するいくつかのタスクを作成します。 この例では、同じタスクを 4 つ作成します。 各タスクで `command-line` を実行してコンピューティング ノードで Batch 環境変数を表示した後、90 秒待ちます。 Batch を使用する場合、このコマンド ラインは、アプリまたはスクリプトを指定する場所です。 Batch には、アプリやスクリプトを計算ノードにデプロイする複数の方法が用意されています。

次の Bash スクリプトでは、4 つの並列タスク (*mytask1* から *mytask4*) を作成します。

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

コマンドの出力には、タスクごとの設定が表示されます。 Batch は、これらのタスクをコンピューティング ノードに配布します。

## <a name="view-task-status"></a>タスクの状態の表示

作成したタスクは、プールで実行するために Batch によってキューに登録されます。 ノードが実行できるようになると、タスクが実行されます。

[az batch task show](/cli/azure/batch/task#az_batch_task_show) コマンドを使用して、Batch タスクの状態を表示します。 次の例では、プールのノードのいずれかで実行されている *mytask1* の詳細を表示します。

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

コマンドの出力には、多くの詳細が含まれていますが、タスクのコマンド ラインの `exitCode` と、`nodeId` を書き留めておいてください。 `exitCode` が 0 の場合は、タスクのコマンド ラインが正常に完了したことを示します。 `nodeId` は、タスクが実行されたプール ノードの ID を示します。

## <a name="view-task-output"></a>タスク出力の表示

コンピューティング ノード上にタスクによって作成されたファイルを一覧表示するには、[az batch task file list](/cli/azure/batch/task) コマンドを使用します。 次のコマンドでは、*mytask1* によって作成されたファイルの一覧が表示されます。

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

次のような出力になります。

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

出力ファイルの 1 つをローカル ディレクトリにダウンロードするには、[az batch task file download](/cli/azure/batch/task) コマンドを使用します。 この例では、タスクの出力は `stdout.txt` にあります。

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

`stdout.txt` の内容は、テキスト エディターで表示できます。 内容は、ノードで設定されている Azure Batch 環境変数を示します。 独自の Batch ジョブを作成すると、これらの環境変数は、タスクのコマンド ラインのほか、コマンド ラインにより実行されるプログラムとスクリプトで参照できます。 次に例を示します。

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Batch のチュートリアルとサンプルを続行する場合は、このクイック スタートで作成した Batch アカウントとリンクされているストレージ アカウントを使用します。 Batch アカウント自体の料金は発生しません。

ジョブがスケジュールされていない場合でも、ノードの実行中はプールに対して料金が発生します。 プールが不要になったら、[az batch pool delete](/cli/azure/batch/pool#az_batch_pool_delete) コマンドを使用して削除します。 プールを削除すると、ノード上のタスク出力はすべて削除されます。

```azurecli-interactive
az batch pool delete --pool-id mypool
```

必要がなくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループ、Batch アカウント、プール、およびすべての関連リソースを削除できます。 次のように、リソースを削除します。

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Batch アカウント、Batch プール、Batch ジョブを作成しました。 このジョブによってサンプル タスクが実行されたため、いずれかのノード上に作成された出力が表示されました。 Batch サービスの主要な概念を理解できたので、より大規模でより現実的なワークロードを使用して Batch を試す準備が整いました。 Azure Batch の詳細については、Azure Batch のチュートリアルを続行してください。

> [!div class="nextstepaction"]
> [Azure Batch のチュートリアル](./tutorial-parallel-dotnet.md)
