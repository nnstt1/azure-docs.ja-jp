---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 7b1ac1f3b2fcb8c999276fecec495cf496108c12
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134924"
---
### <a name="on-demand-bursting"></a>オンデマンド バースト

ディスク バーストのオンデマンド バースト モデルを使用している Premium SSD なら、プロビジョニングされた元のターゲットを超えてバーストできます。最大バースト ターゲットを上限に、ワークロードに応じた必要な回数だけ、バースト可能です。 たとえば、1-TiB P30 ディスクでは、プロビジョニングされる IOPS は 5,000 IOPS です。 このディスクでディスク バーストが有効になっている場合、ワークロードで、最大バースト パフォーマンスである 30,000 IOPS および 1,000 Mbps まで、このディスクに IO を発行できます。 サポートされている各ディスクの最大バースト ターゲットについては、「[VM ディスクのスケーラビリティおよびパフォーマンスの目標](../articles/virtual-machines/disks-scalability-targets.md#premium-ssd-managed-disks-per-disk-limits)」を参照してください。

ワークロードで、プロビジョニングされたパフォーマンス ターゲットを超えて頻繁にディスク バーストが実行されると予想される場合、ディスク バーストはコスト効果が高くありません。 この場合は、より良いベースライン パフォーマンスのために、ディスクのパフォーマンス レベルを[より高いレベル](../articles/virtual-machines/disks-performance-tiers.md)に変更することをお勧めします。 課金の詳細を確認し、ワークロードのトラフィック パターンに照らして評価してください。

オンデマンドのバーストを有効にする前に、次の点を理解しておいてください。

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>課金

オンデマンド バースト モデルを使用している Premium SSD には、バーストの有効化の定額料金が時間単位で課され、プロビジョニングされたターゲットを超えるすべてのバースト トランザクションにトランザクション コストが適用されます。 トランザクション コストは、キャッシュされないディスク IO に基づいて、従量課金制モデルを使用して課金されます。これには、プロビジョニングされたターゲットを超える読み取りと書き込みの両方が含まれます。 以下は、請求時間中のディスク トラフィック パターンの例です。

ディスク構成: Premium SSD - 1 TiB (P30)、ディスク バーストが有効。

- 00:00:00 – 00:10:00 ディスク IOPS が、プロビジョニングされたターゲットである 5,000 IOPS を下回っています 
- 00:10:01 – 00:10:10 アプリケーションからバッチ ジョブが発行されたため、ディスク IOPS が 10 秒間 6,000 IOPS にバーストしました 
- 00:10:11 – 00:59:00 ディスク IOPS が、プロビジョニングされたターゲットである 5,000 IOPS を下回っています 
- 00:59:01 – 01:00:00 アプリケーションからバッチ ジョブがもう 1 件発行されたため、ディスク IOPS が 60 秒間 7,000 IOPS にバーストしました 

この課金時間では、バーストのコストは次の 2 つの料金で構成されています。

1 つ目の料金は、バーストの有効化の定額料金である $X です (お客様のリージョンによって決まります)。 この定額料金は、接続状態にかかわらず、無効になるまで常にディスクに対して課金されます。 

2 つ目は、バースト トランザクションのコストです。 ディスク バーストは、2 つの時間帯で発生しました。 00:10:01 – 00:10:10 では、累積バースト トランザクションは (6,000 – 5,000) X 10 = 10,000 です。 00:59:01 – 01:00:00 では、累積バースト トランザクションは (7,000 – 5,000) X 60 = 120,000 です。 バースト トランザクションの合計は、10,000 + 120,000 = 130,000 です。 バースト トランザクションのコストは、10,000 トランザクションの 13 ユニットに基づいて、(リージョン別の価格に基づいて) $Y として課金されます。

このようにして、この課金時間のディスク バーストに関する総コストは、$X + $Y になります。 同じ計算が、Mbps 単位でプロビジョニングされたターゲットを超えるバーストにも適用されます。 超過分の MB は、IO サイズが 256 KB のトランザクションに変換されます。 ディスク トラフィックが、プロビジョニングされた IOPS と Mbps の両方のターゲットを超える場合は、次の例を参考にバースト トランザクションを計算できます。 

ディスク構成: Premium SSD - 1 TB (P30)、ディスク バーストが有効。

- 00:00:01 – 00:00:05 アプリケーションがバッチ ジョブを発行したため、ディスク IOPS が 5 秒間 10,000 IOPS および 300 Mbps にバーストしました。
- 00:00:06 – 00:00:10 アプリケーションが回復ジョブを発行したため、ディスク IOPS が 5 秒間 6,000 IOPS および 600 Mbps にバーストしました。

バースト トランザクションは、IOPS または Mbps のバーストによるトランザクションの最大数と見なされます。 00:00:01 – 00:00:05 では、累積バースト トランザクションは max ((10,000 – 5,000), (300 - 200) * 1024 / 256)) * 5 = 25,000 トランザクションです。 00:00:06 – 00:00:10 では、累積バースト トランザクションは max ((6,000 – 5,000), (600 - 200) * 1024 / 256)) * 5 = 8,000 トランザクションです。 そのほかに、オンデマンド ベースのディスク バーストを有効にするための総コストを明らかにするために、バーストの有効化の定額料金を含めます。 

