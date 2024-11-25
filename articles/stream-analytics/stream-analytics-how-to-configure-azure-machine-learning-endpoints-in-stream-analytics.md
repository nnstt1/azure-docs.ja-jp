---
title: Azure Stream Analytics で Machine Learning Studio (クラシック) エンドポイントを使用する
description: この記事では、Azure Stream Analytics で Machine Learning のユーザー定義関数を使う方法について説明します。
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: d16ad0ea147343b0880ba0b50e2365ac47ace31d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003525"
---
# <a name="machine-learning-studio-classic-integration-in-stream-analytics"></a>Machine LearningStream Analytics での Studio (クラシック) 統合
Stream Analytics は、Machine Learning Studio (クラシック) エンドポイントを呼び出すユーザー定義関数をサポートしています。 この機能でサポートされている REST API の詳細については、「 [Stream Analytics の REST API ライブラリ](/rest/api/streamanalytics/)」を参照してください。 この資料では、Stream Analytics にこの機能を正しく実装するために必要な補足的な情報を示します。 チュートリアルも用意しており、 [ここ](stream-analytics-machine-learning-integration-tutorial.md)から確認できます。

## <a name="overview-machine-learning-studio-classic-terminology"></a>概要: Machine Learning Studio (クラシック) の用語
Microsoft Machine Learning Studio (クラシック) は、データに対して予測分析ソリューションを構築、テスト、デプロイするために使用できる、コラボレーションのためのドラッグアンドドロップツールを提供します。 このツールは *Machine Learning Studio (クラシック)* と呼ばれます。 Studio (クラシック) は、Machine Learning リソースと通信し、設計を簡単に構築、テストおよび反復処理するために使用できます。 これらのリソースとその定義のとおりです。

* **ワークスペース**: *ワークスペース* は、その他のすべての Machine Learning リソースを管理および制御するためにそれらを保持するコンテナーです。
* **実験**: *実験* は、データセットを使用して機械学習モデルをトレーニングするデータ科学者によって作成されます。
* **エンドポイント**: *エンドポイント* は、機能を入力として取得し、特定の機械学習モデルを適用し、スコア付けされた出力を返す Studio (クラシック) のオブジェクトです。
* **スコア付け Web サービス**: *スコア付け Web サービス* とは、前述のエンドポイントの集合です。

各エンドポイントには、バッチの実行と同期の実行用の API があります。 Stream Analytics では、同期実行を使用します。 特定のサービスには Machine Learning Studio (クラシック) の[要求/応答サービス](../machine-learning/classic/consume-web-services.md)という名前が付けられます。

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Stream Analytics のジョブに必要な Studio (クラシック) リソース
Stream Analytics のジョブを正常に処理するには、Request/Response のエンドポイント、 [apikey](../machine-learning/classic/consume-web-services.md)および Swagger 定義のすべてが必要になります。 Stream Analytics には、Swagger エンドポイントの URL を作成し、インターフェイスを検索し、既定の UDF の定義をユーザーに返す追加エンドポイントがあります。

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>REST API を使用した Stream Analytics および Studio (クラシック) UDF の構成
REST API を使用すると、ジョブを構成して Studio (クラシック) 関数を呼び出すことができます。 手順は次のとおりです。

1. Stream Analytics のジョブの作成
2. 入力の定義
3. 出力の定義
4. ユーザー定義関数 (UDF) の作成
5. UDF を呼び出す Stream Analytics 変換の記述
6. ジョブの開始

## <a name="creating-a-udf-with-basic-properties"></a>基本的なプロパティを持つ UDF の作成
例として、次のサンプルコードでは、Machine Learning Studio (クラシック) エンドポイントにバインドする *newudf* という名前のスカラー udf を作成します。 *エンドポイント* (サービス URI) は、選択したサービスの API のヘルプ ページにあり、*apiKey* はサービスのメイン ページにあります。

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

要求本文の例

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>既定の UDF 用の RetrieveDefaultDefinition エンドポイントの呼び出し
スケルトン UDF を作成したら、UDF を完全に定義する必要があります。 RetrieveDefaultDefinition エンドポイントは、Machine Learning Studio (クラシック) エンドポイントにバインドされているスカラー関数の既定の定義を取得するのに役立ちます。 以下のペイロードでは、Studio (クラシック) エンドポイントにバインドされているスカラー関数の既定の UDF 定義を取得する必要があります。 PUT 要求で既に渡されているので、実際のエンドポイントは指定されません。 Stream Analytics は、要求で明示的に渡される場合、そのエンドポイントを呼び出します。 そうでない場合、最初に参照したものを使用します。 ここでは UDF は単一の文字列型のパラメーター (文) を取り、その文の "sentiment" ラベルである文字列型の単一の出力を返します。

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

要求本文の例

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

このサンプル出力は、次のようになります。

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>応答を使用した UDF の PATCH
ここで前の応答を使用して、以下のように UDF に対して PATH を実行します。

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

要求本文 (RetrieveDefaultDefinition からの出力)

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>UDF を呼び出すための Stream Analytics 変換の実装
ここで各入力イベントにつき (scoreTweet という名前の) UDF に対し問い合わせを行い、そのイベントの応答を出力に書き込みます。

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>ヘルプの参照
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)
