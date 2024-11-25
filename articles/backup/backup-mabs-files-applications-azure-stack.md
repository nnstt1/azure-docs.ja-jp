---
title: Azure Stack VM のファイルのバックアップ
description: Azure Backup を使用して、Azure Stack ファイルとアプリケーションを Azure Stack 環境にバックアップし、復元します。
ms.topic: conceptual
ms.date: 11/11/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: a6c92485ec89e06f9bf545181fe7e6341cd7bff4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284438"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Azure Stack 上のファイルとアプリケーションのバックアップ

Azure Backup を使用して、Azure Stack 上のファイルとアプリケーションを保護 (バックアップ) することができます。 ファイルとアプリケーションをバックアップするには、Microsoft Azure Backup Server を Azure Stack 上で動作する仮想マシンとしてインストールします。 同じ仮想ネットワーク内の任意の Azure Stack サーバー上のファイルを保護できます。 Azure Backup Server をインストールしたら、Azure ディスクを追加して、短期バックアップ データに使用できるローカル ストレージを増やしてください。 Azure Backup Server は、長期保有には Azure Storage を使用します。

> [!NOTE]
> Azure Backup Server と System Center Data Protection Manager (DPM) は似ていますが、DPM は Azure Stack での使用がサポートされていません。
>

この記事では、Azure Stack 環境に Azure Backup Server をインストールする方法については説明しません。 Azure Stack に Azure Backup Server をインストールするには、[Azure Backup Server のインストール](backup-mabs-install-azure-stack.md)に関する記事を参照してください。

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Azure Stack VM のファイルとフォルダーを Azure にバックアップする

Azure Stack 仮想マシンのファイルを保護できるように Azure Backup Server を構成するには、Azure Backup Server コンソールを開きます。 このコンソールを使用して、保護グループを構成し、仮想マシン上のデータを保護します。