価格の詳細については、[Managed Disks の価格のページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。また、ワークロードの評価を行うために、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)を使用できます。 


オンデマンド バーストを有効にするには、「[オンデマンド バーストを有効にする](../articles/virtual-machines/disks-enable-bursting.md)」をご覧ください。

### <a name="credit-based-bursting"></a>クレジットベースのバースト

Premium SSD の場合、クレジットベースのバーストは、P20 以下のディスク サイズで使用できます。 Standard SSD の場合、クレジットベースのバーストは、E30 以下のディスク サイズで使用できます。 Standard と Premium の両方の SSD について、クレジットベースのバーストは、Azure Public、Government、China クラウドのすべてのリージョンで利用できます。 既定では、ディスク バーストは、サポートされているディスク サイズの新しいデプロイと既存のデプロイすべてで有効になっています。 VM レベルのバーストでは、クレジットベースのバーストだけが使用されます。

## <a name="virtual-machine-level-bursting"></a>仮想マシンレベルのバースト

VM レベルのバーストでは、バーストのクレジットベース モデルのみが使用され、これをサポートするすべての VM で既定で有効になっています。

サポートされている次のサイズでは、Azure パブリック クラウド内のすべてのリージョンで、VM レベルでのバーストが有効になっています。 
- [Dsv4 シリーズ](../articles/virtual-machines/dv4-dsv4-series.md)
- [Dasv4 シリーズ](../articles/virtual-machines/dav4-dasv4-series.md)
- [Ddsv4 シリーズ](../articles/virtual-machines/ddv4-ddsv4-series.md)
- [Dasv5 シリーズ](../articles/virtual-machines/dasv5-dadsv5-series.md)
- [Dadsv5 シリーズ](../articles/virtual-machines/dasv5-dadsv5-series.md)
- [Esv4 シリーズ](../articles/virtual-machines/ev4-esv4-series.md)
- [Easv4 シリーズ](../articles/virtual-machines/eav4-easv4-series.md)
- [Edsv4 シリーズ](../articles/virtual-machines/edv4-edsv4-series.md)
- [Easv5 シリーズ](../articles/virtual-machines/easv5-eadsv5-series.md)
- [Eadsv5 シリーズ](../articles/virtual-machines/easv5-eadsv5-series.md)
- [B シリーズ](../articles/virtual-machines/sizes-b-series-burstable.md)
- [Fsv2 シリーズ](../articles/virtual-machines/fsv2-series.md)
- [Dsv3 シリーズ](../articles/virtual-machines/dv3-dsv3-series.md)
- [Esv3 シリーズ](../articles/virtual-machines/ev3-esv3-series.md)
- [Lsv2 シリーズ](../articles/virtual-machines/lsv2-series.md)

## <a name="bursting-flow"></a>バースティングのフロー

