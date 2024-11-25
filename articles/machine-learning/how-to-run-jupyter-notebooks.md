---
title: ワークスペースで Jupyter Notebook を実行する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオのワークスペースから離れずに Jupyter Notebooks を実行する方法について説明します。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 07/22/2021
ms.openlocfilehash: 1bbc831b32d2a29cf590f36ea0f2c436f739ee6b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076701"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>ワークスペースで Jupyter Notebook を実行する

Azure Machine Learning スタジオのワークスペースで Jupyter Notebooks を直接実行する方法について説明します。 [Jupyter](https://jupyter.org/) または [JupyterLab](https://jupyterlab.readthedocs.io) を起動できますが、ワークスペースから離れずにノートブックを編集して実行することもできます。

ノートブックを含むファイルを作成および管理する方法の詳細については、[ワークスペース内のファイルの作成と管理](how-to-manage-files.md)に関するページを参照してください。

> [!IMPORTANT]
> (プレビュー) とマークされている機能はサービス レベル アグリーメントなしで提供されています。実稼働環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

## <a name="edit-a-notebook"></a>ノートブックを編集する

ノートブックを編集するには、ワークスペースの **[User files]\(ユーザー ファイル\)** セクションにあるノートブックを開きます。 編集するセルをクリックします。  このセクションにノートブックがない場合は、[ワークスペース内のファイルの作成と管理](how-to-manage-files.md)に関するページを参照してください。

ノートブックは、コンピューティング インスタンスに接続することなく編集できます。  ノートブックのセルを実行する場合は、コンピューティング インスタンスを選択または作成します。  停止したコンピューティング インスタンスを選択した場合は、最初のセルを実行すると自動的に開始されます。

コンピューティング インスタンスの実行中に、任意の Python ノートブックで [Intellisense](https://code.visualstudio.com/docs/editor/intellisense) によるコード補完を使用することもできます。

ノートブックのツールバーから Jupyter または JupyterLab を起動することもできます。  Azure Machine Learning では、Jupyter または JupyterLab からの更新プログラムの提供やバグの修正は行われません。これは、Microsoft サポートの範囲に含まれないオープン ソース製品であるためです。

## <a name="focus-mode"></a>フォーカス モード

フォーカス モードを使用して現在のビューを拡張すると、アクティブなタブに集中できるようになります。 フォーカス モードでは、Notebook ファイル エクスプローラーが非表示になります。

1. ターミナル ウィンドウのツールバーで、 **[フォーカス モード]** を選択してフォーカス モードをオンにします。 ウィンドウの幅によっては、このツールはツール バーの **[...]** メニュー項目の下にあります。
1. フォーカス モードになっているときに標準ビューに戻るには、 **[標準ビュー]** を選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="フォーカス モード/標準ビューの切り替え":::

## <a name="code-completion-intellisense"></a>コード補完 (IntelliSense)

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) は、多くの機能 (メンバーの一覧表示、パラメーター ヒント、クイック ヒント、入力候補) を備えたコード補完支援機能です。 ほんの数回のキー ストロークで、次のことができます。
* 使用しているコードの詳細を把握する
* 入力しているパラメーターを追跡する
* プロパティおよびメソッドへの呼び出しを追加する 

### <a name="insert-code-snippets-preview"></a>コード スニペットを挿入する (プレビュー)

IntelliSense をトリガーするには、**Ctrl + Space** キーを使用します。  候補をスクロールするか、入力を開始して、挿入するコードを見つけます。  コードを挿入したら、引数をタブ移動して、独自の用途に合わせてコードをカスタマイズします。

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="コード スニペットの挿入":::

VS Code でノートブックを開いたときにも、同じスニペットを使用できます。 使用可能なスニペットの完全な一覧については、[Azure Machine Learning VS Code スニペット](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md)に関する記事を参照してください。

ノートブック ツール バーを使用してスニペット パネルを開くことで、スニペットの一覧を参照および検索することができます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="ノートブック ツール バーでスニペット パネル ツールを開く":::

スニペット パネルでは、新しいスニペットを追加する要求を送信することもできます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="スニペット パネルを使用して新しいスニペットを提案できる":::

## <a name="collaborate-with-notebook-comments-preview"></a>ノートブックのコメントを使用して共同作業する (プレビュー)

ノートブックのコメントを使用して、ノートブックにアクセスできる他のユーザーと共同作業を行います。

ノートブックの上部にある **[Notebook comments]\(ノートブックのコメント\)** ツールを使用して、コメント ペインのオンとオフを切り替えます。  画面の幅が十分でない場合は、ツールのセットの最後にある **[...]** を最初に選択して、このツールを探します。

:::image type="content" source="media/how-to-run-jupyter-notebooks/notebook-comments-tool.png" alt-text="上部のツール バーにあるノートブックのコメント ツールのスクリーンショット。":::  

コメント ペインが表示されているかどうかにかかわらず、任意のコード セルにコメントを追加できます。

1. コード セルでテキストを選択します。  コメントを作成できるのは、コード セル内のテキストのみです。
1. **[New comment thread]\(新しいコメント スレッド\)** ツールを使用して、コメントを作成します。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/comment-from-code.png" alt-text="コード セルへのコメント追加ツールのスクリーンショット。":::
1. コメント ペインがそれまで非表示だった場合は、これで開かれます。  
1. コメントを入力して、ツールで投稿するか、**Ctrl + Enter** キーを使用します。
1. コメントが投稿された後、次のことを行うには右上の **[...]** を選択します。
    * コメントを編集する
    * スレッドを解決する
    * スレッドを削除する

コメントが付いているテキストは、コード内で紫色の強調表示を使用して表示されます。 コメント ペインでコメントを選択すると、強調表示されたテキストが含まれるセルまでノートブックがスクロールします。

> [!NOTE]
> コメントは、コード セルのメタデータに保存されます。

## <a name="clean-your-notebook-preview"></a>ノートブックをクリーンアップする (プレビュー)

ノートブックを作成しているうちに、一般的にはデータの探索やデバッグに使用したセルが溜まっていきます。 *収集* 機能を使用すると、これらの不要なセルが含まれないクリーンなノートブックを作成できます。

1. すべてのノートブック セルを実行します。
1. 新しいノートブックを実行するコードが含まれているセルを選択します。 たとえば、実験を送信するコードや、あるいはモデルを登録するコードなどです。
1. セルのツールバーに表示される **[収集]** アイコンを選択します。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="スクリーンショット: [収集] アイコンを選択します":::
1. 新しい "収集された" ノートブックの名前を入力します。  

新しいノートブックにはコード セルのみが含まれており、収集するよう選択したセルと同じ結果を得るために必要なすべてのセルが含まれています。

## <a name="save-and-checkpoint-a-notebook"></a>ノートブックを保存およびチェックポイントする

*ipynb* ファイルを作成すると、Azure Machine Learning によってチェックポイント ファイルが作成されます。

ノートブック ツール バーでメニューを選択し、 **[ファイル] &gt; [Save and checkpoint]\(保存とチェックポイント\)** の順に選択して、手動でノートブックを保存します。そうすると、ノートブックに関連付けられたチェックポイント ファイルが追加されます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="ノートブック ツール バーの保存ツールのスクリーンショット":::

すべてのノートブックは 30 秒ごとに自動保存されます。 自動保存では、チェックポイント ファイルではなく、最初の *ipynb* ファイルのみが更新されます。
 
ノートブックのメニューで **[チェックポイント]** を選択して、名前付きチェックポイントを作成し、保存されているチェックポイントにノートブックを戻すことができます。

## <a name="export-a-notebook"></a>ノートブックをエクスポートする

ノートブック ツールバーで、メニュー、 **[次としてエクスポート]** の順に選択して、サポートされているいずれかの種類としてノートブックをエクスポートします。

* ノートブック
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="ノートブックをコンピューターにエクスポートする":::

エクスポートしたファイルは、お使いのコンピューターに保存されます。

## <a name="run-a-notebook-or-python-script"></a>ノートブックまたは Python スクリプトを実行する

ノートブックまたは Python スクリプトを実行するには、まず、実行中の[コンピューティング インスタンス](concept-compute-instance.md)に接続します。

* コンピューティング インスタンスがない場合は、次の手順に従って作成します。

    1. ノートブックまたはスクリプト ツールバーで、[コンピューティング] ドロップダウンの右側にある **[+ 新しいコンピューティング]** を選択します。 画面のサイズによっては、これは **[...]** メニューの下に配置されている場合があります。
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="新しいコンピューティングを作成する":::
    1. コンピューティングに名前を付け、 **[仮想マシン サイズ]** を選択します。 
    1. **［作成］** を選択します
    1. コンピューティング インスタンスがファイルに自動的に接続されます。  これで、コンピューティング インスタンスの左側にあるツールを使用して、ノートブック セルまたは Python スクリプトを実行できるようになります。

* コンピューティング インスタンスが停止している場合は、[コンピューティング] ドロップダウンの右側にある **[コンピューティングの開始]** を選択します。 画面のサイズによっては、これは **[...]** メニューの下に配置されている場合があります。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="コンピューティング インスタンスを開始する":::
    
コンピューティング インスタンスに接続したら、ツール バーを使ってノートブック内のすべてのセルを実行するか、Ctrl + Enter キーを使って、選択した 1 つのセルを実行します。 

作成したコンピューティング インスタンスを表示および使用できるのは自分のみです。  **ユーザー ファイル** は VM とは別に格納され、ワークスペース内のすべてのコンピューティング インスタンス間で共有されます。

### <a name="view-logs-and-output"></a>ログと出力を表示する

[ノートブック ウィジェット](/python/api/azureml-widgets/azureml.widgets)を使用して、実行の進行状況とログを表示します。 ウィジェットは非同期であり、トレーニングが終了するまで更新情報が表示されます。 Azure Machine Learning ウィジェットは、Jupyter および JupterLab でもサポートされています。

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="スクリーンショット: Jupyter Notebooks  ウィジェット ":::

## <a name="explore-variables-in-the-notebook"></a>ノートブックの変数を調べる

Notebook ツールバーの **変数エクスプローラー** ツールを使用すると、ノートブックに作成されているすべての変数の名前、型、長さ、サンプル値が表示されます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="スクリーンショット: 変数エクスプローラー ツール":::

ツールを選択して、変数エクスプローラー ウィンドウを表示します。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="スクリーンショット: 変数エクスプローラー ウィンドウ":::

## <a name="navigate-with-a-toc"></a>目次を使用して移動する

目次を表示または非表示にするには、ノートブック ツールバーの **[目次]** ツールを使用します。  見出しを使用してマークダウン セルを開始し、それを目次に追加します。 目次のエントリをクリックして、ノートブック内のそのセルまでスクロールします。  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="スクリーンショット: ノートブックの目次":::

## <a name="change-the-notebook-environment"></a>ノートブック環境を変更する

ノートブックのツールバーを使用すると、ノートブックを実行する環境を変更できます。  

このようなアクションを実行しても、ノートブックの状態またはノートブック内の変数の値は変更されません。

|アクション  |結果  |
|---------|---------| --------|
|カーネルを停止する     |  任意の実行中のセルを停止します。 セルを実行すると、カーネルが自動的に再起動されます。 |
|別のワークスペース セクションに移動する     |     実行中のセルは停止されます。 |

これらのアクションを実行すると、ノートブックの状態はリセットされ、ノートブック内のすべての変数がリセットされます。

|アクション  |結果  |
|---------|---------| --------|
| カーネルを変更する | Notebook に新しいカーネルが使用されます |
| コンピューティングを切り替える    |     Notebook には新しいコンピューティングが自動的に使用されます。 |
| コンピューティングをリセットする | セルを実行しようとすると再起動します |
| コンピューティングを停止する     |    セルは実行されません  |
| Jupyter または JupyterLab でノートブックを開く     |    新しいタブでノートブックが開きます。  |

## <a name="add-new-kernels"></a>新しいカーネルを追加する

[ターミナルを使用](how-to-access-terminal.md#add-new-kernels)して、新しいカーネルを作成し、コンピューティング インスタンスに追加します。 ノートブックによって、接続されたコンピューティング インスタンスにインストールされているすべての Jupyter カーネルが自動的に検出されます。

右側にあるカーネルのドロップダウンを使用して、インストールされているカーネルのいずれかに変更します。  


### <a name="status-indicators"></a>状態インジケーター

**[コンピューティング]** ドロップダウンの横のインジケーターには、その状態が表示されます。  状態は、ドロップダウン リストにも表示されます。  

|Color |コンピューティング状態 |
|---------|---------| 
| [緑] | コンピューターは実行中 |
| [赤] |コンピューティングの失敗 | 
| Black | コンピューティングの停止 |
|  淡い青 |コンピューティングの作成、開始、再起動、設定 |
|  グレー |コンピューティングの削除、停止 |

**[カーネル]** ドロップダウンの横にあるインジケーターには、その状態が表示されます。

|Color |カーネルの状態 |
|---------|---------|
|  [緑] |カーネル接続済み、アイドル、ビジー|
|  グレー |カーネルが接続されていません |

## <a name="find-compute-details"></a>コンピューティングの詳細を確認する

コンピューティング インスタンスの詳細については、[Studio](https://ml.azure.com) の **コンピューティング** に関するページを参照してください。

## <a name="useful-keyboard-shortcuts"></a>便利なキーボード ショートカット
Jupyter Notebook と同様に、Azure Machine Learning Studio ノートブックにはモーダル ユーザー インターフェイスがあります。 キーボードの動作は、ノートブック セルのモードによって異なります。 Azure Machine Learning Studio ノートブックでは、特定のコード セルに対して、コマンド モードと編集モードという 2 つのモードがサポートされています。

### <a name="command-mode-shortcuts"></a>コマンド モードのショートカット

入力を求めるテキスト カーソルがない場合、セルはコマンド モードになります。 セルがコマンド モードの場合、ノートブックを全体として編集できますが、個々のセルに入力することはできません。 `ESC` キーを押すか、マウスを使用してセルのエディター領域の外側を選択し、コマンド モードに入ります。  アクティブなセルの左側の境界線は青い実線で、 **[実行]** ボタンは青です。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="コマンド モードのノートブック セル ":::

| ショートカット                      | 説明                          |
| ----------------------------- | ------------------------------------|
| Enter                         | 編集モードを開始する             |        
| Shift + Enter                 | セルを実行し、下のものを選択する         |     
| Ctrl/Command + Enter       | セルの実行                            |
| Alt + Enter                   | セルを実行し、下にコード セルを挿入する    |
| Ctrl/Command + Alt + Enter | セルを実行し、下にマークダウン セルを挿入する|
| Alt + R                       | [すべて実行]      |                       
| Y                             | セルをコードに変換する    |                         
| M                             | セルをマークダウンに変換する  |                       
| ↑/K                          | 上のセルを選択する    |               
| ↓/J                        | 下のセルを選択する    |               
| A                             | 上にコード セルを挿入する  |            
| B                             | 下にコード セルを挿入する   |           
| Ctrl/Command + Shift + A   | 上にマークダウン セルを挿入する    |      
| Ctrl/Command + Shift + B   | 下にマークダウン セルを挿入する   |       
| X                             | 選択したセルを切り取る    |               
| C                             | 選択したセルをコピーする   |               
| Shift + V                     | 選択したセルを上に貼り付ける           |
| V                             | 選択したセルを下に貼り付ける    |       
| D D                           | 選択したセルを削除する|                
| O                             | TextWriter         |              
| Shift + O                     | 出力のスクロールを切り替える   |          
| I I                           | カーネルを中断する |                   
| 0 0                           | カーネルを再開する |                     
| Shift + Space                 | 上にスクロール  |                         
| Space                         | 下にスクロール|
| タブ                           | フォーカスを次のフォーカス可能な項目に移動する (タブ トラップが無効になっている場合)|
| Ctrl/Command + S           | ノートブックを保存する |                      
| 1                             | h1 に変更する|                       
| 2                             | h2 に変更する|                        
| 3                             | h3 に変更する|                        
| 4                             | h4 に変更する |                       
| 5                             | h5 に変更する |                       
| 6                             | h6 に変更する |                       

### <a name="edit-mode-shortcuts"></a>編集モードのショートカット

編集モードは、エディター領域への入力を求めるテキスト カーソルによって示されます。 セルが編集モードの場合、セルに入力することができます。 `Enter` キーを押すか、マウスを使用してセルのエディター領域を選択し、編集モードに入ります。 アクティブなセルの左側の境界線は緑のハッチで、 **[実行]** ボタンは緑です。 また、編集モードではセルにカーソル プロンプトが表示されます。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="編集モードのノートブック セル":::

次のキーストローク ショートカットを使用すると、編集モードのときに Azure Machine Learning ノートブックのコードをより簡単に移動して実行できます。

| ショートカット                      | 説明|                                     
| ----------------------------- | ----------------------------------------------- |
| エスケープ                        | コマンド モードを開始する|  
| Ctrl/Command + Space       | IntelliSense をアクティブにする |
| Shift + Enter                 | セルを実行し、下のものを選択する |                         
| Ctrl/Command + Enter       | セルの実行  |                                      
| Alt + Enter                   | セルを実行し、下にコード セルを挿入する  |              
| Ctrl/Command + Alt + Enter | セルを実行し、下にマークダウン セルを挿入する  |          
| Alt + R                       | すべてのセルを実行する     |                              
| ［上へ］                            | カーソルを上または前のセルに移動する    |             
| [下へ]                          | カーソルを下または次のセルに移動する |                  
| Ctrl/Command + S           | ノートブックを保存する   |                                
| Ctrl/Command + ↑          | セルの先頭に移動する   |                             
| Ctrl/Command + ↓        | セルの末尾に移動する |                                 
| タブ                           | コード補完またはインデント (タブ トラップが有効になっている場合) |
| Ctrl/Command + M           | タブ トラップを有効または無効にする  |                       
| Ctrl/Command + ]           | インデントする |                                         
| Ctrl/Command + [           | インデントを解除する  |                                        
| Ctrl/Command + A           | すべて選択する|                                      
| Ctrl/Command + Z           | 元に戻す |                                           
| Ctrl/Command + Shift + Z   | やり直す |                                           
| Ctrl/Command + Y           | やり直す |                                           
| Ctrl/Command + Home        | セルの先頭に移動する|                                
| Ctrl/Command + End         | セルの末尾に移動する   |                               
| Ctrl/Command + 左        | 1 単語左に移動する |                               
| Ctrl/Command + →       | 1 単語右に移動する |                              
| Ctrl/Command + Backspace   | 前の単語を削除する |                             
| Ctrl/Command + Delete      | 後の単語を削除する |                              
| Ctrl/Command + /           | セルでコメントを切り替える

## <a name="troubleshooting"></a>トラブルシューティング

* ノートブックに接続できない場合は、Web ソケット通信が無効になって **いない** ことを確認してください。 コンピューティング インスタンスの Jupyter 機能を動作させるには、Web ソケット通信を有効にする必要があります。 *.instances.azureml.net と *.instances.azureml.ms への [WebSocket 接続がお使いのネットワークで許可されている](how-to-access-azureml-behind-firewall.md?tabs=ipaddress#microsoft-hosts)ことを確認してください。 

* プライベート エンドポイントのあるワークスペースにデプロイされたコンピューティング インスタンスには、[仮想ネットワーク内からのみアクセスする](./how-to-secure-training-vnet.md)ことができます。 カスタム DNS またはホスト ファイルを使用している場合は、ワークスペースのプライベート エンドポイントのプライベート IP アドレスを使用して < instance-name >.< region >.instances.azureml.ms のエントリを追加してください。 詳細については、[カスタム DNS](./how-to-custom-dns.md?tabs=azure-cli)に関する記事をご覧ください。

* カーネルがクラッシュして再起動された場合は、次のコマンドを実行して jupyter ログを表示し、詳細を確認することができます: `sudo journalctl -u jupyter`。 カーネルの問題が解決しない場合は、さらに大きなメモリでコンピューティング インスタンスを使用することを検討してください。

* トークンの期限切れの問題が発生する場合は、Azure ML スタジオからサインアウトし、再度サインインしてから、ノートブック カーネルを再起動します。
    
## <a name="next-steps"></a>次のステップ

* [最初の実験を実行する](tutorial-1st-experiment-sdk-train.md)
* [スナップショットを使用してファイル ストレージをバックアップする](../storage/files/storage-snapshots-files.md)
* [セキュリティで保護された環境での作業](./how-to-secure-training-vnet.md#compute-cluster)
