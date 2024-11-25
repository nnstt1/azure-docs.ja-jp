---
title: コマーシャル マーケットプレースでのパートナー センター分析用の概要 ダッシュボード
description: マーケットプレースのアクティビティが要約された集計データのグラフ、傾向、値にパートナー センターの Summary (概要) ダッシュボードからアクセスする方法を説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: ebf0a77eb4b0e79931af4f33a8adad4f6c115c22
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081542"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>コマーシャル マーケットプレース分析での [Summary] (概要) ダッシュボード

この記事では、パートナー センターの [Summary]\(概要\) ダッシュボードについて説明します。 このダッシュボードには、オファーに関するマーケットプレースのアクティビティがまとめられた、集計データのグラフ、傾向、値が表示されます。

>[!NOTE]
> 分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.yml)」を参照してください。

## <a name="summary-dashboard"></a>[Summary]\(概要\) ダッシュボード

[[概要] ダッシュボード](https://go.microsoft.com/fwlink/?linkid=2165765)には、Azure Marketplace および Microsoft AppSource のオファーのビジネス パフォーマンスの概要が示されます。 ダッシュボードは、次のことについて大まかに説明しています。

- 顧客の注文
- 顧客
- 顧客によるオファーの使用
- Azure Marketplace および AppSource での顧客のページ アクセス

## <a name="access-the-summary-dashboard"></a>概要ダッシュボードにアクセスする

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センター ホーム ページの [分析情報] タイルの図。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左側のメニューで **[概要]** を選択します。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[分析]**  >  **[概要]** の順に選択します。

---

## <a name="elements-of-the-summary-dashboard"></a>[Summary]\(概要\) ダッシュボードの要素

次のセクションでは、[Summary]\(概要\) ダッシュボードの使用方法とデータの読み取り方法について説明します。

### <a name="month-range"></a>月範囲

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

各ページの右上隅には、月範囲の選択が表示されます。 過去の指定した月数に基づく月範囲を選択することで、あるいは 12 か月を最大期間とするカスタムの月範囲を選択することで、 **[概要]** ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

[![[概要] ダッシュボードの月範囲オプションの画像。](./media/summary-dashboard/summary-dashboard-filters.png)](./media/summary-dashboard/summary-dashboard-filters.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

各ページの右上隅には、月範囲の選択が表示されます。 過去 3 か月、6 か月、または 12 か月の月範囲を選択することで、あるいは 12 か月を最大期間とするカスタムの月範囲を選択することで、 **[概要]** ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="[概要] ダッシュボードの月範囲オプションの画像。":::

---

> [!NOTE]
> 視覚化ウィジェットおよびエクスポート レポートのすべてのメトリックでは、ユーザーが選択した計算期間が優先されます。

### <a name="orders-widget"></a>[注文] ウィジェット

**[概要]** ダッシュボードの [注文] ウィジェットには、すべてのトランザクションベースのオファーの現在の注文が表示されます。 [注文] ウィジェットには、選択した計算期間のすべての購入した注文 (キャンセルされた注文を除く) の数と傾向が表示されます。 **[注文]** のパーセント値は、選択した計算期間の増加量を表します。

[![[概要] ダッシュボードの [注文] ウィジェットの画像。](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


また、ウィジェットの左下隅にある **[注文] ダッシュボード** リンクを選択して [注文] レポートに移動することもできます。

### <a name="customers-widget"></a>[顧客] ウィジェット

**[概要]** ダッシュボードの **[顧客]** ウィジェットには、選択した計算期間のオファーを購入した顧客の合計数が表示されます。 [顧客] ウィジェットには、選択した計算期間のアクティブな (新規および既存の) 顧客 (チャーン顧客を除く) の合計数と傾向が表示されます。 **[顧客]** の下のパーセント値は、選択した計算期間の増加量を表します。

[![[概要] ダッシュボードの [顧客] ウィジェットの画像。](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

また、ウィジェットの左下隅にある **[顧客] ダッシュボード** リンクを選択して詳しい [顧客] レポートに移動することもできます。

### <a name="usage-widget"></a>[使用状況] ウィジェット

**[概要]** ダッシュボードの **[使用状況]** ウィジェットは、すべての Azure 仮想マシン (VM) オファーの正規化した使用時間と生の使用時間の合計を表します。 [使用状況] ウィジェットには、選択した計算期間の総使用時間の数と傾向が表示されます。

使用状況の概要テーブルには、購入したすべてのオファーについて顧客の使用時間が表示されます。

- 正規化された使用時間は、VM コアの数を考慮するように正規化された使用時間として定義されます ([VM コアの数] x [未調整の使用時間])。 VM は "SHAREDCORE" として指定され、[VM コアの数] の乗数として 1/6 (または 0.1666) を使用します。
- 未調整の使用時間は、VM が実行されている時間量 (時間数単位) として定義されます。

総使用時間の下にあるパーセント値は、選択した計算期間中での使用時間の増加量を表します。

[![[概要] ダッシュボードの [使用状況] ウィジェットの画像。](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

また、ウィジェットの左下隅にある **[使用状況] ダッシュボード** リンクを選択して [使用状況] レポートに移動することもできます。

### <a name="marketplace-insights"></a>Marketplace の分析情報

Marketplace の分析情報では、Azure Marketplace および AppSource のオファーのページにアクセスしたユーザー数が表示されます。 **[ページ アクセス数]** には、コマーシャル マーケットプレース Web 分析の概要が表示されます。これにより公開元は、次のコマーシャル マーケットプレースのオンライン ストアに掲載されている、個々の製品詳細ページの顧客エンゲージメントを測定できます: Microsoft AppSource と Azure Marketplace。 このウィジェットには、選択した計算期間の総ページ アクセスの数と傾向が表示されます。

[![[概要] ダッシュボードの [ページ アクセス数] ウィジェットの画像。](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

また、ウィジェットの左下隅にある **[Marketplace の分析情報] ダッシュボード** リンクを選択して [Marketplace の分析情報] レポートに移動することもできます。

### <a name="geographical-spread"></a>地理的分布

このヒートマップには、選択した計算期間での顧客、注文、および正規化された使用時間の合計数が地域ディメンションに対して表示されます。

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="[概要] ダッシュボードの [展開している国] ウィジェットの画像。":::

次のことを考慮してください。

- マップを動かして正確な場所を見ることができます。
- 特定の場所にズーム インできます。
- このヒートマップには、特定の場所での顧客数、注文数、正規化された使用量 (時間) の詳細を表示するための補助グリッドがあります。
- グリッドで国またはリージョンを検索して選択すると、マップ内の場所にズームできます。 地図内の **[ホーム]** ボタンを選択すると、元のビューに戻ります。

> [!TIP]
> ウィジェットの右上隅にあるダウンロード アイコンを使用して、データをダウンロードできます。 "サムズアップ" アイコンまたは "サムズダウン" アイコンを選択することで、各ウィジェットに関するフィードバックを提供できます。

## <a name="next-steps"></a>次のステップ

- コマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析レポートにアクセスする](analytics.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「[コマーシャル マーケットプレース分析の使用量ダッシュボード](usage-dashboard.md)」を参照してください。
- 成長傾向など、顧客の詳細については、「[コマーシャル マーケットプレース分析の顧客ダッシュボード](customer-dashboard.md)」を参照してください。
<<<<<<< HEAD
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](./partner-center-portal/downloads-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「[パートナー センターの評価とレビューの分析ダッシュボード](./partner-center-portal/ratings-reviews.md)」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.md)」を参照してください。
=======
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](downloads-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「[パートナー センターの評価とレビューの分析ダッシュボード](ratings-reviews.md)」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.yml)」を参照してください。
>>>>>>> repo_sync_working_branch
