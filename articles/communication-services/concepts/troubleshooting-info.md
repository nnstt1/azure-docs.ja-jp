---
title: Azure Communication Services でのトラブルシューティング
description: Communication Services ソリューションのトラブルシューティングに必要な情報を収集する方法について説明します。
author: manoskow
manager: chpalm
services: azure-communication-services
ms.author: manoskow
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 3cd19094b876203569df83cf3bc165968d9051a2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257982"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Azure Communication Services でのトラブルシューティング

このドキュメントでは、Communication Services ソリューションで発生する可能性がある問題のトラブルシューティングについて説明します。 SMS のトラブルシューティングを行う場合は、[Event Grid で配信レポートを有効にする](../quickstarts/telephony-sms/handle-sms-events.md)ことで、SMS の配信の詳細を取得できます。

## <a name="getting-help"></a>ヘルプの表示

開発者の皆さんは、ご質問の送信、機能のご提案、および問題のレポートに関してぜひご協力ください。 これを支援するために、サポートのためのオプションを一覧にした[サポートとヘルプのオプションの専用ページ](../support.md)を用意しています。

特定の種類の問題のトラブルシューティングを支援するために、次の情報をおたずねする場合があります。

* **MS-CV ID**: この ID は、通話とメッセージのトラブルシューティングに使用されます。
* **通話 ID**: この ID は、Communication Services の通話を識別するために使用されます。
* **SMS メッセージ ID**: この ID は、SMS メッセージを識別するために使用されます。
* **呼び出しログ**: これらのログには、呼び出しとネットワークの問題のトラブルシューティングに使用できる詳細情報が含まれています。


## <a name="access-your-ms-cv-id"></a>MS-CV ID にアクセスする

MS-CV ID にアクセスするには、SDK を初期化する際に、`clientOptions` オブジェクト インスタンスで診断を構成します。 診断は、チャット、ID、VoIP 通話など、任意の Azure SDK に対して構成できます。

### <a name="client-options-example"></a>クライアント オプションの例

次のコード スニペットは、診断の構成を示しています。 診断を有効にした状態で SDK を使用すると、構成済みのイベント リスナーに診断の詳細が出力されます。

# <a name="c"></a>[C#](#tab/csharp)
```
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
```
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-server-call-id"></a>サーバ通話 ID にアクセスする
通話のレコーディングや通話の管理の問題など、Call Automation SDK に関する問題のトラブルシューティングを行う場合は、サーバー通話 ID を収集する必要があります。 この ID は、```getServerCallId``` メソッドを使用して収集できます。

#### <a name="javascript"></a>JavaScript
```
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```


## <a name="access-your-client-call-id"></a>クライアント通話 ID にアクセスする

