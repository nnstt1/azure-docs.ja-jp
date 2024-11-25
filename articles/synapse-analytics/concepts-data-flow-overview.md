---
title: データ フロー
description: Azure Synapse Analytics のデータ フローの概要
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 525fed571576f6d5fc49d68abbbe26e46cf3291e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862257"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Azure Synapse Analytics のデータ フロー

## <a name="what-are-data-flows"></a>データ フローとは

データ フローは、Azure Synapse Analytics における視覚的に設計されたデータ変換です。 データ フローを使用すると、データ エンジニアは、コードを記述することなくデータ変換ロジックを開発できます。 生成されたデータ フローは、スケールアウトされた Apache Spark クラスターを使用する Azure Synapse Analytics パイプライン内のアクティビティとして実行されます。 データ フロー アクティビティは、既存の Azure Synapse Analytics のスケジュール設定、制御、フロー、監視機能を使用して運用可能にすることができます。

データ フローは、コーディングを必要としない、完全に視覚的なエクスペリエンスを提供します。 データ フローは、スケール アウトされたデータ処理のために、Synapse で管理される実行クラスターで実行されます。 コードの翻訳、パスの最適化、データ フロー ジョブの実行はすべて、Azure Synapse Analytics によって処理されます。

## <a name="getting-started"></a>作業の開始

データ フローは、Synapse Studio の [開発] ペインから作成されます。 データ フローを作成するには、 **[開発]** の横にあるプラス記号アイコンを選択し、 **[データ フロー]** を選択します。 

![新しいデータ フロー](media/data-flow/new-data-flow.png)

このアクションにより、変換ロジックを作成できるデータ フロー キャンバスに移動します。 **[ソースの追加]** を選択すると、ソース変換の構成が開始します。 詳細については、[ソース変換](../data-factory/data-flow-source.md?context=/azure/synapse-analytics/context/context)に関するページを参照してください。

## <a name="authoring-data-flows"></a>データ フローの作成

データ フローには、変換ロジックを簡単に構築するために設計された独自の作成キャンバスがあります。 データ フロー キャンバスは、上部バー、グラフ、および構成パネルの 3 つの部分に分かれています。 

![スクリーンショットには、上部バー、グラフ、および構成パネルにラベルが付いたデータ フロー キャンバスが示されています。](media/data-flow/canvas-1.png)

### <a name="graph"></a>グラフ

グラフには変換ストリームが表示されます。 ここにはソース データが 1 つ以上のシンクに流れるときのソース データの系列が表示されます。 新しいソースを追加するには、 **[ソースの追加]** を選択します。 新しい変換を追加するには、既存の変換の右下にあるプラス記号を選択します。 詳しくは、[データ フロー グラフの管理](../data-factory/concepts-data-flow-manage-graph.md?context=/azure/synapse-analytics/context/context)方法に関するページを参照してください。

