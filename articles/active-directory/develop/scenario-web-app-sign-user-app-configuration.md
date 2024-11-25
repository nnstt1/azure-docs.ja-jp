---
title: ユーザーをサインインさせる Web アプリを構成する | Azure
titleSuffix: Microsoft identity platform
description: ユーザーをサインインさせる Web アプリを構築する方法について説明します (コードの構成)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 39ca13ebaea9469dbbfa21cc8ded20b36dc2ed3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050662"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>ユーザーをサインインさせる Web アプリ:コード構成

ユーザーをサインインさせる Web アプリのコードを構成する方法について説明します。

## <a name="microsoft-libraries-supporting-web-apps"></a>Web アプリをサポートしている Microsoft ライブラリ

<!-- This section can be in an include for web app and web APIs -->
次の Microsoft ライブラリは、Web アプリ (および Web API) を保護するために使用されます。

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

目的のプラットフォームに対応するタブを選択してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

この記事および以下のコード スニペットは、[ASP.NET Core Web アプリ増分チュートリアルの第 1 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)から抜粋されています。

完全な実装の詳細については、このチュートリアルをご覧ください。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

この記事および以下のコード スニペットは、[ASP.NET Web アプリ サンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)から抜粋されたものです。

完全な実装の詳細については、このサンプルをご覧ください。

# <a name="java"></a>[Java](#tab/java)

