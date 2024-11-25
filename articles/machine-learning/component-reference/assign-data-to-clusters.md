---
title: 'クラスターへのデータの割り当て: コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Assign Data to Cluster (クラスターへのデータの割り当て) コンポーネントを使用して、クラスタリング モデルをスコア付けする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: fa639154b1f22364fa0923a4162b4d4aaf19c9d3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565618"
---
# <a name="component-assign-data-to-clusters"></a>コンポーネント: クラスターへのデータの割り当て

この記事では、Azure Machine Learning デザイナーの "*クラスターへのデータの割り当て*" コンポーネントを使用する方法について説明します。 このコンポーネントでは、*K-Means クラスタリング* アルゴリズムでトレーニングされたクラスタリング モデルを介して予測を生成します。

Assign Data to Clusters (データのクラスターへの割り当て) コンポーネントは、新しい各データ ポイントの推定の割り当てを含むデータセットを返します。 

## <a name="how-to-use-assign-data-to-clusters"></a>クラスターへのデータの割り当てを使用する方法
  
1. Azure Machine Learning デザイナーで、以前にトレーニングしたクラスタリング モデルを見つけます。 以下のいずれかの方法を使用して、クラスタリング モデルを作成およびトレーニングできます。  
  
    - [K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) コンポーネントを使用して K-Means クラスタリング アルゴリズムを構成し、データセットと Train Clustering Model (クラスタリング モデルのトレーニング) コンポーネント (この記事) を使用してモデルをトレーニングします。  
  
    - また、トレーニング済みの既存のクラスタリング モデルを、ワークスペースの **[Saved Models]\(保存済みのモデル\)** グループから追加することもできます。

2. トレーニング済みのモデルを **Assign Data to Clusters** (クラスターへのデータの割り当て) の左側の入力ポートにアタッチします。  

3. 新しいデータセットを入力としてアタッチします。 

   このデータセットでは、ラベルは省略可能です。 一般に、クラスタリングは教師なしの学習方法です。 事前にカテゴリを知ることは想定されていません。 ただし、入力列はクラスタリング モデルのトレーニングで使用された列と同じである必要があり、同じでないとエラーが発生します。

    > [!TIP]
    > クラスター予測からデザイナーに書き込まれる列数を減らすには、[[Select columns in the dataset]\(データセット内の列の選択\)](select-columns-in-dataset.md) を使用して列の一部を選択します。 
    
4. 結果 (クラスターの割り当て) を表示する列を含め、入力データセット全体を結果に含める場合は、 **[Check for append or uncheck for result only]\(追加をチェックまたは結果のみをチェック解除\)** チェック ボックスをオンのままにします。
  
    このチェック ボックスをオフにすると、結果のみが返されます。 このオプションは、Web サービスの一部として予測を作成するときに役立ちます。
  
5.  パイプラインを送信します。  
  
### <a name="results"></a>結果

+  データセット内の値を表示するには、コンポーネントを右クリックして **[可視化]** を選択します。 または、コンポーネントを選択し、右側のパネルの **[出力]** タブに切り替え、 **[Port outputs]\(ポートの出力\)** 内のヒストグラム アイコンをクリックして結果を可視化します。