![スクリーンショットには、[検索] テキスト ボックスがあるキャンバスのグラフ部分が示されています。](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>構成パネル

構成パネルには、現在選択されている変換に固有の設定が表示されます。 変換が選択されていない場合は、データ フローが表示されます。 データフローの全体構成では、 **[パラメーター]** タブを使用してパラメーターを追加できます。詳しくは、[データ フロー パラメーター](../data-factory/parameters-data-flow.md?context=/azure/synapse-analytics/context/context)に関するページを参照してください。

各変換には、少なくとも 4 つの構成タブが含まれます。

#### <a name="transformation-settings"></a>変換設定

各変換の構成ウィンドウの最初のタブには、その変換に固有の設定が含まれています。 詳しくは、各変換のドキュメント ページを参照してください。

![[Source Settings] タブ](media/data-flow/source-1.png)

#### <a name="optimize"></a>最適化

**[最適化]** タブには、パーティション分割を構成するためのオプション設定が含まれています。 データ フローを最適化する方法の詳細については、[マッピング データ フローのパフォーマンス ガイド](../data-factory/concepts-data-flow-performance.md?context=/azure/synapse-analytics/context/context)に関する記事を参照してください。

![[最適化] タブを示すスクリーンショット](media/data-flow/optimize.png)

#### <a name="inspect"></a>検査

**[Inspect]\(検査\)** タブには、変換するデータ ストリームのメタデータのビューが表示されます。 列数、変更された列、追加された列、データ型、列の順序、および列の参照を確認できます。 **[Inspect]\(検査\)** は、メタデータの読み取り専用ビューです。 **[Inspect]\(検査\)** ペインでメタデータを表示するためにデバッグ モードを有効にする必要はありません。

![[Inspect]\(検査\) タブ](media/data-flow/inspect.png)

変換を使ってデータの形状を変更すると、メタデータの変更が **[Inspect]\(検査\)** ペインに反映されます。 ソースの変換に定義済みのスキーマがない場合、メタデータは **[Inspect]\(検査\)** ペインに表示されません。 スキーマの誤差シナリオでは、メタデータがないことは一般的です。

#### <a name="data-preview"></a>データのプレビュー

デバッグ モードがオンの場合、 **[データのプレビュー]** タブには、各変換のデータの対話型スナップショットが表示されます。 詳細については、[デバッグ モードでのデータのプレビュー](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context#data-preview)に関するセクションを参照してください。

### <a name="top-bar"></a>上部バー

上部バーには、検証やデバッグ設定など、データ フロー全体に影響を与えるアクションが含まれています。 基になる JSON コードと、変換ロジックのデータ フロー スクリプトも表示できます。

## <a name="available-transformations"></a>使用可能な変換

使用可能な変換の一覧を取得するには、「[マッピング データ フロー変換の概要](../data-factory/data-flow-transformation-overview.md?context=/azure/synapse-analytics/context/context)」を表示してださい。

## <a name="data-flow-activity"></a>データ フロー アクティビティ

データ フローは、Azure Synapse Analytics パイプライン内で[データ フロー アクティビティ](../data-factory/control-flow-execute-data-flow-activity.md?context=/azure/synapse-analytics/context/context)を使用して運用可能にすることができます。 ユーザーが行う必要があるのは、使用する統合ランタイムを指定し、パラメーター値を渡すことだけです。 詳細については、「[Azure 統合ランタイム](../data-factory/concepts-integration-runtime.md?context=/azure/synapse-analytics/context/context#azure-integration-runtime)」を参照してください。

## <a name="debug-mode"></a>デバッグ モード

デバッグ モードを使用すると、データ フローを構築してデバッグしながら、各変換ステップの結果を対話形式で表示できます。 デバッグ セッションは、データ フロー ロジックを構築するときと、データ フロー アクティビティでパイプライン デバッグを実行するときの両方で使用できます。 詳細については、[デバッグ モードのドキュメント](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context)を参照してください。

## <a name="monitoring-data-flows"></a>データ フローの監視

データ フローは、既存の Azure Synapse Analytics 監視機能と統合されます。 データ フローの監視出力を理解する方法については、[マッピング データ フローの監視](../data-factory/concepts-data-flow-monitoring.md?context=/azure/synapse-analytics/context/context)に関するページを参照してください。

ビジネス ロジックを構築した後のデータ フローの実行時間を最適化するのに役立つ、[パフォーマンス チューニング ガイド](../data-factory/concepts-data-flow-performance.md?context=/azure/synapse-analytics/context/context)が、Azure Synapse Analytics チームによって作成されています。

## <a name="next-steps"></a>次のステップ

* [ソース変換](../data-factory/data-flow-source.md?context=/azure/synapse-analytics/context/context)を作成する方法について学習します。
* データ フローを[デバッグ モード](../data-factory/concepts-data-flow-debug-mode.md?context=/azure/synapse-analytics/context/context)で構築する方法について学習します。
