---
title: Azure Active Directory アプリケーション プロキシを使用してオンプレミス API にアクセスする
description: Azure Active Directory のアプリケーション プロキシは、オンプレミスまたはクラウド VM でホストされている API およびビジネス ロジックにネイティブ アプリが安全にアクセスできるようにします。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.custom: has-adal-ref
ms.openlocfilehash: eb2036976e9e73e7ad466974f9885b9035152851
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059492"
---
# <a name="secure-access-to-on-premises-apis-with-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシを使用してオンプレミス API に安全にアクセスする

ビジネス ロジック API がオンプレミスで実行されていたり、クラウドの仮想マシンでホストされていたりすることがあります。 Android、iOS、Mac、または Windows のネイティブ アプリは、データを使用したりユーザー対話を提供したりするために、この API エンドポイントと対話する必要があります。 Azure AD アプリケーション プロキシと [Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) を使用すると、ネイティブ アプリはオンプレミス API に安全にアクセスすることができます。 Azure Active Directory アプリケーション プロキシは、ファイアウォール ポートを開いてアプリ層で認証と承認を制御するよりも高速かつ安全なソリューションです。

この記事では、ネイティブ アプリがアクセスできる Web API サービスをホストするための Azure AD アプリケーション プロキシ ソリューションを設定する手順を説明します。

## <a name="overview"></a>概要

次の図は、オンプレミス API を公開するための従来の方法を示したものです。 このアプローチでは、受信ポート 80 と 443 を開く必要があります。

