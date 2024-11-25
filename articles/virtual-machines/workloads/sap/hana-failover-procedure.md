---
title: SAP HANA on Azure (L インスタンス) 用ディザスター サイトへの HANA フェールオーバー手順 |Microsoft Docs
description: SAP HANA on Azure (Large Instances) 用ディザスター リカバリー サイトへのフェールオーバーを実行する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5720d1ce2281e8984db98b0dc900c4b9de8a52a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284871"
---
# <a name="disaster-recovery-failover-procedure"></a>ディザスター リカバリーのフェールオーバー手順

>[!IMPORTANT]
>この記事は、SAP HANA の管理ドキュメントや SAP Note の代わりになるものではありません。 SAP HANA の管理と操作 (特にバックアップ、復元、高可用性、ディザスター リカバリー (DR)) について、読者が十分な理解と専門知識を有していることを前提としています。 この記事では、SAP HANA Studio のスクリーンショットを示します。 SAP 管理ツールの画面およびツール自体の内容、構造、特徴は、SAP HANA のリリースごとに変わる可能性があります。

この記事では、SAP HANA on Azure Large Instances (BareMetal インフラストラクチャ) の DR サイトへのフェールオーバーの手順について説明します。 

## <a name="failover-scenarios-and-options"></a>フェールオーバーのシナリオとオプション

DR サイトにフェールオーバーするときは、2 つのケースを考慮する必要があります。

- SAP HANA データベースのデータを最新の状態に戻す必要がある場合。 この場合は、Microsoft に連絡することなく、セルフサービス スクリプトを使用してフェールオーバーを実行できます。 フェールバックの場合は Microsoft と協力する必要があります。
- 最新のレプリケートされたスナップショットではないストレージ スナップショットに復元する場合。 この場合は、Microsoft と連携する必要があります。 

>[!NOTE]
>以下の手順は、DR サイトの HANA Large Instances で実行する必要があります。 
 
レプリケートされた最新のストレージ スナップショットに復元するには、[SAP HANA on Azure 用の Microsoft スナップショット ツールに関するページ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)の「Perform full DR failover - azure_hana_dr_failover (フル DR フェールオーバーの実行 - azure_hana_dr_failover)」の手順に従います。 

複数の SAP HANA インスタンスをフェールオーバーする場合は、azure_hana_dr_failover コマンドを複数回実行する必要があります。 要求されたら、フェールオーバーして復元する SAP HANA の SID を入力します。 


実際のレプリケーション リレーションシップに影響を与えずに、DR フェールオーバーをテストすることもできます。 テスト フェールオーバーを実行するには、[SAP HANA on Azure 用の Microsoft スナップショット ツールに関するページ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)のテスト DR フェールオーバー (azure_hana_test_dr_failover) の実行手順に従います。 

>[!IMPORTANT]
>**フェールオーバー テスト** のプロセスによって DR サイト内に作成したインスタンス上では、運用トランザクションは実行 "*しないでください*"。 azure_hana_test_dr_failover コマンドでは、プライマリ サイトへのリレーションシップを持たない一連のボリュームが作成されます。 そのため、プライマリ サイトに同期することは *できません*。 

複数の SAP HANA インスタンスをテストする場合は、スクリプトを複数回実行します。 要求されたら、フェールオーバーをテストするインスタンスの SAP HANA SID を入力します。 

## <a name="set-dr-volumes-to-an-earlier-snapshot"></a>DR ボリュームを以前のスナップショットに設定する

何時間も前に削除されたデータを復旧するために DR サイトにフェールオーバーする必要があり、かつ DR ボリュームを以前のスナップショットに設定する必要があるとしましょう。 次の手順は、そのような場合に適用されます。 

1. 実行している DR HANA L インスタンスで、HANA の非運用インスタンスをシャットダウンします。 休止中の HANA 運用インスタンスはプレインストールされています。
1. SAP HANA プロセスが実行されていないことを確認します。 この確認には、次のコマンドを使用します。

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      出力では、**hdbdaemon** プロセスが停止状態であり、実行中または開始済み状態の他の HANA プロセスが存在しないことが示されます。
1. ディザスター リカバリー サイトをどのスナップショット名やどの SAP HANA バックアップ ID に復元するかを確認します。 実際のディザスター リカバリーの場合、通常、このスナップショットは最新のスナップショットです。 失われたデータを復旧する必要がある場合は、古いスナップショットを選択します。
1. 高優先度のサポート要求を通じて Azure サポートに問い合わせます。 スナップショットの名前と日付を添えて、スナップショットの復元を依頼してください。 DR サイトにおける HANA バックアップ ID で指定することもできます。 既定では、オペレーション チームは /hana/data ボリュームのみを復元します。 /hana/logbackups ボリュームも必要な場合は、その点を明確に伝える必要があります。 " */hana/shared ボリュームは復元しないでください。* " 代わりに、PRD の /hana/shared ボリュームを再びマウントした後に、 **.snapshot** ディレクトリとそのサブディレクトリの global.ini など、特定のファイルを選択してください。 

   Microsoft の担当部門では、これらの手順を実行します。

   a. 運用ボリュームからディザスター リカバリー ボリュームへのスナップショットのレプリケーションを停止します。 障害の原因が実稼働サイトの障害の場合、既に中断状態になっている可能性があります。
   
   b. お客様が選択したストレージ スナップショット名またはバックアップ ID が指定されたスナップショットを、ディザスター リカバリー ボリューム上に復元します。
   
