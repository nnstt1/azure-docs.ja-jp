---
title: 専用イベント ハブの概要 - Azure Event Hubs | Microsoft Docs
description: この記事では、イベント ハブのシングル テナント デプロイを提供する専用の Azure Event Hubs の概要について説明します。
ms.topic: article
ms.date: 09/23/2021
ms.openlocfilehash: 696df96c9ac833d937fb89c52842b09ace4c4ec5
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210720"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated の概要

*Event Hubs クラスター* は、最も厳しいストリーミングのニーズを持つお客様にシングルテナント デプロイを提供します。 このシングルテナントのサービスでは、99.99% の SLA が保証されており、専用の価格レベルでのみご利用いただけます。 Event Hubs クラスターでは、1 秒あたり数百万のイベントを、保証された容量と 1 秒未満の待ち時間でイングレスすることができます。 専用クラスター内で作成された名前空間とイベント ハブには、プレミアム サービスのすべての機能とそれ以外の機能が含まれますが、イングレスに関する制限はありません。 また、一般的な [Event Hubs Capture](event-hubs-capture-overview.md) 機能も、追加コストなしで含まれています。 この機能を使用すると、Azure Storage または Azure Data Lake に対してデータ ストリームを自動的にバッチおよびログ処理することができます。 

クラスターは、事前に割り当てられた CPU とメモリ リソースの量である **容量ユニット (CU)** を基準にプロビジョニングおよび請求が行われます。 クラスターごとに 1、2、4、8、12、16、または 20 の CU を購入することができます。 CU あたりの取り込みとストリーミングの量は、次のようなさまざまな要因によって異なります。 

- プロデューサーとコンシューマーの数
- ペイロードの形態
- エグレス レート

> [!NOTE]
> すべての Event Hubs クラスターは既定で Kafka に対応しており、既存の Kafka ベースのアプリケーションで使用できる Kafka エンドポイントをサポートしています。 クラスターで Kafka を有効にしても、Kafka 以外のユース ケースには影響しません。クラスターで Kafka を無効にするオプションはなく、無効にする必要もありません。

## <a name="why-dedicated"></a>専用にする理由

専用 Event Hubs では、エンタープライズ レベルの容量を必要とするお客様に 3 つの魅力的なベネフィットが提供されます。

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>シングル テナントではパフォーマンス向上のための容量が保証される

専用クラスターによって、完全なスケールで容量が保証されます。 トラフィックのバーストに対応できる十分な耐久性を備えたストレージと 1 秒未満の待機時間により、ギガバイト単位のストリーミング データまでイングレスできます。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>機能への包括アクセスと排他アクセス

専用オファリングには、追加コストなしでのキャプチャや、BYOK (Bring Your Own Key) などの機能への排他アクセスなどの機能があります。 また、負荷分散、OS の更新、セキュリティ パッチ、パーティション分割は、サービスによって管理されます。 そのため、インフラストラクチャのメンテナンスにかかる時間が短縮され、クライアント側の機能を構築するためにより多くの時間を費やすことができます。  

## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated のクォータと制限
Event Hubs Dedicated オファリングは固定の月額料金で課金され、最低の使用量は 4 時間です。 専用レベルの機能はプレミアムプランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けに、エンタープライズ規模の容量と制限で提供されます。 

クォータと制限に関する詳細については、[Event Hubs のクォータと制限](event-hubs-quotas.md)に関する記事を参照してください

## <a name="how-to-onboard"></a>利用を開始する方法

[Azure portal](https://aka.ms/eventhubsclusterquickstart) を使用して [Event Hubs クラスター](event-hubs-dedicated-cluster-create-portal.md)を作成するセルフサービス エクスペリエンスは、プレビュー段階になりました。 不明な点がある場合や、Event Hubs Dedicated へのオンボードについてサポートが必要な場合は、[Event Hubs チーム](mailto:askeventhubs@microsoft.com)にお問い合わせください。

## <a name="faqs"></a>FAQ

[!INCLUDE [event-hubs-dedicated-clusters-faq](./includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>次のステップ

Event Hubs Dedicated の詳細については、Microsoft の営業担当者または Microsoft サポートにお問い合わせください。 次のリンク先で、クラスターを作成することや、Event Hubs の価格レベルの詳細を確認することもできます。

- [Azure portal を使用して Event Hubs クラスターを作成する](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated の価格](https://azure.microsoft.com/pricing/details/event-hubs/)。 Microsoft の営業担当者または Microsoft サポートに連絡して、Event Hubs Dedicated 容量の詳細を確認することもできます。
- 「[Event Hubs の FAQ](event-hubs-faq.yml)」では、Event Hubs の価格の情報について説明し、よく寄せられる質問のいくつかに回答します。
