---
title: 低速クエリ ログ - Azure Database for MySQL
description: Azure Database for MySQL で利用できる低速クエリ ログと、さまざまなログ記録レベルを有効にするため利用可能なパラメーターについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: d2ba1c751f2ed61ea583f1a1b6478bd0451bbea5
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651760"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL での低速クエリ ログ

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
Azure Database for MySQL では、ユーザーは低速クエリ ログを使用できます。 トランザクション ログへのアクセスはサポートされていません。 低速クエリ ログは、トラブルシューティングの目的でパフォーマンスのボトルネックを特定するために使用できます。

MySQL の低速クエリ ログの詳細については、MySQL のリファレンス マニュアルの[低速クエリ ログに関するセクション](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)を参照してください。

サーバーで[クエリ ストア](concepts-query-store.md)が有効になっている場合、低速クエリ ログに "`CALL mysql.az_procedure_collect_wait_stats (900, 30);`" のようなクエリが記録されている可能性があります。 この動作は、クエリ ストア機能によってクエリに関する統計情報を収集するために必要です。 

## <a name="configure-slow-query-logging"></a>低速クエリ ログを構成する 
既定では、低速クエリ ログは無効です。 有効にするには、`slow_query_log` を ON に設定します。 これは、Azure portal または Azure CLI を使用して有効にすることができます。 

調整できるその他のパラメーターは次のとおりです。

- **long_query_time**: long_query_time (秒単位) より長いクエリがある場合は、そのクエリが記録されます。 既定値は 10 秒です。
- **log_slow_admin_statements**: オンの場合は、slow_query_log に書き込まれるステートメントに、ALTER_TABLE や ANALYZE_TABLE などの管理ステートメントが含まれます。
- **log_queries_not_using_indexes**: インデックスを使用していないクエリを slow_query_log に記録するかどうかを決定します。
- **log_throttle_queries_not_using_indexes**:このパラメーターは、低速クエリ ログに書き込むことができる、インデックスを使用していないクエリの数を制限します。 このパラメーターは、Log_queries_not_using_indexes がオンに設定されている場合に有効です。
- **log_output**: "File" の場合、ローカル サーバー ストレージと Azure Monitor 診断ログの両方に低速クエリ ログの書き込みが許可されます。 "None" の場合、低速クエリ ログは Azure Monitor 診断ログのみに書き込まれます。 

> [!IMPORTANT]
> テーブルにインデックスが作成されていない場合は、`log_queries_not_using_indexes` と `log_throttle_queries_not_using_indexes` パラメーターを ON に設定すると、インデックスが設定されていないそれらのテーブルに対して実行されるすべてのクエリが低速クエリ ログに書き込まれるため、MySQL のパフォーマンスに影響する可能性があります。<br><br>
> 低速クエリのログ記録を長時間にわたって行う場合は、`log_output` を "None" に設定することをお勧めします。 "File" に設定すると、これらのログはローカル サーバー ストレージに書き込まれ、MySQL のパフォーマンスに影響を与える可能性があります。 

低速クエリ ログのパラメーターの完全な説明については、MySQL の[低速クエリ ログのドキュメント](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)を参照してください。

## <a name="access-slow-query-logs"></a>低速クエリ ログにアクセスする
Azure Database for MySQL の低速クエリ ログにアクセスするには、ローカル サーバー ストレージまたは Azure Monitor 診断ログの 2 つのオプションがあります。 これは `log_output` パラメーターを使用して設定します。

ローカル サーバー ストレージの場合は、Azure portal または Azure CLI を使用して、低速クエリ ログを一覧表示およびダウンロードできます。 Azure portal で、Azure portal 内のご使用のサーバーに移動します。 **[監視]** の見出しの下の、 **[サーバー ログ]** ページを選択します。 Azure CLI の詳細については、[Azure CLI を使用した低速クエリ ログの構成とアクセス](howto-configure-server-logs-in-cli.md)に関するページを参照してください。 

Azure Monitor 診断ログを使用すると、低速クエリ ログを Azure Monitor ログ (Log Analytics)、Azure Storage、または Event Hubs にパイプすることができます。 詳細については、[以下](concepts-server-logs.md#diagnostic-logs)を参照してください。

## <a name="local-server-storage-log-retention"></a>ローカル サーバー ストレージ ログの保持期間
サーバーのローカル ストレージにログを記録する場合、ログは作成から最大 7 日間利用できます。 使用可能なログの合計サイズが 7 GB を超える場合、空き領域を利用できるようになるまで古いファイルから削除されます。サーバー ログの 7 GB のストレージ上限は、コストなしで使用可能であり、拡張することはできません。 

ログのローテーションは、24 時間ごとか 7 GB ごとのどちらか早い方のタイミングで行われます。

> [!Note]
> 上記のログ保持期間は、Azure Monitor 診断ログを使用してパイプ処理されたログには適用されません。 出力先のデータ シンク (Azure Storage など) の保持期間を変更することが できます。

## <a name="diagnostic-logs"></a>診断ログ
Azure Database for MySQL は、Azure Monitor の診断ログと統合されます。 MySQL サーバーで低速クエリ ログを有効にしたら、Azure Monitor ログ、Event Hubs、または Azure Storage に対して、それらが出力されるように選択できます。 診断ログを有効にする方法の詳細については、[診断ログのドキュメント](../azure-monitor/essentials/platform-logs-overview.md)の操作方法のセクションを参照してください。

次の表は、各ログの内容を説明しています。 出力方法に応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | サーバーの名前 |
| `start_time_t` [UTC] | クエリの開始時刻 |
| `query_time_s` | クエリの実行にかかった合計時間 (秒) |
| `lock_time_s` | クエリのロックにかかった合計時間 (秒) |
| `user_host_s` | ユーザー名 |
| `rows_sent_s` | 送信された行の数 |
| `rows_examined_s` | 検査された行の数 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 挿入 ID |
| `sql_text_s` | クエリ全体 |
| `server_id_s` | サーバーの ID |
| `thread_id_s` | スレッド ID |
| `\_ResourceId` | リソース URI |

> [!Note]
> `sql_text` の場合、2048 文字を超えたログは切り捨てられます。

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor ログのログを分析する

低速クエリ ログが診断ログによって Azure Monitor ログにパイプされたら、低速クエリの詳細な分析を実行できます。 使用を開始する際に役立つサンプル クエリを以下にいくつか示します。 以下を、お使いのサーバー名で更新してください。

- 特定のサーバーで 10 秒を超えるクエリ

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 特定のサーバーの長いクエリの上位 5 つを一覧表示する

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 低速クエリを、特定のサーバーのクエリ時間の最小値、最大値、平均値、および標準偏差で要約する

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 特定のサーバーの低速クエリの分布をグラフ化する

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 診断ログが有効になっているすべての MySQL サーバーで 10 秒以上のクエリを表示する

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>次の手順
- [Azure portal から低速クエリ ログを構成する方法](howto-configure-server-logs-in-portal.md)
- [Azure CLI から低速クエリ ログを構成する方法](howto-configure-server-logs-in-cli.md)
