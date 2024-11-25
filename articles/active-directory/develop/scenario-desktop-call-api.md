---
title: デスクトップ アプリから Web API を呼び出す | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデスクトップ アプリを構築する方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9cfd26b8cca62c106cd389e4e9e33d058a46c842
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681276"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Web API を呼び出すデスクトップ アプリ:Web API を呼び出す

トークンを取得すると、保護された Web API を呼び出せます。

## <a name="call-a-web-api"></a>Web API を呼び出す

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

PublicClientApplication pca = PublicClientApplication.builder(clientId)
        .authority(authority)
        .build();

// Acquire a token, acquireTokenHelper would call publicClientApplication's acquireTokenSilently then acquireToken
// see https://github.com/Azure-Samples/ms-identity-java-desktop for a full example
IAuthenticationResult authenticationResult = acquireTokenHelper(pca);

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + authenticationResult.accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[MacOS](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL での Web API の呼び出し

トークンを取得するメソッドは `MSALResult` オブジェクトを返します。 `MSALResult` により、Web API を呼び出すために使用できる `accessToken` プロパティが公開されます。 保護された Web API にアクセスするための呼び出しを行う前に、アクセス トークンを HTTP Authorization ヘッダーに追加してください。

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };

NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>複数の API の呼び出し:増分同意と条件付きアクセス

同じユーザーに対して複数の API を呼び出すには、最初の API のトークンを取得した後、`AcquireTokenSilent` を呼び出します。 その他の API のトークンは、ほとんどの場合、自動的に取得されます。

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

対話は、次の場合に必要です。

- ユーザーが最初の API については同意したが、より多くのスコープについて同意する必要が生じた。 この種の同意は、増分同意 と呼ばれます。
- 最初の API は多要素認証を必要としなかったが、次の API は必要とする。

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Axios](https://www.npmjs.com/package/axios)などの HTTP クライアントを使用し、"*認可ベアラー*" としてアクセス トークンを使用して API エンドポイント URI を呼び出します。

```javascript
const axios = require('axios');

async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

```

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-desktop-production.md)」に進みます。