この記事および以下のコード スニペットは、MSAL Java の [Microsoft Graph を呼び出す Java Web アプリケーション](https://github.com/Azure-Samples/ms-identity-java-webapp) サンプルから抜粋されています。

完全な実装の詳細については、このサンプルをご覧ください。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

この記事および以下のコード スニペットは、MSAL Node の [Node.js Web アプリケーションのユーザー サインイン](https://github.com/Azure-Samples/ms-identity-node) サンプルから抜粋されています。

完全な実装の詳細については、このサンプルをご覧ください。

# <a name="python"></a>[Python](#tab/python)

この記事および以下のコード スニペットは、MSAL Python の [Microsoft Graph を呼び出す Python Web アプリケーション](https://github.com/Azure-Samples/ms-identity-python-webapp) サンプルから抜粋されています。

完全な実装の詳細については、このサンプルをご覧ください。

---

## <a name="configuration-files"></a>構成ファイル

Microsoft ID プラットフォームを使用してユーザーをサインインさせる Web アプリケーションは、構成ファイルを使用して構成します。 構成で指定する必要がある値を次に示します。

- クラウド インスタンス (`Instance`) (たとえば、アプリを国内のクラウドで実行する場合など)
- 対象ユーザーのテナント ID (`TenantId`)
- Azure portal からコピーした、アプリケーションのクライアント ID (`ClientId`)

`Authority` への参照が表示される場合もあります。 `Authority` 値は、`Instance` と `TenantId` の値を連結したものです。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core では、これらの設定は [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) ファイルの "AzureAd" セクションにあります。

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.partner.microsoftonline.cn/common" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

ASP.NET Core では、別のファイル ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) がアプリケーションの URL (`applicationUrl`) と TLS/SSL ポート (`sslPort`) およびさまざまなプロファイルを含んでいます。

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Azure portal では、アプリケーションの **[認証]** ページで登録するリダイレクト URI は、これらの URL と一致している必要があります。 前記の 2 つの構成ファイルについては、それは `https://localhost:44321/signin-oidc` です。 これは `applicationUrl` が `http://localhost:3110` ですが、`sslPort` が (44321) と指定されているためです。 `CallbackPath` は、`appsettings.json` で定義されているように `/signin-oidc` となります。

同様に、サインアウト URI は `https://localhost:44321/signout-oidc` に設定されます。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET では、アプリケーションは [Web.Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) ファイルの 12 から 15 行目で構成されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Azure portal では、アプリケーションの **[認証]** ページで登録する応答 URI は、これらの URL と一致している必要があります。 つまり、`https://localhost:44326/` である必要があります。

# <a name="java"></a>[Java](#tab/java)

Java では、構成は `src/main/resources` の下の [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) ファイルにあります。

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Azure portal では、アプリケーションの **[認証]** ページで登録する応答 URI は、アプリケーションで定義される `redirectUri` インスタンスと一致している必要があります。 つまり、`http://localhost:8080/msal4jsample/secure/aad` および `http://localhost:8080/msal4jsample/graph/me` である必要があります。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

ここでは、構成パラメーターが `index.js` に存在します。

```javascript

const REDIRECT_URI = "http://localhost:3000/redirect";

const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/",
        clientSecret: "Enter_the_Client_Secret_Here"
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
};
```

Azure portal では、アプリケーションの [認証] ページで登録する応答 URI は、アプリケーションで定義される redirectUri インスタンスと一致している必要があります (`http://localhost:3000/redirect`)。

> [!NOTE]
> このクイックスタートでは、わかりやすくするために、構成ファイルにクライアント シークレットを格納することをお勧めします。 運用アプリでは、キー コンテナーや環境変数など、他の方法を使用してシークレットを格納できます。

# <a name="python"></a>[Python](#tab/python)

[app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py) の Python 構成ファイルを次に示します。

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common"
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> このクイックスタートでは、わかりやすくするために、構成ファイルにクライアント シークレットを格納することをお勧めします。 運用アプリでは、キー コンテナーや、[Flask のドキュメント](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)で説明されているような環境変数など、他の方法を使用してシークレットを格納できます。
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>初期化コード

初期化コードは、プラットフォームによって異なります。 ASP.NET Core と ASP.NET の場合、ユーザーのサインインは OpenID Connect ミドルウェアに委任されます。 ASP.NET または ASP.NET Core テンプレートでは、Azure Active Directory (Azure AD) v1.0 エンドポイント用の Web アプリケーションが生成されます。 それを Microsoft ID プラットフォームに適合させるには、少し構成が必要です。 Java の場合は、アプリケーションとの連携で Spring によって処理されます。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (および Web API) では、コントローラーまたはコントローラー アクションでの `[Authorize]` 属性があるため、アプリケーションは保護されます。 この属性では、ユーザーが認証されていることが確認されます。 アプリケーションを初期化するコードは、*Startup.cs* ファイル内にあります。

Microsoft ID プラットフォーム (旧称 Azure AD v2.0) を使用して認証を追加するには、次のコードを追加する必要があります。 コード内のコメントは、見てすぐにわかるものにする必要があります。

> [!NOTE]
> Microsoft.Identity.Web を活用する、Microsoft ID プラットフォーム用の新しい ASP.NET Core テンプレートを直接開始する場合は、.NET Core 3.1 および .NET 5.0 用のプロジェクト テンプレートを含むプレビュー NuGet パッケージをダウンロードできます。 インストールが完了したら、ASP.NET Core Web アプリケーション (MVC または Blazor) を直接インスタンス化できます。 詳細については、[Microsoft.Identity.Web Web アプリ プロジェクト テンプレートに関するページ](https://aka.ms/ms-id-web/webapp-project-templates)を参照してください。 以下のすべての手順が実行されるので、これは最も簡単な方法です。
>
> Visual Studio 内で現在の既定 ASP.NET Core Web プロジェクトを使用するか、`dotnet new mvc --auth SingleOrg` または `dotnet new webapp --auth SingleOrg` を使用してプロジェクトを開始すると、次のようなコードが表示されます。
>
> ```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> このコードでは、Azure AD v1.0 アプリケーションの作成に使用される従来の **Microsoft.AspNetCore.Authentication.AzureAD.UI** NuGet パッケージを使用します。 この記事では、そのコードに代わる Microsoft ID プラットフォーム (Azure AD v2.0) アプリケーションを作成する方法について説明します。
>

1. [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) および [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) NuGet パッケージをプロジェクトに追加します。 Microsoft.AspNetCore.Authentication.AzureAD.UI NuGet パッケージが存在する場合は削除します。

2. `AddMicrosoftIdentityWebAppAuthentication` メソッドと `AddMicrosoftIdentityUI` メソッドを使用するように `ConfigureServices` のコードを更新します。

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. *Startup.cs* の `Configure` メソッドで、`app.UseAuthentication();` を呼び出して認証を有効にします。

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

上記のコードには、以下が含まれます。
- `AddMicrosoftIdentityWebAppAuthentication` 拡張メソッドは、**Microsoft.Identity.Web** に定義されています。 それでは次のことが行われます。
  - 認証サービスが追加されます。
  - 構成ファイルを読み取るためのオプションが構成されます (ここでは、"AzureAD" セクションから)。
  - 機関が Microsoft ID プラットフォームになるように OpenID Connect オプションが構成されます。
  - トークンの発行者が検証されます。
  - 名前に対応する要求が、ID トークンの `preferred_username` 要求からマップされるようにします。

- 構成オブジェクトに加えて、`AddMicrosoftIdentityWebAppAuthentication` を呼び出すときに構成セクションの名前を指定できます。 既定では、これは `AzureAd` です。

- `AddMicrosoftIdentityWebAppAuthentication` には、高度なシナリオのためのその他のパラメーターがあります。 たとえば、OpenID Connect ミドルウェア イベントをトレースすると、認証が機能しない場合の Web アプリケーションのトラブルシューティングに役立ちます。 省略可能なパラメーター `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` を `true` に設定すると、HTTP 応答から `HttpContext.User` 内のユーザーの ID への進行に合わせて、ASP.NET Core ミドルウェアのセットによって情報がどのように処理されるかが表示されます。

- `AddMicrosoftIdentityUI` 拡張メソッドは、**Microsoft.Identity.Web.UI** に定義されています。 サインインおよびサインアウトを処理する既定のコントローラーが提供されます。

Microsoft.Identity.Web で Web アプリを作成する方法の詳細については、<https://aka.ms/ms-id-web/webapp> を参照してください。

> [!WARNING]
> Azure AD と外部ログイン プロバイダーを使用する場合、Microsoft.Identity.Web では、現在、**個々のユーザーアカウント** のシナリオ (アプリ内にユーザーアカウントを格納する) はサポートされていません。 詳細については、次の情報を参照してください。[AzureAD/microsoft-identity-web#133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET Web アプリおよび Web API での認証に関連したコードは、[App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) ファイルにあります。

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java サンプルでは、Spring フレームワークが使用されています。 各 HTTP 応答をインターセプトするフィルターを実装するため、アプリケーションは保護されています。 Java Web アプリのクイックスタートでは、このフィルターは `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` 内の `AuthFilter` です。

フィルターは OAuth 2.0 認証コードフローを処理し、ユーザーが認証されているかどうかを確認します (`isAuthenticated()` メソッド)。 ユーザーが認証されていない場合は、Azure AD 承認エンドポイントの URL が計算されて、ブラウザーはこの URI にリダイレクトされます。

認証コード フローを含む応答が到着すると、MSAL Java でトークンを取得できます。 最後にトークン エンドポイントからトークンを受け取ると (リダイレクト URI で)、ユーザーはサインインされます。

詳細については、[AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) の `doFilter()` メソッドを参照してください。

> [!NOTE]
> `doFilter()` のコードは少し異なる順序で記述されていますが、フローは説明されているものです。

このメソッドによってトリガーされる認証コード フローの詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー](v2-oauth2-auth-code-flow.md)」を参照してください。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const msal = require('@azure/msal-node');

// Create msal application object
const cca = new msal.ConfidentialClientApplication(config);
```

# <a name="python"></a>[Python](#tab/python)

Python サンプルでは Flask が使用されます。 Flask と MSAL Python の初期化は、[app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28) で行われます。

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config

app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>次のステップ

次の記事では、サインインとサインアウトをトリガーする方法について説明します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

このシナリオの次の記事である[サインインとサインアウト](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)に関する記事に進みます。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

このシナリオの次の記事である[サインインとサインアウト](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)に関する記事に進みます。

# <a name="java"></a>[Java](#tab/java)

このシナリオの次の記事である[サインインとサインアウト](./scenario-web-app-sign-user-sign-in.md?tabs=java)に関する記事に進みます。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

このシナリオの次の記事である「[サインイン](./scenario-web-app-sign-user-sign-in.md?tabs=nodejs)」に関する記事に進みます。

# <a name="python"></a>[Python](#tab/python)

このシナリオの次の記事である[サインインとサインアウト](./scenario-web-app-sign-user-sign-in.md?tabs=python)に関する記事に進みます。

---
