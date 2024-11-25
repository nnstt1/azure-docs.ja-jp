---
title: Azure Event Grid でのカスタム トピック
description: Azure Event Grid でのカスタム トピックについて説明します。
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 54d331d2080d8adf96bf39c9250e7d6888723c1d
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727754"
---
# <a name="custom-topics-in-azure-event-grid"></a>Azure Event Grid でのカスタム トピック
Event Grid のトピックには、ソースがイベントを送信するエンドポイントが含まれます。 発行元は、イベント グリッド トピックを作成し、イベント ソースにトピックが 1 つ必要か、あるいは複数のイベント ソースが必要かを決定します。 トピックは、関連するイベントのコレクションに使用されます。 サブスクライバーは、特定の種類のイベントに応答するために、どのトピックをサブスクライブするかを決定します。

**カスタム トピック** は、アプリケーションとサード パーティのトピックです。 カスタムのトピックを作成した、またはカスタムのトピックへのアクセス権を割り当てられた場合は、サブスクリプション内にそのカスタム トピックが表示されます。 

アプリケーションを設計するときに、作成するトピック数を柔軟に決定することができます。 大規模なソリューションの場合、**関連するイベントのカテゴリごと** に **カスタム トピック** を作成します。 たとえば、ユーザー アカウントの変更と注文の処理に関連するイベントを送信するアプリケーションがあるものとします。 イベント ハンドラーで両方のイベント カテゴリが必要になることはまずありません。 このような場合は、2 つのカスタム トピックを作成し、イベント ハンドラーが自分と関係のあるトピックをサブスクライブできるようにします。 小規模なソリューションの場合、すべてのイベントを 1 つのトピックに送信することをお勧めします。 イベントのサブスクライバーは、必要なイベントの種類をフィルター処理できます。

## <a name="event-schema"></a>イベント スキーマ
イベント スキーマの詳細については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 カスタム トピックの場合、イベントの発行元が **データ** オブジェクトを決定します。 最上位レベルのデータには、リソースによって定義された標準のイベントと同じフィールドを含める必要があります。

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

以下のセクションでは、Azure portal、CLI、PowerShell、および Azure Resource Manager (ARM) テンプレートを使用してカスタム トピックを作成するためのチュートリアルへのリンクを示します。 


## <a name="azure-portal-tutorials"></a>Azure portal のチュートリアル
|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure portal を使ったカスタム イベントの作成とルーティング](custom-event-quickstart-portal.md) | ポータルを使ってカスタム イベントを送信する方法を示します。 |
| [クイック スタート: Azure Queue Storage へのカスタム イベントのルーティング](custom-event-to-queue-storage.md) | Queue Storage にカスタム イベントを送信する方法について説明します。 |
| [方法: カスタム トピックへの投稿](post-to-custom-topic.md) | カスタム トピックにイベントを投稿する方法を示します。 |


## <a name="azure-cli-tutorials"></a>Azure CLI のチュートリアル
|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI を使ったカスタム イベントの作成とルーティング](custom-event-quickstart.md) | Azure CLI を使ってカスタム イベントを送信する方法を示します。 |
| [Azure CLI: Event Grid のカスタム トピックの作成](./scripts/event-grid-cli-create-custom-topic.md)|カスタム トピックを作成するサンプル スクリプトです。 このスクリプトは、エンドポイントとキーを取得します。|
| [Azure CLI: カスタム トピックのイベントのサブスクライブ](./scripts/event-grid-cli-subscribe-custom-topic.md)|カスタム トピックのサブスクリプションを作成するサンプル スクリプトです。 Webhook にイベントを送信します。|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell のチュートリアル
|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure PowerShell を使ったカスタム イベントの作成とルーティング](custom-event-quickstart-powershell.md) | Azure PowerShell を使ってカスタム イベントを送信する方法を示します。 |
| [PowerShell: Event Grid のカスタム トピックの作成](./scripts/event-grid-powershell-create-custom-topic.md)|カスタム トピックを作成するサンプル スクリプトです。 このスクリプトは、エンドポイントとキーを取得します。|
| [PowerShell: カスタム トピックのイベントのサブスクライブ](./scripts/event-grid-powershell-subscribe-custom-topic.md)|カスタム トピックのサブスクリプションを作成するサンプル スクリプトです。 Webhook にイベントを送信します。|

## <a name="arm-template-tutorials"></a>ARM テンプレートのチュートリアル
|タイトル  |説明  |
|---------|---------|
| [Resource Manager テンプレート: カスタム トピックと Webhook エンドポイント](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid) | カスタム トピックとそのカスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Webhook にイベントを送信します。 |
| [Resource Manager テンプレート: カスタム トピックと Event Hubs エンドポイント](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| カスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Azure Event Hubs にイベントを送信します。 |

> [!NOTE]
> Azure Digital Twins では、Event Grid で作成するカスタム トピックにイベント通知をルーティングできます。 詳細については、「[Azure Digital Twins でのエンドポイントとルートの管理](../digital-twins/how-to-manage-routes.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 

- [システム トピック](system-topics.md)
- [ドメイン](event-domains.md)