---
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: include
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 3ffcfdb156f222cf1eb7134b271550eac4c9fcb3
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114732228"
---
このクイックスタートでは、2 つの Node.js アプリケーションを使用します。バックエンド アプリケーションから呼び出されたダイレクト メソッドに応答するシミュレートされたデバイスのアプリケーションと、シミュレートされたデバイスのダイレクト メソッドを呼び出すバックエンド アプリケーションです。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Node.js 10 以上](https://nodejs.org)。

    開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

    ```cmd/sh
    node --version
    ```

* [サンプル Node.js プロジェクト](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip)。

* ファイアウォールでポート 8883 が開放されていること。 このクイックスタートのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT Hub への利用統計情報の送信に関するクイック スタート](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub](iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前の「[クイック スタート: デバイスから IoT Hub への利用統計情報の送信](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)」を完了した場合は、この手順を省略できます。

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

   **MyNodeDevice**: これは、登録するデバイスの名前です。 示されているように、**MyNodeDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyNodeDevice \
      --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

3. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub connection-string show \
      --policy-name service --name {YourIoTHubName} --output table

    ```

    次のようなサービス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。 このサービス接続文字列は、前の手順でメモしたデバイス接続文字列とは異なります。

## <a name="listen-for-direct-method-calls"></a>ダイレクト メソッドの呼び出しをリッスンする

シミュレートされたデバイス アプリケーションは、IoT Hub 上のデバイス固有エンドポイントに接続し、シミュレートされた利用統計情報を送信して、Hub からのダイレクト メソッド呼び出しをリッスンします。 このクイック スタートでは、Hub からのダイレクト メソッド呼び出しは、利用統計情報の送信間隔を変更するようデバイスに指示します。 シミュレートされたデバイスでは、ダイレクト メソッドを実行した後、ハブに受信確認が返送されます。

1. ローカル ターミナル ウィンドウで、サンプルの Node.js プロジェクトのルート フォルダーに移動します。 次に、**iot-hub\Quickstarts\simulated-device-2** フォルダーに移動します。

2. 適当なテキスト エディターで **SimulatedDevice.js** ファイルを開きます。

    `connectionString` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.js** に保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT Hub にテレメトリを送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](./media/quickstart-control-device-node/simulated-device-telemetry-iot-hub.png)

## <a name="call-the-direct-method"></a>ダイレクト メソッドを呼び出す

バックエンド アプリケーションは、IoT ハブ上のサービス側エンドポイントに接続します。 アプリケーションにより、IoT ハブを通してデバイスへのダイレクト メソッド呼び出しが行われた後、受信確認がリッスンされます。 通常、IoT Hub のバックエンド アプリケーションはクラウドで実行されます。

1. 別のローカル ターミナル ウィンドウで、サンプルの Node.js プロジェクトのルート フォルダーに移動します。 その後、**iot-hub\Quickstarts\back-end-application** フォルダーに移動します。

2. 適当なテキスト エディターで **BackEndApplication.js** ファイルを開きます。

    `connectionString` 変数の値を、前にメモしたサービス接続文字列に置き換えます。 変更を **BackEndApplication.js** に保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、バックエンド アプリケーションを実行します。

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    次のスクリーンショットは、アプリケーションによりデバイスに対してダイレクト メソッド呼び出しが行われ、受信確認が受診されたときの出力を示します。

    ![アプリケーションがデバイスに対してダイレクト メソッドの呼び出しを行うときの出力](./media/quickstart-control-device-node/direct-method-device-call.png)

    バックエンド アプリケーションを実行した後、シミュレートされたデバイスを実行しているコンソール ウィンドウにメッセージが表示され、メッセージの送信速度が変わります。

    ![シミュレートされたクライアントに変更があったときの出力](./media/quickstart-control-device-node/simulated-device-message-change.png)
