---
title: Azure Migrate Server Migration を使用して Hyper-V VM を Azure に移行する
description: Azure Migrate Server Migration を使用してオンプレミスの Hyper-V VM を Azure に移行する方法について説明します。
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 4588e782d8d397d09b6d66f4855884c859e69e2b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278077"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V VM を Azure に移行する

この記事では、[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して、オンプレミスの Hyper-V VM を Azure に移行する方法について説明します。

これは、マシンを評価して Azure に移行する方法を示すシリーズの 3 番目のチュートリアルです。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。

 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate Server Migration ツールを追加します。
> * 移行したい VM を検出します。
> * VM のレプリケートを開始します。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * 完全な VM 移行を実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件


このチュートリアルを始める前に、次の準備が必要です。

1. Hyper-V の移行のアーキテクチャを[確認](hyper-v-migration-architecture.md)します。
1. Hyper-V ホストの移行の要件と、VM 移行のために Hyper-V ホストおよびクラスターがアクセスする必要がある Azure URL を[確認](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)します。
1. Azure に移行する Hyper-V VM の要件を[確認](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)します。
1. [Hyper-V VM を評価](tutorial-assess-hyper-v.md)したうえで Azure に移行することをお勧めしますが、必須ではありません。
1. 既に作成されているプロジェクトに移動するか、[新しいプロジェクトを作成](./create-manage-projects.md)します。
1. ご使用の Azure アカウントのアクセス許可を確認します。Azure アカウントには、VM を作成し、Azure マネージド ディスクに書き込むためのアクセス許可が必要です。

## <a name="download-the-provider"></a>プロバイダーをダウンロードする

Azure Migrate:Server Migration は、Hyper-V VM を移行するにあたり、ソフトウェア プロバイダー (Microsoft Azure Site Recovery プロバイダーおよび Microsoft Azure Recovery Services エージェント) を Hyper-V ホストまたはクラスター ノードにインストールします。 Hyper-V の移行に [Azure Migrate アプライアンス](migrate-appliance.md)は使用されないことに注意してください。

1. Azure Migrate プロジェクトの **[サーバー]** で、 **[Azure Migrate: Server Migration]** で、 **[検出]** をクリックします。
1. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
1. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
1. **[移行先のリージョンが <リージョン名> であることを確認してください]** を選択します。
1. **[リソースの作成]** をクリックします。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。
    - Azure Migrate Server Migration を使用した移行を既に設定してある場合は、リソースが以前に設定されているため、このオプションは表示されません。
    - このボタンのクリック後は、このプロジェクトのターゲット リージョンを変更することはできません。
    - 後続のすべての移行は、このリージョンに対して行われます。

1. **[Hyper-V ホスト サーバーを準備する]** で、Hyper-V レプリケーション プロバイダーと登録キー ファイルをダウンロードします。
    - Azure Migrate Server Migration に Hyper-V ホストを登録するには、登録キーが必要です。
    - キーは生成後 5 日間有効です。

    ![プロバイダーとキーをダウンロードする](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

1. プロバイダー セットアップ ファイルと登録キー ファイルを、レプリケートする VM が実行されている各 Hyper-V ホスト (またはクラスター ノード) にコピーします。  

## <a name="install-and-register-the-provider"></a>プロバイダーをインストールして登録する

プロバイダー セットアップ ファイルと登録キー ファイルを、レプリケートする VM が実行されている各 Hyper-V ホスト (またはクラスター ノード) にコピーします。 プロバイダーをインストールして登録するには、UI またはコマンドを使用して次の手順を実行します。

# <a name="using-ui"></a>[UI の使用](#tab/UI)

以下に説明するように、各ホストでプロバイダー セットアップ ファイルを実行します。

1. タスク バーのファイル アイコンをクリックして、インストーラー ファイルと登録キーがダウンロードされているフォルダーを開きます。
1. **AzureSiteRecoveryProvider.exe** ファイルを選択します。
    - プロバイダーのインストール ウィザードで、 **[オン (推奨)]** が選択されていることを確認し、 **[次へ]** をクリックします。
    - **[インストール]** を選択して、既定のインストール フォルダーをそのまま使用します。
    - **[登録]** を選択して、このサーバーを Azure Site Recovery 資格情報コンテナーに登録します。
    - **[参照]** をクリックします。
    - 登録キーを見つけて、 **[開く]** をクリックします。
    - **[次へ]** をクリックします。
    - **[プロキシを使用せずに直接 Azure Site Recovery に接続する]** が選択されていることを確認し、 **[次へ]** をクリックします。
    - **[完了]** をクリックします。


# <a name="using-commands"></a>[コマンドを使用する](#tab/commands) 

次に説明するように、各ホストで次のコマンドを実行します。

1. 次のように、マシン上のローカル フォルダー (.\Temp など) にインストーラー ファイル (AzureSiteRecoveryProvider.exe) の内容を展開します。

    ```
     AzureSiteRecoveryProvider.exe /q /x:.\Temp\Extracted
    ```

1. 抽出したファイルが含まれているフォルダーに移動します。

    ```
    cd .\Temp\Extracted
    ```
1. Hyper-V レプリケーション プロバイダーをインストールします。 結果は %Programdata%\ASRLogs\DRASetupWizard.log に記録されます。

    ```
    .\setupdr.exe /i 
    ```

1. Azure Migrate に Hyper-V ホストを登録します。

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Credentials <key file path>
    ```

    **プロキシ規則を構成する:** プロキシ経由でインターネットに接続する必要がある場合は、省略可能なパラメーター /proxyaddress と /proxyport を使用して、プロキシ アドレスを指定します (http://ProxyIPAddress) とプロキシ リスニング ポートの形式)。 認証されたプロキシの場合、省略可能なパラメーターである /proxyusername と /proxypassword を使用できます。

    ``` 
   "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password]
    ```
 
    **プロキシ バイパス規則を構成する:** プロキシ バイパス規則を構成するには、省略可能なパラメーター /AddBypassUrls を使用して、';' で区切られたプロキシのバイパス URL を指定し、次のコマンドを実行します。

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password] [/AddBypassUrls URLs]
    ```
    および
    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /configure /AddBypassUrls URLs 
    ```
---

ホストにプロバイダーをインストールした後、Azure portal に移動し、 **[マシンの検出]** で **[Finalize registration]\(登録の完了\)** をクリックします。

 ![登録の終了処理](./media/tutorial-migrate-hyper-v/finalize-registration.png) 

検出された VM が Azure Migrate Server Migration に表示されるまでに、登録を完了してから最大で 15 分かかることがあります。 VM が検出されると、 **[検出済みサーバー]** の数が増えます。

 ![検出済みサーバー](./media/tutorial-migrate-hyper-v/discovered-servers.png)

## <a name="replicate-hyper-v-vms"></a>Hyper-V VM をレプリケートする

検出が完了したら、Azure への Hyper-V VM のレプリケーションを開始できます。

> [!NOTE]
> 最大 10 台のマシンをまとめてレプリケートできます。 レプリケートするマシンがそれより多い場合は、10 台をひとまとまりとして同時にレプリケートしてください。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。
1. **[レプリケート]** の、 **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。 その後、 **[次へ:仮想マシン]** をクリックします。
1. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

        ![評価の選択](./media/tutorial-migrate-hyper-v/select-assessment.png)

1. **[仮想マシン]** で、必要に応じて VM を検索し、移行したい各 VM を確認します。 その後、 **[次へ: ターゲット設定]** をクリックします。

    ![VM を選択する](./media/tutorial-migrate-hyper-v/select-vms.png)

1. **[ターゲット設定]** で、移行先のターゲット リージョン、サブスクリプション、移行後に Azure VM が配置されるリソース グループを選択します。
1. **[レプリケーション ストレージ アカウント]** で、レプリケートされたデータを Azure に格納する Azure Storage アカウントを選択します。
1. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet/サブネットを選択します。
1. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
1. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 次に、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-hyper-v/target-settings.png)

1. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、および可用性構成 (前の手順で選択した場合) を確認します。 VM は [Azure の要件](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット**:移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

    ![VM コンピューティングの設定](./media/tutorial-migrate-hyper-v/compute-settings.png)

1. **[ディスク]** で、Azure にレプリケートする必要がある VM ディスクを指定します。 続けて、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。

    ![[レプリケート] ダイアログ ボックスの [ディスク] タブを表示するスクリーンショット。](./media/tutorial-migrate-hyper-v/disks.png)

1. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="provision-for-the-first-time"></a>初回のプロビジョニング

これが Azure Migrate プロジェクトでレプリケートしている最初の VM である場合は、Azure Migrate: Server Migration によって、プロジェクトと同じリソース グループにこれらのリソースが自動的にプロビジョニングされます。
- **キャッシュ ストレージ アカウント**: Hyper-V ホストにインストールされた Azure Site Recovery プロバイダー ソフトウェアが、レプリケーション用に構成された VM のレプリケーション データを、サブスクリプションのストレージ アカウント (キャッシュ ストレージ アカウントやログ ストレージ アカウントと呼ばれます) にアップロードします。 アップロードされたレプリケーション データは、その後 Azure Migrate サービスによって、ストレージ アカウントから、VM に対応するレプリカマネージド ディスクへとコピーされます。 VM のレプリケーションを構成する際は、キャッシュ ストレージ アカウントを指定する必要があります。Azure Migrate プロジェクトでレプリケーションを初めて構成する際、そのプロジェクト用のキャッシュ ストレージ アカウントが Azure Migrate ポータルによって自動的に作成されます。

## <a name="track-and-monitor"></a>追跡して監視する


- **[レプリケート]** をクリックすると、レプリケーションの開始ジョブが開始されます。
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure に定期的にレプリケートされます。

ジョブの状態は、ポータルの通知で追跡できます。

レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate: Server Migration** 内)。
![レプリケーションの監視](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>テスト移行を実行する


差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にこれを実行することを強くお勧めします。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。オンプレミスのマシンに影響はなく、稼働状態が維持され、レプリケーションが続行されます。
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 Azure VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

テスト移行を実行するには、次のようにします。


1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

1. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/tutorial-migrate-hyper-v/test-migrate.png)

1. **[テスト移行]** で、移行後に Azure VM が配置される Azure 仮想ネットワークを選択します。 非運用環境の仮想ネットワークを使用することをお勧めします。
1. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
1. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
1. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-hyper-v/clean-up.png)
    > [!NOTE]
    > SQL Server を実行しているサーバーを SQL VM RP に登録できるようになりました。これにより、SQL IaaS Agent 拡張機能を使用した自動修正、自動バックアップ、簡略化されたライセンス管理を利用できるようになります。
    >- **[管理]**  >  **[Replicating servers]\(レプリケートしているサーバー\)**  >  **[Machine containing SQL server]\(SQL サーバーを含むマシン\)**  >  **[コンピューティングとネットワーク]** を選択し、 **[はい]** を選択して SQL VM RP に登録します。
    >- アクティブなソフトウェア アシュアランスまたは SQL Server サブスクリプションの対象となっている SQL Server インスタンスがあり、移行するマシンに特典を適用する場合は、[SQL Server の Azure ハイブリッド特典] を選択します。

## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-hyper-v/replicate-servers.png)

1. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
1. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
1. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
1. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[レプリケーションの停止]** を選択します。 次の処理が実行されます。
    - オンプレミス マシンのレプリケーションを停止します。
    - Azure Migrate: Server Migration の **[サーバーをレプリケートしています]** のカウントからマシンを削除します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。
    - VM のレプリケーション状態情報をクリーンアップします。
1. [Azure VM での Windows のライセンス認証に関する問題を確認し、トラブルシューティングします。](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
1. ホスト名、データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
1. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
1. 移行された Azure VM インスタンスにトラフィックを切り替えます。
1. ローカル VM インベントリからオンプレミスの VM を削除します。
1. ローカル バックアップからオンプレミスの VM を削除します。
1. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細については、こちらを参照してください](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細については、こちらを参照してください](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Microsoft Defender for Cloud のジャスト イン タイム管理](../security-center/security-center-just-in-time.md)を利用し、インバウンド トラフィック アクセスをロックダウンし、制限します。
    - [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
-  [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) をデプロイして、リソースの使用率と消費量を監視します。

## <a name="next-steps"></a>次のステップ

Azure クラウド導入フレームワークでの[クラウド移行の工程](/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。
