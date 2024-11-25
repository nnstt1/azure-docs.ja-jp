---
title: Azure Virtual Desktop の用語集を監視する - Azure
description: Azure Monitor for Azure Virtual Desktop に関連する用語と概念に関する用語集。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 46e70166fffba7c938ea6784db6eda18e69a043f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214759"
---
# <a name="azure-monitor-for-azure-virtual-desktop-glossary"></a>Azure Monitor for Azure Virtual Desktop の用語集

この記事では、Azure Monitor for Azure Virtual Desktop (プレビュー) に関連する主な用語と概念を挙げ、簡単に説明します。

## <a name="alerts"></a>アラート

サブスクリプションで構成し、[重大度 0](#severity-0-alerts) として分類されているアクティブな Azure Monitor のアラートは、すべて概要ページに表示されます。 アラートの設定方法について詳しくは、[Azure Monitor のログ アラート](../azure-monitor/alerts/alerts-log.md)に関するページをご覧ください。

## <a name="available-sessions"></a>使用可能なセッション

使用可能なセッションには、ホスト プールで使用可能なセッション数が表示されます。 この数字は、サービスが仮想マシン (VM) の数と、仮想マシンごとに許可されるセッションの最大数を乗算し、合計セッション数を減算することによって計算されます。

## <a name="connection-success"></a>接続の成功

この項目は接続の正常性を示します。 "接続の成功" とは、接続がホストに到着し、その仮想マシンのスタックによって確認されたことを意味します。 接続の失敗は、接続がホストに到着しなかったことを意味します。

## <a name="daily-active-users-dau"></a>日次アクティブ ユーザー数 (DAU)

過去 24 時間にセッションを開始したユーザーの合計数。

## <a name="daily-alerts"></a>毎日のアラート数

毎日トリガーされるアラートの総数。

## <a name="daily-connections-and-reconnections"></a>毎日の接続数と再接続数

過去 24 時間以内に開始または完了した接続と再接続の合計数。

## <a name="daily-connected-hours"></a>毎日の接続時間

過去 24 時間にユーザー間のセッションへの接続に費やした時間の合計数。

## <a name="diagnostics-and-errors"></a>診断とエラー

Azure Monitor for Azure Virtual Desktop にエラーまたはアラートが表示されるときは、次の 3 つの項目に分類されます。

- アクティビティの種類: このカテゴリは、Azure Virtual Desktop 診断によるエラーの分類を示します。 カテゴリとは、管理アクティビティ、フィード、接続、ホスト登録、エラー、およびチェックポイントです。 これらのカテゴリの詳細については、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics.md)」を参照してください。

- 種類: このカテゴリには、エラーの場所が表示されます。 

     - "service" または "ServiceError = TRUE" とマークされたエラーは、Azure Virtual Desktop サービスで発生しています。
     - "deployment" とマークされた、または "ServiceError = FALSE" のタグが付いたエラーは Azure Virtual Desktop サービスの外部で発生しています。
     - ServiceError タグの詳細については、「[一般的なエラーシナリオ](./troubleshoot-set-up-overview.md)」を参照してください。

- ソース: このカテゴリは、エラーが発生した場所について、より具体的な説明を示します。

     - 診断: ユーザーがデプロイのイシューを観察して診断できるように、サービス アクティビティの監視とレポートを担当するサービス ロール。

     - RDBroker: デプロイ アクティビティの調整、オブジェクトの状態の保持、認証の検証などを担当するサービス ロール。

     - RDGateway: エンドユーザーと仮想マシン間のネットワーク接続の処理を担当するサービス ロール。

     - RDStack: VM にインストールされていて、Azure Virtual Desktop サービスと通信できるようにするソフトウェア コンポーネント。

     - クライアント: エンドユーザーのコンピューターで実行されていて、Azure Virtual Desktop サービスへのインターフェイスを提供するソフトウェア。 選択すると、公開されたリソースの一覧を表示し、リモート デスクトップ接続をホストします。

それぞれの診断の問題またはエラーには、どのような問題が発生したかを説明するメッセージが含まれます。 エラーのトラブルシューティングの詳細については、「[Azure Virtual Desktop のイシューの特定と診断](./troubleshoot-set-up-overview.md)」を参照してください。

## <a name="input-delay"></a>入力遅延

