---
title: パッケージ化と配信の移行ガイダンス
description: この記事では、Azure Media Services v2 から v3 への移行に役立つパッケージ化と配信のシナリオ ベースのガイダンスを示します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d8cba5c69802828a0bd486d902d85199f3ae1d00
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322155"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>パッケージ化と配信のシナリオ ベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、Azure Media Services v2 から v3 への移行に役立つパッケージ化と配信のシナリオ ベースのガイダンスを示します。

v3 API でコンテンツが発行される方法への主な変更点。 新しい発行モデルは簡略化されており、より少ないエンティティを使用してストリーミング ロケーターを作成します。 API がわずか 2 つのエンティティに縮小されましたが、以前は 4 つのエンティティが必要でした。 コンテンツ キー ポリシーとストリーミング ロケーターにより、`ContentKeyAuthorizationPolicy`、`AssetDeliveryPolicy`、`ContentKey`、`AccessPolicy` の必要性がなくなっています。

## <a name="packaging-and-delivery-in-v3"></a>v3 でのパッケージ化と配信

1. [コンテンツ キー ポリシー](drm-content-key-policy-concept.md)を作成します。
1. [ストリーミング ロケーター](stream-streaming-locators-concept.md)を作成します。
1. [ストリーミング パス](create-streaming-locator-build-url.md)を取得します。 
    1. それを [DASH](encode-dynamic-packaging-concept.md#deliver-dash) または [HLS](encode-dynamic-packaging-concept.md#deliver-hls) プレーヤー用に構成します。

具体的な手順については、次の「概念、チュートリアル、ハウツー ガイドの発行」を参照してください。

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>概念、チュートリアル、ハウツー ガイドの発行

### <a name="concepts"></a>概念

- [Media Services v3 のダイナミック パッケージ](encode-dynamic-packaging-concept.md)
- [フィルター](filters-concept.md)
- [Dynamic Packager を使用してマニフェストをフィルター処理する](filters-dynamic-manifest-concept.md)
- [Azure Media Services のストリーミング エンドポイント (配信元)](stream-streaming-endpoint-concept.md)
- [CDN 統合を使用してコンテンツをストリーミングする](stream-scale-streaming-cdn-concept.md)
- [ストリーミング ロケーター](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>ハウツー ガイド

- [Media Services v3 でストリーミング エンドポイントを管理する](stream-manage-streaming-endpoints-how-to.md)
- [CLI の例: 資産の公開](cli-publish-asset.md)
- [ストリーミング ロケーターの作成と URL の構築](create-streaming-locator-build-url.md)
- [ジョブの結果をダウンロードする](job-download-results-how-to.md)
- [説明オーディオ トラックの通知](signal-descriptive-audio-howto.md)
- [Azure Media Player のフル セットアップ](../azure-media-player/azure-media-player-full-setup.md)
- [Azure Media Services で Video.js プレーヤーを使用する方法](player-how-to-video-js-player.md)
- [Azure Media Services で Shaka プレーヤーを使用する方法](player-shaka-player-how-to.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。
