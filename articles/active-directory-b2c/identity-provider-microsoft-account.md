---
title: Microsoft アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Microsoft アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5da695eeee6ef9ed26dcdbd29cf5b5684e277b12
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130227753"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Microsoft アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>[前提条件]

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>アプリケーションのパブリッシャー ドメインを検証する
2020 年 11 月現在、新しいアプリケーション登録は、[アプリケーションの発行元ドメインが検証済み](../active-directory/develop/howto-configure-publisher-domain.md)***かつ*** 会社の ID が Microsoft Partner Network で検証され、アプリケーションに関連付けられている場合を除き、ユーザーの同意プロンプトで未検証として表示されます。 (この変更の詳細については[こちら](../active-directory/develop/publisher-verification-overview.md)を参照してください)。Azure AD B2C ユーザー フローの場合、発行元のドメインは、[Microsoft アカウント](../active-directory-b2c/identity-provider-azure-ad-single-tenant.md)またはその他の Azure AD テナントを ID プロバイダーとして使用する場合にのみ表示されることに注意してください。 これらの新しい要件を満たすには、次の手順を実行します。

1. [自分の Microsoft Partner Network (MPN) アカウントを使用して会社 ID を確認します](/partner-center/verification-responses)。 このプロセスにより、会社と会社の主要連絡先に関する情報が検証されます。
1. 発行元の確認プロセスを完了し、次のいずれかのオプションを使用して、MPN アカウントをアプリ登録に関連付けます。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD テナント内にある場合は、[アプリ登録ポータルでアプリを検証](../active-directory/develop/mark-app-as-publisher-verified.md)します。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD B2C テナント内にある場合は、[Microsoft Graph API を使用して発行元により検証済みとしてアプリをマーク](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)します (たとえば、Graph Explorer を使用)。 アプリの検証済み発行元を設定するための UI は、現在、Azure AD B2C テナントでは無効になっています。

## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で Microsoft アカウントを持つユーザーのサインインを有効にするには、[Azure portal](https://portal.azure.com) でアプリケーションを作成する必要があります。 詳細については、[Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。 まだ Microsoft アカウントを持っていない場合は、[https://www.live.com/](https://www.live.com/) で取得できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[スイッチ]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、 *MSAapp1* です。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)]\(任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント) と、個人用の Microsoft アカウント (Skype、Xbox など)\)** を選択します。

   アカウントの種類のその他の選択肢の詳細については、「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)」を参照してください。
1. **[リダイレクト URI (省略可能)]** で **[Web]** を選択し、`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`を入力します。 [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を実際のテナントの名前に、`your-domain-name` を実際のカスタム ドメインに置き換えます。
1. **[登録]** を選択します
1. アプリケーションの [概要] ページに表示されている **[アプリケーション (クライアント) ID]** を記録します。 このクライアント ID は、次のセクションで ID プロバイダーを構成するときに必要です。
1. **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** をクリックします
1. シークレットの **[説明]** を「*アプリケーション パスワード 1*」のように入力して、 **[追加]** をクリックします。
1. **[値]** 列に示されているアプリケーション パスワードを記録します。 このクライアント シークレットは、次のセクションで ID プロバイダーを構成するときに必要です。

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Microsoft を ID プロバイダーとして構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Microsoft アカウント]** を選択します。
1. **[名前]** を入力します。 たとえば、「*MSA*」とします。
1. **[クライアント ID]** には、前に作成した Azure AD アプリケーションのアプリ (クライアント) ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>ユーザー フローに Microsoft ID プロバイダーを追加する 

この時点では、Microsoft ID プロバイダーはセットアップされていますが、サインイン ページではまだ使用できません。 ユーザー フローに Microsoft ID プロバイダーを追加するには:

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Microsoft ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Microsoft アカウント]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Microsoft]** を選択して Microsoft アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>省略可能な要求の構成

Azure AD から `family_name` および `given_name` 要求を取得する場合は、ご利用のアプリケーションに対して省略可能な要求を Azure portal UI またはアプリケーション マニフェストで構成できます。 詳細については、[Azure AD アプリに省略可能な要求を提供する方法](../active-directory/develop/active-directory-optional-claims.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 **Azure Active Directory** を検索して選択します。
1. **[管理]** セクションで、 **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** セクションで、 **[トークンの構成 (プレビュー)]** を選択します。
1. **[省略可能な要求を追加]** を選択します。
1. 構成するトークンの型を選択します。
1. 追加する省略可能な要求を選択します。
1. **[追加]** をクリックします。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

これで、Azure AD テナント内にアプリケーションが作成されたので、そのアプリケーションのクライアント シークレットを Azure AD B2C テナント内に格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `MSASecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前のセクションで記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **Create** をクリックしてください。

## <a name="configure-microsoft-as-an-identity-provider"></a>Microsoft を ID プロバイダーとして構成する

ユーザーが Microsoft アカウントを使用してサインインできるようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できるクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ポリシー ファイルを開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **client_id** の値を、前に記録した Azure AD アプリケーションの "*アプリケーション (クライアント) ID*" に置き換えます。
1. ファイルを保存します。

これで、Azure AD B2C で Azure AD 内の Microsoft アカウント アプリケーションと通信する方法を認識できるようにポリシーが構成されました。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Microsoft]** を選択して Microsoft アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end