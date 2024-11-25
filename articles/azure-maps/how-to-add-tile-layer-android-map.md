---
title: Android マップへのタイル レイヤーの追加 | Microsoft Azure Maps
description: マップにタイル レイヤーを追加する方法について説明します。 Azure Maps Android SDK を使用して気象レーダーのオーバーレイをマップに追加する例を参照してください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: d35b1201d0f60c517f419186d01f493efa9fbeba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435242"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>マップにタイル レイヤーを追加する (Android SDK)

この記事では、Azure Maps Android SDK を使用してマップにタイル レイヤーをレンダリングする方法を示します。 タイル レイヤーを使用すると、Azure Maps ベース マップ タイルの上に画像を重ねることができます。 Azure Maps のタイル システムの詳細については、[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)のドキュメントを参照してください。

タイル レイヤーでは、サーバーからタイルが読み込まれます。 これらの画像は事前にレンダリングし、タイル レイヤーが認識する名前付け規則を使用して、サーバー上の他の画像と同様に保存できます。 または、画像をほぼリアルタイムに生成する動的サービスを使用して、これらの画像をレンダリングすることもできます。 Azure Maps の TileLayer クラスでは、3 種類のタイル サービスの名前付け規則がサポートされています。

* X、Y、ズーム表記 - ズーム レベルに基づいた、タイル グリッド内のタイルの列 (x)、行 (x) の位置です。
* quadkey 表記 - x、y、ズーム情報を組み合わせて、タイルの一意の識別子である 1 つの文字列値にします。
* 境界ボックス - 境界ボックス座標を使用して、[Web マッピング サービス (WMS)](https://www.opengeospatial.org/standards/wms) で一般的に使用されている `{west},{south},{east},{north}` の形式で画像を指定できます。

> [!TIP]
> TileLayer は、マップ上で大規模なデータ セットを視覚化する場合に適しています。 画像からタイル レイヤーを生成できるだけでなく、ベクター データもタイル レイヤーとしてレンダリングできます。 ベクター データをタイル レイヤーとしてレンダリングすることで、マップ コントロールで必要となるのはタイルの読み込みだけです。タイルは、それらが表すベクター データよりもはるかに小さいファイル サイズである可能性があります。 この手法は、何百万行ものデータをマップにレンダリングする必要がある場合によく使われています。

タイル レイヤーに渡されるタイル URL は、TileJSON リソースへの http/https URL、または次のパラメーターを使用するタイル URL テンプレートにする必要があります。

* `{x}` - タイルの X 位置。 `{y}` と `{z}` も必要です。
* `{y}` - タイルの Y 位置。 `{x}` と `{z}` も必要です。
* `{z}` - タイルのズーム レベル。 `{x}` と `{y}` も必要です。
* `{quadkey}` - Bing Maps タイル システムの名前付け規則に基づくタイルの quadkey 識別子。
* `{bbox-epsg-3857}` - EPSG 3857 空間参照系の `{west},{south},{east},{north}` 形式の境界ボックス文字列。
* `{subdomain}` - サブドメインの値のプレースホルダー (サブドメインの値が指定される場合)。
* `azmapsdomain.invalid` - タイル要求のドメインと認証を、マップで使用されているものと同じ値に合わせるためのプレースホルダー。 Azure Maps によってホストされているタイル サービスを呼び出すときにこれを使用します。

## <a name="prerequisites"></a>前提条件

この記事のプロセスを完了するには、[Azure Maps Android SDK](how-to-use-android-map-control-library.md) をインストールしてマップを読み込む必要があります。

## <a name="add-a-tile-layer-to-the-map"></a>マップにタイル レイヤーを追加する

このサンプルは、タイルのセットを指すタイル レイヤーを作成する方法を示しています。 このサンプルでは、"x、y、ズーム" タイル システムを使用します。 このタイル レイヤーのソースは、クラウド ソーシングされた海図が含まれている [OpenSeaMap プロジェクト](https://openseamap.org/index.php)です。 多くの場合、タイル レイヤーを表示するときは、マップ上の都市のラベルをはっきりと確認できることが求められます。 この動作は、マップ ラベル レイヤーの下にタイル レイヤーを挿入することによって実現できます。

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

次のスクリーンショットは、上記のコードによって、濃いグレー スケール スタイルが設定されたマップ上に航海情報のタイル レイヤーが表示されている状態を示しています。

![タイル レイヤーを表示している Android マップ](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC Web マッピング サービス (WMS) を追加する

Web マッピング サービス (WMTS) は、Open Geospatial Consortium (OGC) 標準で、マップ データのイメージを処理します。 Azure Maps で使用できるこの形式のオープン データセットは数多くあります。 この種類のサービスが `EPSG:3857` 座標参照系 (CRS) をサポートしている場合は、そのサービスをタイル レイヤーで使用できます。 WMS サービスを使用する場合は、幅と高さのパラメーターを、そのサービスでサポートされているものと同じ値に設定し、必ず `tileSize` オプションにも同じ値を設定してください。 書式設定された URL で、 `{bbox-epsg-3857}` プレースホルダーを使用してサービスの `BBOX` パラメーターを設定します。

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

次のスクリーンショットは、上記のコードで、[アメリカ地質調査所 (USGS)](https://mrdata.usgs.gov/) の地質データの Web マッピング サービスを、地図上でラベルの下にオーバーレイしています。

![WMS タイル レイヤーを表示している Android マップ](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC Web マッピング タイル サービス (WMTS) を追加する

Web マッピング タイル サービス (WMTS) は、Open Geospatial Consortium (OGC) 標準で、マップのタイルベースのオーバーレイを処理します。 Azure Maps で使用できるこの形式のオープン データセットは数多くあります。 この種類のサービスが `EPSG:3857` または `GoogleMapsCompatible` 座標参照系 (CRS) をサポートしている場合は、そのサービスをタイル レイヤーで使用できます。 WMTS サービスを使用する場合は、幅と高さのパラメーターを、そのサービスでサポートされているものと同じ値に設定し、必ず `tileSize` オプションにも同じ値を設定してください。 書式設定された URL で、以下のプレースホルダーを適宜置換します。

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

次のスクリーンショットは、上記のコードで、[アメリカ地質調査所 (USGS) 全国地図](https://viewer.nationalmap.gov/services/)の画像の Web マッピング タイル サービスを、地図上で道路とラベルの下にオーバーレイしています。

![WMTS タイル レイヤーを表示している Android マップ](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>次のステップ

地図上で画像をオーバーレイする各種方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [イメージ レイヤー](map-add-image-layer-android.md)
