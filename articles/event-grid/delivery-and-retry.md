---
title: Azure Event Grid による配信と再試行
description: Azure Event Grid がイベントをどのように配信し、未配信メッセージをどのように処理するかについて説明します。
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a6055a99e717dd379dc6bd43411c73456bdaede8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730333"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid によるメッセージの配信と再試行
Event Grid は、持続性のある配信を提供します。 一致する各サブスクリプションに対して、**最低 1 回** は各メッセージを直ちに配信しようとします。 サブスクライバーのエンドポイントがイベントの受信を認識しない場合、またはエラーが発生した場合、Event Grid は固定された[再試行スケジュール](#retry-schedule)および[再試行ポリシー](#retry-policy)に基づいて、配信を再試行します。 既定では、Event Grid モジュールはサブスクライバーに一度に 1 つのイベントを配信します。 ただし、ペイロードは 1 つのイベントを持つ配列です。

> [!NOTE]
> Event Grid によるイベント配信の順序は保証されません。そのため、サブスクライバーはこれらを順序どおりに受信できない場合があります。 

## <a name="retry-schedule"></a>再試行のスケジュール
イベント配信の試行に関するエラーを Event Grid で受信した場合は、エラーの種類に基づいて、イベント配信の再試行、イベントの配信不能処理、イベントの削除のいずれを行う必要があるかが Event Grid により決定されます。 

サブスクライブされたエンドポイントによって返されたエラーが、再試行によって修正できない構成関連のエラー (エンドポイントが削除された場合など) である場合、Event Grid によりイベントの配信不能処理が行われます。配信不能が構成されていない場合は、イベントが削除されます。

次の表では、再試行が行われないエンドポイントとエラーの種類について説明します。

| エンドポイントの種類 | エラー コード |
| --------------| -----------|
| Azure リソース | 400 正しくない要求、413 要求のエンティティが大きすぎます、403 許可されていません | 
| Webhook | 400 正しくない要求、413 要求のエンティティが大きすぎます、403 許可されていません、404 見つかりません、401 権限がありません |
 
> [!NOTE]
> エンドポイントに対して配信不能が構成されていない場合、上記のエラーが発生すると、イベントは削除されます。 この種のイベントを削除したくない場合は、配信不能を構成することを検討してください。

サブスクライブされたエンドポイントによって返されたエラーが上記の一覧にない場合、次に示すポリシーを使用して、Event Grid によって再試行が実行されます。

Event Grid は、メッセージの配信後、応答を 30 秒間待機します。 30 秒経過しても、エンドポイントが応答していない場合は、メッセージは再試行のためにキューに入れられます。 Event Grid は、イベント配信に対して指数バックオフ再試行ポリシーを使用します。 Event Grid ではベスト エフォート方式で次のスケジュールに従って配信を再試行します。

- 10 秒
- 30 秒
- 1 分
- 5 分
- 10 分
- 30 分
- 1 時間
- 3 時間
- 6 時間
- 12 時間ごと (最大 24 時間)


エンドポイントが 3 分以内に応答した場合、Event Grid はベスト エフォート方式でイベントを再試行キューから削除しようとしますが、それでも重複が受信される可能性があります。

Event Grid では、すべての再試行の手順に小規模なランダム化を追加します。また、エンドポイントが一貫して正常ではない、長期間ダウンしている、または圧迫されていることがわかっている場合は、状況に応じて、特定の再試行をスキップできます。

## <a name="retry-policy"></a>再試行ポリシー
イベント サブスクリプションを作成するときには、次の 2 つの構成を使用して再試行ポリシーをカスタマイズできます。 再試行ポリシーの制限のいずれかに達した場合、イベントは削除されます。 

- **最大試行回数** - 値は 1 ～ 30 の整数にする必要があります。 既定値は 30 です。
- **イベントの Time-To-Live (TTL)** - 値は 1 ～ 1,440 の整数にする必要があります。 既定値は 1,440 分です

これらの設定を構成するためのサンプルの CLI および PowerShell コマンドについては、「[再試行ポリシーの設定](manage-event-delivery.md#set-retry-policy)」を参照してください。

## <a name="output-batching"></a>出力のバッチ処理 
Event Grid の既定では、サブスクライバーに各イベントが個別に送信されます。 サブスクライバーは、イベント 1 つが格納された配列を受け取ります。 高スループットの状況で HTTP のパフォーマンスを向上させるため、イベントを一括配信するように Event Grid を構成することができます。 バッチ処理は、既定では無効になっており、サブスクリプションごとに有効にできます。

### <a name="batching-policy"></a>バッチ処理のポリシー
一括配信には、次の 2 つの設定があります。

* **[バッチごとの最大イベント数]** - Event Grid によってバッチごとに配信されるイベントの最大数。 この数を超えることはありませんが、発行時に利用できるイベントが他にない場合は、これより少ない数のイベントが配信される可能性があります。 配信できるイベントの数が少ない場合、バッチを作成するために Event Grid でイベントが遅延されることはありません。 1 から 5,000 の間である必要があります。
* **[優先バッチ サイズ (KB 単位)]** - バッチ サイズの目標上限 (KB 単位)。 最大イベント数と同様に、発行の時点で使用できないイベントが増えると、バッチ サイズが小さくなることがあります。 1 つのイベントが優先サイズより大きい "*場合*"、バッチが優先バッチ サイズより大きくなることがあります。 たとえば、優先サイズが 4 KB で、10 KB のイベントが Event Grid にプッシュされた場合でも、10 KB のイベントは削除されるのではなく、独自のバッチで配信されます。

バッチ配信は、ポータル、CLI、PowerShell、または SDK を使用して、イベントごとのサブスクリプションで構成されます。

### <a name="batching-behavior"></a>バッチ処理の動作

* すべてまたはなし

  Event Grid 操作は、"すべてまたはなし" のセマンティクスです。 バッチ配信の一部成功はサポートされていません。 サブスクライバーは、バッチごとに 60 秒で合理的に処理できるだけの数のイベントを要求するように注意する必要があります。

* オプティミスティック バッチ処理

  バッチ処理ポリシー設定は、バッチ処理動作に対する厳密な限界ではなく、ベストエフォート ベースで尊重されます。 イベント レートが低い場合、バッチのサイズが、バッチごとのイベントの要求した最大数より小さいことがよくあります。

* 既定値は OFF に設定されています

  既定では、Event Grid によって、各配信要求に 1 つのイベントのみが追加されます。 バッチ処理を有効にする方法は、イベント サブスクリプション JSON で、この記事で前述した設定のいずれかを指定することです。

* 既定値

  イベント サブスクリプションを作成するときに、両方の設定 (バッチあたりの最大イベント数とキロバイト単位の概算バッチ サイズ) を指定する必要はありません。 1 つの設定さえ指定されれば、Event Grid によって (構成可能な) 既定値が使用されます。 既定値と、それらをオーバーライドする方法については、以降のセクションを参照してください。

### <a name="azure-portal"></a>Azure portal: 
![一括配信の設定](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
イベント サブスクリプションを作成するときは、次のパラメーターを使用します。 

- **max-events-per-batch** - バッチ内のイベントの最大数。 1 から 5000 までの数値を指定する必要があります。
- **preferred-batch-size-in-kilobytes** - 優先バッチ サイズ (KB 単位)。 1 から 1024 までの数値を指定する必要があります。

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Event Grid での Azure CLI の使用の詳細については、「[Azure CLI を使用してストレージ イベントを Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md)」を参照してください。


## <a name="delayed-delivery"></a>遅延配信
エンドポイントで配信エラーが発生すると、Event Grid はそのエンドポイントへのイベント配信とイベントの再試行を遅らせ始めます。 たとえば、エンドポイントに発行された最初の 10 個のイベントが失敗した場合、Event Grid はそのエンドポイントで問題が発生していると想定し、その後の再試行 "*および新しい*" 配信はしばらく (場合によっては数時間) 遅延されます。

遅延配信の機能的な目的は、正常でないエンドポイントと Event Grid システムを保護することです。 バックオフや、正常でないエンドポイントへの配信遅延がなければ、Event Grid の再試行ポリシーとボリューム機能によってシステムがすぐに過負荷になる可能性があります。

## <a name="dead-letter-events"></a>配信不能イベント
Event Grid では、一定の時間内にイベントを配信できない場合、あるいはイベントの配信を一定回数試行したが配信できない場合、未配信イベントをストレージ アカウントに送信できます。 このプロセスは **配信不能処理** と呼ばれます。 Event Grid では、**次のいずれかの** 条件に一致したとき、イベントが配信不能処理されます。 

- イベントが **Time-To-Live** 期間内に配信されない。 
- イベント配信の **試行回数** が上限を超えている。

いずれかの条件が満たされた場合、イベントは削除されるか、配信不能になります。  既定では、Event Grid は配信不能処理を有効にしません。 この処理を有効にするには、イベント サブスクリプションの作成時に、配信不能イベントを保持するようにストレージ アカウントを指定する必要があります。 このストレージ アカウントからイベントをプルして配信を解決します。

Event Grid は、そのすべての再試行を試行し終わると、配信不能の場所にイベントを送信します。 Event Grid で 400 (正しくない要求) または 413 (要求のエンティティが大きすぎます) の応答コードが受信された場合、そのイベントの配信不能処理が即時にスケジュールされます。 これらの応答コードは、イベントの配信が決して成功しないことを示します。

Time-To-Live の期限が切れたかどうかは、スケジュールされている次の配信試行時にのみ確認されます。 したがって、スケジュールされている次の配信試行の前に Time-To-Live の期限が切れた場合でも、イベントの期限切れは次の配信時にのみチェックされ、その後で配信不能になります。 

イベントの配信を最後に試してから配信不能メッセージがその宛先に届くまで、5 分間の遅延があります。 この遅延の目的は、BLOB ストレージの操作数を減らすことにあります。 配信不能の場所が 4 時間にわたって使用できない場合、そのイベントは破棄されます。

配信不能の場所を設定するには、コンテナーを含むストレージ アカウントが必要です。 イベント サブスクリプションの作成時に、このコンテナーのエンドポイントを指定します。 エンドポイントの形式は次のとおりです。`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

イベントが配信不能の場所に送信されたら通知を受け取るようにすることもできます。 Event Grid を使用して配信不能イベントに応答するには、配信不能 Blob ストレージ用の[イベント サブスクリプションを作成](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)します。 配信不能 Blob ストレージが配信不能イベントを受信するたびに、Event Grid はハンドラーに通知します。 ハンドラーは、配信不能イベントを調整するためのアクションで応答します。 配信不能の場所と再試行ポリシーの設定の例については、[配信不能と再試行のポリシー](manage-event-delivery.md)に関する記事を参照してください。

## <a name="delivery-event-formats"></a>配信イベントの形式
このセクションでは、さまざまな配信スキーマ形式 (Event Grid スキーマ、CloudEvents 1.0 スキーマ、およびカスタム スキーマ) のイベントおよび配信不能イベントの例を示します。 これらの形式の詳細については、[Event Grid スキーマ](event-schema.md)および [CloudEvents 1.0 スキーマ](cloud-event-schema.md)に関する記事を参照してください。 

### <a name="event-grid-schema"></a>Event Grid スキーマ

#### <a name="event"></a>event 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>配信不能イベント

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 スキーマ

#### <a name="event"></a>event

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>配信不能イベント

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>カスタム スキーマ

#### <a name="event"></a>event

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>配信不能イベント
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>メッセージの配信状態

Event Grid は、HTTP 応答コードを使用してイベントの受信を確認します。 

### <a name="success-codes"></a>成功コード

Event Grid は、次の HTTP 応答コード **のみ** を正常な配信と見なします。 その他のすべてのステータス コードは配信失敗と見なされ、必要に応じて再試行されるか、配信不能とされます。 正常なステータス コードを受信したら、Event Grid は配信が完了したと見なします。

- 200 OK
- 201 Created
- 202 Accepted
- 203 権限のない情報
- 204 No Content

### <a name="failure-codes"></a>エラー コード

上記のセット (200 ～ 204) にない他のすべてのコードはエラーと見なされ、再試行されます (必要に応じて)。 一部には、以下で説明するように特定の再試行ポリシーが関連付けられていますが、他はすべて標準の指数的バックオフ モデルに従います。 重要な注意点としては、Event Grid のアーキテクチャは高度に並列化されているため、再試行の動作は非決定的です。 

| status code | 再試行の動作 |
| ------------|----------------|
| 400 Bad Request | 再試行されません |
| 401 権限がありません | 5 分以上後に、Azure のリソース エンドポイントに対して再試行 |
| 403 許可されていません | 再試行されません |
| 404 見つかりません | 5 分以上後に、Azure のリソース エンドポイントに対して再試行 |
| 408 要求タイムアウト | 2 分以上後に再試行 |
| 413 要求のエンティティが大きすぎます | 再試行されません |
| 503 サービス利用不可 | 30 秒以上後に再試行 |
| その他すべて | 10 秒以上後に再試行 |

## <a name="custom-delivery-properties"></a>カスタム配信のプロパティ
イベント サブスクリプションを使用すると、配信されたイベントに含まれる HTTP ヘッダーを設定できます。 この機能を使用すると、宛先に必要なカスタム ヘッダーを設定できます。 イベント サブスクリプションを作成するときに、最大 10 個のヘッダーを設定できます。 各ヘッダーの値は、4,096 (4 K) バイトより大きくすることはできません。 次の宛先に配信されるイベントにカスタム ヘッダーを設定できます。

- Webhooks
- Azure Service Bus のトピックとキュー
- Azure Event Hubs
- Relay ハイブリッド接続

詳細については、「[カスタム配信のプロパティ](delivery-properties.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ

* イベント配信のステータスを表示するには、[Event Grid によるメッセージの配信の監視](monitor-event-delivery.md) に関する記事をご覧ください。
* イベント配信オプションをカスタマイズするには、「[配信不能と再試行に関する方針](manage-event-delivery.md)」を参照してください。
