---
title: 概念 - Azure Kubernetes Service (AKS) での持続可能なソフトウェア エンジニアリング
description: Azure Kubernetes Service (AKS) での持続可能なソフトウェア エンジニアリングについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 3fb40b278a4107cb1cdba2230602b99e2bdfff85
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355487"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での持続可能なソフトウェア エンジニアリングの原則

持続可能なソフトウェア エンジニアリングの原則は、持続可能なアプリケーションの定義、構築、実行に役立つ一連の能力です。 全体的な目標は、アプリケーションのすべての側面の炭素排出量を削減することです。 「[持続可能なソフトウェア エンジニアリングの原則][principles-sse]」に、持続可能なソフトウェア エンジニアリングの原則の概要があります。

持続可能なソフトウェア エンジニアリングは、優先順位と焦点についての 1 つの転換です。 ほとんどのソフトウェアの設計および実行の方法では、多くの場合、高速パフォーマンスと、低待機時間に強調が置かれます。 一方、持続可能なソフトウェア エンジニアリングでは、炭素排出量をできるだけ多く削減することに焦点が当てられます。 以下を検討してください。

* ネットワークの移動全体を減らすなど、持続可能なソフトウェア エンジニアリングの原則を適用することで、パフォーマンスの高速化や待機時間の短縮を実現できます。 
* 炭素排出量を削減すると、パフォーマンスの低速化や、優先度の低いワークロードの遅れなどの、待機時間の増加を招く場合もあります。 

持続可能なソフトウェア エンジニアリングの原則をアプリケーションに適用する前に、アプリケーションの優先順位、ニーズ、トレードオフを確認してください。

## <a name="measure-and-optimize"></a>測定と最適化

AKS クラスターの炭素排出量を削減するには、クラスターのリソースがどのように使用されているかを理解する必要があります。 [Azure Monitor][azure-monitor] では、メモリと CPU の使用率など、クラスターのリソースの使用状況に関する詳細が提供されます。 このデータによって、クラスターの炭素排出量を削減するという決意が告知され、変更の効果が観察されます。 

[Microsoft Sustainability Calculator][sustainability-calculator] をインストールして、すべての Azure リソースの炭素排出量を確認することもできます。

## <a name="increase-resource-utilization"></a>リソース使用率の向上

炭素排出量を削減する方法の 1 つは、アイドル時間を減らすことです。 アイドル時間を減らすには、コンピューティング リソースの使用率を上げる必要があります。 次に例を示します。
1. クラスターに 4 つのノードがあり、それぞれが 50% の容量で実行されています。 そのため、4 つのノードすべてで 50% の未使用容量がアイドル状態のままになります。 
1. クラスターを 3 つのノードに減らし、それぞれが同じワークロードで 67% の容量で実行されるようにしました。 各ノードで未使用の容量を 33% に減らし、使用率を上げることに成功しました。

> [!IMPORTANT]
> クラスター内のリソースの変更を検討するときは、[システム プール][system-pools]に、クラスターのコア システム コンポーネントの安定性を維持するための十分なリソースがあることを確認してください。 **決して**、クラスターが不安定になる可能性がある水準にまでクラスターのリソースを削減しないでください。

クラスターの使用率を確認した後、[複数のノード プール][multiple-node-pools]によって提供される、次のような機能の使用を検討してください。 

* ノードのサイズ設定

    [ノードのサイズ設定][node-sizing]を使用して、特定の CPU およびメモリ プロファイルを持つノード プールを定義し、ワークロードのニーズに合わせてノードを調整することができます。 ワークロードのニーズに合わせてノードをサイズ設定すると、より高い使用率で少数のノードを実行できます。 

* クラスター スケーリング

    クラスターの[スケーリング][scale]方法を構成します。 [水平ポッド オートスケーラー][scale-horizontal]と[クラスター オートスケーラー][scale-auto]を使用して、構成に基づきクラスターを自動的にスケーリングします。 クラスターのスケーリング方法を制御して、クラスターのワークロードの変化との同期を保ちながら、すべてのノードを高い使用率で実行し続けます。 

* スポット プール

    突然の中断または終了に耐性があるワークロードの場合、[スポット プール][spot-pools]を使用できます。 スポット プールは、Azure 内部のアイドル容量を利用します。 たとえば、バッチ ジョブまたは開発環境には、スポット プールがうまく機能する場合があります。

> [!NOTE]
>使用率を上げることで余分なノードを減らすこともでき、これによって、[各ノードのリソース予約][resource-reservations]によって消費されるエネルギーを削減できます。

最後に、アプリケーションの Kubernetes マニフェストで、CPU とメモリの *要求* と *制限* の見直しを行います。 
* メモリおよび CPU 値を下げると、より多くのメモリと CPU を、他のワークロードを実行するためにクラスターで使用できるようになります。 
* より少ない CPU とメモリでより多くのワークロードを実行すれば、クラスターの割り当て密度が高まり、使用率が向上します。 

アプリケーション用の CPU とメモリを減らすときに、CPU とメモリの値を低く設定しすぎると、アプリケーションの動作が劣化したり、不安定になったりする可能性があります。 CPU とメモリの *要求* と *制限* を変更する前に、いくつかのベンチマーク テストを実行して、値が適切に設定されているかどうかを確認してください。 これらの値を、アプリケーションが不安定になる水準にまで減らさないでください。

## <a name="reduce-network-travel"></a>ネットワーク移動の削減

クラスターとの間の要求と応答の移動距離を減らすことにより、ネットワーク デバイスによるカーボン排出量と消費電力を削減できます。 ネットワーク トラフィックを見直した後は、ネットワーク トラフィックの発信元により近い[リージョン][regions]にクラスターを作成することを検討してください。 [Azure Traffic Manager][azure-traffic-manager] を使用して、最も近いクラスターや[近接配置グループ][proiximity-placement-groups]にトラフィックをルーティングし、Azure リソース間の距離を短縮できます。

> [!IMPORTANT]
> クラスターのネットワークに変更を加えることを検討するときは、ワークロードの要件を満たすことを犠牲にしてまでネットワークの移動を削減してはなりません。 たとえば、[可用性ゾーン][availability-zones]を使用するとクラスターでネットワーク移動が増えますが、他方では、ワークロード要件を満たすために可用性ゾーンが必要な場合があります。

## <a name="demand-shaping"></a>需要形成

可能であれば、クラスターのリソースの需要を、余剰の容量を使用できる時間またはリージョンにシフトすることを検討してください。 例として、次を検討します。
* バッチ ジョブの実行時間またはリージョンを変更する。
* [スポット プール][spot-pools]を使用する。 
* すぐに処理する必要がないワークロードの実行を先送りするために、キューを使用するようにアプリケーションをリファクタリングする。

## <a name="next-steps"></a>次のステップ

この記事で言及した AKS の機能については、以下を参照してください。

* [複数のノード プール][multiple-node-pools]
* [ノードのサイズ設定][node-sizing]
* [クラスターのスケーリング][scale]
* [水平ポッド自動スケーラー][scale-horizontal]
* [クラスター オートスケーラー][scale-auto]
* [スポット プール][spot-pools]
* [システム プール][system-pools]
* [リソース予約][resource-reservations]
* [近接配置グループ][proiximity-placement-groups]
* [可用性ゾーン][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: /learn/modules/sustainable-software-engineering-overview/
