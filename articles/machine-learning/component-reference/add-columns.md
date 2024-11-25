---
title: '列の追加: コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: ドラッグ アンド ドロップの Azure Machine Learning デザイナーで列の追加コンポーネントを使用して、2 つのデータセットを連結する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9ee1a1440e239e95417528503a2d00db1ed4a8d1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565652"
---
# <a name="add-columns-component"></a>列の追加コンポーネント

この記事では Azure Machine Learning デザイナーのコンポーネントについて説明します。

このコンポーネントを使用して、2 つのデータセットを連結します。 入力として指定した 2 つのデータセットのすべての列を結合し、単一のデータセットを作成します。 3 つ以上のデータセットを連結する必要がある場合は、**列の追加** の複数のインスタンスを使用します。



## <a name="how-to-configure-add-columns"></a>列の追加の構成方法
1. **[列の追加]** コンポーネントをパイプラインに追加します。

2. 連結する 2 つのデータセットを接続します。 3 つ以上のデータセットを結合したい場合は、**列の追加** の複数の組み合わせを連結できます。

    - 異なる数の行を持つ 2 つの列を結合できます。 出力データセットには、より小さいソース列の各行の欠損値が埋め込まれます。

    - 個々の列を選択して追加することはできません。 **列の追加** を使用すると、各データセットのすべての列が連結されます。 このため、列のサブセットのみを追加する場合は、"データセット内の列の選択" を使用して、必要な列でデータセットを作成します。

3. パイプラインを送信します。

### <a name="results"></a>結果
パイプラインの実行後:

- 新しいデータセットの最初の行を表示するには、 **[列の追加]** コンポーネントを右クリックして、[可視化] を選択します。 または、コンポーネントを選択し、右側のパネルの **[出力]** タブに切り替え、 **[Port outputs]\(ポートの出力\)** 内のヒストグラム アイコンをクリックして結果を可視化します。

新しいデータセットの列数は、両方の入力データセットの列の合計数と等しくなります。

入力データセットに同じ名前の 2 つの列がある場合は、列の名前に数値のサフィックスが追加されます。 たとえば、TargetOutcome という名前の列のインスタンスが 2 つある場合、左側の列の名前が TargetOutcome_1 に変更され、右側の列の名前が TargetOutcome_2 に変更されます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。 