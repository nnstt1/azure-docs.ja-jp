---
title: Azure Automation Update Management の更新プログラムの展開を作成する方法
description: この記事では、更新プログラムの展開をスケジュールし、その状態を確認する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 11/05/2021
ms.topic: conceptual
ms.openlocfilehash: 6830c06de1fc687f8c408d438086f6c08550d9f0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551408"
---
# <a name="how-to-deploy-updates-and-review-results"></a>更新プログラムを展開して結果を確認する方法

この記事では、更新プログラムの展開をスケジュールし、展開の完了後に処理を確認する方法について説明します。 選択された Azure 仮想マシンから、選択された Azure Arc 対応サーバーから、または構成されているすべてのマシンとサーバーにおける Automation アカウントから、更新プログラムの展開を構成できます。

各シナリオでは、作成する展開の対象は、その選択されたマシンまたはサーバーになります。また、Automation アカウントから展開を作成する場合は、1 つまたは複数のマシンを対象にすることができます。 Azure VM または Azure Arc 対応サーバーから更新プログラムの展開をスケジュールする場合、手順は Automation アカウントからの展開と同じです。ただし、次の例外があります。

* オペレーティング システムは、マシンの OS に基づいて自動的に事前に選択されます
* 更新する対象マシンが自動的にターゲットに設定されます
* スケジュールを構成するときに、**今すぐ更新**、1 回実行、または定期的なスケジュールの使用を指定できます。

> [!IMPORTANT]
> 更新プログラムの展開を作成すると、そのオペレーティング システムの更新プログラムを提供している会社によって規定されたソフトウェア ライセンス条項 (EULA) の条項に同意したことになります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムの展開をスケジュールすると、1 つまたは複数の対象マシンへの更新プログラムの展開を処理する **Patch-MicrosoftOMSComputers** Runbook にリンクされた [スケジュール](../shared-resources/schedules.md) リソースが作成されます。 リリース スケジュールとサービス期間に従って展開をスケジュールし、更新プログラムをインストールする必要があります。 展開に含める更新プログラムの種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

>[!NOTE]
>展開の作成後に Azure portal または PowerShell を使用してこのスケジュール リソースを削除した場合、スケジュールされた更新プログラムの展開が破棄され、ポータルからスケジュール リソースを再構成しようとするとエラーが表示されます。 スケジュール リソースは、対応するデプロイ スケジュールを削除することによってのみ削除することができます。  

新しい更新プログラムの展開をスケジュールするには、次の手順を実行します。 選択されたリソース (つまり、Automation アカウント、Azure Arc 対応サーバー、Azure VM) によって若干の違いはありますが、いずれの場合も、展開スケジュールの構成時には次の手順が適用されます。

1. ポータルで、次を対象とする展開をスケジュールします。

   * 1 つまたは複数のマシン。 **[Automation アカウント]** に移動し、一覧から Update Management が有効になっている Automation アカウントを選択します。
   * Azure VM。 **[仮想マシン]** に移動し、一覧から VM を選択します。
   * Azure Arc 対応サーバー。 **[サーバー - Azure Arc]** に移動し、一覧からお使いのサーバーを選択します。

2. 選択したリソースに応じて、次のように Update Management に移動します。

   * ご自分の Automation アカウントを選択した場合は、 **[更新の管理]** の **[更新の管理]** に移動し、 **[更新プログラムの展開のスケジュール]** を選択します。
   * Azure VM を選択した場合は、 **[ゲスト + ホストの更新プログラム]** に移動し、 **[Update Management に移動]** を選択します。
   * Azure Arc 対応サーバーを選択した場合は、 **[更新の管理]** に移動し、 **[更新プログラムの展開のスケジュール]** を選択します。

3. **[新しい更新プログラムの展開]** の下の **[名前]** フィールドを使用して、展開に一意の名前を入力します。

