---
title: Azure Data Factory と Azure Data Share を使用したデータ統合
description: Azure Data Factory と Azure Data Share を使用してデータをコピー、変換、共有します
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: 4d99139e708f0b69b3ddacd40e380853dabf8b2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077954"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Azure Data Factory と Azure Data Share を使用したデータ統合

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

お客様が先進的なデータ ウェアハウスおよび分析のプロジェクトに着手すると、より多くのデータが必要になるだけでなく、自社のデータ資産全体に対するデータの可視性を高めることも必要になります。 このワークショップでは、Azure Data Factory と Azure Data Share の強化によって、Azure におけるデータの統合と管理がいかに簡素化されるについて詳しく説明します。 

コーディングが不要な ETL (または ELT) の実現からデータに対する包括的なビューの作成まで、Azure Data Factory の強化によって、データ エンジニアはより多くのデータを安心して取り込むことができるようになり、ひいては、会社により大きな価値をもたらすことができます。 Azure Data Share を使用すれば、統制された形で企業間の共有を行えます。

このワークショップでは、Azure Data Factory (ADF) を使用して Azure SQL Database から Azure Data Lake Storage Gen2 (ADLS Gen2) にデータを取り込みます。 レイクにデータを配置したら、データ ファクトリのネイティブ変換サービスであるマッピング データ フローを介してそれを変換し、Azure Synapse Analytics にシンクします。 その後、Azure Data Share を使用して、変換済みのデータが含まれたテーブルを別のいくつかのデータと共に共有します。 

このラボで使用されるデータは、ニューヨーク市のタクシーのデータです。 これを SQL Database のデータベースにインポートするために、[taxi-data.bacpac ファイル](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)をダウンロードしてください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

* **Azure SQL Database**:SQL DB がない場合は、[SQL DB アカウントの作成](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)方法を確認してください。

* **Azure Data Lake Storage Gen2 ストレージ アカウント**: ADLS Gen2 ストレージ アカウントがない場合は、[ADLS Gen2 ストレージ アカウントの作成](../storage/common/storage-account-create.md)方法を確認してください。

* **Azure Synapse Analytics**:Azure Synapse Analytics がない場合は、[Azure Synapse Analytics インスタンスの作成](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)方法を確認してください。

* **Azure Data Factory**: データ ファクトリを作成していない場合は、[データ ファクトリの作成](./quickstart-create-data-factory-portal.md)方法を確認してください。

* **Azure Data Share**: データ共有を作成していない場合は、[データ共有の作成](../data-share/share-your-data.md#create-a-data-share-account)方法を確認してください。

## <a name="set-up-your-azure-data-factory-environment"></a>Azure Data Factory 環境を設定する

このセクションでは、Azure portal から Azure Data Factory ユーザー エクスペリエンス (ADF UX) にアクセスする方法について説明します。 ADF UX に移動したら、使用するデータ ストアごとに、次の 3 つのリンクされたサービスを構成します: Azure SQL DB、ADLS Gen2、および Azure Synapse Analytics。

Azure Data Factory では、リンクされたサービスによって外部リソースへの接続情報が定義されます。 Azure Data Factory では現在、85 を超えるコネクタをサポートしています。

### <a name="open-the-azure-data-factory-ux"></a>Azure Data Factory UX を開く

1. Microsoft Edge または Google Chrome で [Azure portal](https://portal.azure.com) を開きます。
1. ページの上部にある検索バーを使用して、「Data Factories」と検索します。

    :::image type="content" source="media/lab-data-flow-data-share/portal1.png" alt-text="ポータル 1":::
1. 目的のデータ ファクトリ リソースをクリックして、そのリソース ブレードを開きます。

    :::image type="content" source="media/lab-data-flow-data-share/portal2.png" alt-text="ポータル 2":::
1. **[Author and Monitor]\(作成と監視\)** をクリックして ADF UX を開きます。 ADF UX には、adf.azure.com でアクセスすることもできます。

    :::image type="content" source="media/lab-data-flow-data-share/portal3.png" alt-text="ポータル 3":::
1. ADF UX のホームページにリダイレクトされます。 このページには、データ ファクトリの概念について確認できるクイックスタート、説明ビデオ、チュートリアルへのリンクが記載されています。 作成を開始するには、左側のバーにある鉛筆アイコンをクリックします。

    :::image type="content" source="./media/doc-common-process/get-started-page-author-button.png" alt-text="ポータルの構成":::

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する

1. リンク サービスを作成するには、左側のバーにある **[管理]** ハブを選択して、 **[接続]** ペインの **[リンクされたサービス]** を選択し、 **[New]\(新規\)** を選択して新しいリンク サービスを追加します。

    :::image type="content" source="media/lab-data-flow-data-share/configure2.png" alt-text="ポータルの構成 2":::
1. 最初に構成するリンクされたサービスは Azure SQL DB です。 検索バーを使用して、データ ストアの一覧をフィルタリングできます。 **[Azure SQL Database]** タイルをクリックして [続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/configure-4.png" alt-text="ポータルの構成 4":::
1. SQL DB 構成ペインで、リンクされたサービスの名前として「SQLDB」を入力します。 データ ファクトリが自分のデータベースに接続できるように資格情報を入力します。 SQL 認証を使用する場合は、サーバー名、データベース、自分のユーザー名とパスワードを入力します。 **[Test connection]\(接続のテスト\)** をクリックすると、接続情報が正しいことを確認できます。 完了したら **[作成]** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/configure5.png" alt-text="ポータルの構成 5":::

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Azure Synapse Analytics のリンクされたサービスを作成する

1. 同じ手順を繰り返して、Azure Synapse Analytics のリンクされたサービスを追加します。 [接続] タブで **[新規]** をクリックします。 **[Azure Synapse Analytics]** タイルを選択して [続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/configure-6.png" alt-text="ポータルの構成 6":::
1. リンクされたサービスの構成ペインで、リンクされたサービスの名前として「SQLDW」を入力します。 データ ファクトリが自分のデータベースに接続できるように資格情報を入力します。 SQL 認証を使用する場合は、サーバー名、データベース、自分のユーザー名とパスワードを入力します。 **[Test connection]\(接続のテスト\)** をクリックすると、接続情報が正しいことを確認できます。 完了したら **[作成]** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/configure-7.png" alt-text="ポータルの構成 7":::

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Azure Data Lake Storage Gen2 のリンクされたサービスを作成する

1. このラボに必要な最後のリンクされたサービスは、Azure Data Lake Storage Gen2 です。  [接続] タブで **[新規]** をクリックします。 **[Azure Data Lake Storage Gen2]** タイルを選択し、[続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/configure8.png" alt-text="ポータルの構成 8":::
1. リンクされたサービスの構成ペインで、リンクされたサービスの名前として「ADLSGen2」を入力します。 アカウント キー認証を使用する場合は、 **[ストレージ アカウント名]** ドロップダウンから目的の ADLS Gen2 ストレージ アカウントを選択します。 **[Test connection]\(接続のテスト\)** をクリックすると、接続情報が正しいことを確認できます。 完了したら **[作成]** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/configure9.png" alt-text="ポータルの構成 9":::

### <a name="turn-on-data-flow-debug-mode"></a>データ フローのデバッグ モードを有効にする

「*マッピング データ フローを使用してデータを変換する*」セクションでは、マッピング データ フローを作成します。 マッピング データ フローを作成する前に、デバッグ モードを有効にしておくことをお勧めします。これにより、アクティブな Spark クラスターで変換ロジックを数秒でテストできます。

デバッグを有効にするには、 **[データ フロー]** アクティビティがある状態で、データ フロー キャンバスまたはパイプライン キャンバスの上部のバーにある **[データ フローのデバッグ]** スライダーをクリックします。 確認のダイアログが表示されたら **[OK]** をクリックします。 クラスターは約 5 分から 7 分で起動します。 初期化されている間に、"*コピー アクティビティを使用して Azure SQL DB から ADLS Gen2 にデータを取り込む*" 方法に関するセクションに進んでください。

:::image type="content" source="media/lab-data-flow-data-share/configure10.png" alt-text="ポータルの構成 10":::

:::image type="content" source="media/lab-data-flow-data-share/configure-11.png" alt-text="[データ フローのデバッグ] のスライダーを示すスクリーンショット。":::

## <a name="ingest-data-using-the-copy-activity"></a>コピー アクティビティを使用してデータを取り込む

このセクションでは、Azure SQL DB から ADLS Gen2 ストレージ アカウントにテーブルを 1 つ取り込むコピー アクティビティを含んだパイプラインを作成します。 ADF UX を介してパイプラインを追加し、データセットを構成して、パイプラインをデバッグする方法を見ていきましょう。 このセクションで使用される構成パターンは、リレーショナル データ ストアからファイルベースのデータ ストアへのコピーに適用できます。

Azure Data Factory におけるパイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 自分のデータに対して実行する操作は、アクティビティによって定義されます。 リンクされたサービスでは、使用するデータがデータセットによって参照されます。

### <a name="create-a-pipeline-with-a-copy-activity"></a>コピー アクティビティを含むパイプラインを作成する

1. ファクトリ リソース ペインのプラス アイコンをクリックして、新しいリソースのメニューを開きます。 **[パイプライン]** を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/copy1.png" alt-text="ポータルのコピー 1":::
1. パイプライン キャンバスの **[General]\(全般\)** タブで、パイプラインにわかりやすい名前を付けます (例: "IngestAndTransformTaxiData")。

    :::image type="content" source="media/lab-data-flow-data-share/copy2.png" alt-text="ポータルのコピー 2":::
1. パイプライン キャンバスのアクティビティ ペインで **[移動と変換]** アコーディオンを開き、 **[データ コピー]** アクティビティをキャンバスにドラッグします。 コピー アクティビティにわかりやすい名前を付けます (例: "IngestIntoADLS")。

    :::image type="content" source="media/lab-data-flow-data-share/copy3.png" alt-text="ポータルのコピー 3":::

### <a name="configure-azure-sql-db-source-dataset"></a>Azure SQL DB ソース データセットを構成する

1. コピー アクティビティの **[ソース]** タブをクリックします。 新しいデータセットを作成するには、 **[新規]** をクリックします。 ソースは、先ほど構成したリンクされたサービス "SQLDB" にある "dbo.TripData" テーブルになります。

    :::image type="content" source="media/lab-data-flow-data-share/copy4.png" alt-text="ポータルのコピー 4":::
1. **[Azure SQL Database]** を検索して [続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/copy-5.png" alt-text="ポータルのコピー 5":::
1. データセットに "TripData" という名前を付けます。 リンクされたサービスとして、[SQLDB] を選択してください。 [テーブル名] ドロップダウンから "dbo.TripData" というテーブル名を選択します。 **[From connection/store]\(接続/ストアから\)** スキーマをインポートします。 完了したら、[OK] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/copy6.png" alt-text="ポータルのコピー 6":::

これでソース データセットが正しく作成されました。 ソースの設定の [use query]\(クエリの使用\) フィールドで、既定値である **[テーブル]** が選択されていることを確認してください。

### <a name="configure-adls-gen2-sink-dataset"></a>ADLS Gen2 シンク データセットを構成する

1. コピー アクティビティの **[シンク]** タブをクリックします。 新しいデータセットを作成するには、 **[新規]** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/copy7.png" alt-text="ポータルのコピー 7":::
1. **[Azure Data Lake Storage Gen2]** を検索し、[続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/copy8.png" alt-text="ポータルのコピー 8":::
1. [形式の選択] ペインで **[DelimitedText]** を選択します。ここでは、CSV ファイルに書き込みます。 [続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/copy9.png" alt-text="ポータルのコピー 9":::
1. シンク データセットに "TripDataCSV" という名前を付けます。 リンクされたサービスとして、[ADLSGen2] を選択してください。 CSV ファイルの書き込み先を入力します。 たとえば、`staging-container` というコンテナー内のファイル `trip-data.csv` にデータを書き込むことができます。 出力データにヘッダーを含めたいので、 **[First row as header]\(先頭の行を見出しとして使用\)** を true に設定します。 コピー先にはまだファイルが存在しないため、 **[スキーマのインポート]** は **[なし]** に設定します。 完了したら、[OK] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/copy10.png" alt-text="ポータルのコピー 10":::

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>パイプラインのデバッグ実行でコピー アクティビティをテストする

1. コピー アクティビティが正しく動作していることを確認するために、パイプライン キャンバスの上部にある **[デバッグ]** をクリックしてデバッグを実行します。 デバッグ実行では、データ ファクトリ サービスに発行するパイプラインを、あらかじめエンド ツー エンドで、またはブレークポイントまでテストすることができます。

    :::image type="content" source="media/lab-data-flow-data-share/copy11.png" alt-text="ポータルのコピー 11":::
1. デバッグ実行を監視するために、パイプライン キャンバスの **[出力]** タブに移動します。 監視画面は、[更新] ボタンを手動でクリックしたとき、または 20 秒おきに自動更新されます。 コピー アクティビティには、特殊な監視ビューが用意されています。このビューには、 **[アクション]** 列の眼鏡アイコンをクリックするとアクセスできます。

    :::image type="content" source="media/lab-data-flow-data-share/copy12.png" alt-text="ポータルのコピー 12":::
1. コピーの監視ビューには、アクティビティの実行に関する詳しい情報やパフォーマンス特性が表示されます。 読み書きされたデータ、読み書きされた行、読み書きされたファイル、スループットなどの情報を確認できます。 すべて正しく構成されていれば、ADLS シンク内の 1 つのファイルに書き込まれた 49,999 件の行が表示されるはずです。

    :::image type="content" source="media/lab-data-flow-data-share/copy13.png" alt-text="ポータルのコピー 13":::
1. 次のセクションに進む前に、ファクトリ上部のバーにある **[Publish all]\(すべて発行\)** をクリックして、変更内容をデータ ファクトリ サービスに発行しておくことをお勧めします。 このラボでは説明しませんが、Azure Data Factory では Git との完全な統合がサポートされます。 Git 統合によって、バージョン コントロール、リポジトリへの反復的保存、データ ファクトリでのコラボレーションが可能となります。 詳細については、「[Azure Data Factory でのソース管理](./source-control.md#troubleshooting-git-integration)」を参照してください。

    :::image type="content" source="media/lab-data-flow-data-share/publish1.png" alt-text="ポータルの発行 1":::

## <a name="transform-data-using-mapping-data-flow"></a>マッピング データ フローを使用してデータを変換する

Azure Data Lake Storage にデータを正しくコピーできたら、そのデータを結合し、集約してデータ ウェアハウスに格納します。 ここでは、視覚的に設計できる Azure Data Factory の変換サービス、マッピング データ フローを使用します。 マッピング データ フローを使用するとユーザーは、コーディングなしで変換ロジックを開発し、ADF サービスによって管理された Spark クラスターでそれらを実行することができます。

この手順で作成したデータ フローは、前セクションで作成した "TripDataCSV" データセットと、"SQLDB" に格納された "dbo.TripFares" テーブルとを 4 つのキー列に基づいて内部結合します。 その後、データは `payment_type` 列に基づいて集計され、特定のフィールドの平均が計算されて、Azure Synapse Analytics テーブルに書き込まれます。

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>データ フロー アクティビティをパイプラインに追加する

1. パイプライン キャンバスのアクティビティ ペインで **[移動と変換]** アコーディオンを開き、 **[データ フロー]** アクティビティをキャンバスにドラッグします。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow1.png" alt-text="ポータルのデータ フロー 1":::
1. 開いたサイド ペインで **[Create new data flow]\(新しいデータ フローの作成\)** を選択し、 **[マッピング データ フロー]** を選択します。 **[OK]** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow2.png" alt-text="ポータルのデータ フロー 2":::
1. 変換ロジックを構築するためのデータ フロー キャンバスに誘導されます。 [General]\(全般\) タブで、データ フローに "JoinAndAggregateData" という名前を付けます。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow3.png" alt-text="ポータルのデータ フロー 3":::

### <a name="configure-your-trip-data-csv-source"></a>乗車データの CSV ソースを構成する

1. 最初に、2 つのソース変換を構成する必要があります。 1 つ目のソースの参照先は、"TripDataCSV" DelimitedText データセットです。 ソース変換を追加するには、キャンバスにある **[Add Source]\(ソースの追加\)** ボックスをクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow4.png" alt-text="ポータルのデータ フロー 4":::
1. ソースに "TripDataCSV" という名前を付け、ソース ドロップダウンから "TripDataCSV" データセットを選択します。 最初、このデータセットを作成するときにはデータが存在していなかったため、スキーマをインポートしなかったことを思い出してください。 現在は `trip-data.csv` が存在するので、 **[編集]** をクリックして、データセットの設定タブに移動します。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow5.png" alt-text="ポータルのデータ フロー 5":::
1. **[スキーマ]** タブに移動し、 **[スキーマのインポート]** をクリックします。 ファイル ストアから直接インポートするには、 **[From connection/store]\(接続/ストアから\)** を選択します。 文字列型の 14 個の列が表示されます。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow6.png" alt-text="ポータルのデータ フロー 6":::
1. データ フロー "JoinAndAggregateData" に戻ります。 デバッグ クラスターが起動済み (デバッグ スライダーの横の緑色の円で示されます) である場合、 **[データのプレビュー]** タブでデータのスナップショットを取得できます。 **[最新の情報に更新]** をクリックすると、データのプレビューがフェッチされます。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow7.png" alt-text="ポータルのデータ フロー 7":::

> [!Note]
> データのプレビューでは、データは書き込まれません。

### <a name="configure-your-trip-fares-sql-db-source"></a>乗車料金の SQL DB ソースを構成する

1. 追加する 2 つ目のソースは、SQL DB テーブル "dbo.TripFares" を参照します。 "TripDataCSV" ソースの下に、別の **[Add Source]\(ソースの追加\)** ボックスが表示されます。 これをクリックして、新しいソース変換を追加します。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow8.png" alt-text="ポータルのデータ フロー 8":::
1. このソースには "TripFaresSQL" という名前を付けます。 ソース データセット フィールドの横にある **[New]\(新規\)** をクリックして、新しい SQL DB データセットを作成します。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow9.png" alt-text="ポータルのデータ フロー 9":::
1. **[Azure SQL Database]** タイルを選択して [続行] をクリックします。 *注意事項: お気付きかもしれませんが、データ ファクトリにあるコネクタの多くが、マッピング データ フローではサポート対象外となっています。これらのソースのいずれかからデータを変換するには、コピー アクティビティを使用して、サポート対象のソースにデータを取り込んでください*。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow-10.png" alt-text="ポータルのデータ フロー 10":::
1. データセットに "TripFares" という名前を付けます。 リンクされたサービスとして、[SQLDB] を選択してください。 [テーブル名] ドロップダウンから "dbo.TripFares" というテーブル名を選択します。 **[From connection/store]\(接続/ストアから\)** スキーマをインポートします。 完了したら、[OK] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow11.png" alt-text="ポータルのデータ フロー 11":::
1. データを確認するために、 **[データのプレビュー]** タブでデータ プレビューをフェッチします。

    :::image type="content" source="media/lab-data-flow-data-share/dataflow12.png" alt-text="ポータルのデータ フロー 12":::

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>TripDataCSV と TripFaresSQL を内部結合する

1. 新しい変換を追加するために、"TripDataCSV" の右下隅にあるプラス アイコンをクリックします。 **[Multiple inputs/outputs]\(複数の入出力\)** の **[結合]** を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/join1.png" alt-text="ポータルの結合 1":::
1. 結合変換に "InnerJoinWithTripFares" という名前を付けます。 [Right stream]\(右側のストリーム\) ドロップダウンから [TripFaresSQL] を選択してください。 結合の種類として **[内部]** を選択します。 マッピング データ フローにおけるさまざまな結合の種類について詳しくは、[結合の種類](./data-flow-join.md#join-types)に関するセクションを参照してください。

    **[Join conditions]\(結合条件\)** ドロップダウンで、それぞれのストリームから突き合わせる列を選択します。 さらに結合条件を追加したければ、既存の条件の横にあるプラス アイコンをクリックしてください。 既定では、すべての結合条件が AND 演算子で組み合わされます。つまり、すべての条件が満たされたときに初めて一致と見なされます。 このラボでは、`medallion`、`hack_license`、`vendor_id`、`pickup_datetime` の各列で突き合わせを行います。

    :::image type="content" source="media/lab-data-flow-data-share/join2.png" alt-text="ポータルの結合 2":::
1. データのプレビューで、25 個の列が正常に結合されたことを確認します。

    :::image type="content" source="media/lab-data-flow-data-share/join3.png" alt-text="ポータルの結合 3":::

### <a name="aggregate-by-payment_type"></a>payment_type ごとに集計する

1. 結合変換が完了したら、"InnerJoinWithTripFares" の横にあるプラス アイコンをクリックして集計変換を追加します。 **[Schema modifier]\(スキーマ修飾子\)** の **[Aggregate]\(集計\)** を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/agg1.png" alt-text="ポータルの集計 1":::
1. 集計変換に "AggregateByPaymentType" という名前を付けます。 グループ化列として `payment_type` を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/agg2.png" alt-text="ポータルの集計 2":::
1. **[集計]** タブに移動します。ここで、次の 2 つの集計を指定します。
    * 支払いの種類ごとにグループ化された平均料金
    * 支払いの種類ごとにグループ化された合計乗車距離

    まず、平均料金の式を作成しましょう。 **[Add or select a column]\(列の追加または選択\)** テキスト ボックスに「average_fare」と入力します。

    :::image type="content" source="media/lab-data-flow-data-share/agg3.png" alt-text="ポータルの集計 3":::
1. 集計式を入力するには、 **[式の入力]** と表示された青色のボックスをクリックします。 データ フローの式ビルダーが起動します。これは、入力スキーマや組み込みの関数と演算子、ユーザー定義のパラメーターを使用してデータ フローの式を視覚的に作成するためのツールです。 式ビルダーの機能について詳しくは、[式ビルダーのドキュメント](./concepts-data-flow-expression-builder.md)を参照してください。

    平均料金を取得するために、`total_amount` 列を `toInteger()` で整数にキャストした結果を、`avg()` 集計関数を使用して集計します。 データ フロー式の言語では、`avg(toInteger(total_amount))` として定義されます。 完了したら、 **[Save and finish]\(保存して終了する\)** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/agg4.png" alt-text="ポータルの集計 4":::
1. もう 1 つ集計式を追加するために、`average_fare` の横にあるプラス アイコンをクリックします。 **[Add column]\(列の追加\)** を選択してください。

    :::image type="content" source="media/lab-data-flow-data-share/agg5.png" alt-text="ポータルの集計 5":::
1. **[Add or select a column]\(列の追加または選択\)** テキスト ボックスに「total_trip_distance」と入力します。 直前の手順と同様に、式ビルダーを開いて式を入力します。

    合計乗車距離を取得するには、`trip_distance` 列を `toInteger()` で整数にキャストし、`sum()` 集計関数を使用してその結果を集計します。 データ フロー式の言語では、`sum(toInteger(trip_distance))` として定義されます。 完了したら、 **[Save and finish]\(保存して終了する\)** をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/agg6.png" alt-text="ポータルの集計 6":::
1. **[データのプレビュー]** タブで変換ロジックをテストします。ご覧のように、行と列が先ほどと比べ大幅に少なくなっていることがわかります。 この変換で定義されているグループ化列と集計列は 3 つだけです。その 3 つが引き続きダウンストリームに送られることになります。 このサンプルには、支払いの種類のグループが 5 つしか存在しないため、出力される行も 5 つだけです。

    :::image type="content" source="media/lab-data-flow-data-share/agg7.png" alt-text="ポータルの集計 7":::

### <a name="configure-you-azure-synapse-analytics-sink"></a>Azure Synapse Analytics シンクを構成する

1. 変換ロジックが完成し、Azure Synapse Analytics テーブルにデータを書き込む準備が整いました。 **[Destination]\(コピー先\)** セクションの下にシンク変換を追加します。

    :::image type="content" source="media/lab-data-flow-data-share/sink1.png" alt-text="ポータルのシンク 1":::
1. シンクに "SQLDWSink" という名前を付けます。 シンク データセット フィールドの横にある **[New]\(新規\)** をクリックして、新しい Azure Synapse Analytics データセットを作成します。

    :::image type="content" source="media/lab-data-flow-data-share/sink2.png" alt-text="ポータルのシンク 2":::

1. **[Azure Synapse Analytics]** タイルを選択して [続行] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/sink-3.png" alt-text="ポータルのシンク 3":::
1. データセットに "AggregatedTaxiData" という名前を付けます。 リンクされたサービスとして、[SQLDW] を選択してください。 **[新しいテーブルの作成]** を選択し、新しいテーブルに dbo.AggregateTaxiData という名前を付けます。 完了したら、[OK] をクリックします。

    :::image type="content" source="media/lab-data-flow-data-share/sink4.png" alt-text="ポータルのシンク 4":::
1. シンクの **[設定]** タブに移動します。 新しいテーブルを作成することになるので、[Table action]\(テーブル アクション\) の **[Recreate table]\(テーブルの再作成\)** を選択する必要があります。 挿入を行単位で行うか一括で行うかを切り替える **[Enable staging]\(ステージングの有効化\)** はオフにしてください。

    :::image type="content" source="media/lab-data-flow-data-share/sink5.png" alt-text="ポータルのシンク 5":::

これでデータ フローが正常に作成されました。 今度は、それをパイプライン アクティビティの中で実行してみましょう。

### <a name="debug-your-pipeline-end-to-end"></a>パイプラインをエンド ツー エンドでデバッグする

1. **IngestAndTransformData** パイプラインのタブに戻ります。 "IngestIntoADLS" コピー アクティビティの緑色のボックスに注目してください。 これを "JoinAndAggregateData" データ フロー アクティビティにドラッグします。 "on success" が作成され、コピーが成功した場合にのみデータ フロー アクティビティが実行されます。

    :::image type="content" source="media/lab-data-flow-data-share/pipeline1.png" alt-text="ポータルのパイプライン 1":::
1. コピー アクティビティで行ったように、 **[デバッグ]** をクリックしてデバッグを実行します。 デバッグ実行では、新しいクラスターは起動されずに、アクティブなデバッグ クラスターがデータ フロー アクティビティで使用されます。 このパイプラインは、実行に 1 分強かかります。

    :::image type="content" source="media/lab-data-flow-data-share/pipeline2.png" alt-text="ポータルのパイプライン 2":::
1. コピー アクティビティと同様、データ フローには、アクティビティの完了時に眼鏡アイコンでアクセスできる特殊な監視ビューが用意されています。

    :::image type="content" source="media/lab-data-flow-data-share/pipeline3.png" alt-text="ポータルのパイプライン 3":::
1. 監視ビューで、実行ステージごとの行数や実行時間と共に、簡略化されたデータ フロー グラフを確認できます。 正しく行えば、このアクティビティでは、49,999 件の行が集計されて、5 つの行に格納されているはずです。

    :::image type="content" source="media/lab-data-flow-data-share/pipeline4.png" alt-text="ポータルのパイプライン 4":::
1. 変換をクリックすると、パーティション分割情報や、新しい列、更新された列、削除された列など、実行に関するさらに詳しい情報を取得できます。

    :::image type="content" source="media/lab-data-flow-data-share/pipeline5.png" alt-text="ポータルのパイプライン 5":::

このラボのデータ ファクトリの部分は、これで完成です。 トリガーを使用して運用したければ、リソースを発行します。 コピー アクティビティを使用して Azure SQL Database から Azure Data Lake Storage にデータを取り込んだ後、そのデータを集計して Azure Synapse Analytics に格納するパイプラインを正常に実行できました。 SQL Server 自体を調べれば、データが正常に書き込まれたことを確認できます。

## <a name="share-data-using-azure-data-share"></a>Azure Data Share を使用したデータの共有

このセクションでは、Azure portal を使用して、新しいデータ共有を設定する方法について説明します。 これには、Azure Data Lake Store Gen2 と Azure Synapse Analytics からのデータセットを格納する新しいデータ共有を作成する作業が伴います。 その後、スナップショット スケジュールを構成します。データ コンシューマーは、自分に共有されたデータを必要に応じて自動的に更新することができます。 さらに、データ共有に受信者を招待してみましょう。 

データ共有を作成したら、"*データ コンシューマー*" の立場に入れ替わります。 データ コンシューマーとして、データ共有の招待状を受け取るフローをひととおり行いながら、データの受信場所を構成し、異なる保存場所にデータセットをマッピングします。 その後、スナップショットをトリガーすると、共有されたデータが、指定した保存先にコピーされます。 

### <a name="sharing-data-data-provider-flow"></a>データを共有する (データ プロバイダーのフロー)

1. Microsoft Edge または Google Chrome で Azure portal を開きます。

1. ページの上部にある検索バーを使用して、**Data Shares** を検索します。

    :::image type="content" source="media/lab-data-flow-data-share/portal-ads.png" alt-text="ポータルの広告":::

1. 名前に "Provider" を含むデータ共有アカウントを選択します (例: **DataProvider0102**)。 

1. **[Start sharing your data]\(データの共有を開始する\)** を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/ads-start-sharing.png" alt-text="共有を開始する":::

1. **[+ 作成]** を選択して、新しいデータ共有の構成を開始します。 

1. **[共有名]** に、任意の名前を指定します。 データ コンシューマーに表示される共有名であるため、わかりやすい名前 (例: TaxiData) を付けるようにしてください。

1. **[説明]** に、データ共有の内容を表す文を入力します。 データ共有には、Azure Synapse Analytics や Azure Data Lake Store を含むさまざまなストアに格納された世界各地のタクシー乗車データが格納されます。 

1. データ コンシューマーに守ってほしい使用条件を **[Terms of use]\(利用規約\)** に指定します。 たとえば、「このデータを社外に配布しないこと」や「契約書を参照のこと」と入力します。 

    :::image type="content" source="media/lab-data-flow-data-share/ads-details.png" alt-text="共有の詳細":::

1. **[続行]** をクリックします。 

1. **[データセットの追加]** を選択します。 

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset.png" alt-text="データセットの追加 1":::

1. ADF の変換結果が格納される Azure Synapse Analytics からテーブルを選択するため、 **[Azure Synapse Analytics]** を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset-sql.png" alt-text="データセットの追加 SQL":::


1. 次に進む前に実行すべきスクリプトが提供されます。 提供されたスクリプトによって、Azure Data Share MSI が自己を認証するためのユーザーが SQL データベースに作成されます。 

> [!IMPORTANT]
> スクリプトを実行する前に、自分自身を SQL Server の Active Directory 管理者として設定する必要があります。 

1. 新しいタブを開いて Azure portal に移動します。 提供されたスクリプトをコピーして、データの共有元となるユーザーをデータベースに作成します。 クエリ エクスプローラー (プレビュー) を使用し、AAD 認証で EDW データベースにログインしてください。 

    作成するユーザーをブラケットの内側に含めるように、スクリプトに変更を加える必要があります。 例:
    
    create user [dataprovider-xxxx] from external login;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. 先ほどデータセットを追加したデータ共有の Azure Data Share にもう一度切り替えます。 

1. **[EDW]** を選択し、テーブルに **[AggregatedTaxiData]** を選択します。 

1. **[データセットの追加]** を選択します。

    これで、データセットの一部である SQL テーブルが完成しました。 次に、Azure Data Lake Store から別のデータセットを追加します。 

1. **[データセットの追加]** を選択し、 **[Azure Data Lake Store Gen2]** を選択します。

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset-adls.png" alt-text="データセットの追加 ADLS":::

1. **[次へ]** を選択します

1. *[wwtaxidata]* を展開します。 *[Boston Taxi Data]\(ボストン タクシー データ\)* を展開します。 ファイル レベルまで共有できることに注目してください。 

1. *Boston Taxi Data* フォルダーを選択し、そのフォルダー全体をデータ共有に追加します。 

1. **[データセットの追加]** を選択します。

1. 追加したデータセットを確認します。 SQL テーブルと ADLS Gen2 フォルダーがデータ共有に追加されているはずです。 

1. **[続行]** を選択します

1. この画面では、データ共有に受信者を追加することができます。 追加した受信者には、データ共有への招待状が送信されます。 このラボの目的上、次に示す 2 つの電子メール アドレスを追加する必要があります。

    1. ご利用の Azure サブスクリプションの電子メール アドレス。 

        :::image type="content" source="media/lab-data-flow-data-share/add-recipients.png" alt-text="受信者の追加":::

    1. *janedoe@fabrikam.com* という名前の架空のデータ コンシューマーを追加します。

1. この画面では、データ コンシューマーのスナップショット設定を構成できます。 そうすることで、自分が定義した間隔で定期的に最新のデータを受信することができます。 

1. **[スナップショット スケジュール]** チェック ボックスをオンにし、 *[Recurrence]\(繰り返し\)* ボックスの一覧を使用して、1 時間ごとにデータが更新されるように構成します。  

1. **［作成］** を選択します

    これでアクティブなデータ共有が完成しました。 データ プロバイダーとしてデータ共有を作成する際に何が見えるか確認してみましょう。 

1. 作成したデータ共有 (**DataProvider**) を選択します。 **[Data Share]** の **[送信した共有]** を選択して移動できます。 

1. [スナップショット スケジュール] をクリックします。 スナップショット スケジュールは、必要に応じて無効にすることもできます。 

1. 次に、 **[データセット]** タブを選択します。このデータ共有には、作成後、さらに別のデータセットを追加することができます。 

1. **[共有サブスクリプション]** タブを選択します。この時点では、データ コンシューマーがまだ招待を受け入れていないため、共有サブスクリプションは存在しません。

1. **[招待]** タブに移動します。ここには、保留中の招待が一覧表示されます。 

    :::image type="content" source="media/lab-data-flow-data-share/pending-invites.png" alt-text="保留中の招待":::

1. *janedoe@fabrikam.com* への招待を選択します。 [削除] を選択します。 受信者がまだ招待を受け入れていない場合、以後、招待を受け入れることはできなくなります。 

1. **[履歴]** タブをクリックします。データ コンシューマーがまだ招待を受け入れておらず、またスナップショットもトリガーしていないため、何も表示されません。 

### <a name="receiving-data-data-consumer-flow"></a>データを受信する (データ コンシューマーのフロー)

以上、データ共有を確認してきました。今度は、データ コンシューマーの立場から見ていきましょう。 

受信トレイに、Microsoft Azure から Azure Data Share の招待状が届いているはずです。 Outlook Web Access (outlook.com) を起動し、ご利用の Azure サブスクリプションの資格情報を使用してログインしてください。

受信したメールの [View invitation >]\(招待を表示 >\) をクリックします。 この時点で、データ プロバイダーから送信されたデータ共有への招待を受け入れるデータ コンシューマーの操作をシミュレートすることになります。 

:::image type="content" source="media/lab-data-flow-data-share/email-invite.png" alt-text="招待メール":::

サブスクリプションの選択を求められることがあります。 このラボで使用しているサブスクリプションを選択してください。 

1. *DataProvider* というタイトルの招待をクリックします。 

1. この招待画面を見ると、先ほどデータ プロバイダーとして構成したデータ共有について、さまざまな詳細情報が表示されていることがわかります。 詳細を確認し、提示された利用規約に同意します。

1. あらかじめラボ用に存在するサブスクリプションとリソース グループを選択します。 

1. **[Data share account]\(データ共有アカウント\)** で **[DataConsumer]** を選択します。 新しいデータ共有アカウントを作成することもできます。 

1. **[Received share name]\(受信した共有名\)** の横を見ると、既定の共有名が、データ プロバイダーによって指定された名前になっていることがわかります。 受信しようとしているデータを表すわかりやすい共有名を付けてください (例: **TaxiDataShare**)。

    :::image type="content" source="media/lab-data-flow-data-share/consumer-accept.png" alt-text="招待の受け入れ":::

1. **[Accept and configure now]\(受け入れて今すぐ構成する\)** または **[Accept and configure later]\(受け入れて後で構成する\)** を選択できます。 [Accept and configure now]\(受け入れて今すぐ構成する\) を選択した場合、すべてのデータのコピー先となるストレージ アカウントを指定します。 [Accept and configure later]\(受け入れて後で構成する\) を選択した場合は、共有内のデータセットはマッピングされません。後で手動でマッピングする必要があります。 ここでは、後者を選択することにします。 

1. **[Accept and configure later]\(受け入れて後で構成する\)** を選択します。 

    このオプション構成では、共有サブスクリプションは作成されますが、コピー先がマッピングされないため、データの配置場所は未指定となります。 

    次に、データ共有におけるデータセットのマッピングを構成します。 

1. 受信した共有 (手順 5. で指定した名前) を選択します。

    **[スナップショットのトリガー]** は灰色表示されますが、共有はアクティブです。 

1. **[データセット]** タブを選択します。いずれのデータセットも "マッピング解除済み" であることに注目してください。これは、データのコピー先が存在しないことを意味します。 

    :::image type="content" source="media/lab-data-flow-data-share/unmapped.png" alt-text="マッピングされていないデータセット":::

1. Azure Synapse Analytics テーブルを選択し、 **[+ ターゲットへのマップ]** を選択します。

1. 画面の右側にある **[ターゲット データの種類]** ボックスの一覧を選択します。 

    SQL データはさまざまなデータ ストアにマッピングすることができます。 ここでは、Azure SQL Database にマッピングします。

    :::image type="content" source="media/lab-data-flow-data-share/mapping-options.png" alt-text="mapping":::
    
    (省略可) ターゲット データの種類として **[Azure Data Lake Store Gen2]** を選択します。 
    
    (省略可) ご利用のサブスクリプション、リソース グループ、ストレージ アカウントを選択します。 
    
    (省略可) データ レイクへのデータを CSV 形式または Parquet 形式で受信することを選択できます。 

1. **[ターゲット データの種類]** の横にある [Azure SQL Database] を選択します。 

1. ご利用のサブスクリプション、リソース グループ、ストレージ アカウントを選択します。 

    :::image type="content" source="media/lab-data-flow-data-share/map-to-sqldb.png" alt-text="SQL にマッピング":::

1. 次に進む前に、提供されたスクリプトを実行して、SQL Server に新しいユーザーを作成する必要があります。 まず、提供されたスクリプトをクリップボードにコピーします。 

1. 新しい Azure portal タブを開きます。既存のタブは閉じないでください。すぐに元のタブに切り替える必要があります。 

1. 新しく開いたタブで、 **[SQL データベース]** に移動します。

1. SQL データベース (ご利用のサブスクリプションに 1 つだけ存在する必要があります) を選択します。 データ ウェアハウスを選択しないようご注意ください。 

1. **[クエリ エディター (プレビュー)]** を選択します。

1. AAD 認証を使用してクエリ エディターにログインします。 

1. データ共有で、提供された (手順 14. でクリップボードにコピーした) クエリを実行します。 

    Azure Data Share サービスは、このコマンドにより、Azure サービスのマネージド ID を使用して SQL Server に対する認証を行い、SQL Server にデータをコピーすることができます。 

1. 元のタブに戻って、 **[ターゲットへのマップ]** を選択します。

1. 次に、データセットに含まれる Azure Data Lake Gen2 フォルダーを選択し、Azure Blob Storage アカウントにマッピングします。 

    :::image type="content" source="media/lab-data-flow-data-share/storage-map.png" alt-text="storage":::

    すべてのデータセットのマッピングが済んだら、データ プロバイダーからデータを受信する準備は完了です。 

    :::image type="content" source="media/lab-data-flow-data-share/all-mapped.png" alt-text="マッピング済み":::
    
1. **[詳細]** を選択します。 

    今はデータ共有にコピー先が存在するため、 **[スナップショットのトリガー]** は灰色表示されていないことに注目してください。

1. [スナップショットのトリガー]、[完全なコピー] の順にコピーします。 

    :::image type="content" source="media/lab-data-flow-data-share/trigger-full.png" alt-text="trigger":::

    新しいデータ共有アカウントへのデータのコピーが開始されます。 現実のシナリオなら、このデータはサード パーティから受信することになるでしょう。 

    データが到着するまでには 3 分から 5 分程度かかります。 **[履歴]** タブをクリックすることで進行状況を監視できます。 

    待っている間、元のデータ共有 (DataProvider) に移動し、 **[共有サブスクリプション]** と **[履歴]** タブの状態を見てください。今度はアクティブなサブスクリプションが存在することがわかります。また、共有されたデータをデータ コンシューマーがいつ受信し始めたかを、データ プロバイダーとして監視することもできます。 

1. データ コンシューマーのデータ共有に戻ります。 トリガーの状態が "成功" になったら、コピー先の SQL データベースとデータ レイクに移動し、それぞれのストアにデータが格納されていることを確認します。 

お疲れさまでした。これでラボは終了です。