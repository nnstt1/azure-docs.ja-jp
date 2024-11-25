---
title: 'チュートリアル: Microsoft Azure Maps を使用してストア ロケーター Web アプリケーションを作成する'
description: Microsoft Azure Maps を使用してストア ロケーター Web アプリケーションを作成する方法に関するチュートリアル。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/07/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0f8ea510000e506e4b1fd156a00b1eb89ddbb76e
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081227"
---
# <a name="tutorial-use-azure-maps-to-create-a-store-locator"></a>チュートリアル: Azure Maps を使用してストア ロケーターを作成する

このチュートリアルでは、Azure Maps を使用してシンプルなストア ロケーターを作成する手順を紹介します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure マップ コントロール API を使用して新しい Web ページを作成する。
> * ファイルからカスタム データを読み込んで地図上に表示する。
> * Azure Maps Search サービスを使用して住所を検索したりクエリを入力したりする。
> * ユーザーの場所をブラウザーから取得して地図上に表示する。
> * 複数のレイヤーを組み合わせて独自の記号を地図上で作成する。  
> * データ ポイントをクラスター化する。  
> * 地図にズーム コントロールを追加する。

<a id="Intro"></a>

## <a name="prerequisites"></a>前提条件

1. [Gen 1 (S1) または Gen 2 の価格レベルで Azure Maps アカウントを作成](quick-demo-map-app.md#create-an-azure-maps-account)します。
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

このチュートリアルでは [Visual Studio Code](https://code.visualstudio.com/) アプリケーションを使用していますが、別のコード開発環境を選択することもできます。

## <a name="sample-code"></a>サンプル コード

このチュートリアルでは、Contoso Coffee という架空の企業向けにストア ロケーターを作成します。 また、このチュートリアルには、他のオプション機能を使用してストア ロケーターを拡張する方法について学習するのに役立ついくつかのヒントも含まれています。

[ライブ ストア ロケーターの サンプルはこちら](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator)から表示できます。

このチュートリアルにより簡単に従って取り組むには、次のリソースをダウンロードする必要があります。

* [単純なストア ロケーター サンプルの完全なソース コード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)
* [ストア ロケーター データセットにインポートする店舗所在地データ](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)
* [マップ画像](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/images)

## <a name="store-locator-features"></a>ストア ロケーター機能

このセクションでは、Contoso Coffee ストア ロケーター アプリケーションでサポートされる機能の一覧を示します。

### <a name="user-interface-features"></a>ユーザー インターフェイスの機能

* ヘッダーのストア ロゴ
* マップでは、手のひらツールとズームがサポートされます
* ユーザーの現在の場所を検索するための [My Location]\(現在位置\) ボタン。
* デバイスの画面の幅に応じてページ レイアウトが調整されます。
* 検索ボックスと検索ボタン

### <a name="functionality-features"></a>機能性機能

* ユーザーが **Enter** キーを押すと、検索ボックスに追加された `keypress` イベントによって検索がトリガーされます。
* マップを動かすと、マップの中心から個々の所在地までの距離が計算されます。 結果リストが更新され、最も近い所在地がマップの一番上に表示されます。  
* ユーザーが結果リストでいずれかの結果を選択すると、選択した場所がマップの中央に移動し、その場所についての情報がポップアップ ウィンドウに表示されます。  
* ユーザーが特定の場所を選択すると、マップによってポップアップ ウィンドウがトリガーされます。
* ユーザーが地図を縮小すると、一連の所在地がクラスターとしてグループ化されます。 各クラスターは、内側に番号が付いた円で表されます。 クラスターは、ユーザーがズーム レベルを変更すると、それに従ってグループを形成したり分離したりします。
* クラスターを選択すると、マップ上で 2 レベル拡大表示され、クラスターの位置が中心に来ます。

## <a name="store-locator-design"></a>ストア ロケーターの設計

次の図は、ストア ロケーターの一般的なレイアウトのワイヤーフレームを示したものです。 ライブ ワイヤーフレームは、[こちらから](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator)表示できます。

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-wireframe.png" alt-text="Contoso Coffee ストア ロケーター アプリケーションのワイヤーフレーム。":::

このストア ロケーターには、その実用性を最大化するために、ユーザーの画面幅が 700 ピクセルよりも狭いときに調整されるレスポンシブ レイアウトを採用しています。 レスポンシブ レイアウトにより、モバイル デバイスなどの小さな画面でも使いやすいストア ロケーターとなっています。 小さな画面レイアウトのワイヤーフレームを次に示します。  

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-wireframe-mobile.png" alt-text="モバイル デバイスにおける Contoso Coffee ストア ロケーター アプリケーションのワイヤーフレーム。":::

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>店舗所在地のデータセットを作成する

このセクションでは、マップに表示するストアのデータセットを作成する方法について説明します。 Contoso Coffee ロケーターのデータセットは、Excel ブック内に作成されます。 このデータセットには、米国、カナダ、イギリス、フランス、ドイツ、イタリア、オランダ、デンマーク、スペインの 9 つの国または地域に点在する Contoso Coffee コーヒー ショップ 10,213 店舗の所在地が含まれています。 実際のデータのスクリーンショットを次に示します。

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-data-spreadsheet.png" alt-text="Excel ブック形式のストア ロケーター データのスクリーンショット。":::

完全なデータセットを表示するには、[Excel ブックをここからダウンロード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)します。

このデータのスクリーンショットを見ると、次のことが確認できます。

* 所在地情報は、**AddressLine**、**City**、**Municipality** (郡)、**AdminDivision** (州)、**PostCode** (郵便番号)、**Country** の各列を使用して格納されています。  
* **Latitude** 列と **Longitude** 列には、Contoso Coffee の各所在地に対応する座標が格納されています。 座標情報がない場合は、Azure Maps の Search サービスを使用して所在地の座標を特定できます。
* その他いくつかの列に、コーヒー ショップに関連したメタデータが格納されています。電話番号、ブール型列、店舗の開店時間と閉店時間 (24 時間形式) です。 ブール型列は、Wi-Fi と車椅子の利用の可否に使用されます。 実際の場所データとの関係がより強いメタデータを格納する列を独自に作成してもかまいません。

> [!NOTE]
> Azure Maps は、球面メルカトル図法 "EPSG:3857" でデータをレンダリングしますが、WGS84 測地系を使用する "EPSG:4326" のデータを読み取ります。

## <a name="load-the-store-location-dataset"></a>店舗所在地のデータセットを読み込む

 Contoso Coffee の店舗所在地データセットは小さいので、Excel ワークシートをタブ区切りのテキスト ファイルに変換します。 このファイルは、アプリケーションの読み込み時にブラウザーでダウンロードできます。

 >[!TIP]
>データセットがクライアントのダウンロードには大きすぎる、または頻繁に更新される場合は、データセットをデータベースに格納することもできます。 データがデータベースに読み込まれた後、データのクエリを受け入れる Web サービスを設定し、その結果をユーザーのブラウザーに送信できます。

### <a name="convert-data-to-tab-delimited-text-file"></a>データをタブ区切りテキスト ファイルに変換する

Contoso Coffee の店舗所在地データを Excel ブックからフラット テキスト ファイルに変換するには、次の手順を実行します。

1. [Excel ブックをダウンロード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)します。

2. ブックをハード ドライブに保存します。

3. Excel アプリを起動します。

4. ダウンロードしたブックを開きます。

5. **[名前を付けて保存]** を選択します。

6. **[ファイルの種類]** ボックスの一覧から **[テキスト (タブ区切り)(*.txt)]** を選択します。 

7. このファイルに *ContosoCoffee* という名前を付けます。

:::image type="content" source="./media/tutorial-create-store-locator/data-delimited-text.png" alt-text="[ファイルの種類] ダイアログ ボックスのスクリーンショット。":::

テキスト ファイルをメモ帳で開いた場合、次のテキストのようになります。

:::image type="content" source="./media/tutorial-create-store-locator/data-delimited-file.png" alt-text="タブ区切りデータセットが表示されたメモ帳ファイルのスクリーンショット。":::

## <a name="set-up-the-project"></a>プロジェクトのセットアップ

1. Visual Studio Code アプリを開きます。

2. **[ファイル]** を選択して、 **[ワークスペースを開く]** を選択します。

3. 新しいフォルダーを作成し、"ContosoCoffee" という名前を付けます。

4. エクスプローラーで  **[CONTOSOCOFFEE]** を選択します。

5. アプリケーションのレイアウト、スタイル、ロジックを定義する次の 3 つのファイルを作成します。

    * *index.html*
    * *index.css*
    * *index.js*

6. *data* という名前のフォルダーを作成します。

7. *ContosoCoffee.txt* を *data* フォルダーに追加します。

8. *images* という名前の別のフォルダーを作成します。

9. まだダウンロードしていない場合は、[これらの 10 個の画像をダウンロード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/images)します。

10. ダウンロードした画像を *images* フォルダーに追加します。

    ワークスペース フォルダーは次のスクリーンショットのようになります。

    :::image type="content" source="./media/tutorial-create-store-locator/store-locator-workspace.png" alt-text="Simple Store Locator ワークスペース フォルダーのスクリーンショット。":::

## <a name="create-the-html"></a>HTML ファイルを作成する

HTML ファイルを作成するには、次の手順を実行します。

1. *index.html* の `head` に次の `meta` タグを追加します。

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

2. Azure Maps Web コントロールの JavaScript ファイルと CSS ファイルへの参照を追加します。

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

3. Azure Maps Services モジュールへの参照を追加します。 このモジュールは、Azure Maps の REST サービスをラップして JavaScript で使いやすいようにしている JavaScript ライブラリです。 検索機能を強化するうえで、このモジュールは役立ちます。

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

4. *index.js* と *index.css* への参照を追加します。

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

5. ドキュメントの本文に、`header` タグを追加します。 `header` タグ内にロゴと会社名を追加します。

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

6. `main` タグを追加し、テキスト ボックスと検索ボタンを備えた検索パネルを作成します。 さらに、地図、リスト パネル、[My Location]\(現在位置\) GPS ボタンに対応する `div` 参照を追加します。

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

完了すると、*index.html* は、[この例の index.html ファイル](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html)のようになります。

## <a name="define-the-css-styles"></a>CSS スタイルを定義する

次の手順で、CSS スタイルを定義します。 アプリケーション コンポーネントのレイアウトとアプリケーションの外観は、CSS スタイルによって定義されます。

1. *index.css* を開きます。

2. 次の css コードを追加します。

    >[!NOTE]
    > `@media` スタイルは、画面幅が 700 ピクセルよりも狭いときに使用される代替スタイルのオプションを定義します。  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    } 

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is fewer than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

アプリケーションを実行します。 ヘッダー、検索ボックス、検索ボタンが表示されます。 ただし、マップはまだ読み込まれていないので表示されません。 検索を実行しようとしても何も起こりません。 次のセクションで説明する JavaScript ロジックを設定する必要があります。 ストア ロケーターのすべての機能には、このロジックでアクセスします。

## <a name="add-javascript-code"></a>JavaScript コードを追加する

Contoso Coffee の店舗所在地アプリの JavaScript コードは、次のプロセスを有効にします。

1. ページが読み込みプロセスを完了するまで待機するための `ready` という[イベント リスナー](/javascript/api/azure-maps-control/atlas.map#events)を追加します。 ページの読み込みが完了すると、イベント ハンドラーは、マップの読み込みを監視し、[検索] ボタンと **[My Location]\(現在位置\)** ボタンに機能を提供するために、より多くのイベント リスナーを作成します。

2. ユーザーが [検索] ボタンを選択するか、検索ボックスに場所を入力して Enter キーを押すと、ユーザーのクエリに対するあいまい検索が開始されます。 コードは、検索結果を特定の国または地域に限定するために、それらの国または地域の ISO 2 値の配列を `countrySet` オプションに渡します。 検索の国/地域を限定することで、返される結果の精度向上につながります。
  
3. 検索が完了すると、最初の場所の結果がマップ カメラの中心点として使用されます。 ユーザーが [My Location]\(現在位置\) ボタンを選択すると、コードはブラウザーに組み込まれた *HTML5 Geolocation API* を使用して、ユーザーの位置を取得します。 コードは位置を取得した後、ユーザーの位置を中心にマップを中央に配置します。  

JavaScript を追加するには、次の手順を実行します。

1. *index.js* を開きます。

2. 設定を更新しやすいようグローバル オプションを追加します。 マップ、ポップアップ ウィンドウ、データ ソース、アイコン レイヤー、および HTML マーカーの変数を定義します。 検索領域の中心を示すように HTML マーカーを設定します。 また、Azure Maps 検索サービス クライアントのインスタンスを定義します。

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

3. 次の初期化コードを追加します。 `<Your Azure Maps Key>` を実際のプライマリ サブスクリプション キーに必ず置き換えてください。

   > [!Tip]
   > ポップアップ ウィンドウを使用するときは、単一の `Popup` インスタンスを作成し、その内容と位置を更新することによって再利用することをお勧めします。 コードに追加する `Popup` インスタンスごとに、ページに複数の DOM 要素が追加されます。 ページ上の DOM 要素が増えるほど、ブラウザーで追跡する必要のあるものが増えます。 項目が多すぎると、ブラウザーが低速化する可能性があります。

    ```JavaScript

    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

4. 地図の `ready` イベント リスナーに、検索エリアの中心を表示する HTML マーカーとズーム コントロールを追加します。

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

5. 地図の `ready` イベント リスナーで、データ ソースを追加します。 そのうえで、データセットの読み込みと解析のための呼び出しを行います。 データ ソースでは、クラスタリングを有効にしてください。 データ ソースでのクラスタリングにより、重なり合うポイントがまとめて 1 つのクラスターにグループ化されます。 ユーザーが拡大操作を実行すると、クラスターは個々のポイントに分かれます。 この動作により、ユーザー エクスペリエンスとパフォーマンスが向上します。

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

6. マップの `ready` イベント リスナーにデータセットを読み込んだ後、データをレンダリングするための一連のレイヤーを定義します。 バブル レイヤーでは、クラスター化されたデータ ポイントがレンダリングされます。 シンボル レイヤーは、バブル レイヤー上の各クラスターに含まれるポイントの数をレンダリングします。 2 つ目のシンボル レイヤーでは、地図上の個々の場所のカスタム アイコンをレンダリングします。

   ユーザーが地図上のクラスターまたはアイコンにマウス ポインターを重ねたときにマウス カーソルを変化させるために、バブル レイヤーとアイコン レイヤーに `mouseover` イベントと `mouseout` イベントを追加します。 クラスターのバブル レイヤーには、`click` イベントを追加します。 いずれかのクラスターをユーザーが選択すると、この `click` イベントによって地図が 2 レベル拡大され、クラスターが中心に来るように地図が配置されます。 アイコン レイヤーには `click` イベントを追加します。 ユーザーが個々の所在地アイコンを選択すると、コーヒー ショップの詳細を表示するポップアップ ウィンドウがこの `click` イベントによって表示されます。 地図には、その移動が完了するタイミングを監視するためのイベントを追加します。 このイベントが発生したら、リスト パネル内の項目を更新します。  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

       //Create a layer to render a coffee cup symbol above each bubble for an individual location.
       iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
           iconOptions: {
               //Pass in the ID of the custom icon that was loaded into the map resources.
               image: 'myCustomIcon',

               //Optionally, scale the size of the icon.
               font: ['SegoeUi-Bold'],

               //Anchor the center of the icon image to the coordinate.
               anchor: 'center',

               //Allow the icons to overlap.
               allowOverlap: true
           },

           filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
       });

       map.layers.add(iconLayer);

       //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
       map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
           map.getCanvasContainer().style.cursor = 'pointer';
       });

       //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
       map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
           map.getCanvasContainer().style.cursor = 'grab';
       });

       //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
       map.events.add('click', clusterBubbleLayer, function(e) {
           map.setCamera({
               center: e.position,
               zoom: map.getCamera().zoom + 2
           });
       });

       //Add a click event to the icon layer and show the shape that was selected.
       map.events.add('click', iconLayer, function(e) {
           showPopup(e.shapes[0]);
       });

       //Add an event to monitor when the map is finished rendering the map after it has moved.
       map.events.add('render', function() {
           //Update the data in the list.
           updateListItems();
       });
    });
    ```

7. コーヒー ショップのデータセットを読み込む場合は、まずそれをダウンロードする必要があります。 さらに、テキスト ファイルを行単位に分割する必要があります。 先頭行には、ヘッダー情報が格納されています。 ここでは、コードをたどりやすいように、ヘッダーを解析してオブジェクトに格納しています。それを使用することで、各プロパティのセル インデックスを検索することができます。 先頭行の後、残りの行をループで処理しながらポイント フィーチャーを作成します。 そのポイント フィーチャーをデータ ソースに追加します。 最後に、リスト パネルを更新します。

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

8. リスト パネルが更新されると距離が計算されます。 これは、地図の中心から現在のマップ ビュー内のすべてのポイント フィーチャーまでの距離となります。 その後、距離でフィーチャーが並べ替えられます。 HTML が生成され、個々の場所がリスト パネルに表示されます。

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

9. リスト パネル内の項目がユーザーによって選択されたら、その項目に関連した図形がデータ ソースから取得されます。 図形に格納されているプロパティ情報に基づいてポップアップ ウィンドウが生成されます。 マップは、その図形が中心に来るように配置されます。 マップの幅が 700 ピクセル未満である場合は、ポップアップ ウィンドウが隠れないよう、マップ ビューがオフセットされます。

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is fewer than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

これで、完全に機能するストア ロケーターができあがりました。 ストア ロケーターの *index.html* ファイルを Web ブラウザーで開いてください。 地図上にクラスターが表示されたら、検索ボックスの使用、[My Location]\(現在位置\) ボタンの選択、クラスターの選択、地図の拡大表示によって場所を検索し、個々の場所を表示することができます。

ユーザーが初めて [My Location]\(現在位置\) ボタンを選択すると、ユーザーの位置情報へのアクセス許可を求めるセキュリティ警告がブラウザーに表示されます。 ユーザーが位置情報の共有に同意した場合、その場所が地図で拡大表示され、付近のコーヒー ショップが表示されます。

![ユーザーの位置情報へのアクセスを求めるブラウザーの要求のスクリーンショット](./media/tutorial-create-store-locator/geolocation-api-warning.png)

コーヒー ショップの所在地を含んだエリアを十分拡大表示すると、クラスターが個々の所在地に分かれます。 地図上のいずれかのアイコンを選択するか、サイド パネル内の項目を選択すると、ポップアップ ウィンドウが表示されます。 ポップアップには、選択した場所に関する情報が表示されます。

![完成したストア ロケーターのスクリーンショット](./media/tutorial-create-store-locator/finished-simple-store-locator.png)

ブラウザー ウィンドウのサイズを幅 700 ピクセル未満に変更するか、アプリケーションをモバイル デバイスで開いた場合、小さい画面用に合わせてレイアウトが変化します。

![小画面バージョンのストア ロケーターのスクリーンショット](./media/tutorial-create-store-locator/finished-simple-store-locator-mobile.png)

このチュートリアルでは、Azure Maps を使用して基本的なストア ロケーターを作成する方法を学習しました。 このチュートリアルで作成するストア ロケーターには、必要な機能がすべて備わっているものと思われます。 次のように、ストア ロケーターに機能を追加したり、より高度な機能を使用したりすれば、ユーザー エクスペリエンスの独自性を高めることができます。 

 * 検索ボックスの[入力補完](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI)を有効にする。  
 * [多言語のサポート](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)を追加する。 
 * ユーザーが[ルートに沿って場所をフィルター処理](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route)できるようにする。 
 * [フィルターを設定](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)する機能を追加する。 
 * クエリ文字列を使用して検索の初期値を指定できるようにする。 このオプションをストア ロケーターに実装すれば、ユーザーが検索をブックマークに登録したり共有したりできるようになります。 このページに対し、別のページから検索を渡す簡単な方法としても利用できます。  
 * ストア ロケーターを [Azure App Service Web アプリ](../app-service/quickstart-html.md)としてデプロイする。 
 * データベースにデータを格納して、付近の所在地を検索する。 詳細については、[SQL Server の空間データ型の概要](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017)と[空間データに対するニアレストネイバーのクエリ](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017)に関するページを参照してください。

[ソース コード全体をここで表示](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)できます。 [ライブ サンプルを表示](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)し、[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)を使用した Azure Maps の対象範囲と機能について詳しく理解できます。 また、[データドリブンのスタイルの式を使用](data-driven-style-expressions-web-sdk.md)して、ビジネス ロジックに適用することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

他のコード例や対話型のコーディング エクスペリエンスについては、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールの使用方法](how-to-use-map-control.md)
