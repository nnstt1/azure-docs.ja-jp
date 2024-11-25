---
title: InfiniBand 対応の H シリーズおよび N シリーズの VM でのハイ パフォーマンス コンピューティング - Azure Virtual Machines
description: HPC 用に最適化された InfiniBand 対応の H シリーズおよび N シリーズの VM の機能について説明します。
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 2b9f89126f49b75f5e232521f807f6db3ba9d4e9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693391"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>InfiniBand 対応の H シリーズおよび N シリーズの VM でのハイ パフォーマンス コンピューティング

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure の InfiniBand 対応の H シリーズおよび N シリーズ VM は、実環境のさまざまな HPC ワークロードと AI ワークロードに対するリーダー クラスのパフォーマンス、メッセージ パッシング インターフェイス (MPI) のスケーラビリティ、コスト効率が提供されるように設計されています。 これらのハイ パフォーマンス コンピューティング (HPC) に最適化された VM は、流体力学、地球モデリング、気象シミュレーションなど、サイエンスおよびエンジニアリングにおける非常に多くの計算を要する問題を解決するために使用されます。

これらの記事では、Azure で InfiniBand 対応の H シリーズおよび N シリーズ VM を使い始める方法と、スケーラビリティのために VM 上の HPC および AI ワークロードを最適に構成する方法について説明します。

## <a name="features-and-capabilities"></a>特徴と機能

InfiniBand 対応の H シリーズおよび N シリーズの VM は、最高の HPC パフォーマンス、MPI のスケーラビリティ、HPC ワークロードのコスト効率を実現するように設計されています。 VM の機能の詳細については、[H シリーズ](../../sizes-hpc.md) VM および [N シリーズ](../../sizes-gpu.md) VM のページを参照してください。

### <a name="rdma-and-infiniband"></a>RDMA と InfiniBand

[RDMA 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md) VM の通信は、低待機時間で高帯域幅の InfiniBand ネットワークを介して行われます。 そのような相互接続を介した RDMA 機能は、分散ノードの HPC および AI ワークロードのスケーラビリティとパフォーマンスを向上させるために重要です。 InfiniBand 対応の H シリーズおよび N シリーズの VM は、最適で一貫した RDMA のパフォーマンスのため、小直径設計の非ブロッキング ファット ツリーで接続されます。
InfiniBand 対応の VM での InfiniBand のセットアップについて詳しくは、「[InfiniBand の有効化](enable-infiniband.md)」をご覧ください。

### <a name="message-passing-interface"></a>Message Passing Interface

SR-IOV が有効な H シリーズと N シリーズでは、ほとんどすべての MPI ライブラリとバージョンがサポートされています。 最も一般的に使用される MPI ライブラリは、Intel MPI、OpenMPI、HPC-X、MVAPICH2、MPICH、Platform MPI などです。 すべてのリモート ダイレクト メモリ アクセス (RDMA) 動詞がサポートされています。
サポートされているさまざまな MPI ライブラリのインストールと最適な構成の詳細については、[MPI のセットアップ](setup-mpi.md)に関するページを参照してください。

## <a name="get-started"></a>はじめに

最初のステップでは、VM の仕様と [RDMA の機能](../../sizes-hpc.md#rdma-capable-instances)に基づいて、ワークロードに最適な [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md)の VM の種類を選択します。
2 番目に、InfiniBand を有効にして VM を構成します。 これを行うには、ドライバーが組み込まれている最適化された VM イメージの使用など、さまざまな方法があります。詳細については、[Linux 用の最適化](configure.md)に関するページおよび「[InfiniBand の有効化](enable-infiniband.md)」を参照してください。
3 番目に、分散ノードのワークロードの場合、MPI を適切に選択して構成することが重要です。 詳細については、[MPI のセットアップ](setup-mpi.md)に関するページを参照してください。
4 番目に、パフォーマンスとスケーラビリティのため、VM ファミリに固有のガイダンスに従って、ワークロードを最適に構成します。ガイダンスには、[HBv3 シリーズの概要](hbv3-series-overview.md)、[HC シリーズの概要](hc-series-overview.md)に関するページなどがあります。

## <a name="next-steps"></a>次のステップ

- InfiniBand 対応の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md)の VM の[構成と最適化](configure.md)について学習します。
- [HBv3 シリーズの概要](hb-series-overview.md)と [HC シリーズの概要](hc-series-overview.md)に関するページを参照して、パフォーマンスとスケーラビリティのためにワークロードを最適に構成する方法を学習します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- [Azure での HPC アプリケーションの最適化に関する学習モジュール](/learn/modules/optimize-tightly-coupled-hpc-apps/)を使用して、知識をテストします。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。