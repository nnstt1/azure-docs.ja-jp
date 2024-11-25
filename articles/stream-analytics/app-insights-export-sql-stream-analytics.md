---
title: Azure Application Insights から SQL へのエクスポート | Microsoft Docs
description: Stream Analytics を使用して Application Insights データを SQL へ継続的にエクスポートします。
ms.topic: conceptual
ms.date: 09/11/2017
ms.service: stream-analytics
ms.openlocfilehash: bcf3bc5995a6d9c8c37ab92e303b7d39b0b132fe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594501"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>チュートリアル:Stream Analytics を使用した Application Insights から SQL へのエクスポート
この記事では、[連続エクスポート][export]と [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) を使用して、テレメトリ データを [Azure Application Insights][start] から Azure SQL Database に移動する方法について説明します。 

連続エクスポートにより、JSON 形式でテレメトリ データが Azure Storage に移動されます。 Azure Stream Analytics を使って JSON オブジェクトを解析し、データベース テーブルに行を作成します。

(一般的に、連続エクスポートは、アプリが Application Insights に送信するテレメトリの独自の分析を行うための手段です。 データの集合など、エクスポートされたテレメトリで他の処理を行うように、このコード サンプルを適合させることもできます。)

監視対象のアプリが存在していることを想定して説明を始めます。

この例では、ページ ビュー データを使用しますが、同じパターンを簡単に他のデータ型 (カスタム イベントや例外など) に拡張できます。 

> [!IMPORTANT]
> 連続エクスポートは非推奨であり、従来の Application Insights リソースに対してのみサポートされます。 テレメトリをエクスポートするために[診断設定](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export)を使用するには、[ワークスペースベースの Application Insights リソースに移行](../azure-monitor/app/convert-classic-resource.md)します。

## <a name="add-application-insights-to-your-application"></a>Application Insights をアプリケーションに追加する 
作業を開始するには:

1. [Web ページに Application Insights を設定する](../azure-monitor/app/javascript.md) 
   
    (この例では、クライアント ブラウザーからのページ ビュー データの処理を取り上げますが、サーバー側の [Java](../azure-monitor/app/java-in-process-agent.md) または [ASP.NET](../azure-monitor/app/asp-net.md) アプリに Application Insights を設定し、要求、依存関係、その他のサーバー テレメトリを処理することもできます。)
2. アプリを発行して Application Insights のリソースに表示されるテレメトリ データを確認します。

## <a name="create-storage-in-azure"></a>Azure でのストレージの作成
連続エクスポートでは、常に Azure のストレージ アカウントにデータが出力されるため、まずストレージを作成する必要があります。

1. [Azure ポータル][portal]で、サブスクリプションのストレージ アカウントを作成します。
   
    ![[新規]、[データ + ストレージ] の順に選択し、[クラシック]、[作成] を選択して、ストレージ名を入力した、Azure portal のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/040-store.png)
2. コンテナーを作成する
   
    ![[コンテナー]、[コンテナー] タイル、[追加] の順に選択した、新しいストレージのスクリーンショット。](./media/app-insights-export-sql-stream-analytics/050-container.png)
