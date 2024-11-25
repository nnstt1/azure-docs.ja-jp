---
title: Azure IoT Hub メッセージの形式について | Microsoft Docs
description: 開発者ガイド - IoT Hub メッセージの形式と予期される内容について説明します。
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 40cbc1c5046c944a8915f1db38805cacb95b2889
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294777"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub メッセージを作成し、読み取る

プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルの共通のメッセージ形式が定義されています。 このメッセージ形式は、[device-to-cloud ルーティング](iot-hub-devguide-messages-d2c.md) メッセージと [cloud-to-device](iot-hub-devguide-messages-c2d.md) メッセージの両方で使用されます。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub は、ストリーミング メッセージング パターンを使用して、D2C メッセージングを実装しています。 IoT Hub の device-to-cloud メッセージは、[Service Bus](../service-bus-messaging/index.yml) の "*メッセージ*" というよりはむしろ [Event Hubs](../event-hubs/index.yml) の "*イベント*" であり、複数のリーダーで読み取り可能なサービスを経由する、大量のイベントが存在します。

IoT Hub メッセージは、次のような構成です。

* 事前定義された一連の "*システム プロパティ*" を以下に示します。

* 一連の *アプリケーション プロパティ*。 メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。 IoT Hub によってこれらのプロパティが変更されることはありません。

* 非透過的なバイナリ本文。

HTTPS プロトコルを使用して device-to-cloud メッセージを送信するとき、または cloud-to-device メッセージを送信するときに、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。

IoT Hub を使用した device-to-cloud メッセージングには、次のような特徴があります。

* device-to-cloud メッセージには持続性があり、最長で 7 日間、IoT hub の既定の **messages/events** エンドポイントに保持されます。

* device-to-cloud メッセージは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。 バッチは最大で 256 KB です。

* IoT Hub では、任意のパーティション分割は許可されていません。 D2C メッセージは、発信元の **deviceId** に基づいてパーティション分割されます。

* 「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」で説明されているように、IoT Hub ではデバイスごとに認証とアクセス制御を行うことができます。

* アプリケーション プロパティに移動する情報をメッセージにスタンプできます。 詳細については、[メッセージ エンリッチメント](iot-hub-message-enrichments-overview.md)に関するページを参照してください。

各種プロトコルを使用したメッセージのエンコードとデコードの方法の詳細については、[Azure IoT SDK](iot-hub-devguide-sdks.md) に関するページをご覧ください。

## <a name="system-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT Hub メッセージのシステム プロパティ

