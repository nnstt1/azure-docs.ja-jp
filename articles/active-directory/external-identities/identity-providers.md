---
title: 外部 ID に対する ID プロバイダー - Azure AD
description: Azure AD を外部ユーザーと共有するための既定の ID プロバイダーとして使用する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca0592741018f054640b1d695f643695e4782518
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222134"
---
# <a name="identity-providers-for-external-identities"></a>外部 ID に対する ID プロバイダー

"*ID プロバイダー*" では、ID 情報の作成、保守、管理、およびアプリケーションへの認証サービスの提供が行われます。 アプリとリソースを外部ユーザーと共有する場合は、Azure AD が共有のための既定の ID プロバイダーです。 つまり、Azure AD アカウントまたは Microsoft アカウントを既に持っている外部ユーザーを招待すると、そのユーザーは自分でそれ以上構成を行わなくても自動的にサインインできます。

外部 ID にはさまざまな ID プロバイダーが用意されています。

- **Azure Active Directory アカウント**: ゲスト ユーザーは、Azure AD の職場または学校アカウントを使用して、B2B コラボレーションの招待を利用したり、サインアップ ユーザー フローを完了したりできます。 [Azure Active Directory](azure-ad-account.md) は、既定で許可されている ID プロバイダーの 1 つです。 この ID プロバイダーをユーザー フローで使用できるようにするために必要な追加の構成はありません。

- **Microsoft アカウント**: ゲスト ユーザーは、自分自身の個人用 Microsoft アカウント (MSA) を使用して、B2B コラボレーションの招待を引き換えることができます。 セルフサービス サインアップのユーザー フローを設定するときには、許可される ID プロバイダーの 1 つとして [Microsoft アカウント](microsoft-account.md) を追加できます。 この ID プロバイダーをユーザー フローで使用できるようにするために必要な追加の構成はありません。

- **電子メール ワンタイム パスコード**: ゲスト ユーザーは、招待を引き換えるとき、または共有リソースにアクセスするときに、自分の電子メール アドレスに送信される一時的なコードを要求できます。 その後は、このコードを入力してサインインを続けます。 電子メール ワンタイム パスコード機能では、B2B ゲスト ユーザーが他の手段を使用して認証できないときにユーザーを認証します。 セルフサービス サインアップのユーザー フローを設定するときには、許可される ID プロバイダーの 1 つとして **電子メール ワンタイム パスコード** を追加できます。 いくつかの設定が必要になります。「[電子メール ワンタイム パスコード認証](one-time-passcode.md)」を参照してください。

- **Google**: Google フェデレーションでは、外部ユーザーが自分の Gmail アカウントでアプリにサインインすることにより、あなたからの招待を利用することができます。 Google フェデレーションは、セルフサービスのサインアップ ユーザー フローでも使用できます。 [ID プロバイダーとして Google を追加する](google-federation.md)方法に関するページを参照してください。
   > [!IMPORTANT]
   >
   > - **2021 年 7 月 12 日以降**、Azure AD の B2B のお客様が、カスタムまたは基幹業務アプリケーションのセルフサービス サインアップで使用するために新しい Google の統合をセットアップした場合、認証がシステム Web ビューに移動されるまで、Google ID を使用した認証が機能しなくなります。 [詳細については、こちらを参照してください](google-federation.md#deprecation-of-web-view-sign-in-support)。
   > - **2021 年の 9 月 30 日より**、Google は [埋め込みの Web ビューのサインイン サポートを廃止](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)します。 自分のアプリで埋め込みの Web ビューを使用してユーザーを認証していて、Google フェデレーションを [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md)、Azure AD B2B [(外部ユーザーの招待用)](google-federation.md)、またはセルフサービス サインアップで使用している場合、Google Gmail ユーザーが認証されなくなります。 [詳細については、こちらを参照してください](google-federation.md#deprecation-of-web-view-sign-in-support)。


- **Facebook**: アプリを構築するときに、セルフサービス サインアップを構成し、Facebook フェデレーションを有効にすることで、ユーザーが自分の Facebook アカウントを使用してアプリにサインアップできるようにすることが可能です。 Facebook は、セルフサービス サインアップ ユーザー フローにのみ使用でき、ユーザーがあなたからの招待を利用するときにサインイン オプションとして使用することはできません。 [ID プロバイダーとして Facebook を追加する](facebook-federation.md)方法に関するページを参照してください。

- **SAML/WS-Fed ID プロバイダー フェデレーション**: SAML または WS-Fed プロトコルをサポートする任意の外部 ID プロバイダーとのフェデレーションを設定することもできます。 SAML/WS-Fed IdP フェデレーションでは、外部ユーザーが自分の既存のソーシャル アカウントまたはエンタープライズ アカウントでアプリにサインインすることにより、あなたからの招待を利用することができます。 [SAML/WS-Fed IdP フェデレーションを設定する](direct-federation.md)方法を参照してください。
   > [!NOTE]
   > フェデレーション SAML/WS-Fed IdP は、セルフサービス サインアップ ユーザー フローでは使用できません。

## <a name="adding-social-identity-providers"></a>ソーシャル ID プロバイダーの追加

Azure AD はセルフサービス サインアップが既定で有効になっているため、ユーザーは常に Azure AD アカウントを使用してサインアップすることができます。 ただし、Google や Facebook のようなソーシャル ID プロバイダーを含め、その他の ID プロバイダーを有効にできます。 Azure AD テナントでソーシャル ID プロバイダーを設定するには、その ID プロバイダーでアプリケーションを作成し、資格情報を構成します。 クライアントまたはアプリの ID と、クライアントまたはアプリのシークレットを取得して、Azure AD テナントに追加できます。

Azure AD テナントに ID プロバイダーを追加した後、次のようにします。

- 組織内のアプリまたはリソースに外部ユーザーを招待すると、外部ユーザーはその ID プロバイダーでの自分のアカウントを使用してサインインできるようになります。
- アプリに対して[セルフサービス サインアップ](self-service-sign-up-overview.md)を有効にすると、外部ユーザーは、追加した ID プロバイダーでの自分のアカウントを使用して、アプリにサインアップできます。 サインアップ ページで使用できるようにしたソーシャル ID プロバイダーのオプションから選択できるようになります。

   ![Google と Facebook のオプションが表示されたサインイン画面のスクリーンショット](media/identity-providers/sign-in-with-social-identity.png)

最適なサインイン エクスペリエンスにするには、可能な限り ID プロバイダーとフェデレーションします。これにより、招待されたゲストがアプリにアクセスしたときに、シームレスなサインイン エクスペリエンスを提供できるようになります。  

## <a name="next-steps"></a>次のステップ

アプリケーションへのサインイン用に ID プロバイダーを追加する方法については、次の記事を参照してください。

- [電子メール ワンタイム パスコード認証を追加する](one-time-passcode.md)
- 許可されるソーシャル ID プロバイダーとして [Google を追加する](google-federation.md)
- 許可されるソーシャル ID プロバイダーとして [Facebook を追加する](facebook-federation.md)
- ID プロバイダーが SAML 2.0 または WS-Fed プロトコルをサポートしている組織との [SAML/WS-Fed IdP フェデレーションを設定する](direct-federation.md)。 セルフサービス サインアップのユーザー フローでは SAML/WS-Fed IdP フェデレーションを使用できないことにご注意ください。
