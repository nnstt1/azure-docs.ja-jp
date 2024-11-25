---
title: NVv4 シリーズ
description: NVv4 シリーズ VM の仕様。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 7c2b2edf30b4377ca90b65bb099ab3ceacc5f871
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008448"
---
# <a name="nvv4-series"></a>NVv4 シリーズ 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

NVv4 シリーズの仮想マシンにパワーを与えるのは [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU と AMD EPYC 7V12(Rome) CPU です。基本周波数は 2.45 GHz、全コアのピーク周波数は 3.1 GHz、シングルコアのピーク周波数は 3.3 GHz です。 NVv4 シリーズの場合、Azure では、部分的な GPU を備えた仮想マシンを導入しています。 16 GiB フレーム バッファーを備えた完全な GPU に対して 2 GiB フレーム バッファーを備えた 8 分の 1 の GPU を下限として、GPU で強化されたグラフィックス アプリケーションと仮想デスクトップに対して適正なサイズの仮想マシンを選択します。 NVv4 仮想マシンでは現在、Windows ゲスト オペレーティング システムのみがサポートされています。

<br>

[ACU](acu.md):230-260<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[Ultra Disks](disks-types.md#ultra-disks): サポートされています (可用性、使用状況、およびパフォーマンスの[詳細](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)を参照) <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています<br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2/1,000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4/2,000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8/4,000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8/8,000 |

<sup>1</sup> NVv4 シリーズ VM では、AMD 同時実行マルチスレッド技術を考慮しています



## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows を実行している Azure NVv4 シリーズ VM の GPU 機能を利用するには、AMD GPU ドライバーがインストールされている必要があります。

AMD GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストールおよび検証手順については、[Windows 用 N シリーズ AMD GPU ドライバーのセットアップ](./windows/n-series-amd-driver-setup.md)に関する記事を参照してください。


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