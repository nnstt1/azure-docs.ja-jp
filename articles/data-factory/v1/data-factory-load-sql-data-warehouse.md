---
title: Azure Synapse Analytics にテラバイト単位のデータを読み込む
description: 1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure Synapse Analytics に読み込む方法を示します
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 23cba5dbee4900f56e4180912f276cbd29c743f7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264201"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>1 TB のデータを Data Factory を使用して 15 分以内に Azure Synapse Analytics に読み込む
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory を使用して Azure Synapse Analytics をコピー先またはコピー元としてデータをコピーする方法](../connector-azure-sql-data-warehouse.md)に関するページを参照してください。


[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大量のデータを処理できます。  超並列処理 (MPP) アーキテクチャが基になっている Azure Synapse Analytics は、企業のデータ ウェアハウスのワークロード向けに最適化されています。  ストレージとコンピューティングを別々にスケールできる柔軟性によって、クラウドの弾力性を提供します。

Azure Synapse Analytics は、**Azure Data Factory** を使用するといっそう使いやすくなります。  Azure Data Factory は、フル マネージドのクラウド ベースのデータ統合サービスであり、このサービスを使用して既存のシステムのデータを Azure Synapse Analytics に入力することで、貴重な時間を節約しながら、Azure Synapse Analytics の評価と分析ソリューションの構築を行うことができます。 Azure Data Factory を使用して Azure Synapse Analytics にデータを読み込む主な利点を次に示します。

* **簡単なセットアップ**: 5 つの手順からなる直感的なウィザードを使用でき、スクリプト作成の必要はありません。
* **豊富なデータ ストアのサポート**: オンプレミスとクラウド ベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。
* **セキュリティとコンプライアンスへの準拠**: データは HTTPS または ExpressRoute で転送され、グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **PolyBase の使用による比類のないパフォーマンス**: PolyBase の使用は、Azure Synapse Analytics にデータを移動するための最も効率的な方法です。 ステージング BLOB の機能を使用して、Azure Blob ストレージを含むすべての種類のデータ ストアからデータを高速で読み込むことができます。これは PolyBase が既定でサポートしている機能です。

この記事では、Data Factory コピー ウィザードを使用して、1 TB のデータを 1.2 GBps 以上のスループットで 15 分以内に Azure Blob Storage から Azure Synapse Analytics に読み込む方法を示します。

この記事では、コピー ウィザードを使用して Azure Synapse Analytics にデータを移動するための手順を説明します。

> [!NOTE]
>  Azure Synapse Analytics との間のデータ移動に関する Data Factory の機能の一般的な情報については、[Azure Data Factory を使用した Azure Synapse Analytics 間でのデータの移動](data-factory-azure-sql-data-warehouse-connector.md)に関する記事を参照してください。
>
> Visual Studio、PowerShell などを使用してパイプラインを構築することもできます。「[チュートリアル:Azure Data Factory でコピー アクティビティを使用するための詳細な手順を含む簡単なチュートリアルについては、Azure BLOB から Azure SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルを参照してください。  
>
>

## <a name="prerequisites"></a>前提条件
* Azure Blob Storage: この実験では、Azure Blob Storage (GRS) を使用して、TPC-H テスト データセットを格納します。  Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../../storage/common/storage-account-create.md)」を参照してください。
* [TPC-H](http://www.tpc.org/tpch/) データ: テスト データセットとして TPC-H を使用します。  これを行うには、TPC-H ツールキットの `dbgen` を使用する必要があります。これにより、データセットを簡単に生成できます。  `dbgen` のソース コードを [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) からダウンロードして自分でコンパイルするか、コンパイル済みのバイナリを [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools) からダウンロードします。  次のコマンドで dbgen.exe を実行して、10 個のファイルに分散される `lineitem` テーブルの 1 TB のフラット ファイルを生成します。

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    次に、生成されたファイルを Azure Blob にコピーします。  ADF コピーを使用した実行方法については、「[Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータを移動する](data-factory-onprem-file-system-connector.md)」を参照してください。    
* Azure Synapse Analytics: この実験では、6,000 DWU で作成された Azure Synapse Analytics にデータを読み込みます

    Azure Synapse Analytics データベースを作成する方法の詳細については、「[Azure Synapse Analytics を作成する](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)」を参照してください。  ここでは、PolyBase を使用した Azure Synapse Analytics への最高の読み込みパフォーマンスを得るために、パフォーマンス設定で許可される最大数の Data Warehouse ユニット (DWU) を選択しています。その最大数は 6,000 DWU です。

  > [!NOTE]
  > Azure Blob からの読み込みでは、データ読み込みのパフォーマンスは Azure Synapse Analytics で構成した DWU の数と正比例します。
  >
  > 1 TB を 1,000 DWU の Azure Synapse Analytics に読み込むには 87 分かかり (最大 200 MBps のスループット)、2,000 DWU の Azure Synapse Analytics に読み込むには 46 分かかり (最大 380 MBps のスループット)、6,000 DWU の Azure Synapse Analytics に読み込むには 14 分かかります (最大 1.2 GBps のスループット)。
  >
  >

    6,000 DWU の 専用 SQL プールを作成するには、パフォーマンス スライダーを右端まで移動します。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/performance-slider.png" alt-text="パフォーマンス スライダー":::

    6,000 DWU で構成されていない既存のデータベースの場合は、Azure ポータルを使用してスケール アップできます。  Azure ポータルで既存のデータベースに移動します。次の図に示すように、 **[概要]** パネルに **[スケール]** ボタンがあります。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/scale-button.png" alt-text="[スケール] ボタン":::    

    **[スケール]** ボタンをクリックして次に示すパネルを開き、スライダーを最大値まで移動し、 **[保存]** ボタンをクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/scale-dialog.png" alt-text="[スケール] ダイアログ":::

    この実験では、`xlargerc` リソース クラスを使用して Azure Synapse Analytics にデータを読み込みます。

    最高のスループットを実現するには、`xlargerc` リソース クラスに所属する Azure Synapse Analytics ユーザーでコピーを実行する必要があります。  「[ユーザー リソース クラスの変更例](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)」で、実行方法を確認してください。  
* 次の DDL ステートメントを実行して、Azure Synapse Analytics データベースに変換先テーブル スキーマを作成します。

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  以上で前提条件の手順が完了し、コピー ウィザードを使用するコピー アクティビティを構成する準備が整いました。

## <a name="launch-copy-wizard"></a>コピー ウィザードの起動
1. [Azure Portal](https://portal.azure.com) にログインします。
2. 左上隅にある **[リソースの作成]** 、 **[インテリジェンス + 分析]** 、 **[データ ファクトリ]** の順にクリックします。
3. **[新しいデータ ファクトリ]** ウィンドウで、次の手順を実行します。

   1. **[名前]** に「**LoadIntoSQLDWDataFactory**」と入力します。
       Azure Data Factory の名前はグローバルに一意にする必要があります。 エラー **データ ファクトリ名 "LoadIntoSQLDWDataFactory" は使用できません** が表示された場合は、データ ファクトリの名前を変更し (yournameLoadIntoSQLDWDataFactory など)、作成し直してください。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。  
   2. Azure **サブスクリプション** を選択します。
   3. リソース グループについて、次の手順のいずれかを行います。
      1. **[既存のものを使用]** を選択し、既存のリソース グループを選択します。
      2. **[新規作成]** を選択し、リソース グループの名前を入力します。
   4. データ ファクトリの **場所** を選択します。
   5. ブレードの一番下にある **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。  
   6. **Create** をクリックしてください。
4. 作成が完了すると、次の図に示すような **[Data Factory]** ブレードが表示されます。

   :::image type="content" source="media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png" alt-text="データ ファクトリのホーム ページ":::
5. Data Factory のホーム ページで **[データのコピー]** タイルをクリックして、**コピー ウィザード** を起動します。

   > [!NOTE]
   > 承認中であることを示すメッセージが表示されたまま Web ブラウザーが停止してしまう場合は、**サード パーティの Cookie とサイト データをブロック** する設定を無効にしてください。または、有効な状態のまま **login.microsoftonline.com** に対する例外を作成し、そのうえで、もう一度ウィザードを起動してください。
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>手順 1:データの読み込みスケジュールを構成する
最初の手順は、データの読み込みスケジュールを構成することです。  

**[プロパティ]** ページで次の操作を実行します。

1. **[タスク名]** に「**CopyFromBlobToAzureSqlDataWarehouse**」と入力します。
2. **[Run once now (今すぐ 1 度だけ実行する)]** オプションを選択します。   
3. **[次へ]** をクリックします。  

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png" alt-text="コピー ウィザード - [プロパティ] ページ":::

## <a name="step-2-configure-source"></a>手順 2:ソースの構成
このセクションでは、1-TB TPC-H 行アイテム ファイルを含む Azure BLOB のソースを構成する手順を示します。

1. データ ストアとして **[Azure Blob Storage]** を選択し、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-source-connection.png" alt-text="コピー ウィザード - ソース選択ページ":::

2. Azure Blob ストレージ アカウントの接続情報を入力し、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/source-connection-info.png" alt-text="コピー ウィザード - ソース接続情報":::

3. TPC-H 行項目ファイルが含まれている **フォルダー** を選択し、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-input-folder.png" alt-text="コピー ウィザード - 入力フォルダーの選択":::

4. **[次へ]** をクリックすると、ファイル形式の設定が自動的に検出されます。  列区切り記号が、既定のコンマ ',' ではなく、'|' になっていることを確認します。  データをプレビューした後、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/file-format-settings.png" alt-text="コピー ウィザード - ファイル形式の設定":::

## <a name="step-3-configure-destination"></a>手順 3:コピー先を構成する
このセクションでは、変換先 (Azure Synapse Analytics データベースの `lineitem` テーブル) を構成する方法を示します。

1. 変換先ストアとして **[Azure Synapse Analytics]** を選択し、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-destination-data-store.png" alt-text="コピー ウィザード - 変換先データ ストアの選択":::

2. Azure Synapse Analytics の接続情報を入力します。  `xlargerc` ロールのメンバーであるユーザー (詳細な手順については「**前提条件**」セクションを参照してください) を指定したことを確認し、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/destination-connection-info.png" alt-text="コピー ウィザード - 変換先の接続情報":::

3. 変換先テーブルを選択し、 **[次へ]** をクリックします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/table-mapping-page.png" alt-text="コピー ウィザード - [テーブル マッピング] ページ":::

4. [スキーマ マッピング] ページで [Apply column mapping (列マッピングの適用)] オプションをオフにして、 **[次へ]** をクリックします。

## <a name="step-4-performance-settings"></a>手順 4:パフォーマンス設定

**[Allow polybase (PolyBase を許可する)]** は既定でオンになっています。  **[次へ]** をクリックします。

:::image type="content" source="media/data-factory-load-sql-data-warehouse/performance-settings-page.png" alt-text="コピー ウィザード - [スキーマ マッピング] ページ":::

## <a name="step-5-deploy-and-monitor-load-results"></a>手順 5:デプロイし、読み込み結果を監視する
1. **[完了]** ボタンをクリックしてデプロイします。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/summary-page.png" alt-text="コピー ウィザード - [概要] ページ 1":::

2. デプロイが完了したら、[`Click here to monitor copy pipeline`] をクリックして、コピーの実行の進行状況を監視します。 **[アクティビティ ウィンドウ]** の一覧から、作成したコピー パイプラインを選択します。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png" alt-text="コピー ウィザード - [概要] ページ 2":::

    右側のパネルの **アクティビティ ウィンドウ エクスプローラー** で、コピーの実行の詳細を確認できます。ソースから読み取られたデータ量、変換先に書き込まれたデータ量、および実行の平均スループットなどが表示されます。

    次のスクリーンショットからわかるように、1 TB のデータを Azure Blob Storage から Azure Synapse Analytics にコピーする処理は 14 分で完了し、実質的に 1.22 GBps のスループットが達成されています。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/succeeded-info.png" alt-text="コピー ウィザード - [成功] ダイアログ":::

## <a name="best-practices"></a>ベスト プラクティス
Azure Synapse Analytics データベースを実行するためのいくつかのベスト プラクティスを次に示します。

* クラスター化列ストア インデックスに読み込む場合は、大きなリソース クラスを使用します。
* 結合の効率を上げるために、既定のラウンド ロビン分散ではなく、列選択によるハッシュ分散の使用を検討します。
* 読み込み速度を上げるために、一時的なデータに対するヒープの使用を検討します。
* 統計は、Azure Synapse Analytics の読み込みが完了した後で作成します。

詳細については、[Azure Synapse Analytics のベスト プラクティス](../../synapse-analytics/sql/best-practices-dedicated-sql-pool.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
* [Data Factory コピー ウィザード](data-factory-copy-wizard.md) - この記事では、コピーウィザードの詳細について説明します。
* [コピー アクティビティのパフォーマンスとチューニング ガイド](data-factory-copy-activity-performance.md) - この記事には、参考となるパフォーマンスの測定とチューニングのガイドが含まれています。