---
title: Face Go クライアント ライブラリのクイックスタート
description: Go 用 Face クライアント ライブラリを使用して、顔の検出、類似検索 (画像による顔検索)、顔の識別 (顔認識検索)、顔データの移行を行います。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 5534fc3b82119295dc744e98054fead2f12d4a7d
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442287"
---
Go 用 Face クライアント ライブラリを使用して顔認識を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Face サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

Go 用 Face サービス クライアント ライブラリは、次の目的で使用します。

* [顔を検出して分析する](#detect-and-analyze-faces)
* [顔を識別する](#identify-a-face)
* [顔を確認する](#verify-faces)
* [似た顔を探す](#find-similar-faces)

[リファレンス ドキュメント](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK のダウンロード](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Go](https://golang.org/dl/)
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face リソースを作成"  target="_blank">Face リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイントを取得したら、キーとエンドポイントの[環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)し、それぞれ `FACE_SUBSCRIPTION_KEY` および `FACE_ENDPOINT` という名前を付けます。

## <a name="setting-up"></a>設定

### <a name="create-a-go-project-directory"></a>Go プロジェクト ディレクトリを作成する

コンソール ウィンドウ (cmd、PowerShell、ターミナル、Bash) で、Go プロジェクト用に `my-app` という名前の新しいワークスペースを作成し、そこに移動します。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

ワークスペースには次の 3 つのフォルダーが格納されます。

* **src** - このディレクトリには、ソース コードとパッケージが格納されます。 `go get` コマンドを使用してインストールされるパッケージはすべてこのフォルダーに入ります。
* **pkg** - このディレクトリには、コンパイル済みの Go パッケージ オブジェクトが格納されます。 これらのファイルにはいずれも `.a` という拡張子が付きます。
* **bin** - このディレクトリには、`go install` を実行するときに作成されたバイナリ実行可能ファイルが格納されます。

> [!TIP]
> Go ワークスペースの構造の詳細については、[Go 言語のドキュメント](https://golang.org/doc/code.html#Workspaces)を参照してください。 このガイドには、`$GOPATH` と `$GOROOT` の設定に関する情報が記載されています。

### <a name="install-the-client-library-for-go"></a>Go 用クライアント ライブラリをインストールする

次に、Go 用クライアント ライブラリをインストールします。

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

または、dep を使用している場合は、リポジトリ内で次を実行します。

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go アプリケーションを作成する

次に、**src** ディレクトリに `sample-app.go` という名前のファイルを作成します。

```bash
cd src
touch sample-app.go
```

普段使用している IDE またはテキスト エディターで `sample-app.go` を開きます。 次に、パッケージの名前を追加し、次のライブラリをインポートします。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

次に、さまざまな Face サービス操作を実行するコードの追加を開始します。

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Face サービス Go クライアント ライブラリの主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | このクラスは、Face サービスを使用するための承認を表し、すべての Face 機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|このクラスは、人間の顔に対して実行できる基本的な検出と認識のタスクを処理します。 |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|このクラスは、画像内の 1 つの顔から検出されたすべてのデータを表します。 これを使用して、顔に関する詳細情報を取得できます。|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|このクラスは、クラウドに格納された **FaceList** コンストラクトを管理します。これには、さまざまな顔のセットが格納されます。 |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| このクラスは、クラウドに格納された **Person** コンストラクトを管理します。これには、1 人の人物に属している顔のセットが格納されます。|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| このクラスは、クラウドに格納された **PersonGroup** コンストラクトを管理します。これには、さまざまな **Person** オブジェクトのセットが格納されます。 |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|このクラスは、スナップショット機能を管理します。 これを使用すると、クラウドベースのすべての Face データを一時的に保存し、そのデータを新しい Azure サブスクリプションに移行することができます。 |

## <a name="code-examples"></a>コード例

これらのコード サンプルは、Go 用 Face サービス クライアント ライブラリを使用して基本的なタスクを実行する方法を示しています。

* [クライアントを認証する](#authenticate-the-client)
* [顔を検出して分析する](#detect-and-analyze-faces)
* [顔を識別する](#identify-a-face)
* [顔を確認する](#verify-faces)
* [似た顔を探す](#find-similar-faces)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE] 
> このクイックスタートでは、それぞれ `FACE_SUBSCRIPTION_KEY` および `FACE_ENDPOINT` という名前の、Face キーとエンドポイントの[環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)してあることを前提としています。

**main** 関数を作成し、その関数に次のコードを追加して、エンドポイントとキーでクライアントをインスタンス化します。 キーを使用して **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** オブジェクトを作成し、それをエンドポイントと共に使用して、 **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** オブジェクトを作成します。 また、このコードでは、クライアント オブジェクトの作成に必要なコンテキスト オブジェクトをインスタンス化します。 さらに、このクイックスタートのいくつかのサンプル画像があるリモートの場所を定義しています。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-and-analyze-faces"></a>顔を検出して分析する

顔検出は、顔分析と ID 検証の最初の手順として必要です。 このセクションでは、追加の顔属性データを返す方法について説明します。 顔の識別または検証のためにだけ顔検出を行う場合は、先のセクションまでスキップしてください。


**main** メソッド内に次のコードを追加します。 このコードは、リモートのサンプル画像を定義し、画像から抽出する顔の特徴を指定します。 また、検出された顔からどの AI モデルを使用してデータを抽出するかも指定しています。 それらの選択については、[認識モデルの指定](../../Face-API-How-to-Topics/specify-recognition-model.md)に関するページを参照してください。 最後に、 **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** メソッドで画像に対する顔検出処理を実行し、その結果をプログラムのメモリに保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> また、ローカルの画像から顔を検出することもできます。 [Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) のメソッドを参照してください (**DetectWithStream** など)。

### <a name="display-detected-face-data"></a>検出された顔データを表示する

次のコード ブロックでは、 **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** オブジェクトの配列の最初の要素を取得し、その属性をコンソールに出力します。 複数の顔が写った画像を使用する場合は、配列を反復処理することになります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]





## <a name="identify-a-face"></a>顔を識別する

識別操作では、人物 (1人または複数人) の画像を受け取り、その画像に含まれるそれぞれの顔の同一性を見つけます (顔認識検索)。 検出された顔はそれぞれ、顔の特徴が確認されているさまざまな **Person** オブジェクトのデータベース、つまり **PersonGroup** と比較されます。

### <a name="get-person-images"></a>人物の画像を取得する

このシナリオの手順を実行するには、プロジェクトのルート ディレクトリに画像 (https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images ) を保存する必要があります。

この画像のグループには、3 人の異なる人物に対応する 3 種類の顔画像が含まれています。写真には、1 枚につき 1 人の顔が写っています。 このコードでは、3 つの **PersonGroup Person** オブジェクトを定義し、それらを `woman`、`man`、および `child` で始まる画像ファイルに関連付けます。

### <a name="create-a-persongroup"></a>PersonGroup を作成する

画像をダウンロードしたら、**main** メソッドの一番下に次のコードを追加します。 このコードは、 **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** オブジェクトを認証し、それを使用して新しい **PersonGroup** を定義します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup Person を作成する

次のコード ブロックでは、 **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** を認証し、それを使用して、新しい **PersonGroup Person** オブジェクトを 3 つ定義します。 これらのオブジェクトはそれぞれ、一連の画像に含まれる 1 人の人物を表します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>顔を人物に割り当てる

次のコードは、画像をそのプレフィックスで並べ替えて顔を検出し、画像ファイルの名前に基づいて、それぞれ個別の **PersonGroup Person** オブジェクトに割り当てます。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> URL によって参照されたリモート画像から **PersonGroup** を作成することもできます。 [PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) のメソッドを参照してください (**AddFaceFromURL** など)。

### <a name="train-the-persongroup"></a>PersonGroup をトレーニングする

顔を割り当てたら、**PersonGroup** をトレーニングして、その各 **Person** オブジェクトに関連付けられている視覚的特徴を識別できるようにします。 次のコードは、非同期の **train** メソッドを呼び出し、結果をポーリングして、状態をコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> Face API は、本質的に静的な一連の事前構築済みモデルで実行されます (サービスの実行中にモデルのパフォーマンスが低下したり改善されたりすることはありません)。 Microsoft により、まったく新しいモデル バージョンに移行することなくモデルのバックエンドが更新されると、モデルによって生成される結果が変わる可能性があります。 より新しいバージョンのモデルを利用するには、同じ登録画像でより新しいモデルをパラメーターとして指定し、**PersonGroup** を再トレーニングすることができます。

### <a name="get-a-test-image"></a>テスト画像を取得する

次のコードは、プロジェクトのルートから画像 _test-image-person-group.jpg_ を探して、プログラムのメモリに読み込みます。 この画像は、**PersonGroup** を作成するために使用した画像と同じリポジトリにあります (https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images )。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>テスト画像からソースの顔を検出する

次のコード ブロックでは、テスト画像に通常の顔検出を実行してすべての顔を取得し、それらを配列に保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces-from-source-image"></a>ソース イメージから顔を識別する

**[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** メソッドは、検出された顔の配列を受け取って、指定された **PersonGroup** (前セクションで定義、トレーニングしたもの) と比較します。 検出された顔がグループ内の **Person** と一致する場合は、結果を保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

その後、詳細な一致結果をコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


### <a name="verify-faces"></a>顔を確認する

確認操作では、顔 ID と、別の顔 ID または **Person** オブジェクトのいずれかを取得し、同じ人に属しているかどうかを判断します。 確認を使用すると、識別操作によって返される顔の一致を二重チェックできます。

次のコードでは、2 つのソース画像から顔を検出し、それぞれターゲット画像から検出された顔と照らして確認します。

### <a name="get-test-images"></a>テスト イメージを取得する

次のコード ブロックでは、確認操作のターゲット画像とソース画像を指す変数を宣言します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>確認対象の顔を検出する

次のコードは、ソース画像とターゲット画像から顔を検出して、それらを変数に保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>確認の結果を取得する

次のコードでは、各ソース画像をターゲット画像と比較し、同じ人物のものであるかどうかを示すメッセージを出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="find-similar-faces"></a>似た顔の検索

以下のコードでは、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます (画像による顔検索)。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="detect-faces-for-comparison"></a>比較の対象となる顔を検出する

まず、「[検出して分析する](#detect-and-analyze-faces)」セクションで検出した顔の参照を保存します。 この顔がソースになります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

さらに、別の画像から一連の顔を検出する次のコードを入力します。 これらの顔がターゲットになります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>一致するものを探す

次のコードでは、 **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** メソッドを使用して、ソースの顔と一致するターゲットの顔をすべて検索します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>一致するものを出力する

次のコードは、一致の詳細をコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="run-the-application"></a>アプリケーションの実行

`go run <app-name>` コマンドを使用して、アプリケーション ディレクトリから顔認識アプリを実行します。

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

このクイックスタートで作成した **PersonGroup** を削除したい場合は、 **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** メソッドを呼び出します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Go 用の Face クライアント ライブラリを使用して基本的な顔認識タスクを行う方法について学習しました。 次に、さまざまな顔検出モデルと、ユース ケースに適したモデルを指定する方法について学習します。

> [!div class="nextstepaction"]
> [顔検出モデル バージョンを指定する](../../Face-API-How-to-Topics/specify-detection-model.md)

* [Face サービスとは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go) にあります。
