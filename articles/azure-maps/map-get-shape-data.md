---
title: マップ上の図形からデータを取得する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して、マップ上に描画された図形データを取得する方法を示します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: ae9683140eb1a3faf685bb3ffe2ee299313eabe3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435008"
---
# <a name="get-shape-data"></a>図形データを取得する

この記事では、マップに描画された図形のデータを取得する方法について説明します。 [描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--)内で **drawingManager.getSource()** 関数を使用します。 描画された図形の geojson データを抽出し、他の場所で使用することが必要になるさまざまなシナリオがあります。  


## <a name="get-data-from-drawn-shape"></a>描画された図形からデータを取得する

次の関数では、描画された図形のソース データが取得されて、画面に出力されます。 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

次に示すのは実行できる完全なコード サンプルであり、図形を描画して機能をテスできます。

<br/>

<iframe height="686" title="図形データを取得する" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get shape data</a>」Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次のステップ

描画ツール モジュールのその他の機能の使用方法を確認します。

> [!div class="nextstepaction"]
> [描画イベントに応答する](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [対話式操作の種類とキーボード ショートカット](drawing-tools-interactions-keyboard-shortcuts.md)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [描画ツール バー](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)