復元後、ディザスター リカバリー ボリュームは、DR リージョンの HANA Large Instances にマウントできる状態になります。
      
1. ディザスター リカバリー サイトの HANA L インスタンス ユニットにディザスター リカバリー ボリュームをマウントします。 
1. 休止中の SAP HANA 運用インスタンスを起動します。
1. 回復ポイントの目標 (RPO) 時間を短縮するためにトランザクション ログ バックアップのログをコピーするように選択したとします。 次に、トランザクションログのバックアップを、新たにマウントされた DR の /hana/logbackups ディレクトリにマージします。 既存のバックアップは上書きしないでください。 ストレージ スナップショットの最新のレプリケーションでレプリケートされなかった新しいバックアップをコピーします。
1. また、DR Azure リージョン内の /hana/shared/PRD ボリュームにレプリケートされなかったスナップショットから、1 つのファイルを復元することもできます。

## <a name="recover-the-sap-hana-production-instance"></a>SAP HANA 運用インスタンスを復旧する

次の手順は、復元されたストレージ スナップショットと利用可能なトランザクション ログ バックアップから、SAP HANA 運用インスタンスを復旧する方法を示しています。

1. SAP HANA Studio を使用して、バックアップ場所を **/hana/logbackups** に変更します。

   ![DR の復旧のバックアップ場所を変更する](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA によってバックアップ ファイルの場所がスキャンされ、復元対象となる最新のトランザクション ログ バックアップが提示されます。 次のような画面が表示されるまで、スキャンに数分かかることがあります。

   ![DR 復旧のトランザクション ログ バックアップの一覧](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 既定の設定の一部を調整します。

      - **[Use Delta Backups]\(差分バックアップを使用する\)** をオフにします。
      - **[Initialize Log Area]\(ログ領域を初期化する\)** をオンにします。

   ![ログ領域の初期化を設定する](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. **[完了]** を選択します。

   ![DR の復元を完了する](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

次のような進行状況ウィンドウが表示されます。 この例は、3 ノードのスケールアウト SAP HANA 構成のディザスター リカバリーの復元を示していることに注意してください。

![復元の進行状況](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

**[Finish]\(完了\)** 画面で復元の応答が停止し、進行状況画面が表示されない場合は、ワーカー ノード上ですべての SAP HANA インスタンスが実行されていることを確認します。 必要に応じて、SAP HANA インスタンスを手動で起動します。


## <a name="failback-from-a-dr-to-a-production-site"></a>DR サイトから運用サイトへのフェールバック

DR サイトから実稼働サイトにフェールバックすることができます。 DR サイトへのフェールオーバーが運用 Azure リージョンでの問題に起因するものであり、失われたデータを回復する必要はないというシナリオについて見てみましょう。 

ディザスター リカバリー サイトで、しばらく SAP 運用ワークロードを実行している状態です。 運用サイトでの問題が解決したら、運用サイトでフェールバックを実行できます。 データが失われないように、実稼働サイトに戻す手順では、複数の手順と SAP HANA on Azure オペレーション チームとの緊密な連携が必要となります。 問題が解決された後は、お客様のタイミングで、運用サイトへの同期を開始する操作をオペレーション チームに依頼します。

次の手順に従います。

1. SAP HANA on Azure オペレーション チームが、DR ストレージ ボリュームから運用ストレージ ボリュームを同期する依頼を受けます。この時点で、DR ストレージ ボリュームは運用状態になっています。 この状態では、実稼働サイトの HANA L インスタンスはシャットダウンされています。
1. SAP HANA on Azure オペレーション チームはレプリケーションを監視し、お客様に通知する前にキャッチアップが完了していることを確認します。
1. お客様は、ディザスター リカバリー サイトの HANA 運用インスタンスを使用しているアプリケーションをシャットダウンします。 次に、HANA トランザクション ログのバックアップを実行します。 次に、ディザスター リカバリー サイトの HANA L インスタンス上で実行されている HANA インスタンスを停止します。
1. 今度は、オペレーション チームがディスク ボリュームを手動で再同期します。
1. SAP HANA on Azure オペレーション チームは、実稼働サイト内で HANA L インスタンスをもう一度起動し、お客様に引き渡します 。 HANA L インスタンスの起動時に、SAP HANA インスタンスがシャットダウンされていることを確認します。
1. 以前に DR サイトへのフェールオーバーを実行したときと同じデータベース復元手順を実行します。

## <a name="monitor-disaster-recovery-replication"></a>ディザスター リカバリー レプリケーションの監視

ストレージ レプリケーションの進行状況の状態を監視するには、`azure_hana_replication_status` スクリプトを実行します。 このコマンドを正常に機能させるには、ディザスター リカバリーの場所で実行されているユニットからコマンドを実行する必要があります。 このコマンドは、レプリケーションがアクティブであるかどうかにかかわらず機能します。 DR の場所でテナントの各 HANA L インスタンスに対してコマンドを実行できます。 これを使用して、ブート ボリュームの詳細情報を取得することはできません。 

コマンドとその出力の詳細については、[SAP HANA on Azure 用の Microsoft スナップショット ツールに関するページ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)の「Get DR replication status - azure_hana_replication_status (DR レプリケーション状態の取得 - azure_hana_replication_status)」を参照してください。


## <a name="next-steps"></a>次のステップ

SAP HANA on Azure (Large Instances) の監視について学習します。

> [!div class="nextstepaction"]
> [SAP HANA on Azure (Large Instances) を監視する](troubleshooting-monitoring.md)
