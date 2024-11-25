---
title: Azure AD ユーザーをサインインさせるアプリを構築する
titleSuffix: Microsoft identity platform
description: 任意の Azure Active Directory テナントからユーザーをサインインできるマルチテナント アプリケーションを構築する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 45e3489416a68b23d89a3966fe8515713d834a5b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030426"
---
# <a name="sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>すべての Azure Active Directory ユーザーがマルチテナント アプリケーション パターンを使用してサインインする

多くの組織にサービスとしてのソフトウェア (SaaS) アプリケーションを提供する場合、すべての Azure Active Directory (Azure AD) テナントからのサインインを受け入れるようにアプリケーションを構成できます。 この構成は "*アプリケーションのマルチテナント化*" と呼ばれます。 すべての Azure AD テナントのユーザーは、アプリケーションで自分のアカウントを使用することに同意すれば、そのアプリケーションにサインインできるようになります。

独自のアカウント システムを使用するか、他のクラウド プロバイダーの他の種類のサインインをサポートする既存のアプリケーションがある場合、任意のテナントからの Azure AD サインインの追加は簡単です。 アプリを登録し、OAuth2、OpenID Connect、または SAML を使用してサインイン コードを追加し、アプリケーションに [[Microsoft でサインイン] ボタン][AAD-App-Branding]を配置するだけです。

> [!NOTE]
> この記事では、Azure AD のシングルテナント アプリケーションの構築に慣れていることを前提としています。 慣れていない場合は、[開発者ガイドのホーム ページ][AAD-Dev-Guide]にあるいずれかのクイックスタートから始めます。

アプリケーションを Azure AD マルチテナント アプリケーションに変換する手順は、次の 4 つです。

