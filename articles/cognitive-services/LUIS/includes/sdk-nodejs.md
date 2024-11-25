---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/17/2021
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: 6521cbef900cfda9f8c273f8064c9a1e3c2ebfe4
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110163258"
---
Node.js 用 Language Understanding (LUIS) クライアント ライブラリの用途は次のとおりです。

* アプリを作成する
* 発話の例を使用して、意図 (機械学習エンティティの 1 つ) を追加する
* アプリをトレーニングして公開する
* クエリ予測ランタイム

[リファレンス ドキュメント](/javascript/api/@azure/cognitiveservices-luis-authoring/) |  [作成](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring)および[予測](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)ライブラリ ソース コード | [作成](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)および[予測](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org)
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを入手したら、Azure portal で [Language Understanding 作成リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * アプリケーションを Language Understanding 作成に接続するには、[作成](../luis-how-to-azure-subscription.md)したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-javascript-application"></a>新しい JavaScript アプリケーションを作成する

1. コンソール ウィンドウで、アプリケーション用の新しいディレクトリを作成し、そのディレクトリに移動します。

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. `package.json` ファイルを作成して、ディレクトリを JavaScript アプリケーションとして初期化します。

    ```console
    npm init -y
    ```

1. JavaScript コード用に `index.js` という名前のファイルを作成します。

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>NPM ライブラリをインストールする

次のコマンドを一度に 1 行ずつ実行して、アプリケーション ディレクトリ内に依存関係をインストールします。

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

`package.json` は次のようになります。

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>作成オブジェクト モデル

Language Understanding (LUIS) 作成クライアントは、認証を経て作成キーの保管場所である Azure にアクセスする [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient) オブジェクトです。

## <a name="code-examples-for-authoring"></a>作成のコード例

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* アプリ - [追加](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#add-applicationcreateobject--msrest-requestoptionsbase-)、[削除](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#deletemethod-string--models-appsdeletemethodoptionalparams-)、[公開](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 発話の例 - [バッチによる追加](/javascript/api/@azure/cognitiveservices-luis-authoring/examples#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[ID による削除](/javascript/api/@azure/cognitiveservices-luis-authoring/examples#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 機能 - [語句リスト](/javascript/api/@azure/cognitiveservices-luis-authoring/features#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)の管理
* モデル - [意図](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)とエンティティの管理
* パターン - [パターン](/javascript/api/@azure/cognitiveservices-luis-authoring/pattern#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)の管理
* トレーニング - アプリの[トレーニング](/javascript/api/@azure/cognitiveservices-luis-authoring/train#trainversion-string--string--msrest-requestoptionsbase-)と[トレーニングの状態](/javascript/api/@azure/cognitiveservices-luis-authoring/train#getstatus-string--string--msrest-requestoptionsbase-)のポーリング
* [バージョン](/javascript/api/@azure/cognitiveservices-luis-authoring/versions) - クローンによる管理、エクスポート、削除

## <a name="prediction-object-model"></a>予測オブジェクト モデル

Language Understanding (LUIS) 作成クライアントは、認証を経て作成キーの保管場所である Azure にアクセスする [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient) オブジェクトです。

## <a name="code-examples-for-prediction-runtime"></a>予測ランタイムのコード例

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* `staging` または `production` スロット別の[予測](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [バージョン別の予測](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>依存関係を追加する

希望するエディターまたは IDE で `index.js` ファイルを開き、次の依存関係を追加します。

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>定型コードを追加する

1. `quickstart` メソッドとその呼び出しを追加します。 このメソッドには、残りのコードの大部分が含まれています。 このメソッドは、ファイルの末尾で呼び出されます。

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. 特に指定がなければ、quickstart メソッドの残りのコードを追加します。

## <a name="create-variables-for-the-app"></a>アプリの変数を作成する

変数の 2 つのセットを作成します。最初のセットに変更を加え、2 番目のセットはコード サンプルに表示されるままにしておきます。 

1. 作成キーとリソース名を保持する変数を作成します。

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. エンドポイント、アプリ名、バージョン、および意図名を保持する変数を作成します。

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>クライアントを認証する

自分のキーを指定して [CognitiveServicesCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials) オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient) オブジェクトを作成します。

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS アプリの作成

LUIS アプリには、意図、エンティティ、発話例を備えた自然言語処理 (NLP) モデルが含まれています。

[AppsOperation](/javascript/api/@azure/cognitiveservices-luis-authoring/apps) オブジェクトの [add](/javascript/api/@azure/cognitiveservices-luis-authoring/apps) メソッドを作成してアプリを作成します。 名前と言語のカルチャは必須のプロパティです。

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>アプリの意図の作成
LUIS アプリのモデルの中で最も重要なオブジェクトが、意図です。 意図は、ユーザーが発話において "_意図したこと_" をグループ分けしたものに対応しています。 ユーザーが質問をしたり、言葉を発したりするのは、ボット (またはその他のクライアント アプリケーション) から "_自らが意図した_" 回答を得るためです。 意図の例は、フライトの予約、目的地の天気の確認、カスタマー サービスの連絡先情報の取得などが挙げられます。

意図の一意の名前と共に [model.add_intent](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) メソッドを使用し、アプリ ID とバージョン ID、新しい意図の名前を渡します。

`intentName` の値は、「[アプリの変数を作成する](#create-variables-for-the-app)」セクションの変数の一部として `OrderPizzaIntent` にハードコーディングされます。

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>アプリのエンティティの作成

必須ではないものの、ほとんどのアプリで見られるのがエンティティです。 エンティティは、ユーザーの発話からユーザーが意図した成果を達成するために必要な情報を抽出します。 [事前構築済み](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)エンティティとカスタム エンティティにはいくつかの種類があり、それぞれが専用のデータ変換オブジェクト (DTO) モデルを備えています。  事前構築済みエンティティのうち、アプリに追加することが多いものとしては、[number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md)、[ordinal](../luis-reference-prebuilt-ordinal.md) があります。

重要なのは、エンティティには 1 つの意図だけがマークされるわけではないという点です。 これらは多くの意図に適用することができますし、多くの意図に適用されるのが普通です。 特定の意図が 1 つだけマークされるのは、ユーザーの発話の例のみです。

エンティティ作成のためのメソッドは、[Model](/javascript/api/@azure/cognitiveservices-luis-authoring/model) クラスに含まれています。 エンティティは種類ごとに固有のデータ変換オブジェクト (DTO) モデルを備えています。

エンティティ作成コードによって、サブエンティティを備えた機械学習エンティティが作成され、さまざまな特徴が `Quantity` サブエンティティに適用されます。

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="ポータルの部分的なスクリーンショット。サブエンティティを備えた機械学習エンティティが作成され、さまざまな特徴が `Quantity` サブエンティティに適用される。":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Quantity サブエンティティに特徴を割り当てるために、次のメソッドを `quickstart` メソッドの上に配置して、Quantity サブエンティティの ID を検索します。

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>意図に対する発話の例の追加

アプリでは、発話の意図を特定し、エンティティを抽出するために、発話の例が必要になります。 例では、特定の 1 つの意図をターゲットとし、すべてのカスタム エンティティをマークする必要があります。 事前構築済みエンティティをマークする必要はありません。

[ExampleLabelObject](/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject) オブジェクトの一覧 (発話の例 1 件につき 1 つのオブジェクト) を作成し、発話の例を追加します。 それぞれの例では、エンティティの名前とエンティティの値から成る名前と値のペアのディクショナリを使用し、エンティティをすべてマークする必要があります。 エンティティの値は、発話の例のテキストに表示されているものと正確に一致している必要があります。

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="ポータルでのラベル付けされた発話の例を示す部分的なスクリーンショット。":::

アプリ ID、バージョン ID、および例を指定して [examples.add](/javascript/api/@azure/cognitiveservices-luis-authoring/examples) を呼び出します。

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>アプリをトレーニングする

モデルの作成を終えたら、このバージョンのモデルに合わせて LUIS アプリをトレーニングする必要があります。 トレーニングを済ませたモデルは、[コンテナー](../luis-container-howto.md)で使用したり、ステージング スロットまたは製品スロットに[公開](../luis-how-to-publish-app.md)したりできます。

[train.trainVersion](/javascript/api/@azure/cognitiveservices-luis-authoring/train#trainversion-string--string--msrest-requestoptionsbase-) メソッドでは、アプリ ID とバージョン ID が必要です。

このクイックスタートで示したような非常に小さなモデルであれば、トレーニングが短時間で完了します。 運用レベルのアプリケーションの場合には、アプリのトレーニングに、トレーニングが成功したかどうかと、そのタイミングを確認するための [get_status](/javascript/api/@azure/cognitiveservices-luis-authoring/train#getstatus-string--string--msrest-requestoptionsbase-) メソッドに対するポーリング呼び出しが含まれます。 応答は、オブジェクトごとの状態を格納した [ModelTrainingInfo](/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo) オブジェクトの一覧です。 トレーニングが完了したと判定されるには、オブジェクトがすべて成功している必要があります。

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>アプリを運用スロットに公開する

[app.publish](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#publish-string--applicationpublishobject--msrest-requestoptionsbase-) メソッドを使用して、LUIS アプリを公開します。 これにより、トレーニング済みの最新バージョンがエンドポイントの指定されたスロットに公開されます。 クライアント アプリケーションではこのエンドポイントを使用して、意図の予測とエンティティの抽出の対象となるユーザー発話を送信します。

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>予測ランタイム クライアントを認証する

msRest.ApiKeyCredentials オブジェクトに自分のキーを指定し、これをエンドポイントと共に使用して [LUIS.LUISRuntimeClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient) オブジェクトを作成します。

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>ランタイムから予測を取得する

次のコードを追加して、予測ランタイムへの要求を作成します。 ユーザーの発話は、[predictionRequest](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest) オブジェクトの一部です。

**[luisRuntimeClient.prediction.getSlotPrediction](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** メソッドには、アプリ ID、スロット名、要求を満たす予測要求オブジェクトなど、いくつかのパラメーターが必要です。 詳細、すべての意図の表示、ログなど、その他のオプションは省略可能です。

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node index.js` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```
