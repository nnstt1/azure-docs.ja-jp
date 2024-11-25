---
title: ID プロバイダーのアクセス トークンをアプリに渡す
titleSuffix: Azure AD B2C
description: OAuth 2.0 ID プロバイダーのアクセス トークンを Azure Active Directory B2C のユーザー フローで要求として渡す方法について説明します。
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
ms.openlocfilehash: 73a4b909e5f6e54923aebd2ddec65f9ce80575b2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130217527"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で ID プロバイダーのアクセス トークンをアプリケーションに渡す

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) の[ユーザー フロー](user-flow-overview.md)は、アプリケーションのユーザーに、ID プロバイダーを使用してサインアップまたはサインインする機会を提供します。 ジャーニーが開始されると、Azure AD B2C は ID プロバイダーから[アクセス トークン](tokens-overview.md)を受け取ります。 Azure AD B2C はそのトークンを使用して、そのユーザーに関する情報を取得します。 ユーザー フローで要求を有効にして、Azure AD B2C に登録するアプリケーションにそのトークンを渡します。

::: zone pivot="b2c-user-flow"

Azure AD B2C では、[Facebook](identity-provider-facebook.md) や [Google](identity-provider-google.md) などの [OAuth 2.0](add-identity-provider.md) ID プロバイダーのアクセス トークンを渡すことがサポートされています。 その他すべての ID プロバイダーについては、要求が空で返されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C では、[OAuth 2.0](authorization-code-flow.md) および [OpenID Connect](openid-connect.md) ID プロバイダーのアクセス トークンを渡すことがサポートされています。 その他すべての ID プロバイダーについては、要求が空で返されます。

::: zone-end

次の図は、ID プロバイダーのトークンがどのようにアプリに返されるかを示しています。 

![ID プロバイダーのパススルー フロー](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>要求を有効にする

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー (ポリシー)]** を選択し、ご自身のユーザー フローを選択します。 たとえば、**B2C_1_signupsignin1** などです。
1. **[アプリケーション クレーム]** を選択します。
1. **[ID プロバイダーのアクセス トークン]** 要求を有効にします。

    ![[ID プロバイダーのアクセス トークン] 要求を有効にする](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

1. **[保存]** をクリックしてユーザー フローを保存します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すと便利なことがあります。

1. ユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 以下の例でトークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
1. **[ユーザー フローを実行します]** をクリックし、その後ご自身のアカウントの資格情報でサインインします。 ID プロバイダーのアクセス トークンは **idp_access_token** 要求に表示されます。

    次の例のようなコードが表示されます。

    ![Idp_access_token block が強調表示されている jwt.ms 内のデコードされたトークン](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>要求の要素を追加する

1. ご自身の *TrustframeworkExtensions.xml* ファイルを開き、識別子が `identityProviderAccessToken` の次の **ClaimType** 要素を、**ClaimsSchema** 要素に追加します。

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. アクセス トークンを必要とする OAuth 2.0 ID プロバイダーごとに、**OutputClaim** 要素を **TechnicalProfile** 要素に追加します。 次の例は、Facebook の技術プロファイルに追加された要素を示します。

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. *TrustframeworkExtensions.xml* ファイルを保存します。
1. ご自身の証明書利用者のポリシー ファイル (*SignUpOrSignIn.xml* など) を開き、**TechnicalProfile** に **OutputClaim** 要素を追加します。

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

1. ポリシー ファイルを保存します。

## <a name="test-your-policy"></a>ポリシーのテスト

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すことができると便利なことがあります。

### <a name="upload-the-files"></a>ファイルのアップロード

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. [カスタム ポリシー] ページで、 **[ポリシーのアップロード]** をクリックします。
1. **[ポリシーが存在する場合は上書きする]** を選択し、*TrustframeworkExtensions.xml* ファイルを検索して選択します。
1. **[アップロード]** を選択します。
1. その証明書利用者ファイル (*SignUpOrSignIn.xml* など) で、手順 5 から 7 を繰り返します。

### <a name="run-the-policy"></a>ポリシーを実行する

1. 変更したポリシーを開きます。 たとえば、*B2C_1A_signup_signin* などです。
1. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 以下の例でトークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
1. **[今すぐ実行]** を選択します。

    次の例のようなコードが表示されます。

    ![Idp_access_token block が強調表示されている jwt.ms 内のデコードされたトークン](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>次のステップ

[Azure AD B2C トークンの概要](tokens-overview.md)に関する記事で詳細を参照してください。
