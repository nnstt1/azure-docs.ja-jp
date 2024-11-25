---
title: HttpClient およびプロキシ (MSAL.NET) を提供する | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用して Azure AD に接続するための独自の HttpClient およびプロキシを提供する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 054dbb46f6a13a70b5b2cebbeb1d07d29d799762
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054830"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>MSAL.NET を使用した独自の HttpClient およびプロキシの提供
[クライアント アプリケーションを初期化する](msal-net-initializing-client-applications.md)ときに、`.WithHttpClientFactory method` を使用して、独自の HttpClient を提供できます。  独自の HttpClient を提供すると、(たとえば、ASP.NET Core Web アプリ/API で) HTTP プロキシのきめ細やかな制御、ユーザー エージェント ヘッダーのカスタマイズ、MSAL に対する特定の HttpClient の使用の強制など、高度なシナリオを有効にします。

`HttpClient` は、一度インスタンス化された後、アプリケーションの有効期間全体にわたって再利用することを目的としています。 「[解説](/dotnet/api/system.net.http.httpclient?view=net-5.0#remarks)」を参照してください。

## <a name="initialize-with-httpclientfactory"></a>HttpClientFactory での初期化
次の例では、`HttpClientFactory` を作成し、続いてそれを使用してパブリック クライアント アプリケーションを初期化します。

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="example-implementation-using-a-proxy"></a>プロキシを使用する実装例
```csharp
public class HttpFactoryWithProxy : IMsalHttpClientFactory
{
    private static HttpClient _httpClient;

    public HttpFactoryWithProxy()
    {
        // Consider using Lazy<T> 
        if (_httpClient == null) 
        {
            var proxy = new WebProxy
            {
                Address = new Uri($"http://{proxyHost}:{proxyPort}"),
                BypassProxyOnLocal = false,
                UseDefaultCredentials = false,
                Credentials = new NetworkCredential(
                    userName: proxyUserName,
                    password: proxyPassword)
            };

            // Now create a client handler which uses that proxy
            var httpClientHandler = new HttpClientHandler
            {
                Proxy = proxy,
            };

            _httpClient = new HttpClient(handler: httpClientHandler);
        }
    }

    public HttpClient GetHttpClient()
    {
        return _httpClient;
    }
}
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient および Xamarin iOS
Xamarin iOS を使用する場合は、明示的に iOS 7 以降用の `NSURLSession` ベースのハンドラー を使用する `HttpClient` を作成することをお勧めします。 MSAL.NET は、iOS 7 以降用の `NSURLSessionHandler` を使用する `HttpClient` を自動的に作成します。 詳細については、[HttpClient 用の Xamarin iOS ドキュメント](/xamarin/cross-platform/macios/http-stack)に関するページを参照してください。
