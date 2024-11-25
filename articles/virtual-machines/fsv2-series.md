---
title: Fsv2 シリーズ
description: Fsv2 シリーズ VM の仕様。
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 4c652a9292caf8e7ac198ac6922b11b78a85781e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401423"
---
# <a name="fsv2-series"></a>Fsv2 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Fsv2 シリーズは、Intel® Xeon® Platinum 8272CL (Cascade Lake) プロセッサと Intel® Xeon® Platinum 8168 (Skylake) プロセッサ上で実行されます。 これは、持続する 3.4 GHz の全コア ターボ クロック速度と 3.7 GHz の最大シングルコア ターボ周波数を備えています。 Intel® AVX-512 命令は、Intel スケーラブル プロセッサでの新機能です。 これらの命令は、単精度浮動小数点演算と倍精度浮動小数点演算の両方でベクトル処理ワークロードに最大 2 倍のパフォーマンス向上を提供します。 つまり、これらは、あらゆるコンピューティング ワークロードで実際に高速です。

Fsv2 シリーズの VM は、Intel® ハイパースレッディング テクノロジを備えています。

[ACU](acu.md):195 - 210<br>
[Premium Storage](premium-storage-performance.md):サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU の数 | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps |  バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> |最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2<sup>4</sup>  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 4000/200 | 2| 5000   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 8000/200 | 2|10000  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 16000/400 | 4|12500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 32000/800 | 4|12500  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 64000/1600 | 8|16000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 80000/2000 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 80000/2000 | 8|28000 |
| Standard_F72s_v2<sup>2、3</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 80000/2000 | 8|30000 |

<sup>1</sup> Fsv2 シリーズの VM では、ディスクのパフォーマンスを[バースト](./disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。

<sup>2</sup> 64 個を超える vCPU を使用するには、次のサポートされているゲスト オペレーティング システムのいずれかが必要です。

- Windows Server 2016 以降
- Azure 用にチューニングされたカーネル (4.15 カーネル以降) を含む Ubuntu 16.04 LTS 以降
- SLES 12 SP2 以降
- Microsoft が提供する LIS パッケージ 4.3.1 (以降) がインストールされた RHEL または CentOS バージョン 6.7 から 6.10
- Microsoft が提供する LIS パッケージ 4.2.1 (以降) がインストールされた RHEL または CentOS バージョン 7.3
- RHEL または CentOS バージョン 7.6 以降
- UEK4 以降を含む Oracle Linux
- バックポート カーネルを含む Debian 9、Debian 10 以降
- 4\.14 カーネル以降を含む CoreOS

<sup>3</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。<br>
<sup>4</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報: [ディスクの種類](./disks-types.md#ultra-disks)


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
