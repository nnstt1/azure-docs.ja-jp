---
title: 空間分析のゾーンとラインの配置
titleSuffix: Azure Cognitive Services
description: 空間分析でゾーンとラインを設定する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: 203a75b6f06b858fb398fb55ecdea6ea63f85efe
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751147"
---
# <a name="zone-and-line-placement-guide"></a>ゾーンとラインの配置ガイド

この記事では、空間内での人の動きを正確に分析するために、空間分析操作のゾーンとラインを定義する方法のガイドラインを説明します。 これはすべての操作に適用されます。 

ゾーンとラインは、JSON SPACEANALYSIS_CONFIG パラメーターを使用して定義されます。 詳細については、[空間分析の操作](spatial-analysis-operations.md)に関する記事をご覧ください。

## <a name="guidelines-for-drawing-zones"></a>ゾーンの描画に関するガイドライン

空間はそれぞれ異なることに注意してください。ニーズに応じて、位置またはサイズを更新する必要があります。

カメラ ビューの特定のセクションを表示する場合は、関心のある特定のフロア領域を対象とし、関心のない他の領域を含めずに、できるだけ大きなゾーンを作成します。 これにより、収集されるデータの精度が向上し、追跡する必要のない領域からの誤検知を防ぐことができます。多角形の角を配置するときは、追跡する領域外に配置しないよう注意してください。  

### <a name="example-of-a-well-shaped-zone"></a>適切に形成されたゾーンの例

ゾーンは、各縁端に沿って 3 人が十分に立てる大きさにし、関心領域に焦点を合わせる必要があります。 空間分析では、足がゾーン内にある人を特定するため、二次元の画像にゾーンを描画するときは、ゾーンを床に敷かれたカーペットであると想像してください。

![適切に形成されたゾーン](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>適切に形成されていないゾーンの例

次の例は、適切に形成されていないゾーンを示しています。 これらの例では、関心領域は *It's Game Time* というディスプレイの前の空間です。

**ゾーンがフロア上にありません。**

![適切に形成されていないゾーン](./media/spatial-analysis/zone-not-on-floor.png) 

**ゾーンが小さすぎます。**

![ゾーンが小さすぎる](./media/spatial-analysis/zone-too-small.png)

**ディスプレイの周囲の領域がゾーンに完全には取り込まれていません。**

![端部の周囲の領域がゾーンに完全には取り込まれていない](./media/spatial-analysis/zone-bad-capture.png)

**ゾーンがカメラ画像の端部に近すぎ、ディスプレイが正しく取り込まれていません。**

![ゾーンがカメラ画像の端部に近すぎ、ディスプレイが正しく取り込まれていない](./media/spatial-analysis/zone-edge.png)

**ゾーンが棚によって部分的に遮られているため、人とフロアが完全には見えていません。**

![ゾーンが部分的に遮られているため、人が完全には見えていない](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>適切に形成されたラインの例

ラインは、入口全体が十分に収まる長さにする必要があります。 空間分析では、足がラインを越えている人を特定するため、二次元の画像にラインを描画するときは、床に横たわっているように描画していると想像してください。 

可能であれば、ラインを延長して実際の入口の幅よりも長くします。 これを行うことで余分な交差 (次の画像のように、ラインが壁に接している状態) が生じない場合は、ラインを延長してください。

![適切に形成されたライン](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>適切に形成されていないラインの例

次の例は、適切に定義されていないラインを示しています。

**ラインがフロアの入口通路全体をカバーしていません。**

![ラインがフロアの入口通路全体をカバーしていない](./media/spatial-analysis/zone-line-bad-coverage.png)

**ラインが高すぎてドア全体をカバーしていません。**

![ラインが高すぎてドア全体をカバーしていない](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [空間分析の操作を構成する](./spatial-analysis-operations.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
