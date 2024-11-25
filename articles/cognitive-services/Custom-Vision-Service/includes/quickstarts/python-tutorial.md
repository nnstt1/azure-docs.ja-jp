---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: e4aea3bcf4ae07d6b397e4318763a81f1e694778
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2021
ms.locfileid: "107931666"
---
Python 用 Custom Vision クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、画像分類モデルを構築するためのコード例を試してみましょう。 プロジェクトを作成し、タグを追加し、プロジェクトをトレーニングして、プロジェクトの予測エンドポイント URL を使用してプログラムでテストします。 この例は、独自の画像認識アプリを構築するためのテンプレートとしてご利用ください。

> [!NOTE]
> コードを記述 "_せずに_" 分類モデルの構築とトレーニングを行う場合は、[ブラウザーベースのガイダンス](../../getting-started-build-a-classifier.md)を参照してください。

Python 用 Custom Vision クライアント ライブラリを使用すると、次のことができます。

* 新しい Custom Vision プロジェクトを作成する
* プロジェクトにタグを追加する
* 画像をアップロードし、タグ付けする
* プロジェクトをトレーニングする
* 現在のイテレーションを公開する
* 予測エンドポイントをテストする

[リファレンス ドキュメント](/python/api/overview/azure/cognitiveservices/customvision) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [パッケージ (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [サンプル](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python のインストールには、[pip](https://pip.pypa.io/en/stable/) が含まれている必要があります。 pip がインストールされているかどうかを確認するには、コマンド ラインで `pip --version` を実行します。 最新バージョンの Python をインストールして pip を入手してください。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision リソースを作成"  target="_blank">Custom Vision リソースを作成</a>し、トレーニングおよび予測リソースを作成し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Custom Vision に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python 用の Custom Vision で画像分析アプリを作成するには、Custom Vision のクライアント ライブラリが必要です。 Python のインストール後、PowerShell またはコンソール ウィンドウで次のコマンドを実行します。

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python ファイルを作成して次のライブラリをインポートします。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py) にあり、このクイックスタートのコード例が含まれています。

リソースの Azure エンドポイントとサブスクリプション キー用の変数を作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Custom Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **キーとエンドポイント** のページにあります。 トレーニング リソース用の API エンドポイントに加え、トレーニング リソース用と予測リソース用の両方のキーを取得する必要があります。
>
> 予測リソース ID は、Azure portal の対象のリソースの **[プロパティ]** タブに、**リソース ID** として表示されます。
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関する記事を参照してください。

## <a name="object-model"></a>オブジェクト モデル

|名前|説明|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | このクラスでは、モデルの作成、トレーニング、および公開を処理します。 |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| このクラスでは、画像分類予測のために、モデルに対するクエリ実行を処理します。|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| このクラスでは、単一の画像に対して単一の物体予測を定義します。 これには、オブジェクト ID と名前、オブジェクトの境界ボックスの位置、および信頼度スコアのプロパティが含まれます。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Custom Vision クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [新しい Custom Vision プロジェクトを作成する](#create-a-new-custom-vision-project)
* [プロジェクトにタグを追加する](#add-tags-to-the-project)
* [画像をアップロードし、タグ付けする](#upload-and-tag-images)
* [プロジェクトをトレーニングする](#train-the-project)
* [現在のイテレーションを公開する](#publish-the-current-iteration)
* [予測エンドポイントをテストする](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>クライアントを認証する

エンドポイントとキーを使用してトレーニングと予測のクライアントをインスタンス化します。 自分のキーを指定して **ApiKeyServiceClientCredentials** オブジェクトを作成し、それを自分のエンドポイントと共に使用して [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) および [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient) オブジェクトを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>新しい Custom Vision プロジェクトを作成する

新しい Custom Vision Service プロジェクトを作成するための次のコードをスクリプトに追加します。 

プロジェクトを作成するときに他のオプションを指定するには、[create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) メソッドを参照してください ([分類子の構築](../../getting-started-build-a-classifier.md)に関する Web ポータル ガイドで説明されています)。  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>プロジェクトにタグを追加する

プロジェクトに分類タグを追加するには、次のコードを追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

最初に、このプロジェクト用のサンプル画像をダウンロードします。 [サンプル画像フォルダー](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)の内容をお使いのローカル デバイスに保存します。

> [!NOTE]
> トレーニングを完了するために、より広範なイメージのセットが必要ですか。 Trove (Microsoft Garage プロジェクト) を使用すると、トレーニング目的で画像のセットを収集して購入することができます。 画像を収集したら、それらをダウンロードした後、通常の方法で Custom Vision プロジェクトにインポートできます。 詳細については、[Trove ページ](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)を参照してください。

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、それぞれの画像を対応するタグと共にアップロードします。 1 回のバッチで最大 64 個の画像をアップロードできます。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> 画像のパスは、Cognitive Services Python SDK Samples リポジトリをダウンロードした場所に応じて変更する必要があります。

## <a name="train-the-project"></a>プロジェクトをトレーニングする

このコードにより、予測モデルの最初のイテレーションが作成されます。 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> 選択したタグでトレーニングする
>
> 必要に応じて、適用したタグのサブセットのみでトレーニングできます。 ある特定のタグが十分に適用されていないが、他のタグが十分に適用されている場合に、これを行うことができます。 **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** 呼び出しで、使用するタグの ID 文字列の一覧に、オプションのパラメーター *selected_tags* を設定します。 モデルは、そのリストのタグのみを認識するようにトレーニングします。

## <a name="publish-the-current-iteration"></a>現在のイテレーションを公開する

イテレーションは、公開されるまで予測エンドポイントで利用できません。 次のコードでは、モデルの現在のイテレーションをクエリの実行に使用できるようにします。 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>予測エンドポイントをテストする

画像を予測エンドポイントに送信し、予測を取得するには、ファイルの末尾に以下のコードを追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>アプリケーションの実行

*CustomVisionQuickstart.py* を実行します。

```powershell
python CustomVisionQuickstart.py
```

アプリケーションの出力は次のテキストのようになります。

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

**<base_image_location>/images/Test/** 内のテスト画像にタグが適切に付けられていることを確認してください。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>次のステップ

以上、画像の分類処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](../../test-your-model.md)

* Custom Vision とは
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py) にあります。
* [SDK のリファレンス ドキュメント](/python/api/overview/azure/cognitiveservices/customvision)
