---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と WEDO の統合 | Microsoft Docs'
description: Azure Active Directory と WEDO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 49bccfa7a5a3cc90038c5068841c75c972616fe2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316658"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と WEDO の統合

このチュートリアルでは、WEDO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と WEDO を統合すると、次のことができます。

* WEDO にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して WEDO に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* WEDO でのシングル サインオン (SSO) が有効なサブスクリプション。 SSO サブスクリプションを取得するには、[WEDO クライアント サポート チーム](mailto:info@wedo.swiss)にお問い合わせください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* WEDO では、**SP Initiated SSO と IDP Initiated SSO** がサポートされています。
* WEDO では、[自動化されたユーザー プロビジョニング](wedo-provisioning-tutorial.md)がサポートされています。

## <a name="add-wedo-from-the-gallery"></a>ギャラリーから WEDO を追加する

Azure AD への WEDO の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に WEDO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**WEDO**」と入力します。
1. 結果のパネルから **[WEDO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-wedo"></a>WEDO に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、WEDO に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと WEDO の関連ユーザーとの間にリンク関係を確立する必要があります。

WEDO に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[WEDO SSO の構成](#configure-wedo-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[WEDO テスト ユーザーの作成](#create-wedo-test-user)** - WEDO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **WEDO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.wedo.swiss/sp/acs` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.wedo.swiss/sp/acs` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.wedo.swiss/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[WEDO クライアント サポート チーム](mailto:info@wedo.swiss)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. WEDO アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    | 名前 | ソース属性|
    | ------------ | --------- |
    | email | user.email |
    | firstName | user.firstName |
    | lastName | user.lasttName |
    | userName | user.userName |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[WEDO のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に WEDO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[WEDO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-wedo-sso"></a>WEDO SSO の構成

WEDO で Azure AD SSO を有効にするには、以下の手順に従います。

1. [WEDO](https://login.wedo.swiss/) にログインします。 **管理者ロール** を持っている必要があります。
1. [Profile settings]\(プロファイル設定\) で、**[Network settings]\(ネットワーク設定\)** セクションの **[Authentication]\(認証\)** メニューを選択します。
1. **[SAML Authentication]\(SAML 認証\)** ページで、次の手順を実行します。

   ![SAML 認証のリンク](media/wedo-tutorial/network-security-authentification.png)

   a. **SAML 認証** を有効にします。

   b. **[Identity Provider metadata (XML)]\(ID プロバイダー メタデータ (XML)\)** タブを選択します。

   c. Azure portal からダウンロードした **フェデレーション メタデータ XML** をメモ帳で開き、メタデータ XML の内容をコピーして、**[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

   d. **[保存]** をクリックします。

### <a name="create-wedo-test-user"></a>WEDO テスト ユーザーの作成

このセクションでは、WEDO で Bob Simon というテスト ユーザーを作成します。 情報は、「**Azure AD のテスト ユーザーの作成**」と一致している必要があります。

1. WEDO の [Profile setting]\(プロファイル設定\) で、**[Network settings]\(ネットワーク設定\)** セクションの **[Users]\(ユーザー\)** を選択します。
1. **[ユーザーの追加]** をクリックします。
1. [Add user]\(ユーザーの追加\) ポップアップで、ユーザーの情報を入力します

    a. 名として「`B`」。

    b. 姓として「`Simon`」。

    c. メール アドレスとして「`username@companydomain.extension`」を入力します。 たとえば、`B.Simon@contoso.com` のようにします。 必ずメール アドレスのドメインを会社の短い名前と同じにする必要があります。

    d. ユーザーの種類として「`User`」。

    e. **[Create user]\(ユーザーの作成\)** をクリックします。

    f. **[Select teams]\(チームの選択\)** ページで、**[Save]\(保存\)** をクリックします。

    g.  **[Invite user]\(ユーザーの招待\)** ページで、**[Yes]\(はい\)** をクリックします。

1. 電子メールで受信したリンクを使用してユーザーを検証する

> [!NOTE]
> フェイク ユーザーを作成する場合 (上のメール アドレスはネットワークに存在しません)、[サポート](mailto:info@wedo.swiss)に連絡して、ユーザーを検証します*。

> [!NOTE]
> WEDO では、自動ユーザー プロビジョニングもサポートされています。自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](./wedo-provisioning-tutorial.md)を参照してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる WEDO のサインオン URL にリダイレクトされます。  

* WEDO のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した WEDO に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで WEDO タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した WEDO に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

WEDO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
