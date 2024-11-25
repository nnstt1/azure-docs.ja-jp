---
title: ストリーミング イベントをキャプチャする - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs でイベントのストリーミングをキャプチャするキャプチャ機能の概要を示します。
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: fbc151b7dafe5c2f29f0101122b3936ae162a734
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735261"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする
Azure Event Hubs を利用すると、Event Hubs のストリーミング データをご自分で選択した Gen 1 または Gen 2 の [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) アカウントまたは [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) アカウントに自動的に配信できます。その際、時間やサイズの間隔を柔軟に指定できます。 Capture の設定は手軽で、実行しても管理コストは発生しません。また、Event Hubs の Standard レベルでの[スループット ユニット数](event-hubs-scalability.md#throughput-units)または Premium レベルでの[処理ユニット数](event-hubs-scalability.md#processing-units)に応じて、自動的にスケーリングされます。 Event Hubs Capture はストリーミング データを Azure に読み込む最も簡単な方法であり、これを利用すれば、データのキャプチャではなくデータの処理に注力できるようになります。

:::image type="content" source="./media/event-hubs-features/capture.png" alt-text="Event Hubs のデータの Azure Storage または Azure Data Lake Storage へのキャプチャを示す図":::

> [!NOTE]
> Azure Data Lake Storage **Gen 2** を使用するように Event Hubs Capture を構成することは、Azure Blob Storage を使用するように構成することと同じです。 詳細については、[Event Hubs Capture の構成](event-hubs-capture-enable-through-portal.md)に関する記事をご覧ください。 

Event Hubs Capture を利用すると、リアルタイムおよびバッチベースのパイプラインを同じストリームで処理できます。 すなわち、変化するニーズに合わせて拡大可能なソリューションを構築できます。 将来のリアルタイム処理を視野に入れてバッチベースのシステムを構築している場合も、既存のリアルタイム ソリューションに効率的なコールド パスを追加したいと考えている場合も、Event Hubs Capture ならストリーミング データの操作が容易です。

> [!IMPORTANT]
> 宛先となるストレージ (Azure Storage または Azure Data Lake Storage) は、イベント ハブと同じサブスクリプションに存在する必要があります。 

## <a name="how-event-hubs-capture-works"></a>Event Hubs Capture の仕組み

Event Hubs は分散ログに似た、テレメトリの受信のための持続的バッファーです。 Event Hubs でのスケーリングの鍵となるのは、[パーティション分割されたコンシューマー モデル](event-hubs-scalability.md#partitions)です。 各パーティションは独立したデータのセグメントであり、個別に使用されます。 このデータは、構成可能なリテンション期間に基づいて、所定のタイミングで破棄されます。 そのため、特定のイベント ハブが "いっぱい" になることはありません。

Event Hubs Capture を使用すると、キャプチャされたデータを格納するための独自の Azure Blob Storage アカウントとコンテナー、または Azure Data Lake Storage アカウントを指定することができます。 これらのアカウントのリージョンは、イベント ハブと同じであっても、別のリージョンであってもかまわないため、Event Hubs Capture 機能の柔軟性がさらに高まります。

キャプチャされたデータは [Apache Avro][Apache Avro] 形式で書き込まれます。これはコンパクトで高速なバイナリ形式で、インライン スキーマを備えた便利なデータ構造になっています。 この形式は Hadoop エコシステム、Stream Analytics、Azure Data Factory で幅広く使用されています。 Avro の操作の詳細は、この記事の後半に記載してあります。

### <a name="capture-windowing"></a>キャプチャのウィンドウ化

Event Hubs Capture では、キャプチャを制御するウィンドウを設定できます。 このウィンドウは "先に来たものが優先されるポリシー" が適用される最小サイズと時間の構成です。つまり、先に生じたトリガーによってキャプチャ操作が行われます。 15 分/100 MB のキャプチャ ウィンドウを設定してあるときに 1 MB/秒で送信する場合は、サイズのウィンドウが時間のウィンドウよりも先にトリガーとなります。 各パーティションのキャプチャは個別に行われ、完了したブロック BLOB がキャプチャ時に (キャプチャが実行されるタイミングとなったときに) 書き込まれます。 ストレージの名前付け規則は次のとおりです。

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

日付値はゼロ パディングされます。ファイル名の例は次のようになります。

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Azure Storage Blob が一時的に利用できなくなった場合、Event Hubs Capture では、イベント ハブで構成されたデータ保持期間の間、データが保持され、ストレージ アカウントが再び利用可能になったら、データがバックフィルされます。

### <a name="scaling-throughput-units-or-processing-units"></a>スループット ユニット数または処理ユニット数のスケーリング

Event Hubs の Standard レベルでは、トラフィックは[スループット ユニット数](event-hubs-scalability.md#throughput-units)によって制御され、Premium レベルの Event Hubs では、[処理ユニット数](event-hubs-scalability.md#processing-units)によって制御されます。 Event Hubs Capture では、内部の Event Hubs ストレージからデータが直接コピーされるため、スループット ユニットまたは処理ユニットのエグレス クォータが回避され、他の処理リーダー (Stream Analytics や Spark など) に対するエグレスを節約できます。

構成されると、Event Hubs Capture は最初のイベント送信時に自動的に実行され、そのまま実行を継続します。 ダウンストリーム処理で処理が行われていることを把握しやすいように、Event Hubs はデータがないときは空のファイルを書き込みます。 このプロセスにより、バッチ プロセッサに提供可能な、予測しやすいパターンとマーカーが得られます。

## <a name="setting-up-event-hubs-capture"></a>Event Hubs Capture の設定

[Azure Portal](https://portal.azure.com) または Azure Resource Manager テンプレートを使用して、イベント ハブの作成時に Capture を構成できます。 詳細については、次の記事を参照してください。

- [Azure Portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)
- [Azure Resource Manager テンプレートを使用して、イベント ハブを含んだ Event Hubs 名前空間を作成して Capture を有効にする](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> 既存のイベント ハブの Capture 機能を有効にすると、この機能が有効になった **後** に、イベント ハブに到着したイベントが取り込まれるようになります。 この機能を有効にする前にイベント ハブに存在していたイベントは取り込まれません。 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>キャプチャしたファイルの確認と Avro の操作

Event Hubs Capture では、構成された時間枠で指定された Avro 形式のファイルが作成されます。 これらのファイルは、 [Azure Storage Explorer][Azure Storage Explorer]などの任意のツールを使用して確認できます。 また、ローカルにダウンロードして操作することができます。

Event Hubs Capture によって生成されたファイルには、次の Avro スキーマがあります。

![Avro スキーマ][3]

Avro ファイルを調べるには、Apache の [Avro Tools][Avro Tools] jar を使うと簡単です。 また、軽量 SQL 主導のエクスペリエンス向けの [Apache Drill][Apache Drill] または [Apache Spark][Apache Spark] を使用して、取り込まれたデータに対する複雑な分散処理を実行することもできます。 

### <a name="use-apache-drill"></a>Apache Drill を使用する

[Apache Drill][Apache Drill] は、任意の場所にある構造化および半構造化データのクエリを実行できる、"ビッグ データ探索のためのオープンソースの SQL クエリ エンジン" です。 このエンジンは、スタンドアロン ノードとして、またはパフォーマンスを向上させるために巨大なクラスターとして実行できます。

Azure Blob Storage へのネイティブ サポートを使用できるため、Avro ファイル内のデータにクエリを実行することが容易になります。次のドキュメントを参照してください。

[Apache Drill: Azure Blob Storage プラグイン][Apache Drill: Azure Blob Storage Plugin]

キャプチャされたファイルに簡単にクエリするには、コンテナーを使用して Apache Drill を有効にした状態で VM を作成および実行して Azure Blob Storage にアクセスできます。 次の例を参照してください: [Event Hubs Capture を使用した大規模なストリーミング](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture-databricks-delta)。

### <a name="use-apache-spark"></a>Apache Spark を使用する

[Apache Spark][Apache Spark] は、"大規模なデータ処理のための統合された分析エンジン" です。 さまざまな言語 (SQL を含む) がサポートされ、Azure Blob Storage に容易にアクセスできます。 Azure で Apache Spark を実行するにはいくつかのオプションがあり、いずれも Azure Blob Storage への容易なアクセスを提供します。

- [HDInsight: Azure Storage 内のファイルのアドレス指定][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Avro ツールを使用する

[Avro ツール][Avro Tools]は、jar パッケージとして入手できます。 この jar ファイルをダウンロードしたら、次のコマンドを実行することによって、特定の Avro ファイルのスキーマを表示できます。

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

このコマンドによって次の情報が返されます。

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

また、Avro ツールを使用してファイルを JSON 形式に変換し、その他の処理を実行することもできます。

より高度な処理を実行するには、お好みのプラットフォーム用の Avro をダウンロードしてインストールしてください。 この記事の執筆時点では、C、C++、C\#、Java、NodeJS、Perl、PHP、Python、Ruby 向けの実装があります。

Apache Avro には、[Java][Java] と [Python][Python] 向けの完全な入門ガイドが用意されています。 [Event Hubs Capture の概要](event-hubs-capture-python.md)に関する記事を読むこともできます。

## <a name="how-event-hubs-capture-is-charged"></a>Event Hubs Capture に対する課金方法

Event Hubs Capture は、[スループット ユニット](event-hubs-scalability.md#throughput-units) (Standard レベル) または[処理ユニット](event-hubs-scalability.md#processing-units) (Premium レベル) と同様に、時間単位の料金で課金されます。 料金は、その名前空間で購入されたスループット ユニットまたは処理ユニットの数に正比例します。 スループット ユニットまたは処理ユニットが増減すると、Event Hubs Capture の測定もそれに応じたパフォーマンスを提供するために調整されます。 測定は連携して行われます。 料金の詳細については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」をご覧ください。 

エグレス クォータは別途請求されるため、Capture では消費されません。 

## <a name="integration-with-event-grid"></a>Event Grid との統合 

Event Hubs 名前空間をソースとして Azure Event Grid サブスクリプションを作成できます。 以下のチュートリアルでは、イベント ハブをソースとして、また Azure Functions アプリをシンクとして使用して、Event Grid サブスクリプションを作成する方法を示します。[Event Grid と Azure Functions を使用して、キャプチャされた Event Hubs データを処理し、Azure Synapse Analytics に移行します](store-captured-data-data-warehouse.md)。

## <a name="next-steps"></a>次のステップ
Event Hubs Capture は Azure にデータを取得する最も簡単な方法です。 Azure Data Lake、Azure Data Factory、Azure HDInsight を利用することで、使い慣れたツールとプラットフォームを使用して、必要なスケールでバッチ処理やその他の分析を実行できます。

Azure portal および Azure Resource Manager テンプレートを使用してこの機能を有効にする方法を学習してください。

- [Azure Portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)
- [Azure Resource Manager テンプレートを使用した Event Hubs Capture の有効化](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
