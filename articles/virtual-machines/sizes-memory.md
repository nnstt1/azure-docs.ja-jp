---
title: Azure VM のサイズ - メモリ | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまなメモリ最適化済みのサイズを一覧表示します。 このシリーズの各サイズにおけるストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines
documentationcenter: ''
author: brbell
tags: azure-resource-manager,azure-service-management
keywords: VM の分離,分離された VM,分離,分離された
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/20/2021
ms.author: brbell
ms.openlocfilehash: 50a187435b1a47fe2559ce5ce2d36905570b8419
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456596"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>メモリ最適化済み仮想マシンのサイズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

> [!TIP]
> **[仮想マシン セレクター ツール](https://aka.ms/vm-selector)** を使って、ワークロードに最適な他のサイズを見つけてください。

メモリ最適化済み VM のサイズは、リレーショナル データベース サーバー、中規模から大規模のキャッシュ、インメモリ分析に適した、メモリと CPU の高い比率を提供します。 この記事では、このグループ内の各サイズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。

- オリジナルの D シリーズに続く [Dv2 と DSv2 シリーズ](dv2-dsv2-series-memory.md)には、より強力な CPU が備わっています。 Dv2 シリーズは D シリーズよりも、およそ 35% 高速です。 これは、Intel&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) または Intel&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) または Intel&reg; Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行され、Intel Turbo Boost Technology 2.0 を備えています。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

    Dv2 と DSv2 シリーズは、より高速の vCPU やより優れた一時ストレージ パフォーマンスが要求される、または高いメモリ要求を持つアプリケーションに最適です。 多数のエンタープライズ レベルのアプリケーションに、強力な組み合わせで対処します。

- [Eav4 と Easv4 シリーズ](eav4-easv4-series.md)では、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを利用しており、ほとんどのメモリ最適化されたワークロードを実行するためのオプションが増えています。 Eav4 シリーズと Easv4 シリーズは、Ev3 および Esv3 シリーズと同じメモリおよびディスク構成を備えています。

- [Ev3 と Esv3 シリーズ](ev3-esv3-series.md) は、ハイパースレッド構成の Intel&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) または Intel&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) プロセッサを備えることで、大半の汎用ワークロード向けに付加価値を高め、他の多くのクラウドの汎用 VM と一線化されています。 メモリが増設 (約 7 GiB/vCPU から 8 GiB/vCPU) されている一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Ev3 は、D/Dv2 ファミリーのハイ メモリ VM サイズのフォローアップです。

- [Ev4 および Esv4 シリーズ](ev4-esv4-series.md) は、ハイパースレッド構成の第 2 世代 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサで実行されます。これは、メモリを集中的に使用するさまざまなエンタープライズ アプリケーションに最適であり、最大 504 GiB の RAM を搭載します。 [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、および [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 Ev4 と Esv4 シリーズにはローカル一時ディスクは含まれていません。 詳細については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.yml)に関するページを参照してください。

- [Edv4 および Edsv4 シリーズ](edv4-edsv4-series.md)は、第 2 世代の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、高い vCPU 数と大量のメモリを活かせる非常に大規模なデータベースやその他のアプリケーションに最適です。 さらに、これらの VM サイズには、低待機時間で高速なローカル ストレージを利用するアプリケーション向けの高速で大容量のローカル SSD ストレージが含まれています。 3\.4 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。

- [Easv5 シリーズと Eadsv5 シリーズ](easv5-eadsv5-series.md)は、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で利用し、ほとんどのメモリ最適化されたワークロードを実行するための顧客オプションを増やします。 これらの仮想マシンでは、リレーショナル データベース サーバーやインメモリ分析ワークロードなど、ほとんどのメモリ集中型のエンタープライズ アプリケーションに関連する要件を満たす vCPU とメモリの組み合わせが提供されます。 

- [Edv5 シリーズと Edsv5 シリーズ](edv5-edsv5-series.md)はハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Ice Lake) プロセッサ上で実行され、メモリを集中的に使用するエンタープライズ アプリケーションに最適です。また、最大 512 GiB の RAM、[Intel&reg; ターボ ブースト テクノロジ 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; ハイパースレッド テクノロジ](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel&reg; アドバンスト ベクトル エクステンション 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) を備えています。 また、[Intel&reg; ディープ ラーニング ブースト](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)をサポートしています。 これらの新しい VM サイズには 50% 大きいローカル ストレージがある他、[Gen2 VM](./generation-2.md) の [Ev3/Esv3](./ev3-esv3-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上しています。 これは、3.4 GHz の全コア ターボ クロック速度を特徴としています。

- [Ev5 シリーズと Esv5 シリーズ](ev5-esv5-series.md)は、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Ice Lake) プロセッサで実行されます。これは、メモリを集中的に使用するさまざまなエンタープライズ アプリケーションに最適であり、最大 512 GiB の RAM を搭載します。 これは、3.4 GHz の全コア ターボ クロック速度を特徴としています。

- [M シリーズ](m-series.md)は、多数の vCPU (最大 128 個の vCPU) と大量のメモリ (最大 3.8 TiB) を提供します。 このシリーズも非常に大規模なデータベースや他のアプリケーションに最適であり、多数の vCPU と大量のメモリによるメリットを活用できます。

- [Mv2 シリーズ](mv2-series.md)は、クラウドの VM で最大の vCPU 数 (最大 416 個の vCPU) と最大のメモリ (最大 11.4 TiB) を提供します。 非常に大規模なデータベースや他のアプリケーションに最適であり、多数の vCPU と大量のメモリによるメリットを活用することができます。

Azure Compute では、特定のハードウェアの種類に分離される、単一顧客専用の仮想マシン サイズを提供します。 これらの仮想マシン サイズは、コンプライアンスや規制上の要件などの要素に関連するワークロードについて、他の顧客からの高いレベルの分離を必要とするワークロードに最適です。 お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの分離された仮想マシンのリソースをさらに分割することもできます。 分離された VM オプションについては、下の仮想マシン ファミリーのページをご覧ください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

Azure で、その VM の名前付けが行われる方法の詳細については、「[Azure 仮想マシンのサイズの名前付け規則](./vm-naming-conventions.md)」を参照してください。
