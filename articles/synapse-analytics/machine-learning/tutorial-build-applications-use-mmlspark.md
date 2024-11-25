---
title: 'チュートリアル: Synapse Machine Learning を使用して機械学習アプリケーションをビルドする'
description: Synapse Machine Learning を使用して、Azure Synapse Analytics で機械学習アプリケーションを作成する方法について説明します。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 03d7aec55e7a6146346ebbcc746ecbbd75b81d3f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318486"
---
# <a name="tutorial-build-machine-learning-applications-using-synapse-machine-learning"></a>チュートリアル: Synapse Machine Learning を使用して機械学習アプリケーションをビルドする

この記事では、Synapse Machine Learning ([SynapseML](https://github.com/microsoft/SynapseML)) を使用して機械学習アプリケーションを作成する方法について説明します。 SynapseML によって Apache Spark の分散機械学習ソリューションが拡張され、[Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md)、[OpenCV](https://opencv.org/)、[LightGBM](https://github.com/Microsoft/LightGBM) などの多くのディープ ラーニング ツールとデータ サイエンス ツールが追加されます。  SynapseML を使用すると、効果的で拡張性の高い予測と分析のモデルをさまざまな Spark データ ソースから構築できます。
Synapse Spark には、次のような組み込みの SynapseML ライブラリが用意されています。

- [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) – 機械学習用のライブラリ サービスであり、ツイートの感情分析などのテキスト分析を可能にします。
- [Cognitive Services on Spark](https://arxiv.org/abs/1810.08744) - 異常検出などの認知データ モデリング サービスのソリューション設計を得るために、Azure Cognitive Services の機能を SparkML パイプラインに組み合わせます。
- [LightGBM](https://github.com/Microsoft/LightGBM) - LightGBM は、ツリー ベースの学習アルゴリズムを使用する勾配ブースティング フレームワークです。 分散と効率の向上を目指して設計されています。
- Conditional KNN - 条件付きクエリを使用したスケーラブル KNN モデル。
- HTTP on Spark – Spark と HTTP プロトコルベースのアクセシビリティを統合するための分散マイクロサービス オーケストレーションを可能にします。

このチュートリアルでは、SynapseML で Azure Cognitive Services を使用するサンプルについて説明します。 

- Text Analytics - 文のセットのセンチメント (またはムード) を取得します。
- Computer Vision - 画像のセットに関連付けられたタグ (1 単語の描写) を取得します。
- Bing Image Search - Web で自然言語クエリに関連する画像を検索します。
- Anomaly Detector - 時系列データ内の異常を検出します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件 

- Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
- Azure Synapse Analytics ワークスペースの Spark プール。 詳細については、[Azure Synapse での Spark プールの作成](../get-started-analyze-spark.md)に関する記事を参照してください。
- [Azure Synapse での Cognitive Services の構成](./tutorial-configure-cognitive-services-synapse.md)に関するチュートリアルで説明されている事前構成手順。


## <a name="get-started"></a>開始
はじめに、SynapseML をインポートし、サービス キーを構成します。 

```python
import synapse.ml

from synapse.ml.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
cognitive_service_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_SERVICE_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME") 
# A Bing Search v7 subscription key
bingsearch_service_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_BING_SEARCH_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME")
# An Anomaly Dectector subscription key
anomalydetector_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_ANOMALY_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME")


```


## <a name="text-analytics-sample"></a>Text Analytics のサンプル

[Text Analytics](../../cognitive-services/text-analytics/index.yml) サービスには、テキストからインテリジェントな分析情報を抽出するためのアルゴリズムがいくつか用意されています。 たとえば、指定された入力テキストのセンチメントを見つけることができます。 このサービスでは、0.0 と 1.0 の間のスコアが返されます。低いスコアは否定的なセンチメントを示し、高いスコアは肯定的なセンチメントを示します。 このサンプルでは、3 つの単純な文を使用し、それぞれのセンチメントを返します。

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia") # Set the location of your cognitive service
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>予想される結果

|text | センチメント|
|--|--|
| I am frustrated by this rush hour traffic! | 否定的 |
| this is a dog | 中立 |
| I am so happy today, its sunny! | 肯定的 |

## <a name="computer-vision-sample"></a>Computer Vision のサンプル
[Computer Vision](../../cognitive-services/computer-vision/index.yml) は、画像を分析して、顔、物体、自然言語による記述などの構造を識別します。 このサンプルでは、次の画像にタグを付けます。 タグは、認識可能な物体、人物、風景、アクションなど、画像内のものを 1 単語で表したものです。


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia") # Set the location of your cognitive service
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>予想される結果

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skating, person, man, outdoor, riding, sport, skateboard, young, board, shirt, air, park, boy, side, jumping, ramp, trick, doing, flying] |

## <a name="bing-image-search-sample"></a>Bing Image Search のサンプル
[Bing Image Search](../../cognitive-services/bing-image-search/overview.md) では、Web を検索して、ユーザーの自然言語クエリに関連する画像を取得します。 このサンプルでは、引用付きの画像を検索するテキスト クエリを使用しています。 クエリに関連する写真を含む画像 URL のリストが返されます。


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>予想される結果

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Anomaly Detector のサンプル

[Anomaly Detector](../../cognitive-services/anomaly-detector/index.yml) は、時系列データ内の不規則性を検出するのに適しています。 このサンプルでは、このサービスを使用して、時系列全体での異常を検出します。

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>予想される結果

|timestamp | value | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826.0|false|
|1972-02-01T00:00:00Z|799.0|false|
|1972-03-01T00:00:00Z|890.0|false|
|1972-04-01T00:00:00Z|900.0|false|
|1972-05-01T00:00:00Z|766.0|false|
|1972-06-01T00:00:00Z|805.0|false|
|1972-07-01T00:00:00Z|821.0|false|
|1972-08-01T00:00:00Z|20000.0|true|
|1972-09-01T00:00:00Z|883.0|false|
|1972-10-01T00:00:00Z|898.0|false|
|1972-11-01T00:00:00Z|957.0|false|
|1972-12-01T00:00:00Z|924.0|false|
|1973-01-01T00:00:00Z|881.0|false|
|1973-02-01T00:00:00Z|837.0|false|
|1973-03-01T00:00:00Z|9000.0|true|


## <a name="speech-to-text-sample"></a>音声テキスト変換の例

[音声テキスト変換](../../cognitive-services/speech-service/index-text-to-speech.yml)サービスでは、音声のストリームまたはファイルをテキストに変換します。 この例では、1 つのオーディオ ファイルをテキストに変換します。 

```python
# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("northeurope") # Set the location of your cognitive service
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```
### <a name="expected-results"></a>予想される結果

|url | DisplayText |
|--|--|
| `https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav` | Custom Speech には、オーディオ データを Custom Speech ポータルから得られた認識結果と比較することによって、モデルの認識品質を視覚的に検査するツールがあります。 You can playback uploaded audio and determine if the provided recognition result is correct. This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data.|

## <a name="clean-up-resources"></a>リソースをクリーンアップする
Spark インスタンスがシャットダウンされるようにするには、接続されているセッション (ノートブック) を終了します。 プールは、Apache Spark プールに指定されている **アイドル時間** に達したときにシャットダウンされます。 また、ノートブックの右上にあるステータス バーから **[セッションの停止]** を選択することもできます。

![セッションの停止を示すスクリーンショット](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>次のステップ

* [Synapse サンプル ノートブックをチェックアウトする](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [SynapseML GitHub リポジトリ](https://github.com/microsoft/SynapseML)
