---
title: SendGrid を使用するカスタム メール確認
titleSuffix: Azure AD B2C
description: Azure AD B2C 対応アプリケーションを使用する目的で新規登録した顧客に送信される確認メールをカスタマイズするために、SendGrid と統合する方法について学習します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5bb6c3aef0476e3da440eb8523d0ccc09491e074
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501402"
---
# <a name="custom-email-verification-with-sendgrid"></a>SendGrid を使用するカスタム メール確認

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) でカスタム メールを使用し、自分のアプリケーションに新規登録したユーザーにカスタマイズしたメールを送信します。 サード パーティの電子メール プロバイダーである SendGrid を使用すると、独自の電子メール テンプレート、および *From:* アドレスと件名を使用でき、ローカライズとカスタムのワンタイム パスワード (OTP) 設定をサポートできます。

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

カスタム メール確認では、[SendGrid](https://sendgrid.com)、[Mailjet](https://Mailjet.com)、または [SparkPost](https://sparkpost.com) といったサードパーティの電子メール プロバイダー、カスタム REST API、あるいは任意の HTTP ベースの電子メール プロバイダー (独自のものも含む) を使用する必要があります。 この記事では、SendGrid を使用するソリューションの設定について説明します。

## <a name="create-a-sendgrid-account"></a>SendGrid アカウントの作成

まだ用意していない場合、まず、SendGrid アカウントを設定してください (Azure のお客様は毎月、25,000 の無料電子メールをロック解除できます)。 設定方法については、「[SendGrid を使用した Azure での電子メールの送信方法](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-twilio-sendgrid-accountcreate-a-twilio-sendgrid-account)」の「[SendGrid アカウントの作成](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)」セクションをご覧ください。

[SendGrid API キー](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key)を作成するセクションを必ず完了してください。 後の手順で使用するために、API キーを記録しておきます。

> [!IMPORTANT]
> SendGrid によって、共有 IP アドレスと[専用 IP アドレス](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/)から電子メールを送信する機能が顧客に提供されます。 専用 IP アドレスを使用する場合は、IP アドレスのウォームアップを使用して、独自の評判を適切に築く必要があります。 詳細については、「[IP アドレスのウォームアップ](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/)」を参照してください。

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C ポリシー キーの作成

次に、SendGrid API キーを Azure AD B2C ポリシー キーに格納し、ポリシーで参照されるようにします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** では、 **[手動]** を選びます。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `SendGridSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前に記録しておいた SendGrid API キーを入力します。
1. **[キー使用法]** には **[署名]** を選択します。
1. **［作成］** を選択します

## <a name="create-sendgrid-template"></a>SendGrid テンプレートを作成する

SendGrid アカウントを作成し、SendGrid API キーを Azure AD B2C ポリシー キーに格納したら、SendGrid [動的トランザクション テンプレート](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)を作成します。

1. SendGrid サイトで [[transactional templates]\(トランザクション テンプレート\)](https://sendgrid.com/dynamic_templates) ページを開き、 **[Create a Dynamic Template]\(動的テンプレートの作成\)** を選択します。
1. 「`Verification email`」のような一意のテンプレート名を入力し、 **[Create]\(作成\)** を選択します。
1. 新しいテンプレートの編集を開始するには、テンプレート (つまり `Verification email`) を選択して **[Add Version]\(バージョンの追加\)** を選択します。
1. **[Blank Template]\(空のテンプレート\)** を選択して、 **[Code Editor]\(コード エディター\)** を選択します。
1. HTML エディターに次の HTML テンプレートを貼り付けるか、独自のテンプレートを貼り付けます。 `{{otp}}` パラメーターと `{{email}}` パラメーターは、ワンタイム パスワード値とユーザー電子メール アドレスで動的に置換されます。

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">

                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. 左側にある **[Settings]\(設定\)** を展開し、 **[Version Name]\(バージョン名\)** にテンプレートのバージョンを入力します。 
1. **[Subject]\(件名\)** に「`{{subject}}`」と入力します。
1. ページの最上部の **[Save]\(保存\)** を選択します。
1. **[Transactional Templates]\(トランザクション テンプレート\)** に戻るには、戻る矢印を選択します。
1. 後の手順で使用するために、作成したテンプレートの **ID** を記録します。 たとえば、「 `d-989077fbba9746e89f3f6411f596fb96` 」のように入力します。 [要求変換を追加する](#add-the-claims-transformation)ときにこの ID を指定します。

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C の要求の種類を追加する

ポリシーで、`<BuildingBlocks>` 内の `<ClaimsSchema>` 要素に次の要求の種類を追加します。

これらの要求の種類は、ワンタイム パスワード (OTP) コードを利用してメール アドレスを生成し、検証するために必要です。

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>要求変換の追加

次に、SendGrid に送信される要求の本文となる JSON 文字列要求を出力する要求変換が必要になります。

JSON オブジェクトの構造は、InputClaims の InputParameters と TransformationClaimTypes というドット表記で ID により定義されます。 ドット表記の数値は配列を意味します。 値は、InputClaims の値と InputParameters の "Value" プロパティから取得されます。 JSON 要求変換の詳細については、「[JSON 要求変換](json-transformations.md)」を参照してください。

次の要求変換を `<BuildingBlocks>` 内の `<ClaimsTransformations>` 要素に追加します。 要求変換 XML を次のように更新します。

* 「[SendGrid テンプレートの作成](#create-sendgrid-template)」で先に作成した SendGrid トランザクション テンプレートの ID で `template_id` InputParameter 値を更新します。
* `from.email` アドレス値を更新します。 確認メールがスパムに指定されないように有効なメール アドレスを使用します。
   > [!NOTE]
   > このメール アドレスは、SendGrid でドメイン認証か Single Sender Authentication のいずれかを使用した Sender Authentication で検証する必要があります。
* `personalizations.0.dynamic_template_data.subject` 件名入力パラメーターの値を組織に適切な件名で更新します。

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>DataUri コンテンツ定義の追加

`<BuildingBlocks>` 内の要求変換の下で、バージョン 2.1.2 データ URI を参照するように次の [ContentDefinition](contentdefinitions.md) を追加します。

```xml
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>DisplayControl の作成

ユーザーに送信される確認コードでメール アドレスを検証する目的で確認の表示制御が使用されます。

この例の表示制御には次の動作が設定されています。

1. ユーザーからアドレス要求の種類 `email` を収集します。
1. ユーザーが自分に送信されたコードを要求の種類 `verificationCode` に与えるまで待機します。
1. この表示制御を参照するセルフアサート技術プロファイルに `email` を戻します。
1. `SendCode` アクションを利用し、OTP コードを生成し、その OTP コードが含まれるメールをユーザーに送信します。

![確認コード メールの送信アクション](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

引き続きコンテンツ定義の下の `<BuildingBlocks>` 内で、種類が [VerificationControl](display-control-verification.md) の次の [DisplayControl](display-controls.md) をポリシーに追加します。

```xml
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>OTP 技術プロファイルの追加

`GenerateOtp` 技術プロファイルによってメール アドレスのコードが生成されます。 `VerifyOtp` 技術プロファイルによって、メール アドレスに関連付けられているコードが検証されます。 ワンタイム パスワードの形式と有効期間の構成を変更できます。 OTP 技術プロファイルの詳細については、[ワンタイム パスワード技術プロファイルの定義](one-time-password-technical-profile.md)に関するページを参照してください。

> [!NOTE]
> Web.TPEngine.Providers.OneTimePasswordProtocolProvider プロトコルによって生成される OTP コードは、ブラウザー セッションに関連付けられています。 つまり、ユーザーはさまざまなブラウザー セッションで、それぞれ対応するセッションに対して有効な一意の OTP コードを生成できます。 一方、組み込みの電子メール プロバイダーで生成される OTP コードはブラウザーのセッションとは独立しているため、ブラウザーの新しいセッションで新たに OTP コードを生成すると、前の OTP コードが置き換えられます。

次の技術プロファイルを `<ClaimsProviders>` 要素に追加します。

```xml
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>REST API 技術プロファイルの追加

この REST API 技術プロファイルによってメール コンテンツが生成されます (SendGrid 形式が使用されます)。 RESTful 技術プロファイルの詳細については、[RESTful 技術プロファイルの定義](restful-technical-profile.md)に関するページを参照してください。

OTP 技術プロファイルの場合と同様に、次の技術プロファイルを `<ClaimsProviders>` 要素に追加します。

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>DisplayControl への参照を付ける

最後の手順では、作成した DisplayControl への参照を追加します。 既存の `LocalAccountSignUpWithLogonEmail` と `LocalAccountDiscoveryUsingEmailAddress` セルフアサート技術プロファイルを次のように置き換えます。 以前のバージョンの Azure AD B2C ポリシーを使用していた場合。 これらの技術プロファイルでは、DisplayControl を参照する `DisplayClaims` が使用されます。

詳細については、[セルフアサート技術プロファイル](restful-technical-profile.md)に関するページと「[DisplayControl](display-controls.md)」を参照してください。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[オプション] メールをローカライズする

メールをローカライズするには、ローカライズされた文字列を SendGrid またはご自分のメール プロバイダーに送信する必要があります。 たとえば、メールの件名、本文、ご自分のコード メッセージ、またはメールの署名をローカライズできます。 これを行うには、[GetLocalizedStringsTransformation](string-transformations.md) 要求変換を使用して、ローカライズされた文字列を要求の種類にコピーします。 JSON ペイロードを生成する `GenerateEmailRequestBody` 要求変換では、ローカライズされた文字列を含む入力要求が使用されます。

1. ご自分のポリシーでは、subject、message、codeIntro、および signature の文字列要求を定義します。
1. ローカライズされた文字列値を手順 1 の要求に置き換える [GetLocalizedStringsTransformation](string-transformations.md) 要求変換を定義します。
1. 次の XML スニペットの入力要求を使用するように `GenerateEmailRequestBody` 要求変換を変更します。
1. Azure AD B2C によってローカライズされるすべての文字列の代わりに、動的パラメーターが使用されるように、お使いの SendGrid テンプレートを更新します。

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. 次の [Localization](localization.md) 要素を追加します。

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. [ContentDefinitions](contentdefinitions.md) 要素を更新し、LocalizedResources 要素への参照を追加します。

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. 最後に、次の入力要求変換を `LocalAccountSignUpWithLogonEmail` および `LocalAccountDiscoveryUsingEmailAddress` 技術プロファイルに追加します。

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="optional-localize-the-ui"></a>[オプション] UI をローカライズする

Localization 要素を使用すると、ユーザー体験に関するポリシーで複数のロケールや言語をサポートすることができます。 ポリシーでのローカライズのサポートにより、[検証表示コントロールのユーザー インターフェイス要素](localization-string-ids.md#verification-display-control-user-interface-elements)と[ワンタイム パスワードのエラー メッセージ](localization-string-ids.md#one-time-password-error-messages)の両方に言語固有の文字列を使用できます。 次の LocalizedString を LocalizedResources に追加します。

```xml
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
    <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxNumberOfCodeGenerated">You have exceeded the number of code generation attempts allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```


## <a name="next-steps"></a>次のステップ

カスタム メール確認ポリシーの例は GitHub で確認できます。

- [カスタム メール確認 - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- カスタム REST API や任意の HTTP ベース SMTP メール プロバイダーの使用方法については、「[Azure Active Directory B2C カスタム ポリシーで RESTful 技術プロファイルを定義する](restful-technical-profile.md)」を参照してください。

::: zone-end
