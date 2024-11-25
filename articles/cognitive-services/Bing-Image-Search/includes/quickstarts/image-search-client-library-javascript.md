---
title: Bing Image Search JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: f56346b9282be42d6022fd6f15f4ce206ae8dbd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253213"
---
このクイックスタートでは、Bing Image Search クライアント ライブラリを使用して最初の画像検索を行います。このクライアント ライブラリは、API のラッパーであり、同じ機能を含んでいます。 このシンプルな JavaScript アプリケーションは、イメージ検索クエリを送信し、JSON 応答を解析して、返された最初のイメージの URL を表示します。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) で入手できます。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。
* [Bing Image Search SDK for JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  インストールするには、`npm install @azure/cognitiveservices-imagesearch` を実行します
* クライアントを認証するための `CognitiveServicesCredentials` クラス (`@azure/ms-rest-azure-js` パッケージに含まれています)。
     * インストールするには、`npm install @azure/ms-rest-azure-js` を実行します

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、厳格度、https、およびその他の要件を設定します。

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. プロジェクトの main メソッドでは、有効なサブスクリプション キー、Bing で返す必要のある画像の結果、および検索語句の変数を作成します。 その後、キーを使用して画像検索クライアントをインスタンス化します。

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>非同期ヘルパー関数を作成する

1. クライアントを非同期に呼び出す関数を作成し、Bing Image Search サービスから応答を返します。

    ```javascript
    // a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```

## <a name="send-a-query-and-handle-the-response"></a>クエリを送信して応答を処理する

1. ヘルパー関数を呼び出して `promise` を処理し、応答で返されたイメージの結果を解析します。

    応答に検索結果が含まれている場合は、最初の結果を格納して、返された URL の合計数と共にサムネイルの URL などの詳細を出力します。
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](../../overview.md)
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Azure Cognitive Services SDK の Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Azure Cognitive Services のドキュメント](../../../index.yml)
* [Bing Image Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)