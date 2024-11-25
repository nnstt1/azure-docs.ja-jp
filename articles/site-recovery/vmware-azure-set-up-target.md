---
title: Azure Site Recovery で VMware VM レプリケーション ターゲットを準備する
description: この記事では、Azure への VMware VM レプリケーションの Azure ターゲット環境を準備する方法について説明します。
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: ad52303ed03f5894c09308639ed5281854deae40
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576590"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>VMware VM または物理サーバーの Azure へのディザスター リカバリーのためのターゲット環境を準備する

この記事では、VMware 仮想マシンおよび物理サーバーの Azure へのレプリケーションを開始するための Azure ターゲット環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は以下を前提としています。
- Recovery Services コンテナーを [Azure portal](https://portal.azure.com "Azure portal") 上で作成し、ソース マシンを保護します
- ソースの[VMware の仮想マシン](vmware-azure-set-up-source.md)および[物理サーバー](physical-azure-set-up-source.md)を Azure にレプリケートできるようにオンプレミス環境をセットアップします。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1: 保護の目標の選択** と **ステップ 2: ソースの準備** を完了した後は、**ステップ 3: ターゲットの準備** を行います。

![ターゲットを準備する](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、仮想マシンおよび物理サーバーのレプリケート先のサブスクリプションを選びます。
2. **デプロイメント モデル:** デプロイメント モデルを選びます (クラシックまたは Resource Manager)。

選んだデプロイ モデルに基づいて検証が実行され、ご自分の仮想マシンおよび物理サーバーのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、仮想ネットワークが少なくとも 1 つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

仮想ネットワークがない場合は、ページの上部にある **[+ ネットワーク]** ボタンをクリックして作成できます。

## <a name="next-steps"></a>次のステップ
[レプリケーションの設定を構成する](vmware-azure-set-up-replication.md)。
