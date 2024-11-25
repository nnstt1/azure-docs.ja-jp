---
title: コンピューティング ノードでジョブを準備し完了するタスクの作成
description: Azure Batch コンピューティング ノード間のデータ転送を最小にするにはジョブ レベルの準備タスクを使用し、ジョブの完了時にノードをクリーンアップするには解放タスクを使用します。
ms.topic: how-to
ms.date: 09/10/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 66be694cc05655973afac088066e95c6d7033bb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659742"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Batch コンピューティング ノードでのジョブ準備タスクとジョブ解放タスクの実行

 Azure Batch のジョブでは、タスクを実行する前に何らかの形式のセットアップが必要になることがよくあります。 また、タスクの完了時にジョブ実行後のメンテナンスが必要になる場合もあります。 たとえば、タスクに共通する入力データをコンピューティング ノードにダウンロードしたり、ジョブの完了後にタスクの出力データを Azure Storage にアップロードしたりすることが必要になる場合があります。 **ジョブの準備** タスクと **ジョブの解放** タスクを使用して、これらの操作を実行できます。

## <a name="what-are-job-preparation-and-release-tasks"></a>ジョブの準備タスクと解放タスク

ジョブのタスクが実行される前に、1 つ以上のタスクの実行がスケジュールされているすべてのコンピューティング ノードで、ジョブの準備タスクが実行されます。 ジョブが完了すると、少なくとも 1 つのタスクを実行したプールの各ノードでジョブ解放タスクが実行されます。 通常の Batch タスクと同様に、ジョブの準備タスクまたは解放タスクが実行されるときに呼び出されるコマンド ラインを指定できます。

ジョブの準備タスクとジョブの解放タスクは、ファイルのダウンロード ([リソース ファイル](/dotnet/api/microsoft.azure.batch.jobpreparationtask.resourcefiles))、管理者特権での実行、カスタム環境変数、最大実行期間、再試行回数、ファイルのリテンション期間などの使い慣れた Batch タスク機能を提供します。