バーストのクレジット システムは、VM レベルとディスク レベルの両方で同様に適用されます。 リソース (VM またはディスク) は、それ自体のバースト バケット内にクレジットがフルにストックされた状態で開始されます。 これらのクレジットを使用すると、最大バースト率で最長 30 分のバーストが可能です。 リソースで使用されている IOPS または MB/秒がそのリソースのパフォーマンス ターゲットを下回っている場合は、常にクレジットが蓄積されます。 リソースでバースト クレジットが蓄積されている場合に、ワークロードで追加のパフォーマンスが必要になったら、リソースはそれらのクレジットを使用し、パフォーマンスの制限を超えて、ワークロードの需要を満たすためにパフォーマンスを高めることができます。

![バースト バケットの図。](media/managed-disks-bursting/bucket-diagram.jpg)

利用可能なクレジットの使い方は任意です。 30 分のバースト クレジットを連続で使用することも、1 日を通して散発的に使用することもできます。 リソースがデプロイされると、クレジットがフルに割り当てられます。 これらを使い切ると、補充には 1 日未満の時間がかかります。 クレジットは自由に使用できます。リソースをバーストするために、バースト バケットをフルにする必要はありません。 バーストの蓄積はリソースごとに異なったものとなります。これは、パフォーマンス ターゲットを下回る未使用の IOPS と MB/秒に基づくためです。 ベースライン パフォーマンスが高いリソースの方が、ベースライン パフォーマンスが低いリソースよりも早くバースト クレジットを蓄積できます。 たとえば、アイドル状態の P1 ディスクでは、1 秒に 120 IOPS が蓄積されます。一方、アイドル状態の P20 ディスクでは、1 秒に 2,300 IOPS が蓄積されます。

## <a name="bursting-states"></a>バースティングの状態
バースティングが有効になっているリソースには、3 つの状態があります。
- **蓄積中** - リソースの IO トラフィックの使用量がパフォーマンス ターゲットを下回っています。 IOPS と MB/秒のバースティング クレジットの累積は、別々に行われます。 リソースでは、IOPS クレジットを蓄積しながら MB/秒クレジットを消費したり、またはその逆を行ったりできます。
- **バースティング中** - リソースのトラフィックの使用量がパフォーマンス ターゲットを上回っています。 バースト トラフィックは、IOPS または帯域幅から別々にクレジットを消費します。
- **一定** - リソースのトラフィックがパフォーマンス ターゲットと正確に一致しています。

## <a name="bursting-examples"></a>バーストの例

次の例では、さまざまな VM とディスクの組み合わせでバーストがどのように機能するかを示しています。 例を理解しやすいように、MB/秒に焦点を当てていますが、同じロジックが IOPS に別途適用されます。

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>バースト可能なディスクを備えたバースト不可能な仮想マシン
**VM とディスクの組み合わせ:** 
- Standard_D8as_v4 
    - キャッシュなしの MB/秒:192
- P4 OS ディスク
    - プロビジョニングされる MB/秒:25
    - 最大バースト MB/秒:170 
- 2 つの P10 データ ディスク 
    - プロビジョニングされる MB/秒:100
    - 最大バースト MB/秒:170

 VM が起動すると、OS ディスクからデータが取得されます。 OS ディスクは起動されている VM の一部であるため、この OS ディスクのバースト クレジットはいっぱいになります。 これらのクレジットを使用して、170 MB/秒で OS ディスクの起動をバーストできます。

