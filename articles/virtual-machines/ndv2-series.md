---
title: NDv2 シリーズ
description: NDv2 シリーズ VM の仕様。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 80f96605069e2bfdc7596831018a81c09a0c7a9a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060423"
---
# <a name="updated-ndv2-series"></a>更新された NDv2 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブルなスケール セット :heavy_check_mark: 均一スケール セット

NDv2 シリーズは、きわめて要求の厳しい GPU アクセラレーション AI、機械学習、シミュレーション、HPC ワークロードのニーズに合わせて設計された GPU ファミリに新たに追加された仮想マシンです。

NDv2 は、それぞれ 32 GB の GPU メモリを搭載した、NVIDIA Tesla V100 NVLINK 接続の GPU を 8 個備えています。 また、各 NDv2 VM には、ハイパースレッド非対応の Intel Xeon Platinum 8168 (Skylake) コアが 40 個と 672 GiB のシステム メモリが備わっています。

NDv2 インスタンスは、CUDA GPU 最適化計算カーネルと、GPU アクセラレーションを "標準" でサポートするさまざまな AI、ML、分析ツール (TensorFlow、Pytorch、Caffe、RAPIDS フレームワークなど) を活用して、HPC や AI ワークロードで優れたパフォーマンスを発揮します。

特筆すべき点として、NDv2 は、計算量の多いワークロードのスケールアップ (VM あたり 8 個の GPU を使用) とスケールアウト (複数の VM を連携) の両方に対応するように構築されています。 NDv2 シリーズは現在、HB シリーズの HPC VM と同様の 100 Gigabit InfiniBand EDR バックエンド ネットワークをサポートしており、ハイパフォーマンスのクラスタリングによって、AI と ML の分散トレーニングを含む並列シナリオに対応します。 このバックエンド ネットワークは、NVIDIA の NCCL2 ライブラリで採用されているプロトコルも含め、主要な InfiniBand プロトコルをすべてサポートしているため、GPU のシームレスなクラスタリングが実現します。

> [!IMPORTANT]
> ND40rs_v2 VM で [InfiniBand を有効](./workloads/hpc/enable-infiniband.md)にする際は、4.7-1.0.0.1 Mellanox OFED ドライバーを使用してください。
>
> GPU メモリの増加により、新しい ND40rs_v2 VM では、[第 2 世代の VM](./generation-2.md) とマーケットプレース イメージを使用する必要があります。 
>
> 注意:GPU あたり 16 GB のメモリを備えた ND40s_v2 のプレビュー提供は終了し、最新の ND40rs_v2 に置き換えられました。

<br>

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[Ultra Disks](disks-types.md#ultra-disks): サポートされています (可用性、使用状況、およびパフォーマンスの[詳細](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)を参照) <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています<br>
InfiniBand: サポートされています<br>
Nvidia NVLink Interconnect:サポートされています<br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD): GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大ネットワーク帯域幅 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 Mbps | 8 |


## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーをインストールする必要があります。

[NVIDIA GPU ドライバー拡張機能](./extensions/hpccompute-gpu-linux.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](./extensions/overview.md)」をご覧ください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、[Linux 用 N シリーズ GPU ドライバーのセットアップ](./linux/n-series-driver-setup.md)に関するページを参照してください。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細については、「[Azure で利用できるディスクの種類](disks-types.md)」を参照してください

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