Azure Monitor for Azure Virtual Desktop の "入力遅延" は、各セッションのプロセス パフォーマンス カウンターごとの入力遅延を意味します。 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) のホスト パフォーマンス ページでは、このパフォーマンス カウンターが、30 秒ごとにレポートをサービスに送信するように構成されています。 この 30 秒の間隔は "サンプル" と呼ばれ、その期間で最悪のケースを報告します。 中央値と p95 の値には、すべてのサンプルにおける中央値と 95 パーセンタイルが反映されます。

**[ホスト別の入力遅延]** で、そのホストに対してページ内の他のすべてのビジュアルをフィルター処理するセッション ホスト行を選択することができます。 また、プロセス名を選択して、時間グラフの中央値の入力遅延をフィルター処理することもできます。

遅延は以下のカテゴリに分類されます。

- 良い: 150 ミリ秒未満。
- 許容範囲内: 150-500 ミリ秒。
- Poor (低い): 500-2,000 ミリ秒 (2 秒未満)。
- Bad (悪い): 2,000 ミリ秒を超える (2 秒以上)。

入力遅延カウンターのしくみの詳細については、[ユーザー入力遅延のパフォーマンス カウンター](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)に関する記事を参照してください。

##  <a name="monthly-active-users-mau"></a>月間アクティブ ユーザー (MAU)

過去 28 日間にセッションを開始したユーザーの合計数。 データを 30 日以内に格納している場合、データの使用可能な期間が 28 日未満になると、MAU と接続値が期待を下回る場合があります。

## <a name="performance-counters"></a>パフォーマンス カウンター

パフォーマンス カウンターには、ハードウェア コンポーネント、オペレーティング システム、およびアプリケーションのパフォーマンスが表示されます。

次の表に、Azure Monitor が Azure Virtual Desktop で使用する、推奨されるパフォーマンス カウンターと期間を示します。

|パフォーマンス カウンター名|間隔|
|---|---|
|Logical Disk(C:)\\Avg.ディスク キューの長さ|30 秒|
|Logical Disk(C:)\\Avg.Disk sec/Transfer|60 秒|
|Logical Disk(C:)\\Current Disk Queue Length|30 秒|
|Memory(\*)\\Available Mbytes|30 秒|
|Memory(\*)\\Page Faults/sec|30 秒|
|Memory(\*)\\Pages/sec|30 秒|
|Memory(\*)\\% Committed Bytes in Use|30 秒|
|PhysicalDisk(\*)\\Avg.ディスク キューの長さ|30 秒|
|PhysicalDisk(\*)\\Avg.Disk sec/Read|30 秒|
|PhysicalDisk(\*)\\Avg.Disk sec/Transfer|30 秒|
|PhysicalDisk(\*)\\Avg.Disk sec/Write|30 秒|
|Processor Information(_Total)\\% Processor Time|30 秒|
|Terminal Services(\*)\\Active Sessions|60 秒|
|Terminal Services(\*)\\Inactive Sessions|60 秒|
|Terminal Services(\*)\\Total Sessions|60 秒|
|\*User Input Delay per Process(\*)\\Max Input Delay|30 秒|
|\*User Input Delay per Session(\*)\\Max Input Delay|30 秒|
|RemoteFX Network(\*)\\Current TCP RTT|30 秒|
|RemoteFX Network(\*)\\Current UDP Bandwidth|30 秒|

パフォーマンス カウンターの読み取り方法の詳細については、「[パフォーマンスカウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md)」を参照してください。

入力遅延パフォーマンス カウンターの詳細については、[ユーザー入力遅延のパフォーマンス カウンター](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)に関する記事を参照してください。

## <a name="potential-connectivity-issues"></a>潜在的な接続のイシュー

潜在的な接続のイシューには、ホスト、ユーザー、公開されたリソース、接続エラー率が高いクライアントが表示されます。 "レポート基準" フィルターを選択すると、次の列の値をチェックして、問題の重大度を評価できます。

- 試行回数 (接続試行の回数)
- リソース (公開されたアプリまたはデスクトップの数)
- ホスト (VM の数)
- クライアント

たとえば、 **[ユーザー別]** フィルターを選択した場合は、 **[試行回数]** 列で各ユーザーの接続試行回数を確認できます。

接続のイシューが複数のホスト、ユーザー、リソース、またはクライアントにまたがっている場合は、そのイシューがシステム全体に影響する可能性があります。 そうでない場合は、低優先度の小規模なイシューになります。

