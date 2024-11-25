---
title: Azure Monitor でのログ データ インジェスト時間 | Microsoft Docs
description: Azure Monitor でのログ データ収集の待機時間に影響するさまざまな要因について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: ff7f250fa7fae2fb0a68710c94a9437204a61439
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714830"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Azure Monitor でのログ データ インジェスト時間
Azure Monitor とは、毎月増加するテラバイト単位のデータを送信する何千もの顧客にサービスを提供する高スケールのデータ サービスです。 ログ データが収集されてから、そのデータが使用可能になるまでにかかる時間について、よく質問されることがあります。 この記事では、この待機時間に影響するさまざまな要因について説明します。

## <a name="typical-latency"></a>一般的な待機時間
待機時間は、データが監視対象のシステムで作成される時間と Azure Monitor で分析に使用できるようになる時間を指します。 ログ データを取り込むための一般的な待ち時間は **20 秒から 3 分** です。 ただし、特定のデータに対する具体的な待機時間は、次に説明するさまざまな要因によって異なります。


## <a name="factors-affecting-latency"></a>待機時間に影響する要因
特定のデータ セットの合計インジェスト時間は、次の高度な領域に分割することができます。 

- **エージェント時間**: イベントを検出し、それを収集して、ログ レコードとして Azure Monitor ログのインジェスト ポイントに送信する時間。 ほとんどの場合、このプロセスは、エージェントによって処理されます。 ネットワークによって、追加の待ち時間が発生する可能性があります。
- **パイプライン時間**: ログ レコードを処理するインジェスト パイプラインの時間。 これには、イベントのプロパティを解析し、場合によっては計算済みの情報を追加する時間が含まれます。
- **インデックス作成時間**: ログ レコードを Azure Monitor のビッグ データ ストアに取り込むために費やされた時間。

このプロセスによるさまざまな待機時間の詳細を以下に示します。

### <a name="agent-collection-latency"></a>エージェント コレクションの待機時間

**時間は変動**

エージェントと管理ソリューションは、さまざまな方法を使用して仮想マシンからデータを収集します。これが待機時間に影響する場合があります。 具体的な例をいくつか次に示します。

| データの種類  | 収集の頻度  | Notes |
|:--------------|:----------------------|:------|
| Windows イベント、syslog イベント、パフォーマンス メトリック | すぐに収集される| | 
| Linux パフォーマンス カウンター | 30 秒間隔でポーリングされる| |
| IIS ログとカスタム ログ | タイムスタンプが変更された後に収集される | IIS ログの場合、これは [IIS で構成されているロール オーバー スケジュール](../agents/data-sources-iis-logs.md)の影響を受けます。 |
| Active Directory Replication ソリューション | 5 日おきに評価される | エージェントは評価が完了したときにのみ、これらのログを収集します。|
| Active Directory Assessment ソリューション | Active Directory インフラストラクチャが週 1 回評価される | エージェントは評価が完了したときにのみ、これらのログを収集します。|

### <a name="agent-upload-frequency"></a>エージェントのアップロードの頻度

**1 分未満**

Log Analytics エージェントを軽量にするため、エージェントではログがバッファーに格納されて、定期的に Azure Monitor にアップロードされます。 アップロードの頻度は、データの型に応じて 30 秒から 2 分間の範囲で異なります。 ほとんどのデータは、1 分未満でアップロードされます。 

### <a name="network"></a>ネットワーク

**場合により異なる**

ネットワークの状態は、このデータが Azure Monitor ログのインジェスト ポイントに達するまでの待機時間に悪影響を及ぼす可能性があります。

### <a name="azure-metrics-resource-logs-activity-log"></a>Azure メトリック、リソース ログ、アクティビティ ログ

**30 秒から 15 分**

Azure データが Azure Monitor ログのインジェスト ポイントで使用可能になるまでには、処理のための追加の時間がかかります。