3. ストレージ アクセス キーのコピー
   
    これは、ストリーム分析サービスへの入力のセットアップのためにすぐに必要になります。
   
    ![ [設定]、[キー] の順に開き、プライマリ アクセス キーのコピーを取得した、ストレージのスクリーンショット。](./media/app-insights-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure Storage への連続エクスポートの開始
1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。
   
    ![[参照]、[Application Insights]、お使いのアプリケーションの順に選択した、Azure portal のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/060-browse.png)
2. 連続エクスポートを作成します。
   
    ![[設定]、[連続エクスポート]、[追加] の順に選択した、Application Insights リソースのスクリーンショット。](./media/app-insights-export-sql-stream-analytics/070-export.png)

    以前に作成したストレージ アカウントを選択します。

    ![[追加] を選択した、[連続エクスポート] のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/080-add.png)

    表示するイベントの種類を設定します。

    ![イベントの種類を選択した、[連続エクスポートの追加] のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/085-types.png)


1. データを蓄積します。 しばらく待機し、ユーザーにアプリケーションを使用してもらいます。 テレメトリが開始し、統計グラフが[メトリックス エクスプローラー](../azure-monitor/essentials/metrics-charts.md)に表示され、個々のイベントが[診断検索](../azure-monitor/app/diagnostic-search.md)に表示されます。 
   
    また、データはストレージにもエクスポートされます。 
2. エクスポートされたデータを、ポータルまたは Visual Studio で調べます。ポータルの場合は、 **[参照]** 、ストレージ アカウント、 **[コンテナー]** の順に選択します。 Visual Studio で、 **[表示]、[Cloud Explorer]** の順に選びます。[Azure]、[Storage] の順に開きます (このメニュー オプションがない場合は、Azure SDK をインストールする必要があります:[新しいプロジェクト] ダイアログを開き、[Visual C#]、[クラウド]、[Microsoft Azure SDK for .NET の取得] の順に開きます)。
   
    ![[サーバー ブラウザー]、[Azure]、[Storage] の順に開いた、Visual Studio のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/087-explorer.png)
   
    パス名の共通部分を書き留めます。共通部分はアプリケーションの名前とインストルメンテーション キーから派生します。 

イベントが JSON 形式で BLOB ファイルに書き込まれます。 各ファイルに 1 つ以上のイベントが含まれる場合があります。 このため、イベント データを読み取って必要なフィールドをフィルター処理します。 データの処理に関して行えることはありますが、今日の計画は、Stream Analytics を使用してデータを SQL Database に移動することです。 それにより、興味深い多くのクエリを実行しやすくなります。

## <a name="create-an-azure-sql-database"></a>Azure SQL Database の作成
再度、[Azure portal][portal] でサブスクリプションから始め、データを書き込むデータベース (および、まだ保有していない場合は新しいサーバー) を作成します。

![[作成]、[データ + ストレージ]、[SQL Database] の順に選択した、Azure portal のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/090-sql.png)

サーバーに Azure サービスがアクセス可能であることをご確認ください。

![Azure portal での SQL サーバーとファイアウォール設定のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Azure SQL Database にテーブルを作成する
任意の管理ツールを使って前のセクションで作成したデータベースに接続します。 このチュートリアルでは、 [SQL Server Management Tools](/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用します。

![Azure SQL Database への接続のスクリーンショット](./media/app-insights-export-sql-stream-analytics/31-sql-table.png)

新しいクエリを作成し、次の T-SQL を実行します。

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![SQL Server Management Studio での PageViewsTable の作成のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/34-create-table.png)

このサンプルでは、ページ ビューからデータを使用します。 使用可能なその他のデータを確認するには、JSON 出力を検査し、「 [Application Insights エクスポート データ モデル](../azure-monitor/app/export-data-model.md)」を参照してください。

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics インスタンスの作成
[Azure Portal](https://portal.azure.com/) で、Azure Stream Analytics サービスを選び、新しい Stream Analytics ジョブを作成します。

![スクリーンショットには、[作成] ボタンが強調表示された [Stream Analytics ジョブ] ページが示されています。](./media/app-insights-export-sql-stream-analytics/001.png)

![新しい Stream Analytics ジョブのスクリーンショット](./media/app-insights-export-sql-stream-analytics/002.png)

新しいジョブが作成されたら、 **[リソースに移動]** を選びます。

![スクリーンショットには、[デプロイメントに成功しました] のメッセージと [リソースに移動] ボタンが示されています。](./media/app-insights-export-sql-stream-analytics/003.png)

#### <a name="add-a-new-input"></a>新しい入力を追加する

![スクリーンショットには、[追加] ボタンが選択された [入力] ページが示されています。](./media/app-insights-export-sql-stream-analytics/004.png)

連続エクスポート BLOB から入力を取るよう設定します。

![スクリーンショットには、[入力のエイリアス]、[ソース]、[ストレージ アカウント] のドロップダウン メニュー オプションが選択された [新しい入力] ウィンドウが示されています。](./media/app-insights-export-sql-stream-analytics/0005.png)

ここで、ストレージ アカウントからのプライマリ アクセス キーが必要になります。これは前にメモしておいたものです。 ストレージ アカウント キーとしてこれを設定します。

#### <a name="set-path-prefix-pattern"></a>パスのプレフィックス パターンの設定

**日付の書式は YYYY-MM-DD (ダッシュ付き) に設定してください。**

パスのプレフィックス パターンは、Stream Analytics がストレージ内の入力ファイルを検索する方法を指定します。 連続エクスポートによるデータ格納方法と一致するように設定する必要があります。 次のように設定します。

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

次の点に注意してください。

* `webapplication27` は Application Insights リソースの名前です。**すべて小文字で指定します**。 
* `1234...` は Application Insights リソースのインストルメンテーション キーです。**ダッシュは削除します**。 
* `PageViews` は分析するデータの種類です。 使用可能な種類は、連続エクスポートで設定するフィルターによって異なります。 エクスポートされたデータを調べて、その他の使用可能な種類を確認します。「[Application Insights エクスポート データ モデル](../azure-monitor/app/export-data-model.md)」をご覧ください。
* `/{date}/{time}` はそのまま書き込まれるパターンです。

Application Insights リソースの名前と iKey を取得するには、概要ページの [Essentials] を開くか、[設定] を開きます。

> [!TIP]
> 入力パスが正しく設定されていることを確認するには、Sample 関数を使用します。 失敗した場合:選択したサンプルの時間範囲でストレージにデータがあることを確認します。 入力定義を編集し、ストレージ アカウント、パスのプレフィックス、日付形式が正しく設定されていることを確認します。

 
## <a name="set-query"></a>クエリの設定
クエリ セクションを開きます。

既定のクエリを次のもので置き換えます。

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

最初のいくつかのプロパティはページ ビュー データに固有のプロパティです。 他のテレメトリの種類のエクスポートにはそれぞれ異なるプロパティがあります。 [プロパティの種類と値についての詳細なデータ モデル リファレンス](../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>データベースへの出力のセットアップ
SQL を出力として選択します。

![[出力] が選択された、Stream Analytics のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/006.png)

データベースを指定します。

![データベースの詳細を含む新しい出力のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/007.png)

ウィザードを閉じ、出力がセットアップされたことを示す通知を待機します。

## <a name="start-processing"></a>処理の開始
ジョブを操作バーから開始します。

![[開始] が選択された、Stream Analytics のスクリーンショット。](./media/app-insights-export-sql-stream-analytics/008.png)

データの処理を今から開始するか、以前のデータを使って開始するかを選択できます。 連続エクスポートの実行を開始してから既に時間が経過している場合は、後者がお勧めです。

数分後、SQL Server Management Tools に戻り、流れているデータを監視します。 たとえば、次のようなクエリを使用します。

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="next-steps"></a>次のステップ
* [Stream Analytics を使用して Power BI にエクスポートする](../azure-monitor/app/export-power-bi.md)
* [プロパティの種類と値についての詳細なデータ モデル リファレンス](../azure-monitor/app/export-data-model.md)
* [Application Insights での連続エクスポート](../azure-monitor/app/export-telemetry.md)

<!--Link references-->

[diagnostic]: ../azure-monitor/app/diagnostic-search.md
[export]: ../azure-monitor/app/export-telemetry.md
[metrics]: ../azure-monitor/essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../azure-monitor/app/app-insights-overview.md

