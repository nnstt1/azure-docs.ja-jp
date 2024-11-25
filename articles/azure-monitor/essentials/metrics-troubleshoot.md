---
title: Azure Monitor のメトリック グラフのトラブルシューティング
description: メトリック グラフの作成、カスタマイズ、または解釈に関する問題のトラブルシューティングを行います。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.openlocfilehash: f8ff057f818999a9d170fe6f58101c99cd931f7e
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809687"
---
# <a name="troubleshooting-metrics-charts"></a>メトリック グラフのトラブルシューティング

Azure メトリックス エクスプローラーでのグラフの作成、カスタマイズ、解釈で問題が発生する場合は、この記事を使用します。 メトリック初心者の場合は、[メトリックス エクスプローラーの概要](metrics-getting-started.md)および[メトリックス エクスプローラーの高度な機能](../essentials/metrics-charts.md)について学習してください。 構成されたメトリック グラフの[例](../essentials/metric-chart-samples.md)を見ることもできます。

## <a name="chart-shows-no-data"></a>グラフにデータが表示されない

正しいリソースとメトリックを選択した後、グラフにデータが表示されない場合があります。 この動作は、次に理由で発生する可能性があります。

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Microsoft.Insights リソース プロバイダーがサブスクリプションに登録されていない

メトリックを調べるには、*Microsoft.Insights* リソース プロバイダーがサブスクリプションに登録されている必要があります。 多くの場合は、自動的に登録されます (つまり、警告ルールの構成、リソースの診断設定のカスタマイズ、または自動スケーリング ルールの構成を行った後)。 Microsoft.Insights リソース プロバイダーが登録されていない場合は、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」で説明されている手順に従って、手動で登録する必要があります。

**解決方法:** **[サブスクリプション]** 、 **[リソース プロバイダー]** タブの順に開き、サブスクリプションに *Microsoft.Insights* が登録されていることを確認します。

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>リソースに対する十分なアクセス権がない

Azure では、メトリックへのアクセスは、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) によって制御されます。 リソースのメトリックを調べるには、[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader)、[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)、または[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)のメンバーである必要があります。

**解決方法:** メトリックを調べるリソースに対して十分なアクセス許可があることを確認します。

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>選択した時間範囲の間にリソースがメトリックを生成しなかった

リソースの中には、メトリックを常時生成していないものもあります。 たとえば、Azure では、停止されている仮想マシンのメトリックは収集されません。 他には、いくつかの条件が発生した場合にのみ、メトリックを生成するリソースもあります。 たとえば、トランザクションの処理時間を示すメトリックには、少なくとも 1 つのトランザクションが必要です。 選択した時間範囲内にトランザクションがなかった場合、グラフは当然空になります。 さらに、ほとんどの Azure メトリックは 1 分ごとに収集されますが、収集頻度がもっと低いメトリックもいくつかあります。 調べようとしているメトリックの詳細については、メトリックのドキュメントを参照してください。

**解決方法:** グラフの時間をより広い範囲に変更します。 "過去 30 日間" から始めて、時間の細分性を大きくします (または、"自動時間細分性" オプションに任せます)。

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 日より長い時間範囲を選択した