![従来の API アクセス](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

次の図は、Azure AD アプリケーション プロキシを使用して、受信ポートを開かずに API を安全に公開する方法を示しています。

![Azure AD アプリケーション プロキシの API アクセス](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD アプリケーション プロキシはこのソリューションのバックボーンを形成し、API アクセスのパブリック エンドポイントとして機能し、認証と承認を提供します。 [Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) ライブラリを使用することで、さまざまなプラットフォームから API にアクセスできます。

Azure AD アプリケーション プロキシの認証と承認は Azure AD 上に構築されているため、Azure AD の条件付きアクセスを使用して、信頼済みデバイスのみがアプリケーション プロキシ経由で公開された API にアクセスできるようにすることが可能です。 デスクトップについては Azure AD Join または Azure AD Hybrid Joined を使用し、デバイスについては Intune Managed を使用します。 Azure AD Multi-Factor Authentication などの Azure Active Directory Premium 機能や、[Azure Identity Protection](../identity-protection/overview-identity-protection.md) の機械学習支援型のセキュリティも活用できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、次のものが必要です。

- アプリを作成して登録できるアカウントを持つ、Azure ディレクトリへの管理者アクセス権
- [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) からのサンプル Web API およびネイティブ クライアント アプリ

## <a name="publish-the-api-through-application-proxy"></a>アプリケーション プロキシ経由で API を公開する

アプリケーション プロキシを介してイントラネットの外部で API を公開するには、Web アプリを公開する場合と同じパターンに従ってください。 詳細については、[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)を参照してください。

アプリケーション プロキシ経由で SecretAPI Web API を公開するには、次のようにします。

1. ローカル コンピューターまたはイントラネット上で、サンプルの SecretAPI プロジェクトを ASP.NET Web アプリとしてビルドして公開します。 Web アプリにローカルでアクセスできることを確認します。

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。 次に、 **[エンタープライズ アプリケーション]** を選択します。

1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページの上部で、 **[新しいアプリケーション]** を選択します。

1. **[アプリケーションの追加]** ページで、 **[オンプレミスのアプリケーション]** を選択します。 **[独自のオンプレミスのアプリケーションの追加]** ページが表示されます。

1. アプリケーション プロキシ コネクタがインストールされていない場合は、インストールするように求められます。 **[アプリケーション プロキシ コネクタのダウンロード]** を選択してコネクタをダウンロードしてインストールします。

1. アプリケーション プロキシ コネクタをインストールしたら、 **[独自のオンプレミスのアプリケーションの追加]** ページで、次のようにします。

   1. **[名前]** の横に *SecretAPI* と入力します。

   1. **[内部 URL]** の横に、イントラネット内から API へのアクセスに使用する URL を入力します。

   1. **[事前認証]** が **[Azure Active Directory]** に設定されていることを確認します。

   1. ページの上部にある **[追加]** を選択し、アプリが作成されるまで待機します。

   ![API アプリを追加する](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページで、 **[SecretAPI]** アプリを選択します。

1. **[SecretAPI - 概要]** ページで、左側のナビゲーションから **[プロパティ]** を選択します。

1. エンド ユーザーが **[MyApps]** パネルで API を利用できないようにするために、 **[プロパティ]** ページの下部にある **[Visible to users]\(ユーザーに表示する\)** を **[いいえ]** に設定し、 **[保存]** を選択します。

   ![ユーザーに表示しない](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Azure AD アプリケーション プロキシを通じて Web API を公開しました。 次に、アプリにアクセスできるユーザーを追加します。

1. **[SecretAPI - 概要]** ページで、左側のナビゲーションから **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ページで **[ユーザーの追加]** を選択します。

1. **[割り当ての追加]** ページで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ページで、少なくとも自分自身を含め、アプリにアクセスできるユーザーを検索して選択します。 すべてのユーザーを選択した後、 **[選択]** を選択します。

   ![ユーザーを選択して割り当てる](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. **[割り当ての追加]** ページに戻り、 **[割り当て]** を選択します。

> [!NOTE]
> 統合 Windows 認証を使用する API では、[追加の手順](./application-proxy-configure-single-sign-on-with-kcd.md)が必要になることがあります。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>ネイティブ アプリを登録して API へのアクセス権を付与する

ネイティブ アプリは、特定のプラットフォームまたはデバイスで使用するために開発されたプログラムです。 ネイティブ アプリが API に接続してアクセスできるようにするには、Azure AD に登録する必要があります。 次の手順では、ネイティブ アプリを登録し、アプリケーション プロキシ経由で公開された Web API へのアクセス権を付与する方法を示します。

AppProxyNativeAppSample ネイティブ アプリを登録するには、次のようにします。

1. Azure Active Directory の **[概要]** ページで、 **[アプリの登録]** を選択し、 **[アプリの登録]** ウィンドウの上部で **[新規登録]** を選択します。

1. **[アプリケーションの登録]** ページで、次のようにします。

   1. **[名前]** に *AppProxyNativeAppSample* と入力します。

   1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。

   1. **[リダイレクト URL]** で、ドロップダウンから **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、 *https://login.microsoftonline.com/common/oauth2/nativeclient* と入力します。

   1. **[登録]** を選択し、アプリが正常に登録されるまで待ちます。

      ![[新しいアプリケーションの登録]](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

これで AppProxyNativeAppSample アプリが Azure Active Directory に登録されました。 ネイティブ アプリに SecretAPI Web API へのアクセス権を付与するには、次のようにします。

1. Azure Active Directory の **[概要]**  >  **[アプリの登録]** ページで、 **[AppProxyNativeAppSample]** アプリを選択します。

1. **[AppProxyNativeAppSample]** ページで、左側のナビゲーションから **[API のアクセス許可]** を選択します。

1. **[API のアクセス許可]** ページで、 **[アクセス許可の追加]** を選択します。

1. 最初の **[API アクセス許可の要求]** ページで、 **[所属する組織で使用している API]** タブを選択し、 **[SecretAPI]** を検索して選択します。

1. 次の **[API アクセス許可の要求]** ページで、 **[user_impersonation]** の横のチェック ボックスをオンにし、 **[アクセス許可の追加]** を選択します。

    ![API を選択する](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. **[API のアクセス許可]** ページに戻り、 **[Contoso に管理者の同意を与えます]** を選択して、他のユーザーがアプリに個別に同意しなくても済むようにします。

## <a name="configure-the-native-app-code"></a>ネイティブ アプリ コードを構成する

最後の手順では、ネイティブ アプリを構成します。 NativeClient サンプル アプリの *Form1.cs* ファイルからのものである次のスニペットによって、MSAL ライブラリは、API 呼び出しを要求するためのトークンを取得し、それをベアラーとしてアプリのヘッダーに添付します。

```csharp
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
    .Create(<App ID of the Native app>)
    .WithDefaultRedirectUri() // Will automatically use the default Uri for native app
    .WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
    .Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
{
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
}

if (authResult != null)
{
    // Use the Access Token to access the Proxy Application

    HttpClient httpClient = new HttpClient();
    HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
}
```

ネイティブ アプリが Azure Active Directory に接続し、API アプリのプロキシを呼び出すように構成するには、NativeClient サンプル アプリの *App.config* ファイルにあるプレース ホルダーの値を Azure AD からの値で更新します。

- **[ディレクトリ (テナント) ID]** を `<add key="ida:Tenant" value="" />` フィールドに貼り付けます。 この値 (GUID) は、いずれかのアプリの **[概要]** ページから検索してコピーすることができます。

- AppProxyNativeAppSample の **[アプリケーション (クライアント) ID]** を `<add key="ida:ClientId" value="" />` フィールドに貼り付けます。 この値 (GUID) は AppProxyNativeAppSample の **[概要]** ページから検索してコピーすることができます。

- AppProxyNativeAppSample の **[リダイレクト URI]** を `<add key="ida:RedirectUri" value="" />` フィールドに貼り付けます。 この値 (URI) は AppProxyNativeAppSample の **[認証]** ページから検索してコピーすることができます。

- SecretAPI の **[アプリケーション ID の URI]** を `<add key="todo:TodoListResourceId" value="" />` フィールドに貼り付けます。 この値 (URI) は SecretAPI の **[API の公開]** ページから検索してコピーすることができます。

- SecretAPI の **[ホーム ページ URL]** を `<add key="todo:TodoListBaseAddress" value="" />` フィールドに貼り付けます。 この値 (URI) は SecretAPI の **[ブランド]** ページから検索してコピーすることができます。

パラメーターを構成した後は、ネイティブ アプリをビルドして実行します。 **[サインイン]** ボタンを選択すると、アプリでサインインが行われ、SecretAPI に正常に接続されたことを確認する成功画面が表示されます。

![スクリーンショットに、Secret API の成功のメッセージと [OK] ボタンが示されています。](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)
- [クイック スタート: Web API にアクセスするようにクライアント アプリケーションを構成する](../develop/quickstart-configure-app-access-web-apis.md)
- [ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする方法](application-proxy-configure-native-client-application.md)
