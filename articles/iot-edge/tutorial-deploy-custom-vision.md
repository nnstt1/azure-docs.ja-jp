---
title: 'チュートリアル: Azure IoT Edge を使用してデバイスに Custom Vision 分類器をデプロイする'
description: このチュートリアルでは、Custom Vision と IoT Edge を使用して、コンピューター ビジョン モデルをコンテナーとして実行する方法について説明します。
services: iot-edge
author: kgremban
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: e407b430d73e43454a71f897184b29e5c12fee44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740571"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>チュートリアル:Custom Vision Service を使用してエッジで画像の分類を実行する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge では、ワークロードをクラウドからエッジに移すことにより、IoT ソリューションの効率性を高めることができます。 この機能は、コンピューター ビジョン モデルのような、多くのデータを処理するサービスに適しています。 [Custom Vision Service](../cognitive-services/custom-vision-service/overview.md) を使用すると、カスタム画像分類器を構築して、コンテナーとしてデバイスにデプロイすることが可能です。 この 2 つのサービスを組み合わせることで、あらかじめサイトからすべてのデータを転送しなくても、画像やビデオ ストリームから分析情報を得ることができます。 Custom Vision では、トレーニングされたモデルに対して画像を比較して分析情報を生成する分類器が提供されます。

たとえば、IoT Edge デバイス上の Custom Vision では、高速道路の交通量が通常よりも多いか少ないかや、駐車場に連続した駐車スペースがあるかどうかを判断することができます。 これらの分析情報は、アクションを実行する別のサービスと共有できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Custom Vision を使用して、画像分類器を構築する。
> * デバイス上の Custom Vision Web サーバーに対してクエリを実行する IoT Edge モジュールを開発する。
> * 画像分類器の結果を IoT Hub に送信する。

<center>

