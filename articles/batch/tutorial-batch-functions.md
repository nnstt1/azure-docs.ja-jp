---
title: チュートリアル - Azure Functions を使用して Batch ジョブをトリガーする
description: チュートリアル - Storage Blob に追加されたときに、スキャン済みのドキュメントに OCR を適用する
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: peshultz
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 02379ee6872564b73441f6756479965912f3925f
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449101"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>チュートリアル:Azure Functions を使用して Batch ジョブをトリガーする

このチュートリアルでは、[Azure Functions](../azure-functions/functions-overview.md) を使用して Batch ジョブをトリガーする方法について説明します。 Azure Storage Blob コンテナーに追加されたドキュメントに Azure Batch を介して光学式文字認識 (OCR) を適用する例を見ていきます。 OCR 処理を効率化するために、BLOB コンテナーにファイルが追加されるたびに Batch OCR ジョブを実行する Azure 関数を構成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Batch Explorer を使用して、プールおよびジョブを作成する
> * Storage Explorer を使用して、BLOB コンテナーと Shared Access Signature (SAS) を作成する
> * BLOB によってトリガーされる Azure 関数を作成する
> * Storage に入力ファイルをアップロードする
> * タスクの実行を監視する
> * 出力ファイルを取得する

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Batch アカウントおよびリンクされた Azure ストレージ アカウント。 アカウントを作成およびリンクする方法の詳細については、「[Batch アカウントを作成する](quick-create-portal.md#create-a-batch-account)」を参照してください。
<<<<<<< HEAD
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
=======
* [Batch Explorer](https://azure.github.io/BatchExplorer/)。
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)。
>>>>>>> repo_sync_working_branch

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Batch Explorer を使用して Batch プールと Batch ジョブを作成する

このセクションでは、Batch Explorer を使用して、OCR タスクを実行する Batch プールと Batch ジョブを作成します。

### <a name="create-a-pool"></a>プールを作成する

1. ご自分の Azure 資格情報を使用して、Batch Explorer にサインインします。
1. 左側のバーにある **[Pools]\(プール\)** 、検索フォームの上にある **[Add]\(追加\)** ボタンの順に選択して、プールを作成します。 
    1. ID と表示名を選択します。 この例では、`ocr-pool` を使用します。
    1. スケールの種類を **[Fixed size]\(固定サイズ\)** に設定し、専用ノードの数を 3 に設定します。
    1. オペレーティング システムとして **[Ubuntuserver]**  >  **[18.04-lts]** を選択します。
    1. 仮想マシンのサイズとして [`Standard_f2s_v2`] を選択します。
    1. 開始タスクを有効にし、コマンド `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"` を追加します。 必ずユーザー ID を **[Task user (Admin)]\(タスクのユーザー (管理者)\)** として設定します。これにより、開始タスクに `sudo` を使用したコマンドを含めることができます。
    1. **[OK]** を選択します。
  
### <a name="create-a-job"></a>ジョブの作成

1. 左側のバーにある **[Jobs]\(ジョブ\)**、検索フォームの上にある **[Add]\(追加\)** ボタンの順に選択して、プール上にジョブを作成します。
   1. ID と表示名を選択します。 この例では、`ocr-job` を使用します。
   1. プールを `ocr-pool` またはご自分のプールに対して選択した名前に設定します。
   1. **[OK]** を選択します。

## <a name="create-blob-containers"></a>BLOB コンテナーを作成する

ここで、OCR Batch ジョブの実際の入力および出力ファイルを格納する BLOB コンテナーを作成します。 この例では、入力コンテナーは `input` という名前で、そこには OCR が適用されていないドキュメントがすべて処理のために最初にアップロードされます。 出力コンテナーは `output` という名前で、そこには OCR が適用された処理済みのドキュメントが Batch ジョブによって書き込まれます。

1. ご自分の Azure 資格情報を使用して、[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) にサインインします。
1. ご自分の Batch アカウントにリンクされているストレージ アカウントを使用し、「[BLOB コンテナーを作成する](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)」の手順に従って、2 つの BLOB コンテナー (1 つは入力ファイル用、1 つは出力ファイル用) を作成します。
1. Storage Explorer 内の出力コンテナー用に Shared Access Signature を作成するために、出力コンテナーを右クリックして、 **[Shared Access Signature の取得...]** を選択します。 **[アクセス許可]** で、 **[書き込み]** を選択します。 それ以外のアクセス許可は必要ありません。  

## <a name="create-an-azure-function"></a>Azure Function の作成

このセクションでは、ファイルがご自分の入力コンテナーにアップロードされるたびに OCR Batch ジョブをトリガーする Azure 関数を作成します。

1. 「[Azure Blob Storage によってトリガーされる関数の作成](../azure-functions/functions-create-storage-blob-triggered-function.md)」の手順に従って、関数を作成します。
    1. **[ランタイム スタック]** で [.NET] を選択します。 Batch .NET SDK を活用するために、C# で関数を作成します。
    1. **[Hosting]\(ホスティング\)** でストレージ アカウントを求められたら、Batch アカウントにリンクしたのと同じストレージ アカウントを使用します。
    1. Azure Blob Storage アカウント トリガーを作成する際は、パスを (入力コンテナーの名前と一致するように) `input/{name}` に設定してください。
1. BLOB によってトリガーされる関数が作成されたら、 **[Code + Test]\(コードとテスト\)** を選択します。 関数内で GitHub の [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) と [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) を使用します。 `function.proj` は既定では存在しないので、 **[アップロード]** ボタンを選択して開発ワークスペースにアップロードします。
    * `run.csx` は、ご自分の入力 BLOB コンテナーに新しい BLOB が追加されると実行されます。
    * `function.proj` は、Batch .NET SDK など、ご自分の関数コード内で外部ライブラリを列挙します。
1. `run.csx` ファイルの `Run()` 関数内にある変数のプレースホルダーの値を変更して、ご自分の Batch およびストレージの資格情報を反映させます。 ご自分の Batch およびストレージ アカウントの資格情報は、Azure portal 内にあるご自分の Batch アカウントの **[キー]** セクションで確認できます。
    * Azure portal 内にあるご自分の Batch アカウントの **[キー]** セクションでご自分の Batch およびストレージ アカウントの資格情報を取得します。 


## <a name="trigger-the-function-and-retrieve-results"></a>関数をトリガーし、結果を取得する

スキャン済みファイルの一部またはすべてを GitHub 上の [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) ディレクトリからご自分の入力コンテナーにアップロードします。 Batch Explorer を監視して、各ファイルのタスクが `ocr-pool` に追加されることを確認します。 数秒後に、OCR が適用されたファイルが出力コンテナーに追加されます。 その後、ファイルは Storage Explorer 上で表示および取得できるようになります。

さらに、Azure Functions Web エディター ウィンドウの下部にあるログ ファイルを確認できます。そこには、ご自分の入力コンテナーにアップロードしたすべてのファイルに関する、このようなメッセージが表示されます。

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Storage Explorer からご使用のローカル マシンに出力ファイルをダウンロードするには、まず対象のファイルを選択した後、上部のリボンにある **[Download]\(ダウンロード\)** を選択します。

> [!TIP]
> ダウンロードしたファイルは、PDF リーダーで開くと、検索できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ジョブがスケジュールされていない場合でも、ノードの実行中はプールに対して料金が発生します。 不要になったプールは、次の手順を使用して削除してください。

1. アカウント ビューで、**[プール]** およびプールの名前を選択します。
1. **[削除]** を選択します。

プールを削除すると、ノード上のタスク出力はすべて削除されます。 ただし、出力ファイルはストレージ アカウントに残ります。 Batch アカウントとストレージ アカウントも、不要になったら削除できます。

## <a name="next-steps"></a>次のステップ

.NET API を使用して Batch ワークロードのスケジュール設定と処理を行う他の例については、GitHub のサンプルを参照してください。

> [!div class="nextstepaction"]
> [Batch C# のサンプル](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
