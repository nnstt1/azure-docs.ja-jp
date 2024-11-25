---
title: MARS エージェントを使用して Windows Server にファイルを復元する
description: この記事では、Microsoft Azure Recovery Services (MARS) エージェントを使用して、Azure に格納されているデータを Windows サーバーまたは Windows コンピューターに復元する方法について説明します。
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: fd17d4f2199440c72e5b0ceb4ec7fda7ab40d837
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555112"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>MARS エージェントを使用して Windows Server にファイルを復元する

この記事では、バックアップ コンテナーからデータを復元する方法について説明します。 データを復元するには、Microsoft Azure Recovery Services (MARS) エージェントのデータの回復ウィザードを使用します。 次のようにすることができます。

* バックアップが実行されたのと同じマシンにデータを復元する
* 別のコンピューターにデータを復元する

書き込み可能な復旧ポイントのスナップショットを回復ボリュームとしてマウントするには、インスタント リストア機能を使用します。 その後、回復ボリュームを調べてファイルをローカル コンピューターにコピーすることによって、ファイルを選択的に復元できます。

> [!NOTE]
> インスタント リストアを使用してデータを復元する場合は、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar)が必要です。 また、バックアップ データは、サポート記事に記載されているロケール内のコンテナーで保護されている必要があります。 インスタント リストアをサポートするロケールの最新リストについては、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar)をご覧ください。
>

Azure Portal の Recovery Services コンテナーでインスタント リストアを使います。 Backup コンテナーにデータを格納した場合は、Recovery Services コンテナーに変換されています。 インスタント リストアを使用する場合は、MARS 更新プログラムをダウンロードして、インスタント リストアを説明する手順に従います。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>インスタント リストアを使用して同じコンピューターにデータを回復する

ファイルを誤って削除し、それを (バックアップが取得されたのと) 同じコンピューターに復元する場合は、次の手順を使用するとデータの復旧に役立ちます。

1. **Microsoft Azure Backup** スナップインを開きます。 スナップインがインストールされている場所がわからない場合は、コンピューターまたはサーバーで **Microsoft Azure Backup** を検索します。

    デスクトップ アプリが検索結果に表示されます。

2. **[データの回復]** を選択してウィザードを開始します。

    ![[データの回復] が強調表示された Azure Backup のスクリーンショット (同じコンピューターに復元)](./media/backup-azure-restore-windows-server/recover.png)