1. Azure Backup Server コンソールで **[保護]** を選択し、ツール バーの **[新規]** を選択して、**新しい保護グループの作成** ウィザードを開きます。

   ![Azure Backup Server コンソールでの保護の構成](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    ウィザードが開くまで数秒かかる可能性があります。 ウィザードが開いたら、 **[次へ]** を選択して、 **[保護グループの種類の選択]** 画面に進みます。

   ![新しい保護グループのウィザードが開いたところ](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. **[保護グループの種類の選択]** 画面で、 **[サーバー]** を選択し、 **[次へ]** を選択します。

    ![保護グループの種類の選択](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    **[グループ メンバーの選択]** 画面が表示されます。

    ![グループ メンバーの選択](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. **[グループ メンバーの選択]** 画面で **[+]** を選択し、サブ項目の一覧を展開します。 保護するすべての項目で、チェック ボックスをオンにします。 すべての項目を選択したら、 **[次へ]** を選択します。

    ![保護する各項目の選択](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft では、保護ポリシーを共有するすべてのデータを 1 つの保護グループに配置することをお勧めしています。 保護グループの計画とデプロイの詳細については、System Center DPM の記事である「[保護グループの展開](/system-center/dpm/create-dpm-protection-groups)」を参照してください。

4. **[データの保護方法の選択]** 画面で、保護グループの名前を入力します。 **[I want short-term protection using:]\(次のものを使用した短期的な保護を利用する:\)** と **[I want online protection]\(オンライン保護を利用する\)** のチェック ボックスをオンにします。 **[次へ]** を選択します。

    ![データの保護方法の選択](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    **[オンライン保護を利用する]** をオンにするには、最初に **[次を使用して短期的な保護を行う:]** で [ディスク] を選択する必要があります。 Azure Backup Server はテープを使用する保護に対応していないため、短期的な保護の選択肢はディスクのみです。

5. **[短期的な目標値の指定]** 画面で、ディスクに保存された回復ポイントの保持期間と、増分バックアップを保存するタイミングを選択します。 **[次へ]** を選択します。

    > [!IMPORTANT]
    > Azure Backup Server に接続されたディスク上に、5 日間を超える運用の復旧 (バックアップ) データを保持することは推奨 **されません**。
    >

    ![短期的な目標値の指定](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    増分バックアップの間隔を選択する代わりに、スケジュールされた各回復ポイントの直前に高速完全バックアップを実行するには、 **[回復ポイントの直前]** を選択します。 アプリケーションのワークロードを保護している場合、Azure Backup Server は同期頻度のスケジュールに従って回復ポイントを作成します (アプリケーションで増分バックアップがサポートされている場合)。 アプリケーションで増分バックアップがサポートされていない場合、Azure Backup Server は高速完全バックアップを実行します。

    **[ファイルの回復ポイント]** では、回復ポイントを作成するタイミングを指定します。 回復ポイントを作成する時刻や曜日を変更するには、 **[変更]** を選択します。

6. **[ディスク割り当ての確認]** 画面では、保護グループに割り当てられている記憶域プールのディスク領域を確認します。

    **[合計データ サイズ]** は、バックアップするデータのサイズです。Azure Backup Server 上の **[Disk space to be provisioned]/(プロビジョニングされるディスク領域/)** は、保護グループ用に推奨されている領域です。 Azure Backup Server は、設定に基づいて理想的なバックアップ ボリュームを選択します。 ただし、ディスク割り当ての詳細でバックアップ ボリュームの選択を編集できます。 ワークロードの場合、ドロップダウン メニューで、優先ストレージを選択します。 編集すると、[利用可能なディスク記憶域] ペインの [ストレージの合計] と [空き記憶域] の値が変わります。 過小にプロビジョニングされた領域とは、今後もスムーズなバックアップを確実に行うためにボリュームに追加することを Azure Backup Server から提案されるストレージの量です。

7. **[レプリカの作成方法の選択]** で、最初の全データのレプリケーションを処理する方法を選択します。 ネットワーク経由でレプリケートする場合、Azure では、ピーク時以外を選択することをお勧めします。 データが大量にある場合や、ネットワークの状態が最適でない場合は、リムーバブル メディアを使用してデータをレプリケートすることを検討してください。

8. **[整合性チェック オプションの選択]** で、整合性チェックを自動化する方法を選択します。 整合性チェックは、データのレプリケーションに不整合が生じたときや、スケジュールに従ってレプリケーションを行う場合にのみ実行されるようにしてください。 自動整合性チェックを構成しない場合は、次の方法で、いつでも手動でチェックを実行できます。
    * Azure Backup Server コンソールの **[保護]** 領域で保護グループを右クリックし、 **[整合性チェックの実行]** を選択します。

9. Azure にバックアップすることにした場合、 **[オンライン保護するデータの指定]** ページで、Azure にバックアップするワークロードが選択されていることを確認してください。

10. **[オンライン バックアップ スケジュールの指定]** で、Azure への増分バックアップを行うタイミングを指定します。

    毎日、毎週、毎月、毎年というタイミングでバックアップをスケジュールできます。また、実行する日時を選択できます。 バックアップは、最大 1 日に 2 回実行できます。 バックアップ ジョブが実行されるたびに、Azure Backup Server のディスクに格納されたバックアップ データのコピーから Azure にデータ回復ポイントが作成されます。

11. **[オンライン保持ポリシーの指定]** で、毎日、毎週、、毎月、毎年のバックアップから作成される回復ポイントを Azure に保持する方法を指定します。

12. **[オンライン レプリケーションの選択]** で、最初の全データのレプリケーションを実行する方法を指定します。

13. **[概要]** で設定を確認します。 **[グループの作成]** を選択すると、最初のデータ レプリケーションが実行されます。 データのレプリケーションが終了すると、 **[ステータス]** ページで、保護グループの状態が **[OK]** と表示されます。 保護グループの設定に応じて最初のバックアップ ジョブが実行されます。

## <a name="recover-file-data"></a>ファイル データの回復

仮想マシンにデータを回復するには、Azure Backup Server コンソールを使用します。

1. Azure Backup Server コンソールのナビゲーション バーで、 **[回復]** を選択し、回復するデータを参照します。 結果ペインでデータを選択します。

2. 回復ポイント セクションのカレンダーで太字で示されている日付は、回復ポイントが使用可能であることを表しています。 復旧する日付を選択します。

3. **[回復可能な項目]** ペインで、回復する項目を選択します。

4. **[操作]** ウィンドウの **[回復]** を選択して回復ウィザードを開きます。

5. 次のようにデータを回復できます。

    * **元の場所に回復する** - クライアント コンピューターが VPN 経由で接続されている場合は、このオプションは機能しません。 代わりに、別の場所を使用して、その場所からデータをコピーします。
    * **別の場所に回復する**

6. 回復オプションを指定します。

    * **[既存のバージョンの回復の動作]** で、 **[コピーの作成]** 、 **[スキップ]** 、または **[上書き]** を選択します。 上書きは、元の場所に回復するときにのみ使用できます。
    * **[セキュリティの復元]** で、 **[Apply settings of the destination computer]\(対象のコンピューターの設定を適用する\)** または **[Apply the security settings of the recovery point version]\(回復ポイントのバージョンのセキュリティ設定を適用する\)** を選択します。
    * **[ネットワークの使用帯域幅の調整]** で **[変更]** を選択し、ネットワークの使用帯域幅の調整を有効にします。
    * **[通知]** で、 **[この回復が完了したら電子メールを送信する]** を選択して、通知を受け取る受信者を指定します。 複数のメール アドレスはコンマで区切ります。
    * 必要な選択を行ったら、 **[次へ]** を選択します

7. 回復の設定を確認し、 **[回復]** を選択します。

    >[!Note]
    >回復ジョブの進行中は、選択された回復項目のすべての同期ジョブが取り消されます。

Modern Backup Storage (MBS) を使用している場合、ファイル サーバーのエンドユーザー回復 (EUR) はサポートされません。 ファイル サーバーの EUR は、Modern Backup Storage で使用しないボリューム シャドウ コピー サービス (VSS) に依存しています。 EUR が有効になっている場合は、次の手順を使用してデータを回復します。

1. 保護されたファイルに移動し、ファイル名を右クリックして、 **[プロパティ]** を選択します。

2. **[プロパティ]** メニューで **[以前のバージョン]** を選択し、回復するバージョンを選択します。

## <a name="view-azure-backup-server-with-a-vault"></a>コンテナーを含む Azure Backup Server の表示

Azure portal で Azure Backup Server エンティティを表示するには、次の手順に従います。

1. Recovery Services コンテナーを開きます。
2. [バックアップ インフラストラクチャ] を選択します。
3. バックアップ管理サーバーを表示します。

## <a name="next-steps"></a>次のステップ

Azure Backup Server を使用して他のワークロードを保護する方法については、次のいずれかの記事を参照してください。

* [Azure Backup サービスの概要](./backup-overview.md)
* [Azure AD の概要](../active-directory/fundamentals/active-directory-whatis.md)
* [Azure Recovery Services コンテナー](./backup-azure-recovery-services-vault-overview.md)
* [Azure Storage について](../storage/common/storage-introduction.md)
* [Azure Stack Hub について](/azure-stack/operator/azure-stack-overview)
* [SharePoint ファームのバックアップ](./backup-mabs-sharepoint-azure-stack.md)
* [SQL Server のバックアップ](./backup-mabs-sql-azure-stack.md)
