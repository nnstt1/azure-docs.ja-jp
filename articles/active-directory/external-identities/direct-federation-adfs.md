---
title: B2B 用 AD FS を使用して SAML/WS-Fed IdP フェデレーションを設定する - Azure AD
description: ご自身の Azure AD アプリにゲストがサインインできるようにするために、AD FS を SAML/WS-Fed IdP フェデレーションの ID プロバイダー (IdP) として設定する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/27/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984ddc25f11f76ba8dbe0874ac5aa64c15ebf323
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758467"
---
# <a name="example-configure-samlws-fed-based-identity-provider-federation-with-ad-fs-preview"></a>例: AD FS を使用して SAML/WS-Fed ベースの ID プロバイダー フェデレーションを構成する (プレビュー)

>[!NOTE]
>- Azure Active Directory の "*直接フェデレーション*" は、"*SAML/WS-Fed ID プロバイダー (IdP) フェデレーション*" と呼ばれるようになりました。
>- SAML/WS-Fed IdP フェデレーションは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Active Directory フェデレーション サービス (AD FS) を SAML 2.0 または WS-Fed IdP として使用して [SAML/WS-Fed IdP フェデレーション](direct-federation.md)を設定する方法について説明します。 フェデレーションをサポートするには、IdP に特定の属性とクレームを構成する必要があります。 フェデレーション用の IdP を構成する方法を示すために、例として Active Directory フェデレーション サービス (AD FS) を使用します。 AD FS を SAML IdP として設定する方法と、WS-Fed IdP として設定する方法の両方を紹介します。

> [!NOTE]
> この記事では、わかりやすいように、AD FS を SAML 用に設定する方法と、WS-Fed 用に設定する方法の両方について説明します。 IdP が AD FS であるフェデレーションの統合には、WS-Fed をプロトコルとして使用することをお勧めします。

## <a name="configure-ad-fs-for-saml-20-federation"></a>AD FS を SAML 2.0 のフェデレーション用に構成する

Azure AD B2B は、以下に示す特定の要件に従って、SAML プロトコルを使用する IdP と連携するように構成できます。 SAML の構成手順を説明するために、このセクションでは SAML 2.0 用に AD FS を設定する方法を示します。

フェデレーションを設定するには、IdP からの SAML 2.0 応答において以下の属性が受け取られる必要があります。 これらの属性は、オンライン セキュリティ トークン サービスの XML ファイルにリンクするか手動で入力することによって、構成できます。 「[Create a test AD FS instance](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)」(AD FS テスト インスタンスの作成) のステップ 12 では、AD FS エンドポイントを検索する方法や、メタデータ URL (たとえば `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`) を生成する方法について説明しています。 

|属性  |値  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|対象ユーザー     |`urn:federation:MicrosoftOnline`         |
|発行者     |パートナー IdP の発行者 URI (たとえば `http://www.example.com/exk10l6w90DHM0yi...`)         |

IdP によって発行される SAML 2.0 トークン内に以下のクレームが構成されている必要があります。


|属性  |値  |
|---------|---------|
|NameID の形式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


次のセクションでは、SAML 2.0 IdP の例として、AD FS を使用して必須の属性とクレームを構成する方法について説明します。

### <a name="before-you-begin"></a>開始する前に

この手順を開始する前に、AD FS サーバーが既に設定されていて稼働している必要があります。 AD FS サーバーの設定に関するヘルプについては、「[Create a test AD FS 3.0 instance on an Azure virtual machine](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)」(Azure 仮想マシンでの AD FS 3.0 テスト インスタンスの作成) を参照してください。

### <a name="add-the-claim-description"></a>要求記述を追加する

1. ご利用の AD FS サーバー上で、 **[ツール]**  >  **[AD FS の管理]** を選択します。
2. ナビゲーション ウィンドウで、 **[サービス]**  >  **[要求記述]** を選択します。
3. **[操作]** の **[要求記述の追加]** を選択します。
4. **[要求記述の追加]** ウィンドウで、次の値を指定します。

   - **[表示名]** : 永続的な識別子
   - **要求識別子**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - **[このフェデレーション サービスが受け付けることができる要求の種類としてこの要求記述をフェデレーション メタデータで公開する]** のチェック ボックスをオンにします。
   - **[このフェデレーション サービスが送信できる要求の種類としてこの要求記述をフェデレーション メタデータで公開する]** のチェック ボックスをオンにします。

5. **[OK]** をクリックします。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>証明書利用者信頼と要求規則を追加する

1. AD FS サーバー上で、 **[ツール]**  >  **[AD FS の管理]** に移動します。
2. ナビゲーション ウィンドウで、 **[信頼関係]**  >  **[証明書利用者信頼]** を選択します。
3. **[操作]** の **[証明書利用者信頼の追加]** を選択します。 
4. 証明書利用者信頼の追加ウィザードの **[データ ソースの選択]** では、**[オンラインまたはローカル ネットワークで公開されている証明書利用者についてのデータをインポートする]** オプションを使用します。 このフェデレーション メタデータ URL を指定します - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml。 その他は既定の選択のままにします。 **[閉じる]** を選択します。
5. **要求規則の編集** ウィザードが開きます。
6. **要求規則の編集** ウィザードで、**[規則の追加]** を選択します。 **[規則の種類の選択]** で、**[LDAP 属性を要求として送信]** を選択します。 **[次へ]** を選択します。
7. **[要求規則の構成]** で、次の値を指定します。 

   - **[要求規則名]** : 電子メール要求規則 
   - **[属性ストア]** : Active Directory 
   - **[LDAP 属性]** : 電子メール アドレス 
   - **[出力方向の要求の種類]** : 電子メール アドレス

