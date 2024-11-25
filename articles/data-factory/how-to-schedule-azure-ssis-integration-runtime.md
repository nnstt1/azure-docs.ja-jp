---
title: Azure-SSIS Integration Runtime のスケジュールを設定する方法
description: この記事では、Azure Data Factory を使用して、Azure-SSIS Integration Runtime の開始と停止のスケジュールを設定する方法を説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7615c7d3d0556aef2bd2f73148ac986709082ce0
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845724"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Azure-SSIS Integration Runtime をスケジュールに従って開始および停止する方法

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Factory (ADF) を使用して、Azure-SSIS Integration Runtime (IR) の開始と停止のスケジュールを設定する方法を説明します。 Azure-SSIS IR は、SQL Server Integration Services (SSIS) パッケージの実行専用の ADF コンピューティング リソースです。 Azure-SSIS IR を実行するには、それに関連するコストがあります。 このため一般には、SSIS パッケージを Azure で実行する必要がある場合にのみ IR を実行し、必要ないときには IR を停止する必要があります。 ADF のユーザー インターフェイス (UI)/アプリまたは Azure PowerShell を使用して、[IR を手動で開始または停止する](manage-azure-ssis-integration-runtime.md)ことができます。

または、ADF パイプラインで Web アクティビティを作成し、スケジュールに従って IR を開始/停止することができます。たとえば、朝、毎日の ETL ワークロードを実行する前に開始し、午後、ワークロードが完了した後で停止することができます。  また、IR を開始および停止する 2 つの Web アクティビティの間を SSIS パッケージの実行アクティビティで連結し、パッケージ実行の直前/直後に必要に応じて IR を開始/停止することもできます。 SSIS パッケージの実行アクティビティについて詳しくは、「[Azure Data Factory の SSIS パッケージの実行アクティビティを使用して SSIS パッケージを実行する](how-to-invoke-ssis-package-ssis-activity.md)」をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
Azure-SSIS IR をまだプロビジョニングしていない場合は、[チュートリアル](./tutorial-deploy-ssis-packages-azure.md)の手順に従ってプロビジョニングします。 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Azure-SSIS IR を開始および停止する ADF パイプラインを作成してスケジュールする
このセクションでは、ADF パイプラインの Web アクティビティを使用して、スケジュールに従って、またはオンデマンドで、Azure-SSIS IR を開始/停止する方法を説明します。 3 つのパイプラインを作成します。 

1. 1 つ目のパイプラインには、Azure-SSIS IR を開始する Web アクティビティが含まれます。 
2. 2 つ目のパイプラインには、Azure-SSIS IR を停止する Web アクティビティが含まれます。
3. 3 つ目のパイプラインには、Azure-SSIS IR を開始/停止する 2 つの Web アクティビティの間を連結する SSIS パッケージの実行アクティビティが含まれます。 

パイプラインを作成してテストしたら、スケジュール トリガーを作成して、パイプラインに関連付けることができます。 スケジュール トリガーによって、関連するパイプラインの実行スケジュールを定義します。 

たとえば、2 つのトリガーを作成し、1 つ目のトリガーは毎日午前 6 時に実行するようにスケジュールして 1 つ目のパイプラインに関連付け、2 つ目のトリガーは毎日午後 6 時に実行するようにスケジュールして 2 つ目のパイプラインに関連付けることができます。  このようにすると、毎日午前 6 時から午後 6 時までは IR が実行されていて、毎日の ETL ワークロードを実行できる状態になります。  

3 つ目のトリガーを作成し、毎日午前 0 時に実行するようにスケジュールして 3 つ目のパイプラインに関連付けた場合、そのパイプラインは毎日午前 0 時に実行するとパッケージ実行の直前に IR を開始し、パッケージを実行して、パッケージ実行直後に IR を停止するので、IR が無駄に実行されることがなくなります。

### <a name="create-your-adf"></a>ADF を作成する

