---
title: チュートリアル - Azure IoT Central で新しい種類のゲートウェイ デバイスを定義する | Microsoft Docs
description: このチュートリアルでは、ビルダーとして Azure IoT Central アプリケーションで新しい種類の IoT ゲートウェイ デバイスを定義する方法について説明します。
author: rangv
ms.author: rangv
ms.date: 08/18/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 44a659b5b10e1f97293e2cd2636d5774baaebd40
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426210"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>チュートリアル - Azure IoT Central アプリケーションで新しい種類の IoT ゲートウェイ デバイスを定義する

このチュートリアルでは、IoT Central アプリケーションでゲートウェイ デバイス テンプレートを使用してゲートウェイ デバイスを定義する方法について説明します。 次に、ゲートウェイ デバイスを介して IoT Central アプリケーションに接続するいくつかのダウンストリーム デバイスを構成します。 

このチュートリアルでは、**スマート ビルディング** ゲートウェイ デバイス テンプレートを作成します。 **スマート ビルディング** ゲートウェイデバイスは、他のダウンストリーム デバイスとリレーションシップがあります。

![ゲートウェイ デバイスとダウンストリーム デバイスのリレーションシップの図](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

ゲートウェイ デバイスは、ダウンストリーム デバイスが IoT Central アプリケーションと通信できるようにするだけでなく、次のことも実行できます。

* 自身のテレメトリ (温度など) を送信する。
* オペレーターによって行われた書き込み可能なプロパティの更新に応答する。 (たとえば、オペレーターはテレメトリの送信間隔を変更できます)。
* コマンド (デバイスの再起動など) に応答する。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
>
> * ダウンストリーム デバイス テンプレートを作成する
> * ゲートウェイ デバイス テンプレートの作成
> * デバイス テンプレートを公開する
> * シミュレートされたデバイスを作成する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要になります。

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="create-downstream-device-templates"></a>ダウンストリーム デバイス テンプレートを作成する

このチュートリアルでは、シミュレートされたダウンストリーム デバイスを生成するために、**S1 センサー** デバイスおよび **RS40 混雑状況センサー** デバイス用のデバイス テンプレートを使用します。

**S1 センサー** デバイスのデバイス テンプレートを作成するには、次の手順を実行します。

1. 左側のペインで **[デバイス テンプレート]** を選択します。 次に、 **[+ 新規]** を選択して、テンプレートの追加を開始します。

1. **Minew S1** デバイスのタイルが表示されるまで、下にスクロールします。 タイルを選択し、 **[次のステップ: カスタマイズ]** を選択します。

1. **[Review]\(確認\)** ページで **[作成]** を選択して、アプリケーションにデバイス テンプレートを追加します。 

**RS40 混雑状況センサー** デバイスのデバイス テンプレートを作成するには:

1. 左側のペインで **[デバイス テンプレート]** を選択します。 次に、 **[+ 新規]** を選択して、テンプレートの追加を開始します。

1. **RS40 混雑状況センサー** デバイスのタイルが表示されるまで、下にスクロールします。 タイルを選択し、 **[次のステップ: カスタマイズ]** を選択します。

1. **[Review]\(確認\)** ページで **[作成]** を選択して、アプリケーションにデバイス テンプレートを追加します。 

これで、次のように 2 種類のダウンストリーム デバイスのデバイス テンプレートが作成されました。

![ダウンストリーム デバイスのデバイス テンプレート](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>ゲートウェイ デバイス テンプレートを作成する

このチュートリアルでは、ゲートウェイ デバイスのデバイス テンプレートもゼロから作成します。 このテンプレートは、シミュレートされたゲートウェイ デバイスをアプリケーション内に作成するために後で使用します。

新しいゲートウェイ デバイス テンプレートをアプリケーションに追加するには、次の手順を実行します。

1. 左側のペインで **[デバイス テンプレート]** を選択します。 次に、 **[+ 新規]** を選択して、テンプレートの追加を開始します。

1. **[テンプレートの種類の選択]** ページで **[IoT デバイス]** タイルを選択し、 **[次へ: カスタマイズ]** を選択します。

1. **[Customize device]\(デバイスのカスタマイズ\)** ページで、 **[This is a gateway device]\(これはゲートウェイ デバイスです\)** チェック ボックスをオンにします。

1. テンプレート名として「**スマート ビルディング ゲートウェイ デバイス**」と入力し、 **[次: レビュー]** を選択します。

1. **[Review]\(レビュー\)** ページで、 **[Create]\(作成\)** を選択します。 



1. **[モデルの作成]** ページで、 **[Custom model]\(カスタム モデル\)** タイルを選択します。

1. **[+ 機能の追加]** を選択して、機能を追加します。

1. 表示名として「**データの送信**」と入力し、機能タイプとして **[プロパティ]** を選択します。

1. **[+ 機能の追加]** を選択して、別の機能を追加します。 表示名として「**ブール型テレメトリ**」と入力し、機能タイプとして **[テレメトリ]** を選択し、スキーマとして **[ブール型]** を選択します。

1. **[保存]** を選択します。

### <a name="add-relationships"></a>リレーションシップの追加

次に、ダウンストリーム デバイス テンプレートにリレーションシップを追加します。

1. **[スマート ビルディング ゲートウェイ デバイス]** テンプレートで、 **[リレーションシップ]** を選択します。

1. **[+ リレーションシップの追加]** を選択します。 表示名として「**環境センサー**」と入力し、ターゲットとして **[S1 センサー]** を選択します。

1. 再度、 **[+ リレーションシップの追加]** を選択します。 表示名として「**混雑状況センサー**」と入力し、ターゲットとして **[RS40 混雑状況センサー]** を選択します。

1. **[保存]** を選択します。

![リレーションシップが表示されている [スマート ビルディング ゲートウェイ デバイス] テンプレート](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>クラウド プロパティを追加する

ゲートウェイ デバイス テンプレートにはクラウド プロパティを含めることができます。 クラウド プロパティは IoT Central アプリケーション内のみに存在しており、デバイスとの間で送受信されることはありません。

クラウド プロパティを **スマート ビルディング ゲートウェイ デバイス** テンプレートに追加するには、次の手順を実行します。

1. **[スマート ビルディング ゲートウェイ デバイス]** テンプレートで、 **[クラウドのプロパティ]** を選択します。

1. 下表の情報に従って、ゲートウェイ デバイス テンプレートに 2 つのクラウド プロパティを追加します。

    | Display name      | セマンティックの種類 | スキーマ |
    | ----------------- | ------------- | ------ |
    | Last Service Date | なし          | Date   |
    | Customer Name     | なし          | String |

1. **[保存]** を選択します。

### <a name="create-views"></a>ビューの作成

作成者は、環境センサー デバイスの関連情報がオペレーターに表示されるよう、アプリケーションをカスタマイズできます。 カスタマイズを行うことで、オペレーターがアプリケーションに接続された環境センサー デバイスを管理できるようになります。 オペレーター向けのデバイス操作用のビューを 2 種類作成できます。

* デバイス プロパティとクラウド プロパティを表示および編集するためのフォーム。
* デバイスを視覚化するためのビュー。

**スマート ビルディング ゲートウェイ デバイス** テンプレートの既定のビューを生成するには、次の手順を実行します。

1. **[スマート ビルディング ゲートウェイ デバイス]** テンプレートで、 **[ビュー]** を選択します。

1. **[既定のビューの生成]** タイルを選択し、すべてのオプションが選択されていることを確認します。

1. **[Generate default dashboard view]\(既定のダッシュボード ビューの生成\)** を選択します。

## <a name="publish-the-device-template"></a>デバイス テンプレートを公開する

シミュレートされたゲートウェイ デバイスを作成したり、実物のゲートウェイ デバイスを接続したりする前に、デバイス テンプレートを発行する必要があります。

ゲートウェイ デバイス テンプレートを発行するには、次の手順を実行します。

1. **[デバイス テンプレート]** ページで **[スマート ビルディング ゲートウェイ デバイス]** テンプレートを選択します。

2. **[発行]** を選択します。

3. **[Publish a Device Template]\(デバイス テンプレートの発行\)** ダイアログで、 **[発行]** を選択します。

デバイス テンプレートを公開すると、 **[デバイス]** ページに表示され、オペレーターが確認できるようになります。 オペレーターはテンプレートを利用し、デバイス インスタンスを作成したり、ルールと監視を確立したりできます。 発行後のテンプレートを編集すると、アプリケーション全体で動作に影響を与えることがあります。

発行後にデバイス テンプレートを変更する方法の詳細については、「[既存のデバイス テンプレートを編集する](howto-edit-device-template.md)」を参照してください。

## <a name="create-the-simulated-devices"></a>シミュレートされたデバイスを作成する

このチュートリアルでは、シミュレートされたダウンストリーム デバイスとシミュレートされたゲートウェイ デバイスを使用します。

シミュレートされたゲートウェイ デバイスを作成するには、次の手順を実行します。

1. **[デバイス]** ページのデバイス テンプレートの一覧で、 **[スマート ビルディング ゲートウェイ デバイス]** を選択します。

1. **[+ 新規]** を選択して、新しいデバイスの追加を開始します。

1. 生成された **デバイス ID** と **デバイス名** を保持します。 **[シミュレート済み]** スイッチが **[オン]** になっていることを確認します。 **［作成］** を選択します

シミュレートされたダウンストリーム デバイスを作成するには、次の手順を実行します。

1. **[デバイス]** ページのデバイス テンプレートの一覧で、 **[RS40 混雑状況センサー]** を選択します。

1. **[+ 新規]** を選択して、新しいデバイスの追加を開始します。

1. 生成された **デバイス ID** と **デバイス名** を保持します。 **[シミュレート済み]** スイッチが **[オン]** になっていることを確認します。 **［作成］** を選択します

1. **[デバイス]** ページのデバイス テンプレートの一覧で、 **[S1 センサー]** を選択します。

1. **[+ 新規]** を選択して、新しいデバイスの追加を開始します。

1. 生成された **デバイス ID** と **デバイス名** を保持します。 **[シミュレート済み]** スイッチが **[オン]** になっていることを確認します。 **［作成］** を選択します

![アプリケーションのシミュレートされたデバイス](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>ダウンストリーム デバイスのリレーションシップをゲートウェイ デバイスに追加する

シミュレートされたデバイスをアプリケーションに作成したので、ダウンストリーム デバイスとゲートウェイ デバイスの間にリレーションシップを作成できます。

1. **[デバイス]** ページのデバイス テンプレートの一覧で **[S1 センサー]** を選択し、シミュレートされた **[S1 センサー]** デバイスを選択します。

1. **[ゲートウェイに接続]** を選択します。

1. **[ゲートウェイへの接続]** ダイアログで、 **[スマート ビルディング ゲートウェイ デバイス]** テンプレートを選択してから、前に作成したシミュレートされたインスタンスを選択します。

1. **[接続]** を選択します。

1. **[デバイス]** ページのデバイス テンプレートの一覧で **[RS40 混雑状況センサー]** を選択してから、シミュレートされた **[RS40 混雑状況センサー]** デバイスを選択します。

1. **[Connect to gateway]\(ゲートウェイへの接続\)** を選択します。

1. **[ゲートウェイへの接続]** ダイアログで、 **[スマート ビルディング ゲートウェイ デバイス]** テンプレートを選択してから、前に作成したシミュレートされたインスタンスを選択します。

1. **[接続]** を選択します。

これで、両方のシミュレートされたダウンストリーム デバイスが、シミュレートされたゲートウェイ デバイスに接続されました。 ゲートウェイ デバイスの **[Downstream Devices]\(ダウンストリーム デバイス\)** ビューに移動すると、関連するダウンストリーム デバイスを確認できます。

![ダウンストリーム デバイス ビュー](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

## <a name="connect-real-downstream-devices"></a>実際のダウンストリーム デバイスを接続する

「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」チュートリアルのサンプル コードでは、デバイスによって送信されるプロビジョニング ペイロードにデバイス テンプレートのモデル ID を含める方法が示されています。 モデル ID により、IoT Central でデバイスを正しいデバイス テンプレートと関連付けることができます。 次に例を示します。

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
  provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
    provisioning_host=provisioning_host,
    registration_id=registration_id,
    id_scope=id_scope,
    symmetric_key=symmetric_key,
  )

  provisioning_device_client.provisioning_payload = {"modelId": model_id}
  return await provisioning_device_client.register()
```

ダウンストリーム デバイスを接続するときに、プロビジョニング ペイロードを変更して、ゲートウェイ デバイスの ID を含めることができます。 モデル ID により、IoT Central でデバイスを正しいダウンストリーム デバイス テンプレートと関連付けることができます。 ゲートウェイ ID により、IoT Central でダウンストリーム デバイスとそのゲートウェイの間の関係を確立できます。 この場合、デバイスによって送信されるプロビジョニング ペイロードは、次の JSON のようになります。

```json
{
  "modelId": "dtmi:rigado:S1Sensor;2",
  "iotcGateway":{
    "iotcGatewayId": "gateway-device-001"
  }
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

* デバイス テンプレートとしての新しい IoT ゲートウェイの作成。
* クラウド プロパティの作成。
* カスタマイズの作成。
* デバイス テレメトリの視覚化の定義。
* リレーションシップの追加。
* デバイス テンプレートの公開。

次の学習内容は次のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する](tutorial-add-edge-as-leaf-device.md)
