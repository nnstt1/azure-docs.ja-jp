---
title: クイック スタート:REST API と C# を使用して画像に関する分析情報を取得する - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API および C# を使用して画像をアップロードし、その画像に関する分析情報を取得する方法について説明します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e2f2b1d21e36f059b3848e6f9a1a9d6ed02c05ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669457"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>クイック スタート:Bing Visual Search REST API と C# を使用して画像に関する分析情報を取得する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

このクイックスタートでは、Bing Visual Search API に画像をアップロードして、返される分析情報を表示する方法を紹介します。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) のいずれかのエディション。
* NuGet パッケージとして入手できる [Json.NET フレームワーク](https://www.newtonsoft.com/json)。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. Visual Studio で、BingSearchApisQuickStart という名前の新しいコンソール ソリューションを作成します。 メイン コード ファイルに次の名前空間を追加します。

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. サブスクリプション キー、エンドポイント、およびアップロードする画像へのパスを格納する変数を追加します。 `uriBase` 値には、次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、お使いのリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用できます。

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. 画像へのパスを取得するための `GetImageFileName()` という名前のメソッドを作成します。
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. 画像のバイナリ データを取得するためのメソッドを作成します。

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>フォーム データを作成する

1. ローカルの画像をアップロードするには、最初に、API に送信するフォーム データを作成します。 フォーム データには、`Content-Disposition` ヘッダー、"image" に設定されている `name` パラメーター、画像のファイル名に設定されている `filename` パラメーターが含まれます。 フォームの内容には、画像のバイナリ データが含まれます。 アップロードできる画像の最大サイズは、1 MB です。

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. POST フォーム データを書式設定する境界文字列を追加します。 境界文字列により、データの開始、終了、改行文字が決まります。

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. フォーム データにパラメーターを追加するには、次の変数を使います。

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. `BuildFormDataStart()` という名前の関数を作成し、境界文字列と画像へのパスを使用して、フォーム データの開始を作成します。
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. `BuildFormDataEnd()` という名前の関数を作成し、境界文字列を使用して、フォーム データの終了を作成します。
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Bing Visual Search API を呼び出す

1. Bing Visual Search エンドポイントを呼び出し、JSON 応答を返すための関数を作成します。 その関数では、フォーム データの開始と終了、画像データを含むバイト配列、および `contentType` 値を受け取ります。

2. `WebRequest` 使用して、URI、contentType 値、ヘッダーを格納します。  

3. `request.GetRequestStream()` を使用して、フォーム データと画像データを書き込んだ後、応答を受け取ります。 関数は次のコードのようになります。
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Main メソッドを作成する

1. アプリケーションの `Main()` メソッドでは、画像のファイル名とバイナリ データを取得します。

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. 境界を書式設定して、POST の本文を設定します。 その後、`BuildFormDataStart()` と `BuildFormDataEnd()` を呼び出して、フォーム データを作成します。

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. `CONTENT_TYPE_HEADER_PARAMS` とフォーム データの境界を書式設定して、`ContentType` 値を作成します。

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. `BingImageSearch()` を呼び出して API の応答を取得し、応答を出力します。

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HTTPClient の使用

`HttpClient` を使用する場合は、`MultipartFormDataContent` クラスを使用してフォーム データを作成できます。 次のコード セクションを使用して、前の例にある対応するメソッドを置き換えます。

1. `Main()` メソッドを次のコードに置き換えます。

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. `BingImageSearch()` メソッドを次のコードに置き換えます。

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Visual Search のシングルページ Web アプリを作成する](../tutorial-bing-visual-search-single-page-app.md)