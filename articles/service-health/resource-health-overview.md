---
title: Azure Resource Health の概要
description: Azure Resource Health が、Azure のリソースに影響を及ぼしているサービスの問題を診断したり、サポートを受けたりするときにどのように役立つかについて説明します。
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 532c7001b12efb2917c906f7607fef33bac194ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728581"
---
# <a name="resource-health-overview"></a>Resource Health の概要
 
Azure Resource Health は、Azure のリソースに影響を及ぼしているサービスの問題を診断したり、サポートを受けたりするために役立ちます。 リソースの現在と過去の正常性について報告します。

[Azure の状態](https://status.azure.com)レポートは、さまざまな Azure ユーザーに影響するサービスの問題について報告します。 Resource Health は、リソースの正常性に関するパーソナライズされたダッシュボードを提供します。 Resource Health は、Azure サービスの問題によってリソースが利用できなかったすべての時間を示します。 このデータにより、SLA 違反が発生したかどうかを簡単に確認できます。

## <a name="resource-definition-and-health-assessment"></a>リソース定義と正常性評価

*リソース* は、仮想マシン、Web アプリ、SQL Database など、Azure サービスの特定のインスタンスです。 Resource Health は、さまざまな Azure サービスからの信号を基に、リソースが正常であるかどうかを評価します。 リソースが正常でない場合、Resource Health は追加の情報を分析して問題の原因を特定します。 また、問題解決のために Microsoft が実施しているアクションについても報告し、問題に対処するためにユーザーができることを特定します。

正常性の評価方法の詳細については、[Azure Resource Health](resource-health-checks-resource-types.md) に関する記事でリソースの種類と正常性チェックの一覧を参照してください。

## <a name="health-status"></a>正常性状態

リソースの正常性は、次のいずれかの状態として表示されます。

### <a name="available"></a>利用可能

*使用可能* は、リソースの正常性に影響を与えるイベントが検出されていないことを意味します。 リソースが過去 24 時間以内に予定外のダウンタイムから回復した場合は、"最近解決された" ことを示す通知が表示されます。

![*使用可能* 状態の仮想マシンについて、"最近解決された" 問題が通知されている画面](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>使用不可

*使用不可* は、サービスにおいて、リソースの正常性に影響を与える継続中のイベント (プラットフォームまたはプラットフォーム以外のイベント) が検出されたことを意味します。

#### <a name="platform-events"></a>プラットフォームのイベント

プラットフォームのイベントは、Azure インフラストラクチャの複数のコンポーネントによってトリガーされます。 これには、スケジュールされたアクション (計画的なメンテナンスなど) と、予期しないインシデント (計画されていないホストの再起動や、指定の期間後に障害が発生すると予測される機能が低下したホスト ハードウェアなど) の両方が含まれます。

Resource Health は、イベントと復旧プロセスについてさらに詳細な情報を提供します。 また、ユーザーは、有効なサポート契約がない場合でも、Microsoft サポートに問い合わせることができます。

![プラットフォームのイベントが理由で *使用不可* 状態となっている仮想マシン](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>プラットフォーム以外のイベント

プラットフォーム以外のイベントは、ユーザー アクションによってトリガーされます。 たとえば、仮想マシンを停止した場合や、Azure Cache for Redis への接続数が最大数に到達した場合などです。

![プラットフォーム以外のイベントが理由で "使用不可" 状態となっている仮想マシン](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

*不明* は、Resource Health がリソースに関する情報を 10 分以上受け取っていないことを意味します。 これは一般的に、仮想マシンの割り当てが解除されている場合に発生します。 この状態はリソースの状態を明確に示すものではありませんが、トラブルシューティングにとって重要なデータ ポイントである可能性がありまあす。

リソースが予期した通りに実行されている場合、リソースの状態は数分後に *使用可能* に変更されます。

リソースで問題が発生している場合、*不明* の正常性状態は、プラットフォーム内のイベントによってリソースが影響を受けていることを意味している可能性があります。

![*不明* 状態の仮想マシン](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>低下しています

*低下* は、リソースでパフォーマンス低下が検出されたものの、まだ使用可能であることを意味します。

低下と報告されるタイミングについては、リソースごとに独自の基準があります。

![*低下* 状態の仮想マシン](./media/resource-health-overview/degraded.png)

## <a name="history-information"></a>履歴情報

Resource Health の **[正常性の履歴]** セクションで、最大 30 日間の履歴にアクセスできます。

![過去 2 週間の Resource Health イベントの一覧](./media/resource-health-overview/history-blade.png)

## <a name="root-cause-information"></a>根本原因に関する情報

プラットフォームによって開始された使用不可の根本原因に関する詳細情報が Azure にある場合、その情報は、最初に使用不可になってから最大 72 時間は Resource Health に表示されている可能性があります。 この情報は、現在、仮想マシンについてのみ利用できます。 

## <a name="get-started"></a>はじめに

1 つのリソースについて Resource Health を開くには

1. Azure portal にサインインします。
2. リソースを参照します。
3. 左側のウィンドウのリソース メニューで、 **[リソース正常性]** を選択します。

![リソース ビューから Resource Health を開く様子](./media/resource-health-overview/from-resource-blade.png)

Resource Health には、 **[すべてのサービス]** を選択し、フィルター テキスト ボックスに「**resource health**」と入力してアクセスすることもできます。 **[ヘルプとサポート]** ウィンドウで、[[リソース正常性]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth) を選択します。

![[すべてのサービス] から Resource Health を開く様子](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>次のステップ

Resource Health について詳しくは、次のリファレンスをご覧ください。
-  [Azure Resource Health で利用できるリソースの種類と正常性チェック](resource-health-checks-resource-types.md)
-  [Azure Resource Health に関してよく寄せられる質問](resource-health-faq.yml)
