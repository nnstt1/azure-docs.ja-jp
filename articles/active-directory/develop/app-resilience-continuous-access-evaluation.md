---
title: 継続的アクセス評価が有効になった API をアプリケーションで使用する方法 | Azure
titleSuffix: Microsoft identity platform
description: 重大なイベントとポリシー評価に基づいて有効期間の長いアクセス トークンを取り消すことができるように継続的アクセス評価のサポートを追加することで、アプリのセキュリティと復元性を強化する方法。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/09/2021
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 816975db5ee6fd613e077dd7d268825c6601b3fe
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444447"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>継続的アクセス評価が有効になった API をアプリケーションで使用する方法

[継続的アクセス評価](../conditional-access/concept-continuous-access-evaluation.md) (CAE) は、トークンの有効期間ではなく[重要なイベント](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation)と[ポリシー評価](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)に基づいてアクセス トークンを破棄できる、Azure AD の機能です。 一部のリソース API については、リスクとポリシーがリアルタイムで評価されるため、トークンの有効期間を最大 28 時間まで延長することができます。 このような有効期間が長いトークンは Microsoft Authentication Library (MSAL) によって事前に更新されるため、アプリケーションの復元性が向上します。

この記事では、CAE 対応 API をアプリケーションで使用する方法について説明します。 MSAL を使用していないアプリケーションでは、CAE を使用するための [要求のチャレンジ、要求要求、およびクライアント機能](claims-challenge.md) のサポートを追加できます。

## <a name="implementation-considerations"></a>実装時の注意事項

継続的アクセス評価を使用するには、アプリもそれがアクセスするリソース API も CAE 対応であることが必要です。 ただし、CAE 対応のリソースを使用するようにコードを準備しても、CAE 対応でない API を使用できなくなるわけではありません。

リソース API によって CAE が実装されていて、CAE を処理できることがご利用のアプリケーションで宣言されている場合、アプリには、そのリソースに対して CAE トークンが与えられます。 このため、CAE に対応する準備がアプリでできていることを宣言する場合、アプリケーションは、Microsoft Identity アクセス トークンを受け入れるすべてのリソース API の CAE 要求チャレンジを処理する必要があります。 これらの API 呼び出し内で CAE 応答を処理しない場合、トークンが、返された有効期間内にまだあるものの CAE のために取り消されている状態では、API 呼び出しを再試行するループにアプリが陥る可能性があります。

## <a name="the-code"></a>コード

最初の手順として、CAE のために呼び出しを拒否したリソース API からの応答を処理するコードを追加します。 CAE を使用すると、アクセス トークンが取り消されたとき、または使用している IP アドレスの変更が API で検出されたときに、401 状態と WWW-Authenticate ヘッダーが API から返されます。 この WWW-Authenticate には、アプリケーションが新しいアクセス トークンを取得するために使用できる要求チャレンジが含まれています。

次に例を示します。

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

アプリによって次のことが生じていないか確認されます。

- 401 状態を返す API 呼び出し
- 次の内容を含む WWW-Authenticate ヘッダーが存在すること
  - "insufficient_claims" という値を含む "error" パラメーター
  - "claims" パラメーター

これらの条件が満たされると、アプリは MSAL.NET `WwwAuthenticateParameters` クラスを使用してクレーム チャレンジを抽出およびデコードできます。

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        string claimChallenge = WwwAuthenticateParameters.GetClaimChallengeFromResponseHeaders(APIresponse.Headers);
```

そして、アプリでは要求チャレンジを使用して、リソースに対する新しいアクセス トークンを取得します。

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

CAE 対応のリソースから返された要求チャレンジを処理する準備がアプリケーションでできたら、そのアプリが CAE に対応可能な状態にあることを Microsoft Identity に伝えることができます。 これをお使いの MSAL アプリケーションで行うには、"cp1" のクライアント機能を使用してパブリック クライアントを構築します。

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

ユーザーをアプリケーションにサインインさせてから、Azure portal を使用してユーザーのセッションを取り消すことで、アプリケーションをテストできます。 CAE 対応の API が次にアプリで呼び出されたとき、ユーザーは再認証を行うように求められます。

## <a name="next-steps"></a>次の手順

- [継続的アクセス評価](../conditional-access/concept-continuous-access-evaluation.md)の概念の概要
- [要求のチャレンジ、クレーム要求、およびクライアントの能力](claims-challenge.md)
