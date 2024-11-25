---
title: Azure Video Analyzer for Media (旧称 Video Indexer) で人物モデルをカスタマイズする - API
titleSuffix: Azure Video Analyzer for Media
description: Azure Video Analyzer for Media (旧称 Video Indexer) API を使用して人物モデルをカスタマイズする方法について説明します。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 01205b70b3f4195be290075727bece30d57c515e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121391"
---
# <a name="customize-a-person-model-with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API を使用して人物モデルをカスタマイズする

Azure Video Analyzer for Media (旧称 Video Indexer) では、ビデオ コンテンツの顔検出と著名人の認識がサポートされています。 著名人認識機能では、IMDB、Wikipedia、LinkedIn のトップ インフルエンサーなど、よく要求されるデータ ソースに基づいて、約 100 万人の顔を処理できます。 著名人認識機能によって認識されない顔も検出されますが、それらは名前が未設定のままになります。 Video Analyzer for Media に動画をアップロードし、結果を取得した後で、認識されなかった顔に戻って名前を付けることができます。 顔に名前のラベルを付けると、顔と名前がお使いのアカウントの人物モデルに追加されます。 その後、Video Analyzer for Media によって、その顔が今後の動画と過去の動画で認識されます。

Video Analyzer for Media API を使用して、ビデオで検出された顔を編集することができます。これについては、このトピックで説明します。 また、[Video Analyzer for Media Web サイトを使用した人物モデルのカスタマイズ](customize-person-model-with-api.md)に関するページで説明しているように、Video Analyzer for Media Web サイトを使用することもできます。

## <a name="managing-multiple-person-models"></a>複数の人物モデルの管理

Video Analyzer for Media では、アカウントごとに複数の人物モデルがサポートされています。 この機能は現在、Video Analyzer for Media API を通じてのみ利用可能です。

ご利用のアカウントがさまざまなユースケース シナリオに対応している場合は、アカウントごとに複数の人物モデルを作成することができます。 たとえば、コンテンツがスポーツに関連している場合、スポーツ (フットボール、バスケットボール、サッカーなど) ごとに別個の人物モデルを作成できます。

モデルが作成されたら、ビデオのアップロード/インデックスの作成または再作成の際に、特定の人物モデルのモデル ID を指定して使用できます。 ビデオの新しい顔のトレーニングを行うと、そのビデオが関連付けられた特定のカスタム モデルが更新されます。

各アカウントの人物モデルの数は 50 に制限されています。 複数の人物モデルをサポートする必要がない場合は、アップロード時やインデックスの作成または再作成時に、ビデオに人物モデル ID を割り当てないでください。 この場合、Video Analyzer for Media ではアカウントの既定のカスタム人物モデルが使われます。

## <a name="create-a-new-person-model"></a>新しい人物モデルを作成する

指定されたアカウントで新しい人物モデルを作成するには、[Create Person Model](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Person-Model) API を使用します。

応答では、以下の例の形式に従って、先ほど作成した人物モデルの名前と生成されたモデル ID が提供されます。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

その後、[インデックスを作成するためにビデオをアップロードする](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)ときや、[ビデオのインデックスを再作成する](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)ときに、**personModelId** パラメーターに **id** 値を使用します。

## <a name="delete-a-person-model"></a>人物モデルを削除する

指定されたアカウントからカスタムの人物モデルを削除するには、[Delete Person Model](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Person-Model) API を使用します。

人物モデルが正常に削除された後、削除されたモデルを使用していた現在のビデオのインデックスは再作成されるまで変わりません。 インデックスが再作成されると、そのモデルを使用してインデックスが作成された現在のビデオでは、削除されたモデルで名前が付けられた顔は Video Analyzer for Media によって認識されなくなります。しかし、顔は引き続き検出されます。 削除されたモデルを使用してインデックスが作成された現在のビデオでは、アカウントの既定の人物モデルが使用されるようになります。 アカウントの既定のモデルでも削除されたモデルの顔に名前が付けられている場合、それらの顔はビデオで引き続き認識されます。

人物モデルが正常に削除されたときに返されるコンテンツはありません。

## <a name="get-all-person-models"></a>すべての人物モデルを取得する

指定されたアカウントのすべての人物モデルを取得するには、[Get Person Models](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Person-Models) API を使用します。

応答では、アカウント内のすべての人物モデル (指定されたアカウント内の既定の人物モデルを含む) のリストが提供され、以下の例の形式に従ってそれぞれの名前と ID が示されます。

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

ビデオで使用するモデルは選択できます。それには、[インデックスを作成するためにビデオをアップロードする](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)とき、または[ビデオのインデックスを再作成する](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)ときに、`personModelId` パラメーターの人物モデルの `id` 値を使用します。

## <a name="update-a-face"></a>顔を更新する

このコマンドでは、ビデオの ID と顔の ID を使用し、名前でビデオの顔を更新できます。 この操作によって、アップロード時やインデックスの作成または再作成時にビデオが関連付けられた人物モデルが更新されます。 人物モデルが割り当てられていない場合は、アカウントの既定の人物モデルが更新されます。

システムでは次に、同じ人物モデルを共有している、現在のその他のビデオに出現する同じ顔が認識されます。 他の現在のビデオの顔の認識は、バッチ プロセスであるため、有効になるまで少し時間がかかることがあります。

Video Analyzer for Media で著名人として認識された顔は、新しい名前で更新できます。 指定した新しい名前は、組み込みの著名人認識より優先されます。

顔を更新するには、[Update Video Face](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Face) API を使用します。

名前は人物モデルで一意であるため、同じ人物モデルの 2 つの異なる顔に、同じ `name` パラメーター値を指定した場合、Video Analyzer for Media ではこれらの顔は同じ人物であると見なされて、それらの顔は、ビデオのインデックスを再作成するときに集約されます。

## <a name="next-steps"></a>次の手順

[Video Analyzer for Media の Web サイトを使用した人物モデルのカスタマイズ](customize-person-model-with-website.md)