| プロパティ | 説明  |ユーザーが設定可能|クエリをルーティング </br>するためのキーワード|
| --- | --- | --- | --- |
| message-id |要求/応答パターンに使用する、メッセージのユーザー設定 ID。 形式:ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。  | はい | messageId |
| iothub-enqueuedtime |IoT Hub が [Device-to-Cloud](iot-hub-devguide-d2c-guidance.md) メッセージを受信した日時。 | いいえ | enqueuedTime |
| user-id |メッセージの送信元を指定するために使用される ID。 IoT Hub でメッセージが生成されると、 `{iot hub name}`に設定されます。 | はい | userId |
| iothub-connection-device-id |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **deviceId** が含まれます。 | いいえ | connectionDeviceId |
| iothub-connection-module-id |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **moduleId** が含まれます。 | いいえ | connectionModuleId |
| iothub-connection-auth-generation-id |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **connectionDeviceGenerationId** (「[デバイス ID のプロパティ](iot-hub-devguide-identity-registry.md#device-identity-properties)」を参照) が含まれています。 | いいえ |connectionDeviceGenerationId |
| iothub-connection-auth-method |IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。| いいえ | connectionAuthMethod |
| dt-dataschema | この値は、IoT Hub で、device-to-cloud メッセージに対して設定されます。 デバイス接続で設定されたデバイス モデル ID が含まれます。 | いいえ | $dt-dataschema |
| dt-subject | device-to-cloud メッセージを送信しているコンポーネントの名前。 | はい | $dt-subject |

## <a name="application-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT Hub メッセージのアプリケーション プロパティ

アプリケーション プロパティの一般的な用途として、デバイスからメッセージが送信された日時の記録があります。この場合、`iothub-creation-time-utc` プロパティを使用してデバイスからタイムスタンプを送信します。 このタイムスタンプの形式は、タイムゾーン情報を含まない UTC であることが必要です。 たとえば `2021-04-21T11:30:16Z` は有効ですが、`2021-04-21T11:30:16-07:00` は有効ではありません。

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT Hub メッセージのシステム プロパティ

| プロパティ | 説明  |ユーザーが設定可能|
| --- | --- | --- |
| message-id |要求/応答パターンに使用する、メッセージのユーザー設定 ID。 形式:ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。  |はい|
| sequence-number |IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |いいえ|
| to |[C2D](iot-hub-devguide-c2d-guidance.md) メッセージで指定される宛先。 |いいえ|
| absolute-expiry-time |メッセージの有効期限の日時。 |はい| 
| correlation-id |通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 |はい|
| user-id |メッセージの送信元を指定するために使用される ID。 IoT Hub でメッセージが生成されると、 `{iot hub name}`に設定されます。 |はい|
| iothub-ack |フィードバック メッセージのジェネレーター。 このプロパティは、デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用されます。 使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値を意味します。 |はい|

### <a name="system-property-names"></a>システム プロパティ名

システム プロパティ名は、メッセージがルーティングされるエンドポイントによって異なります。 これらの名前の詳細については、次の表を参照してください。

|システム プロパティ名|Event Hubs|Azure Storage|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|メッセージ ID|message-id|messageId|MessageId|message-id|
|User id|user-id|userId|UserId|user-id|
|Connection device id|iothub-connection-device-id| connectionDeviceId|iothub-connection-device-id|iothub-connection-device-id|
|Connection module id|iothub-connection-module-id|connectionModuleId|iothub-connection-module-id|iothub-connection-module-id|
|Connection auth generation id|iothub-connection-auth-generation-id|connectionDeviceGenerationId| iothub-connection-auth-generation-id|iothub-connection-auth-generation-id|
|Connection auth method|iothub-connection-auth-method|connectionAuthMethod|iothub-connection-auth-method|iothub-connection-auth-method|
|contentType|content-type|contentType|ContentType|iothub-content-type|
|contentEncoding|content-encoding|contentEncoding|ContentEncoding|iothub-content-encoding|
|iothub-enqueuedtime|iothub-enqueuedtime|enqueuedTime| 該当なし |iothub-enqueuedtime|
|CorrelationId|correlation-id|correlationId|CorrelationId|correlation-id|
|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|
|dt-subject|dt-subject|dt-subject|dt-subject|dt-subject|

## <a name="message-size"></a>メッセージ サイズ

IoT Hub では、メッセージのサイズは、プロトコルに関係なく実際のペイロードのみを考慮して測定されます。 サイズ (バイト単位) は次の値の和として計算されます。

* 本文のサイズ (バイト単位)
* メッセージ システム プロパティの全値のサイズ (バイト単位)。
* すべてのユーザー プロパティの名前と値のサイズ (バイト単位)

プロパティの名前と値は ASCII 文字に制限されているため、文字列の長さがバイト単位のサイズと等しくなります。

## <a name="anti-spoofing-properties"></a>なりすまし対策のプロパティ

D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

最初の 2 つには、「[デバイス ID プロパティ](iot-hub-devguide-identity-registry.md#device-identity-properties)」で説明されている発信元デバイスの **deviceId** と **generationId** が含まれています。

**iothub-connection-auth-method** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>次のステップ

* IoT Hub でのメッセージ サイズの制限については、[IoT Hub のクォータと調整](iot-hub-devguide-quotas-throttling.md)に関するページをご覧ください。

* IoT Hub メッセージをさまざまなプログラミング言語で作成し、読み取る方法については、[クイック スタート](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)をご覧ください。