---
title: fhir 用 Azure API の Azure IoT Hub を介してデバイスデータを受信する
description: このチュートリアルでは、デバイス データを IoT Hub から Azure IoT Connector for FHIR 経由で Azure API for FHIR にルーティングできるようにする方法について説明します。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: rabhaiya
ms.openlocfilehash: 504b5755c84f179c56b124c5a31189806f6e1483
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042372"
---
# <a name="receive-device-data-through-azure-iot-hub"></a>Azure IoT Hub を通じてデバイス データを受信する

Azure IoT Connector for FHIR&#174; (高速ヘルスケア相互運用性リソース)* を使用すると、Internet of Medical Things (IoMT) デバイスから Azure API for FHIR にデータを取り込むことができます。 [Azure portal を使用した Azure IoT Connector for FHIR (プレビュー) のデプロイ](iot-fhir-portal-quickstart.md)に関するクイックスタートには、Azure IoT Central で管理されているデバイスから Azure IoT Connector for FHIR に[テレメトリを送信](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot)する例が示されています。 Azure IoT Connector for FHIR は、Azure IoT Hub を介してプロビジョニングおよび管理されているデバイスでも使用できます。 このチュートリアルでは、Azure IoT Hub から Azure IoT Connector for FHIR に接続し、デバイス データをルーティングする手順について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブな Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 少なくとも 1 つの Azure IoT Connector for FHIR を備えた Azure API for FHIR リソース - [Azure portal を使用して Azure IoT Connector for FHIR (プレビュー) をデプロイする](iot-fhir-portal-quickstart.md)
- 実際のデバイスまたはシミュレートされたデバイスに接続されている Azure IoT Hub リソース - [Azure portal を使用して IoT ハブを作成する](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> Azure IoT Hub でシミュレートされたデバイス アプリケーションを使用する場合は、サポートされている各種言語およびシステムの中から任意のアプリケーションを選択できます。

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) の接続文字列を取得する

