---
title: AMQP 1.0 でレガシ WindowsAzure.ServiceBus .NET フレームワーク ライブラリを使用|Microsoft Docs
description: この記事では、AMQP (Advanced Messaging Queuing Protocol) でレガシ WindowsAzure.ServiceBus .NET フレームワーク ライブラリを使用する方法について説明します。
ms.topic: article
ms.date: 04/30/2021
ms.openlocfilehash: 160da6a770e58e9a76e966f018d25dc9bc8a8c76
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108770067"
---
# <a name="use-legacy-windowsazureservicebus-net-framework-library-with-amqp-10"></a>AMQP 1.0 でレガシ WindowsAzure.ServiceBus .NET フレームワーク ライブラリを使用

> [!NOTE]
> この記事は、同じパッケージ内の AMQP に切り替えて使用する場合の WindowsAzure.ServiceBus パッケージの既存のユーザー向けです。 このパッケージは引き続き重要なバグ修正プログラムを受け取りますが、2020 年 11 月の時点で利用可能であり既定で AMQP をサポートする新しい [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) パッケージへアップグレードされることを強く推奨します。

既定では、WindowsAzure.ServiceBus パッケージは、Service Bus メッセージング プロトコル (SBMP) と呼ばれる専用 SOAP ベースのプロトコルを使用して Service Bus サービスと通信します。 バージョン 2.1 では、AMQP 1.0 のサポートが追加されました。既定のプロトコルではなく 使用することをお勧めします。

既定のプロトコルの代わりに AMQP 1.0 を使用するには、Service Bus 接続文字列を使用して、または [TransportType](/dotnet/api/microsoft.servicebus.messaging.transporttype) オプション経由でクライアント コンストラクターにおいて明示的に構成する必要があります。 AMQP 1.0 を使用する場合、アプリケーション コードはこの変更以外は変更されません。