![図 - チュートリアルのアーキテクチャ、分類器のステージングとデプロイ](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

>[!TIP]
>このチュートリアルは、[Raspberry Pi 3 での Custom Vision と Azure IoT Edge](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi) サンプル プロジェクトの簡易バージョンです。 このチュートリアルは、クラウド VM 上で実行するように設計されており、静的な画像を使用して画像分類器のトレーニングとテストを行います。これは、IoT Edge 上で Custom Vision の評価を始めたばかりのユーザーに役立ちます。 このサンプル プロジェクトでは、物理ハードウェアを使用し、画像分類器のトレーニングとテストを行うためのライブ カメラ フィードを設定します。これは、より詳細な実際のシナリオを試す場合に便利です。

このチュートリアルを開始する前に、前のチュートリアル「[Linux コンテナーを使用して IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)」を完了して、Linux コンテナー開発用の環境を設定しておく必要があります。 このチュートリアルを完了すると、次の前提条件が満たされます。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Linux コンテナーで Azure IoT Edge を実行しているデバイス。 クイックスタートを使用して、[Linux デバイス](quickstart-linux.md)または [Windows デバイス](quickstart.md)を設定できます。
* コンテナー レジストリ ([Azure Container Registry](../container-registry/index.yml) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

Custom Vision サービスを使用して IoT Edge モジュールを開発するには、開発用マシンに次の追加の前提条件をインストールします。

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Custom Vision を使用して画像分類器を構築する

画像分類器を構築するには、Custom Vision プロジェクトを作成し、トレーニング画像を用意する必要があります。 このセクションで行う手順の詳細については、「[Custom Vision で分類子を構築する方法](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md)」を参照してください。

画像分類器は、構築してトレーニングした後、Docker コンテナーとしてエクスポートし、IoT Edge デバイスにデプロイすることができます。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

1. Web ブラウザーで、[Custom Vision の Web ページ](https://customvision.ai/)に移動します。

2. **[サインイン]** を選択し、Azure リソースへのアクセスに使用するのと同じアカウントを使用してサインインします。

3. **[新しいプロジェクト]** を選択します。

4. 以下の値を設定して、プロジェクトを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | 名前 | **EdgeTreeClassifier** など、プロジェクトの名前を指定します。 |
   | 説明 | オプションのプロジェクトの説明。 |
   | リソース | Custom Vision Service リソースを含む Azure リソース グループを 1 つ選択するか、まだ追加していない場合は **新規作成** します。 |
   | プロジェクトの種類 | **分類** |
   | 分類の種類 | **[Multiclass (single tag per image)]\(マルチクラス (画像ごとに 1 つのタグ)\)** |
   | ドメイン | **[General (compact)]\(汎用 (コンパクト)\)** |
   | Export Capabilities (エクスポート機能) | **[Basic platforms (Tensorflow, CoreML, ONNX, ...)]\(基本プラットフォーム (Tensorflow、CoreML、ONNX、...)\)** |

5. **[プロジェクトの作成]** を選択します。

### <a name="upload-images-and-train-your-classifier"></a>画像をアップロードして分類器をトレーニングする

画像分類器を作成するには、トレーニング画像のセットと、テスト画像が必要です。

1. [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) リポジトリのサンプル画像を、ローカル開発マシンに複製またはダウンロードします。

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Custom Vision プロジェクトに戻り、 **[Add images]\(画像の追加\)** を選択します。

3. ローカルに複製した Git リポジトリを参照し、最初の画像フォルダー **Cognitive-CustomVision-Windows/Samples/Images/Hemlock** に移動します。 フォルダー内の 10 個の画像をすべて選択し、 **[開く]** を選択します。

4. この画像のグループにタグ **hemlock** を追加し、**Enter** キーを押してタグを適用します。

5. **[Upload 10 files]\(10 ファイルをアップロード\)** を選択します。

   ![Custom Vision にドクニンジンのタグが付けられたファイルをアップロードする](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. 画像が正常にアップロードされたら、 **[完了]** を選択します。

7. **[Add images]\(画像の追加\)** をもう一度選択します。

8. 2 つ目の画像フォルダー **Cognitive-CustomVision-Windows/Samples/Images/Japanese Cherry** を参照します。 フォルダー内の 10 個の画像をすべて選択し、 **[開く]** を選択します。

9. この画像のグループにタグ **japanese cherry** を追加し、**Enter** キーを押してタグを適用します。

10. **[Upload 10 files]\(10 ファイルをアップロード\)** を選択します。 画像が正常にアップロードされたら、 **[完了]** を選択します。

11. 両方の画像のセットがタグ付けされ、アップロードされたら、 **[Train]\(トレーニング\)** を選択して分類器をトレーニングします。

### <a name="export-your-classifier"></a>分類器をエクスポートする

1. 分類器をトレーニングした後、分類器の [パフォーマンス] ページで **[エクスポート]** を選択します。

   ![トレーニング済みの画像分類器をエクスポートする](./media/tutorial-deploy-custom-vision/export.png)

2. プラットフォームとして **[DockerFile]** を選択します。 

3. バージョンとして **[Linux]** を選択します。  

4. **[エクスポート]** を選択します。 

   ![Linux コンテナーを持つ DockerFile としてエクスポートする](./media/tutorial-deploy-custom-vision/export-2.png)

5. エクスポートが完了したら、 **[ダウンロード]** を選択し、.zip パッケージをローカルのコンピューターに保存します。 パッケージのすべてのファイルを展開します。 画像分類器サーバーを含む IoT Edge モジュールを作成するために、これらのファイルを使用します。 

ここまでで、Custom Vision プロジェクトの作成とトレーニングが完了しました。 次のセクションでは、エクスポートしたファイルを使用します。Custom Vision Web ページの使用は、これで終了です。 

## <a name="create-an-iot-edge-solution"></a>IoT Edge ソリューションを作成する

現時点で、開発マシン上に画像分類器のコンテナー バージョンのファイルがあります。 このセクションでは、画像分類器コンテナーを IoT Edge モジュールとして実行するように構成します。 画像分類器と共にデプロイされる第 2 のモジュールも作成します。 第 2 のモジュールでは、要求が分類器にポストされ、結果がメッセージとして IoT Hub に送信されます。 

### <a name="create-a-new-solution"></a>新しいソリューションの作成

ソリューションとは、単一の IoT Edge デプロイ用に複数のモジュールを開発、整理するための論理的な方法のことです。 ソリューションには、1 つまたは複数のモジュールのコードと、それらを IoT Edge デバイス上でどのように構成するかを宣言する配置マニフェストが含まれています。 

1. Visual Studio Code で、 **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

1. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | **CustomVisionSolution** のように、ソリューションのわかりやすい名前を入力するか、既定値をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Python モジュール]** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **classifier** という名前を付けます。<br><br>このモジュール名は、小文字にすることが重要です。 IoT Edge では、モジュールを参照するときに大文字と小文字が区別されます。このソリューションでは、すべての要求が小文字で書式設定されるライブラリを使用します。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、前の手順で事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリの **ログイン サーバー** の値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。<br><br>文字列は最終的に、 **\<registry name\>.azurecr.io/classifier** のようになります。 |
 
   ![Docker イメージ リポジトリを指定する](./media/tutorial-deploy-custom-vision/repository.png)

Visual Studio Code ウィンドウによって、IoT Edge ソリューション ワークスペースが読み込まれます。

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

IoT Edge 拡張機能は、Azure からコンテナー レジストリの資格情報をプルし、それらを環境ファイルに取り込もうとします。 資格情報が既に含まれているかどうかを確認します。 含まれていない場合は、次のようにして追加します。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした **ユーザー名** と **パスワード** の値を使用して、フィールドを更新します。
3. このファイルを保存します。

>[!NOTE]
>このチュートリアルでは、開発とテストのシナリオに便利な、Azure Container Registry の管理者ログイン資格情報を使用します。 運用環境のシナリオに向けて準備ができたら、サービス プリンシパルのような最小限の特権で認証できるオプションを使用することをお勧めします。 詳細については、[[コンテナー レジストリへのアクセスを管理する]](production-checklist.md#manage-access-to-your-container-registry) を参照してください。

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用のモジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。このチュートリアルではこれを使用します。 

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。 

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。 

### <a name="add-your-image-classifier"></a>画像分類器を追加する

Visual Studio Code の Python モジュール テンプレートには、IoT Edge をテストするために実行できるいくつかのサンプル コードが含まれています。 このシナリオでは、そのコードを使用しません。 代わりに、このセクションの手順を使用して、前にエクスポートした画像分類器コンテナーでサンプル コードを置き換えます。 

1. エクスプローラーで、ダウンロードして展開した Custom Vision パッケージを参照します。 展開したパッケージ内のすべての内容をコピーします。 2 つのフォルダー (**app** と **azureml**) および 2 つのファイル (**Dockerfile** と **README**) であるはずです。 

2. エクスプローラーで、Visual Studio Code に IoT Edge ソリューションを作成する場所として指定したディレクトリを参照します。 

3. 分類器モジュール フォルダーを開きます。 前のセクションで提案された名前を使用した場合、フォルダー構造は **CustomVisionSolution/modules/classifier** のようになります。 

4. **classifier** フォルダーにファイルを貼り付けます。 

5. Visual Studio Code ウィンドウに戻ります。 今度は、ソリューション ワークスペースで、モジュール フォルダーに画像分類器ファイルが表示されます。 

   ![画像分類器ファイルがあるソリューション ワークスペース](./media/tutorial-deploy-custom-vision/workspace.png)

6. classifier フォルダー内の **module.json** ファイルを開きます。 

7. 追加した新しい Dockerfile を指すように **platforms** パラメーターを更新します。さらに、このチュートリアルで使用している唯一のアーキテクチャである AMD64 以外のすべてのオプションを削除します。 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. 変更を保存します。 

### <a name="create-a-simulated-camera-module"></a>シミュレートされたカメラ モジュールを作成する

実際の Custom Vision デプロイには、ライブ画像またはビデオ ストリームを提供するカメラがあると思われます。 このシナリオでは、画像分類器にテスト画像を送信するモジュールを構築することにより、カメラをシミュレートします。 

#### <a name="add-and-configure-a-new-module"></a>新しいモジュールを追加して構成する

このセクションでは、同じ CustomVisionSolution に新しいモジュールを追加し、シミュレートされたカメラを作成するコードを設定します。 

1. 同じ Visual Studio Code ウィンドウで、コマンド パレットを使用して **Azure IoT Edge: Add IoT Edge Module** を実行することで追加できます。 コマンド パレットで、新しいモジュールに以下の情報を指定します。 

   | Prompt | 値 | 
   | ------ | ----- |
   | Select deployment template file (配置テンプレート ファイルの選択) | CustomVisionSolution フォルダー内の **deployment.template.json** ファイルを選択します。 |
   | Select module template (モジュール テンプレートの選択) | **[Python モジュール]** を選択します |
   | Provide a module name (モジュール名の指定) | モジュールに **cameraCapture** という名前を付けます |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | **localhost:5000** を、Azure コンテナー レジストリの **ログイン サーバー** の値に置き換えます。<br><br>文字列は最終的に、 **\<registryname\>.azurecr.io/cameracapture** のようになります。 |

   VS Code ウィンドウで、新しいモジュールがソリューション ワークスペースに読み込まれ、deployment.template.json ファイルが更新されます。 これで、classifier と cameraCapture という 2 つのモジュール フォルダーが表示されるようになりました。 

2. **modules** / **cameraCapture** フォルダー内の **main.py** ファイルを開きます。 

3. ファイル全体を次のコードに置き換えます。 このサンプル コードにより、分類器モジュールで実行されている画像処理サービスに POST 要求が送信されます。 このモジュール コンテナーに、要求で使用するサンプル画像を提供します。 その後、応答が IoT Hub メッセージとしてパッケージ化され、出力キューに送信されます。  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. **main.py** ファイルを保存します。 

5. **requrements.txt** ファイルを開きます。 

6. コンテナーに含めるライブラリのための新しい行を追加します。

   ```Text
   requests
   ```

7. **requirements.txt** ファイルを保存します。


#### <a name="add-a-test-image-to-the-container"></a>テスト画像をコンテナーに追加する

このシナリオで画像フィードを提供するために、実際のカメラを使用する代わりに、単一のテスト画像を使用します。 テスト画像は、GitHub リポジトリに含まれています。これは、このチュートリアルの前の方で画像をトレーニングするためにダウンロードしたものです。 

1. **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test** にあるテスト画像に移動します。 

2. **test_image.jpg** をコピーします。 

3. IoT Edge ソリューションのディレクトリに移動し、**modules** / **cameraCapture** フォルダーにテスト画像を貼り付けます。 この画像は、前のセクションで編集した main.py ファイルと同じフォルダー内にある必要があります。 

4. Visual Studio Code で、cameraCapture モジュールの **Dockerfile.amd64** ファイルを開きます

5. 作業ディレクトリ `WORKDIR /app` を確立する行の後に、次のコード行を追加します。

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Dockerfile を保存します。

### <a name="prepare-a-deployment-manifest"></a>配置マニフェストを準備する

このチュートリアルのここまでで、木の画像を分類するように Custom Vision モデルをトレーニングし、そのモデルを IoT Edge モジュールとしてパッケージ化しました。 次に、画像分類サーバーに対してクエリを実行し、その結果を IoT Hub にレポートできる、第 2 のモジュールを作成しました。 これで、これら 2 つのモジュールをどのように起動および実行するかを IoT Edge デバイスに指示する配置マニフェストを作成する準備ができました。 

Visual Studio Code 用の IoT Edge 拡張機能では、各 IoT Edge ソリューションに、配置マニフェストの作成を支援するためのテンプレートが用意されています。 

1. ソリューション フォルダー内の **deployment.template.json** ファイルを開きます。 

2. **modules** セクションを見つけます。このセクションには 3 つのモジュールが含まれています。2 つは自分で作成した classifier と cameraCapture であり、3 つ目は既定で含まれている SimulatedTemperatureSensor です。 

3. **SimulatedTemperatureSensor** モジュールとそのすべてのパラメーターを削除します。 このモジュールはテスト シナリオ用のサンプル データを提供するために用意されていますが、このデプロイでは必要ありません。 

4. 画像分類モジュールに **classifier** 以外の名前を付けた場合は、ここで名前の文字がすべて小文字であることを確認してください。 cameraCapture モジュールは、要求ライブラリを使用して classifier モジュールを呼び出します。このライブラリではすべての要求が小文字で書式設定され、IoT Edge では大文字と小文字が区別されます。 

5. cameraCapture モジュールの **createOptions** パラメーターを次の JSON で更新します。 この情報によって、モジュール コンテナーに環境変数が作成されます。これらの変数は、main.py プロセスで取得されます。 この情報を配置マニフェストに含めることで、モジュール イメージを再ビルドせずに、イメージまたはエンドポイントを変更できます。 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Custom Vision モジュールに *classifier* 以外の名前を付けた場合は、それに一致するように画像処理エンドポイントの値を更新します。 

6. ファイルの末尾で、$edgeHub モジュールの **routes** パラメーターを更新します。 予測結果を cameraCapture から IoT Hub にルーティングできます。

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    2 番目のモジュールに *cameraCapture* 以外の名前を付けた場合は、それに一致するようにルートの値を更新します。 

7. **deployment.template.json** ファイルを保存します。

## <a name="build-and-push-your-iot-edge-solution"></a>IoT Edge ソリューションをビルドしてプッシュする

両方のモジュールを作成し、配置マニフェスト テンプレートを構成したので、コンテナー イメージをビルドしてコンテナー レジストリにプッシュする準備ができました。

レジストリにイメージを配置したら、ソリューションを IoT Edge デバイスにデプロイできます。 IoT ハブを通じてデバイスにモジュールを設定できますが、Visual Studio Code を通じて IoT ハブとデバイスにアクセスすることもできます。 このセクションでは、IoT ハブへのアクセスを設定してから、VS Code を使用してソリューションを IoT Edge デバイスに配置します。

まず、ソリューションをビルドしてコンテナー レジストリにプッシュします。

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。

2. ターミナルで次のコマンドを入力して、Docker にサインインします。 自分の Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインします。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

3. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分間かかる可能性がありますが、次回これらのコマンドを実行するときは、それより速くなります。

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

Visual Studio Code エクスプローラーと Azure IoT Tools 拡張機能を使用して、モジュール プロジェクトを自分の IoT Edge デバイスにデプロイします。 シナリオ用の配置マニフェストである **deployment.amd64.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションで、 **[デバイス]** を展開して IoT デバイスの一覧を表示します。

2. IoT Edge デバイスの名前を右クリックして、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

3. **config** フォルダーで **deployment.amd64.json** ファイルを選択し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。

4. お使いのデバイスの **[モジュール]** を展開し、デプロイされて実行中のモジュールの一覧を表示します。 更新ボタンをクリックします。 新しい **classifier** モジュールと **cameraCapture** モジュールが、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。  

また、すべてのモジュールが動作していることをデバイス自体で確認することもできます。 IoT Edge デバイスで、次のコマンドを実行してモジュールの状態を表示します。

   ```bash
   iotedge list
   ```

両方のモジュールが開始するまでに数分かかる場合があります。 IoT Edge ランタイムは、新しい配置マニフェストを受け取り、コンテナー ランタイムからモジュール イメージを取得して、それぞれの新しいモジュールを開始する必要があります。

## <a name="view-classification-results"></a>分類の結果を表示する

モジュールの結果を表示する方法は、2 つあります。デバイス自体では、メッセージが生成されて送信されるとき、Visual Studio Code では、メッセージが IoT Hub に届くときです。 

デバイスでは、cameraCapture モジュールのログを見て、送信されるメッセージと、IoT Hub による受信の確認をチェックします。 

   ```bash
   iotedge logs cameraCapture
   ```

Visual Studio Code で、IoT Edge デバイスの名前を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。 

> [!NOTE]
> 最初、cameraCapture モジュールからの出力には接続エラーが表示されることがあります。 これは、モジュールがデプロイされてから開始されるまでの遅延が原因です。
>
> cameraCapture モジュールにより、成功するまで接続の再試行が自動的に行われます。 接続に成功すると、予期される画像分類メッセージが表示されます。これについて以下で説明します。

cameraCapture モジュールからのメッセージとして送信される、Custom Vision モジュールの結果には、画像がドクニンジンまたは桜のものである確率が含まれます。 画像はドクニンジンであるため、確率は 1.0 と表示されるはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Custom Vision モデルをトレーニングし、モジュールとして IoT Edge デバイスにデプロイしました。 次に、画像分類サービスに対してクエリを実行し、その結果を IoT Hub にレポートできるモジュールを構築しました。

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [SQL Server データベースを使用してエッジでデータを格納する](tutorial-store-data-sql-server.md)