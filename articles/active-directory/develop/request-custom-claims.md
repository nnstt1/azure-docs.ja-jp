---
title: カスタム要求を要求する (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: カスタム要求の方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 70a883fc1c2cd78124c9efe44a54cc528ee5426c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039920"
---
# <a name="request-custom-claims-using-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL を使用してカスタム要求を行う

OpenID Connect では、必要に応じて、UserInfo エンドポイントや ID トークンから個々の要求を返すように求めることができます。 要求は、行われた要求のリストを含む JSON オブジェクトとして表されます。 詳細については、「[OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)」を参照してください。

iOS および macOS 用の Microsoft Authentication Library (MSAL) を使用すると、対話型とサイレントの両方のトークン取得シナリオで特定の要求を行うことができます。 これを行うには、`claimsRequest` パラメーターを使用します。

状況に応じた複数のシナリオがあります。 次に例を示します。

- アプリケーションの標準のセット以外で要求を行っている。
- アプリケーションのスコープを使用して指定できない標準の要求の特定の組み合わせを要求している。 たとえば、要求が見つからないためにアクセス トークンが拒否された場合、アプリケーションは、MSAL を使用して見つからない要求を求めることができます。

> [!NOTE]
> 要求が指定されている場合、MSAL は常にアクセス トークン キャッシュをバイパスします。 追加の要求が必要とされる場合は、`claimsRequest` パラメーターのみを指定してください (それぞれの MSAL API 呼び出しで常に同じ `claimsRequest` パラメーターを指定するのではありません)。

`claimsRequest` は `MSALSilentTokenParameters` および `MSALInteractiveTokenParameters` で指定できます。

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` は、JSON 要求の NSString 表現から構築できます。 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



追加で特定の要求を行うことによっても変更できます。

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



次に、トークンパラメーターに `MSALClaimsRequest` を設定し、MSAL トークン取得 API のいずれかに提供する必要があります。

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>次のステップ

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します
