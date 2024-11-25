---
title: Azure Video Analyzer for Media (旧称 Video Indexer) を大規模に使用する場合の考慮事項 - Azure
description: このトピックでは、Azure Video Analyzer for Media (旧称 Video Indexer) を大規模に使用する場合の考慮事項について説明します。
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f27dc577fdd90842e7108b2e57e1f0cbfe841080
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665684"
---
# <a name="things-to-consider-when-using-video-analyzer-for-media-at-scale"></a>Video Analyzer for Media を大規模に使用する場合の考慮事項

ビデオのインデックス作成に Azure Video Analyzer for Media (旧称 Video Indexer) を使用しているときにビデオのアーカイブが増加している場合は、スケーリングを検討してください。 

この記事では、次のような質問に回答します。

* 考慮すべき技術的な制約はありますか。
* スマートで効率的な方法はありますか。
* プロセスに余分なコストをかけないようにすることはできますか。

この記事では、Video Analyzer for Media を大規模に使用する方法に関する 6 つのベスト プラクティスについて説明します。

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>ビデオのアップロード時に、バイト配列に対する URL の使用を検討する

Video Analyzer for Media では、URL からビデオをアップロードするか、ファイルをバイト配列として送信して直接アップロードするかを選択できます。後者にはいくつかの制約があります。 詳細については、「[アップロードに関する考慮事項と制限事項](upload-index-videos.md#uploading-considerations-and-limitations)」を参照してください。

まず、ファイル サイズの制限があります。 URL を使用した場合のアップロード サイズの制限が 30 GB であるのに比べて、バイト配列ファイルのサイズは 2 GB に制限されています。

次に、パフォーマンスとスケーリング能力に影響を与える可能性があるいくつかの問題について説明します。

* マルチパートを使用してファイルを送信すると、ネットワークへの依存性が高まる 
* サービスの信頼性 
* 接続性 
* アップロード速度 
* World Wide Web のどこかでパケットが失われる

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Video Analyzer for Media を大規模に使用する場合の最初の考慮事項":::

URL を使用してビデオをアップロードする場合は、メディア ファイルの場所へのパスを指定するだけで、Video Indexer によって残りの処理が自動的に行われます ([upload video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API の `videoUrl` フィールドを参照)。

> [!TIP]
> upload video API の省略可能なパラメーター `videoUrl` を使用します。

URL を使用してビデオをアップロードする方法の例については、[この例](upload-index-videos.md#code-sample)をご覧ください。 または、[AzCopy](../../storage/common/storage-use-azcopy-v10.md) を使用して、迅速かつ確実にコンテンツをストレージ アカウントに取得し、そこから [SAS URL](../../storage/common/storage-sas-overview.md) を使用してコンテンツを Video Analyzer for Media に送信できます。 Video Analyzer for Media では、"*読み取り専用*" の SAS URL を使用することをお勧めします。

## <a name="automatic-scaling-of-media-reserved-units"></a>メディア占有ユニットの自動スケーリング 

2021 年 8 月 1 日以降、Azure Video Analyzer for Media (旧称 Video Indexer) では、[Azure Media Services](../../media-services/latest/media-services-overview.md) (AMS) による[占有ユニット](../../media-services/latest/concept-media-reserved-units.md) (MRU) の自動スケーリングが有効になりました。その結果、Azure Video Analyzer for Media を使用してそれらを管理する必要がなくなります。 これにより、自動スケーリングされるビジネス ニーズに応じて、多くの場合では価格を下げることができるなど、価格の最適化が可能になります。 

## <a name="respect-throttling"></a>調整を考慮する

Video Analyzer for Media は、大規模なインデックス作成を処理するように構築されています。これを最大限に活用するには、システムの機能を認識し、それに応じて統合を設計する必要があります。 ビデオのバッチに対するアップロード要求を送信しても、一部のムービーがアップロードされず、HTTP 429 応答コード (要求が多すぎる) を受信することになります。 これは、[サポートされている 1 分あたりのムービーの制限](upload-index-videos.md#uploading-considerations-and-limitations)を超える要求を送信したことが原因で発生する可能性があります。 Video Analyzer for Media によって HTTP 応答に `retry-after` ヘッダーが追加され、このヘッダーにより、次回の再試行を行うタイミングが指定されます。 次の要求を試す前に、必ずそのことを考慮してください。

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="調整を考慮して統合を適切に設計する":::

## <a name="use-callback-url"></a>コールバック URL を使用する

アップロード要求を送信した瞬間から要求の状態を常にポーリングするのではなく、[コールバック URL](upload-index-videos.md#callbackurl) を追加し、Video Analyzer for Media で更新が行われるまで待機することをお勧めします。 アップロード要求の状態が変更されるとすぐに、指定した URL への POST 通知が取得されます。

コールバック URL は、[upload video API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) のパラメーターの 1 つとして追加できます。 [GitHub リポジトリ](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)でコード サンプルを確認してください。 

コールバック URL の場合は、Azure Functions を使用することもできます。これは、HTTP によってトリガーされ、次のフローを実装できるサーバーレスのイベントドリブン プラットフォームです。

### <a name="callback-url-definition"></a>コールバック URL の定義

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>適切なインデックス作成パラメーターを使用する

Video Analyzer for Media の大規模な使用に関する意思決定を行うときは、ニーズに合った適切なパラメーターを使用して、その方法を最大限に活用する方法を確認してください。 ユース ケースについて考えてみましょう。さまざまなパラメーターを定義することで、コストを節約し、ビデオのインデックス作成プロセスを高速化することができます。

ビデオをアップロードしてインデックスを作成する前に、この短い[ドキュメント](upload-index-videos.md)を参照してください。[indexingPreset](upload-index-videos.md#indexingpreset) と [streamingPreset](upload-index-videos.md#streamingpreset) を確認して、オプションの内容を理解してください。

たとえば、ビデオを視聴する予定がない場合は、プリセットをストリーミングに設定しません。オーディオ分析情報のみが必要な場合は、ビデオ分析情報に対してインデックスを作成しません。

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>最適な解像度のインデックス (最高解像度ではない)

ビデオのインデックス作成に必要なビデオ品質を知りたい場合があります。 

多くの場合、HD (720P) ビデオと 4K ビデオの間で、インデックス作成のパフォーマンスにはほとんど違いがありません。 最終的には、同程度に信頼できるほぼ同等の分析情報を得ることができます。 アップロードするムービーの品質が高くなると、ファイル サイズが大きくなり、ビデオのアップロードに必要なコンピューティング能力と時間が増大します。

たとえば、顔検出機能の場合、解像度が高くなると、小さいがコンテキスト上重要な顔が多数存在するシナリオで役立ちます。 ただし、これには、実行時に 2 次的な増加が伴い、誤検知のリスクが高まります。

そのため、ユース ケースに適した結果が得られることを確認し、最初にローカルでテストすることをお勧めします。 720P と 4K で同じビデオをアップロードし、取得された分析情報を比較します。

## <a name="next-steps"></a>次のステップ

[API によって生成された Azure Video Analyzer for Media の出力を調べる](video-indexer-output-json-v2.md)
