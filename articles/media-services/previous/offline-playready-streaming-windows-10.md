---
title: PlayReady で保護されたコンテンツのオフライン ストリーミング用にアカウントを構成する - Azure
description: この記事では、オフラインの Windows 10 のために PlayReady ストリーミング用の Azure Media Services アカウントを構成する方法を示します。
services: media-services
keywords: DASH, DRM, Widevine オフライン モード, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e103e361b037e54dcc161cfcbd6081d6adb53ee
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712044"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Windows 10 用のオフライン PlayReady ストリーミング

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="使用している Media Services のバージョンを選択してください:"]
> * [Version 3](../latest/drm-offline-playready-streaming-for-windows-10.md)
> * [Version 2](offline-playready-streaming-windows-10.md)

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Azure Media Services は、DRM 保護を適用したオフラインでのダウンロード/再生をサポートしています。 この記事では、Windows 10/PlayReady クライアントに向けた Azure Media Services のオフライン サポートについて説明します。 iOS/FairPlay および Android/Widevineデバイスに向けたオフライン モードのサポートの詳細については、以下の記事を参照してください。

- [オフラインの iOS 用 FairPlay Streaming](media-services-protect-hls-with-offline-fairplay.md)
- [Android 用のオフラインの Widevine ストリーミング](offline-widevine-for-android.md)

## <a name="overview"></a>概要

このセクションでは、オフライン モードでの再生に関する背景情報をいくつか示します。特に、必要な理由を説明します。

* 一部の国/地域では、インターネットの使用や帯域幅がまだ制限されています。  ユーザーは先にダウンロードしておくことで、満足できる十分に高い解像度でコンテンツを視聴できます。 通常、この場合の問題は、ネットワークを使えるかどうかではなく、ネットワーク帯域幅の制限です。 OTT/OVP プロバイダーは、オフライン モードのサポートを求めています。
* Netflix の 2016 年第 3 四半期株主会議で公表されたように、コンテンツのダウンロードは、"よく求められる機能" であり、"検討の余地がある" と Netflix CEO の Reed Hastings 氏は述べています。
* コンテンツ プロバイダーによっては、国/地域の境を超えた DRM ライセンス配信を許可しないことがあります。 ユーザーが海外旅行する必要があり、そこでもコンテンツを見たい場合は、オフライン ダウンロードが必要です。
 
オフライン モードの実装においては、以下のような課題に直面します。

* 多くのプレーヤーやエンコーダー ツールで MP4 がサポートされていますが、MP4 コンテナーと DRM の間にはバインディングがありません。
* 長期的には、CENC を使用する CFF が主流となります。 ただし現在では、ツール/プレーヤーのサポート エコシステムがまだありません。 今すぐにでもソリューションが必要なのです。
 
そのアイデアは、次のようなものです。H264/AAC によるスムーズ ストリーミング ([PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)) ファイル形式には、PlayReady (AES-128 CTR) とのバインディングがあります。 個々のスムーズ ストリーミング .ismv ファイル (オーディオはビデオ内に多重化されていると想定) 自体は fMP4 であり、再生に使用できます。 スムーズ ストリーミング コンテンツが PlayReady 暗号化を受けると、各 .ismv ファイルは、PlayReady で保護された、断片化された MP4 になります。 好みのビットレートの .ismv ファイルを選択し、ダウンロードのためにその名前を .mp4 に変更できます。

プログレッシブ ダウンロード用の、PlayReady で保護された MP4 をホストするためのオプションが 2 つあります。

* プログレッシブ ダウンロードのために、この MP4 を同じコンテナー/メディア サービスアセットに配置し、Azure Media Services ストリーミング エンドポイントを利用できます。
* Azure Storage からの直接プログレッシブ ダウンロードのために SAS ロケーターを使用し、Azure Media Services をバイパスできます。
 
2 種類の PlayReady ライセンス配信を使用できます。

* Azure Media Services の PlayReady ライセンス配信サービス。
* 任意の場所でホストされる PlayReady ライセンス サーバー。

以下に、2 セットのテスト用アセットを示します。1 つ目は AMS で PlayReady ライセンス配信を使用しており、2 つ目は Azure VM でホストされた PlayReady ライセンス サーバーを使用しています。

アセット 1:

* プログレッシブ ダウンロードの URL: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

アセット 2:

* プログレッシブ ダウンロードの URL: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (オンプレミス): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

再生テストには、Windows 10 でユニバーサル Windows アプリケーションを使用しました。 [Windows 10 のユニバーサル サンプル](https://github.com/Microsoft/Windows-universal-samples)のセクションには、[アダプティブ ストリーミングのサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)という名前の基本的なプレーヤーのサンプルがあります。 必要なのは、ダウンロードしたビデオを選択し、アダプティブ ストリーミング ソースではなくソースとして使用するためのコードを追加することだけです。 変更は、ボタン クリックのイベント ハンドラー内です。

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

 ![PlayReady で保護された fMP4 のオフライン モード再生](./media/offline-playready/offline-playready1.jpg)

ビデオは PlayReady の保護下にあるため、スクリーン ショットにビデオを含めることはできません。

要約すると、Azure Media Services で以下のようにしてオフライン モードを実現しました。

* コンテンツのコード変換と PlayReady 暗号化は Azure Media Services またはその他のツールで行えます。
* プログレッシブ ダウンロードのために、コンテンツを Azure Media Services または Azure Storage にホストできます。
* PlayReady ライセンス配信は、Azure Media Services または他の場所から行えます。
* 準備されたスムーズ ストリーミング コンテンツは、引き続き DASH からオンライン ストリーミングに使用することも、PlayReady を使用して DRM としてスムーズ ストリーミングに使用することもできます。

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次のステップ

[DRM システムのハイブリッド設計](hybrid-design-drm-sybsystem.md)