- **Azure プラットフォームのメトリック** は、1 分後にメトリック データベースで使用可能になりますが、Azure Monitor ログのインジェスト ポイントにエクスポートできるようになるまでには、さらに 3 分かかります。
- 通常、**リソース ログ** には、Azure サービスに応じて、さらに 30 秒から 90 秒かかります。 一部の Azure サービス (具体的には、Azure SQL Database と Azure Virtual Network) では、現在 5 分間隔でそれらのログが報告されます。 これをさらに向上させるための取り組みが進行中です。 ご利用の環境におけるこの待機時間を調べる方法については、[下のクエリ](#checking-ingestion-time)を参照してください。
- **アクティビティ ログ** データは、推奨されるサブスクリプションレベルの診断設定を使用して Azure Monitor ログに送信する場合、30 秒後に取り込まれます。 ただし、従来の統合を使用している場合は、10 分から 15 分かかる場合があります。  

### <a name="management-solutions-collection"></a>管理ソリューションのコレクション

**場合により異なる**

一部のソリューションでは、エージェントからデータを収集せず、さらに待機時間がかかるコレクション メソッドを使用する場合があります。 一部のソリューションでは、ほぼリアルタイムでの収集を試みることなく、一定の間隔でデータを収集します。 具体的な例を次に示します。

- Microsoft 365 ソリューションを使用すると、Management Activity API (現在はほぼリアルタイムの待機時間を保証していません) を使用して、アクティビティ ログがポーリングされます。
- Windows Analytics ソリューション (Update Compliance など) のデータは、毎日の頻度でソリューションによって収集されます。

各ソリューションのコレクションの頻度を判断するには、[そのドキュメント](../insights/solutions.md)を参照してください。

### <a name="pipeline-process-time"></a>パイプライン処理時間

**30 秒から 60 秒**

インジェスト ポイントで使用可能になったデータがクエリに使用できるようになるまでには、さらに 30 秒から 60 秒かかります。

([_TimeReceived](./log-standard-columns.md#_timereceived) プロパティに指定されているように) ログ レコードは Azure Monitor パイプラインに取り込まれると、テナントが分離され、データが失われないように、一時的なストレージに書き込まれます。 通常、このプロセスにより、さらに 5 - 15 秒が加算されます。 一部の管理ソリューションでは、データのストリーミング時に、データを集計し、分析情報を派生させるため、より重いアルゴリズムが実装されます。 たとえば、ネットワーク パフォーマンス監視は、3 分間隔で受信データを集計するため、事実上 3 分間の待機時間が加算されます。 待機時間を増やす別のプロセスは、カスタム ログを処理するプロセスです。 場合によっては、このプロセスによって、エージェントがファイルから収集するログの待機時間が数分増えることがあります。

### <a name="new-custom-data-types-provisioning"></a>新しいカスタム データ型のプロビジョニング

新しいカスタム データの型を[カスタム ログ](../agents/data-sources-custom-logs.md)または[データ コレクター API](../logs/data-collector-api.md) から作成すると、システムにより専用のストレージ コンテナーが作成されます。 これは、最初にこのデータ型が出現したときにのみ発生する 1 回限りのオーバーヘッドです。

### <a name="surge-protection"></a>急激な増加の保護

**通常は 1 分未満 (ただし、それ以上かかる場合もあります)** 

Azure Monitor の最優先事項は、顧客データが失われることがないようにすることです。そのため、システムにはデータの急激な増加に対する保護が組み込まれています。 これには、膨大な負荷でもシステムが機能し続けられるようにするバッファーが含まれます。 通常の負荷では、これらのコントロールにより追加される待機時間は 1 分未満ですが、極端な状態で障害が発生した場合、データの安全を確保する一方で、膨大な時間が追加される場合があります。

### <a name="indexing-time"></a>インデックス作成時間

**5 分以下**

すべてのビッグ データ プラットフォームには、分析および高度な検索機能の提供と、データへの即時アクセスの提供の間のバランスを取る機能が、組み込まれています。 Azure Monitor を使用すると、何十億ものレコードに対して強力なクエリを実行し、数秒以内で結果を取得できます。 これを可能にしているのは、インフラストラクチャがデータ インジェスト中にデータを動的に変換して、それを一意のコンパクトな構造に格納しているからです。 システムは、これらの構造を作成するのに十分なデータ量になるまで、データをバッファーします。 これは、ログ レコードが検索結果に表示される前に完了する必要があります。

このプロセスは現在、データ量が少ない場合は約 5 分かかりますが、データ レートが高くなるほど時間は短くなります。 これは直感に反しているように思えますが、このプロセスにより、大量の運用環境のワークロードの待機時間の最適化が可能になります。

## <a name="checking-ingestion-time"></a>インジェスト時間のチェック
インジェスト時間は、さまざまな状況とリソースの種類によって変わる場合があります。 ログ クエリを使用すると、ご利用の環境の具体的な動作を把握することができます。 次の表は、レコードが作成され、Azure Monitor に送信されるときにそのレコードのさまざまな時間を判断する方法を示しています。

| 手順 | プロパティまたは関数 | 説明 |
|:---|:---|:---|
| データ ソースで作成されるレコード | [TimeGenerated](./log-standard-columns.md#timegenerated) <br>データ ソースがこの値を設定しない場合は、_TimeReceived と同じ時間に設定されます。 |
| Azure Monitor のインジェスト エンドポイントによって受信されるレコード | [_TimeReceived](./log-standard-columns.md#_timereceived) | このフィールドは、大量の処理向けに最適化されていないため、大きなデータセットをフィルター処理するためには使用しないでください。 |
| ワークスペースに保存され、クエリに使用できるレコード | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | 特定の時間枠で取り込まれたレコードのみをフィルター処理する必要がある場合は、ingestion_time () を使用することをお勧めします。 このような場合は、より大きな範囲を使用する TimeGenerated フィルターを追加することもお勧めします。 |

### <a name="ingestion-latency-delays"></a>インジェストの待ち時間
特定のレコードの待ち時間は、[ingestion_time()](/azure/kusto/query/ingestiontimefunction) 関数の結果を _TimeGenerated_ プロパティと比較することによって測定することができます。 このデータを、さまざまな集計と組み合わせて使用すれば、取り込みの待ち時間の動作を突き止めることができます。 インジェスト時間のパーセンタイルを観察すれば、大量のデータの分析情報が得られます。 

たとえば、次のクエリを実行すると、過去 8 時間でインジェスト時間が最も長かったコンピューターを表示できます。 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

前出のパーセンタイル チェックは、待ち時間の一般的な傾向を見つけるのに適しています。 待ち時間の短期間のスパイクを識別するのであれば、最大値 (`max()`) を使用した方が効果的である場合があります。

一定の期間にわたる特定のコンピューターのインジェスト時間をドリルダウンする場合は、次のクエリを使用すると、昨日のデータもグラフに視覚化されます。 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
コンピューターのインジェスト時間を、それが置かれている (対応する IP アドレスに基づく) 国や地域ごとに表示するには、次のクエリを使用します。 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
エージェントから送信されるデータの種類が異なれば、インジェストの待ち時間も異なる可能性があるので、前出のクエリを他の種類のデータにも使用してみましょう。 各種 Azure サービスのインジェスト時間を観察するには、次のクエリを使用します。 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>応答していないリソース 
ときには、リソースからのデータ送信が停止することもあります。 リソースからデータが送信されているかどうかを把握するには、標準の _TimeGenerated_ フィールドで識別できる最新のレコードに着目します。  

エージェントからは、1 分間に 1 回ハートビートが送信されます。そこで、_Heartbeat_ テーブルを使用して VM の状態をチェックします。 アクティブなコンピューターのうち、ハートビートを最近レポートしていないコンピューターを一覧表示するには、次のクエリを使用します。 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>次のステップ
* Azure Monitor の[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/monitor/v1_3/) をお読みください。