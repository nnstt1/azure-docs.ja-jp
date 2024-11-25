---
title: Azure Media Services ダイナミック パッケージの概要 | Microsoft Docs
description: この記事では Microsoft Azure Media Services ダイナミック パッケージの概要について説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 397af7a327657cda11c25ade08e93a8f13fe3fdb
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706264"
---
# <a name="dynamic-packaging"></a>動的パッケージ

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="使用している Media Services のバージョンを選択してください:"]
> * [Version 3](../latest/encode-dynamic-packaging-concept.md)
> * [Version 2](media-services-dynamic-packaging-overview.md)

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Microsoft Azure Media Services を使用して、多数のメディア ソース ファイルの形式、メディア ストリーミング形式、およびコンテンツ保護形式をさまざまなクライアント テクノロジ (iOS、XBOX、Silverlight、Windows 8 など) に配信できます。 これらのクライアントは異なるプロトコルを認識します。たとえば、iOS は HTTP Live Streaming (HLS) V4 形式が必要で、Silverlight および Xbox はスムーズ ストリーミングが必要です。 MPEG DASH、HLS またはスムーズ ストリーミングを認識するクライアントに提供する、一連のアダプティブ ビットレート (マルチビット レート) MP4 (ISO Base Media 14496-12) ファイルまたは一連のアダプティブ ビットレート スムーズ ストリーミング ファイルがある場合、Media Services の動的パッケージを活用する必要があります。

ダイナミック パッケージを使用すれば、一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルを含むアセットを作成するだけで済みます。 そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

次の図は、従来のエンコードおよび静的パッケージングのワークフローを示しています。

![静的なエンコード](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

次の図は、動的パッケージのワークフローを示しています。

![動的なエンコード](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>一般的なシナリオ

1. 入力ファイル (中間ファイル) をアセットにアップロードします。 たとえば、H.264、MP4、または WMV などです (サポートされている形式の一覧については、「 [Media Encoder Standard の形式とコーデック](media-services-media-encoder-standard-formats.md)」を参照してください)。
2. Mezzanine ファイルを H.264 MP4 アダプティブ ビットレート セットにエンコードします。
3. オンデマンド ロケーターを作成することによって、アダプティブ ビットレート MP4 セットを含むアセットを発行します。
4. コンテンツにアクセスしてストリーミングするストリーミング URL を構築します。

## <a name="preparing-assets-for-dynamic-streaming"></a>動的ストリーミング用のアセットの準備

動的ストリーミング用にアセットを準備するには、次のオプションがあります。

- [マスター ファイルをアップロードします](media-services-dotnet-upload-files.md)。
- [Media Encoder Standard エンコーダーを使用して、H.264 MP4 アダプティブ ビットレート セットを生成します](media-services-dotnet-encode-with-media-encoder-standard.md)。
- [コンテンツをストリーミングします](media-services-deliver-content-overview.md)。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>ダイナミック パッケージによってサポートされているオーディオ コーデック

ダイナミック パッケージでは、[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、HE-AAC v1、HE-AAC v2)、[Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3、E-AC3)、Dolby Atmos、または [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express、DTS LBR、DTS HD、DTS HD Lossless) でエンコードされたオーディオを含む MP4 ファイルがサポートされています。 Dolby Atmos コンテンツのストリーミングは、CSF (Common Streaming Format) または CMAF (Common Media Application Format) フラグメント化 MP4 による MPEG-DASH プロトコルなどの標準でサポートされ、CMAF による HLS (HTTP ライブ ストリーミング) 経由でサポートされています。

> [!NOTE]
> ダイナミック パッケージでは、[Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) オーディオ (古いコーデック) を含むファイルはサポートされていません。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