4. 更新プログラムの展開の対象となるオペレーティング システムを選択します。

    > [!NOTE]
    > このオプションは、Azure VM または Azure Arc 対応サーバーを選択した場合は使用できません。 オペレーティング システムが自動的に識別されます。

5. **[更新するグループ]** 領域で、サブスクリプション、リソース グループ、場所、タグを組み合わせたクエリを定義し、デプロイに含める Azure VM の動的グループを構築します。 詳細については、「[Update Management を利用して動的グループを使用する](configure-groups.md)」を参照してください。

    > [!NOTE]
    > このオプションは、Azure VM または Azure Arc 対応サーバーを選択した場合は使用できません。 スケジュールされた展開の対象は自動的にそのマシンとなります。

   > [!IMPORTANT]
   > Azure VM の動的グループを構築するとき、Update Management では、グループのスコープ内のサブスクリプションまたはリソースグループを結合する最大 500 のクエリのみがサポートされます。

6. **[更新するマシン]** 領域で、保存した検索またはインポートしたグループを選択するか、ドロップダウン メニューから **[マシン]** を選択し、個々のマシンを選択します。 このオプションを使用すると、各マシンの Log Analytics エージェントの準備状況を確認できます。 Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../../azure-monitor/logs/computer-groups.md)に関するページを参照してください スケジュールされた更新プログラムのデプロイには、最大で 1,000 台のマシンを含めることができます。

    > [!NOTE]
    > このオプションは、Azure VM または Azure Arc 対応サーバーを選択した場合は使用できません。 スケジュールされた展開の対象は自動的にそのマシンとなります。

