---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Evernote の統合 | Microsoft Docs
description: Azure Active Directory と Evernote の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: cf47fe3054b3ebbfc8b85de45894c083fcae2106
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285882"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Evernote の統合

このチュートリアルでは、Evernote と Azure Active Directory (Azure AD) を統合する方法について説明します。 Evernote を Azure AD と統合すると、次のことができます。

* Evernote にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Evernote にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Evernote でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Evernote では、**SP と IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-evernote-from-the-gallery"></a>ギャラリーからの Evernote の追加

Azure AD への Evernote の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Evernote を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Evernote**」と入力します。
1. 結果のパネルから **[Evernote]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-evernote"></a>Evernote の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Evernote に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Evernote の関連ユーザーとの間にリンク関係を確立する必要があります。

Evernote に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Evernote の SSO の構成](#configure-evernote-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Evernote のテスト ユーザーの作成](#create-evernote-test-user)** - Evernote で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Evernote** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** Initiated モードで構成する場合は、次の手順を行います。 

    **[識別子]** ボックスに、`https://www.evernote.com/saml2` という URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://www.evernote.com/Login.action`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **署名** オプションを変更するには、 **[編集]** をクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![[Edit]\(編集\) ボタンが選択されている [S A M L Signing Certificate]\(S A M L 署名証明書\) ダイアログを示すスクリーンショット。](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/assertion.png)

    a. **[署名オプション]** で **[SAML 応答とアサーションへの署名]** オプションを選択します。

    b. **[保存]**

1. **[Evernote のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Evernote へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Evernote]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-evernote-sso"></a>Evernote の SSO の構成

1. Evernote 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Evernote のセットアップ]** をクリックすると、Evernote アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Evernote にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Evernote を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Evernote 企業サイトにサインインして、次の手順を実行します。

4. **[管理コンソール]** に移動します。

    ![管理コンソール](./media/evernote-tutorial/admin.png)

5. **[管理コンソール]** から **[セキュリティ]** に移動し、 **[シングル サインオン]** を選択します。

    ![SSO の設定](./media/evernote-tutorial/security.png)

6. 次の値を構成します。

    ![Certificate-Setting](./media/evernote-tutorial/certificate.png)
    
    a.  **SSO を有効にします。** SSO は既定で有効になっています (SSO の要件を削除する場合は、 **[シングル サインオンを無効にする]** をクリックします)

    b. Azure portal からコピーした **[ログイン URL]** の値を **[SAML HTTP Request URL]\(SAML HTTP 要求 URL\)** ボックスに貼り付けます。

    c. Azure AD からダウンロードした証明書をメモ帳で開き、"BEGIN CERTIFICATE" および "END CERTIFICATE" を含む内容をコピーして、 **[X.509 証明書]** ボックスに貼り付けます。 

    d. **[変更の保存]** をクリックします。

### <a name="create-evernote-test-user"></a>Evernote テスト ユーザーの作成

Azure AD ユーザーが Evernote にサインインできるようにするには、そのユーザーを Evernote にプロビジョニングする必要があります。  
Evernote の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Evernote の企業サイトに管理者としてサインインします。

2. **[管理コンソール]** をクリックします。

    ![管理コンソール](./media/evernote-tutorial/admin.png)

3. **[管理コンソール]** から **[ユーザーの追加]** に移動します。

    ![[Add Users]\(ユーザーの追加\) が選択されている [Users]\(ユーザー\) メニューを示すスクリーンショット。](./media/evernote-tutorial/create-user.png)

4. **[メール]** テキストボックスの **[チーム メンバーを追加]** にユーザー アカウントのメール アドレスを入力し、 **[招待する]** をクリックします。

    ![Add-testUser](./media/evernote-tutorial/add-user.png)
    
5. 招待が送信された後、招待を承諾するメールが Azure Active Directory アカウント保有者に届きます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Evernote のサインオン URL にリダイレクトされます。  

* Evernote のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Evernote に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Evernote] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Evernote に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Evernote を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
