---
title: Azure Event Grid イベント スキーマ
description: すべてのイベントに存在するプロパティとスキーマについて説明します。 イベントは、4 つの必須文字列プロパティから構成されます。
ms.topic: reference
ms.date: 09/15/2021
ms.openlocfilehash: 3a6f63cc6d12b44ea2cb7fc02d1ae7df096358b8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550768"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid イベント スキーマ

この記事では、すべてのイベントに存在するプロパティとスキーマについて説明します。 イベントは、4 つの必須文字列プロパティから構成されます。 プロパティは、すべてのイベントに共通であり、発行元を問いません。 データ オブジェクトには、各発行元に固有のプロパティが含まれています。 システム トピックの場合、これらのプロパティは、リソース プロバイダー (Azure Storage や Azure Event Hubs など) に固有です。

イベント ソースは、複数のイベント オブジェクトを含めることができる配列で Azure Event Grid にイベントを送信します。 Event Grid トピックへイベントを送信する際の、配列の合計サイズの上限は 1 MB です。 配列内の各イベントは 1 MB に制限されます。 イベントまたは配列がサイズ制限を超えた場合は、**[413 ペイロードが大きすぎます]** という応答を受信します。 ただし、操作は 64 KB 単位で課金されます。 そのため、64 KB を超えるイベントでは、複数のイベントが発生したかのように操作の料金が発生します。 たとえば、130 KB のイベントでは、3 つの独立したイベントとして操作が課金されます。

Event Grid は、1 つのイベントを含む配列でサブスクライバーにイベントを送信します。 この動作は、今後変更される可能性があります。

Event Grid イベントおよび各 Azure パブリッシャーのデータ ペイロードの JSON スキーマは、[イベント スキーマ ストア](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)にあります。

## <a name="event-schema"></a>イベント スキーマ

すべてのイベント発行元に使用されているプロパティの例を次に示します。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

たとえば、Azure BLOB ストレージ イベントに対して次のようなスキーマが発行されます。

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>イベントのプロパティ

すべてのイベントには、次の同じ最上位レベルのデータが含まれています。

| プロパティ | 種類 | 必須 | 説明 |
| -------- | ---- | -------- | ----------- |
| topic | string | いいえ。ただし、追加する場合は、Event Grid トピックの Azure Resource Manager ID と完全に一致させる必要があります。 追加しなかった場合は、Event Grid によってイベントに記録されます。 | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | string | はい | 発行元が定義したイベントの対象のパス。 |
| eventType | string | はい | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | はい | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | はい | イベントの一意識別子。 |
| data | object | いいえ | リソース プロバイダーに固有のイベント データ。 |
| dataVersion | string | いいえ。ただし、空の値が記録されます。 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | 必須ではありませんが、追加する場合は、Event Grid スキーマの `metadataVersion` と完全に一致させる必要があります (現在は `1` のみ)。 追加しなかった場合は、Event Grid によってイベントに記録されます。 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトのプロパティの詳細については、イベント ソースを参照してください。

* [Azure Policy](./event-schema-policy.md)
* [Azure サブスクリプション (管理操作)](event-schema-subscriptions.md)
* [コンテナー レジストリ](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [リソース グループ (管理操作)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

カスタム トピックの場合、イベントの発行元がデータ オブジェクトを決定します。 最上位レベルのデータには、リソースによって定義された標準のイベントと同じフィールドを含める必要があります。

イベントをカスタム トピックに発行する場合は、サブスクライバーがそのイベントに関心があるかどうかを簡単に知ることができるイベントの件名を作成してください。 サブスクライバーは、その件名を使用してイベントをフィルター処理したり、ルーティングしたりします。 そのイベントが発生したパスを示すことにより、サブスクライバーがそのパスのセグメントでフィルター処理できるように考慮してください。 このパスにより、サブスクライバーはイベントを狭く、または幅広くフィルター処理できます。 たとえば、`/A/B/C` のように件名に 3 つのセグメント パスを示した場合、サブスクライバーは最初のセグメント `/A` でフィルター処理して幅広い一連のイベントを取得できます。 これらのサブスクライバーは、`/A/B/C` や `/A/D/E` などの件名を持つイベントを取得します。 他のサブスクライバーは、`/A/B` でフィルター処理して、より狭い一連のイベントを取得できます。

件名に、何が起こったかに関するより詳細な情報が必要になる場合があります。 たとえば、コンテナーにファイルが追加された場合、**ストレージ アカウント** パブリッシャーは件名 `/blobServices/default/containers/<container-name>/blobs/<file>` を指定します。 サブスクライバーはパス `/blobServices/default/containers/testcontainer` でフィルター処理することにより、ストレージ アカウント内の他のコンテナーではなく、そのコンテナーのすべてのイベントを取得できます。 サブスクライバーはまた、テキスト ファイルのみを操作するために、サフィックス `.txt` でフィルター処理またはルーティングすることもできます。

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