7. **[更新プログラムの分類]** 領域を使用して、製品の [更新プログラムの分類](view-update-assessments.md#work-with-update-classifications)を指定します。 製品ごとに、更新プログラムの展開に含めるものを除き、サポート対象の更新プログラムの分類すべての選択を解除します。

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="特定の更新プログラムの分類の選択を示す例。":::

    選択した更新プログラムのセットのみを展開で適用する場合は、次の手順で説明するように、 **[更新プログラムの包含/除外]** オプションを構成するときに、事前に選択されている更新プログラムの分類をすべて選択解除する必要があります。 これにより、ターゲット マシンにインストールされているのは、この展開で *[含める]* に指定した更新プログラムのみになります。

   >[!NOTE]
   > 更新プログラムの分類ごとの更新プログラムのデプロイは、RTM バージョンの CentOS では動作しません。 CentOS に更新プログラムを正しくデプロイするには、更新プログラムが確実に適用されるように、すべての分類を選択します。 CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法は現在ありません。 [更新プログラムの分類](overview.md#update-classifications)の詳細については、次を参照してください。

   >[!NOTE]
   > Update Management でサポートされる Linux ディストリビューションでは、更新プログラムの分類に基づく更新プログラムのデプロイが正しく機能しない場合があります。 これは、OVAL ファイルの名前付けスキーマに関して見つかっている問題によるものです。この問題が妨げとなって、Update Management が、フィルタリング規則に基づいて適切に分類を照合することができません。 セキュリティ更新プログラムの評価に別のロジックが使用されていることが原因で、デプロイ中に適用されるセキュリティ更新プログラムとは結果が異なる場合があります。 分類が **緊急** および **セキュリティ** として設定されている場合、更新プログラムのデプロイは正しく機能します。 影響を受けるのは、評価中の "*更新プログラムの分類*" のみです。
   >
   > Windows Server マシンの Update Management には影響せず、更新プログラムの分類とデプロイに変更は生じません。

8. **[更新プログラムの包含/除外]** 領域を使用して、選択した更新プログラムを展開に追加または除外します。 **[包含/除外]** ページで、包含または除外する Windows 更新プログラムのサポート技術情報の記事 ID 番号を入力します。 サポートされている Linux ディストリビューションの場合は、パッケージの名前を指定します。

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="特定の更新プログラムを含める方法を示す例。":::

   > [!IMPORTANT]
   > 包含より除外が優先されることを覚えておいてください。 たとえば、`*` の除外ルールを定義すると、Update Management ではすべてのパッチまたはパッケージがインストールから除外されます。 除外されたパッチは、マシンに不足しているものとして表示されます。 Linux マシンでは、除外された依存パッケージを含むパッケージを含めた場合、メイン パッケージはインストールされません。

   > [!NOTE]
   > 置き換え済みの更新プログラムを更新プログラムの展開に含めるように指定することはできません。

   更新プログラムの展開で包含/除外と更新プログラムの分類を同時に使用する方法を理解するのに役立つシナリオの例を次に示します。

   * 更新プログラムの特定のリストのみをインストールする場合は、 **[更新プログラムの分類]** は選択せずに、 **[含める]** オプションを使用して適用する更新プログラムのリストを指定する必要があります。

   * 1 つ以上のオプションの更新プログラムと共に、セキュリティ更新プログラムと重要な更新プログラムのみをインストールする場合は、 **[更新プログラムの分類]** で **[セキュリティ]** と **[クリティカル]** を選択する必要があります。 次に、 **[含める]** オプションで、オプションの更新プログラムの KBID を指定します。

   * セキュリティ更新プログラムと重要な更新プログラムのみをインストールし、レガシ アプリケーションの中断を回避するために python の更新プログラムを 1 つ以上スキップする場合は、 **[更新プログラムの分類]** で **[セキュリティ]** と **[クリティカル]** を選択する必要があります。 次に、 **[除外]** オプションで、スキップする python パッケージを追加します。

9. **[スケジュール設定]** を選択します。 既定の開始時刻は、現在の時刻の 30 分後です。 開始時刻は、10 分後以降の将来の任意の時点に設定できます。

    > [!NOTE]
    > このオプションは、Azure Arc 対応サーバーを選択した場合は異なります。 **[今すぐ更新]** を選択するか、または開始時刻を 20 分後にすることができます。

10. **[繰り返し]** を使用して、展開を 1 回だけ実行するか、定期的なスケジュールを使用するかどうかを指定し、 **[OK]** を選択します。

11. **[事前スクリプトと事後スクリプト]** 領域で、デプロイの前後に実行するスクリプトを選択します。 詳細については、「[事前スクリプトと事後スクリプトを管理する](pre-post-scripts.md)」を参照してください。

12. **[メンテナンス期間 (分)]** フィールドを使用して、更新プログラムをインストールするために許容される時間を指定します。 メンテナンス期間を指定するときは、次の詳細を考慮してください。

    * メンテナンス期間によって、インストールされる更新プログラムの数が制御されます。
    * メンテナンス期間の終了が近づいている場合でも、Update Management では、新しい更新プログラムのインストールは停止されません。
    * メンテナンス期間を超過した場合でも、進行中の更新は終了されません。 残りの更新プログラムのインストールは試行されません。 これが常に発生している場合は、メンテナンス期間を再評価する必要があります。
    * Windows でメンテナンス期間が超過した場合、Service Pack の更新プログラムのインストールに時間がかかることが原因であることがよくあります。

    > [!NOTE]
    > Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、`Unattended-Upgrade` パッケージを再構成して自動更新を無効にします。 パッケージの構成方法については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)をご覧ください。

13. **[再起動のオプション]** フィールドを使用して、展開中の再起動の処理方法を指定します。 次のオプションを使用できます。 
    * 必要に応じて再起動 (既定値)
    * 常に再起動
    * 再起動しない
    * 再起動のみ (このオプションでは更新プログラムはインストールされません)

    > [!NOTE]
    > **[再起動のオプション]** が **[再起動しない]** に設定されている場合、「[再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に記載されているレジストリ キーにより、再起動イベントが発生する可能性があります。

14. 展開スケジュールの構成が完了したら、 **[作成]** を選択します。

    ![更新プログラムのスケジュール設定ウィンドウ](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > 選択した Azure Arc 対応サーバーの展開スケジュールの構成が完了したら、 **[確認と作成]** を選択します。

15. 状態ダッシュボードに戻ります。 **[展開スケジュール]** を選択して、作成した展開スケジュールを表示します。 最大 500 個のスケジュールが一覧表示されます。 500 個を超えるスケジュールがあり、完全な一覧を確認したい場合は、[ソフトウェア更新プログラムの構成 - 一覧](/rest/api/automation/softwareupdateconfigurations/list) REST API メソッドを参照してください。 API バージョン 2019-06-01 以降を指定します。

## <a name="schedule-an-update-deployment-programmatically"></a>プログラムで更新プログラムの展開をスケジュールする

REST API を使用して更新プログラムの展開を作成する方法については、「[ソフトウェア更新プログラムの構成 - 作成](/rest/api/automation/softwareupdateconfigurations/create)」を参照してください。

サンプル Runbook を使用して、週単位の更新プログラムの展開を作成できます。 この Runbook について詳しくは、「[Create a weekly update deployment for one or more VMs in a resource group](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group)」(リソース グループ内の VM に対して週単位の更新プログラムのデプロイを作成する) をご覧ください。

## <a name="check-deployment-status"></a>展開の状態を確認する

スケジュールされた展開の開始後、 **[更新の管理]** の **[履歴]** タブに、その状態が表示されます。 展開が現在実行中の場合、状態は **[処理中]** と表示されます。 展開が正常に終了すると、状態が **[成功]** に変わります。 展開に含まれる 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[失敗]** になります。

## <a name="view-results-of-a-completed-update-deployment"></a>完了した更新プログラムの展開の結果を表示する

展開が完了したら、それを選択してその結果を表示することができます。

[![特定の展開に関する更新プログラムの展開ステータスのダッシュボード](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

**[更新プログラムを実行した結果]** には、ターゲット VM 上の更新プログラムの合計数と展開結果が表示されます。 右側の表には、更新プログラムの詳細な内訳とそれぞれのインストール結果が示されます。

使用できる値は次のとおりです。

* **試行されていません** - 定義されたメンテナンス期間に基づく利用可能な時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **選択されていません** - 更新プログラムが展開用に選択されていませんでした。
* **成功** - 更新できました。
* **失敗** - 更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、 **[すべてのログ]** を選択します。

ターゲット VM での更新プログラムの展開を管理する Runbook のジョブ ストリームを確認するには、 **[出力]** を選択します。

展開で発生したエラーの詳細情報を確認するには、 **[エラー]** を選択します。

## <a name="deploy-updates-across-azure-tenants"></a>Azure テナント全体に更新プログラムをデプロイする

Update Management への報告を行う別の Azure テナントに、修正プログラムを適用する必要があるマシンが存在する場合は、次の対処法を使用して、それらをスケジュールを設定する必要があります。 スケジュールを作成するには、`ForUpdateConfiguration` パラメーターを指定して [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) コマンドレットを使用します。 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) コマンドレットを使用して、他のテナントのマシンを `NonAzureComputer` パラメーターに渡すことができます。 次の例は、その方法を示したものです。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule `
    -ResourceGroupName mygroup `
    -AutomationAccountName myaccount `
    -Name myupdateconfig `
    -Description test-OneTime `
    -OneTime `
    -StartTime $startTime `
    -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  `
    -ResourceGroupName $rg `
    -AutomationAccountName <automationAccountName> `
    -Schedule $sched `
    -Windows `
    -NonAzureComputer $nonAzurecomputers `
    -Duration (New-TimeSpan -Hours 2) `
    -IncludedUpdateClassification Security,UpdateRollup `
    -ExcludedKbNumber KB01,KB02 `
    -IncludedKbNumber KB100
```

## <a name="next-steps"></a>次のステップ

* 更新プログラムの展開の結果について通知するアラートを作成する方法については、[Update Management のアラートを作成](configure-alerts.md)に関する記事を参照してください。
* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](../troubleshoot/update-management.md)に関する記事を参照してください。
