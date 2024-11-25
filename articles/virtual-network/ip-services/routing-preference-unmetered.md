---
title: Azure での従量制課金なしのルーティング優先設定
description: CDN プロバイダーにデータを送信するリソースのルーティング設定を構成する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e4a1fcc508b83eae0d016fec02808047dfc79009
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224875"
---
# <a name="what-is-routing-preference-unmetered"></a>従量制課金なしのルーティング優先設定とは

従量制課金なしのルーティング優先設定は、顧客が Azure で元のコンテンツをホストしているコンテンツ配信ネットワーク (CDN) プロバイダーで使用できます。 このサービスを使用すると、CDN プロバイダーは、さまざまな場所で Microsoft グローバル ネットワーク エッジ ルーターとの直接ピアリング接続を確立できます。

![従量制課金なしのルーティング優先設定](./media/routing-preference-unmetered/unmetered.png)

Azure の配信元から CDN プロバイダーに送信されるネットワーク トラフィックには、直接接続の利点があります。
* これらの直接リンクを介してルーティングされる Azure リソースからのトラフィック送信のデータ転送課金は無料です。
* Azure の CDN プロバイダーと配信元の間の直接接続では、間にホップが存在しないため、最適なパフォーマンスが得られます。 これは、配信元からのデータを頻繁にフェッチする CDN ワークロードに役立ちます。

## <a name="configuring-routing-preference-unmetered"></a>従量制課金なしのルーティング優先設定の構成

従量制課金なしのルーティング優先設定を利用するには、CDN プロバイダーがこのプログラムに含まれている必要があります。 CDN プロバイダーがプログラムに含まれていない場合は、CDN プロバイダーに問い合わせてください。

次に、リソースのルーティング設定を構成し、ルーティング設定の種類を **[インターネット]** に設定します。 パブリック IP アドレスの作成中にルーティング優先設定を構成し、パブリック IP を仮想マシンやインターネットに接続するロード バランサーなどのリソースに関連付けることができます。 [Azure portal を使用してパブリック IP アドレスのルーティング優先設定を構成する方法を説明します](./routing-preference-portal.md)

また、ストレージ アカウントのルーティング優先設定を有効にし、2 番目のエンドポイントを発行することもできます。このエンドポイントは、CDN プロバイダーによって使用されてストレージの配信元からデータをフェッチする必要があります。 たとえば、ストレージ アカウント *StorageAccountA* に対してインターネット ルート固有のエンドポイントを公開すると、次のように、ストレージ サービスに対して 2 番目のエンド ポイントが公開されます。

![ストレージ アカウントのルーティング優先設定](./media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>次のステップ

* [Azure PowerShell を使用して VM 用にルーティング優先設定を構成する](./configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLI を使用して VM 用にルーティング優先設定を構成する](./configure-routing-preference-virtual-machine-cli.md)
* [ストレージ アカウントのルーティング優先設定を構成する](../../storage/common/network-routing-preference.md)