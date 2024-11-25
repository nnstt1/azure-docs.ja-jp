---
title: .NET で 2 つ以上のビデオ ファイルをつなぎ合わせる方法 | Microsoft Docs
description: この記事では、2 つ以上のビデオ ファイルをつなぎ合わせる方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: fcf3ef3c122639ee0d2c9fa2664fe1b165195a8f
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493719"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>.NET で 2 つ以上のビデオ ファイルをつなぎ合わせる方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>2 つ以上のビデオ ファイルをつなぎ合わせる

複数のビデオ ファイルをつなぎ合わせるプリセットを生成する方法の例を次に示します。 最も一般的なシナリオは、ヘッダーまたはトレーラーをメイン ビデオに追加する場合です。

> [!NOTE]
> まとめて編集されるビデオ ファイルは、プロパティ (ビデオの解像度、フレーム レート、オーディオ トラック数など) を共有している必要があります。 フレーム レートやオーディオ トラック数が異なるビデオを混在させないように注意してください。

## <a name="prerequisites"></a>前提条件

[Media Services .ＮＥＴ サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet/)を複製またはダウンロードします。 

## <a name="example"></a>例

[EncodingWithMESCustomStitchTwoAssets フォルダー](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/Encoding_StitchTwoAssets/Program.cs)で、ビデオ ファイルを合成する方法を示すコードを見つけます。
