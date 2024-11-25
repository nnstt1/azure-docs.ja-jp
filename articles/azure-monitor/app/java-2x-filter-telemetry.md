---
title: Java Web アプリでの Azure Application Insights テレメトリのフィルター処理
description: 監視する必要のないイベントをフィルターで除外して、テレメトリのトラフィックを削減します。
ms.topic: conceptual
ms.date: 3/14/2019
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 0170a0075d9f5eea7088a82e884b02241713210a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078923"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Java Web アプリでのテレメトリのフィルター処理

> [!CAUTION]
> このドキュメントは、推奨されなくなった Application Insights Java 2.x に適用されます。
>
> 最新バージョン用のドキュメントについては [Application Insights Java 3.x](./java-in-process-agent.md) に関するページをご覧ください。

フィルターは、お使いの [Java Web アプリが Application Insights に送信](java-2x-get-started.md)するテレメトリを選択する方法を提供します。 すぐに使用できるフィルターが用意されており、また、独自のカスタム フィルターを作成することもできます。

すぐに使えるフィルターは、次のとおりです。

* トレース重大度レベル
* 特定の URL、キーワード、または応答コード
* 迅速な応答 - つまり、アプリが迅速に応答した要求
* 特定のイベント名

> [!NOTE]
> フィルターでは、アプリのメトリックにゆがみが生じます。 たとえば、遅い応答を診断するために、高速な応答時間を破棄するフィルターを設定するとします。 ただし、Application Insights によって報告される平均応答時間は実際の速度より低くなること、また、要求数は実際の数より少なくなることに注意する必要があります。
> これが問題になる場合は、代わりに[サンプリング](./sampling.md)を使用します。

## <a name="setting-filters"></a>フィルターを設定する

ApplicationInsights.xml で、次の例のように `TelemetryProcessors` セクションを追加します。


```xml

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```

[すべてのビルトイン プロセッサを確認します](https://github.com/microsoft/ApplicationInsights-Java/tree/main/agent/agent-tooling/src/main/java/com/microsoft/applicationinsights/agent/internal)。

## <a name="built-in-filters"></a>ビルトイン フィルター

### <a name="metric-telemetry-filter"></a>メトリック テレメトリ フィルター

```xml

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` - カスタム メトリック名のコンマ区切りのリスト。


### <a name="page-view-telemetry-filter"></a>ページ ビュー テレメトリ フィルター

```xml

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` - ここで、期間はページの読み込みにかかる時間です。 これを設定した場合、この時間よりも高速に読み込まれたページは報告されません。
* `NotNeededNames` - ページ名のコンマ区切りリスト。
* `NotNeededUrls` - URL フラグメントのコンマ区切りリスト。 たとえば、`"home"` は URL に "home" のあるすべてのページを除外します。


### <a name="request-telemetry-filter"></a>要求テレメトリ フィルター


```xml

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>合成ソース フィルター

SyntheticSource プロパティに値を持つすべてのテレメトリを除外します。 これには、ボット、スパイダー、および可用性テストからの要求も含まれます。

すべての合成要求のテレメトリを除外します。


```xml

           <Processor type="SyntheticSourceFilter" />
```

特定の合成ソースのテレメトリを除外します。


```xml

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` - 合成ソース名のコンマ区切りのリスト。

### <a name="telemetry-event-filter"></a>テレメトリ イベント フィルター

([TrackEvent()](./api-custom-events-metrics.md#trackevent) を使って記録された) カスタム イベントをフィルターします。


```xml

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` - イベント名のコンマ区切りリスト。


### <a name="trace-telemetry-filter"></a>トレース テレメトリ フィルター

([TrackTrace()](./api-custom-events-metrics.md#tracktrace) または [ログ記録フレームワーク コレクター](java-2x-trace-logs.md)を使用して記録された) ログ トレースをフィルターします。

```xml

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` の有効な値は次のとおりです。
  *  OFF - すべてのトレースを除外
  *  TRACE - フィルタリングなし。 トレース レベルに等しい
  *  INFO - TRACE レベルを除外
  *  WARN - TRACE と INFO を除外
  *  ERROR - WARN、INFO、TRACE を除外
  *  CRITICAL - CRITICAL 以外のすべてを除外


## <a name="custom-filters"></a>カスタム フィルター

### <a name="1-code-your-filter"></a>1.フィルターをコーディングする

コード内に、`TelemetryProcessor` を実装するクラスを作成します。

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

        /* Any parameters that are required to support the filter.*/
        private final String successful;

        /* Initializers for the parameters, named "setParameterName" */
        public void setNotNeeded(String successful)
        {
            this.successful = successful;
        }

        /* This method is called for each item of telemetry to be sent.
           Return false to discard it.
           Return true to allow other processors to inspect it. */
        @Override
        public boolean process(Telemetry telemetry) {
            if (telemetry == null) { return true; }
            if (telemetry instanceof RequestTelemetry)
            {
                RequestTelemetry requestTelemetry = (RequestTelemetry)    telemetry;
                return request.getSuccess() == successful;
            }
            return true;
        }
    }

```

### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2.構成ファイルでフィルターを呼び出す

ApplicationInsights.xml で、以下を使用します。

```xml


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3.お使いのフィルターを呼び出す (Java Spring)

Spring フレームワークに基づくアプリケーションの場合、カスタムのテレメトリ プロセッサを、ご自身のメイン アプリケーション クラスに Bean として登録する必要があります。 その後、アプリケーションの開始時に自動接続されます。

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

独自のフィルター パラメーターを `application.properties` に作成し、Spring Boot の外部化された構成フレームワークを利用して、そのパラメーターをご自身のカスタム フィルターに渡す必要があります。 


## <a name="troubleshooting"></a>トラブルシューティング

"*フィルターが機能しません。* "

* 有効なパラメーター値を指定していることを確認してください。 たとえば、期間は整数である必要があります。 無効な値を指定すると、フィルターは無視されます。 カスタム フィルターがコンストラクターまたは set メソッドから例外をスローした場合、これも無視されます。

## <a name="next-steps"></a>次のステップ

* [サンプリング](./sampling.md) - メトリックがゆがめられない代替方法として、サンプリングを検討してください。