3. **[使用の開始]** ページで、データを同じサーバーまたはコンピューターに復元するには、 **[このサーバー (`<server name>`)]**  >  **[次へ]** を選択します。

    ![データの回復ウィザードの [使用の開始] ページのスクリーンショット (同じコンピューターに復元)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. **[回復モードの選択]** ページで、 **[個々のファイルとフォルダー]** > **[次へ]** を順に選択します。

    ![データの回復ウィザードの [回復モードの選択] ページのスクリーンショット (同じコンピューターに復元)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > 個別のファイルとフォルダーを復元するオプションには、.NET Framework 4.5.2 以降が必要です。 **[個別のファイルとフォルダー]** オプションが表示されない場合は、.NET Framework をバージョン 4.5.2 以降にアップグレードして再試行する必要があります。

   > [!TIP]
   > **[個別のファイルとフォルダー]** オプションを使用すると、復旧ポイントのデータにすばやくアクセスできます。 個別のファイルの復旧に適しており、合計サイズは 80 GB 未満であることをお勧めします。 復旧中の転送またはコピーの速度は最大 6 MBps です。 **[ボリューム]** オプションでは、指定されたボリューム内のすべてのバックアップ済みデータを復旧します。 このオプションでは、より高速な転送速度 (最大 40 MBps) が提供され、大きなサイズのデータやボリューム全体の復旧に推奨されます。

5. **[ボリュームと日付の選択]** ページで、復元するファイルとフォルダーを含むボリュームを選択します。

    カレンダーで回復ポイントを選択します。 **太字** になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 1 日の中で複数の復旧ポイントを使用できる場合は、 **[時間]** ドロップダウン メニューから特定の復旧ポイントを選択します。

    ![データの回復ウィザードの [ボリュームと日付の選択] ページのスクリーンショット (同じコンピューターに復元)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 復元する復旧ポイントを選択したら、 **[マウント]** を選択します。

    Azure Backup がローカルの回復ポイントをマウントし、回復ボリュームとして使用します。

7. **[ファイルの参照と回復]** ページで、 **[参照]** を選択して Windows エクスプローラーを開き、必要なファイルとフォルダーを見つけます。

    ![データの回復ウィザードの [ファイルの参照と回復] ページのスクリーンショット (同じコンピューターに復元)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. Windows エクスプローラーで、復元するファイルとフォルダーをコピーし、それをサーバーまたはコンピューターの任意のローカルの場所に貼り付けます。 回復ボリュームから直接ファイルを開くか、またはストリーミングして、正しいバージョンを回復していることを確認できます。

    ![[コピー] が強調表示された Windows エクスプローラーのスクリーンショット (同じコンピューターに復元)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. 完了したら、 **[ファイルの参照と回復]** ページで **[マウント解除]** を選択します。 その後、 **[はい]** を選択して、ボリュームをマウント解除することを確認します。

    ![データの回復ウィザードの [ファイルの参照と回復] ページのスクリーンショット (同じコンピューターに復元) - 回復ボリュームのマウント解除の確認](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > **[マウント解除]** を選択しない場合、回復ボリュームは、マウントされた時刻から 6 時間マウントされたままになります。 ただし、ファイルのコピーが継続している場合は、マウント時間が最大 7 日間に延長されます。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームがマウントされている間に実行されるようにスケジュールされたバックアップ操作はすべて、回復ボリュームがマウント解除された後に実行されます。
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>インスタント リストアを使用してデータを別のコンピューターに復元する

サーバー全体が失われた場合でも、Azure Backup から別のコンピューターにデータを回復できます。 次の手順はそのワークフローを示しています。

これらの手順には、次の用語が含まれています。

* *ソース コンピューター* – バックアップが取得され、現在は使用できない元のコンピューター。
* *ターゲット コンピューター* – データの回復先となるコンピューター。
* *サンプルのコンテナー* – ソース コンピューターとターゲット コンピューターが登録されている Recovery Services コンテナー。

> [!NOTE]
> バックアップを、以前のバージョンのオペレーティング システムを実行しているターゲット コンピューターに復元することはできません。 たとえば、Windows 7 コンピューターから取得されたバックアップは Windows 7 (以降の) コンピューターで復元できます。 Windows 10 コンピューターから取得されたバックアップは、Windows 7 コンピューターに復元できません。
>
>

1. ターゲット コンピューターで **Microsoft Azure Backup** スナップインを開きます。

2. ターゲット コンピューターとソース コンピューターが同じ Recovery Services コンテナーに登録されていることを確認します。

3. **[データの回復]** を選択して **[データの回復ウィザード]** を開きます。

    ![[データの回復] が強調表示された Azure Backup のスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/recover.png)

4. **[使用の開始]** ページで、 **[別のサーバー]** を選択します。

    ![データの回復ウィザードの [使用の開始] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. サンプルのコンテナーに対応するコンテナー資格情報ファイルを指定し、 **[次へ]** を選択します。

    コンテナー資格情報ファイルが無効である (または期限が切れている) 場合は、Azure portal で[サンプルのコンテナーから新しいコンテナー資格情報ファイルをダウンロードします](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-)。 有効なコンテナー資格情報を指定すると、対応するバックアップ コンテナーの名前が表示されます。

6. **[バックアップ サーバーの選択]** ページで、表示されているコンピューターの一覧からソース コンピューターを選択し、パスフレーズを指定します。 **[次へ]** を選択します。

    ![データの回復ウィザードの [バックアップ サーバーの選択] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **[回復モードの選択]** ページで、 **[個別のファイルとフォルダー]**  >  **[次へ]** を選択します。

    ![データの回復ウィザードの [回復モードの選択] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **[ボリュームと日付の選択]** ページで、復元するファイルとフォルダーを含むボリュームを選択します。

    カレンダーで回復ポイントを選択します。 **太字** になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 1 日の中で複数の復旧ポイントを使用できる場合は、 **[時間]** ドロップダウン メニューから特定の復旧ポイントを選択します。

    ![データの回復ウィザードの [ボリュームと日付の選択] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. **[マウント]** を選択して、復旧ポイントをターゲット コンピューター上の回復ボリュームとしてローカルでマウントします。

10. **[ファイルの参照と回復]** ページで、 **[参照]** を選択して Windows エクスプローラーを開き、必要なファイルとフォルダーを見つけます。

    ![データの回復ウィザードの [ファイルの参照と回復] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows エクスプローラーで、回復ボリュームからファイルとフォルダーをコピーし、それをターゲット コンピューターの場所に貼り付けます。 回復ボリュームから直接ファイルを開くか、またはストリーミングして、正しいバージョンが回復されていることを確認できます。

    ![[コピー] が強調表示された Windows エクスプローラーのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. 完了したら、 **[ファイルの参照と回復]** ページで **[マウント解除]** を選択します。 その後、 **[はい]** を選択して、ボリュームをマウント解除することを確認します。

    ![ボリュームをマウント解除します (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > **[マウント解除]** を選択しない場合、回復ボリュームは、マウントされた時刻から 6 時間マウントされたままになります。 ただし、ファイル コピーが進行中の場合は、マウント時間が最大 24 時間まで延長されます。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームがマウントされている間に実行されるようにスケジュールされたバックアップ操作はすべて、回復ボリュームがマウント解除された後に実行されます。
    >

## <a name="next-steps"></a>次のステップ

* ファイルとフォルダーを回復したので、 [バックアップを管理](backup-azure-manage-windows-server.md)できます。

* [ファイルとフォルダーのバックアップに関する一般的な質問](backup-azure-file-folder-backup-faq.yml)を確認する。
