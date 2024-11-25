---
title: Azure Video Analyzer for Media (旧 Video Indexer) のシーン、ショット、キーフレーム
description: このトピックでは、Azure Video Analyzer for Media (旧 Video Indexer) のシーン、ショット、キーフレームの概要について説明します。
ms.topic: how-to
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b6825b5a50793589a07c16ef274d1e4231a2d4e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655945"
---
# <a name="scenes-shots-and-keyframes"></a>シーン、ショット、キーフレーム

Azure Video Analyzer for Media (旧 Video Indexer) では、構造とセマンティックのプロパティに基づくビデオのテンポラル単位へのセグメント化がサポートされています。 この機能を使用すると、さまざまな粒度に基づいてビデオ コンテンツを簡単に参照、管理、編集できます。 たとえば、このトピックの説明はシーン、ショット、キーフレームに基づいています。   

![シーン、ショット、キーフレーム](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>シーン検出  
 
Azure Video Analyzer for Media では、視覚的な手掛かりに基づいて、ビデオ内でシーンが変化するタイミングが判定されます。シーンは単一のイベントを表し、意味的に関連する一連の連続したショットで構成されます。 シーンのサムネイルは、その基になるショットの最初のキーフレームです。 Azure Video Analyzer for Media では、連続するショット間の色の一貫性に基づいてビデオがシーンごとにセグメント化され、各シーンの開始と終了の時間が取得されます。 シーンの検出は、ビデオのセマンティックな側面を定量化する必要があるので、困難な作業と見なされます。

> [!NOTE]
> 少なくとも 3 つのシーンが含まれるビデオに適用されます。

## <a name="shot-detection"></a>ショット検出

Azure Video Analyzer for Media では、連続するフレームの配色での突然の遷移と段階的な遷移の両方を追跡することにより、視覚的な手掛かりに基づいてビデオでのショットの変化が特定されます。 ショットのメタデータには、開始と終了の時間、およびそのショットに含まれるキーフレームのリストが含まれます。 ショットは、同時に同じカメラで撮影された連続するフレームです。

## <a name="keyframe-detection"></a>キーフレームの検出

Video Analyzer for Media により、各ショットを最適に表すフレームが選択されます。 キーフレームは、審美的プロパティ (たとえば、コントラストや安定性) に基づいてビデオ全体から選択された代表的なフレームです。 Azure Video Analyzer for Media により、ショットのメタデータの一部としてキーフレーム ID のリストが取得されます。顧客は、これに基づいてキーフレームを高解像度画像として抽出できます。  

### <a name="extracting-keyframes"></a>キーフレームの抽出

ビデオの高解像度のキーフレームを抽出するには、まずビデオをアップロードしてインデックスを作成する必要があります。

![キーフレーム](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-analyzer-for-media-website"></a>Video Analyzer for Media の Web サイトを使用

Video Analyzer for Media の Web サイトを使用してキーフレームを抽出するには、ビデオをアップロードしてインデックスを作成します。 インデックス作成ジョブが完了したら、 **[ダウンロード]** ボタンをクリックし、 **[成果物 (ZIP)]** を選択します。 これにより、[成果物] フォルダーがコンピューターにダウンロードされます。 

![[ダウンロード] ドロップダウンを示すスクリーンショット。[成果物] が選択されています。](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
フォルダーを解凍して開きます。 *_KeyframeThumbnail* フォルダーに、ビデオから抽出されたすべてのキーフレームが表示されます。 

#### <a name="with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API を使用

Video Indexer API を使用してキーフレームを取得するには [Upload Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) 呼び出しを使用してビデオをアップロードし、インデックスを作成します。 インデックス作成ジョブが完了したら、[Get Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index) を呼び出します。 これにより、JSON ファイル内のコンテンツから抽出された Video Indexer すべての分析情報が得られます。  

各ショットのメタデータの一部として、キーフレーム ID の一覧が表示されます。 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

次に、[Get Thumbnails](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) 呼び出しで、これらのキーフレーム ID をそれぞれ実行する必要があります。 これにより、各キーフレームのイメージがコンピューターにダウンロードされます。 

## <a name="editorial-shot-type-detection"></a>編集ショット タイプの検出

キーフレームは、出力 JSON のショットと関連付けられます。 

Insights JSON 内の個々のショットに関連付けられたショット タイプは、その編集タイプを表します。 これらのショット タイプ特性は、ビデオを編集してクリップやトレーラーを作成したり、芸術的な目的で特定のスタイルのキーフレームを検索したりするときに便利な場合があります。 各ショットの最初のキーフレームの分析に基づいて、さまざまなタイプが決定されます。 ショットは最初のキーフレームに表示される顔のスケール、サイズ、位置によって識別されます。 

ショットのサイズとスケールは、カメラとフレームに表示される顔との距離に基づいて決定されます。 これらのプロパティを使用して、Video Analyzer for Media は次のショット タイプを検出します。

* ワイド: 人物の全身が表示されます。
* ミディアム: 人物の上半身と顔が表示されます。
* クローズアップ: 人物の顔が主に表示されます。
* エクストリーム クローズアップ: 人物の顔が画面いっぱいに表示されます。 

ショット タイプは、フレームの中心を基準としたときの対象の人物の位置によって決定することもできます。 このプロパティは、Video Analyzer for Media で次のショット タイプを定義します。

* 左フェース: 人物がフレームの左側に表示されます。
* 中央フェース: 人物がフレームの中央領域に表示されます。
* 右フェース: 人物がフレームの右側に表示されます。
* 屋外: 人物が屋外の背景で表示されます。
* 室内: 人物が屋内の背景で表示されます。

追加の特性:

* 2 ショット: 2 人の人物の中間サイズの顔を示します。
* 複数の顔: 人物が 3 人以上。


## <a name="next-steps"></a>次のステップ

[API によって生成された Video Analyzer for Media の出力を調べる](video-indexer-output-json-v2.md#scenes)
