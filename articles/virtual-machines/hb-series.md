---
title: HB シリーズ
description: HB シリーズ VM の仕様。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: de52171eb30cc82df37b098c0a9145dec47acdb6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082647"
---
# <a name="hb-series"></a>HB シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブルなスケール セット :heavy_check_mark: 均一スケール セット

HB シリーズ VM は、流体力学、陽解法有限要素解析、気象モデリングなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 60 個の AMD EPYC 7551 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 HB VM では、最大 260 GB/秒のメモリ帯域幅が提供されます。

HB シリーズ VM は、100 Gb/秒の Mellanox EDR InfiniBand を特徴としています。 これらの VM は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 これらの VM は、アダプティブ ルーティング、および標準 RC トランスポートと UD トランスポートに加え、動的接続トランスポート (DCT) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、これらを使用することをお勧めします。

[ACU](acu.md): 199-216<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[Ultra Disks](disks-types.md#ultra-disks): サポートされています (可用性、使用状況、およびパフォーマンスの[詳細](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)を参照) <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): サポートされています (パフォーマンスと潜在的な問題の[詳細](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965)) <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています<br>
<br>

| サイズ | vCPU | プロセッサ | メモリ (GiB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GiB) | 最大データ ディスク数 | 最大イーサネット vNIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 228 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 8 |

詳細については、下記を参照してください。
- [アーキテクチャと VM トポロジ](./workloads/hpc/hb-series-overview.md)
- サポート対象 OS など、サポート対象の[ソフトウェア スタック](./workloads/hpc/hb-series-overview.md#software-specifications)
- HB シリーズ VM の予想される[パフォーマンス](./workloads/hpc/hb-series-performance.md)

[!INCLUDE [hpc-include.md](./workloads/hpc/includes/hpc-include.md)]

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

- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
