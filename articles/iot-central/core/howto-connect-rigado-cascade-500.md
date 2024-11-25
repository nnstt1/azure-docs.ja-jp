---
title: Azure IoT Central で Rigado Cascade 500 を接続する | Microsoft Docs
description: Rigado Cascade 500 ゲートウェイ デバイスを IoT Central アプリケーションに接続する方法について説明します。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 08/18/2021
ms.openlocfilehash: 1a255336d958ea4d4d027d533c80bfb493d88f8b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435487"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Rigado Cascade 500 ゲートウェイ デバイスを Azure IoT Central アプリケーションに接続する

この記事では、Rigado Cascade 500 ゲートウェイ デバイスを、ご利用の Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="what-is-cascade-500"></a>Cascade 500 とは

Cascade 500 IoT ゲートウェイは Rigado 社のハードウェア製品であり、同社の Cascade Edge-as-a-Service ソリューションの一部として含まれています。 商用 IoT プロジェクトおよび製品チーム向けに、柔軟なエッジ コンピューティング能力、コンテナー化された堅牢なアプリケーション環境、Bluetooth 5、LTE、Wi-Fi などのさまざまなワイヤレス デバイス接続オプションを提供します。

Cascade 500 は Azure IoT プラグ アンド プレイの認定を受けていて、お客様はデバイスを自分のエンド ツー エンド ソリューションに簡単にオンボードできます。 Cascade ゲートウェイを使用すると、ゲートウェイ デバイスに近接しているさまざまな状態監視センサーにワイヤレスで接続できます。 これらのセンサーは、ゲートウェイ デバイス経由で IoT Central にオンボードできます。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を完了するには、次が必要です。

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- Rigado Cascade 500 デバイス。 詳細については、[Rigado](https://www.rigado.com/) に関するページをご覧ください。

## <a name="add-a-device-template"></a>デバイス テンプレートの追加

Cascade 500 ゲートウェイ デバイスをお使いの Azure IoT Central アプリケーション インスタンスにオンボードするには、お使いのアプリケーション内で対応するデバイス テンプレートを構成する必要があります。

Cascade 500 デバイス テンプレートを追加するには、次のようにします。 

1. 左ウィンドウの **[Device Templates]\(デバイス テンプレート\)** タブに移動し、[+ 新規] を選択します。![新しいデバイス テンプレートの作成](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. 表示されたページには、***カスタム テンプレートを作成** または *_構成済みデバイス テンプレートを使用_**のオプションが示されています
1. 次に示すように、構成済みデバイス テンプレートの一覧から C500 デバイス テンプレートを選択します。![C500 デバイス テンプレートを選択](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. ***カスタマイズ*** を選択して、次の手順に進みます。 
1. 次の画面で、***[作成]*** を選択して、C500 デバイス テンプレートをお使いの IoT Central アプリケーションにオンボードします。

## <a name="retrieve-application-connection-details"></a>アプリケーション接続の詳細を取得する

次に、Cascade 500 デバイスを接続するために、Azure IoT Central アプリケーションの **[スコープ ID]** と **[主キー]** を取得する必要があります。 

1. 左側のウィンドウで **[管理]** に移動し、 **[デバイスの接続]** をクリックします。 
2. IoT Central アプリケーションの **[スコープ ID]** をメモしておきます。
![アプリのスコープ ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. ここで、 **[キーの表示]** をクリックして、 **[主キー]** 
![[主キー]](./media/howto-connect-rigado-cascade-500/primary-key-sas.png) をメモしておきます  

## <a name="contact-rigado-to-connect-the-gateway"></a>Rigado に連絡してゲートウェイを接続する 

Cascade 500 デバイスを IoT Central アプリケーションに接続するには、Rigado に連絡して、上記の手順で取得したアプリケーション接続の詳細情報を提供する必要があります。 

デバイスがインターネットに接続されると、Rigado は、セキュリティで保護されたチャネルを介して、構成の更新を Cascade 500 ゲートウェイ デバイスにプッシュ ダウンできるようになります。 

この更新により、IoT Central 接続の詳細情報が Cascade 500 デバイスに 適用され、デバイスの一覧にこのデバイスが表示されるようになります。 

![デバイスの一覧](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

これで、IoT Central アプリケーションで C500 デバイスを使用する準備が整いました。

## <a name="next-steps"></a>次のステップ

推奨される次のステップは以下のとおりです。

- [Azure IoT Central のデバイス接続](./concepts-get-connected.md)について確認する
- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