1. [Azure ポータル](https://portal.azure.com/)にサインインします。    
2. 左側のメニューで **[新規]** をクリックし、 **[データ + 分析]** 、 **[Data Factory]** の順にクリックします。 
   
   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png" alt-text="[新規] -> [Data Factory]":::
   
3. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**MyAzureSsisDataFactory**」と入力します。 
      
   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png" alt-text="[新しいデータ ファクトリ] ページ":::
 
   ADF の名前は、グローバルに一意である必要があります。 次のエラーが発生する場合は、ADF の名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 ADF アーティファクトの名前付け規則については、「[Azure Data Factory - 名前付け規則](naming-rules.md)」をご覧ください。
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. ADF を作成する Azure **サブスクリプション** を選択します。 
5. **リソース グループ** について、次の手順のいずれかを行います。
     
   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、新しいリソース グループの名前を入力します。   
         
   リソース グループについて詳しくは、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関する記事をご覧ください。
   
6. **[バージョン]** で、 **[V2]** を選択します。
7. **[場所]** で、ドロップダウン リストから ADF の作成がサポートされている場所のいずれかを選択します。
8. **[ダッシュボードにピン留めする]** をオンにします。     
9. **Create** をクリックしてください。
10. Azure ダッシュボードに、次のようなタイルと状態が表示されます: **[Deploying data factory]\(データ ファクトリをデプロイしています\)** 。 

    :::image type="content" source="media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png" alt-text="[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル":::
   
11. 作成が完了すると、次に示すような ADF ページが表示されます。
   
    :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png" alt-text="データ ファクトリのホーム ページ":::
   
12. **[作成と監視]** をクリックして、別のタブで ADF UI/アプリを起動します。

### <a name="create-your-pipelines"></a>パイプラインを作成する

1. ホーム ページで **[Orchestrate]\(調整\)** を選択します。 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF のホーム ページを示すスクリーンショット。":::
   
2. **[アクティビティ]** ツールボックスの **[全般]** メニューを展開し、 **[Web]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティ プロパティ ウィンドウの **[全般]** タブで、アクティビティの名前を「**startMyIR**」に変更します。 **[設定]** タブに切り替え、次の操作を実行します。

   1. **[URL]** に、Azure-SSIS IR を開始する REST API の URL、`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` を入力して、`{subscriptionId}`、`{resourceGroupName}`、`{factoryName}`、`{integrationRuntimeName}` を、自分の IR の実際の値に置き換えます。  または、ADF UI/アプリの監視ページから、IR のリソース ID をコピーして貼り付け、上記の URL の次の部分を置き換えてもかまいません。 `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png" alt-text="ADF SSIS IR のリソース ID":::
  
   2. **[メソッド]** では、 **[POST]** を選択します。 
   3. **[本文]** には、「`{"message":"Start my IR"}`」を入力します。
   4. **[認証]** で **[マネージド ID]** を選択して、指定したシステム マネージド ID を ADF に使用します。詳しくは、「[Data Factory のマネージド ID](./data-factory-service-identity.md)」を参照してください。
   5. **[リソース]** に、「`https://management.azure.com/`」と入力します。
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png" alt-text="ADF Web アクティビティのスケジュールの SSIS IR":::
  
3. 1 つ目のパイプラインを複製して 2 つ目を作成し、アクティビティ名を「**stopMyIR**」に変更して、次のプロパティを置き換えます。

   1. **[URL]** に、Azure-SSIS IR を停止する REST API の URL、`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01` を入力して、`{subscriptionId}`、`{resourceGroupName}`、`{factoryName}`、`{integrationRuntimeName}` を、自分の IR の実際の値に置き換えます。
   2. **[本文]** には、「`{"message":"Stop my IR"}`」を入力します。 

4. 3 つ目のパイプラインを作成し、 **[アクティビティ]** ツールボックスから **[SSIS パッケージの実行]** アクティビティをドラッグして、パイプライン デザイナー画面にドロップします。そして、[ADF で SSIS パッケージの実行アクティビティを使用して、SSIS パッケージを呼び出す方法](how-to-invoke-ssis-package-ssis-activity.md)に関する記事に従って構成します。  次に、1 つ目/2 つ目のパイプラインでの Web アクティビティと同じように、IR を開始/停止する 2 つの Web アクティビティの間に SSIS パッケージの実行アクティビティを連結します。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png" alt-text="ADF Web アクティビティのオンデマンド SSIS IR":::

5. 3 番目のパイプラインを手動で作成する代わりに、テンプレートから自動的に作成することもできます。 これを行うには、 **[パイプライン]** の横にある **[...]** 記号を選択して、パイプライン アクションのドロップダウン メニューを表示し、 **[テンプレートからのパイプライン]** アクションを選択します。そして、 **[カテゴリ]** の下の **[SSIS]** チェックボックスをオンにし、 **[SSIS パッケージの実行の前後に適時に Azure-SSIS IR を開始または停止するように ADF パイプラインのスケジュールを設定]** テンプレートを選択します。次に、 **[Azure-SSIS Integration Runtime]** ドロップダウン メニューで IR を選択し、最後に **[このテンプレートを使用]** ボタンを選択します。 パイプラインが自動的に作成されます。これには、SSIS パッケージのみが残っており、SSIS パッケージの実行アクティビティに割り当てることができます。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-on-demand-ssis-ir-template.png" alt-text="ADF オンデマンド SSIS IR テンプレート":::

6. 3 番目のパイプラインの堅牢性を高めるために、ネットワーク接続やその他の問題のために一時的なエラーが発生した場合に IR を開始/停止する Web アクティビティが再試行され、IR が実際に開始/停止したときにのみ完了するようにすることができます。 これを行うには、各 Web アクティビティを Until アクティビティに置き換えます。このアクティビティには、IR を開始/停止する 2 つの Web アクティビティと、IR の状態を確認するための 2 つの Web アクティビティが含まれます。 これらの Until アクティビティを、"*SSIS IR の開始*" および "*Ssis IR の停止*" と呼ぶことにします。  *[SSIS IR の開始]* Until アクティビティには、 *[SSIS IR 開始の試行]* および *[SSIS IR 状態の取得]* Web アクティビティが含まれます。 *[SSIS IR の停止]* Until アクティビティには、 *[SSIS IR 停止の試行]* および *[SSIS IR 状態の取得]* Web アクティビティが含まれます。 *[SSIS IR の開始]* / *[SSIS IR の停止]* Until アクティビティの **[設定]** タブで、 **[式]** にそれぞれ、`@equals('Started', activity('Get SSIS IR Status').output.properties.state)`/`@equals('Stopped', activity('Get SSIS IR Status').output.properties.state)` と入力します。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-until-activity-on-demand-ssis-ir.png" alt-text="ADF Until アクティビティのオンデマンド SSIS IR":::

   両方の Until アクティビティ内の *[SSIS IR 開始の試行]* / *[SSIS IR 停止の試行]* Web アクティビティは、1 つ目/2 つ目のパイプラインの Web アクティビティと似ています。 *[SSIS IR 状態の取得]* Web アクティビティの **[設定]** タブで、次の操作を行います。

   1. **[URL]** に、Azure-SSIS IR の状態を取得する REST API の URL、`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}?api-version=2018-06-01` を入力して、`{subscriptionId}`、`{resourceGroupName}`、`{factoryName}`、`{integrationRuntimeName}` を、自分の IR の実際の値に置き換えます。
   2. **[メソッド]** で **[GET]** を選択します。 
   3. **[認証]** で **[マネージド ID]** を選択して、指定したシステム マネージド ID を ADF に使用します。詳しくは、「[Data Factory のマネージド ID](./data-factory-service-identity.md)」を参照してください。
   4. **[リソース]** に、「`https://management.azure.com/`」と入力します。
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-until-activity-on-demand-ssis-ir-open.png" alt-text="ADF Until アクティビティのオンデマンド SSIS IR を開く":::

7. ADF のマネージド ID にそれ自体に対する **共同作成者** ロールを割り当て、そのパイプライン内の Web アクティビティが REST API を呼び出してその中にプロビジョニングされた Azure-SSIS IR を開始/停止できるようにします。  Azure portal の ADF ページで **[アクセス制御 (IAM)]** 、 **[+ ロールの割り当ての追加]** の順にクリックし、 **[ロールの割り当ての追加]** ブレードで、次の操作を行います。

   1. **[ロール]** で、 **[共同作成者]** を選択します。 
   2. **[アクセスの割り当て先]** で **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。 
   3. **[選択]** で、ADF 名を検索して選択します。 
   4. **[保存]** をクリックします。
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png" alt-text="ADF マネージド ID のロールの割り当て":::

8. ファクトリ/パイプライン ツール バーの **[すべて検証]/[検証]** をクリックして、ADF とすべてのパイプラインの設定を検証します。 **[>>]** ボタンをクリックして、 **[Factory/Pipeline Validation Output]\(ファクトリ/パイプライン検証出力\)** を閉じます。  

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png" alt-text="パイプラインの検証":::

### <a name="test-run-your-pipelines"></a>パイプラインをテスト実行する

1. 各パイプラインのツール バーで **[テストの実行]** を選択し、下部ウィンドウの **[出力]** ウィンドウを確認します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png" alt-text="テストの実行":::
    
2. 3 つ目のパイプラインをテストするために、SSIS パッケージを SSIS カタログ (SSISDB) に格納している場合は、SQL Server Management Studio (SSMS) を起動して実行を確認できます。 **[サーバーへの接続]** ウィンドウで、次の操作を行います。 

    1. **[サーバー名]** に、「 **&lt;自分のサーバー名&gt;.database.windows.net**」と入力します。
    2. **[オプション >>]** を選択します。
    3. **[データベースの接続]** では **[SSISDB]** を選択します。
    4. **[接続]** を選択します。 
    5. **[Integration Services カタログ]**  ->  **[SSISDB]** -> ご使用のフォルダー -> **[プロジェクト]** -> ご使用の SSIS プロジェクト -> **[パッケージ]** を展開します。 
    6. 指定した SSIS パッケージを右クリックして実行し、 **[レポート]**  ->  **[標準レポート]**  ->  **[すべての実行]** を選択します。 
    7. 実行されたことを確認します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png" alt-text="SSIS パッケージの実行を確認":::

### <a name="schedule-your-pipelines"></a>パイプラインのスケジュールを設定する

パイプラインが意図したとおりに動作したので、指定した周期で実行するトリガーを作成できます。 トリガーをパイプラインに関連付ける方法について詳しくは、「[スケジュールに基づいてパイプラインをトリガーする](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」をご覧ください。

1. パイプラインのツール バーで、 **[トリガー]** を選択し、 **[新規作成/編集]** を選択します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png" alt-text="[トリガー] -> [新規作成/編集] メニュー オプションが強調表示されているスクリーンショット。":::

2. **[トリガーの追加]** ウィンドウで、 **[+ 新規]** を選択します。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png" alt-text="[Add Triggers]\(トリガーの追加\) - [新規]":::

3. **[新しいトリガー]** ウィンドウで、次の操作を行います。 

    1. **[名前]** に、エトリガーの名前を入力します。 次の例では、「**Run daily**」がトリガーの名前です。 
    2. **[種類]** では **[スケジュール]** を選択します。 
    3. **[開始日 (UTC)]** に、開始日時を UTC で入力します。 
    4. **[繰り返し]** に、トリガーの周期を入力します。 次の例では **毎日** 1 回です。 
    5. **[終了]** では、 **[終了日なし]** を選択するか、または **[指定日]** を選択してから終了日時を入力します。 
    6. **[アクティブ化]** を選択して、ADF の設定全体を発行した直後にトリガーをアクティブ化します。 
    7. **[次へ]** を選択します。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png" alt-text="[トリガー] -> [新規作成/編集]":::
    
4. **[Trigger Run Parameters]\(トリガー実行のパラメーター\)** ページで、警告を確認し、 **[完了]** を選択します。 
5. ファクトリ ツール バーで **[すべて公開]** を選択して、ADF の設定全体を公開します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png" alt-text="すべてを公開":::

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Azure portal でパイプラインとトリガーを監視する

1. トリガーの実行とパイプラインの実行を監視するには、ADF UI/アプリの左側の **[監視]** タブを使用します。 手順について詳しくは、「[パイプラインの監視](quickstart-create-data-factory-portal.md#monitor-the-pipeline)」をご覧ください。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png" alt-text="パイプライン実行":::

2. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列にある最初のリンク ( **[View Activity Runs]\(アクティビティの実行の表示\)** ) をクリックします。 3 つ目のパイプラインでは、パイプラインの連結されたアクティビティごとに 1 つずつ、3 つのアクティビティ実行が表示されます (IR を開始する Web アクティビティ、パッケージを実行する SSIS パッケージの実行アクティビティ、IR を停止する Web アクティビティ)。 パイプラインの実行を再び表示するには、上部の **[パイプライン]** リンクをクリックします。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png" alt-text="アクティビティの実行":::

3. トリガーの実行を表示するには、上部の **[Pipeline Runs]\(パイプラインの実行\)** の下にあるドロップダウン リストから **[Trigger Runs]\(トリガーの実行\)** を選択します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png" alt-text="トリガーの実行":::

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>PowerShell でパイプラインとトリガーを監視する

次の例のようなスクリプトを使用して、パイプラインとトリガーを監視します。

1. パイプラインの実行の状態を取得します。

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. トリガーに関する情報を取得します。

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. トリガーの実行の状態を取得します。

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Azure-SSIS IR を開始/停止する Azure Automation Runbook を作成してスケジュールする

このセクションでは、スケジュールに従って Azure-SSIS IR を開始/停止する PowerShell スクリプトを実行する Azure Automation Runbook を作成する方法について説明します。  これは、IR の開始/停止の前/後に前/後処理の追加スクリプトを実行する場合に便利です。

### <a name="create-your-azure-automation-account"></a>Azure Automation アカウントを作成する

Azure Automation アカウントをまだ持っていない場合は、この手順の説明に従って作成します。 手順について詳しくは、「[Azure Automation アカウントを作成する](../automation/quickstarts/create-account-portal.md)」をご覧ください。 この手順の中で、**Azure 実行** アカウント (Azure Active Directory のサービス プリンシパル) を作成し、Azure サブスクリプションの **共同作成者** ロールを割り当てます。 Azure SSIS IR が存在する ADF を含む同じサブスクリプションであることを確認します。 Azure Automation はこのアカウントを使用して、Azure Resource Manager に対して認証し、リソースを処理します。 

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、ADF UI/アプリがサポートされる Web ブラウザーは、Microsoft Edge と Google Chrome だけです。
2. [Azure ポータル](https://portal.azure.com/)にサインインします。    
3. 左側のメニューで **[新規]** を選択し、 **[監視 + 管理]** を選択し、 **[Automation]** を選択します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png" alt-text="[監視 + 管理] > [Automation] オプションが強調表示されているスクリーンショット。":::
    
2. **[Automation アカウントの追加]** ウィンドウで、次の操作を行います。

    1. **[名前]** に、Azure Automation アカウントの名前を入力します。 
    2. **[サブスクリプション]** で、ADF と Azure-SSIS IR が含まれるサブスクリプションを選択します。 
    3. **[リソース グループ]** では、 **[新規作成]** を選択して新しいリソース グループを作成するか、 **[既存のものを使用]** を選択して既存のリソース グループを選択します。 
    4. **[場所]** では、Azure Automation アカウントの場所を選択します。 
    5. **[Azure 実行アカウントの作成]** で **[はい]** を選択します。 Azure Active Directory にサービス プリンシパルが作成されて、Azure サブスクリプションでの **共同作成者** ロールが割り当てられます。
    6. Azure ダッシュボードに常に表示するには、 **[ダッシュボードにピン留め]** を選択します。 
    7. **［作成］** を選択します 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png" alt-text="[新規] -> [監視 + 管理] -> [Automation]":::
   
3. Azure ダッシュボードと通知に、Azure Automation アカウントのデプロイの状態が表示されます。 
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png" alt-text="Automation の展開"::: 
    
4. 正常に作成された後、Azure Automation アカウントのホーム ページが表示されます。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png" alt-text="Automation ホームページ":::

### <a name="import-adf-modules"></a>ADF モジュールをインポートする

1. 左側のメニューの **[共有リソース]** セクションで **[モジュール]** を選択し、モジュールの一覧に **Az.DataFactory** + **Az.Profile** があるかどうか確認します。

   :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png" alt-text="必要なモジュールの確認":::

2.  **Az.DataFactory** がない場合は、[Az.DataFactory モジュール](https://www.powershellgallery.com/packages/Az.DataFactory/)の PowerShell ギャラリーに移動して、 **[Deploy to Azure Automation]\(Azure Automation にデプロイする\)** 、自分の Azure Automation アカウントの順に選択し、 **[OK]** を選択します。 左側のメニューの **[共有リソース]** セクションの **[モジュール]** の表示に戻り、**Az.DataFactory** モジュールの **[状態]** が **利用可能** に変わるまで待ちます。

    :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png" alt-text="データ ファクトリ モジュールの確認":::

3.  **Az.Profile** がない場合は、[Az.Profile モジュール](https://www.powershellgallery.com/packages/Az.profile/)の PowerShell ギャラリーに移動して、 **[Deploy to Azure Automation]\(Azure Automation にデプロイする\)** 、自分の Azure Automation アカウントの順に選択し、 **[OK]** を選択します。 左側のメニューの **[共有リソース]** セクションの **[モジュール]** の表示に戻り、**Az.Profile** モジュールの **[状態]** が **利用可能** に変わるまで待ちます。

    :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png" alt-text="プロファイル モジュールの確認":::

### <a name="create-your-powershell-runbook"></a>PowerShell Runbook を作成する

次のセクションでは、PowerShell Runbook を作成する手順を説明します。 Runbook に関連付けられるスクリプトは、**OPERATION** パラメーターに指定するコマンドに応じて、Azure-SSIS IR を開始または停止します。 このセクションでは、Runbook を作成するためのすべての詳細は説明しません。 詳しくは、[Runbook の作成](../automation/learn/powershell-runbook-managed-identity.md)に関する記事をご覧ください。

1. **[Runbook]** タブに切り替え、ツールバーから **[+ Runbook の追加]** を選択します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png" alt-text="[+ Runbook の追加] ボタンが強調表示されているスクリーンショット。":::
   
2. **[新しい Runbook の作成]** を選択して、次のようにします。 

    1. **[名前]** に、「**StartStopAzureSsisRuntime**」と入力します。
    2. **[Runbook の種類]** で、 **[PowerShell]** を選びます。
    3. **［作成］** を選択します
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png" alt-text="[Runbook の追加] ボタン":::
   
3. Runbook のスクリプト ウィンドウに次の PowerShell スクリプトをコピーして貼り付けます。 ツールバーの **[保存]** ボタンと **[公開]** ボタンを使用して、Runbook を保存してから公開します。 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png" alt-text="PowerShell Runbook の編集":::
    
4. ツールバーの **[開始]** ボタンを選択して、Runbook をテストします。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png" alt-text="Runbook を開始するボタン":::
    
5. **[Runbook の開始]** ウィンドウで、次のようにします。 

    1. **[RESOURCEGROUPNAME]** には、ADF と Azure-SSIS IR が含まれるリソース グループの名前を入力します。 
    2. **[DATAFACTORYNAME]** には、ADF と Azure-SSIS IR の名前を入力します。 
    3. **[AZURESSISNAME]** には、Azure-SSIS IR の名前を入力します。 
    4. **[OPERATION]** には、「**START**」を入力します。 
    5. **[OK]** を選択します。  

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png" alt-text="[Runbook の開始] ウィンドウ":::
   
6. [ジョブ] ウィンドウで、 **[出力]** タイルを選択します。 出力ウィンドウで、メッセージ " **##### Completed #####** " が " **##### Starting #####** " の後に表示されるまで待機します。 Azure-SSIS IR を開始するには約 20 分かかります。 **[ジョブ]** ウィンドウを閉じて、 **[Runbook]** ウィンドウに戻ります。

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png" alt-text="[出力] タイルが強調表示されているスクリーンショット。":::
    
7. 前の 2 つの手順を繰り返し、今度は **OPERATION** の値として **STOP** を使用します。 ツール バーの **[開始]** ボタンを選択して、再び Runbook を開始します。 リソース グループ、ADF、Azure-SSIS IR の名前を入力します。 **[OPERATION]** には「**STOP**」を入力します。 出力ウィンドウで、メッセージ " **##### Completed #####** " が " **##### Stopping #####** " の後に表示されるまで待機します。 Azure-SSIS IR の停止には、開始ほどは時間がかかりません。 **[ジョブ]** ウィンドウを閉じて、 **[Runbook]** ウィンドウに戻ります。

8. **[Webhook]** メニュー項目を選択することで作成できる Webhook、または次に示す **[スケジュール]** メニュー項目を選択することで作成できるスケジュールを使用して、Runbook をトリガーすることもできます。  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Azure-SSIS IR を開始/停止する Rrunbook のスケジュールを作成する

前のセクションでは、Azure-SSIS IR を開始または停止する Azure Automation Runbook を作成しました。 このセクションでは、Runbook の 2 つのスケジュールを作成します。 1 つ目のスケジュールを構成するときは、**OPERATION** に **START** を指定します。 同様に、2 つ目のスケジュールを構成するときは、**OPERATION** パラメーターに **STOP** を指定します。 スケジュールを作成する手順について詳しくは、「[スケジュールを作成する](../automation/shared-resources/schedules.md#create-a-schedule)」をご覧ください。

1. **[Runbook]** ウィンドウで **[スケジュール]** を選択し、ツールバーの **[+ スケジュールの追加]** を選択します。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png" alt-text="Azure SSIS IR - 開始":::
   
2. **[Runbook のスケジュール設定]** ウィンドウで、次のようにします。 

    1. **[スケジュールを Runbook にリンクします]** を選択します。 
    2. **[新しいスケジュールを作成します]** を選択します。
    3. **[新しいスケジュール]** ウィンドウで、 **[名前]** に「**毎日 IR を開始**」と入力します。 
    4. **[開始]** に、現在時刻より数分後の時刻を入力します。 
    5. **[繰り返し]** では **[定期的]** を選択します。 
    6. **[繰り返し間隔]** に「**1**」と入力して、 **[日]** を選択します。 
    7. **［作成］** を選択します 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png" alt-text="Azure SSIS IR 開始のスケジュール":::
    
3. **[パラメーターと実行設定]** タブに切り替えます。リソース グループ、ADF、Azure-SSIS IR の名前を指定します。 **[OPERATION]** に「**START**」と入力して、 **[OK]** を選択します。 **[OK]** を再び選択すると、Runbook の **[スケジュール]** ページにスケジュールが表示されます。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png" alt-text="[操作] フィールドが強調表示されているスクリーンショット。":::
    
4. 前の 2 つの手順を繰り返して、「**毎日 IR を停止**」という名前のスケジュールを作成します。 "**毎日 IR を開始**" スケジュールで指定した時刻から 30 分以上後の時刻を入力します。 **[OPERATION]** に「**STOP**」と入力して、 **[OK]** を選択します。 **[OK]** を再び選択すると、Runbook の **[スケジュール]** ページにスケジュールが表示されます。 

5. **[Runbook]** ウィンドウで、左側のメニューの **[ジョブ]** を選択します。 指定時刻にスケジュールによって作成されたジョブとその状態が表示されます。 Runbook をテストした後と同じように、出力などのジョブの詳細を確認できます。 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png" alt-text="Azure SSIS IR を開始するスケジュール":::
    
6. テストが完了したら、編集してスケジュールを無効にします。 左側のメニューで **[スケジュール]** を選択し、 **"毎日 IR を開始" または "毎日 IR を停止"** を選択して、 **[有効]** で **[いいえ]** を選択します。 

## <a name="next-steps"></a>次のステップ
次のブログ記事を参照してください。
-   [ADF パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

SSIS ドキュメントの次の記事をご覧ください。 

- [Azure で SSIS パッケージを配置、実行、および監視する](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSIS カタログへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)