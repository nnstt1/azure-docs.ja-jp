---
title: 診断ログ
titleSuffix: Azure Cognitive Services
description: このガイドでは、Azure Cognitive Service の診断ログを有効にするための詳細な手順について説明します。 これらのログでは、問題の識別やデバッグに使用されるリソースの操作に関する豊富で頻繁なデータが提供されます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: pafarley
ms.openlocfilehash: 4c621fb7abc494247c9d736639766e6b7c095d8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080500"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Azure Cognitive Services の診断ログを有効にする

このガイドでは、Azure Cognitive Service の診断ログを有効にするための詳細な手順について説明します。 これらのログでは、問題の識別やデバッグに使用されるリソースの操作に関する豊富で頻繁なデータが提供されます。 続行する前に、少なくとも 1 つの Cognitive Service ([音声サービス](./speech-service/overview.md)や [LUIS](./luis/what-is-luis.md) など) へのサブスクリプションを持つ Azure アカウントが必要です。

## <a name="prerequisites"></a>前提条件

診断ログを有効にするには、ログ データを格納する場所が必要になります。 このチュートリアルでは、Azure Storage と Log Analytics を使用します。

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) - ポリシー監査、静的解析、またはバックアップの診断ログを保持します。 設定を構成するユーザーが両方のサブスクリプションに対して適切な Azure RBAC アクセスを持っている限り、ストレージ アカウントはログを出力するリソースと同じサブスクリプションに属している必要はありません。
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) - Azure リソースによって生成された生ログの分析を可能にする柔軟なログ検索および分析ツール。

> [!NOTE]
> * その他の構成オプションも使用できます。 詳細については、[Azure リソースからのログ データの収集と使用](../azure-monitor/essentials/platform-logs-overview.md)に関するページを参照してください。
> * 診断ログの "トレース" は、[カスタムの質問と回答](./qnamaker/how-to/get-analytics-knowledge-base.md?tabs=v2)にのみ使用できます。

## <a name="enable-diagnostic-log-collection"></a>診断ログの収集を有効にする  

最初に、Azure portal を使用して診断ログを有効にします。

> [!NOTE]
> PowerShell または Azure CLI を使用してこの機能を有効にするには、[Azure リソースからのログ データの収集と使用](../azure-monitor/essentials/platform-logs-overview.md)に関するページに示されている手順を使用します。

1. Azure Portal に移動します。 次に、Cognitive Services リソースを見つけて選択します。 たとえば、音声サービスのサブスクリプションです。   
2. 次に、左側のナビゲーション メニューから、 **[監視]** を見つけて **[診断設定]** を選択します。 この画面には、このリソースに対して前に作成したすべての診断設定が含まれています。
3. 前に作成したリソースで使用したいものが存在する場合は、ここでそれを選択できます。 それ以外の場合は、 **[+ Add diagnostic setting] (+ 診断設定の追加)** を選択します。
4. 設定の名前を入力します。 次に、 **[ストレージ アカウントへのアーカイブ]** および **[Log Analytics への送信]** を選択します。
5. 構成するよう求めるメッセージが表示されたら、診断ログを格納するために使用するストレージ アカウントと OMS ワークスペースを選択します。 **注**:ストレージ アカウントまたは OMS ワークスペースがない場合は、プロンプトに従って作成してください。
6. **[Audit]** 、 **[RequestResponse]** 、および **[AllMetrics]** を選択します。 次に、診断ログ データの保持期間を設定します。 保持ポリシーが 0 に設定されていると、そのログ カテゴリのイベントは無期限に格納されます。
7. **[保存]** をクリックします。

ログ データがクエリや分析に使用できるようになるまでに最大 2 時間かかることがあります。 そのため、すぐには何も表示されなくても気にしないでください。

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Azure Storage の診断データを表示およびエクスポートする

Azure Storage は、大量の非構造化データを格納するために最適化された、強力なオブジェクト ストレージ ソリューションです。 このセクションでは、ストレージ アカウントにクエリを実行して 30 日間の合計トランザクションを取得し、そのデータを Excel にエクスポートする方法について説明します。

1. Azure portal から、最後のセクションで作成した Azure Storage リソースを見つけます。
2. 左側のナビゲーション メニューから、 **[監視]** を見つけて **[メトリック]** を選択します。
3. 使用可能なドロップダウンを使用してクエリを構成します。 この例では、時間範囲を **[過去 30 日間]** に、メトリックを **[トランザクション]** に設定します。
4. クエリが完了すると、過去 30 日間のトランザクションの視覚化が表示されます。 このデータをエクスポートするには、ページの上部にある **[Excel へのエクスポート]** ボタンを使用します。

診断データで実行できる操作の詳細については、[Azure Storage](../storage/blobs/storage-blobs-introduction.md) に関するページを参照してください。

## <a name="view-logs-in-log-analytics"></a>Log Analytics ログを表示する

リソースに対するログ分析データを調査するには、次の手順に従います。

1. Azure portal で、左側のナビゲーション メニューから **[Log Analytics]** を見つけて選択します。
2. 診断を有効にするときに作成したリソースを見つけて選択します。
3. **[全般]** で、 **[ログ]** を見つけて選択します。 このページから、ログに対してクエリを実行できます。

### <a name="sample-queries"></a>サンプル クエリ

ログ データを調査するために使用できるいくつかの基本的な Kusto クエリを次に示します。

指定した期間の Azure Cognitive Services のすべての診断ログを表示するには、次のクエリを実行します。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

最新の 10 件のログを表示するには、次のクエリを実行します。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

**リソース** で操作をグループ化するには、次のクエリを実行します。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
操作を実行するためにかかる平均時間を見つけるには、次のクエリを実行します。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

OperationName によって分割された一定期間の操作の量と、10 秒ごとにビン分割された数を表示するには、次のクエリを実行します。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>次のステップ

* ログ記録を有効にする方法、および各種の Azure サービスでサポートされているメトリックやログのカテゴリを理解するには、Microsoft Azure での[メトリックの概要](../azure-monitor/data-platform.md)に関するページと「[Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)」の記事の両方を参照してください。
* Event Hubs については次の記事をご覧ください。
  * [Azure Event Hubs とは](../event-hubs/event-hubs-about.md)
  * [Event Hubs の使用](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* 「[Azure Monitor ログでのログ検索について](../azure-monitor/logs/log-query-overview.md)」をお読みください。