また、エントリを選択して追加情報を表示することもできます。 イシューに関連するホスト、リソース、およびクライアントのバージョンを確認できます。 この表示には、接続試行中に報告されたエラーも表示されます。

## <a name="round-trip-time-rtt"></a>ラウンドトリップ時間 (RTT)

ラウンドトリップ時間 (RTT) は、エンドユーザーの場所とセッション ホストの Azure リージョンとの間の接続のラウンドトリップ時間の推定値です。 待機時間が最も短い場所を確認するには、[Azure Virtual Desktop エクスペリエンス見積もりツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)で目的の場所を検索してください。

## <a name="session-history"></a>セッションの履歴

**[セッション]** 項目には、すべてのセッションの状態と、接続されているか、接続解除されているかが表示されます。 **[アイドル セッション]** には、接続解除されたセッションのみが表示されます。

## <a name="severity-0-alerts"></a>重大度 0 のアラート

すぐに対処する必要がある最も緊急度の高い項目。 このような問題に対処しないと、Azure Virtual Desktop のデプロイが動作しなくなる可能性があります。

## <a name="time-to-connect"></a>接続までの時間

接続までの時間は、ユーザーがセッションを開始してから、サービスへのサインインとしてカウントされるまでの時間です。 新しい接続の確立は、既存の接続の再確立よりも時間がかかる傾向があります。

## <a name="user-report"></a>ユーザー レポート

ユーザー レポートのページでは、特定のユーザーの接続履歴と診断情報を表示できます。 各ユーザー レポートには、使用パターン、ユーザー フィードバック、およびセッション中に発生したすべてのエラーが表示されます。 ほとんどの小さなイシューは、ユーザーからのフィードバックによって解決できます。 さらに詳しく調べる必要がある場合は、特定の接続 ID または期間で情報をフィルター処理することもできます。

## <a name="users-per-core"></a>コアあたりのユーザー数

これは、各仮想マシン コアのユーザー数です。 時間の経過と共にコアあたりの最大ユーザー数を追跡することで、コアごとの高い、低い、または変化するユーザー数で環境が一貫して実行されるかを判断できます。 アクティブになっているユーザーの数を把握することで、環境を効率的に準備し、スケーリングすることができます。

## <a name="windows-event-logs"></a>Windows イベント ログ

Windows イベント ログは、Windows 仮想マシン上の Log Analytics エージェントによって収集されるデータ ソースです。 システムやアプリケーションなどの標準ログのイベントに加えて、監視が必要なアプリケーションによって作成されるカスタム ログも収集できます。

次の表に、Azure Monitor for Azure Virtual Desktop に必要な Windows イベント ログを示します。

|イベント名|イベントの種類|
|---|---|
|Application|エラーと警告|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin|エラー、警告、情報|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|エラー、警告、情報|
|システム|エラーと警告|
| Microsoft-FSLogix-Apps/Operational|エラー、警告、情報|
|Microsoft-FSLogix-Apps/Admin|エラー、警告、情報|

Windows イベント ログの詳細については、[Windows イベント レコードのプロパティ](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)をご覧ください。

## <a name="next-steps"></a>次の手順

- 開始するには、「[Azure Monitor for Azure Virtual Desktop を使用してデプロイを監視する](azure-monitor.md)」を参照してください。
- データ ストレージのコストを見積もり、測定、管理には、[Azure Monitor コストを見積もる](azure-monitor-costs.md)をご覧ください。
- 問題が発生した場合のヘルプや既知の問題については、[トラブルシューティング ガイド](troubleshoot-azure-monitor.md)をご覧ください。


また、Azure Advisor を設定して、一般的なイシューを解決または回避する方法を判断することもできます。 詳細については、「[Azure Virtual Desktop で Azure Advisor を使用する](azure-advisor.md)」を参照してください。

ヘルプが必要な場合、または質問がある場合は、コミュニティ リソースをご確認ください。

- [Azure Virtual Desktop の TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) で、コミュニティに質問したり提案したりすることができます。
   
- フィードバックを残す方法については、「[Azure Virtual Desktop のトラブルシューティングの概要、フィードバック、およびサポート](troubleshoot-set-up-overview.md#report-issues)」を参照してください。

- また、Azure Virtual Desktop についてのフィードバックは、[Azure Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます
