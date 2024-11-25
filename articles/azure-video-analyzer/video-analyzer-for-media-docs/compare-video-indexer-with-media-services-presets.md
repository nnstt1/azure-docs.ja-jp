---
title: Azure Video Analyzer for Media (旧称 Video Indexer) と Azure Media Services v3 プリセットの比較
description: この記事では、Azure Video Analyzer for Media (旧称 Video Indexer) 機能と Azure Media Services v3 プリセットを比較します。
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 66e62013c4719bf0b20808a240bdfa44483ab5b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607617"
---
# <a name="compare-azure-media-services-v3-presets-and-video-analyzer-for-media"></a>Azure Media Services v3 プリセットと Video Analyzer for Media を比較する 

この記事では、**Video Analyzer for Media (旧称 Video Indexer) API** と **Media Services v3 API** の機能を比較します。 

現在、[Video Analyzer for Media API](https://api-portal.videoindexer.ai/) と [Media Services v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) によって提供される機能には重複があります。 次の表に、違う点と似ている点を理解するための最新ガイドラインを示します。 

## <a name="compare"></a>比較

|機能|Video Analyzer for Media API |ビデオ アナライザーとオーディオ アナライザーのプリセット<br/>(Media Services v3 API)|
|---|---|---|
|メディア分析情報|[拡張](video-indexer-output-json-v2.md) |[基礎](../../media-services/latest/analyze-video-audio-files-concept.md)|
|エクスペリエンス|サポートされている機能の完全な一覧を参照: <br/> [概要](video-indexer-overview.md)|ビデオの分析情報のみを返す|
|課金|[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|コンプライアンス|最新のコンプライアンスの更新については、[Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) にアクセスし、"Video Analyzer for Media" を検索して関心がある認証に準拠しているかどうかを確認してください。|最新のコンプライアンスの更新については、[Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) を入手し、"Media Services" を検索して、関心がある認証に準拠しているかどうかを確認します。|
|無料試用版|米国東部|使用不可|
|利用可能なリージョン|[リージョン別の Cognitive Services の可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)に関するページを参照してください。|[リージョン別の Media Services の可用性](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)に関するページを参照してください。|

## <a name="next-steps"></a>次のステップ

[Video Analyzer for Media の概要](video-indexer-overview.md)

[Media Services v3 の概要](../../media-services/latest/media-services-overview.md)
