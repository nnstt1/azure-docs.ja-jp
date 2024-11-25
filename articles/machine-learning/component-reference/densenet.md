---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの DenseNet コンポーネントを使用して、DenseNet アルゴリズムを使ってイメージ分類モデルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 919378cc51128a946d32698a06e2330c57eb1147
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565581"
---
# <a name="densenet"></a>DenseNet

この記事では、Azure Machine Learning デザイナーの **DenseNet** コンポーネントを使用し、DenseNet アルゴリズムを使ってイメージ分類モデルを作成する方法について説明します。  

この分類アルゴリズムは、教師あり学習手法であり、ラベル付きデータセットが必要となります。 

> [!NOTE]
> このコンポーネントは、スタジオの *[データのラベル付け]* から生成されたラベル付きデータセットはサポートしておらず、[[Convert to Image Directory]\(イメージ ディレクトリへの変換\)](convert-to-image-directory.md) コンポーネントから生成されたラベル付きイメージ ディレクトリのみサポートしています。 

モデルとラベル付きイメージ ディレクトリを [PyTorch モデルのトレーニング](train-pytorch-model.md)への入力として提供することにより、モデルをトレーニングすることができます。 その後は、トレーニングされたモデルで[画像モデルのスコア付け](score-image-model.md)を使用して、新しい入力例の値を予測することができます。

### <a name="more-about-densenet"></a>DenseNet の詳細

DenseNet の詳細については、「[Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993)」 (高密度に接続された畳み込みネットワーク) という研究論文を参照してください。

## <a name="how-to-configure-densenet"></a>DenseNet の構成方法

1.  デザイナーで、**DenseNet** コンポーネントをパイプラインに追加します。  

2.  **Model name (モデル名)** には、特定の DenseNet 構造の名前を指定します。これは、サポートされている DenseNet である 'densenet121'、'densenet161'、'densenet169'、'densenet201' から選択できます。

3.  **Pretrained (事前トレーニング済み)** には、ImageNet で事前トレーニングされているモデルを使用するかどうかを指定します。 選択した場合は、選択した事前トレーニング済みモデルに基づいてモデルを微調整できます。選択を解除した場合は、ゼロからトレーニングできます。

4.  **Memory efficient (メモリ効率)** には、チェックポイント処理を使用するかどうかを指定します。これは、メモリ効率が大幅に向上しますが、時間がかかります。 詳細については、「[Memory-Efficient Implementation of DenseNets](https://arxiv.org/pdf/1707.06990.pdf)」 (DenseNets の高メモリ効率の実装) という研究論文を参照してください。

5.  トレーニングと検証のイメージ データセット コンポーネントである **DenseNet** コンポーネントの出力を [Pytorch モデルのトレーニング](train-pytorch-model.md)に接続します。 

6. パイプラインを送信します。


## <a name="results"></a>結果

パイプラインの実行が完了した後、モデルをスコア付けに使用するには、[PyTorch モデルのトレーニング](train-pytorch-model.md)を[画像モデルのスコア付け](score-image-model.md)に接続し、新しい入力例の値を予測します。

## <a name="technical-notes"></a>テクニカル ノート  

###  <a name="component-parameters"></a>コンポーネントのパラメーター  

| 名前             | Range | Type    | Default     | 説明                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| モデル名       | Any   | モード    | densenet201 | 特定の DenseNet 構造体の名前     |
| Pretrained (事前トレーニング済み)       | Any   | Boolean | True        | ImageNet で事前トレーニング済みモデルを使用するかどうか |
| Memory efficient (メモリ効率) | Any   | Boolean | False       | メモリ効率が大幅に向上するが、時間がかかるチェックポイント処理を使用するかどうか |

###  <a name="output"></a>出力  

| 名前            | 種類                    | 説明                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未トレーニング モデル | UntrainedModelDirectory | Pytorch モデルのトレーニングに接続できる未トレーニングの DenseNet モデル。 |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。 
