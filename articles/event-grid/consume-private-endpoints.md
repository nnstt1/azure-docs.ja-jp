---
title: プライベート リンク サービスを使用してイベントを配信する
description: この記事では、プライベート リンク サービスを使用してイベントを配信できないという制限に対処する方法について説明します。
ms.topic: how-to
ms.date: 07/01/2021
ms.openlocfilehash: 0672b2b93cf7413ac9a3d46e8d824354276ba89f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286119"
---
# <a name="deliver-events-using-private-link-service"></a>プライベート リンク サービスを使用してイベントを配信する
現時点では、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してイベントを配信することはできません。 つまり、配信されたイベント トラフィックがプライベート IP 空間から外に出てはならないという、ネットワークの分離の厳格な要件がある場合はサポートされません。 

## <a name="use-managed-identity"></a>マネージド ID の使用
ただし、暗号化されたチャネルと、パブリック IP 空間を使用する送信者 (この場合は Event Grid) の既知の ID を使用してイベントを安全に送信する方法が要件で求められる場合は、システム マネージド ID が構成された Azure イベント グリッド カスタム トピックまたはドメインを使用して、Event Hubs、Service Bus、または Azure Storage サービスにイベントを配信できます。 マネージド ID を使用したイベント配信の詳細については、「[マネージ ID を使用したイベント配信](managed-service-identity.md)」を参照してください。 

その後、Azure Functions で構成されたプライベート リンクまたは仮想ネットワークにデプロイされた Webhook を使用して、イベントをプルできます。 次のサンプルを参照してください:「[Azure Functions を使用してプライベート エンドポイントに接続する](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)」。


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="プライベート リンク サービスを介して配信する":::


この構成では、トラフィックはパブリック IP/インターネット経由で Event Grid から Event Hubs、Service Bus、または Azure Storage に送られますが、チャネルを暗号化でき、Event Grid のマネージド ID が使用されます。 Azure Functions、または仮想ネットワークにデプロイされた Webhook を、プライベート リンク経由で Event Hubs、Service Bus、または Azure Storage を使用するように構成すると、トラフィックのそのセクションは確実に Azure 内に留まります。

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>マネージド ID を使用して Event Hubs にイベントを配信する
マネージド ID を使用して Event Hubs 名前空間のイベント ハブにイベントを配信するには、これらの手順を実行します。

1. システム割り当て ID ([システム トピック](enable-identity-system-topics.md)、[カスタム トピック、ドメイン](enable-identity-custom-topics-domains.md)) を有効にします。  
1. [Event Hubs 名前空間で **Azure Event Hubs データ送信者** ロールに ID を追加します](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal)。
1. [Event Hubs 名前空間で、 **[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** 設定を有効にします](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services)。 
1. システム割り当て ID を使用するように、イベント ハブをエンドポイントとして使用する[イベント サブスクリプションを構成します](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)。

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>マネージド ID を使用して Service Bus にイベントを配信する
マネージド ID を使用して Service Bus 名前空間の Service Bus キューまたはトピックにイベントを配信するには、これらの手順を実行します。

1. システム割り当て ID ([システム トピック](enable-identity-system-topics.md)、[カスタム トピック、ドメイン](enable-identity-custom-topics-domains.md)) を有効にします。 
1. [Service Bus 名前空間で **Azure Service Bus データ送信者**](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus)ロールに ID を追加します。
1. [Service Bus 名前空間で、 **[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** 設定を有効にします](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services)。 
1. システム割り当て ID を使用するように、Service Bus のキューまたはトピックをエンドポイントとして使用する[イベント サブスクリプションを構成します](managed-service-identity.md)。

## <a name="deliver-events-to-storage"></a>Storage にイベントを配信する 
マネージド ID を使用して Storage キューにイベントを配信するには、これらの手順を実行します。

1. システム割り当て ID ([システム トピック](enable-identity-system-topics.md)、[カスタム トピック、ドメイン](enable-identity-custom-topics-domains.md)) を有効にします。 
1. [Azure Storage キューの **ストレージ キュー データ メッセージ送信者**](../storage/blobs/assign-azure-role-data-access.md)ロールに ID を追加します。
1. Storage キューをエンドポイントとして使用する[イベント サブスクリプションを構成し](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)、システムで割り当てられた ID を使用します。


## <a name="next-steps"></a>次のステップ
マネージド ID を使用したイベント配信の詳細については、「[マネージ ID を使用したイベント配信](managed-service-identity.md)」を参照してください。