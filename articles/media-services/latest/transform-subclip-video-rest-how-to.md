---
title: Media Services を使用してエンコードを実行する際にビデオをサブクリップする - REST
description: このトピックでは、Azure Media Services を REST で使用してエンコードを実行する際にビデオをサブクリップする方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 3f8712709f1160150275ee55d84522746bbe8a3a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "122652931"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Media Services を使用してエンコードを実行する際にビデオをサブクリップする - REST

[ジョブ](/rest/api/media/jobs)を使用してビデオをエンコードする際に、ビデオをトリミングまたはサブクリップすることができます。 この機能は、[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) プリセットまたは [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) プリセットを使用して構築された[変換](/rest/api/media/transforms)で動作します。 

このトピックの REST サンプルでは、エンコード ジョブを送信する際にビデオをトリミングするジョブを作成します。 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]


## <a name="prerequisites"></a>前提条件

このトピックで説明する手順を完了するには以下を行う必要があります。

- [Azure Media Services アカウントを作成](./account-create-how-to.md)します。
- [Azure Media Services REST API を呼び出すように Postman を構成します](setup-postman-rest-how-to.md)。
    
    トピック「[Azure AD トークンを取得する](setup-postman-rest-how-to.md#get-azure-ad-token)」の最後の手順を必ず実行してください。 
- 変換と出力アセットを作成します。 変換と出力アセットの作成方法は、「[リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)」チュートリアルでご覧いただけます。
- [エンコードの概念](encode-concept.md)に関するトピックを確認します。

## <a name="create-a-subclipping-job"></a>サブクリップ ジョブを作成する

1. ダウンロードした Postman のコレクションで、 **[Transforms and jobs]\(変換とジョブ\)**  ->  **[Create Job with Sub Clipping]\(サブクリップを含むジョブの作成\)** を選択します。
    
    **PUT** 要求は次のようになります。
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. "transformName" 環境変数の値を実際の変換名で更新します。 
1. **[Body]\(本文\)** タブを選択し、"myOutputAsset" を実際の出力アセットの名前で更新します。

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. **[送信]** をクリックします。

    作成および送信されたジョブに関する情報とジョブの状態とを含んだ **応答** が表示されます。 

## <a name="next-steps"></a>次のステップ

[カスタム変換を使用してエンコードする方法](transform-custom-preset-rest-how-to.md) 