音声通話またはビデオ通話をトラブルシューティングする際に、`call ID` の提供を求められることがあります。 これは、`call` オブジェクトの `id` プロパティから取得できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.startCall` or `callAgent.join` methods
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId)
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.startCall(…)` or `callAgent.join(…)` methods
Log.d(call.getCallId())
```
---

## <a name="access-your-sms-message-id"></a>SMS メッセージ ID にアクセスする

SMS の問題については、応答オブジェクトからメッセージ ID を収集できます。

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>呼び出しログを有効にしてアクセスする

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Communication Services Calling SDK は、内部的に [@azure/logger](https://www.npmjs.com/package/@azure/logger) ライブラリを使用してログ記録を制御します。
ログ出力を構成するには、`@azure/logger` パッケージの `setLogLevel` メソッドを使用します。

```javascript
import { setLogLevel } from '@azure/logger';
setLogLevel('verbose');
const callClient = new CallClient();
```

AzureLogger を使用して、Azure SDK からのログ出力をリダイレクトするには、`AzureLogger.log` メソッドをオーバーライドします。コンソール以外の場所にログをリダイレクトしたい場合などに、これを使用できます。

```javascript
import { AzureLogger } from '@azure/logger';
// redirect log output
AzureLogger.log = (...args) => {
  console.log(...args); // to console, file, buffer, REST API..
};
```

# <a name="ios"></a>[iOS](#tab/ios)

iOS 用に開発する場合、ログは `.blog` ファイルに格納されます。 ログは暗号化されているため、直接表示できないことに注意してください。

これらにアクセスするには、Xcode を開きます。 [Windows]\(ウィンドウ\) > [Devices and Simulators]\(デバイスとシミュレーター\) > [Devices]\(デバイス\) に移動します。 デバイスを選択します。 [Installed Apps]\(インストールされているアプリ\) でアプリケーションを選択し、[Download container]\(コンテナーのダウンロード\) をクリックします。

これにより、`xcappdata` ファイルを取得できます。 このファイルを右クリックし、[Show package contents]\(パッケージの内容の表示\) を選択します。 その後、`.blog` ファイルを表示し、それを Azure サポート リクエストに添付できます。

# <a name="android"></a>[Android](#tab/android)

Android 用に開発する場合、ログは `.blog` ファイルに格納されます。 ログは暗号化されているため、直接表示できないことに注意してください。

Android Studio で、シミュレーターとデバイスの両方から [View]\(表示\) > [Tool Windows]\(ツール ウィンドウ\) > [Device File Explorer]\(デバイス ファイル エクスプローラー\) を選択して、デバイス ファイル エクスプローラーに移動します。 `.blog` ファイルはアプリケーションのディレクトリ内にあり、`/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` のように表示されるはずです。 このファイルをサポート リクエストに添付できます。

---

## <a name="enable-and-access-call-logs-windows"></a>呼び出しログを有効にしてアクセスする (Windows)

Windows 用に開発する場合、ログは `.blog` ファイルに格納されます。 ログは暗号化されているため、直接表示できないことに注意してください。

これらは、アプリがどこにローカル データを保持しているかを調べることでアクセスできます。 UWP アプリがどこにローカル データを保持しているかを調べるには、さまざまな方法があり、次の手順はこれらの方法の 1 つにすぎません。
1. Windows コマンド プロンプトを開きます (Windows キー + R キー)
2. 「`cmd.exe`」と入力します
3. 「`where /r %USERPROFILE%\AppData acs*.blog`」と入力します
4. アプリケーションのアプリ ID が、前のコマンドによって返されたものと一致するかどうかを確認してください。
5. 「`start `」の後に手順 3 で返されたパスを入力して、ログが含まれているフォルダーを開きます。 例: `start C:\Users\myuser\AppData\Local\Packages\e84000dd-df04-4bbc-bf22-64b8351a9cd9_k2q8b5fxpmbf6`
6. `*.blog` と `*.etl` のすべてのファイルを Azure サポート リクエストに添付してください。


## <a name="calling-sdk-error-codes"></a>Calling SDK のエラー コード

Azure Communication Services の Calling SDK では、通話の問題のトラブルシューティングに役立つ次のエラー コードを使用します。 これらのエラー コードは、通話の終了後に `call.callEndReason` プロパティを通じて公開されます。

| エラー コード | 説明 | 実行するアクション |
| -------- | ---------------| ---------------|
| 403 | 禁止または認証エラー。 | Communication Services トークンが有効であり、有効期限が切れていないことを確認します。 |
| 404 | 通話が見つかりません。 | 通話先の番号 (または参加している通話) が存在することを確認します。 |
| 408 | 通話コントローラーがタイムアウトしました。 | ユーザー エンドポイントからのプロトコル メッセージの待機中に通話コントローラーがタイムアウトしました。 クライアントが接続され、使用可能であることを確認します。 |
| 410 | ローカル メディア スタックまたはメディア インフラストラクチャ エラー。 | サポートされている環境で最新の SDK を使用していることを確認します。 |
| 430 | クライアント アプリケーションにメッセージを配信できません。 | クライアント アプリケーションが実行されていて使用可能であることを確認します。 |
| 480 | リモート クライアント エンドポイントが登録されていません。 | リモート エンドポイントが使用可能であることを確認します。 |
| 481 | 着信通話の処理に失敗しました。 | Azure portal からサポート リクエストを提出します。 |
| 487 | エンドポイントの不一致の問題が原因で通話がキャンセルされたか、ローカルで拒否されたか、終了しました。または、メディア プランの生成に失敗しました。 | 想定されている動作です。 |
| 490、491、496、487、498 | ローカル エンドポイント ネットワークの問題。 | ネットワークを確認します。 |
| 500、503、504 | Communication Services インフラストラクチャ エラー。 | Azure portal からサポート リクエストを提出します。 |
| 603 | Communication Services のリモート参加者によって、通話がグローバルに拒否されました。 | 想定されている動作です。 |

## <a name="chat-sdk-error-codes"></a>Chat SDK のエラー コード

Azure Communication Services の Chat SDK では、チャットの問題のトラブルシューティングに役立てるために次のエラー コードを使用しています。 エラー コードは、エラー応答の `error.code` プロパティを通じて公開されます。

| エラー コード | 説明 | 実行するアクション |
| -------- | ---------------| ---------------|
| 401 | 権限がありません | Communication Services トークンが有効であり、有効期限が切れていないことを確認します。 |
| 403 | Forbidden | 要求のイニシエーターがリソースへのアクセス権を持っていることを確認してください。 |
| 429 | 要求が多すぎます | クライアント側アプリケーションで、このシナリオがユーザーフレンドリな方法で処理されていることを確認してください。 エラーが解決しない場合は、サポート リクエストを提出してください。 |
| 503 | サービス利用不可 | Azure portal からサポート リクエストを提出します。 |

## <a name="related-information"></a>関連情報
- [ログと診断](logging-and-diagnostics.md)
- [メトリック](metrics.md)
