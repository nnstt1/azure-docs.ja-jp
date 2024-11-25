---
title: 接続文字列を取得する - Azure Event Hubs | Microsoft Docs
description: この記事では、クライアントが Azure Event Hubs への接続に使用できる接続文字列を取得する方法について説明します。
ms.topic: article
ms.date: 07/23/2021
ms.openlocfilehash: 67a20adb89ffe67546e9704896542f308a243dc3
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665410"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs の接続文字列の取得

Event Hubs を使用するには、Event Hubs の名前空間を作成する必要があります。 名前空間は、複数のイベント ハブまたは Kafka トピック用のスコープ コンテナーです。 この名前空間より、一意の [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) が提供されます。 名前空間を作成した後は、Event Hubs との通信に必要な接続文字列を取得できます。

Azure Event Hubs の接続文字列には、次のコンポーネントが埋め込まれています。

* FQDN = 作成した Event Hubs 名前空間の FQDN (Event Hubs 名前空間の名前に続けて servicebus.windows.net が含まれます)
* SharedAccessKeyName = アプリケーションの SAS キーに選んだ名前
* SharedAccessKey = キーの生成された値。

接続文字列テンプレートは次のようになります
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

接続文字列は、たとえば次のようになります `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

この記事では、接続文字列を取得するさまざまな方法について説明します。

## <a name="get-connection-string-from-the-portal"></a>ポータルから接続文字列を取得する
1. [Azure ポータル](https://portal.azure.com)にサインインします。 
2. 左のナビゲーション メニューから、 **[すべてのサービス]** を選択します。 
3. **[分析]** セクションで **[Event Hubs]** を選択します。 
4. イベント ハブの一覧で、自分のイベント ハブを選択します。
6. **[Event Hubs 名前空間]** ページで、左側のメニューの **[共有アクセス ポリシー]** を選択します。
7. ポリシーの一覧で **共有アクセス ポリシー** を選択します。 既定のポリシーの名前は **RootManageSharedAccessPolicy** です。 適切なアクセス許可 (読み取り、書き込み) を持つポリシーを追加し、そのポリシーを使用できます。 

    :::image type="content" source="./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png" alt-text="Event Hubs の共有アクセス ポリシー":::
8. **[接続文字列 - 主キー]** フィールドの隣にある **コピー** ボタンを選択します。 

    :::image type="content" source="./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png" alt-text="Event Hubs - 接続文字列の取得":::

## <a name="getting-the-connection-string-with-azure-powershell"></a>Azure PowerShell を使用した接続文字列の取得

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下に示すように、[Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey)を使用して、指定するポリシーまたはルール名の接続文字列を取得することができます。

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName MYRESOURCEGROUP -NamespaceName MYEHUBNAMESPACE -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Azure CLI を使用した接続文字列の取得
次を使用して、名前空間の接続文字列を取得できます。

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group MYRESOURCEGROUP --namespace-name MYEHUBNAMESPACE --name RootManageSharedAccessKey
```

また、次を使用して、EventHub エンティティの接続文字列を取得することもできます。

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group MYRESOURCEGROUP --namespace-name MYEHUBNAMESPACE --eventhub-name MYEHUB --name RootManageSharedAccessKey
```

Event Hubs 用の Azure CLI コマンドについて詳しくは、[Event Hubs 用の Azure CLI](/cli/azure/eventhubs) に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
* [Event Hub を作成する](event-hubs-create.md)
