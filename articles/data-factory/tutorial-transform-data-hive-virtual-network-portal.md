---
title: Azure portal から Azure Virtual Network の Hive を使用してデータを変換する
description: このチュートリアルでは、Azure Data Factory で Hive アクティビティを使用してデータを変換するための詳細な手順を説明します。
ms.service: data-factory
ms.subservice: tutorials
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: f937d03691c21430f0055916b81e3a8963cbceba
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769847"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Azure portal から Azure Data Factory 内の Hive アクティビティを使用して Azure Virtual Network のデータを変換する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Azure Portal を使用して Data Factory パイプラインを作成します。このパイプラインで、Azure Virtual Network (VNet) にある HDInsight クラスター上の Hive アクティビティを使用してデータを変換します。 このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * 自己ホスト型統合ランタイムを作成する
> * Azure Storage および Azure HDInsight のリンクされたサービスを作成する
> * Hive アクティビティを含むパイプラインを作成する。
> * パイプラインの実行をトリガーする。
> * パイプラインの実行を監視します 
> * 出力を検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage アカウント**。 Hive スクリプトを作成し、Azure ストレージにアップロードします。 Hive スクリプトからの出力は、このストレージ アカウントに格納されます。 このサンプルでは、この Azure ストレージ アカウントがプライマリ ストレージとして HDInsight クラスターによって使用されます。 
- **Azure Virtual Network。** Azure 仮想ネットワークを持っていない場合は、[こちらの手順](../virtual-network/quick-create-portal.md)に従って作成してください。 このサンプルでは、HDInsight は Azure 仮想ネットワーク内にあります。 Azure Virtual Network の構成例を次に示します。 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png" alt-text="Create virtual network":::
- **HDInsight クラスター。** HDInsight クラスターを作成し、前の手順で作成した仮想ネットワークに参加させます。手順については、「[Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight/hdinsight-plan-virtual-network-deployment.md)」を参照してください。 仮想ネットワークでの HDInsight の構成例を次に示します。 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png" alt-text="仮想ネットワークでの HDInsight":::
- **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページに記載されている手順に従います。
- **仮想マシン**。 Azure 仮想マシン VM を作成し、HDInsight クラスターが含まれている仮想ネットワークに参加させます。 詳細については、[仮想マシンの作成方法](../virtual-network/quick-create-portal.md#create-virtual-machines)に関するページを参照してください。 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive スクリプトを BLOB ストレージ アカウントにアップロードする

1. 次の内容で、**hivescript.hql** という名前の Hive SQL ファイルを作成します。

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Azure Blob Storage で、**adftutorial** という名前のコンテナーを作成します (存在しない場合)。
3. **hivescripts** という名前のフォルダーを作成します。
4. **hivescript.hql** ファイルを **hivescripts** サブフォルダーにアップロードします。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. [Azure Portal](https://portal.azure.com/) にログインします。    
2. 左側のメニューで **[新規]** をクリックし、 **[データ + 分析]** 、 **[Data Factory]** の順にクリックします。 
   
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png" alt-text="New->DataFactory":::
3. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialHiveFactory**」と入力します。 
      
     :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png" alt-text="[新しいデータ ファクトリ] ページ":::
 
   Azure データ ファクトリの名前は **グローバルに一意** にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
    *データ ファクトリ名 "MyAzureSsisDataFactory" は利用できません*
3. データ ファクトリを作成する Azure **サブスクリプション** を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
     リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
4. **バージョン** として **[V2]** を選択します。
5. データ ファクトリの **場所** を選択します。 データ ファクトリの作成がサポートされている場所のみが一覧に表示されます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、 **[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

     :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png" alt-text="[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル":::
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png" alt-text="データ ファクトリのホーム ページ":::
10. **[Author & Monitor]\(作成と監視\)** をクリックして、別のタブで Data Factory ユーザー インターフェイス (UI) を起動します。
11. ホーム ページで、次の図に示すように、左パネルの **[管理]** タブに切り替えます。 

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="[管理] タブを示すスクリーンショット。":::

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する
Hadoop クラスターは仮想ネットワーク内にあるため、同じ仮想ネットワークにセルフホステッド統合ランタイム (IR) をインストールする必要があります。 このセクションでは、新しい VM を作成し、それを同じ仮想ネットワークに参加させた後、セルフホステッド IR をインストールします。 セルフホステッド IR により、Data Factory サービスは、仮想ネットワーク内の HDInsight などのコンピューティング サービスに処理要求をディスパッチできます。 また、仮想ネットワーク内のデータ ストアと Azure との間でデータを移動することもできます。 セルフホステッド IR は、データ ストアまたはコンピューティングがオンプレミス環境にある場合にも使用します。 

1. Azure Data Factory UI で、ウィンドウの下部にある **[接続]** をクリックします。 **[Integration Runtimes]\(統合ランタイム\)** タブに切り替え、ツール バーの **[+ 新規]** ボタンをクリックします。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png" alt-text="新規統合ランタイム メニュー":::
2. **[Integration Runtime Setup]\(統合ランタイムの設定\)** ウィンドウで、 **[Perform data movement and dispatch activities to external computes]\(データの移動を実行し、アクティビティを外部コンピューティングにディスパッチする\)** オプションを選択し、 **[次へ]** をクリックします。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png" alt-text="データの移動を実行し、アクティビティをディスパッチするオプションを選択する":::
3. **[プライベート ネットワーク]** を選択し、 **[次へ]** をクリックします。
    
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png" alt-text="[プライベート ネットワーク] の選択":::
4. **[名前]** に「**MySelfHostedIR**」と入力し、 **[次へ]** をクリックします。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png" alt-text="統合ランタイム名を指定する"::: 
5. コピー ボタンをクリックして、統合ランタイムの **認証キー** をコピーして保存します。 ウィンドウを開いたままにしておきます。 このキーは、仮想マシンにインストールされている IR を登録するために使用します。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png" alt-text="認証キーをコピーする":::

### <a name="install-ir-on-a-virtual-machine"></a>仮想マシンに IR をインストールする

1. Azure VM で、[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)をダウンロードします。 前の手順で取得した **認証キー** を使用して、セルフホステッド統合ランタイムを手動で登録します。 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png" alt-text="統合ランタイムの登録":::

2. セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。 
   
    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png" alt-text="正常に登録":::
3. **[構成マネージャーの起動]** をクリックします。 ノードがクラウド サービスに接続されると、次のページが表示されます。 
   
    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png" alt-text="ノード接続済み":::

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Azure Data Factory UI のセルフホステッド IR

1. **Azure Data Factory UI** に、セルフホステッド VM の名前とその状態が表示されます。

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png" alt-text="既存のセルフホステッド ノード":::
2. **[完了]** をクリックして **[Integration Runtime Setup]\(統合ランタイムの設定\)** ウィンドウを閉じます。 統合ランタイムの一覧にセルフホステッド IR が表示されます。

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png" alt-text="一覧のセルフホステッド IR":::


## <a name="create-linked-services"></a>リンクされたサービスを作成します

このセクションでは、次の 2 つのリンクされたサービスを作成してデプロイします。
- Azure Storage アカウントをデータ ファクトリにリンクする、**Azure Storage のリンクされたサービス**。 このストレージは、HDInsight クラスターによって使用されるプライマリ ストレージです。 この場合、この Azure Storage アカウントを使用して、Hive スクリプトとスクリプトの出力を保存します。
- **HDInsight のリンクされたサービス**。 Azure Data Factory は、Hive スクリプトを実行するために、スクリプトをこの HDInsight クラスターに送信します。

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

1. **[リンクされたサービス]** タブに切り替え、 **[新規]** をクリックします。

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png" alt-text="新しいリンクされたサービスのボタン":::    
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png" alt-text="[Azure Blob Storage] の選択":::
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。
    2. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** で **[MySelfHostedIR]** を選択します。
    3. **[ストレージ アカウント名]** で、使用する Azure ストレージ アカウントを選択します。 
    4. ストレージ アカウントへの接続をテストするために、 **[テスト接続]** をクリックします。
    5. **[保存]** をクリックします。
   
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png" alt-text="Azure Blob Storage アカウントを指定する":::

### <a name="create-hdinsight-linked-service"></a>HDInsight のリンクされたサービスを作成する

1. **[新規]** ボタンをもう一度クリックして、別のリンクされたサービスを作成します。 
    
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png" alt-text="新しいリンクされたサービスのボタン":::    
2. **[Compute]** タブに切り替えます。 **[Azure HDInsight]** を選択し、 **[続行]** をクリックします。

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png" alt-text="Azure HDInsight を選択する":::
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**AzureHDInsightLinkedService**」と入力します。
    2. **[Bring your own HDInsight]\(自分で HDInsight を用意する\)** を選択します。 
    3. **[Hdi cluster]\(HDI クラスター\)** で、使用する HDInsight クラスターを選択します。 
    4. HDInsight クラスターの **ユーザー名** を入力します。
    5. ユーザーの **パスワード** を入力します。 
    
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png" alt-text="Azure HDInsight の設定":::

この記事では、インターネット経由でクラスターにアクセスできることが前提となっています。 たとえば、`https://clustername.azurehdinsight.net` でクラスターに接続できることが必要です。 このアドレスではパブリック ゲートウェイが使用されていますが、ネットワーク セキュリティ グループ (NSG) またはユーザー定義ルート (UDR) を使用してインターネットからのアクセスが制限されている場合は、このゲートウェイを使用できません。 Data Factory が Azure Virtual Network の HDInsight クラスターにジョブを送信できるようにするには、HDInsight によって使用されるゲートウェイのプライベート IP アドレスに URL を解決できるように、Azure 仮想ネットワークを構成する必要があります。

1. Azure Portal で、HDInsight がある仮想ネットワークを開きます。 名前が `nic-gateway-0` で始まるネットワーク インターフェイスを開きます。 そのプライベート IP アドレスをメモしておきます。 たとえば、10.6.0.15 です。 
2. Azure 仮想ネットワークに DNS サーバーがある場合は、HDInsight クラスターの URL `https://<clustername>.azurehdinsight.net` を `10.6.0.15` に解決できるように、DNS レコードを更新します。 Azure 仮想ネットワークに DNS サーバーがない場合は、セルフホステッド統合ランタイム ノードとして登録されているすべての VM の hosts ファイル (C:\Windows\System32\drivers\etc) を編集し、次のようなエントリを追加することで、一時的にこれを回避することができます。 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>パイプラインを作成する 
この手順では、Hive アクティビティがある新しいパイプラインを作成します。 このアクティビティでは、Hive スクリプトを実行してサンプル テーブルからデータを返し、定義されたパスに保存します。

以下の点に注意してください。

- **scriptPath** は、MyStorageLinkedService に使用した Azure ストレージ アカウントの Hive スクリプトへのパスを示します。 パスの大文字と小文字は区別されます。
- **Output** は、Hive スクリプトで使用される引数です。 `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` の形式で、Azure ストレージ上の既存のフォルダーを指定します。 パスの大文字と小文字は区別されます。 

1. Data Factory UI で、左側のウィンドウの **[+] (プラス記号)** をクリックし、 **[パイプライン]** をクリックします。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png" alt-text="新しいパイプライン メニュー":::
2. **[アクティビティ]** ツールボックスで **[HDInsight]** を展開し、パイプライン デザイナー画面に **Hive** アクティビティをドラッグ アンド ドロップします。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png" alt-text="Hive アクティビティのドラッグ アンド ドロップ":::
3. プロパティ ウィンドウで、 **[HDI cluster]\(HDI クラスター\)** タブに切り替え、 **[HDInsight Linked Service]\(HDInsight のリンクされたサービス\)** で **[AzureHDInsightLinkedService]** を選択します。

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png" alt-text="HDInsight のリンクされたサービスを選択する":::
4. **[スクリプト]** タブに切り替え、次の手順を実行します。 

    1. **[スクリプトにリンクされたサービス]** で **[AzureStorageLinkedService]** を選択します。 
    2. **[ファイル パス]** で、 **[ストレージを参照]** をクリックします。 
 
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png" alt-text="[ストレージを参照]":::
    3. **[Choose a file or folder]\(ファイルまたはフォルダーの選択\)** ウィンドウで、**adftutorial** コンテナーの **hivescripts** フォルダーに移動します。**hivescript.hql** を選択し、 **[完了]** をクリックします。  
        
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png" alt-text="[Choose a file or folder]\(ファイルまたはフォルダーの選択\)"::: 
    4. **[ファイル パス]** に **adftutorial/hivescripts/hivescript.hql** が表示されていることを確認します。

        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png" alt-text="スクリプトの設定":::
    5. **[スクリプト] タブ** で、 **[詳細]** セクションを展開します。 
    6. **[パラメーター]** の **[Auto-fill from script]\(スクリプトから自動入力\)** をクリックします。 
    7. **[出力]** パラメーターの値を `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` 形式で入力します。 (例: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`)。
 
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png" alt-text="[スクリプトの引数]":::
1. アーティファクトを Data Factory に公開するために、 **[発行]** をクリックします。

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png" alt-text="Data Factory に公開するためのオプションを示すスクリーンショット。":::

## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする

1. 最初に、ツール バーの **[検証]** ボタンをクリックして、パイプラインを検証します。 **右矢印 (>>)** をクリックして **[Pipeline Validation Output]\(パイプラインの検証の出力\)** ウィンドウを閉じます。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png" alt-text="パイプラインの検証"::: 
2. パイプラインの実行をトリガーするために、ツール バーの [トリガー] をクリックし、[Trigger Now]\(今すぐトリガー\) をクリックします。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png" alt-text="[Trigger Now]\(今すぐトリガー\)":::

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. 左側で **[監視]** タブに切り替えます。 **[Pipeline Runs]\(パイプラインの実行\)** の一覧にパイプライン実行が表示されます。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png" alt-text="パイプラインの実行を監視する":::
2. 一覧を更新するには、 **[最新の情報に更新]** をクリックします。
4. パイプライン実行に関連付けられているアクティビティの実行を表示するために、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** をクリックします。 これ以外に、パイプラインを停止および再実行するためのアクション リンクがあります。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png" alt-text="アクティビティの実行の表示":::
5. **HDInsightHive** タイプのパイプラインにはアクティビティが 1 つしかないため、表示されるアクティビティ実行は 1 つのみです。 前のビューに戻るために、上部の **[パイプライン]** リンクをクリックします。

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png" alt-text="アクティビティの実行":::
6. **adftutorial** コンテナーの **outputfolder** に出力ファイルがあることを確認します。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png" alt-text="[出力ファイル]":::

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * 自己ホスト型統合ランタイムを作成する
> * Azure Storage および Azure HDInsight のリンクされたサービスを作成する
> * Hive アクティビティを含むパイプラインを作成する。
> * パイプラインの実行をトリガーする。
> * パイプラインの実行を監視します 
> * 出力を検証する

次のチュートリアルに進み、Azure 上の Spark クラスターを使ってデータを変換する方法について学習しましょう。

> [!div class="nextstepaction"]
>[Data Factory の制御フローの分岐とチェーン](tutorial-control-flow-portal.md)
