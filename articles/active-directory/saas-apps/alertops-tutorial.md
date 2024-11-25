---
title: チュートリアル:Azure Active Directory と AlertOps の統合 | Microsoft Docs
description: Azure Active Directory と AlertOps の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: jeedes
ms.openlocfilehash: 0c40d5f65148bc21ef9f4704023f9d1bfbd70db3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330797"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>チュートリアル:AlertOps の Azure Active Directory との統合

このチュートリアルでは、AlertOps と Azure Active Directory (Azure AD) を統合する方法について学習します。 AlertOps と Azure AD と統合すると、次のことができます。

* AlertOps にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して AlertOps に自動的にサインインできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AlertOps でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AlertOps では、**SP Initiated SSO と IDP Initiated SSO** をサポートしています。

## <a name="add-alertops-from-the-gallery"></a>ギャラリーから AlertOps を追加する

Azure AD への AlertOps の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AlertOps を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**AlertOps**」と入力します。
1. 結果ウィンドウで **[AlertOps]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-alertops"></a>AlertOps の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AlertOps で Azure AD の SSO を構成し、テストします。 SSO が機能するには、Azure AD ユーザーと AlertOps の関連ユーザーの間で、リンク関係を確立する必要があります。

AlertOps に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AlertOps の SSO の構成](#configure-alertops-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AlertOps のテスト ユーザーの作成](#create-alertops-test-user)** - AlertOps で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AlertOps** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    1. **[識別子]** ボックスに、`https://<SUBDOMAIN>.alertops.com` の形式で URL を入力します。

    1. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.alertops.com/login.aspx` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.alertops.com/login.aspx` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[AlertOps クライアント サポート チーム](mailto:support@alertops.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[AlertOps のセットアップ]** セクションで、ご自分の要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に AlertOps へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AlertOps]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-alertops-sso"></a>AlertOps の SSO の構成

1. AlertOps 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup AlertOps]\(AlertOps の設定)** をクリックすると、AlertOps アプリケーションに移動します。 そこから、管理者資格情報を提供して AlertOps にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. AlertOps を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の AlertOps 企業サイトにサインインして、次の手順のようにします。

4. 左側のナビゲーション パネルから **[Account settings]\(アカウント設定\)** をクリックします。

    ![このスクリーンショットは、[Account Settings]\(アカウント設定\) がコールアウトされた状態の AlertOps メニューを示しています。](./media/alertops-tutorial/settings.png)

5. **[Subscription Settings]\(サブスクリプションの設定\)** ページで **[SSO]** を選択し、次の手順を実行します。

    ![このスクリーンショットは、この手順で説明されている値が入力された状態の [Subscription Settings]\(サブスクリプションの設定\) ウィンドウの [S S O] を示しています。](./media/alertops-tutorial/configuration.png)

    a. **[Use Single Sign-On(SSO)]\(シングル サインオン (SSO) を使用する\)** チェック ボックスをオンにします。

    b. **[SSO Provider]\(SSO プロバイダー\)** としてドロップダウンから **[Azure Active Directory]** を選択します。

    c. **[発行者の URL]** テキストボックスに、Azure portal の **[基本的な SAML 構成]** セクションで使用した ID 値を使用します。

    d. **[SAML endpoint URL]\(SAML エンドポイント URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. **[SLO endpoint URL]\(SLO エンドポイント URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    f. **[SAML Signature Algorithm]\(SAML 署名アルゴリズム\)** としてボックスの一覧から **[SHA256]** を選択します。

    g. ダウンロードした証明書 (Base64) ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、[X.509 Certificate]\(X.509 証明書\) ボックスに貼り付けます。

### <a name="create-alertops-test-user"></a>AlertOps のテスト ユーザーの作成

1. 別のブラウザーのウィンドウで、管理者としてご自分の AlertOps 企業サイトにサインオンします。

2. 左側のナビゲーション パネルから、 **[Users]\(ユーザー\)** をクリックします。

    ![このスクリーンショットは、[Users]\(ユーザー\) がコールアウトされた状態の AlertOps メニューを示しています。](./media/alertops-tutorial/user.png)

3. **[Add User]\(ユーザーの追加\)** を選択します。

    ![このスクリーンショットは、[Add User]\(ユーザーの追加\) ボタンを備えた [Users]\(ユーザー\) ウィンドウを示しています。](./media/alertops-tutorial/add-user.png)

4. **[Add User]** ダイアログで、次の手順を実行します。

    ![このスクリーンショットは、この手順で説明されている値が入力された状態の [Add User]\(ユーザーの追加\) ペインを示しています。](./media/alertops-tutorial/add-values.png)

    a. **[Login User Name]\(ログイン ユーザー名\)** ボックスにユーザー名を入力します (例: **Brittasimon**)。

    b. **[Official Email]\(公式メール\)** ボックスに、ユーザーのメール アドレスを入力します (この例では **Brittasimon\@contoso.com**)。

    c. **[名]** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    d. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    e. 所属する組織に応じた **[Type]\(種類\)** の値をボックスの一覧から選択します。

    f. 所属する組織に応じて、ユーザーの **[Role]\(役割\)** をボックスの一覧から選択します。

    g. **[追加]** を選択します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる AlertOps のサインオン URL にリダイレクトされます。  

* AlertOps のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した AlertOps に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリの [AlertOps] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した AlertOps に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

AlertOps を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