Azure IoT Hub には、Azure IoT Connector for FHIR と安全に接続するための接続文字列が必要です。 「[接続文字列を生成する](iot-fhir-portal-quickstart.md#generate-a-connection-string)」セクションの説明に従って、Azure IoT Connector for FHIR 用の新しい接続文字列を作成します。 この接続文字列は、次のセクションで使用できるように保存しておいてください。

Azure IoT Connector for FHIR は、内部で Azure Event Hub インスタンスを使用してデバイス メッセージを受信します。 上記で作成した接続文字列は、実際には、この基盤となるイベント ハブへの接続文字列です。

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Hub を Azure IoT Connector for FHIR (プレビュー) に接続する

Azure IoT Hub は、[メッセージ ルーティング](../../iot-hub/iot-hub-devguide-messages-d2c.md)と呼ばれる機能をサポートしています。これは、イベント ハブ、ストレージ アカウント、Service Bus などのさまざまな Azure サービスにデバイス データを送信する機能です。 Azure IoTfhir 用のコネクタは、この機能を使用して、Azure IoT ハブのデバイスデータを接続し、そのイベントハブエンドポイントに送信します。

> [!NOTE] 
> 現在、[メッセージ ルーティングの作成](../../iot-hub/tutorial-routing.md)に使用できるのは PowerShell または CLI コマンドのみです。これは、Azure IoT Connector for FHIR のイベント ハブが顧客サブスクリプションではホストされておらず、Azure portal に表示されないからです。 ただし、PowerShell または CLI を使用してメッセージ ルート オブジェクトを追加すると、それらのオブジェクトが Azure portal に表示され、そこから管理できるようになります。

メッセージ ルーティングの設定は、2 つの手順から成ります。

### <a name="add-an-endpoint"></a>エンドポイントの追加
この手順では、IoT ハブによるデータのルーティング先のエンドポイントを定義します。 このエンドポイントを作成するには、好みに応じて、PowerShell の [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) コマンドか、CLI の [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint) コマンドを使用します。

エンドポイントの作成コマンドで使用するパラメーターの一覧を次に示します。

|PowerShell パラメーター|CLI パラメーター|説明|
|---|---|---|
|ResourceGroupName|resource-group|IoT Hub リソースのリソース グループ名。|
|名前|hub-name|IoT Hub リソースの名前。|
|EndpointName|endpoint-name|作成するエンドポイントに割り当てる名前を使用します。|
|EndpointType|endpoint-type|IoT Hub が接続する必要があるエンドポイントの種類。 "EventHub" (PowerShell の場合) および "eventhub" (CLI の場合) というリテラル値を使用します。|
|EndpointResourceGroup|endpoint-resource-group|fhir リソース用の fhir Azure API 用の Azure IoT コネクタのリソースグループ名。 この値は、Azure API for FHIR の [概要] ページから取得できます。|
|EndpointSubscriptionId|endpoint-subscription-id|fhir リソース用の fhir Azure API 用の Azure IoT コネクタのサブスクリプション ID。 この値は、Azure API for FHIR の [概要] ページから取得できます。|
|ConnectionString|connection-string|Azure IoT Connector for FHIR への接続文字列。 前の手順で取得した値を使用します。|

### <a name="add-a-message-route"></a>メッセージ ルートを追加する
この手順では、上で作成したエンドポイントを使用してメッセージ ルートを定義します。 ルートを作成するには、好みに応じて、PowerShell の [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) コマンドか、CLI の [az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create) コマンドを使用します。

メッセージ ルートの追加コマンドで使用するパラメーターの一覧を次に示します。

|PowerShell パラメーター|CLI パラメーター|説明|
|---|---|---|
|ResourceGroupName|g|IoT Hub リソースのリソース グループ名。|
|名前|hub-name|IoT Hub リソースの名前。|
|EndpointName|endpoint-name|上で作成したエンドポイントの名前。|
|RouteName|route-name|作成するメッセージ ルートに割り当てる名前。|
|source|source-type|エンドポイントに送信するデータの種類。 "DeviceMessages" (PowerShell の場合) および "devicemessages" (CLI の場合) というリテラル値を使用します。|

## <a name="send-device-message-to-iot-hub"></a>デバイス メッセージを IoT ハブに送信する

実際のデバイスまたはシミュレートされたデバイスを使用して、以下に示す心拍メッセージのサンプルを Azure IoT Hub に送信します。 このメッセージは Azure IoT Connector for FHIR にルーティングされ、そこで FHIR Observation リソースに変換されてから Azure API for FHIR に格納されます。

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> 必ず、Azure IoT Connector for FHIR で構成した[マッピング テンプレート](iot-mapping-templates.md)に準拠したデバイス メッセージを送信するようにしてください。

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR でデバイス データを表示する

Postman を使用して fhir の Azure IoT Connector によって作成された fhir 監視リソースを表示できます。 詳細については、「 [Postman を使用した fhir サービスへのアクセス](./../use-postman.md)」と、 `GET` 上記の `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` サンプルメッセージで送信されたハートレートの値を使用して監視 fhir リソースを表示するように要求します。

> [!TIP]
> Azure API for FHIR データ プレーンへの適切なアクセス権をユーザーに確実に付与します。 [Azure のロールベースのアクセス制御 (Azure RBAC)](configure-azure-rbac.md) を使用して、必要なデータ プレーン ロールを割り当てます。


## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、Azure IoT Connector for FHIR にデバイス データをルーティングするように Azure IoT Hub を設定しました。 以下の次のステップの中からいずれかを選択して、Azure IoT Connector for FHIR の詳細を確認してください。

Azure IoT Connector for FHIR 内のさまざまなデータ フロー ステージについて説明します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR のデータ フロー](iot-data-flow.md)

デバイスと FHIR マッピング テンプレートを使用して IoT コネクタを構成する方法について説明します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR のマッピング テンプレート](iot-mapping-templates.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。