1. [アプリケーション登録をマルチテナントに更新する](#update-registration-to-be-multi-tenant)
2. [/common エンドポイントに要求を送信するようにコードを更新する](#update-your-code-to-send-requests-to-common)
3. [複数の issuer 値を処理するようにコードを更新する](#update-your-code-to-handle-multiple-issuer-values)
4. [ユーザーおよび管理者の同意について理解し、コードに適切な変更を加える](#understand-user-and-admin-consent)

それでは、各手順の詳細を見ていきましょう。 また、サンプル「[Azure AD と OpenID Connect を使用して Microsoft Graph を呼び出すマルチテナント SaaS Web アプリケーションを構築する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)」に直接移動することもできます。

## <a name="update-registration-to-be-multi-tenant"></a>登録をマルチテナントに更新する

既定では、Azure AD の Web アプリケーション/API の登録はシングルテナントです。 登録をマルチテナントにするには、[Azure portal][AZURE-portal] のアプリケーション登録の **[認証]** ウィンドウで **[サポートされているアカウントの種類]** スイッチを見つけて、 **[任意の組織のディレクトリ内のアカウント]** に設定します。

Azure AD でアプリケーションをマルチテナントにするには、そのアプリケーションのアプリ ID URI をグローバルに一意なものにする必要があります。 アプリ ID URI は、プロトコル メッセージでアプリケーションを識別する手段の 1 つです。 シングル テナント アプリケーションの場合、アプリ ID URI はそのテナント内で一意であれば十分です。 これに対してマルチテナント アプリケーションの場合、Azure AD が全テナントから該当するアプリケーションを特定できるように、アプリ ID URI がグローバルで一意になっている必要があります。 グローバルな一意性を確保するため、アプリ ID URI には Azure AD テナントの検証済みドメインと一致するホスト名が含まれていなければならないという条件が存在します。

既定で、Azure Portal で作成されたアプリには、アプリの作成時にグローバルに一意のアプリ ID URI が設定されますが、この値は変更することができます。 たとえば、テナントの名前が contoso.onmicrosoft.com である場合、有効なアプリケーション ID URI は `https://contoso.onmicrosoft.com/myapp`のようになります。 また、テナントの検証済みドメインが `contoso.com` である場合は、有効なアプリケーション ID URI は `https://contoso.com/myapp` のようになります。 アプリ ID URI がこのパターンに従っていないと、アプリケーションのマルチテナントとしての設定が失敗します。

## <a name="update-your-code-to-send-requests-to-common"></a>/common に要求を送信するようにコードを更新する

シングルテナント アプリケーションでは、サインイン要求はテナントのサインイン エンドポイントに送信されます。 たとえば、contoso.onmicrosoft.com のエンドポイントは次のようになります。`https://login.microsoftonline.com/contoso.onmicrosoft.com` テナントのエンドポイントに要求が送信されることで、そのテナントのユーザー (またはゲスト) は当該テナントのアプリケーションにサインインできます。

マルチテナント アプリケーションでは、アプリケーションがユーザーのサインイン元のテナントを事前に知ることができないため、テナントのエンドポイントに要求を送信できません。 このため、要求は、すべての Azure AD テナントと多重通信するエンドポイントに送信されます。`https://login.microsoftonline.com/common`

Microsoft ID プラットフォームは、/common エンドポイントで要求を受信すると、ユーザーのサインインを行い、結果としてユーザーのサインイン元のテナントを特定します。 /common エンドポイントは、Azure AD でサポートされるすべての認証プロトコル (OpenID Connect、OAuth 2.0、SAML 2.0、WS-Federation) に対応しています。

その後のアプリケーションに対するサインイン応答には、ユーザーを表すトークンが含まれます。 アプリケーションは、トークンの issuer 値に基づいてユーザーのサインイン元のテナントを特定できます。 /common エンドポイントから応答が返された場合、トークン内の issuer 値はユーザーのテナントに対応しています。

> [!IMPORTANT]
> /common エンドポイントはテナントや発行者ではなく、マルチプレクサーです。 /common を使用する場合、アプリケーションのトークン検証ロジックを、このことを考慮するように更新する必要があります。

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>複数の issuer 値を処理するようにコードを更新する

Web アプリケーションと Web API は、Microsoft ID プラットフォームからトークンを受信して検証します。

> [!NOTE]
> ネイティブ クライアント アプリケーションにより、Microsoft ID プラットフォームにトークンが要求され受信する際に、API にトークンが送信され、API で検証が行われます。 ネイティブ アプリケーションではアクセス トークンを検証しないため、トークンを不透明なものとして処理する必要があります。

アプリケーションで Microsoft ID プラットフォームから受信したトークンが検証される仕組みを見てみましょう。 シングルテナント アプリケーションは通常、次のようなエンドポイント値を取得します。

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

そして、この値を使用して、次のようなメタデータ URL (この例では OpenID Connect) を作成します。

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

さらに、この URL を使用して、トークンの検証に使用する 2 種類の重要な情報である、テナントの署名キーと issuer 値をダウンロードします。

各 Azure AD テナントは、次のような形をした一意の issuer 値を持ちます。

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

ここで、GUID の値は、テナントのテナント ID を名前変更できるようにしたものです。 前述の `contoso.onmicrosoft.com` のメタデータ リンクを選択すると、ドキュメントでこの issuer 値を確認できます。

シングルテナント アプリケーションでトークンを検証する際、トークンの署名を、メタデータ ドキュメントからの署名キーに照らしてチェックします。 このテストにより、トークン内の issuer 値が、メタデータ ドキュメント内で見つかった値に一致することを確認できます。

/common エンドポイントはテナントに対応しておらず発行者でもないので、/common のメタデータの issuer 値を確認すると、実際の値の代わりに次のようなテンプレート URL が表示されます。

```http
https://sts.windows.net/{tenantid}/
```

このため、マルチテナント アプリケーションでは、メタデータの issuer 値をトークンの `issuer` 値と照合するだけでは、トークンの検証を行うことができません。 マルチテナント アプリケーションには、issuer 値のテナント ID の部分に基づいて issuer 値が有効であるかどうかを判定するロジックが必要になります。

たとえば、マルチテナント アプリケーションで、アプリケーションのサービスにサインアップしている特定のテナントからのサインインのみを許可するには、トークンの issuer 値または `tid` 要求値のいずれかを調べて、サブスクライバーのリストにテナントが含まれていることを確認する必要があります。 マルチテナント アプリケーションではユーザーのみを処理して、テナントに基づくアクセスの判定を行わない場合は、issuer 値を完全に無視することができます。

[マルチテナントのサンプル][AAD-Samples-MT]では、Azure AD テナントでサインインできるようにするために、issuer の検証が無効化されています。

## <a name="understand-user-and-admin-consent"></a>ユーザーおよび管理者の同意について

Azure AD のアプリケーションにユーザーがサインインするには、そのアプリケーションがユーザーのテナントに表示される必要があります。 このようにすることで、組織では、ユーザーがテナントからアプリケーションにサインインする場合に一意のポリシーを適用するなどの操作を行うことができます。 シングルテナント アプリケーションの場合、この登録は簡単であり、[Azure portal][AZURE-portal] でのアプリケーションの登録時に行われるものです。

マルチテナント アプリケーションの場合、最初のアプリケーションの登録は、開発者が使用する Azure AD テナントに保存されます。 ユーザーが初めて別のテナントからこのアプリケーションにサインインすると、Azure AD により、アプリケーションで要求されるアクセス許可に同意するかどうかを尋ねられます。 同意した場合、アプリケーションを表す "*サービス プリンシパル*" と呼ばれるものがユーザーのテナントに作成され、サインインを続行できます。 また、アプリケーションに対するユーザーの同意を記録するデリゲートが、ディレクトリに作成されます。 アプリケーションのアプリケーション オブジェクトおよびサービス プリンシパル オブジェクトの詳細と、それらの関係については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト][AAD-App-SP-Objects]に関するページを参照してください。

![単一層への同意を示しています][Consent-Single-Tier]

この同意は、アプリケーションから要求されるアクセス許可によって異なります。 Microsoft ID プラットフォームでは、アプリケーション専用アクセス許可と委任アクセス許可の 2 種類がサポートされています。

* 委任アクセス許可を付与されると、アプリケーションは、サインイン済みのユーザーとして、そのユーザーが実行可能な操作の一部を行うことができます。 たとえば、アプリケーションに対し、サインイン済みユーザーのカレンダーを読み取る委任アクセス許可を付与できます。
* アプリケーション専用アクセス許可は、アプリケーションの ID に直接付与されます。 たとえば、アプリケーションに、アプリケーションにサインインしているユーザーに関係なく、テナントのユーザーの一覧を読み取るアプリケーション専用アクセス許可を付与できます。

アクセス許可には、通常のユーザーが同意できるものと、テナント管理者の同意が必要なものがあります。

ユーザーおよび管理者の同意の詳細については、「[管理者の同意ワークフローの構成](../manage-apps/configure-admin-consent-workflow.md)」を参照してください。

### <a name="admin-consent"></a>管理者の同意

アプリケーション専用アクセス許可では、常にテナント管理者の同意が必要になります。 アプリケーションがアプリケーション専用アクセス許可を要求する場合に、ユーザーがそのアプリケーションにサインインしようとすると、このユーザーは同意できないことを示すエラー メッセージが表示されます。

一部の委任アクセス許可でも、テナント管理者の同意が必要になります。 たとえば、サインイン済みユーザーとして Azure AD に書き戻しを行うアクセス許可には、テナント管理者の同意が必要です。 アプリケーション専用アクセス許可と同様に、管理者の同意が必要な委任アクセス許可を要求するアプリケーションに通常のユーザーがサインインしようとすると、アプリケーションでエラーが発生します。 アクセス許可に管理者の同意が必要かどうかは、リソースを公開した開発者により決定されており、リソースのドキュメントに記載されています。 [Microsoft Graph API][MSFT-Graph-permission-scopes] のアクセス許可に関するドキュメントには、管理者の同意が必要なアクセス許可が示されています。

アプリケーションで管理者の同意が必要なアクセス許可を使用する場合、ジェスチャ (管理者が操作を開始できるボタンやリンクなど) を設定します。 通常、この操作に対してアプリケーションから送信される要求は OAuth2/OpenID Connect 承認要求ですが、この要求には `prompt=admin_consent` クエリ文字列パラメーターも含まれています。 管理者が同意し、ユーザーのテナントにサービス プリンシパルが作成されると、以降のサインイン要求では `prompt=admin_consent` パラメーターは不要になります。 管理者は要求されたアクセス許可を許容可能と判断しているため、その時点からは、テナント内の他のユーザーが同意を求められることはありません。

テナント管理者は、通常ユーザーによるアプリケーションへの同意を無効にすることができます。 通常ユーザーによる同意が無効化された場合、テナントでアプリケーションを使用するには常に管理者の同意が必要になります。 エンド ユーザーによる同意を無効化した状態でアプリケーションをテストする場合は、[Azure portal][AZURE-portal] の **[エンタープライズ アプリケーション]** の **[[ユーザー設定]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** セクションで構成スイッチを見つけることができます。

`prompt=admin_consent` パラメーターは、管理者の同意を必要としないアクセス許可を要求するアプリケーションでも使用できます。 この方法が使用される例として、アプリケーションで、テナント管理者が一度 "サインアップ" すると、その時点から他のユーザーが同意を求められないエクスペリエンスを必要とする場合があります。

アプリケーションが管理者の同意を必要とし、管理者はサインインしたが、`prompt=admin_consent` パラメーターが送信されない場合、管理者がアプリケーションへの同意に成功すると、**自分のユーザー アカウントについてのみ** 適用されます。 通常のユーザーは、アプリケーションへのサインインも同意も実行できないままです。 この機能は、他のユーザーのアクセスを許可する前に、テナント管理者がアプリケーションを調査できるようにしたい場合に役立ちます。

### <a name="consent-and-multi-tier-applications"></a>同意と多層アプリケーション

一部のアプリケーションは多層化されており、それぞれの層が個別の Azure AD 登録で表されている場合があります。 たとえば、Web API を呼び出すネイティブ アプリケーションや、Web API を呼び出す Web アプリケーションなどです。 どちらの場合でも、クライアント (ネイティブ アプリケーションまたは Web アプリケーション) は、リソース (Web API) を呼び出すアクセス許可を要求します。 クライアントがユーザーのテナントに対する同意を得られるようにするには、アクセス許可を要求されるリソースがすべて、あらかじめユーザーのテナントに存在する必要があります。 この条件が満たされない場合、Azure AD は、最初にリソースを追加する必要があることを示すエラーを返します。

#### <a name="multiple-tiers-in-a-single-tenant"></a>1 つのテナント内の複数の階層

この処理は、論理アプリケーションが 2 つ以上のアプリケーション登録 (別々のクライアントとリソースなど) で構成されている場合に問題になる可能性があります。 まずユーザーのテナントにリソースを追加するにはどうすればいいのでしょうか。 Azure AD では、ワンステップでクライアントとリソースが同意されるようにすることによって、この状況に対応します。 ユーザーには、同意ページにクライアントとリソースの両方によって要求されたアクセス許可の合計が表示されます。 この動作を有効にするには、リソースのアプリケーション登録で、[アプリケーションのマニフェスト][AAD-App-Manifest]に `knownClientApplications` というクライアントのアプリ ID を含める必要があります。 次に例を示します。

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

この方法については、この記事の末尾の「[関連コンテンツ](#related-content)」セクションにある多層ネイティブ クライアントによる Web API 呼び出しのサンプルを参照してください。 次の図は、1 つのテナントに登録されている多層アプリケーションのための同意の概要を示しています。

![多層の既知のクライアント アプリへの同意を示しています][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>複数のテナント内の複数の階層

同様のケースは、アプリケーションの各層を別々のテナントに登録する場合にも起こります。 たとえば、Exchange Online API を呼び出すネイティブ クライアント アプリケーションを構築する場合を考えます。 ネイティブ アプリケーションを開発するため、また開発後にユーザーのテナントでこのネイティブ アプリケーションを実行するために、Exchange Online のサービス プリンシパルが存在する必要があります。 この場合、開発者とユーザーは、テナントでサービス プリンシパルを作成するために、Exchange Online を購入する必要があります。

API が Microsoft 以外の組織によって作成されている場合、この API の開発者は、ユーザーがユーザーのテナントでアプリケーションに対して同意する手段を提供する必要があります。 推奨される設計は、サード パーティー開発者向けに、サインアップを実装する Web クライアントとしても機能できるような API を構築することです。 これを行うには、次の手順を実行します。

1. 前述のセクションに従って、API がマルチテナント アプリケーションの登録およびコード要件を実装していることを確認します。
2. API のスコープとロールの公開に加えて、登録に、"サインインとユーザー プロファイルの読み取り" アクセス許可 (既定で提供) が含まれていることを確認します。
3. Web クライアントでサインイン/サインアップ ページを実装し、[管理者の同意](#admin-consent)のガイダンスに従います。
4. ユーザーがアプリケーションに同意し、テナントにサービス プリンシパルと同意の委任のリンクが作成されたら、ネイティブ アプリケーションで API のトークンを取得できます。

次の図に、異なるテナントに登録されている多層アプリケーションの同意の概要を示します。

![多層の複数のアプリへの同意を示しています][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>同意の取り消し

ユーザーおよび管理者は、次の方法により、いつでもアプリケーションに対する同意を取り消すことができます。

* ユーザーは、[[アクセス パネル アプリケーション]][AAD-Access-Panel] リストから個々のアプリケーションを削除することで、アプリケーションへのアクセス許可を取り消します。
* 管理者は、[Azure portal][AZURE-portal] の [[エンタープライズ アプリケーション]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) セクションを使用してアプリケーションを削除することで、アプリケーションへのアクセス許可を取り消します。

管理者が、テナント内のすべてのユーザーについてアプリケーションに対して同意した場合、ユーザーは個別にアクセス許可を取り消すことはできません。 アクセス許可を取り消すことができるのは管理者のみであり、取り消しの対象はすべてのアプリケーションのみになります。

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>マルチテナント アプリケーションとアクセス トークンのキャッシュ

マルチテナント アプリケーションでは、Azure AD で保護されている API を呼び出すアクセス トークンを取得することもできます。 マルチテナント アプリケーションで Active Directory 認証ライブラリ (MSAL) を使用する際によくあるエラーは、最初は /common を使用してユーザーのトークンを要求し、応答を受信してから、その後も /common を使用して同じユーザーのトークンを要求することです。 Azure AD からの応答は /common ではなくテナントから送信されるため、MSAL ではトークンがテナントから送信されたものとしてキャッシュされます。 ユーザーのアクセス トークンを取得するためのその後の /common への呼び出しでは、キャッシュ エントリが見つからないため、ユーザーはもう一度サインインするように求められます。 キャッシュが見つからない問題を回避するために、サインイン済みのユーザーに対する以降の呼び出しは、テナントのエンドポイントに向けて行われるようにしてください。

## <a name="related-content"></a>関連コンテンツ

* [マルチテナント アプリケーションのサンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [アプリケーションのブランド化ガイドライン][AAD-App-Branding]
* [アプリケーション オブジェクトとサービス プリンシパル オブジェクト][AAD-App-SP-Objects]
* [Azure Active Directory とアプリケーションの統合][AAD-Integrating-Apps]
* [同意フレームワークの概要][AAD-Consent-Overview]
* [Microsoft Graph API のアクセス許可のスコープ][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>次のステップ

この記事では、任意の Azure AD テナントからユーザーをサインインさせることのできるアプリケーションを構築する方法を説明しました。 アプリと Azure AD の間でのシングル サインオン (SSO) を有効にした後、Microsoft 365 のように、アプリケーションを更新して Microsoft リソースによって公開される API にアクセスすることもできます。 そのため、パーソナライズされたエクスペリエンスをアプリケーションに提供できます。たとえば、プロファイル画像や次の予定などのコンテキスト情報をユーザーに表示できます。

Azure AD や、Exchange、SharePoint、OneDrive、OneNote などの Microsoft 365 サービスへの API 呼び出しを行う方法の詳細については、[Microsoft Graph API][MSFT-Graph-overview] に関するページを参照してください。

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken