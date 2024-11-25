---
title: カスタム分析を使用して Azure IoT Central を拡張する |Microsoft Docs
description: ソリューション開発者は、カスタムの分析と視覚化を実行するために IoT Central アプリケーションを構成します。 このソリューションでは、Azure Databricks を使用します。
author: dominicbetts
ms.author: dobett
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5f046920ce711ac6b1e6b9a461d71d9d49b2ddc7
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597665"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure Databricks を使用したカスタム分析で Azure IoT Central を拡張する

この攻略ガイドでは、カスタムの分析と視覚化を使用して IoT Central アプリケーションを拡張する方法を説明します。 この例では、[Azure Databricks](/azure/azure-databricks/) ワークスペースを使用して、IoT Central のテレメトリ ストリームを分析したり、[ボックス プロット](https://wikipedia.org/wiki/Box_plot)などの視覚化を生成したりします。  

この攻略ガイドでは、既に[組み込みの分析ツール](./howto-create-custom-analytics.md)を使用して実行できることを超えて IoT Central を拡張する方法を示します。

この攻略ガイドでは、以下の方法について説明します。

* *継続的データ エクスポート* を使用してテレメトリをストリーム配信します。
* Azure Databricks 環境を作成して、デバイスのテレメトリを分析およびプロットします。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="iot-central-application"></a>IoT Central アプリケーション

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、次の設定を使用して IoT Central アプリケーションを作成します。

| 設定 | 値 |
| ------- | ----- |
| 料金プラン | Standard |
| アプリケーション テンプレート | ストア内分析 – 条件監視 |
| アプリケーション名 | 既定値を受け入れるか、独自の名前を選択します |
| URL | 既定値を受け入れるか、独自の一意の URL プレフィックスを選択します |
| ディレクトリ | Azure Active Directory テナント |
| Azure サブスクリプション | お使いの Azure サブスクリプション |
| リージョン | 最も近いリージョン |

この記事の例とスクリーンショットでは、**米国** リージョンを使用します。 近くの場所を選択して、必ずすべてのリソースを同じリージョン内に作成してください。

このアプリケーション テンプレートには、テレメトリを送信する 2 つのシミュレートされたサーモスタット デバイスが含まれます。

### <a name="resource-group"></a>Resource group

作成するその他のリソースを格納するため、**IoTCentralAnalysis** という [リソース グループを Azure portal で作成](https://portal.azure.com/#create/Microsoft.ResourceGroup)します。 Azure リソースは IoT Central アプリケーションと同じ場所に作成してください。

### <a name="event-hubs-namespace"></a>Event Hubs 名前空間

以下の設定を使用して、[Azure portal で Event Hubs 名前空間を作成](https://portal.azure.com/#create/Microsoft.EventHub)します。

| 設定 | 値 |
| ------- | ----- |
| Name    | 名前空間名を選択します |
| Pricing tier | Basic |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | IoTCentralAnalysis |
| 場所 | East US |
| スループット単位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks ワークスペース

以下の設定を使用して、[Azure portal で Azure Databricks サービスを作成](https://portal.azure.com/#create/Microsoft.Databricks)します。

| 設定 | 値 |
| ------- | ----- |
| ワークスペース名    | ワークスペース名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | IoTCentralAnalysis |
| 場所 | 米国東部 |
| 価格レベル | Standard |

必要なリソースを作成すると、次のスクリーンショットのような **IoTCentralAnalysis** リソース グループが表示されます。

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="IoT Central 分析リソース グループの画像。":::

## <a name="create-an-event-hub"></a>イベント ハブの作成

テレメトリをイベント ハブに連続してエクスポートするように IoT Central アプリケーションを構成できます。 このセクションでは、IoT Central アプリケーションからテレメトリを受信するイベント ハブを作成します。 このイベント ハブは、処理するためにテレメトリを Stream Analytics ジョブに配信します。

1. Azure portal で Event Hubs 名前空間に移動し、 **[+ イベント ハブ]** を選択します。
1. イベント ハブに **centralexport** という名前を付けます。
1. 名前空間内のイベント ハブの一覧で、**centralexport** を選択します。 次に、**[共有アクセス ポリシー]** を選択します。
1. **[+ 追加]** を選択します。 **[送信]** と **[リッスン]** の要求を含む、**SendListen** という名前のポリシーを作成します。
1. ポリシーの準備ができたら、リストでそれを選択して、**[接続文字列 – 主キー]** の値をコピーします。
1. この接続文字列をメモしておきます。これは後で、イベント ハブから読み取るように Databricks ノートブックを構成するときに使用します。

次のスクリーンショットのような Event Hubs 名前空間が作成されます。

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="Event Hubs 名前空間の画像。":::

## <a name="configure-export-in-iot-central"></a>IoT Central でエクスポートを構成する

このセクションでは、シミュレートされたデバイスからイベント ハブにテレメトリをストリーム配信するようにアプリケーションを構成します。

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、先ほど作成した IoT Central アプリケーションに移動します。 エクスポートを構成するには、まず宛先を作成します。

1. **[データのエクスポート]** ページに移動し、 **[宛先]** を選択します。
1. **[+ New destination]\(+ 新しい宛先\)** を選択します。
1. 次の表の値を使用して、宛先を作成します。

    | 設定 | 値 |
    | ----- | ----- |
    | 送信先の名前 | テレメトリ イベント ハブ |
    | 変換先の型 | Azure Event Hubs |
    | 接続文字列 | 先ほどメモしたイベント ハブの接続文字列 |

    **[イベント ハブ]** は、**centralexport** と表示されます。

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="データのエクスポート先を示すスクリーンショット。":::

1. **[保存]** を選択します。

エクスポート定義を作成するには、次のようにします。

1. **[データのエクスポート]** ページに移動し、 **[+ New Export]\(+ 新しいエクスポート\)** を選択します。

1. 次の表の値を使用して、エクスポートを構成します。

    | 設定 | 値 |
    | ------- | ----- |
    | エクスポート名 | イベント ハブ エクスポート |
    | 有効 | On |
    | エクスポートするデータの種類 | 製品利用統計情報 |
    | 変換先 | **[+ Destination]\(+ 宛先\)** を選択し、 **[Telemetry event hub]\(テレメトリ イベント ハブ\)** を選択します |

1. **[保存]** を選択します。

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="データのエクスポート定義を示すスクリーンショット。":::

続行する前に、 **[データのエクスポート]** ページでエクスポートの状態が **[正常]** になるまで待ちます。

## <a name="configure-databricks-workspace"></a>Databricks ワークスペースを構成する

Azure portal で、Azure Databricks サービスに移動し、**[ワークスペースの起動]** を選択します。 ブラウザーで新しいタブが開き、ワークスペースにサインインします。

### <a name="create-a-cluster"></a>クラスターの作成

**[Azure Databricks]** ページで、共通タスクの一覧から **[新しいクラスター]** を選択します。

次の表の情報を使用して、クラスターを作成します。

| 設定 | 値 |
| ------- | ----- |
| クラスター名 | centralanalysis |
| クラスター モード | Standard |
| Databricks Runtime のバージョン | 5.5 LTS (Scala 2.11、Spark 2.4.5) |
| Python バージョン | 3 |
| 自動スケールの有効化 | いいえ |
| 終了するまでの非アクティブ状態の長さ (分) | 30 |
| worker の種類 | Standard_DS3_v2 |
| ワーカー | 1 |
| ドライバーの種類 | ワーカーと同じ |

クラスターの作成には数分かかる場合があります。クラスターの作成が完了するまで待ってから続行します。

### <a name="install-libraries"></a>ライブラリのインストール

**[クラスター]** ページで、クラスターの状態が **[実行中]** になるまで待ちます。

次の手順は、サンプルに必要なライブラリをクラスターにインポートする方法を示しています。

1. **[クラスター]** ページで、**centralanalysis** インタラクティブ クラスターの状態が **[実行中]** になるまで待ちます。

1. クラスターを選択して **[ライブラリ]** タブを選択します。

1. **[ライブラリ]** タブで、**[Install New] \(新規インストール\)** を選択します。

1. **[Install Library] \(ライブラリのインストール\)** ページで、ライブラリ ソースとして **[Maven]** を選択します。

1. **[Coordinates] \(座標\)** テキスト ボックスに、次の値を入力します。`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. **[インストール]** を選択してライブラリをクラスターにインストールします。

1. ライブラリの状態が **[Installed] \(インストール済み\)** になります。

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="インストールされたライブラリのスクリーンショット。":::

### <a name="import-a-databricks-notebook"></a>Databricks ノートブックをインポートする

IoT Central テレメトリを分析および視覚化するための Python コードを含む Databricks ノートブックをインポートするには、次の手順を使用します。

1. Databricks 環境の **[ワークスペース]** ページに移動します。 自分のアカウント名の横にあるドロップダウンを選択して **[インポート]** を選択します。

1. URL からのインポートを選択し、次のアドレスを入力します: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. ノートブックをインポートするには、**[インポート]** を選択します。

1. **[ワークスペース]** を選択して、インポートされたノートブックを表示します。

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="インポートされたノートブックのスクリーンショット。":::

5. 最初の Python セル内のコードを編集して、先ほど保存した Event Hubs 接続文字列を追加します。

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>分析を実行する

分析を実行するには、ノートブックをクラスターにアタッチする必要があります。

1. **[デタッチ]** を選択し、**centralanalysis** クラスターを選択します。
1. クラスターが実行されていない場合は、それを起動します。
1. ノートブックを開始するには、実行ボタンを選択します。

最後のセルにエラーが表示される場合があります。 その場合は、前のセルが実行されていることを確認し、いくつかのデータがストレージに書き込まれるまで少し待ってから、最後のセルをもう一度実行します。

### <a name="view-smoothed-data"></a>平滑化されたデータを表示する

ノートブックで、セル 14 まで下にスクロールして、デバイスの種類別のローリング平均湿度のプロットを表示します。 このプロットは、ストリーミング テレメトリの到着に合わせて継続的に更新されます。

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="平滑化されたテレメトリのプロットのスクリーンショット。":::

ノートブックでグラフのサイズを変更できます。

### <a name="view-box-plots"></a>ボックス プロットを表示する

ノートブックで、[ボックス プロット](https://en.wikipedia.org/wiki/Box_plot)を表示するには、セル 20 まで下にスクロールします。 ボックス プロットは静的データに基づいているため、それらを更新するにはセルを再実行する必要があります。

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="ボックス プロットのスクリーンショット。":::

ノートブックでプロットのサイズを変更できます。

## <a name="tidy-up"></a>片付ける

この手順の後で片付けて、不要なコストがかからないようにするには、Azure portal で **IoTCentralAnalysis** リソース グループを削除します。

アプリケーション内の **[管理]** ページから IoT Central アプリケーションを削除することができます。

## <a name="next-steps"></a>次のステップ

この攻略ガイドで学習した内容は次のとおりです。

* *継続的データ エクスポート* を使用してテレメトリをストリーム配信します。
* Azure Databricks 環境を作成してテレメトリ データを分析およびプロットします。

カスタム分析の作成方法が理解されたところで、次のステップとして [IoT Central デバイス ブリッジを使用して他の IoT クラウドを IoT Central に接続する](howto-build-iotc-device-bridge.md)方法の学習に進むことをお勧めします。
