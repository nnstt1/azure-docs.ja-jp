---
title: Azure Service Bus の Premium レベルと Standard レベル
description: この記事では、Azure Service Bus の Standard レベルと Premium レベルについて説明します。 これらのレベルを比較して、技術的な違いを示します。
ms.topic: conceptual
ms.date: 11/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0734e6d7d54966617e66a5ac5876df4a1da9264f
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028783"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus の Premium および Standard メッセージング レベル

Service Bus メッセージングには、キューやトピックなどのエンティティが含まれており、エンタープライズ メッセージング機能と、クラウド スケールの豊富な発行/サブスクライブ セマンティクスが結合されます。 Service Bus メッセージングは、多くの高度なクラウド ソリューションで、通信のバックボーンとして使用されます。

Service Bus メッセージングに *Premium* レベルを導入して、ミッション クリティカルなアプリケーションのスケール、パフォーマンス、および可用性に関する顧客の一般的な要求に対処しています。 運用環境シナリオには、Premium レベルをお勧めします。 機能セットとほぼ同じですが、Service Bus メッセージングのこれら 2 つのレベルは、さまざまなユース ケースに応えるように設計されています。

次の表に、大まかな違いをいくつか示します。

| Premium | Standard |
| --- | --- |
| 高スループット |変わりやすいスループット |
| 予測可能なパフォーマンス |変わりやすい待機時間 |
| 固定価格 |従量課金制の変わりやすい料金 |
| ワークロードをスケールアップおよびスケールダウンする機能 |該当なし |
| 最大 100 MB のメッセージ サイズ。 詳細については、[大量メッセージ サポート](#large-messages-support)に関する記事を参照してください。 |最大 256 KB のメッセージ サイズ |

**Service Bus Premium メッセージング** では、各顧客のワークロードが分離した状態で実行されるように、CPU とメモリのレベルでリソースが分離されます。 このリソースのコンテナーを、*メッセージング ユニット* と呼びます。 各 Premium 名前空間には、1 つ以上のメッセージング ユニットが割り当てられます。 各 Service Bus Premium 名前空間に対して 1、2、4、8、または 16 のメッセージング ユニットを購入することができます。 1 つのワークロードまたはエンティティが複数のメッセージング ユニットにまたがることができ、メッセージング ユニットの数は任意で変更できます。 その結果、Service Bus ベースのソリューションのパフォーマンスは、予測可能で反復可能になります。

このパフォーマンスは、より予測可能かつ利用可能なだけでなく、より高速です。 Premium メッセージングでのピークのパフォーマンスは、Standard レベルよりもはるかに高速です。

## <a name="premium-messaging-technical-differences"></a>Premium メッセージングの技術的な相違点

次のセクションでは、Premium メッセージング レベルと Standard メッセージング レベルのいくつかの違いについて説明します。

### <a name="partitioned-queues-and-topics"></a>パーティション分割されたキューとトピック

Premium メッセージングでは、パーティション分割されたキューとトピックをサポートしていません。 パーティション分割の詳細については、「[パーティション分割されたキューとトピック](service-bus-partitioning.md)」を参照してください。

### <a name="express-entities"></a>エクスプレス エンティティ

分離されたランタイム環境で Premium メッセージングが実行されるため、Premium 名前空間ではエクスプレス エンティティがサポートされません。 エクスプレス エンティティでは、メッセージを永続記憶装置に書き込む前に、一時的にメモリに保持します。 Standard メッセージングで実行しているコードがあり、それを Premium レベルに移植したい場合は、エクスプレス エンティティ機能が無効になっていることを確認します。

## <a name="premium-messaging-resource-usage"></a>Premium メッセージング リソースの使用
一般に、エンティティに対するどの操作でも、CPU とメモリが使用されます。 これらいくつかの操作を次に示します。 

- キュー、トピック、サブスクリプションに対する CRUD (Create、Retrieve、Update、Delete (作成、取得、更新、削除)) などの管理操作。
- ランタイム操作 (メッセージの送受信)
- 操作とアラートの監視

ただし、追加の CPU とメモリ使用によりさらに課金されることはありません。 Premium メッセージング レベルでは、メッセージング ユニットの料金は単一です。

次の理由で、CPU とメモリの使用が追跡され、表示されます。 

- システム内部に透明性を提供する。
- 購入したリソースの容量を把握する。
- スケール アップ/スケール ダウンを判断するのに役立つ容量計画。

## <a name="messaging-unit---how-many-are-needed"></a>メッセージング ユニット - 必要な数

Azure Service Bus Premium 名前空間をプロビジョニングする場合は、割り当てられるメッセージング ユニット数を指定する必要があります。 これらのメッセージング ユニットは、名前空間に割り当てられる専用リソースです。

Service Bus Premium 名前空間に割り当てられるメッセージング ユニット数は、ワークロードの変化 (増加または減少) を考慮して **動的に調整** できます。

アーキテクチャのメッセージング ユニット数を決定するときは、いくつかの要素を考慮する必要があります。

- 名前空間に割り当てられた ***1 つまたは 2 つのメッセージング ユニット*** から始めます。
- 名前空間の[リソース使用状況メトリック](monitor-service-bus-reference.md#resource-usage-metrics)内の CPU 使用率メトリックを調査します。
    - CPU 使用率が "***20% を下回る** _" 場合は、名前空間に割り当てられたメッセージング ユニット数を "_ *_スケールダウン_*" できる可能性があります。
    - CPU 使用率が "***70% を超える** _" 場合は、名前空間に割り当てられたメッセージング ユニット数を "_ *_スケールアップ_**" すると、アプリケーションにメリットがあります。

Service Bus 名前空間を構成して、自動スケーリングする (メッセージング ユニットを増減する) 方法については、「[メッセージング ユニットを自動的に更新する](automate-update-messaging-units.md)」を参照してください。

> [!NOTE]
> 名前空間に割り当てられたリソースの **スケール** は、プリエンティブまたはリアクティブにすることができます。
>
>  * **プリエンプティブ**:(季節性や傾向により) 追加のワークロードが予想される場合は、ワークロードが増える前に、さらに多くのメッセージング ユニットを名前空間に割り当てることができます。
>
>  * **リアクティブ**:リソース使用状況メトリックを調べて追加のワークロードが特定された場合、増加する需要を組み込むために追加のリソースを名前空間に割り当てることができます。
>
> Service Bus の課金メーターは時間単位です。 スケールアップの場合は、これらが使用された時間の追加リソースに対してのみ課金されます。
>

## <a name="get-started-with-premium-messaging"></a>Premium メッセージングを使ってみる

Premium メッセージングは簡単に使い始めることができ、そのプロセスは Standard メッセージングと似ています。 まず、[Azure Portal](https://portal.azure.com) で[名前空間を作成](service-bus-create-namespace-portal.md)します。 **[価格レベル]** で **[Premium]** を選択します。 **[価格の詳細を表示]** をクリックして、各レベルについての詳細を表示します。

:::image type="content" source="./media/service-bus-premium-messaging/select-premium-tier.png" alt-text="名前空間の作成時の Premium レベルの選択を示すスクリーンショット。":::

[Azure Resource Manager テンプレートを使用して Premium 名前空間](https://azure.microsoft.com/resources/templates/servicebus-pn-ar/)を作成することもできます。

## <a name="large-messages-support"></a>大きいメッセージのサポート
Azure Service Bus Premium レベルの名前空間では、最大 100 MB の大きいメッセージ ペイロードを送信する機能がサポートされています。 この機能は主に、他のエンタープライズ メッセージング ブローカー上で大きいメッセージ ペイロードが使用され、Azure Service Bus にシームレスに移行しようとしているレガシ ワークロードを対象としています。

Azure Service Bus 上で大きいメッセージを送信するときの考慮事項をいくつか次に示します。
   * Azure Service Bus Premium レベルの名前空間上でのみサポートされます。
   * AMQP プロトコルを使用する場合にのみサポートされます。 SBMP プロトコルを使用する場合はサポートされません。
   * [Java Message Service (JMS) 2.0 クライアント SDK](how-to-use-java-message-service-20.md) および他の言語のクライアント SDK を使用しているときにサポートされます。
   * 大きいメッセージを送信すると、スループットが低下し、待機時間が長くなります。
   * 100 MB のメッセージ ペイロードがサポートされていますが、Service Bus 名前空間からのパフォーマンスの信頼性を確保するために、メッセージ ペイロードはできるだけ小さくすることをお勧めします。
   * 最大メッセージ サイズは、キューまたはトピックに送信されたメッセージにのみ適用されます。 サイズ制限は、受信操作には適用されません。 このため、特定のキュー (またはトピック) の最大メッセージ サイズは更新できます。

### <a name="enabling-large-messages-support-for-a-new-queue-or-topic"></a>新しいキュー (またはトピック) に対して大きいメッセージのサポートを有効にする

大きいメッセージのサポートを有効にするには、次に示すように、新しいキュー (またはトピック) を作成するときに最大メッセージ サイズを設定します。 

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview.png" alt-text="既存のキューに対して大きいメッセージのサポートを有効にする方法を示すスクリーンショット。":::

### <a name="enabling-large-messages-support-for-an-existing-queue-or-topic"></a>既存のキュー (またはトピック) に対して大きいメッセージのサポートを有効にする

既存のキュー (またはトピック) に対して大きいメッセージのサポートを有効にすることもできます。これを行うには、以下のように、その特定のキュー (またはトピック) の **_[概要]_** で **[Max message size]\(最大メッセージ サイズ\)** を更新します。

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview-update.png" alt-text="大きいメッセージのサポートが有効になっている [キューの作成] ページのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のリンクをご覧ください。

- [メッセージング ユニットを自動的に更新する](automate-update-messaging-units.md)
- [Azure Service Bus Premium メッセージングの概要 (ブログの投稿)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure Service Bus Premium メッセージングの概要 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