AMQP を使用する際にサポートされていない API 機能がいくつかあります。 これらのサポートされていない機能については、「[動作の違い](#behavioral-differences)」をご覧ください。 そのほか、AMQP を使用すると意味が変わる詳細な構成設定もいくつかあります。

## <a name="configure-connection-string-to-use-amqp-10"></a>接続文字列を構成して AMQP 1.0 を使用する方法

`;TransportType=Amqp` で接続文字列を追加して、AMQP 1.0 を使用して Service Bus に接続するようにクライアントに指示します。
たとえば、次のように入力します。 

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

`namespace` と `SAS key` は、Service Bus 名前空間を作成するときに、[Azure portal][Azure portal] から取得します。 詳細については、「[Azure portal を使用して Service Bus 名前空間を作成する][Create a Service Bus namespace using the Azure portal]」を参照してください。

### <a name="amqp-over-websockets"></a>AMQP over WebSocket
AMQP over WebSockets を使用するには、接続文字列の `TransportType` を `AmqpWebSockets` に設定します。 たとえば、 `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`と指定します。 

## <a name="message-serialization"></a>メッセージのシリアル化

既定のプロトコルを使用する場合、.NET クライアント ライブラリの既定のシリアル化の動作として、クライアント ライブラリと Service Bus サービスの間のトランスポートのために [DataContractSerializer][DataContractSerializer] 型を使用して [BrokeredMessage][BrokeredMessage] インスタンスをシリアル化します。 AMQP トランスポート モードを使用すると、クライアント ライブラリでは、[ブローカー メッセージ][BrokeredMessage]を AMQP メッセージにシリアル化するために、AMQP 型システムが使用されます。 このシリアル化によって、別のプラットフォームで実行されている可能性のある受信側アプリケーション (JMS API を使用して Service Bus にアクセスする Java アプリケーションなど) で、メッセージを受信して解釈できるようになります。

[BrokeredMessage][BrokeredMessage] インスタンスを構築すると、メッセージの本文として機能する .NET オブジェクトをパラメーターとしてコンストラクターに提供できます。 AMQP プリミティブ型にマップできるオブジェクトの場合、本文は AMQP データ型にシリアル化されます。 オブジェクトを AMQP プリミティブ型 (つまり、アプリケーションによって定義されているカスタム型) に直接マップできない場合、オブジェクトは [DataContractSerializer][DataContractSerializer] を使用してシリアル化され、シリアル化されたバイトが AMQP データ メッセージで送信されます。

.NET 以外のクライアントとの相互運用性を高めるには、メッセージ本文の AMQP 型に直接シリアル化できる .NET 型のみを使用してください。 次の表は、それらの型と、対応する AMQP 型システムへのマッピングを示します。

| .NET 本文のオブジェクト型 | 対応する AMQP の型 | AMQP 本文セクションの型 |
| --- | --- | --- |
| [bool] |boolean |AMQP 値 |
| byte |ubyte |AMQP 値 |
| ushort |ushort |AMQP 値 |
| uint |uint |AMQP 値 |
| ulong |ulong |AMQP 値 |
| sbyte |byte |AMQP 値 |
| short |short |AMQP 値 |
| INT |INT |AMQP 値 |
| long |long |AMQP 値 |
| float |float |AMQP 値 |
| double |double |AMQP 値 |
| decimal |decimal128 |AMQP 値 |
| char |char |AMQP 値 |
| DateTime |timestamp |AMQP 値 |
| Guid |uuid |AMQP 値 |
| byte[] |binary |AMQP 値 |
| string |string |AMQP 値 |
| System.Collections.IList |list |AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されているアイテムのみです。 |
| System.Array |array |AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されているアイテムのみです。 |
| System.Collections.IDictionary |map |AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されている項目のみです。注: String キーのみがサポートされます。 |
| Uri |記述子付き string (次の表を参照) |AMQP 値 |
| DateTimeOffset |記述子付き long (次の表を参照) |AMQP 値 |
| TimeSpan |記述子付き long (次の表を参照) |AMQP 値 |
| ストリーム |binary |AMQP データ (複数の場合あり)。 データ セクションには、Stream オブジェクトから読み取られた未加工のバイトが格納されます。 |
| その他のオブジェクト |binary |AMQP データ (複数の場合あり)。 DataContractSerializer またはアプリケーションから提供されるシリアライザーを使用するオブジェクトのシリアル化されたバイナリが格納されます。 |

| .NET 型 | 対応する AMQP の記述子付き型 | Notes |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>動作の違い

WindowsAzure.ServiceBus API の動作は、AMQP を使用する場合と既定のプロトコルを使用する場合では以下のようないくつかの細かい違いがあります。

* [OperationTimeout][OperationTimeout] プロパティは無視されます。
* `MessageReceiver.Receive(TimeSpan.Zero)` は `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` として実装されます。
* ロック トークンによるメッセージの完了は、最初にメッセージを受信したメッセージ レシーバーでのみ実行できます。

## <a name="control-amqp-protocol-settings"></a>AMQP プロトコル設定を制御する

[.NET API](/dotnet/api/) では、AMQP プロトコルの動作を制御するいくつかの設定が公開されています。

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)** : リンクに適用する初期のクレジットを制御します。 既定値は 0 です。
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)** : 接続オープン時のネゴシエーションで提供される最大 AMQP フレーム サイズを制御します。 既定値は 65,536 バイトです。
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)** : 転送がバッチ可能である場合、この値でディスポジションを送信する場合の最大遅延が決まります。 既定では、送信側/受信側によって継承されます。 個々の送信側/受信側は、既定値 (20 ミリ秒) をオーバーライドできます。
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)** : AMQP 接続を TLS 接続で確立するかどうかを制御します。 既定値は **true** です。

## <a name="next-steps"></a>次のステップ

さらに詳しい情報については、 次のリンク先を参照してください。

* [Service Bus AMQP の概要]
* [AMQP 1.0 プロトコル ガイド]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[Azure portal]: https://portal.azure.com
[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[AMQP 1.0 プロトコル ガイド]: service-bus-amqp-protocol-guide.md
