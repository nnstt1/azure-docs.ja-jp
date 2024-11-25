---
title: 'チュートリアル: Azure Active Directory と UserEcho の統合 | Microsoft Docs'
description: Azure Active Directory と UserEcho の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: fb98d34d5f31b339010fa2c47a135dcf0a1cdf7c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737982"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>チュートリアル: Azure Active Directory と UserEcho の統合

このチュートリアルでは、UserEcho と Azure Active Directory (Azure AD) を統合する方法について説明します。
UserEcho と Azure AD の統合には、次の利点があります。

* UserEcho にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで UserEcho に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と UserEcho の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* UserEcho でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* UserEcho では、**SP** によって開始される SSO がサポートされます

## <a name="adding-userecho-from-the-gallery"></a>ギャラリーからの UserEcho の追加

Azure AD への UserEcho の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に UserEcho を追加する必要があります。

**ギャラリーから UserEcho を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**UserEcho**」と入力し、結果パネルで **[UserEcho]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの UserEcho](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、UserEcho で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと UserEcho 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

UserEcho で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[UserEcho のシングル サインオンの構成](#configure-userecho-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[UserEcho のテスト ユーザーの作成](#create-userecho-test-user)** - UserEcho で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

UserEcho で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **UserEcho** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[UserEcho のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.userecho.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[UserEcho クライアント サポート チーム](https://feedback.userecho.com/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[UserEcho のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-userecho-single-sign-on"></a>UserEcho のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として UserEcho 企業サイトにサインオンします。

2. 上部にあるツール バーで、ユーザー名をクリックして、メニューを展開し、 **[Setup (セットアップ)]** をクリックします。
   
    ![UserEcho サイトから [Setup]\(セットアップ\) が選択されていることを示すスクリーンショット。](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. **[Integrations (統合)]** をクリックします。
   
    ![[Settings]\(設定\) メニューから [Integrations]\(統合\) が選択されていることを示すスクリーンショット。](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. **[Website (Web サイト)]** 、 **[Single sign-on (SAML2) (シングル サインオン (SAML2))]** の順にクリックします。
   
    ![[Integrations]\(統合\) メニューから [Single sign-on SAML2]\(シングル サインオン SAML2\) が選択されていることを示すスクリーンショット。](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. **[Single sign-on (SAML2) (シングル サインオン (SAML2))]** ページで、次の手順に従います。
   
    ![[Single Sign-on SAML]\(シングル サインオン SAML\) ページを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. **[SAML-enabled]** で **[Yes]** を選択します。
    
    b. **[SAML SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。
    
    c. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。
    
    d. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[X.509 Certificate]** ボックスに貼り付けます。
    
    e. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に UserEcho へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[UserEcho]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[UserEcho]** を選択します。

    ![アプリケーションの一覧の UserEcho リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-userecho-test-user"></a>UserEcho のテスト ユーザーの作成

このセクションの目的は、UserEcho で Britta Simon というユーザーを作成することです。

**UserEcho で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. UserEcho 企業サイトに管理者としてサインオンします。

2. 上部にあるツール バーで、ユーザー名をクリックして、メニューを展開し、 **[Setup (セットアップ)]** をクリックします。
   
    ![UserEcho サイトから [Setup]\(セットアップ\) が選択されていることを示すスクリーンショット。](./media/userecho-tutorial/tutorial_userecho_06.png)

3. **[Users (ユーザー)]** をクリックして、 **[Users (ユーザー)]** セクションを展開します。
   
    ![[Settings]\(設定\) メニューから [Users]\(ユーザー\) が選択されていることを示すスクリーンショット。](./media/userecho-tutorial/tutorial_userecho_10.png)

4. **[ユーザー]** をクリックします。
   
    ![スクリーンショットは、[Users]\(ユーザー\) を示しています。](./media/userecho-tutorial/tutorial_userecho_11.png)

5. **[Invite a new user (新しいユーザーを招待)]** をクリックします。
   
    ![[Invite a new user]\(新しいユーザーを招待\) コントロールを示すスクリーンショット。](./media/userecho-tutorial/tutorial_userecho_12.png)

6. **[Invite a new user (新しいユーザーを招待)]** ダイアログで、次の手順に従います。
   
    ![[Invite a new user]\(新しいユーザーを招待\) ダイアログ ボックスを示すスクリーンショット。ここで、ユーザー情報を入力できます。](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. **[名前]** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。
    
    b.  **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。
    
    c. **[招待]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [UserEcho] タイルをクリックすると、SSO を設定した UserEcho に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)