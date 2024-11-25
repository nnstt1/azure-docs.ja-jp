---
title: Web API を呼び出すトークンを取得する (デーモン アプリ) - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すデーモン アプリ (トークンを取得する) をビルドする方法について学習します。
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
ms.openlocfilehash: 9e037a1ba1ba3c0820321662d4f3feffa6dd2b35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050624"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Web API を呼び出すデーモン アプリ - トークンを取得する

機密クライアント アプリケーションを構築したら、`AcquireTokenForClient` を呼び出し、スコープを渡し、必要に応じてトークンを強制的に更新することでアプリのトークンを取得できます。

## <a name="scopes-to-request"></a>要求するスコープ

クライアントの資格情報フローのために要求するスコープは、後に `/.default` が続くリソースの名前です。 この表記は、アプリケーションの登録時に静的に宣言された "*アプリケーション レベルのアクセス許可*" を使用するように Azure Active Directory (Azure AD) に指示します。 また、これらの API アクセス許可は、テナント管理者によって付与される必要があります。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'], // e.g. 'https://graph.microsoft.com/.default'
};
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python では、構成ファイルは次のコード スニペットのようになります。

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD (v1.0) リソース

クライアント資格情報に使用するスコープは、常に、リソース ID の後に `/.default` が続くものにする必要があります。

> [!IMPORTANT]
> MSAL がバージョン 1.0 のアクセス トークンを受け入れるリソースのためのアクセス トークンを要求すると、Azure AD では、最後のスラッシュの前のすべてを取得し、それをリソース ID として使用することで、要求されたスコープから目的の対象ユーザーを解析します。
> したがって、Azure SQL Database () のように、 `https://database.windows.net` リソースは、末尾がスラッシュ (Azure SQL Database の場合) であることを想定している場合は、 `https://database.windows.net/` のスコープを要求する必要があり `https://database.windows.net//.default` ます。 (二重スラッシュに注意してください)。「MSAL.NET issue [#747: `Resource url's trailing slash is omitted, which caused sql auth failure`](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)」も参照してください。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

アプリのトークンを取得するには、プラットフォームに応じて `AcquireTokenForClient` またはそれと同等のものを使用します。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient によるアプリケーション トークン キャッシュの使用

MSAL.NET では、`AcquireTokenForClient` によってアプリケーション トークン キャッシュが使用されます (他のすべての AcquireToken *XX* メソッドでは、ユーザー トークン キャッシュが使用されます)。`AcquireTokenSilent` は "*ユーザー*" トークン キャッシュを使用するため、`AcquireTokenForClient` を呼び出す前に `AcquireTokenSilent` を呼び出さないでください。 `AcquireTokenForClient` は *アプリケーション* トークン キャッシュそのものをチェックして、更新します。

# <a name="java"></a>[Java](#tab/java)

このコードは、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)から抽出したものです。

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

次のコード スニペットは、MSAL Node の機密クライアント アプリケーションでトークンを取得する方法を示しています。

```JavaScript
try {
    const authResponse = await cca.acquireTokenByClientCredential(tokenRequest);
    console.log(authResponse.accessToken) // display access token
} catch (error) {
    console.log(error);
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

---

### <a name="protocol"></a>Protocol

選択した言語のライブラリがまだない場合は、プロトコルを直接使用することもできます。

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>最初のケース:共有シークレットを使用してトークン要求にアクセスする

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>2 番目のケース:証明書を使用してトークン要求にアクセスする

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

詳細については、プロトコルのドキュメント:[Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー](v2-oauth2-client-creds-grant-flow.md)。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="did-you-use-the-resourcedefault-scope"></a>リソース/.default スコープを使用しましたか?

無効なスコープを使用したことを示すエラー メッセージが表示された場合は、`resource/.default` スコープを使用しなかった可能性があります。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>管理者の同意を提供するのを忘れましたか? デーモン アプリにはそれが必要です。

API を呼び出すときに、**この操作を完了するのに十分な特権がありません** というエラーが表示された場合は、テナント管理者がアプリケーションにアクセス許可を付与する必要があります。 上記のクライアント アプリの登録の手順 6 を参照してください。
通常、次のようなエラーが表示されます。

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

### <a name="are-you-calling-your-own-api"></a>独自の API を呼び出している場合

デーモン アプリのアプリ登録にアプリのアクセス許可を追加できなかった場合に、独自の Web API を呼び出す場合は、Web API でアプリ ロールが公開されていますか?

詳細については、「[アプリケーションのアクセス許可 (アプリ ロール) の公開](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)」を参照してください。特に、「[Azure AD で、許可されたクライアントのみに Web API のトークンが発行されることを確認する](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients)」を参照してください。

## <a name="next-steps"></a>次のステップ

# <a name="net"></a>[.NET](#tab/dotnet)

このシナリオの次の記事である [Web API の呼び出し](./scenario-daemon-call-api.md?tabs=dotnet)に関する記事に進みます。

# <a name="java"></a>[Java](#tab/java)

このシナリオの次の記事である [Web API の呼び出し](./scenario-daemon-call-api.md?tabs=java)に関する記事に進みます。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

このシナリオの次の記事である [Web API の呼び出し](./scenario-daemon-call-api.md?tabs=nodejs)に関する記事に進みます。

# <a name="python"></a>[Python](#tab/python)

このシナリオの次の記事である [Web API の呼び出し](./scenario-daemon-call-api.md?tabs=python)に関する記事に進みます。

---
