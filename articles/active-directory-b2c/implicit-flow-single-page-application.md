---
title: 暗黙的フローを使用したシングルページ サインイン
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で OAuth 2.0 暗黙的フローを使用して、シングルページ サインインを追加する方法を学習します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 9003d6eb0d0f9ebc364ee84c307e85c32357c6d7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038690"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での OAuth 2.0 暗黙的フローを使用したシングルページ サインイン

最新のアプリケーションの多くには、主に JavaScript で記述されたシングル ページ アプリ (SPA) のフロントエンドがあります。 アプリが、React、Angular、Vue.js などのフレームワークを使用して記述されていることもよくあります。 主にブラウザーで実行される、SPA やその他の JavaScript アプリには、認証に関していくつかの追加の課題があります。

- これらのアプリのセキュリティ特性は、従来のサーバーベースの Web アプリケーションとは異なります。
- 多くの承認サーバーや ID プロバイダーでは、クロス オリジン リソース共有 (CORS) 要求をサポートしていません。
- アプリからブラウザーにフル ページがリダイレクトされると、ユーザー エクスペリエンスに悪影響が及ぶ場合があります。

SPA のサポートには、[OAuth 2.0 認証コード フロー (PKCE あり)](./authorization-code-flow.md) の方法が推奨されます。

[MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) のような一部のフレームワークでは、暗黙的な許可フローのみがサポートされます。 このような場合、Azure Active Directory B2C (Azure AD B2C) では、OAuth 2.0 認可の暗黙的な許可フローがサポートされます。 このフローは [OAuth 2.0 の仕様のセクション 4.2](https://tools.ietf.org/html/rfc6749) で説明されています。 暗黙的フローでは、アプリは Azure Active Directory (Azure AD) 承認エンドポイントから直接トークンを受け取るため、サーバー間の交換は実行されません。 すべての認証ロジックとセッション処理は、ページ リダイレクトまたはポップアップ ボックスを使って、JavaScript クライアント内ですべて行われます。

Azure AD B2C によって、標準の OAuth 2.0 暗黙的フローが、単純な認証と承認以上まで拡張されます。 Azure AD B2C には、[ポリシー パラメーター](user-flow-overview.md)が導入されています。 ポリシー パラメーターと共に OAuth 2.0 を使用して、サインアップ、サインイン、プロファイル管理のユーザー フローなどのポリシーをアプリに追加できます。 この記事の HTTP 要求例では、**{tenant}.onmicrosoft.com** を例として使用します。 実際のテナントがあり、ユーザー フローも作成済みである場合は、`{tenant}` を実際のテナントの名前に置き換えてください。

暗黙的サインイン フローは、次の図のようになっています。 各手順については、この記事の後の方で詳しく説明します。

![OpenID Connect の暗黙的なフローを示すスイムレーン スタイルの図](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>認証要求を送信する

ご利用の Web アプリケーションでユーザーを認証し、ユーザー フローを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせることができます。 ユーザーはユーザー フローに応じてアクションを実行します。

この要求では、クライアントによって、ユーザーから取得する必要があるアクセス許可が `scope` パラメーターで示され、実行するユーザー フローが示されます。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。 `{tenant}`を Azure AD B2C テナントの名前に置き換えます。 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` を、テナントに登録済みのアプリケーションのアプリ ID に置き換えます。 `{policy}` を、テナント内に作成したポリシーの名前に置き換えます (例: `b2c_1_sign_in`)。

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
|{tenant}| はい | Azure AD B2C テナントの名前。|
|{policy}| はい| 実行するユーザー フロー。 Azure AD B2C テナントに作成したユーザー フローの名前を指定します。 例: `b2c_1_sign_in`、`b2c_1_sign_up`、または`b2c_1_edit_profile`。 |
| client_id | はい | [Azure portal](https://portal.azure.com/) によってアプリケーションに割り当てられたアプリケーション ID。 |
| response_type | はい | OpenID Connect サインインでは、 `id_token` を指定する必要があります。 応答の種類として `token` を含めることもできます。 `token` を使用する場合、アプリは承認エンドポイントへ 2 度目の要求を行うことなく、すぐに承認エンドポイントからアクセス トークンを受け取ることができます。  応答の種類 `token` を使用する場合は、`scope` パラメーターに、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri | いいえ | アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかのリダイレクト URI と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| response_mode | いいえ | 結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  暗黙的フローの場合は `fragment` を使用します。 |
| scope | はい | スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、ID トークンの形式でユーザーに関するデータを取得するためのアクセス許可を示します。 Web アプリの場合、 `offline_access` スコープは任意です。 これは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。 |
| state | いいえ | 要求に含まれ、トークンの応答でも返される値。 使用したい任意の内容の文字列を指定できます。 通常、クロスサイト リクエスト フォージェリ攻撃を防ぐために、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce | はい | 要求に追加する (アプリによって生成された) 値。この値が、最終的な ID トークンに要求として追加されます。 アプリはこの値を確認することにより、トークン リプレイ攻撃を緩和できます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| prompt | いいえ | 必要とされている、ユーザーとの対話の種類。 現在有用な値は `login` のみです。 このパラメーターによってユーザーは、その要求で資格情報の入力を強制されます。 シングル サインオンは作用しません。 |

この時点で、ユーザーはポリシーのワークフローを完了するよう求められます。 ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりするなど、いくつかの手順が必要なことがあります。 ユーザー アクションは、ユーザー フローがどのように定義されているかによって異なります。

ユーザーがユーザー フローを完了すると、Azure AD B2C はユーザーが `redirect_uri` に使用した値でアプリに応答を返します。 これには、`response_mode` パラメーターで指定されたメソッドが使用されます。 応答は、実行されたユーザー フローとは無関係に、ユーザー アクションの各シナリオでまったく同じになります。

### <a name="successful-response"></a>成功応答
`response_mode=fragment` と `response_type=id_token+token` を使用している成功応答は、次のようになります (読みやすいように改行してあります)。

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| パラメーター | 説明 |
| --------- | ----------- |
| access_token | アプリが要求したアクセス トークン。 |
| token_type | トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires_in | アクセス トークンが有効な時間の長さ (秒単位)。 |
| scope | トークンが有効である範囲。 スコープは、後で使用できるようトークンをキャッシュするためにも使用できます。 |
| id_token | アプリが要求した ID トークン。 この ID トークンを使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 ID トークンとその内容の詳細については、「[Azure AD B2C トークン リファレンス](tokens-overview.md)」を参照してください。 |
| state | 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるように、エラー応答をリダイレクト URI に送信することもできます。

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --------- | ----------- |
| error | 発生したエラーの種類を分類するために使用されるコード。 |
| error_description | 認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |
| state | 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。|

## <a name="validate-the-id-token"></a>ID トークンの検証

ID トークンを受信してもユーザーの認証には不十分です。 ID トークンの署名を検証し、アプリの要件ごとにトークン内の要求を確認します。 Azure AD B2C は、[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

使用する言語に応じて、JWT を検証するためにさまざまなオープン ソース ライブラリを利用できます。 独自の検証ロジックを実装するより、利用できるオープンソース ライブラリを試すことを検討してください。 この記事の情報が、これらのライブラリを適切に使用する方法を学ぶ助けになります。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあります。 アプリはこのエンドポイントを使用して、実行時に Azure AD B2C に関する情報を取得できます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 Azure AD B2C テナントにはユーザー フロー別の JSON メタデータ ドキュメントがあります。 たとえば、fabrikamb2c.onmicrosoft.com テナントの b2c_1_sign_in ユーザー フローのメタデータ ドキュメントは、次の場所にあります。

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

この構成ドキュメントのプロパティの 1 つは `jwks_uri` です。 同じユーザー フローの値は次のようになります。

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

どのユーザー フローが ID トークンの署名に使用されたか (およびどこからメタデータを取得できるか) は、2 つの方法で判断できます。
-  ユーザー フロー名が `id_token` 内の `acr` 要求に含まれています。 ID トークンの要求を解析する方法については、「[Azure AD B2C トークン リファレンス](tokens-overview.md)」を参照してください。 
- 要求を発行するときに、`state` パラメーターの値に含まれるユーザー フローをエンコードします。 その後、`state` パラメーターをデコードして、どのユーザー フローが使用されたかを確認します。 どちらの方法も有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、このエンドポイントにある RSA-256 公開キーを利用し、ID トークンの署名を検証できます。 このエンドポイントには、特定の時点で、それぞれが `kid` によって識別されるキーが複数存在すると表示される場合があります。 `id_token` のヘッダーにも `kid` 要求が含まれています。 これが、これらのキーのうち、どれが ID トークンの署名に使用されたかを示しています。 [トークンの検証](tokens-overview.md)を含む詳細については、[Azure AD B2C トークン リファレンス](tokens-overview.md)を参照してください。
<!--TODO: Improve the information on this-->

ID トークンの署名の検証後に、いくつかの要求で検証が必要になります。 次に例を示します。

* トークン再生攻撃を防止するために、`nonce` 要求を検証します。 その値が、サインイン要求で指定した値と一致していることが必要です。
* ID トークンが自分のアプリのために発行されたことを確認するために、`aud` 要求を検証します。 その値がアプリのアプリケーション ID と一致している必要があります。
* ID トークンの有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証します。

他にも実行する必要があるいくつかの検証については、[OpenID Connect Core の仕様](https://openid.net/specs/openid-connect-core-1_0.html)で詳しく説明されています。シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザーまたは組織がアプリにサインアップ済みであることを確認する。
* ユーザーが適切な承認や特権を持っていることを確認する。
* Azure AD Multi-Factor Authentication の使用など、特定の強度の認証が行われたことを確認する。

ID トークンに含まれる要求の詳細については、[Azure AD B2C トークン リファレンス](tokens-overview.md)を参照してください。

ID トークンを検証した後、ユーザーとのセッションを開始できます。 アプリでは、ID トークン内の要求を使用してユーザーに関する情報を取得できます。 取得した情報は、表示、記録、承認などに利用することができます。

## <a name="get-access-tokens"></a>アクセス トークンを取得する
Web アプリで必要なのはユーザー フローを実行することだけの場合、以降のセクションの一部を省略できます。 以降のセクションの情報が当てはまるのは、認証される呼び出しを Web API に対して行う必要があり、Azure AD B2C によって保護されている Web アプリのみです。

ユーザーを SPA にサインインさせたので、Azure AD でセキュリティ保護されている Web API を呼び出すアクセス トークンを取得できます。 応答の種類として `token` を使用して既にトークンを取得済みの場合でも、このメソッドを使用してその他のリソースのトークンを取得できます。再度のサインインのためにユーザーをリダイレクトする必要はありません。

一般的な Web アプリ フローでは、`/token` エンドポイントに対して要求を行います。 ただし、エンドポイントでは CORS 要求はサポートされていないため、AJAX 呼び出しを行って更新トークンを取得することはできません。 代わりに、非表示の HTML iframe 要素で暗黙的フローを使用して、他の Web API 用の新しいトークンを取得できます。 次に例を示します (読みやすいように改行してあります)。

```http
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
|{tenant}| 必須 | Azure AD B2C テナントの名前。|
{policy}| 必須| 実行するユーザー フロー。 Azure AD B2C テナントに作成したユーザー フローの名前を指定します。 例: `b2c_1_sign_in`、`b2c_1_sign_up`、または`b2c_1_edit_profile`。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com) でアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。  応答の種類として `token` を含めることもできます。 ここで `token` を使用する場合、アプリは承認エンドポイントへ 2 度目の要求を行うことなく、すぐに承認エンドポイントからアクセス トークンを受け取ることができます。 応答の種類 `token` を使用する場合は、`scope` パラメーターに、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri |推奨 |アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかのリダイレクト URI と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。  トークンを取得するには、意図したリソースに必要なすべてのスコープを含めます。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 暗黙的フローの場合は `fragment` を使用します。 他の 2 つのモード (`query` と `form_post`) を指定することはできますが、暗黙的フローでは機能しません。 |
| state |推奨 |要求に含まれ、トークンの応答として返される値。  使用したい任意の内容の文字列を指定できます。  通常、クロスサイト リクエスト フォージェリ攻撃を防ぐために、ランダムに生成された一意の値が使用されます。  この状態は、認証要求の前にアプリ内のユーザーの状態に関する情報をエンコードする目的にも使用されます。 たとえば、ユーザーがいるページまたはビューです。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な ID トークンに要求として追加されます。  アプリはこの値を確認することにより、トークン リプレイ攻撃を緩和できます。 通常この値は、要求の送信元を特定する、ランダム化された一意の文字列です。 |
| prompt |必須 |非表示の iframe のトークンを更新および取得するには、`prompt=none` を使用して、iframe がサインイン ページに停滞せずにすぐに応答できるようにします。 |
| login_hint |必須 |非表示の iframe のトークンを更新および取得するには、ある時点でそのユーザーが持っている可能性がある複数のセッションを区別できるように、このヒントにそのユーザーのユーザー名を含めます。 `preferred_username` 要求を使って、以前のサインインからユーザー名を抽出できます (`preferred_username` 要求を受け取るには、`profile` スコープが必要です)。 |
| domain_hint |必須 |`consumers` または `organizations` を指定できます。  非表示の iframe のトークンを更新および取得するには、要求に `domain_hint` 値を含めます。  使用する値を決定するには、以前のサインイン時の ID トークンから `tid` 要求を抽出します (`tid` 要求を受け取るには、`profile` スコープが必要です)。 `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用します。  それ以外の場合は、 `domain_hint=organizations`を指定します。 |

`prompt=none` パラメーターを設定することによって、この要求はすぐに成功または失敗のいずれかになり、アプリケーションに戻ります。  成功すると、`response_mode` パラメーターで指定された方法を使用して、指定されたリダイレクト URI でアプリに応答が送信されます。

### <a name="successful-response"></a>成功応答
`response_mode=fragment` を使用した場合の成功応答は次の例のようになります。

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| パラメーター | 説明 |
| --- | --- |
| access_token |アプリが要求したトークン。 |
| token_type |トークンの種類は常にベアラーになります。 |
| state |要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| scope |アクセス トークンが有効である範囲。 |

### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるように、エラー応答をリダイレクト URI に送信することもできます。  `prompt=none` の場合、予期されるエラーは次の例のようになります。

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類するために使用できるエラー コード文字列。 この文字列はエラーへの対応に利用することもできます。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。

## <a name="refresh-tokens"></a>更新トークン
ID トークンとアクセス トークンは、どちらも短時間で期限切れになります。 これらのトークンを定期的に更新するようにアプリを準備する必要があります。 暗黙的フローでは、セキュリティ上の理由から、更新トークンを取得できません。 いずれかの種類のトークンを更新するには、非表示の HTML iframe 要素で暗黙的フローを使用します。 承認要求には、パラメーター `prompt=none` を含めます。 新しい id_token 値を受け取るには、必ず `response_type=id_token`、`scope=openid`、および `nonce` パラメーターを使用します。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
ユーザーをアプリからサインアウトさせる場合は、サインアウトする Azure AD にユーザーをリダイレクトします。ユーザーをリダイレクトさせなかった場合、そのユーザーは、資格情報を再入力しなくてもアプリに対する再認証を行うことができてしまいます。Azure AD とのシングル サインオン セッションが有効であるためです。

ユーザーを単純に、「[ID トークンの検証](#validate-the-id-token)」で説明したのと同じ OpenID Connect メタデータ ドキュメントに列挙されている `end_session_endpoint` にリダイレクトすることができます。 次に例を示します。

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| パラメーター | 必須 | 説明 |
| --------- | -------- | ----------- |
| {tenant} | はい | Azure AD B2C テナントの名前。 |
| {policy} | はい | ご利用のアプリケーションからユーザーをサインアウトさせるために使用するユーザー フロー。 |
| post_logout_redirect_uri | いいえ | サインアウトの正常終了後にユーザーをリダイレクトする URL。これが含まれていない場合、Azure AD B2C では、ユーザーに対して一般的なメッセージが表示されます。 |
| state | いいえ | 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリケーションでは、要求と応答の `state` 値が同一であることを検証する必要があります。 |


> [!NOTE]
> ユーザーを `end_session_endpoint` にリダイレクトすると、ユーザーの Azure AD B2C でのシングル サインオン状態の一部が消去されます。 ただしそれによって、そのユーザーが自分のソーシャル ID プロバイダー セッションからサインアウトされることはありません。 ユーザーがその後のサインインで同じ ID プロバイダーを選択した場合は、そのユーザーは資格情報を入力しなくても再認証されます。 ユーザーが Azure AD B2C アプリケーションからサインアウトする場合、たとえば、Facebook アカウントから完全なサインアウトするのを望んでいるとは限りません。 ただしローカル アカウントについては、そのユーザーのセッションは適切に終了されます。
>

## <a name="next-steps"></a>次の手順

[Azure AD B2C での JavaScript SPA へのサインイン](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-core-samples/VanillaJSTestApp/app/b2c)のサンプル コードをご覧ください。