[Azure のほとんどのメトリックは 93 日間保存されます](../essentials/data-platform-metrics.md#retention-of-metrics)。 ただし、1 つのグラフでクエリできるデータは 30 日までです。 この制限は、[ログ ベースのメトリック](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)には適用されません。

**解決方法:** 空のグラフが表示される場合、またはグラフにメトリック データの一部のみが表示される場合は、日時指定の開始日と終了日の間隔が、30 日を超えていないことを確認します。 30 日間の間隔を選択したら、グラフを[パン](metrics-charts.md#pan)して完全なリテンション期間を表示できます。

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>すべてのメトリック値がロックされた Y 軸の範囲外だった

[グラフの Y 軸の境界をロックする](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis)ことにより、意図せずグラフの表示領域にグラフの線が表示されなくなっている可能性があります。 たとえば、Y 軸を 0% から 50% の範囲にロックした場合、メトリックの値が一定して 100% であると、線は常に表示領域の外側にレンダリングされ、グラフは空白に見えるようになります。

**解決方法:** グラフの Y 軸の境界が、メトリックの値の範囲外でロックされていないことを確認します。 Y 軸の境界がロックされている場合、一時的にそれをリセットして、メトリック値がグラフの範囲外になっていないことを確認できます。 **sum**、**min**、**max** の集計のグラフの場合、自動細分性では Y 軸範囲をロックしないことをお勧めします。ブラウザー ウィンドウのサイズを変更したり、画面の解像度を切り替えたりすると、値の細分性が変化するためです。 細分性が切り替わると、グラフの表示領域が空のままになる可能性があります。

### <a name="you-are-looking-at-a-guest-classic-metric-but-didnt-enable-azure-diagnostic-extension"></a>ゲスト (クラシック) のメトリックを見ているが、Azure Diagnostics 拡張機能が有効になっていなかった

**ゲスト (クラシック)** のメトリックを収集するには、Azure Diagnostics 拡張機能を構成するか、またはご自分のリソースの **[診断設定]** パネルを使って有効にする必要があります。

**解決方法:** Azure Diagnostics 拡張機能を有効にしてもメトリックが表示されない場合は、[Azure Diagnostics 拡張機能のトラブルシューティング ガイド](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)の手順をご覧ください。 [ゲスト (クラシック) の名前空間とメトリックを選択できない](#cannot-pick-guest-namespace-and-metrics)場合のトラブルシューティング手順も参照してください

## <a name="error-retrieving-data-message-on-dashboard"></a>ダッシュボードに "データの取得中にエラーが発生しました" というメッセージが表示される

この問題は、ダッシュボードを作成するときに使ったメトリックが後で非推奨になって Azure から削除された場合に、発生することがあります。 それに該当するかどうかを確認するには、リソースの **[メトリック]** タブを開き、メトリック ピッカーでメトリックを使用できるかどうかを調べます。 メトリックが表示されない場合、メトリックは Azure から削除されています。 通常、メトリックが非推奨になったときは、リソースの正常性について似た観点を提供する、さらに新しくて良いメトリックがあります。

**解決方法:** ダッシュボードでグラフに対して別のメトリックを選択し、障害が発生したタイルを更新します。 [Azure サービスで使用可能なメトリックのリストを確認する](./metrics-supported.md)ことができます。

## <a name="chart-shows-dashed-line"></a>グラフに破線が表示される

Azure のメトリック グラフでは、2 つの既知の時間グレイン データ ポイントの間に欠損値 ("null 値" とも呼ばれます) があることを示すために、破線スタイルが使われます。 たとえば、時間セレクターで "1 分" の時間細分性を選択したのに、メトリックが報告されたのが 07:26、07:27、07:29、07:30 (2 番目と 3 番目のデータ ポイントの間の分のギャップに注意してください) であった場合、07:27 と 07:29 の間は破線で接続され、他のすべてのデータ ポイントは実線で接続されます。 メトリックで **count** と **sum** の集計が使われている場合、破線は 0 まで下がります。 **avg**、**min**、または **max** の集計の場合は、破線は最も近い 2 つの既知のデータ ポイントを接続します。 また、グラフの右端または左端のデータが不足している場合は、破線は不足しているデータ ポイントの方向に延長されます。
  ![グラフの右端または左端のデータが不足している場合に、不足しているデータ ポイントの方向に破線が延長されるようすを示すスクリーンショット。](./media/metrics-troubleshoot/dashed-line.png)

**解決方法:** この動作は仕様です。 欠落しているデータ ポイントを識別するのに便利です。 折れ線グラフは高密度のメトリックの傾向を視覚化するには優れた選択ですが、値がまばらなメトリックを解釈するのは困難な場合があります (特に、値と時間グレインの関連付けが重要な場合)。 破線によりこのようなグラフの読み取りは容易になりますが、グラフがまだ明確でない場合は、別のグラフの種類でメトリックを表示することを検討してください。 たとえば、同じメトリックに対する散布図グラフでは、値があるときにのみドットが表示され、値がないときはデータ ポイントがそっくりスキップされることにより、各時間グレインが明確に示されます。![[散布図] メニュー オプションが強調表示されているスクリーンショット。](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > それでも、メトリックに折れ線グラフを使う方がよい場合は、グラフに沿ってマウスを移動すると、マウス ポインターの位置にあるデータ ポイントが強調表示されるので、時間の細分性を評価するのに役立つ場合があります。

## <a name="chart-shows-unexpected-drop-in-values"></a>グラフの値に予期しない低下が示される

多くの場合、メトリックの値における認識される低下は、グラフに示されているデータの解釈の誤りです。 グラフに直近の分が表示されている場合、最後のメトリック データ ポイントが Azure によってまだ受信または処理されていないため、合計やカウントが低下して誤解を招く可能性があります。 サービスによっては、メトリックの処理の待ち時間が数分になる場合があります。 1 分または 5 分の細分性で最近の時間範囲を示すグラフの場合、過去数分間の値の低下がより顕著になります。![過去数分間の値の低下を示すスクリーンショット。](./media/metrics-troubleshoot/unexpected-dip.png)

**解決方法:** この動作は仕様です。 データが "*部分的*" または "*不完全*" であっても、受け取ったデータをすぐに表示することにはメリットがあると思われます。 このようにすると、重要な結論を早急に下し、すぐに調査を開始できます。 たとえば、エラーの数を示すメトリックの場合、部分的な値 X を見ると、特定の 1 分間に少なくとも X 回のエラーが発生したことがわかります。 その 1 分間に発生したエラーの正確な数が表示されるまで待つことなく、すぐに問題の調査を開始できます。正確な数は重要ではないことがあります。 完全なデータ セットを受信するとグラフは更新されますが、その時点では、さらに近い分の新しい不完全なデータ ポイントも表示される可能性があります。

## <a name="cannot-pick-guest-namespace-and-metrics"></a>ゲストの名前空間とメトリックを選択できない

仮想マシンと仮想マシン スケール セットには、メトリックの 2 つのカテゴリがあります。Azure ホスティング環境によって収集される **仮想マシン ホスト** メトリックと、仮想マシンで実行されている [監視エージェント](../agents/agents-overview.md)によって収集される **ゲスト (クラシック)** メトリックです。 監視エージェントは、[Azure Diagnostics 拡張機能](../agents/diagnostics-extension-overview.md)を有効にすることでインストールします。

既定では、ゲスト (クラシック) メトリックは Azure Storage アカウントに格納され、リソースの **[診断設定]** タブから選択します。 ゲスト メトリックが収集されない場合、またはメトリックス エクスプローラーでアクセスできない場合は、**仮想マシン ホスト** メトリック名前空間のみが表示されます。

![メトリックの画像](./media/metrics-troubleshoot/vm-metrics.png)

**解決方法:** **ゲスト (クラシック)** の名前空間とメトリックがメトリックス エクスプローラーに表示されない場合:

1. [Azure Diagnostics 拡張機能](../agents/diagnostics-extension-overview.md)が有効になっており、メトリックを収集するように構成されていることを確認します。
    > [!WARNING]
    > [Log Analytics エージェント](../agents/agents-overview.md#log-analytics-agent) (Microsoft Monitoring Agent または "MMA" とも呼ばれます) を使って **ゲスト (クラシック)** をストレージ アカウントに送信することはできません。

1. **Microsoft.Insights** リソース プロバイダーが [ご利用のサブスクリプションに登録されている](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)ことを確認します。

1. ストレージ アカウントがファイアウォールによって保護されていないことを確認します。 Azure portal では、メトリック データを取得してグラフをプロットするために、ストレージ アカウントにアクセスする必要があります。

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使って、メトリックがストレージ アカウントに送られていることを検証します。 メトリックが収集されていない場合は、[Azure Diagnostics 拡張機能のトラブルシューティング ガイド](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)に従ってください。

## <a name="next-steps"></a>次のステップ

* [メトリックス エクスプローラーの概要について確認する](metrics-getting-started.md)
* [メトリックス エクスプローラーの高度な機能の詳細](../essentials/metrics-charts.md)
* [Azure サービスで使用可能なメトリックのリストを表示する](./metrics-supported.md)
* [構成されたグラフの例を参照する](../essentials/metric-chart-samples.md)
