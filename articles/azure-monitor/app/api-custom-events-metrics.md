---
title: カスタムのイベントとメトリックのための Application Insights API | Microsoft Docs
description: デバイスまたはデスクトップ アプリケーション、Web ページ、またはサービスに数行のコードを追加して、使用状況の追跡や問題の診断を行います。
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 648c9ee1de00ef638354a287e43fa234610e9dc7
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291500"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>カスタムのイベントとメトリックのための Application Insights API

アプリケーションに数行のコードを挿入して、ユーザーの行動を調べたり、問題の診断に役立つ情報を取得したりすることができます。 デバイスとデスクトップ アプリケーション、Web クライアント、Web サーバーからテレメトリを送信できます。 [Azure Application Insights](./app-insights-overview.md) コア テレメトリ API を使用すると、カスタムのイベントやメトリック、独自バージョンの標準テレメトリを送信できます。 この API は、Application Insights の標準のデータ コレクターで使用される API と同じものです。

## <a name="api-summary"></a>API の概要

コア API は、`GetMetric` (.NET のみ) のようないくつかの違いは別として、すべてのプラットフォームにわたって同一です。

| Method | 使用目的 |
| --- | --- |
| [`TrackPageView`](#page-views) |ページ、画面、ブレード、フォーム |
| [`TrackEvent`](#trackevent) |ユーザー アクションとその他のイベント。 ユーザーの行動を追跡するために、またはパフォーマンスを監視するために使用されます。 |
| [`GetMetric`](#getmetric) |0 と多次元メトリックは、は、C# の場合のみ、一元的に構成された集計です。 |
| [`TrackMetric`](#trackmetric) |キューの長さなど、特定のイベントに関連しないパフォーマンスを測定します。 |
| [`TrackException`](#trackexception) |診断用に例外を記録します。 他のイベントとの関連で例外の発生箇所を追跡し、スタック トレースを調べます。 |
| [`TrackRequest`](#trackrequest) |パフォーマンス分析用にサーバー要求の頻度と期間を記録します。 |
| [`TrackTrace`](#tracktrace) |リソース診断ログ メッセージ。 サードパーティのログをキャプチャすることもできます。 |
| [`TrackDependency`](#trackdependency) |アプリが依存する外部コンポーネントへの呼び出しの実行時間と頻度を記録します。 |

これらのテレメトリの呼び出しのほとんどに [プロパティとメトリックをアタッチ](#properties) できます。

## <a name="before-you-start"></a><a name="prep"></a>開始する前に

Application Insights SDK の参照がまだない場合:

* Application Insights SDK をプロジェクトに追加します。

  * [ASP.NET プロジェクト](./asp-net.md)
  * [ASP.NET Core プロジェクト](./asp-net-core.md)
  * [Java プロジェクト](./java-in-process-agent.md)
  * [Node.js プロジェクト](./nodejs.md)
  * [各 Web ページの JavaScript](./javascript.md)
* デバイスまたは Web サーバー コードに次を追加します。

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>TelemetryClient インスタンスの取得

`TelemetryClient` インスタンスを取得します (Web ページの JavaScript を除く)。

[ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) アプリと [.NET/.NET Core 向けの非 HTTP/ワーカー](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) アプリの場合、それぞれのドキュメントに説明されているとおり、依存関係インジェクション コンテナーから `TelemetryClient` のインスタンスを取得することが推奨されます。

AzureFunctions v2 以降または Azure WebJobs v3 以降を使用する場合は、[このドキュメント](../../azure-functions/functions-monitoring.md)に従ってください。

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

このメソッドが現在不使用のメッセージであることがわかっている場合は、[microsoft/ApplicationInsights-dotnet # 1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) にアクセスして詳細を確認してください。

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
```

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient はスレッド セーフです。

ASP.NET および Java プロジェクトの場合は、受信 HTTP 要求が自動的にキャプチャされます。 アプリの他のモジュールのために TelemetryClient の追加のインスタンスを作成することもできます。 たとえば、ミドルウェア クラスでビジネス ロジック イベントを報告する 1 つの TelemetryClient インスタンスを使用できます。 マシンを識別するために UserId や DeviceId などのプロパティを設定できます。 こうした情報は、インスタンスから送信されるすべてのイベントに付属します。

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Node.js のプロジェクトでは、`new applicationInsights.TelemetryClient(instrumentationKey?)` を使用して新しいインスタンスを作成できますが、シングルトン `defaultClient` から分離された構成を必要とするシナリオにのみ推奨されます。

## <a name="trackevent"></a>TrackEvent

Application Insights の *カスタム イベント* はデータ ポイントであり、[メトリックス エクスプローラー](../essentials/metrics-charts.md)では集計カウントとして、[診断検索](./diagnostic-search.md)では個々の発生として表示できます。 (これは MVC にも他のフレームワークの "イベント" にも関連していません)。

さまざまなイベントをカウントするために、`TrackEvent` 呼び出しを挿入します。 これによって、ユーザーが特定の機能を使用する頻度や、特定の目標を達成する頻度、特定の種類の間違いを起こす頻度をカウントできます。

たとえば、ゲーム アプリで、ユーザーが勝利したときにイベントを送信します。

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Analytics でのカスタム イベント

テレメトリは、[Application Insights ログのタブ](../logs/log-query-overview.md)または[使用エクスペリエンス](usage-overview.md)の `customEvents` テーブルにあります。 イベントは、`trackEvent(..)` または[クリック分析自動収集プラグイン](javascript-click-analytics-plugin.md)から取得できます。

[サンプリング](./sampling.md)が実行中の場合は、itemCount プロパティは 1 より大きい値を示します。 たとえば itemCount==10 は trackEvent() への 10 回の呼び出しで、サンプリング プロセスはそれらのうちの 1 つだけを転送したことを意味します。 カスタム イベントの正しい数を取得するには、したがって `customEvents | summarize sum(itemCount)` などのコードを使用する必要があります。

## <a name="getmetric"></a>GetMetric

GetMetric() 呼び出しを効果的に使用し、.NET および .NET Core アプリケーション用にローカルで事前集計されたメトリックをキャプチャする方法については、[GetMetric](./get-metric.md) のドキュメントにアクセスしてください。

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric は、メトリックを送信するための推奨されるメソッドではありません。 メトリックは送信される前に必ず、ある期間にわたって事前に集計される必要があります。 GetMetric(..) オーバーロードのいずれかを使用して、SDK の事前集計機能にアクセスするためのメトリック オブジェクトを取得します。 独自の事前集計ロジックを実装する場合は、TrackMetric() メソッドを使用して集計結果を送信できます。 アプリケーションで、一定時間にわたる集計を行わず、すべての機会に個別のテレメトリ項目を送信する必要がある場合は、おそらくイベント テレメトリのユース ケースに該当します。TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry) を参照してください。

Application Insights では、特定のイベントに関連付けられていないメトリックをグラフ化できます。 たとえば、一定の間隔でキューの長さを監視できます。 メトリックでは、個々の測定値は変化と傾向よりも関心が薄いため、統計グラフが役に立ちます。

Application Insights にメトリックを送信するために、`TrackMetric(..)` API を使用できます。 メトリックを送信するには、次の 2 つの方法があります。

* 単一の値。 アプリケーションで、測定を実行するたびに、対応する値を Application Insights に送信します。 たとえば、コンテナー内の項目数を記述するメトリックがあるとします。 特定の期間に、まずコンテナーに 3 つの項目を配置し、次に 2 つの項目を削除します。 したがって、`TrackMetric` を 2 回呼び出します。最初に値 `3` を渡して、次に値 `-2` を渡します。 Application Insights は、両方の値を自動的に格納します。

* 集計。 メトリックを使用する場合、個々の測定値はあまり重要ではありません。 代わりに特定の期間に、発生したことの概要が重要です。 このような概要は _集計_ と呼ばれます。 上記の例で、その期間の集計メトリックの合計は `1` で、メトリック値のカウントは `2` です。 集計アプローチを使用する場合、期間ごとに `TrackMetric` を 1 回だけ呼び出し、集計値を送信します。 これは、すべての関連情報を収集しながら、Application Insights に送信するデータ ポイントを少なくすることによって、コストとパフォーマンスのオーバーヘッドを大幅に削減できるため、推奨される方法です。

### <a name="examples"></a>例

#### <a name="single-values"></a>単一の値

1 つのメトリック値を送信するには:

*JavaScript*

```javascript
appInsights.trackMetric({name: "queueLength", average: 42});
```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "queueLength";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
```

### <a name="custom-metrics-in-analytics"></a>Analytics でのカスタム メトリック

テレメトリは、[Application Insights Analytics](../logs/log-query-overview.md) の `customMetrics` テーブルにあります。 各行は、アプリでの `trackMetric(..)` に対する呼び出しを表します。

* `valueSum`: これは測定値の合計です。 平均値を取得するには、`valueCount` で除算します。
* `valueCount`: この `trackMetric(..)` 呼び出しで集計された測定値の数。

## <a name="page-views"></a>ページ ビュー

デバイスまたは Web ページ アプリケーションでは、各画面または各ページが読み込まれた場合既定でページ ビュー テレメトリが送信されます。 ただし、これを変更し、ページ ビューを追跡する回数を増やしたり、変えたりできます。 たとえば、タブまたはブレードを表示するアプリケーションで、ユーザーが新しいブレードを開いたときに常にページを追跡できます。

ユーザーとセッションのデータはページ ビューとともにプロパティとして送信されます。そのため、ページ ビューのテレメトリがあれば、ユーザーとセッションのグラフがアクティブになります。

### <a name="custom-page-views"></a>カスタム ページ ビュー

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

別々の HTML ページ内で複数のタブを使用している場合、URL を指定することもできます。

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>ページ ビューのタイミング

既定では、**ページ ビューの読み込み時間** として報告される時間は、ブラウザーが要求を送信した時点からブラウザーのページ読み込みイベントが呼び出されるまで測定されます。

代わりに、次のいずれかを行うことができます。

* [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) の呼び出し `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` で明示的な時間を設定する。
* ページ ビューのタイミングの呼び出し (`startTrackPage` と `stopTrackPage`) を使用する。

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

1 番目のパラメーターとして使用する名前により、開始呼び出しと停止呼び出しを関連付けます。 既定値は現在のページ名です。

メトリックス エクスプローラーに結果として表示されるページ読み込み時間は、開始呼び出しと停止呼び出しの時間間隔から求められます。 実際の時間間隔はユーザーが決定します。

### <a name="page-telemetry-in-analytics"></a>Analytics でのページ テレメトリ

[Analytics](../logs/log-query-overview.md) では、2 つのテーブルに、ブラウザー操作からのデータが表示されます。

* `pageViews` テーブルには、URL とページ タイトルに関するデータが含まれます。
* `browserTimings` テーブルには、受信データの処理にかかった時間などのクライアントのパフォーマンスに関するデータが含まれます。

ブラウザーでさまざまなページの処理にかかる時間を知るには、次のようにします。

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

さまざまなブラウザーの人気度を検出するには、次のようにします。

```kusto
pageViews
| summarize count() by client_Browser
```

AJAX 呼び出しにページ ビューを関連付けるには、依存関係によって結合します。

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

サーバー SDK では、TrackRequest を使用して HTTP 要求が記録されます。

Web サービス モジュールが実行されていない状況で要求をシミュレーションする場合に、これを自分で呼び出すこともできます。

ただし、要求テレメトリを送信するための推奨される方法は、要求が<a href="#operation-context">操作コンテキスト</a>として機能しているところです。

## <a name="operation-context"></a>操作コンテキスト

テレメトリ項目を操作コンテキストと関連付けることで、それらの項目を互いに相関させることができます。 標準の要求追跡モジュールでは、HTTP 要求の処理中に送信される例外や他のイベントに対してこの関連付けが行われます。 [検索](./diagnostic-search.md)と[分析](../logs/log-query-overview.md)では、操作 ID を使用して、要求に関連付けられたイベントを簡単に見つけることができます。

相関の詳細については、「[Application Insights におけるテレメトリの相関付け](./correlation.md)」を参照してください。

手動でテレメトリを追跡している場合、テレメトリの相関付けを確実に行うための最も簡単な方法として、次のパターンを使用できます。

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

操作コンテキストの設定に合わせて、指定した種類のテレメトリ項目を `StartOperation` で作成します。 テレメトリ項目は、操作を破棄するか `StopOperation` を明示的に呼び出すと送信されます。 テレメトリの種類として `RequestTelemetry` を使用する場合、その継続時間は開始から停止までの間の一定の間隔に設定されます。

操作のスコープ内にあるテレメトリ項目は、その操作の「子」になります。 操作コンテキストは入れ子にできます。

検索では、操作コンテキストを使用して **関連項目** の一覧が作成されます。

![関連項目](./media/api-custom-events-metrics/21.png)

カスタム操作の追跡の詳細については、「[Application Insights .NET SDK でカスタム操作を追跡する](./custom-operations-tracking.md)」をご覧ください。

### <a name="requests-in-analytics"></a>Analytics での要求

[Application Insights Analytics](../logs/log-query-overview.md) で、要求は `requests` テーブルに表示されます。

[サンプリング](./sampling.md) を操作中の場合は、itemCount プロパティに 1 より大きい値が表示されます。 たとえば itemCount==10 は trackRequest() への 10 回の呼び出しで、サンプリング プロセスはそれらのうちの 1 つだけを転送したことを意味します。 要求の正しい数と要求名別にセグメント化された平均所要時間を取得するには、次のようなコードを使用します。

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

次の目的で例外を Application Insights に送信します。

* 問題の頻度の指標として[例外の件数](../essentials/metrics-charts.md)を数える。
* [個々の発生を確認する](./diagnostic-search.md)。

レポートにはスタック トレースが含まれます。

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException({exception: ex});
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

SDK が多数の例外を自動的にキャッチするため、常に TrackException を明示的に呼び出す必要はありません。

* ASP.NET:[例外をキャッチするコードを記述します](./asp-net-exceptions.md)。
* Java EE:[例外は自動的にキャッチされます](./java-in-process-agent.md)。
* JavaScript:例外は自動的にキャッチされます。 自動コレクションを無効にする場合は、Web ページに挿入するコード スニペットに次の行を追加します。

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Analyticsでの例外

[Application Insights Analytics](../logs/log-query-overview.md) で、例外は `exceptions` テーブルに表示されます。

[サンプリング](./sampling.md)が実行中の場合は、`itemCount` プロパティは 1 より大きい値を示します。 たとえば itemCount==10 は trackException() への 10 回の呼び出しで、サンプリング プロセスはそれらのうちの 1 つだけを転送したことを意味します。 例外の種類別にセグメント化された例外の正しい数を取得するには、次のようなコードを使用します。

```kusto
exceptions
| summarize sum(itemCount) by type
```

重要なスタック情報の大部分は既に個別の変数に抽出されていますが、`details` 構造を分析してさらに詳細な情報を取得できます。 この構造は動的なので、期待する型に結果をキャストする必要があります。 次に例を示します。

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

例外を関連する要求に関連付けるには、結合を使用します。

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

TrackTrace を使用すると、Application Insights に "階層リンクの追跡" を送信して問題を診断できます。 診断データのチャンクを送信し、[診断検索](./diagnostic-search.md)で検査できます。

.NET [ログ アダプター](./asp-net-trace-logs.md)では、この API を使用してポータルにサードパーティのログを送信します。

Java では、[Application Insights Java エージェント](java-in-process-agent.md)によってログが自動収集され、ポータルに送信されます。

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*クライアント/ブラウザー側の JavaScript*

```javascript
trackTrace({
    message: string, 
    properties?: {[string]:string}, 
    severityLevel?: SeverityLevel
})
```

メソッドの出入りなどの診断イベントをログに記録します。

 パラメーター | 説明
---|---
`message` | 診断データ。 名前よりはるかに長くなることがあります。
`properties` | 文字列と文字列のマップ:ポータルで、[例外のフィルター](#properties)に使用される追加のデータ。 既定値は空です。
`severityLevel` | サポートされる値:[SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

メッセージ コンテンツで検索できますが、(プロパティ値とは異なり) フィルター処理はできません。

`message` のサイズ制限は、プロパティの制限よりも非常に高くなっています。
TrackTrace の利点は、比較的長いデータをメッセージの中に配置できることです。 たとえば、メッセージ中で POST データをエンコードできます。

加えて、メッセージに重大度レベルを追加することができます。 また他のテレメトリと同様、プロパティ値を追加することで、さまざまなトレースの組み合わせをフィルタリングしたり検索したりすることができます。 次に例を示します。

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

この後に [Search](./diagnostic-search.md) で、特定のデータベースに関連し特定の重大度レベルを持つすべてのメッセージを簡単に抽出することができます。

### <a name="traces-in-analytics"></a>Analytics でのトレース

[Application Insights Analytics](../logs/log-query-overview.md) で、TrackTrace への呼び出しは `traces` テーブルに表示されます。

[サンプリング](./sampling.md)が実行中の場合は、itemCount プロパティは 1 より大きい値を示します。 たとえば、itemCount==10 は、サンプリング プロセスで転送されたのは `trackTrace()` への 10 回の呼び出しのうち 1 回だけであることを意味します。 トレース呼び出しの正確な数を取得するには、`traces | summarize sum(itemCount)` などのコードを使用する必要があります。

## <a name="trackdependency"></a>TrackDependency

応答時間と外部コードの呼び出しの成功率を追跡するには、TrackDependency 呼び出しを使用します。 結果は、ポータルの依存関係グラフに表示されます。 依存関係呼び出しが行われるたびに、以下のコード スニペットを追加する必要があります。

> [!NOTE]
> .NET および .NET Core の場合は、関連付けに必要な `DependencyTelemetry` プロパティと、開始時刻や期間などの他のプロパティを設定する `TelemetryClient.StartOperation` (拡張機能) メソッドを代わりに使用できるため、下の例のようにカスタム タイマーを作成する必要はありません。 詳細については、こちらの記事の[出力方向の依存関係の追跡に関するセクション](./custom-operations-tracking.md#outgoing-dependencies-tracking)を参照してください。

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    dependencyTelemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

サーバー SDK には、データベースや REST API などに対する依存関係の呼び出しを自動的に検出して追跡する[依存関係モジュール](./asp-net-dependencies.md)が含まれています。 このモジュールを機能させるには、サーバーにエージェントをインストールする必要があります。

Java では、[Application Insights Java エージェント](java-in-process-agent.md)を使用して、多くの依存関係呼び出しを自動的に追跡することができます。

この呼び出しは、自動追跡ではキャッチされない呼び出しを追跡する場合に使用します。

C# の標準の依存関係追跡モジュールを無効にするには、[ApplicationInsights.config](./configuration-with-applicationinsights-config.md) を編集し、`DependencyCollector.DependencyTrackingTelemetryModule` への参照を削除します。 Java の場合は、「[特定の自動収集テレメトリの抑制](./java-standalone-config.md#suppressing-specific-auto-collected-telemetry)」を参照してください。

### <a name="dependencies-in-analytics"></a>Analytics での依存関係

[Application Insights Analytics](../logs/log-query-overview.md) で、trackDependency 呼び出しは `dependencies` テーブルに表示されます。

[サンプリング](./sampling.md)が実行中の場合は、itemCount プロパティは 1 より大きい値を示します。 たとえば itemCount==10 は trackDependency() への 10 回の呼び出しで、サンプリング プロセスはそれらのうちの 1 つだけを転送したことを意味します。 ターゲット コンポーネント別にセグメント化された依存関係の正しい数を取得するには、次のようなコードを使用します。

```kusto
dependencies
| summarize sum(itemCount) by target
```

依存関係を関連する要求に関連付けるには、結合を使用します。

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>データのフラッシュ

通常、SDK からは一定の間隔 (通常 30 秒) で、またはバッファがいっぱいになったとき (通常 500 項目) にデータが送信されます。 ただし、終了するアプリケーションで SDK を使用する場合などには、バッファーのフラッシュが必要になることがあります。

*C#*

```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

[サーバー テレメトリ チャネル](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)の場合、この関数は非同期です。

理想的には、アプリケーションのシャットダウン アクティビティで flush() メソッドを使用する必要があります。

## <a name="authenticated-users"></a>認証されたユーザー

Web アプリでは、ユーザーは (既定では) [Cookie により識別](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool)されます。 ユーザーは、別のコンピューターまたはブラウザーからアプリにアクセスしたり、Cookie を削除した場合、複数回カウントされることがあります。

ユーザーがアプリにサインインしていれば、認証されたユーザーの ID をブラウザー コードに設定して、より正確な数値を取得できます。

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

ASP.NET Web MVC アプリケーションでの例:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

ユーザーの実際のサインイン名を使用する必要はありません。 必要なのは、そのユーザーに一意の ID であるということだけです。 ID には、スペースや `,;=|` を含めることはできません。

ユーザー ID はセッション Cookie にも設定され、サーバーに送信されます。 サーバー SDK がインストールされている場合、認証されたユーザー ID は、クライアントおよびサーバー テレメトリの両方のコンテキスト プロパティの一部として送信されます。 送信後、フィルター処理や検索を行うことができます。

アカウントにアプリのグループ ユーザーがある場合、アカウントの識別子も渡すことができます (同じ文字制約が適用されます)。

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

[メトリックス エクスプローラー](../essentials/metrics-charts.md)で、**ユーザー、認証アカウント**、**ユーザー アカウント** をカウントするグラフを作成できます。

また、特定のユーザー名とアカウントを持つクライアント データ ポイントを[検索する](./diagnostic-search.md)こともできます。

> [!NOTE]
> .NET Core SDK の [ApplicationInsightsServiceOptions クラスの EnableAuthenticationTrackingJavaScript プロパティ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)を使用すると、Application Insights JavaScript SDK によって送信される各トレースの認証 ID としてユーザー名を挿入するために必要な JavaScript 構成が簡略化されます。 このプロパティが true に設定されている場合、ASP.NET Core のユーザーのユーザー名が[クライアント側のテレメトリ](asp-net-core.md#enable-client-side-telemetry-for-web-applications)と共に出力されます。そのため、`appInsights.setAuthenticatedUserContext` を手動で追加する必要はなくなります。ASP.NET CORE の SDK によって既に挿入されているためです。 認証 ID は、[JavaScript API リファレンス](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#setauthenticatedusercontext)で説明されているように、サーバーにも送信され、そこで、.NET CORE の SDK によって識別され、サーバー側のテレメトリに使用されます。 ただし、ASP.NET Core MVC と同じ方法で動作しない JavaScript アプリケーション (SPA Web アプリなど) の場合は、やはり、`appInsights.setAuthenticatedUserContext` を手動で追加する必要があります。

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>プロパティを使用したデータのフィルタリング、検索、セグメント化

プロパティと測定値をイベント (およびメトリック、ページ ビュー、その他のテレメトリ データ) に結び付けることができます。

*プロパティ* は、利用状況レポートでテレメトリをフィルター処理するのに使用できる文字列値です。 たとえば、アプリケーションで複数のゲームを提供する場合、各イベントにゲームの名前を結び付けることで人気のあるゲームを確認できます。

文字列の長さには 8,192 の制限があります。 (データの大きなチャンクを送信する場合は、TrackTrace のメッセージ パラメーターを使用します。)

*メトリックス* はグラフィカルに表示できる数値です。 たとえば、ゲーマーが達成するスコアに漸増があるかどうかを確認できます。 イベントともに送信されるプロパティ別にグラフをセグメント化し、ゲームごとの個別のグラフや積み重ねグラフを表示できます。

メトリック値は、0 以上でないと正しく表示されません。

使用できる [プロパティ、プロパティ値、およびメトリックの数には制限](#limits) があります。

*JavaScript*

```javascript
appInsights.trackEvent({
  name: 'some event',
  properties: { // accepts any type
    prop1: 'string',
    prop2: 123.45,
    prop3: { nested: 'objects are okay too' }
  }
});

appInsights.trackPageView({
  name: 'some page',
  properties: { // accepts any type
    prop1: 'string',
    prop2: 123.45,
    prop3: { nested: 'objects are okay too' }
  }
});
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> プロパティで個人を特定できる情報を記録しないように注意します。

### <a name="alternative-way-to-set-properties-and-metrics"></a>プロパティとメトリックを設定する別の方法

個別のオブジェクトでイベントのパラメーターを集めたほうが便利であれば、そのようにできます。

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Track*() を複数回呼び出すために、同じテレメトリ項目インスタンス (この例では `event`) を再利用しないでください。 再利用すると、正しくない構成でテレメトリが送信される場合があります。

### <a name="custom-measurements-and-properties-in-analytics"></a>Analytics でのカスタム測定とプロパティ

[Analytics](../logs/log-query-overview.md) で、カスタム メトリックとプロパティは、各テレメトリ レコードの `customMeasurements` および `customDimensions` 属性に表示されます。

たとえば、要求テレメトリに "game" というプロパティを追加した場合、このクエリは "game" のさまざまな値の出現数をカウントし、カスタム メトリック "score" の平均を表示します。

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

次のことに注意してください。

* customDimensions または customMeasurements JSON から値を抽出する場合、これは動的な型を持つため、`tostring` または `todouble` にキャストする必要があります。
* [サンプリング](./sampling.md)の可能性について考慮するには、`count()` ではなく、`sum(itemCount)` を使用する必要があります。

## <a name="timing-events"></a><a name="timed"></a> タイミング イベント

アクションの実行にかかる時間をグラフで示す必要が生じることがあります。 たとえば、ユーザーがゲームで選択肢について考える時間について調べるとします。 測定パラメーターを使用することでこの調査を行うことができます。

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>カスタム テレメトリの既定のプロパティ

記述するカスタム イベントのいくつかに既定のプロパティ値を設定する必要がある場合、TelemetryClient インスタンスで設定できます。 既定値は、そのクライアントから送信されたすべてのテレメトリ項目に追加されます。

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...

TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

個別のテレメトリの呼び出しはプロパティ ディクショナリの既定値をオーバーライドできます。

*JavaScript Web クライアントの場合*、JavaScript テレメトリ初期化子を使用します。

標準コレクション モジュールのデータなど、*すべてのテレメトリにプロパティを追加する* には、[`ITelemetryInitializer` を実装します](./api-filtering-sampling.md#add-properties)。

## <a name="sampling-filtering-and-processing-telemetry"></a>テレメトリのサンプリング、フィルタリング、および処理

テレメトリを SDK からの送信前に処理するコードを記述することができます。 この処理では、HTTP 要求のコレクションや依存関係のコレクションなど、標準的なテレメトリ モジュールから送信されるデータも対象となります。

テレメトリに[プロパティを追加する](./api-filtering-sampling.md#add-properties)には、`ITelemetryInitializer` を実装します。 たとえば、バージョン番号や、他のプロパティから算出された値を追加できます。

`ITelemetryProcessor` を実装すると、テレメトリが SDK から送信される前に[フィルタリング](./api-filtering-sampling.md#filtering)によってテレメトリを変更または破棄することができます。 送信対象や破棄対象を指定できますが、メトリックへの影響を考慮する必要があります。 項目を破棄する方法によっては、関連する項目間を移動する機能が失われる可能性があります。

[サンプリング](./api-filtering-sampling.md)は、アプリからポータルに送信されるデータの量を減らすためのパッケージ化ソリューションです。 これにより、表示されるメトリックに影響をあたえることなくデータ量を削減できます。 また、サンプリングを行った場合でも、変わらずに例外、要求、ページ ビューなどの関連する項目間を移動して問題を診断できます。

[詳細については、こちらを参照してください](./api-filtering-sampling.md)。

## <a name="disabling-telemetry"></a>テレメトリの無効化

テレメトリの収集と送信を *動的に停止および開始* するには

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

*選択されている標準のコレクターを無効にする* には (たとえば、パフォーマンス カウンター、HTTP 要求、依存関係)、[ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 内の該当する行を削除するか、コメントアウトします。たとえば、独自の TrackRequest データを送信する場合にこれを行います。

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

初期化時にパフォーマンス カウンター、HTTP 要求、依存関係などの *選択されている標準コレクターを無効にする* には、構成メソッドを次のように SDK の初期化コードに追加します。

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

初期化後にこれらのコレクターを無効にするには、構成オブジェクト `applicationInsights.Configuration.setAutoCollectRequests(false)` を使用します。

## <a name="developer-mode"></a><a name="debug"></a>開発者モード

デバッグ中、結果をすぐに確認できるように、テレメトリをパイプラインから送信すると便利です。 テレメトリで問題を追跡する際に役立つ付加的なメッセージも取得できます。 アプリケーションの速度を低下させる可能性があるため、本稼働ではオフにしてください。

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Node.js の場合は、`setInternalLogging` 経由で内部ログを有効にし、`maxBatchSize` を 0 に設定することによって開発者モードを有効にすることができます。これにより、テレメトリは、収集されるとすぐに送信されます。

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> 選択したカスタム テレメトリにインストルメンテーション キーを設定する

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> 動的なインストルメンテーション キー

開発、テスト、運用の各環境のテレメトリが混じらないようにするために、環境に応じて[個別の Application Insights リソースを作成](./create-new-resource.md)し、それぞれのキーを変更できます。

インストルメンテーション キーは構成ファイルから取得する代わりにコードで設定できます。 ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Web ページでは、スクリプトに一語一語コーディングするのではなく、Web サーバーの状態から設定することもできます。 たとえば、ASP.NET アプリで生成される Web ページでは次のように設定します。

*Razor の JavaScript*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient には、すべてのテレメトリ データとともに送信される値が含まれる Context プロパティが備わっています。 通常、標準のテレメトリ モジュールによって設定されますが、自分で設定することもできます。 次に例を示します。

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

いずれかの値を自分で設定した場合は、その値と標準の値が混同されないように、[ApplicationInsights.config](./configuration-with-applicationinsights-config.md) から該当する行を削除することを検討します。

* **Component**:アプリそのバージョン。
* **Device**:アプリが実行されているデバイスに関するデータ (Web アプリでは、テレメトリを送信するサーバーまたはクライアント デバイスのデータ)。
* **InstrumentationKey**:テレメトリが表示される Azure 内の Application Insights リソース。 通常、ApplicationInsights.config から取得されます。
* **[場所]** :デバイスの地理的な場所。
* **Operation**:Web アプリでは現在の HTTP 要求。 他の種類のアプリケーションでは、イベントをグループ化するために、これを設定できます。
  * **[ID]** :診断検索でイベントを調べるときに関連項目を見つけることができるように、さまざまなイベントを関連付けるために生成される値。
  * **Name**:識別子。通常は HTTP 要求の URL です。
  * **SyntheticSource**:null 値または空ではない場合に、要求元がロボットまたは Web テストとして識別されたことを示す文字列。 既定で、メトリックス エクスプローラーの計算から除外されます。
* **セッション**:ユーザーのセッション。 ID は生成された値に設定されますが、ユーザーがしばらくの間アクティブでない場合には変更されます。
* **[ユーザー]** :ユーザー情報。

## <a name="limits"></a>制限

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

データ速度の上限に達するのを回避するには、[サンプリング](./sampling.md)を使用します。

データの保持期間を確認する方法については、[データの保持とプライバシー](./data-retention-privacy.md)に関するページを参照してください。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [ASP.NET リファレンス](/dotnet/api/overview/azure/insights)
* [Java リファレンス](/java/api/overview/azure/appinsights)
* [JavaScript リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>SDK コード

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server パッケージ](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>疑問がある場合

* *Track_() の呼び出しでは、どのような例外がスローされることがありますか。*

    [なし] : try catch 句で例外をラップする必要はありません。 SDK で問題が発生すると、デバッグ コンソール出力のメッセージが記録されます。メッセージがスルーされる場合は、診断検索にも記録されます。
* *ポータルからデータを取得する REST API はありますか。*

    はい、[データ アクセス API](https://dev.applicationinsights.io/) があります。 データを抽出する別の方法としては、[Analytics から Power BI へのエクスポート](./export-power-bi.md)や[連続エクスポート](./export-telemetry.md)などがあります。

## <a name="next-steps"></a><a name="next"></a>次のステップ

* [イベントおよびログを検索する](./diagnostic-search.md)
* [トラブルシューティング](../faq.yml)
