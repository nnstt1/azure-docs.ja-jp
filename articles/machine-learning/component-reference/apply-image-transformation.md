---
title: イメージ変換の適用
titleSuffix: Azure Machine Learning
description: イメージ変換の適用コンポーネントを使用して、イメージの変換をイメージ ディレクトリに適用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 45929ecdcc4327a12e2b2867a0cb0c95ca78a3fd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566125"
---
# <a name="apply-image-transformation"></a>イメージ変換の適用 

この記事では、Azure Machine Learning デザイナーのイメージ変換の適用コンポーネントを使用して、以前に指定したイメージ変換に基づいて入力イメージ ディレクトリを変更する方法について説明します。  

変換を指定するには、[イメージ変換の初期化](init-image-transformation.md)コンポーネントに接続する必要があります。次に、イメージ変換の適用コンポーネントの入力イメージ ディレクトリにそのような変換を適用できます。

## <a name="how-to-use-apply-image-transformation"></a>イメージ変換の適用の使用方法  

1. **イメージ変換の適用** コンポーネントをパイプラインに追加します。 このコンポーネントは *[Computer Vision/Image Data Transformation]\(Computer Vision/イメージ データ変換\)* カテゴリにあります。 

2. **イメージ変換の初期化** の出力を **イメージ変換の適用** の左側の入力に接続します。

     > [!NOTE]
     > このコンポーネントには、[イメージ変換の初期化](init-image-transformation.md)モジュールによって生成されたイメージ変換のみが受け入れられます。 その他の種類の変換の場合は、それを [変換の適用](apply-transformation.md)に接続してください。そうしないと、'InvalidTransformationDirectoryError' がスローされます。


3. 変換するイメージ ディレクトリを接続します。

4. **[モード]** では、入力変換を使用する目的に対して指定します。'トレーニング用' または '推論用'。 

   **[For training]\(トレーニング用\)** を選択した場合、イメージ変換の初期化で指定したすべての変換が適用されます。

   **[For inference]\(推論用\)** を選択した場合、新しいサンプルをランダムに作成するなどの変換は、適用される前に除外されます。 これは、'ランダム左右反転' のように、新しいサンプルをランダムに作成する変換操作は、トレーニングでのデータの拡張に使用されるためです。それは、推論のサンプルを正確な予測と評価のために修正する必要があるため、推論で削除する必要があります。

   > [!NOTE]
   > **[For inference]\(推論用\)** モードで除外される変換は次のとおりです。Random resized crop (ランダム サイズ変更クロップ)、Random crop (ランダム クロップ)、Random horizontal flip (ランダム左右反転)、Random vertical flip (ランダム上下反転)、Random rotation (ランダム回転)、Random affine (ランダム アフィン)、Random grayscale (ランダム グレースケール)、Random perspective (ランダム パースペクティブ)、Random erasing (ランダム消去)。

5. イメージ変換を新しいイメージ ディレクトリに適用するには、パイプラインを送信します。  

### <a name="component-parameters"></a>コンポーネントのパラメーター

| 名前 | Range | Type | Default                   | 説明                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| モード | Any   | モード | (ユーザーに指定するように要求します) | 入力変換を使用する目的。 推論では 'ランダム' 変換操作を除外しますが、トレーニングではそれらを保持する必要があります |

### <a name="expected-inputs"></a>想定される入力  

| 名前                       | 種類                    | 説明                       |
| -------------------------- | ----------------------- | --------------------------------- |
| 入力イメージ変換 | TransformationDirectory | 入力イメージ変換        |
| 入力イメージ ディレクトリ      | ImageDirectory          | 変換されるイメージ ディレクトリ |

### <a name="outputs"></a>出力  

| 名前                   | 種類           | 説明            |
| ---------------------- | -------------- | ---------------------- |
| 出力イメージ ディレクトリ | ImageDirectory | 出力イメージ ディレクトリ |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)をご覧ください。 