以下のセクションでは、[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリの [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) クラスと [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) クラスの使用方法について説明します。

> [!TIP]
> ジョブの準備タスクと解放タスクは、"共有プール" 環境で特に役に立ちます。この環境では、コンピューティング ノードのプールが異なるジョブの実行間で維持され、多くのジョブによって使用されます。

## <a name="when-to-use-job-preparation-and-release-tasks"></a>ジョブ準備タスクおよびジョブ解放タスクを使用するのに適した状況

ジョブの準備タスクと解放タスクは、次のような状況に適しています。

- **共通タスク データのダウンロード**: Batch ジョブでは、ジョブのタスクに対する入力として共通のデータ セットが必要になることがよくあります。 たとえば、毎日のリスク分析の計算では、市場データはジョブ固有ですが、そのジョブのすべてのタスクに共通です。 このような市場データはサイズが数ギガバイトになることも多く、各コンピューティング ノードに 1 回だけダウンロードして、そのノードで実行する各タスクがそれを使用できるようにする必要があります。 **ジョブの準備タスク** を使用して、ジョブの他のタスクの実行前に各ノードにこのデータをダウンロードします。

- **ジョブとタスクの出力の削除**: ジョブの合間にプールのコンピューティング ノードが使用停止されない "共有プール" 環境では、実行の合間にジョブ データの削除が必要になる場合があります。 場合によっては、ノードのディスク領域を節約したり、組織のセキュリティ ポリシーを満たしたりする必要があるためです。 ジョブの準備タスクによってダウンロードされた、またはタスクの実行中に生成されたデータを削除するには、**ジョブの解放タスク** を使用します。

- **ログの保有期間**: タスクによって生成されるログ ファイルのコピーや、障害が発生したアプリケーションによって生成される可能性があるクラッシュ ダンプ ファイルの保持が必要な場合があります。 そのような場合は、**ジョブの解放タスク** を使用してこのデータを圧縮し、[Azure ストレージ アカウント](accounts.md#azure-storage-accounts)にアップロードします。

## <a name="job-preparation-task"></a>ジョブの準備タスク

ジョブのタスクを実行する前に、タスクを実行するようにスケジュールされている各コンピューティング ノードで、Batch によってジョブの準備タスクが実行されます。 既定では、Batch では、ジョブの準備タスクが完了するまで待機してから、ノード上で実行するようにスケジュールされたタスクが実行されます。 ただし、完了を待たないようにサービスを構成することもできます。 ノードが再起動されると、ジョブの準備タスクが再度実行されます。 また、この動作を無効にすることもできます。 ジョブの準備タスクとジョブ マネージャー タスクが構成されたジョブがある場合、他のすべてのタスクと同様に、ジョブの準備タスクはジョブ マネージャー タスクの前に実行されます。 ジョブの準備タスクは常に最初に実行されます。

ジョブの準備タスクは、タスクを実行するようにスケジュールされているノードでのみ実行されます。 これにより、ノードにタスクがまったく割り当てられていない場合に、準備タスクが無駄に実行されることがなくなります。 これは、ジョブのタスク数がプール内のノード数より少ない場合に発生する可能性があります。 また、[同時実行タスクの実行](batch-parallel-node-tasks.md)が有効になっている場合も当てはまります。この場合、タスク数が同時実行可能なタスクの総数より少ないと、一部のノードがアイドル状態のままになります。

> [!NOTE]
> [JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) と [CloudPool.StartTask](/dotnet/api/microsoft.azure.batch.cloudpool.starttask) は異なります。JobPreparationTask が各ジョブの開始時に実行されるのに対し、StartTask はコンピューティング ノードが初めてプールに追加されたとき、または再起動したときにのみ実行されます。

## <a name="job-release-task"></a>ジョブの解放タスク

ジョブが完了済みとしてマークされると、少なくとも 1 つのタスクを実行したプールの各ノードでジョブの解放タスクが実行されます。 ジョブを完了済みとして指定するには、終了要求を発行します。 この要求は、ジョブの状態を "*終了中*" に設定し、ジョブに関連付けられているアクティブなタスクまたは実行中のタスクを終了してから、ジョブの解放タスクを実行します。 その後、ジョブは *完了* 状態に移行します。

> [!NOTE]
> ジョブを削除した場合も、ジョブの解放タスクが実行されます。 ただし、ジョブが既に終了している場合は、その後でジョブを削除しても解放タスクが再度実行されることはありません。

ジョブの解放タスクは、Batch サービスによって終了されるまでに最大 15 分間実行できます。 詳細については、[REST API のリファレンス ドキュメント](/rest/api/batchservice/job/add#jobreleasetask)に関する記事を参照してください。

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Batch .NET でのジョブ準備タスクとジョブ解放タスク

ジョブの準備タスクを使用するには、[JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) オブジェクトをジョブの [CloudJob.JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) プロパティに割り当てます。 同様に、ジョブの解放タスクを使用するには、[JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) を初期化し、それをジョブの [CloudJob.JobReleaseTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) に割り当てます。

次のコード スニペットで、`myBatchClient` は [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) のインスタンス、`myPool` は Batch アカウント内の既存のプールです。

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

前に述べたとおり、解放タスクはジョブの終了時または削除時に実行されます。 [JobOperations.TerminateJobAsync を使用してジョブを終了します](/dotnet/api/microsoft.azure.batch.joboperations.terminatejobasync)。 [JobOperations.DeleteJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.deletejobasync) を使用してジョブを削除します。 通常は、タスクが完了したときか、定義したタイムアウトに達したときに、ジョブを終了または削除します。

```csharp
// Terminate the job to mark it as completed; this will initiate the
// job release task on any node that executed job tasks. Note that the
// job release task is also executed when a job is deleted, so you don't
// have to call Terminate if you delete jobs after task completion.

await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>GitHub 上のサンプル コード

ジョブの準備タスクとジョブの解放タスクの動作を確認するには、GitHub の [JobPrepRelease](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease) サンプル プロジェクトをご覧ください。 このコンソール アプリケーションは次のことを行います。

1. 2 つのノードを含むプールを作成します。
1. ジョブ準備タスク、ジョブ解放タスク、標準タスクを含むジョブを作成します。
1. ノードの "共有" ディレクトリ内のテキスト ファイルに最初にノード ID を書き込むジョブ準備タスクを実行します。
1. 同じテキスト ファイルにタスク ID を書き込むタスクを各ノードで実行します。
1. すべてのタスクが完了したら (またはタイムアウトに達したら)、各ノードのテキスト ファイルの内容をコンソールに出力します。
1. ジョブが完了したら、ジョブ解放タスクを実行してノードからファイルを削除します。
1. タスクを実行した各ノードのジョブ準備タスクとジョブ解放タスクの終了コードを出力します。
1. ジョブまたはプールの削除を確認できるように実行を一時停止します。

サンプル アプリケーションからの出力は次のようになります。

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> 新しいプールにノードが作成されて起動されるタイミングは不定である (一部のノードだけ他のノードよりも早く、タスクを処理できる状態になることがある) ため、出力結果が異なる場合があります。 たとえばタスクの実行には時間がかからないので、ジョブのすべてのタスクがプール内のいずれか 1 つのノードによって実行される可能性もあります。 このとき、タスクを実行しなかったノードについては、ジョブの準備タスクとジョブの解放タスクが存在しません。

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Azure Portal でのジョブの準備タスクと解放タスクの確認

ジョブとそのタスクのプロパティは、[Azure portal](https://portal.azure.com) を使用して確認できます。 サンプル アプリケーションを実行した後で、そのジョブのタスクで変更された共有テキスト ファイルをダウンロードすることもできます。

次のスクリーンショットに、Azure portal の **準備タスク ブレード** を示します。 タスクの完了後 (ただし、ジョブとプールが削除される前)、*JobPrepReleaseSampleJob* プロパティに移動し、**[準備タスク]** または **[リリース タスク]** をクリックして、それらのプロパティを表示します。

:::image type="content" source="media/batch-job-prep-release/portal-jobprep-01.png" alt-text="Azure portal のジョブの準備タスク プロパティを示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [ジョブとタスクのエラー チェック](batch-job-task-error-checking.md)の詳細を確認してください。
- [アプリケーション パッケージ](batch-application-packages.md)を使用してタスクの実行のために Batch コンピューティング ノードを準備する方法を確認します。
- [Batch コンピューティング ノードにデータとアプリケーションをコピーする](batch-applications-to-pool-nodes.md)さまざまな方法を確認します。
- [Azure Batch ファイル規則ライブラリ](batch-task-output.md#use-the-batch-file-conventions-library-for-net)を使用して、ログおよび他のジョブやタスクの出力データを保持する方法について学習します。
