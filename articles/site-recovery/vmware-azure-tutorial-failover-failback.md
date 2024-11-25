---
title: Site Recovery を使用して VMware VM を Azure にフェールオーバーする - クラシック
description: Azure Site Recovery (クラシック) で VMware VM を Azure にフェールオーバーする方法について説明します
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 7c1de30fee09da94546ea0f8b5835477e0f83a2a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445307"
---
# <a name="fail-over-vmware-vms---classic"></a>VMware VM をフェールオーバーする - クラシック

この記事では、[Azure Site Recovery](site-recovery-overview.md) (クラシック) を使用して、オンプレミスの VMware 仮想マシン (VM) を Azure にフェールオーバーする方法について説明します。

プレビュー リリースでのフェールオーバーの詳細については、[この記事](vmware-azure-tutorial-failover-failback-preview.md)を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * VMware VM のプロパティが Azure の要件に準拠していることを確認する。
> * 特定の VM を Azure にフェールオーバーする。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションが使用されます。可能な設定とパスがすべて示されているわけではありません。 フェールオーバーについて詳しく学習する場合は、「[Fail over VMs and physical servers](site-recovery-failover.md)」 (VM と物理サーバーをフェールオーバーする) を参照してください。

さまざまな種類のフェールオーバーの[詳細を参照してください](failover-failback-overview.md#types-of-failover)。 1 つの復旧計画で複数の VM をフェールオーバーする場合は、[この記事](site-recovery-failover.md)を参照してください。

## <a name="before-you-start"></a>開始する前に

前のチュートリアルを完了します。

1. VMware VM、Hyper-V VM、および物理マシンから Azure へのオンプレミス ディザスター リカバリー用に [Azure を設定](tutorial-prepare-azure.md)したことを確認します。
2. ディザスター リカバリーのためにオンプレミスの [VMware](vmware-azure-tutorial-prepare-on-premises.md) 環境を準備します。
3. [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定します。
4. [ディザスター リカバリー訓練](tutorial-dr-drill-azure.md)を実行して、すべてが予想どおりに動作することを確認します。

## <a name="verify-vm-properties"></a>VM のプロパティを確認する

フェールオーバーを実行する前に、VM のプロパティを確認し、VM で [Azure の要件](vmware-physical-azure-support-matrix.md#replicated-machines)が満たされていることを確認します。

次のようにプロパティを確認します。

1. **[保護されたアイテム]** で、**[レプリケートされたアイテム]** を選択してから、確認する VM を選びます。

2. **[レプリケートされたアイテム]** ウィンドウには、VM 情報、正常性状態、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** を選択して、詳細を表示します。

3. **[コンピューティングとネットワーク]** で、必要に応じて、これらのプロパティを変更することができます。
    * Azure の名前
    * Resource group
    * ターゲット サイズ
    * [可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)
    * マネージド ディスクの設定

4. 以下を含む、ネットワーク設定を表示して変更することができます。

    * Azure VM がフェールオーバー後に配置される、ネットワークとサブネット。
    * それに割り当てられる IP アドレス。

5. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。

## <a name="run-a-failover-to-azure"></a>Azure へのフェールオーバーを実行する

1. **[設定]**  >  **[レプリケートされたアイテム]** で、フェールオーバーする VM を選択してから、 **[フェールオーバー]** を選びます。
2. **[フェールオーバー]** で、フェールオーバー先の **復旧ポイント** を選択します。 次のいずれかのオプションを使うことができます。
   * **Latest**:最初に、Site Recovery に送信されるすべてのデータを処理します。 フェールオーバー後に作成された Azure VM では、フェールオーバーがトリガーされた時点で Site Recovery にレプリケートされたすべてのデータが保持されているため、最も低い復旧ポイントの目標 (RPO) が提供されます。
   * **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントに VM をフェールオーバーします。 このオプションでは、未処理のデータの処理に時間がかからないため、低い RTO (回復ポイントの目標) が提供されます。
   * **最新のアプリ整合性**:このオプションでは、Site Recovery によって処理されたアプリ整合性の最新の復旧ポイントに VM をフェールオーバーします。
   * **Custom**:このオプションを使用して、復旧ポイントを指定することができます。

3. フェールオーバーをトリガーする前にソース VM のシャットダウンを試行するには、**[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 シャットダウンが失敗した場合でも、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

一部のシナリオでは、フェールオーバーで追加処理が必要です。これが完了するまで約 8 分から 10 分かかります。 以下の場合は、テスト フェールオーバーの時間が長くなることがあります。

* 9\.8 よりも前のバージョンのモビリティ サービスを実行している VMware VM。
* 物理サーバー。
* VMware Linux VM。
* 物理サーバーとして保護されている Hyper-V VM。
* DHCP サービスが有効になっていない VMware VM。
* 次のブート ドライバーがない VMware VM: storvsc、vmbus、storflt、intelide、atapi。

> [!WARNING]
> 進行中のフェールオーバーを取り消さないでください。 フェールオーバーが開始される前に、VM のレプリケーションが停止されます。 進行中のフェールオーバーをキャンセルすると、フェールオーバーは停止しますが、VM が再びレプリケートされることはありません。

## <a name="connect-to-failed-over-vm"></a>フェールオーバーされた VM に接続する

1. リモート デスクトップ プロトコル (RDP) と Secure Shell (SSH) を使用して、フェールオーバー後の Azure VM に接続する場合は、[要件が満たされていることを確認](failover-failback-overview.md#connect-to-azure-after-failover)します。
2. フェールオーバー後は、VM に移動し、それに[接続する](../virtual-machines/windows/connect-logon.md)ことで検証します。
3. フェールオーバー後に別の復旧ポイントを使用する場合は、**[復旧ポイントの変更]** を使用します。 次の手順でフェールオーバーをコミットした後、このオプションは使用できなくなります。
4. 検証後に、**[コミット]** を選択して、フェールオーバー後の VM の復旧ポイントを最終処理します。
5. コミット後、他の使用可能なすべての復旧ポイントが削除されます。 この手順でフェールオーバーが完了します。

>[!TIP]
> フェールオーバー後に接続の問題が発生した場合は、[トラブルシューティング ガイド](site-recovery-failover-to-azure-troubleshoot.md)に従ってください。


## <a name="next-steps"></a>次のステップ

フェールオーバー後、Azure VM をオンプレミスに再保護します。 次に、VM を再保護してオンプレミス サイトにレプリケートした後、準備が整ったら Azure からフェールバックします。

> [!div class="nextstepaction"]
> [Azure VM を再保護する](vmware-azure-reprotect.md)
> [Azure からフェールバックする](vmware-azure-failback.md)
