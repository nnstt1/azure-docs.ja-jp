---
title: Azure VM のサイズ - HPC | Microsoft Docs
description: Azure のハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 11c3fadd7f95f07bbcbf095e8d6a126022bbea0b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697923"
---
# <a name="high-performance-computing-vm-sizes"></a>ハイ パフォーマンス コンピューティング VM のサイズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

> [!TIP]
> **[仮想マシン セレクター ツール](https://aka.ms/vm-selector)** を使って、ワークロードに最適な他のサイズを見つけてください。

Azure H シリーズ仮想マシン (VM) は、実環境のさまざまな HPC ワークロードに対して、リーダー クラスのパフォーマンス、スケーラビリティ、およびコスト効率が実現されるように設計されています。

[HBv3 シリーズ](hbv3-series.md) VM は、流体力学、明示的および暗黙的な有限要素分析、気象モデリング、地震処理、貯水池シミュレーション、RTL シミュレーションなど、HPC アプリケーションのために最適化されています。 HBv3 VM は、AMD EPYC™ 7003 シリーズ (Milan) CPU を最大 120 コア、448 GB の RAM を備え、ハイパースレッド機能はありません。 HBv3 シリーズ VM も毎秒 350 GB のメモリ帯域幅を提供します。L3 キャッシュはコアあたり最大 32 MB です。ブロック デバイス SSD パフォーマンスは最大毎秒 7 GB です。クロック周波数は最大 3.675 GHz です。 

HBv3 シリーズ VM はすべて、NVIDIA ネットワークの毎秒 200 Gb の HDR InfiniBand を備えており、スーパーコンピューター規模の MPI ワークロードを可能にします。 これらの VM は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 HDR InfiniBand ファブリックはまた、アダプティブ ルーティング、および標準 RC トランスポートと UD トランスポートに加え、動的接続トランスポート (DCT) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、これらを使用することを強くお勧めします。

[HBv2 シリーズ](hbv2-series.md) VM は、流体力学、有限要素解析、貯留層シミュレーションなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 120 個の AMD EPYC 7742 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 各 HBv2 VM では、最大 340 GB/秒のメモリ帯域幅が提供され、最大 4 テラフロップの FP64 コンピューティングが提供されます。

HBv2 VM は 200 Gb/秒の Mellanox HDR InfiniBand を、HB および HC シリーズ VM は、どちらも 100 Gb/秒の Mellanox EDR InfiniBand を備えています。 これらの各種類の VM は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 HBv2 VM は、アダプティブ ルーティング、および標準 RC トランスポートと UD トランスポートに加え、動的接続トランスポート (DCT) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、これらを使用することを強くお勧めします。

[HB シリーズ](hb-series.md) VM は、流体力学、陽解法有限要素解析、気象モデリングなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HB VM は 60 個の AMD EPYC 7551 プロセッサ コア、CPU コアあたり 4 GB の RAM を搭載し、ハイパースレッディングはありません。 AMD EPYC プラットフォームは、260 GB/秒を超えるメモリ帯域幅を提供します。

[HC シリーズ](hc-series.md) VM は、陰解法有限要素解析、分子力学、計算化学などの、高密度計算に基づいたアプリケーション向けに最適化されています。 HC VM は 44 個の Intel Xeon Platinum 8168 プロセッサ コア、CPU コアあたり 8 GB の RAM を搭載し、ハイパースレッディングはありません。 Intel Xeon Platinum プラットフォームは、Intel Math Kernel Library などの Intel の豊富なソフトウェア ツールのエコシステムをサポートしています。

[H シリーズ](h-series.md) VM は、高い CPU 周波数またはコアあたり大容量メモリの要件に基づいたアプリケーション向けに最適化されています。 H シリーズ VM は 8 または 16 個の Intel Xeon E5 2667 v3 プロセッサ コア、CPU コアあたり 7 または 14 GB の RAM を搭載し、ハイパースレッディングはありません。 H シリーズは、一貫した RDMA パフォーマンスを得るために、非ブロッキングのファット ツリー構成内に 56 Gb/秒の Mellanox FDR InfiniBand を搭載しています。 H シリーズ VM は、Intel MPI 5.x と MS-MPI をサポートしています。

> [!NOTE]
> HBv3、HBv2、HB、HC シリーズの VM はすべて、物理サーバーに排他的にアクセスできます。 物理サーバーごとに 1 つの VM のみが存在します。また、これらの VM サイズについては、他の VM との共有マルチテナントは存在しません。

> [!NOTE]
> [A8 ～ A11 の VM](./sizes-previous-gen.md#a-series---compute-intensive-instances) は 2021 年 3 月の時点で廃止されています。 これらのサイズの新しい VM のデプロイは実行できなくなりました。 既存の VM がある場合は、他の VM サイズへの移行など、[HPC 移行ガイド](https://azure.microsoft.com/resources/hpc-migration-guide/)に記載されている今後の手順に関してのお知らせメールをご覧ください。

## <a name="rdma-capable-instances"></a>RDMA 対応のインスタンス

ほとんどの HPC VM サイズには、リモート ダイレクト メモリ アクセス (RDMA) 接続のためのネットワーク インターフェイスが備わっています。 'r' 表記で識別される一部の [N シリーズ](./nc-series.md)のサイズも RDMA に対応しています。 このインターフェイスは、標準の Azure イーサネット ネットワーク インターフェイスに加えて、他の VM サイズでも利用可能です。

この 2 番目のインターフェイスにより、RDMA 対応インスタンスは InfiniBand (IB) ネットワークを介して通信することができ、HBv3、HBv2 では HDR のレートで、HB、HC、NDv2 では EDR のレートで、H16r、H16mr、および RDMA 対応 N シリーズの他の仮想マシンでは FDR のレートで動作します。 これらの RDMA 機能により、メッセージ パッシング インターフェイス (MPI) ベースのアプリケーションのスケーラビリティとパフォーマンスが向上します。

> [!NOTE]
> **SR-IOV サポート**: Azure HPC では現在、InfiniBand に対して SR-IOV 対応であるかどうかに応じて、2 つのクラスの VM があります。 現時点では、Azure の新しい世代、RDMA 対応、または InfiniBand 対応の VM は、H16r、H16mr、NC24r を除き、ほぼすべてが SR-IOV 対応です。
> RDMA は、InfiniBand (IB) ネットワーク経由でのみ有効で、RDMA 対応のすべての VM でサポートされています。
> IP over IB は、SR-IOV 対応の VM のみでサポートされています。
> RDMA は、イーサネット ネットワーク経由では有効になっていません。

- **オペレーティング システム** - CentOS、RHEL、Ubuntu、SUSE などの Linux ディストリビューションが一般的に使用されています。 すべての HPC シリーズ VM で Windows Server 2016 およびそれ以降のバージョンがサポートされています。 SR-IOV に対応していない VM では、Windows Server 2012 R2 および Windows Server 2012 もサポートされています。 [HBv2 以降と、(仮想または物理) コアが 64 個を超える VM サイズでは Windows Server 2012 R2 はサポートされていない](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)ことに注意してください。 Marketplace でサポートされている VM イメージの一覧と、それらを適切に構成する方法については、「[VM イメージ](./workloads/hpc/configure.md)」を参照してください。 それぞれの VM サイズのページには、ソフトウェア スタックのサポートの一覧も記載されています。

- **InfiniBand とドライバー** - InfiniBand が有効になっている VM では、RDMA を有効にするための適切なドライバーが必要です。 Marketplace でサポートされている VM イメージの一覧と、それらを適切に構成する方法については、「[VM イメージ](./workloads/hpc/configure.md)」を参照してください。 VM 拡張機能について、または InfiniBand ドライバーの手動インストールについて「[InfiniBand の有効化](./workloads/hpc/enable-infiniband.md)」も参照してください。

- **MPI** - Azure の SR-IOV 対応 VM サイズでは、ほぼすべてのフレーバーの MPI を Mellanox OFED と一緒に使用できます。 SR-IOV に対応していない VM の場合、サポートされている MPI 実装では、VM 間の通信に Microsoft Network Direct (ND) インターフェイスが使用されます。 そのため、Intel MPI 5.x および Microsoft MPI (MS-MPI) 2012 R2 以降のバージョンのみがサポートされています。 より新しいバージョンの Intel MPI ランタイム ライブラリは、Azure RDMA ドライバーと互換性がある場合とない場合があります。 Azure 上の HPC VM での MPI の設定の詳細については、[HPC 用の MPI のセットアップ](./workloads/hpc/setup-mpi.md)に関するページを参照してください。

  > [!NOTE]
  > **RDMA ネットワーク アドレス空間**: Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 Azure 仮想ネットワークにデプロイ済みのインスタンスで MPI アプリケーションを実行する場合、仮想ネットワークのアドレス空間が RDMA ネットワークと重複しないようにしてください。

## <a name="cluster-configuration-options"></a>クラスター構成オプション

Azure には、次に示すような、RDMA ネットワークを使用して通信できる HPC VM のクラスターを作成するためのオプションがいくつか用意されています。 

- **仮想マシン** - 同じスケール セットまたは可用性セット内に RDMA 対応の HPC VM をデプロイします (Azure Resource Manager デプロイ モデルを使用する場合)。 クラシック デプロイ モデルを使用する場合は、同じクラウド サービス内に VM をデプロイします。

- **Virtual Machine Scale Sets** - 仮想マシン スケール セットで、スケール セット内の InfiniBand 通信に対して単一の配置グループにデプロイを制限するようにします。 たとえば、Resource Manager テンプレートでは、`singlePlacementGroup` プロパティを `true` に設定します。 `singlePlacementGroup=true` で起動できるスケール セットの最大サイズは、既定で 100 VM に制限されることに注意してください。 HPC ジョブ スケールにおいて、1 つのテナントで 100 を超える VM が必要な場合は、増加を要求できます。[オンライン カスタマー サポートに申請](../azure-portal/supportability/how-to-create-azure-support-request.md) (無料) してください。 1 つのスケール セットの VM 数の上限は 300 まで増やすことができます。 可用性セットを利用して VM をデプロイするとき、上限は可用性セットあたり 200 VM です。

  > [!NOTE]
  > **仮想マシン間の MPI**: 仮想マシン (VM) 間で RDMA (MPI 通信の使用など) が必要な場合は、VM が同じ仮想マシン スケール セットまたは可用性セットに含まれていることを確認します。

- **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/) を使用して HPC クラスターを作成し、MPI ジョブを実行します。

- **Azure Batch** - [Azure Batch](../batch/index.yml) プールを作成して、MPI ワークロードを実行します。 Azure Batch で MPI アプリケーションを実行するときにコンピューティング集中型インスタンスを使用するには、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。

- **Microsoft HPC Pack** - [HPC Pack](/powershell/high-performance-computing/overview) には、RDMA 対応の Linux VM 上にデプロイした場合に Azure RDMA ネットワークを使用する MS-MPI 用のランタイム環境が含まれています。 デプロイの例については、[HPC Pack を使用して Linux RDMA クラスターをセットアップして MPI アプリケーションを実行する](/powershell/high-performance-computing/hpcpack-linux-openfoam)方法に関するページを参照してください。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

- **Azure サブスクリプション** – 多数のコンピューティング集中型インスタンスをデプロイするには、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

- **料金と利用可能な製品** - Azure リージョン別の [VM 料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)および [利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページをご確認ください。

- **コア クォータ** – 場合によっては、Azure サブスクリプションのコア クォータを既定値から増やす必要があります。 サブスクリプションによっては、H シリーズを含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../azure-portal/supportability/how-to-create-azure-support-request.md) (無料) してください。 (既定の制限は、サブスクリプション カテゴリによって異なる場合があります。)

  > [!NOTE]
  > 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 Azure のクォータは容量保証ではなくクレジット制限です。 クォータに関係なく、使用したコアに対してのみ課金されます。
  
- **仮想ネットワーク** – コンピューティング集中型インスタンスを使用するために、Azure [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) は不要です。 ただし、多くのデプロイでは、少なくともクラウド ベースの Azure Virtual Network またはサイト間接続 (オンプレミス リソースへのアクセスが必要な場合) が必要になります。 必要に応じて、新しい仮想ネットワークを作成して、インスタンスをデプロイします。 アフィニティ グループ内の仮想ネットワークにコンピューティング集中型の VM を追加することはできません。

- **サイズ変更** - 特殊なハードウェアが使用されるため、コンピューティング集中型インスタンスのサイズ変更は同じサイズのファミリ (H シリーズまたは N シリーズ) 内でのみ可能です。 たとえば、H シリーズの VM は H シリーズのあるサイズから別のサイズにのみ変更できます。 VM によっては、InfiniBand ドライバーのサポートと NVMe ディスクに関するその他の考慮事項に注意する必要があります。


## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

- [VM の構成](./workloads/hpc/configure.md)、[InfiniBand の有効化](./workloads/hpc/enable-infiniband.md)、[MPI の設定](./workloads/hpc/setup-mpi.md)、[HPC ワークロード](./workloads/hpc/overview.md)での Azure 用の HPC アプリケーションの最適化について学習します。
- [HBv3 シリーズの概要](./workloads/hpc/hbv3-series-overview.md)および [HC シリーズの概要](./workloads/hpc/hc-series-overview.md)に関する記事を確認します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
