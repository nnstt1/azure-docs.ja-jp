---
title: Azure Service Bus での AMQP エラーのトラブルシューティング | Microsoft Docs
description: Azure Service Bus を使用しているときに受け取る可能性がある AMQP エラーの一覧と、それらのエラーの原因を示します。
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0a481f0dccc7a409ada51fb809065fd58db31aed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570951"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure Service Bus での AMQP エラー
この記事では、Azure Service Bus で AMQP を使用しているときに受け取るエラーの一部を示します。 それらはすべてサービスの標準的な動作です。 接続/リンクで送信/受信の呼び出しを行うことで、それらを回避できます。接続/リンクが自動的に再作成されます。

## <a name="link-is-closed"></a>リンクが閉じられる 
AMQP の接続とリンクがアクティブになっても、10 分間リンクを使用して呼び出しが行われないと (たとえば、送信または受信)、次のエラーが表示されます。 そのため、リンクは閉じられます。 接続はまだ開いています。

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>接続が閉じられる
アクティビティがない (アイドル) ために接続のすべてのリンクが閉じられた後、5 分以内に新しいリンクが作成されないと、AMQP 接続で次のエラーが表示されます。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>リンクが作成されない 
新しい AMQP 接続が作成されても、AMQP 接続の作成から 1 分以内にリンクが作成されないと、次のエラーが表示されます。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>次のステップ

AMQP と Service Bus の詳細については、次のリンクを参照してください。

* [Service Bus AMQP の概要]
* [AMQP 1.0 プロトコル ガイド]
* [Windows Server 用 Service Bus の AMQP]

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[AMQP 1.0 プロトコル ガイド]: service-bus-amqp-protocol-guide.md
[Windows Server 用 Service Bus の AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)