---
title: Azure Cosmos DB、Azure Analysis Services、および Power BI を使用してリアルタイム ダッシュボードを作成する
description: Azure Cosmos DB と Azure Analysis Services を使用して Power BI でライブ天気ダッシュボードを作成する方法について説明します。
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: a2a9096ac6f8762500d1060dbab7277b2dafe9dd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113434"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Azure Cosmos DB と Power BI を使用してリアルタイム ダッシュボードを作成する
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB OLTP コネクタと Azure Analysis Services を使用して Power BI でライブ天気ダッシュボードを作成するために必要な手順について説明します。 Power BI ダッシュボードには、地域の気温と降水量に関するほぼリアルタイムの情報を示すグラフが表示されます。

もう 1 つの方法として、[Azure Synapse Link for Azure Cosmos DB](../synapse-link.md) で準リアルタイムのレポートを作成できます。 Azure Synapse Link を使用すれば、Power BI を接続して Azure Cosmos DB のデータを分析できます。トランザクションのワークロードに対するパフォーマンスとコストに影響はなく、ETL パイプラインも必要ありません。 [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) または [インポート](/power-bi/connect-data/service-dataset-modes-understand#import-mode) モードを使用できます。 詳細については、[こちら](../synapse-link-power-bi.md)をクリックしてください。


## <a name="reporting-scenarios"></a>レポートのシナリオ

Azure Cosmos DB に格納されているデータに対してレポート ダッシュボードを設定するには、複数の方法があります。 整合性制約の要件とデータのサイズに応じて、次の表に各シナリオのレポート設定を示します。


|シナリオ |セットアップ |
|---------|---------|
|1. 集計を使用して大規模なデータセットに関するリアルタイムのレポートを生成する     | **オプション 1:** [Power BI、Azure Synapse Link と DirectQuery](../synapse-link-power-bi.md)<br />  **オプション 2:** [Power BI、Spark コネクタと DirectQuery + Azure Databricks + Azure Cosmos DB Spark コネクタ。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)<br />  **オプション 3:** Power BI、Azure Analysis Services コネクタと DirectQuery + Azure Analysis Services + Azure Databricks + Cosmos DB Spark コネクタ。     |
|2. 大規模なデータセット (10 GB 以上) に関するリアルタイムのレポートを生成する    |  **オプション 1:** [Power BI、Azure Synapse Link と DirectQuery](../synapse-link-power-bi.md)<br />  **オプション 2:** [Power BI、Azure Analysis Services コネクタと DirectQuery + Azure Analysis Services](create-real-time-weather-dashboard-powerbi.md)       |
|3. 大規模なデータセット (10 GB 未満) に関するリアルタイムのレポートを生成する     |  [Power BI Azure Cosmos DB コネクタ、インポート モードと増分更新](create-real-time-weather-dashboard-powerbi.md)       |
|4. 定期更新があるアドホック レポートを生成する   |  [Power BI Azure Cosmos DB コネクタとインポート モード (スケジュールされた定期更新)](powerbi-visualize.md)       |
|5. アドホック レポート (更新なし) を生成する    |  [Power BI Azure Cosmos DB コネクタとインポート モード](powerbi-visualize.md)       |


シナリオ 4 と 5 は、[Azure Cosmos DB Power BI コネクタを使用](powerbi-visualize.md)して簡単に設定できます。 この記事では、シナリオ 2 (オプション 2) と 3 の設定について説明します。

### <a name="power-bi-with-incremental-refresh"></a>増分更新がある Power BI

Power BI には、増分更新を構成できるモードがあります。 このモードにより、Azure Analysis Services のパーティションを作成および管理する必要がなくなります。 増分更新は、大規模なデータセットの最新の更新のみをフィルター処理するように設定できます。 ただし、このモードは、10 GB のデータセット制限がある Power BI Premium サービスでのみ機能します。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis コネクタ + Azure Analysis Services

Azure Analysis Services は、エンタープライズ レベルのデータ モデルをクラウドでホストする、完全に管理されたサービスとしてのプラットフォームを提供します。 大量のデータセットを Azure Cosmos DB から Azure Analysis Services に読み込むことができます。 常にデータセット全体に対してクエリを実行することを避けるために、データセットを Azure Analysis Services の複数のパーティションに分割して、異なる頻度で個別に更新することができます。

## <a name="power-bi-incremental-refresh"></a>Power BI 増分更新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure Cosmos DB に気象データを取り込む

[気象データ](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation)を Azure Cosmos DB に読み込むためのインジェスト パイプラインを設定します。 HTTP ソースと Cosmos DB シンクを使用して最新の気象データを Azure Cosmos DB に定期的に読み込むように [Azure Data Factory (ADF)](../../data-factory/connector-azure-cosmos-db.md) ジョブを設定できます。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Power BI を Azure Cosmos DB に接続する

1. **Azure Cosmos アカウントを Power BI に接続する** - Power BI Desktop を開き、Azure Cosmos DB コネクタを使用して適切なデータベースとコンテナーを選択します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI コネクタ":::

1. **増分更新を構成する** - [Power BI での増分更新](/power-bi/service-premium-incremental-refresh)に関する記事の手順に従って、データセットの増分更新を構成します。 次のスクリーンショットに示すように、**RangeStart** パラメーターと **RangeEnd** パラメーターを追加します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="範囲のパラメーターを構成する":::

   データセットにはテキスト形式の日付列があるため、次のフィルターを使用するには **RangeStart** パラメーターと **RangeEnd** パラメーターを変換する必要があります。 **[詳細エディター]** ウィンドウで、クエリを変更し、次のテキストを追加して、RangeStart パラメーターと RangeEnd パラメーターに基づいて行をフィルター処理するようにします。

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   ソース データセットに存在する列とデータ型に応じて、RangeStart フィールドと Rangestart フィールドを適宜変更できます。

   
   |プロパティ  |データ型  |Assert  |
   |---------|---------|---------|
   |_ts        |   数値      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) と [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Date (例:- 2019-08-19)     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") と [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Date (例:- 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") と [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **更新ポリシーを定義する** - テーブルの **コンテキスト** メニューの **[増分更新]** タブに移動して、更新ポリシーを定義します。 **毎日** 更新され、前月のデータが格納されるように更新ポリシーを設定します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="更新ポリシーを定義する":::

   *M クエリが折りたたまれていることを確認できない* という警告は無視してください。 Azure Cosmos DB コネクタは、フィルター クエリを折りたたみます。

1. **データを読み込んでレポートを生成する** - 前に読み込んだデータを使用して、気温と降水量に関するレポートを作成するためのグラフを作成します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="データを読み込んでレポートを生成する":::

1. **Power BI Premium にレポートを発行する** - 増分更新は Premium のみの機能であるため、[発行] ダイアログでは、Premium 容量におけるワークスペースの選択のみが可能です。 最初の更新は、履歴データをインポートするために時間がかかる場合があります。 その後のデータ更新は増分更新を使用するため、はるかに高速になります。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis コネクタ + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure Cosmos DB に気象データを取り込む 

[気象データ](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation)を Azure Cosmos DB に読み込むためのインジェスト パイプラインを設定します。 HTTP ソースと Cosmos DB シンクを使用して最新の気象データを Azure Cosmos DB に定期的に読み込むように Azure Data Factory (ADF) ジョブを設定できます。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Analysis Services を Azure Cosmos アカウントに接続する

1. **新しい Azure Analysis Services クラスターを作成する** - Azure Cosmos アカウントおよび Databricks クラスターと同じリージョンに [Azure Analysis Services のインスタンスを作成します](../../analysis-services/analysis-services-create-server.md)。

1. **Visual Studio で新しい Analysis Services 表形式プロジェクトを作成する** -  [SQL Server Data Tools (SSDT) をインストール](/sql/ssdt/download-sql-server-data-tools-ssdt)し、Visual Studio で Analysis Services 表形式プロジェクトを作成します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Azure Analysis Services プロジェクトを作成する":::

   **統合ワークスペース** インスタンスを選択し、互換性レベルを **SQL Server 2017 / Azure Analysis Services (1400)** に設定します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Analysis Services 表形式モデル デザイナー":::

1. **Azure Cosmos DB データ ソースを追加する** - **[モデル]** >  **[データ ソース]**  >  **[新しいデータ ソース]** に移動し、次のスクリーンショットに示すように Azure Cosmos DB データ ソースを追加します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Cosmos DB データ ソースを追加する":::

   **アカウント URI**、**データベース名**、および **コンテナー名** を指定して、Azure Cosmos DB に接続します。 Azure Cosmos コンテナーからのデータが Power BI にインポートされていることを確認できます。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Azure Cosmos DB データをプレビューする":::

1. **Analysis Services モデルを構築する** - クエリ エディターを開いて、読み込まれたデータセットを最適化するために必要な操作を実行します。

   * 気象に関連した列 (気温と降水量) だけを抽出する

   * テーブルから月の情報を抽出する。 このデータは、次のセクションで説明するパーティションの作成に役立ちます。

   * 気温列を数値に変換する

   結果の M 式は次のようになります。

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   また、気温列のデータ型を10 進数に変更して、これらの値を Power BI にプロットできるようにします。

1. **Azure Analysis のパーティションを作成する** - Azure Analysis Services でパーティションを作成して、データセットを、個別に、異なる頻度で更新できる論理パーティションに分割します。 この例では、データセットを最新月のデータとその他すべてに分割する 2 つのパーティションを作成します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Analysis Services のパーティションを作成する":::

   Azure Analysis Services で次の 2 つのパーティションを作成します。

   * **最新月** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **履歴** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Azure Analysis Server にモデルをデプロイする** - Azure Analysis Services プロジェクトを右クリックし、 **[デプロイ]** を選択します。 **[Deployment Server properties]\(デプロイ サーバーのプロパティ\)** ウィンドウで、サーバー名を追加します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Azure Analysis Services モデルをデプロイする":::

1. **パーティションの更新とマージを構成する** - Azure Analysis Services では、パーティションを個別に処理することができます。 **最新月** のパーティションが最新データで常に更新されるようにするため、更新間隔を 5 分に設定します。 データを更新するには、[REST API](../../analysis-services/analysis-services-async-refresh.md)、[Azure Automation](../../analysis-services/analysis-services-refresh-azure-automation.md)、または[ロジック アプリ](../../analysis-services/analysis-services-refresh-logic-app.md)を使用します。 履歴パーティションのデータを更新する必要はありません。 さらに、最新月パーティションを履歴パーティションに統合し、新しい最新月パーティションを作成するためのコードを記述する必要があります。

## <a name="connect-power-bi-to-analysis-services"></a>Power BI を Analysis Services に接続する

1. **Azure Analysis Services データベース コネクタを使用して Azure Analysis Server に接続する** - **ライブ モード** を選択し、次のスクリーンショットに示すように Azure Analysis Services インスタンスに接続します。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Azure Analysis Services からデータを取得する":::

1. **データを読み込んでレポートを生成する** - 前に読み込んだデータを使用して、気温と降水量に関するレポートを作成するためのグラフを作成します。 ライブ接続を作成しているため、前の手順でデプロイした Azure Analysis Services モデルのデータに対してクエリを実行する必要があります。 気温グラフは、新しいデータが Azure Cosmos DB に読み込まれてから 5 分以内に更新されます。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="データを読み込んでレポートを生成する":::

## <a name="next-steps"></a>次のステップ

* Power BI の詳細については、「 [Power BI の概要](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)」を参照してください。

* [Qlik Sense を Azure Cosmos DB に接続してデータを可視化する](../visualize-qlik-sense.md)