![VM は 192 MB のスループットの要求を OS ディスクに送信し、OS ディスクは 170 MB/秒のデータで応答します。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

起動が完了すると、VM 上でアプリケーションが実行され、重要度の低いワークロードが発生します。 このワークロードでは、すべてのディスクに均等に分散される 15 MB/秒が必要です。

![アプリケーションは 15 MB/秒のスループットの要求を VM に送信し、VM は要求を受け取り、各ディスクに 5 MB/秒の要求を送信します。各ディスクは 5 MB/秒を返し、VM は 15 MB/秒をアプリケーションに返します。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

その後、アプリケーションは 192 MB/秒を必要とするバッチ ジョブを処理する必要があります。 2 MB/秒が OS ディスクによって使用され、残りはデータ ディスク間で均等に分割されます。

![アプリケーションは 192 MB/秒のスループットの要求を VM に送信し、VM は要求を受け取り、要求を一括でデータ ディスクに送信し (それぞれ 95 MB/秒)、2 MB/秒を OS ディスクに送信します。データ ディスクは需要を満たすためにバーストし、すべてのディスクが要求されたスループットを VM に返し、そこからアプリケーションに返されます。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>バースト不可能なディスクを備えたバースト可能な仮想マシン
**VM とディスクの組み合わせ:** 
- Standard_L8s_v2 
    - キャッシュなしの MB/秒:160
    - 最大バースト MB/秒:1,280
- P50 OS ディスク
    - プロビジョニングされる MB/秒:250 
- 2 台の P50 データ ディスク 
    - プロビジョニングされる MB/秒:250

 最初の起動の後、VM 上でアプリケーションが実行され、重要度の低いワークロードが発生します。 このワークロードでは、すべてのディスクに均等に分散される 30 MB/秒が必要です。
![アプリケーションは 30 MB/秒のスループットの要求を VM に送信し、VM は要求を受け取り、各ディスクに 10 MB/秒の要求を送信します。各ディスクは 10 MB/秒を返し、VM は 30 MB/秒をアプリケーションに返します。](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

その後、アプリケーションは 600 MB/秒を必要とするバッチ ジョブを処理する必要があります。 この需要を満たすために Standard_L8s_v2 によってバーストが行われ、そのディスクへの要求が P50 ディスクに均等に分散されます。

![アプリケーションは 600 MB/秒のスループットの要求を VM に送信し、VM はバーストを受けて要求を受け取り、各ディスクに 200 MB/秒の要求を送信します。各ディスクは 200 MB/秒を返し、VM はバーストを実行して 600 MB/秒をアプリケーションに返します。](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>バースト可能なディスクを備えたバースト可能な仮想マシン
**VM とディスクの組み合わせ:** 
- Standard_L8s_v2 
    - キャッシュなしの MB/秒:160
    - 最大バースト MB/秒:1,280
- P4 OS ディスク
    - プロビジョニングされる MB/秒:25
    - 最大バースト MB/秒:170 
- 2 つの P4 データ ディスク 
    - プロビジョニングされる MB/秒:25
    - 最大バースト MB/秒:170 

VM が起動すると、バーストが実行されて OS ディスクに対して 1,280 MB/秒のバースト制限が要求され、OS ディスクは 170 MB/秒のバースト パフォーマンスで応答します。

![VM は起動時にバーストを実行して 1280 MB/秒の要求を OS ディスクに送信し、OS ディスクはバーストを実行して 1280 MB/秒を返します。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

起動後に、重要度の低いワークロードを持つアプリケーションを起動してください。 このアプリケーションでは、すべてのディスクに均等に分散される 15 MB/秒が必要です。

![アプリケーションは 15 MB/s のスループットの要求を VM に送信し、VM は要求を受け取り、各ディスクに 5 MB/s の要求を送信します。各ディスクは 5 MB/s の応答を返し、VM は 15 MB/s をアプリケーションに返します。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

その後、アプリケーションは 360 MB/秒を必要とするバッチ ジョブを処理する必要があります。 この需要を満たすために Standard_L8s_v2 によってバーストが行われて、要求が行われます。 OS ディスクに必要とされるのは 20 MB/秒のみです。 残りの 340 MB/秒は、バーストする P4 データ ディスクによって処理されます。

![アプリケーションは 360 MB/秒のスループットの要求を VM に送信し、VM はバーストを受けて要求を取得し、各データ ディスクに 170 MB/秒と 20 MB/秒の要求を OS ディスクから送信します。各ディスクは、要求された MB/秒を返し、VM はバーストを実行して 360 MB/秒をアプリケーションに返します。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)