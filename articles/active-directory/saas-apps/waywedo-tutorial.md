---
title: チュートリアル:Azure Active Directory と Way We Do の統合 | Microsoft Docs
description: Azure Active Directory と Way We Do の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 26e55c1bbb1c20781a28dda8ad635c99d058da2d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731515"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>チュートリアル:Way We Do と Azure Active Directory の統合

このチュートリアルでは、Way We Do と Azure Active Directory (Azure AD) を統合する方法について説明します。 Way We Do を Azure AD と統合すると、次のことができます。

* Way We Do にアクセスできる Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Way We Do に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Way We Do でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Way We Do では、**SP** によって開始される SSO がサポートされます
* Way We Do では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-way-we-do-from-the-gallery"></a>ギャラリーからの Way We Do の追加

Azure AD への Way We Do の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Way We Do を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Way We Do**」と入力します。
1. 結果ウィンドウで **[Way We Do]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B. Simon** というテスト ユーザーを使用して、Way We Do に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Way We Do の関連ユーザーの間で、リンク関係を確立する必要があります。

Way We Do で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Way We Do SSO の構成](#configure-way-we-do-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Way We Do テスト ユーザーの作成](#create-way-we-do-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Way We Do で作成します。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Way We Do** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 この値を取得するには、[Way We Do クライアント サポート チーム](mailto:support@waywedo.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[Way We Do のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Way We Do SSO の構成

1. Way We Do 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **マイアプリによるセキュリティで保護された Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Way We Do のセットアップ]** をクリックすると、Way We Do アプリケーションに移動します。 そこから、管理者の資格情報を入力して Way We Do にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Way We Do を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Way We Do 企業サイトにサインインして、次の手順を実行します。

1. Way We Do の任意のページで右上隅にある **人アイコン** をクリックし、ドロップダウン メニューで **[Account]\(アカウント\)** をクリックします。

    ![Way We Do アカウント](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. **メニュー アイコン** をクリックしてプッシュ ナビゲーション メニューを開き、 **[Single Sign On]\(シングル サイン オン\)** をクリックします。

    ![Way We Do シングル](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. **[Single sign-on setup]\(シングル サイン オンの設定\)** ページで、次の手順を行います。

    ![Way We Do 保存](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. **[Turn on single sign-on]\(シングル サイン オンの有効化\)** トグルをクリックして、**[Yes]\(はい\)** に切り替えて、シングル サインオンを有効にします。

    1. **[Single sign-on name]\(シングル サイン オン名\)** テキストボックスに、自分の名前を入力します。

    1. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    1. **[SAML SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[Certificate]\(証明書\)** の横にある **選択** ボタンをクリックして、証明書をアップロードします。

    1. **オプション設定** -

        * Enable Passwords (パスワードを有効にする) - このオプションを有効にすると、ユーザーがシングル サインオンのみを使用できるように通常のパスワードが Way We Do に対して機能します。

        * Enable Auto-provisioning (自動プロビジョニングを有効にする) - このオプションを有効にすると、サインオンに使用されている電子メール アドレスが Way We Do 内のユーザー リストと自動的に比較されます。 電子メール アドレスが Way We Do 内のアクティブ ユーザーと一致しない場合は、サインインするユーザーに対して新しいユーザー アカウントが自動的に追加され、不足している情報があれば要求されます。

          > [!NOTE]
          > シングル サインオンを介して追加されたユーザーは、一般ユーザーとして追加され、システムでのロールは割り当てられません。 管理者は編集者または管理者としてセキュリティ ロールにアクセスし、それを変更することができると共に、1 つまたは複数の Org Chart ロールを割り当てることもできます。

    1. **[Save]\(保存\)** をクリックして設定を保持します。

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

このセクションでは、B. Simon に Way We Do へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Way We Do]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-way-we-do-test-user"></a>Way We Do テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Way We Do に作成します。 Way We Do では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Way We Do にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Way We Do クライアント サポート チーム](mailto:support@waywedo.com)にお問い合わせください。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Way We Do] タイルを選択すると、SSO を設定した Way We Do に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)