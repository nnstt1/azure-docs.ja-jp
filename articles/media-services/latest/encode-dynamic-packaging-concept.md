---
title: Azure Media Services v3 のダイナミック パッケージ
description: この記事では、Azure Media Services でのダイナミック パッケージの概要について説明します。
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: acc993f5690fbc250659830b52099f8d3d966421
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322167"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Media Services v3 のダイナミック パッケージ

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services では、HLS および MPEG DASH ストリーミング プロトコル形式でコンテンツを配信する組み込みの配信元サーバーとパッケージ機能が提供されます。 AMS では、[ストリーミング エンドポイント](stream-streaming-endpoint-concept.md)は、これらの一般的な形式を使用したアダプティブ ビットレート ストリーミングをサポートするクライアント プレーヤーに、フォーマットされた HLS および DASH コンテンツを送信する "配信元" サーバーとして機能します。 また、ストリーミング エンドポイントでは、すべての主要なデバイス (iOS デバイスや Android デバイスなど) に到達するために、Just-In-Time やコンテンツ保護を有効または無効にしたダイナミック パッケージなどの多くの機能もサポートされています。 

現在市場にあるほとんどのブラウザーとモバイル デバイスでは、HLS または DASH のストリーミング プロトコルがサポートおよび認識されています。 たとえば、iOS ではストリームを HTTP ライブ ストリーミング (HLS) 形式で配信する必要があり、Android デバイスでは、HLS に加えて特定のモデルで (または Android デバイス用のアプリケーション レベル プレーヤーの [Exoplayer](https://exoplayer.dev/) を使用して) MPEG DASH がサポートされます。

Media Services では、[ストリーミング エンドポイント](stream-streaming-endpoint-concept.md) (配信元) は、ダイナミック (Just-In-Time) パッケージおよび配信元サービスを表します。これは、ライブのオンデマンド コンテンツをクライアント プレーヤー アプリに直接配信できます。 次のセクションで説明する一般的なストリーミング メディア プロトコルのいずれかを使用します。 "*ダイナミック パッケージ*" は、すべてのストリーミング エンドポイントに標準で付属する機能です。

Just-In-Time パッケージには、次の利点があります。

* すべてのファイルを標準の MP4 ファイル形式で格納できます
* 静的にパッケージ化された HLS 形式と DASH 形式の複数のコピーを BLOB ストレージに格納する必要がないため、格納されるビデオ コンテンツの量が減り、ストレージの全体的なコストが削減されます
* カタログ内の静的コンテンツを再パッケージ化することなく、時間の経過と共に進化する新しいプロトコルの更新と仕様の変更をすぐに利用できます
* ストレージ内の同じ MP4 ファイルを使用して、暗号化と DRM の有無に関係なくコンテンツを配信できます
* 単純な資産レベル フィルターまたはグローバル フィルターを使用してマニフェストを動的フィルター処理または変更して、特定のトラック、解像度、言語を削除したり、コンテンツの再エンコードや再レンダリングを行わずに、同じ MP4 ファイルから短いハイライト クリップを提供したりすることができます。 

## <a name="to-prepare-your-source-files-for-delivery"></a>ソース ファイルをデリバリー用に準備するには

ダイナミック パッケージを活用するには、中間 (ソース) ファイルを一連の単一または複数のビットレート MP4 (ISO Base Media 14496-12) ファイルに[エンコード](encode-concept.md)する必要があります。 エンコードされた MP4 を含む[資産](assets-concept.md)と、Media Services のダイナミック パッケージで必要とされるストリーミング構成ファイルが必要です。 この一連の MP4 ファイルから、ダイナミック パッケージを使用することで、以下に説明するストリーミング メディア プロトコルを介してビデオを配信することができます。

通常は、Azure Media Services の標準エンコーダーを使用して、コンテンツに対応したエンコード プリセットまたはアダプティブ ビットレート プリセットを使用することによって、このコンテンツを生成します。  どちらの場合も、ストリーミングおよび動的パッケージに対応した一連の MP4 ファイルが生成されます。  または、外部サービス、オンプレミス、または独自の VM またはサーバーレス関数アプリでエンコードすることもできます。 外部でエンコードされたコンテンツは、アダプティブ ビットレート ストリーミング形式のエンコード要件を満たしている場合、ストリーミング用の資産にアップロードできます。 ストリーミング用に事前にエンコードされた MP4 をアップロードするプロジェクトの例については、.NET SDK サンプルの「[Stream Existing Mp4 ファイル](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4)」を参照してください。


Azure Media Services のダイナミック パッケージでは、MP4 コンテナー形式のビデオおよびオーディオ ファイルのみがサポートされています。 Dolby などの代替コーデックを使用するときは、オーディオ ファイルを MP4 コンテナーにエンコードする必要もあります。  

> [!TIP]
> MP4 およびストリーミング構成ファイルを取得する方法の 1 つは、[Media Services を使用してお使いの中間ファイルをエンコードする](#encode-to-adaptive-bitrate-mp4s)ことです。  [コンテンツ対応エンコード プリセット](encode-content-aware-concept.md)を使用して、コンテンツに最適なアダプティブ ストリーミング レイヤーと設定を生成することをお勧めします。 [VideoEncoding フォルダーの .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding) を使用したエンコードのコード サンプルを参照してください 

エンコードされた資産内のビデオをクライアントが再生できるようにするには、[ストリーミング ロケーター](stream-streaming-locators-concept.md)を使用して資産を公開し、適切な HLS および DASH ストリーミング URL をビルドする必要があります。 URL の format クエリで使用されるプロトコルを変更することで、サービスでは適切なストリーミング マニフェスト (HLS、MPEG DASH) が配信されます。

その結果、保存と課金の対象となるのは、単一のストレージ形式 (MP4) のファイルのみです。Media Services では、クライアント プレーヤーからの要求に応じて適切な HLS または DASH マニフェストを生成して提供します。

Media Services 動的暗号化を使用してコンテンツを保護する場合は、「[ストリーミング プロトコルと暗号化の種類](drm-content-protection-concept.md#streaming-protocols-and-encryption-types)」を参照してください。

## <a name="deliver-hls"></a>HLS を配信する
### <a name="hls-dynamic-packaging"></a>HLS ダイナミック パッケージ

ストリーミング クライアントは、次の HLS 形式を指定できます。 最新のプレーヤーおよび iOS デバイスとの互換性のために CMAF 形式を使用することをお勧めします。  レガシ デバイスの場合は、format クエリ文字列を変更するだけで、v4 と v3 の形式も使用できます。

|Protocol| [書式設定文字列]| 例|
|---|---|---|
|HLS CMAF (推奨)| format=m3u8-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|HLS V4 |  format=m3u8-aapl | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 | format=m3u8-aapl-v3 | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|


> [!NOTE]
> Apple の以前のガイドラインでは、低帯域幅ネットワークのフォールバックではオーディオのみのストリームを提供することを推奨していました。  現在、Media Services エンコーダーではオーディオのみのトラックが自動的に生成されます。Apple のガイドラインでは、特に Apple TV の配信では、オーディオのみのトラックを "*含めない*" ように定められています。  プレーヤーが既定でオーディオのみのトラックを含めないようにするには、HLS でオーディオのみの再生を削除する "audio-only=false" タグを URL に使用するか、または単に HLS-V3 を使用することをお勧めします。 たとえば、「 `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)` 」のように入力します。


### <a name="hls-packing-ratio-for-vod"></a>VOD のための HLS 圧縮率

以前の HLS 形式のために VOD コンテンツの圧縮率を制御するには、.ism ファイルの **fragmentsPerHLSSegment** メタデータ タグを設定して、古い v3 および v4 の HLS 形式マニフェストから配信される TS セグメントの既定の 3:1 の圧縮率を制御することができます。 この設定変更では、圧縮率を調整するために、ストレージ内の .ism ファイルを直接変更する必要があります。

**fragmentsPerHLSSegment** を 1 に設定した .ism サーバー マニフェストの例を示します。 
``` xml
   <?xml version="1.0" encoding="utf-8" standalone="yes"?>
   <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
         <meta name="formats" content="mp4" />
         <meta name="fragmentsPerHLSSegment" content="1"/>
      </head>
      <body>
         <switch>
         ...
         </switch>
      </body>
   </smil>
```

## <a name="deliver-dash"></a>DASH を配信する
### <a name="dash-dynamic-packaging"></a>DASH ダイナミック パッケージ

ストリーミング クライアントは、次の MPEG-DASH 形式を指定できます。

|Protocol| [書式設定文字列]| 例|
|---|---|---|
|MPEG-DASH CMAF (推奨)| format=mpd-time-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|MPEG-DASH CSF (レガシ)| format=mpd-time-csf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |

## <a name="deliver-smooth-streaming-manifests"></a>スムーズ ストリーミング マニフェストを配信する
### <a name="smooth-streaming-dynamic-packaging"></a>スムーズ ストリーミング ダイナミック パッケージ

ストリーミング クライアントは、次のスムーズ ストリーミング形式を指定できます。

|Protocol|メモと例| 
|---|---|
|スムーズ ストリーミング| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2.0 (レガシ マニフェスト)|既定では、Smooth Streaming のマニフェスト形式には、繰り返しタグ (r タグ) が含まれています。 ただし、一部のプレーヤーは、`r-tag` をサポートしていません。 これらのプレーヤーを使用するクライアントは、r タグを無効にする形式を使用できます。<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> スムーズ ストリーミングでは、オーディオとビデオの両方がストリームに存在している必要があります。

## <a name="on-demand-streaming-workflow"></a>オンデマンド ストリーミングのワークフロー

次の手順は、Azure Media Services の標準エンコーダーとダイナミック パッケージを併用した一般的な Media Services でのストリーミング ワークフローを示しています。

1. MP4、QuickTime/MOV、またはその他のサポートされているファイル形式の[入力ファイルをアップロード](job-input-from-http-how-to.md)します。 このファイルは、中間ファイルやソース ファイルとも呼ばれます。 サポートされている形式の一覧については、[Standard Encoder でサポートされている形式](encode-media-encoder-standard-formats-reference.md)に関するページを参照してください。
1. 中間ファイルを H.264/AAC MP4 アダプティブ ビットレート セットに[エンコード](#encode-to-adaptive-bitrate-mp4s)します。

    エンコードされたファイルが既にあり、ファイルをコピーしてストリーミングするだけであれば、[CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) および [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio) API を使用します。 結果として、ストリーミング マニフェスト (.ism ファイル) を含む新しい MP4 ファイルが作成されます。

    さらに、アダプティブ ビットレート ストリーミングの適切な設定 (通常は 2 秒の GOP、キー フレームの距離 2 秒 (最小および最大)、固定ビットレート (CBR) モード エンコード) を使用してエンコードされている限り、事前にエンコードされたファイルで .ism と .ismc ファイルを生成できます。  

    既存のエンコード済み MP4 ファイルからストリーミングするために .ism (サーバー マニフェスト) と .ismc (クライアント マニフェスト) を生成する方法の詳細については、[既存の MP4 をストリーミングする .NET SDK サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4)を参照してください。

1. アダプティブ ビットレート MP4 セットが含まれる出力資産を発行します。 [ストリーミング ロケーター](stream-streaming-locators-concept.md)を作成して発行します。
1. さまざまな形式 (HLS、MPEG-DASH および Smooth Streaming) をターゲットとする URL を構築します。 これらのさまざまな形式の正しいマニフェストおよび要求の処理は、*ストリーミング エンドポイント* が行います。
    
次の図は、ダイナミック パッケージのワークフローを使用したオンデマンド ストリーミングを示しています。

![ダイナミック パッケージを使用したオンデマンド ストリーミングのワークフローの図](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

上の画像のダウンロード パスは、"*ストリーミング エンドポイント*" (配信元) を通じて直接 MP4 ファイルをダウンロードできることを示すためにのみ存在します (ダウンロードできることを示す [ストリーミング ポリシー](stream-streaming-policy-concept.md)は、ストリーミング ロケーターで指定します)。<br/>ダイナミック パッケージャーによってファイルが変更されることはありません。 "*ストリーミング エンドポイント*" (配信元) 機能をバイパスする必要がある場合は、Azure Blob Storage API を使用して、プログレッシブ ダウンロード用に MP4 に直接アクセスすることができます。 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>アダプティブ ビットレート MP4 へのエンコード

[Media Services を使用してビデオをエンコードする方法](encode-concept.md)の例については、以下の記事を参照してください。

* [コンテンツに対応したエンコードを使用する](encode-content-aware-concept.md)。
* [組み込みのプリセットを使用して HTTPS の URL をエンコードする](job-input-from-http-how-to.md)。
* [組み込みのプリセットを使用してローカル ファイルをエンコードする](job-input-from-local-file-how-to.md)。
* [自分の特定のシナリオまたはデバイス要件に対応するカスタム プリセットを構築する](transform-custom-presets-how-to.md)。
* [.NET を使用して標準エンコーダーでエンコードするためのコード サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding)

サポートされる標準エンコーダー入力の[形式およびコーデック](encode-media-encoder-standard-formats-reference.md)のリストを参照してください。

## <a name="live-streaming-workflow"></a>ライブ ストリーミング ワークフロー

ライブ イベントは、"*パススルー*" (オンプレミスのライブ エンコーダーによって複数のビットレート ストリームが送信される) または "*ライブ エンコード*" (オンプレミスのライブ エンコーダーによってシングル ビットレート ストリームが送信される) のいずれかに設定できます。 

"*ダイナミック パッケージ*" を使用したライブ ストリーミングの一般的なワークフローは次のとおりです。

1. [ライブ イベント](live-event-outputs-concept.md)を作成します。
1. 取り込み URL を取得し、その URL を使用してコントリビューション フィードを送信するようにオンプレミス エンコーダーを構成します。
1. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が受信されていることを確認します。
1. 新しい資産を作成します。
1. ライブ出力を作成し、作成した資産の名前を使用します。<br />ライブ出力により、ストリームが資産にアーカイブされます。
1. 組み込みのストリーミング ポリシー タイプでストリーミング ロケーターを作成します。<br />コンテンツを暗号化する場合は、「[コンテンツ保護の概要](drm-content-protection-concept.md)」を確認してください。
1. 使用する URL を取得するためのパスをストリーミング ロケーターに列挙します。
1. ストリーミングするストリーミング エンドポイントのホスト名を取得します。
1. さまざまな形式 (HLS、MPEG-DASH および Smooth Streaming) をターゲットとする URL を構築します。 さまざまな形式の正しいマニフェストおよび要求の処理は、"*ストリーミング エンドポイント*" が行います。

次の図は、"*ダイナミック パッケージ*" を使用したライブ ストリーミングのワークフローを示しています。

![ダイナミック パッケージを使用したパススルー エンコードのワークフローの図](./media/live-streaming/pass-through.svg)

Media Services v3 のライブ ストリームの詳細については、[ライブ ストリーミングの概要](stream-live-streaming-concept.md)に関するページを参照してください。

## <a name="video-codecs-supported-by-dynamic-packaging"></a>ダイナミック パッケージでサポートされているビデオ コーデック

ダイナミック パッケージでは、MP4 コンテナー ファイル形式になっていて、[H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC または AVC1) または [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC、hev1、または hvc1) でエンコードされたビデオが格納されている、ビデオ ファイルがサポートされています。

> [!NOTE]
> "*ダイナミック パッケージ*" では、最大 4K の解像度および最大 60 フレーム/秒のフレーム レートをテスト済みです。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>ダイナミック パッケージによってサポートされているオーディオ コーデック

ダイナミック パッケージでは、次のいずれかのコーデックでエンコードされたオーディオ ストリームが含まれ、MP4 ファイル コンテナー形式で格納されているオーディオ ファイルもサポートされます。

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、HE-AAC v1、または HE-AAC v2)。 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 または E-AC3)。  ダイナミック パッケージを使用するには、エンコードされたオーディオが MP4 コンテナー形式で格納されている必要があります。
* Dolby Atmos

   Dolby Atmos コンテンツのストリーミングは、CSF (Common Streaming Format) または CMAF (Common Media Application Format) フラグメント化 MP4 による MPEG-DASH プロトコルなどの標準でサポートされ、CMAF による HLS (HTTP ライブ ストリーミング) 経由でサポートされています。
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH-CSF、DASH-CMAF、HLS-M2TS、HLS-CMAF パッケージ形式でサポートされている DTS コード:  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution と DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (コアなし) (dtsl)

ダイナミック パッケージでは、複数のコーデックと言語が使用された複数のオーディオ トラックがある資産のストリーム配信のために、DASH または HLS (バージョン 4 以降) を使用して複数のオーディオ トラックがサポートされます。

上記のすべてのオーディオ コーデックで、ダイナミック パッケージを使用するには、エンコードされたオーディオが MP4 コンテナー形式で格納されている必要があります。 サービスでは、Blob Storage での生の基本ストリーム ファイル形式はサポートされていません (たとえば、.dts や .ac3 はサポートされていません)。 

オーディオ パッケージでは、拡張子が .mp4 または .mp4a のファイルのみがサポートされています。 

### <a name="limitations"></a>制限事項

#### <a name="ios-limitation-on-aac-51-audio"></a>AAC 5.1 オーディオに関する iOS の制限

Apple iOS デバイスは、5.1 AAC オーディオ コーデックをサポートしません。 マルチチャンネル オーディオは、ドルビー デジタルまたはドルビー デジタル プラス コーデックを使用してエンコードする必要があります。

詳細については、「[Apple デバイスの HLS 作成仕様](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices)」を参照してください。

> [!NOTE]
> Media Services では、Dolby Digital、Dolby Digital Plus、Dolby Digital Plus with Dolby Atmos マルチチャンネル オーディオ形式のエンコードがサポートされません。

#### <a name="dolby-digital-audio"></a>ドルビー デジタル オーディオ

Media Services ダイナミック パッケージでは現在、[ドルビー デジタル](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) オーディオを含んだファイルがサポートされません (ドルビーによってレガシ コーデックと見なされているため)。

## <a name="manifests"></a>マニフェスト

Media Services の "*ダイナミック パッケージ*" では、HLS、MPEG-DASH、スムーズ ストリーミングのストリーミング クライアント マニフェストが、URL 内の **format** クエリに基づいて動的に生成されます。  

マニフェスト ファイルには、トラックの種類 (オーディオ、ビデオ、またはテキスト)、トラック名、開始時刻と終了時刻、ビットレート (品質)、トラック言語、プレゼンテーション ウィンドウ (固定時間のスライディング ウィンドウ)、ビデオ コーデック (FourCC) などの、ストリーミング メタデータが含まれます。 また、次に再生可能なビデオ フラグメントとその場所の情報を通知して、次のフラグメントを取得するようにプレイヤーに指示します。 フラグメント (またはセグメント) とは、ビデオ コンテンツの実際の "チャンク" です。

### <a name="examples"></a>例

#### <a name="hls"></a>HLS

以下に示すのは、HLS マニフェスト ファイル (HLS マスター プレイリストとも呼ばれます) の例です。 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

以下に示すのは、MPEG-DASH マニフェスト ファイル (MPEG-DASH Media Presentation Description (MPD) とも呼ばれます) の例です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>スムーズ ストリーミング

以下に示すのは、Smooth Streaming マニフェスト ファイルの例です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>マニフェスト内のトラックの名前付け

.ism ファイルにオーディオ トラック名が指定されている場合、Media Services によって、特定のオーディオ トラックのテクスチャ情報を指定するための `Label` 要素が `AdaptationSet` 内に追加されます。出力 DASH マニフェストの例:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

プレーヤーは、`Label` 要素を使用して UI に表示できます。

### <a name="signaling-audio-description-tracks"></a>オーディオ説明トラックのシグナル通知

ナレーション トラックをビデオに追加することで、目が不自由なクライアントがナレーションを聞いてビデオ録画を追うことができます。 オーディオ トラックには、マニフェストでオーディオ説明として注釈を付ける必要があります。 そのためには、"accessibility" パラメーターと "role" パラメーターを .ism ファイルに追加します。 オーディオ トラックをオーディオ説明としてシグナル通知するためには、これらのパラメーターを正しく設定する必要があります。 たとえば、特定のオーディオ トラックの .ism ファイルに `<param name="accessibility" value="description" />` と `<param name="role" value="alternate"` を追加します。 

詳細については、[説明を含んだオーディオ トラックをシグナル通知する方法](signal-descriptive-audio-howto.md)の例を参照してください。

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming マニフェスト

Smooth Streaming ストリームを再生している場合、そのオーディオ トラックに対応する `Accessibility` 属性と `Role` 属性の値がマニフェストに含まれています。たとえば、オーディオ説明であることを示すために、`Role="alternate" Accessibility="description"` が `StreamIndex` 要素に追加されます。

#### <a name="dash-manifest"></a>DASH マニフェスト

DASH マニフェストの場合は、オーディオ説明をシグナル通知するために次の 2 つの要素が追加されます。

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS プレイリスト

HLS v7 以降 `(format=m3u8-cmaf)` では、オーディオ説明トラックをシグナル通知する際に、そのプレイリストに `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` が含まれます。



#### <a name="example"></a>例

詳細については、[オーディオ説明トラックをシグナル通知する方法](signal-descriptive-audio-howto.md)に関するページを参照してください。

## <a name="dynamic-manifest-filtering"></a>動的マニフェスト フィルター

プレーヤーに送信されるトラック数、形式、ビットレート、およびプレゼンテーション時間枠を制御するために、Media Services ダイナミック パッケージャーで動的フィルターを使用できます。 詳細については、[ダイナミック パッケージャーでの事前フィルター処理マニフェスト](filters-dynamic-manifest-concept.md)に関するページを参照してください。

## <a name="dynamic-encryption-for-drm"></a>DRM の動的暗号化

"*動的暗号化*" を使用すると、AES-128 または次の 3 つの主要なデジタル著作権管理 (DRM) システムのいずれかを用いて、ライブまたはオンデマンドのコンテンツを動的に暗号化できます。コンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM ライセンスを配信するためのサービスも提供しています。 詳細については、[動的暗号化](drm-content-protection-concept.md)に関するページを参照してください。

> [!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="more-information"></a>詳細情報

[Azure Media Services コミュニティ](media-services-community.md)を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="need-help"></a>お困りの際は、

[[新しいサポート要求]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) に移動することで、サポート チケットを開くことができます。

## <a name="next-steps"></a>次のステップ

[ビデオのアップロード、エンコード、およびストリーミング](stream-files-tutorial-with-api.md)
