---
title: Event GrID ソースとしての Azure SignalR
description: Azure Event Grid の Azure SignalR イベントに対して用意されているプロパティについて説明します
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: a7caf42d40308ea2f95d7bf71091fd8e0ee63088
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651068"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR Service 用の Azure Event Grid イベント スキーマ

この記事では、SignalR Service イベントのプロパティとスキーマについて説明します。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 また、Azure SingnalR をイベント ソースとして使用するためのクイック スタートとチュートリアルの一覧も示されています。


## <a name="available-event-types"></a>使用可能なイベントの種類

SignalR Service から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | クライアント接続が接続されたときに発生します。 |
| Microsoft.SignalRService.ClientConnectionDisconnected | クライアント接続が接続解除されたときに発生します。 |

## <a name="example-event"></a>イベントの例

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
次の例では、クライアント接続が接続された場合のイベントのスキーマを示します。 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

クライアント接続が接続解除された場合のイベントのスキーマも同様です。 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例では、クライアント接続が接続された場合のイベントのスキーマを示します。 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionConnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "specversion": "1.0"
}]
```

クライアント接続が接続解除された場合のイベントのスキーマも同様です。 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>イベントのプロパティ


# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
イベントのトップレベルのデータを次に示します。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | SignalR Service のイベント データ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| `source` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | SignalR Service のイベント データ。 |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| `timestamp` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `hubName` | string | クライアント接続が属するハブ。 |
| `connectionId` | string | クライアント接続の一意の識別子です。 |
| `userId` | string | 要求で定義されているユーザーの識別子です。 |
| `errorMessage` | string | 接続解除の原因となったエラーです。 |

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法
|タイトル | 説明 |
|---------|---------|
| [Event Grid を使用して Azure SignalR Service のイベントに反応する](../azure-signalr/signalr-concept-event-grid-integration.md) | Azure SignalR Service と Event Grid の統合の概要。 |
| [Azure SignalR Service のイベントを Event Grid に送信する方法](../azure-signalr/signalr-howto-event-grid-integration.md) | Event Grid を通して Azure SignalR Service のイベントをアプリケーションに送信する方法を示します。 |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
