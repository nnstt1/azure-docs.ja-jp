---
title: 制限とクォータ - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: この記事では、Custom Vision Service に対する異なる種類のライセンス キー、および制限とクォータについて説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: pafarley
ms.openlocfilehash: b8dfe8711733efeb33561531c85925111799daba
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479307"
---
# <a name="limits-and-quotas"></a>制限とクォータ

Custom Vision Service にはキーの層が 2 つあります。 Azure portal を通じて F0 (Free) または S0 (Standard) サブスクリプションにサインアップすることができます。 価格とトランザクションについて詳しくは、対応する [Cognitive Services の価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)をご覧ください。

プロジェクトあたりトレーニング画像数とプロジェクトあたりタグ数は、S0 プロジェクトの場合、時間の経過と共に増えることが予想されます。

|要素|**F0 (free)**|**S0 (standard)**|
|-----|-----|-----|
|プロジェクト|2|100|
|プロジェクトあたりのトレーニング画像 |5,000|100,000|
|予測/月|10,000 |無制限|
|タグ/プロジェクト|50|500|
|イテレーション |20|20|
|タグあたりの最小ラベル付き画像、分類 (50+ 推奨) |5|5|
|タグあたりの最小ラベル付き画像、物体検出 (50+ 推奨)|15|15|
|予測画像の保存期間|30 日|30 日|
|[予測](https://go.microsoft.com/fwlink/?linkid=865445)操作、ストレージあり (秒あたりトランザクション)|2|10|
|[予測](https://go.microsoft.com/fwlink/?linkid=865445)操作、ストレージなし (秒あたりトランザクション)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (秒あたり API 呼び出し)|2|10|
|[その他の API 呼び出し](https://go.microsoft.com/fwlink/?linkid=865446) (秒あたりトランザクション)|10|10|
|許容される画像の種類|jpg、png、bmp、gif|jpg、png、bmp、gif|
|画像の高さ/幅 (ピクセル単位) の最小値|256 (注を参照)|256 (注を参照)|
|画像の高さ/幅 (ピクセル単位) の最大値|10,240|10,240|
|最大画像サイズ (トレーニング画像アップロード) |6 MB|6 MB|
|最大画像サイズ (予測)|4 MB|4 MB|
|イメージあたりの最大領域数 (物体検出)|300|300|
|イメージあたりの最大タグ数 (分類)|100|100|

> [!NOTE]
> 256 ピクセルより小さい画像は受け入れられますが、アップスケールされます。
> 画像の縦横比を 25 より大きくすることはできません。