8. **[完了]** を選択します。
9. **[要求規則の編集]** ウィンドウに新しい規則が表示されます。 **[Apply]** をクリックします。 
10. **[OK]** をクリックします。  

### <a name="create-an-email-transform-rule"></a>電子メール変換規則を作成する
1. **[要求規則の編集]** で、**[規則の追加]** をクリックします。 **[規則の種類の選択]** で、**[入力方向の要求を変換]** を選択し、**[次へ]** をクリックします。 
2. **[要求規則の構成]** で、次の値を指定します。 

   - **[要求規則名]** : 電子メール変換規則 
   - **[入力方向の要求の種類]** : 電子メール アドレス 
   - **[出力方向の要求の種類]** : 名前 ID 
   - **[出力方向の名前 ID 形式]** : 永続的な識別子 
   - **[すべての要求値をパススルーする]** を選択します。

3. **[完了]** をクリックします。 
4. **[要求規則の編集]** ウィンドウに新しい規則が表示されます。 **[適用]** をクリックします。 
5. **[OK]** をクリックします。 これで、SAML 2.0 プロトコルを使用したフェデレーション用に AD FS サーバーが構成されました。

## <a name="configure-ad-fs-for-ws-fed-federation"></a>AD FS を WS-Fed フェデレーション用に構成する 
Azure AD B2B は、以下に示す特定の要件に従って、WS-Fed プロトコルを使用する IdP と連携するように構成できます。 現時点で 2 つの WS-Fed プロバイダー (AD FS と Shibboleth) が、Azure AD との互換性テスト済みです。 ここでは、WS-Fed IdP の例として、Active Directory フェデレーション サービス (AD FS) を使用します。 WS-Fed 準拠のプロバイダーと Azure AD の間に証明書利用者信頼を確立する方法の詳細については、Azure AD ID プロバイダーの互換性に関するドキュメントをダウンロードしてください。

フェデレーションを設定するには、IdP からの WS-Fed メッセージにおいて以下の属性が受け取られる必要があります。 これらの属性は、オンライン セキュリティ トークン サービスの XML ファイルにリンクするか手動で入力することによって、構成できます。 「[Create a test AD FS instance](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)」(AD FS テスト インスタンスの作成) のステップ 12 では、AD FS エンドポイントを検索する方法や、メタデータ URL (たとえば `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`) を生成する方法について説明しています。
 
|属性  |値  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|対象ユーザー     |`urn:federation:MicrosoftOnline`         |
|発行者     |パートナー IdP の発行者 URI (たとえば `http://www.example.com/exk10l6w90DHM0yi...`)         |

IdP によって発行される WS-Fed トークンに必須の要求:

|属性  |値  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

次のセクションでは、WS-Fed IdP の例として、AD FS を使用して必須の属性とクレームを構成する方法について説明します。

### <a name="before-you-begin"></a>開始する前に
この手順を開始する前に、AD FS サーバーが既に設定されていて稼働している必要があります。 AD FS サーバーの設定に関するヘルプについては、「[Create a test AD FS 3.0 instance on an Azure virtual machine](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)」(Azure 仮想マシンでの AD FS 3.0 テスト インスタンスの作成) を参照してください。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>証明書利用者信頼と要求規則を追加する 
1. AD FS サーバー上で、 **[ツール]**  >  **[AD FS の管理]** に移動します。 
1. ナビゲーション ウィンドウで、 **[信頼関係]**  >  **[証明書利用者信頼]** を選択します。 
1. **[操作]** の **[証明書利用者信頼の追加]** を選択します。  
1. 証明書利用者信頼の追加ウィザードの **[データ ソースの選択]** では、 **[オンラインまたはローカル ネットワークで公開されている証明書利用者についてのデータをインポートする]** オプションを使用します。 このフェデレーション メタデータ URL を指定します: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  その他は既定の選択のままにします。 **[閉じる]** を選択します。
1. **要求規則の編集** ウィザードが開きます。 
1. **要求規則の編集** ウィザードで、**[規則の追加]** を選択します。 **[規則の種類の選択]** で、**[カスタムの規則を使用して要求を送信]** を選択します。 *[次へ]* を選択します。 
1. **[要求規則の構成]** で、次の値を指定します。

   - **[要求規則名]** : 不変 ID の発行  
   - **カスタム規則**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. **[完了]** を選択します。 
1. **[要求規則の編集]** ウィンドウに新しい規則が表示されます。 **[適用]** をクリックします。  
1. 同じ **要求規則の編集** ウィザードで、**[規則の追加]** を選択します。 **[規則の種類の選択]** で、**[LDAP 属性を要求として送信]** を選択します。 **[次へ]** を選択します。
1. **[要求規則の構成]** で、次の値を指定します。 

   - **[要求規則名]** : 電子メール要求規則  
   - **[属性ストア]** : Active Directory  
   - **[LDAP 属性]** : 電子メール アドレス  
   - **[出力方向の要求の種類]** : 電子メール アドレス 

1.  **[完了]** を選択します。 
1.  **[要求規則の編集]** ウィンドウに新しい規則が表示されます。 **[適用]** をクリックします。  
1.  **[OK]** をクリックします。 これで、WS-Fed を使用したフェデレーション用に AD FS サーバーが構成されました。

## <a name="next-steps"></a>次のステップ
次に、Azure AD ポータルで、または PowerShell を使用して、[Azure AD 上で SAML/WS-Fed IdP フェデレーションを構成](direct-federation.md#step-3-configure-samlws-fed-idp-federation-in-azure-ad)します。
