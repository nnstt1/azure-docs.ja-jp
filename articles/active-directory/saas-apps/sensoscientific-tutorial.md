---
title: 'チュートリアル: Azure Active Directory と SensoScientific Wireless Temperature Monitoring System の統合 | Microsoft Docs'
description: Azure Active Directory と SensoScientific Wireless Temperature Monitoring System の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 337ce33c1b774aeb2a64e2e11f6cd270732cdcb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124801545"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>チュートリアル: Azure Active Directory と SensoScientific Wireless Temperature Monitoring System の統合

このチュートリアルでは、SensoScientific Wireless Temperature Monitoring System と Azure Active Directory (Azure AD) を統合する方法について説明します。
SensoScientific Wireless Temperature Monitoring System と Azure AD の統合には、次の利点があります。

* SensoScientific Wireless Temperature Monitoring System にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SensoScientific Wireless Temperature Monitoring System に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SensoScientific Wireless Temperature Monitoring System と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* SensoScientific Wireless Temperature Monitoring System でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SensoScientific Wireless Temperature Monitoring System では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>ギャラリーからの SensoScientific Wireless Temperature Monitoring System の追加

Azure AD への SensoScientific Wireless Temperature Monitoring System の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SensoScientific Wireless Temperature Monitoring System を追加する必要があります。

**ギャラリーから SensoScientific Wireless Temperature Monitoring System を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SensoScientific Wireless Temperature Monitoring System**」と入力し、結果パネルで **[SensoScientific Wireless Temperature Monitoring System]** を選択してから、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の SensoScientific Wireless Temperature Monitoring System](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、SensoScientific Wireless Temperature Monitoring System で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SensoScientific Wireless Temperature Monitoring System 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

SensoScientific Wireless Temperature Monitoring System で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SensoScientific Wireless Temperature Monitoring System のシングル サインオンの構成](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SensoScientific Wireless Temperature Monitoring System のテスト ユーザーの作成](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - SensoScientific Wireless Temperature Monitoring System で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SensoScientific Wireless Temperature Monitoring System で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SensoScientific Wireless Temperature Monitoring System** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[SensoScientific Wireless Temperature Monitoring System のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SensoScientific Wireless Temperature Monitoring System の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>SensoScientific Wireless Temperature Monitoring System のシングル サインオンの構成

1. 管理者として SensoScientific Wireless Temperature Monitoring System アプリケーションにサインオンします。

1. 上部にあるナビゲーション メニューの **[Configuration]\(構成\)** をクリックし、**[Configure]\(構成\)** の **[Single Sign On]\(シングル サインオン\)** に移動して [Single Sign On Settings]\(シングル サインオン設定\) を開き、次の手順を実行します。

    ![Configure single sign-on](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. **[Issuer Name (発行者名)]** として [Azure AD] を選択します。

    b. **[発行者の URL]** ボックスに、Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

    c. **[シングル サインオン サービス URL]** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    d. **[シングル サインアウト サービス URL]** ボックスに、Azure portal からコピーした **ログアウト URL** を貼り付けます。

    e. Azure ポータルからダウンロードした証明書を参照してアップロードします。

    f. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SensoScientific Wireless Temperature Monitoring System へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[SensoScientific Wireless Temperature Monitoring System]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション リストから「**SensoScientific Wireless Temperature Monitoring SystemScaleX Enterprise**」を選択します。

    ![アプリケーションの一覧の SensoScientific Wireless Temperature Monitoring System のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific Wireless Temperature Monitoring System のテスト ユーザーの作成

Azure AD ユーザーが SensoScientific Wireless Temperature Monitoring System にサインインできるようにするには、ユーザーを SensoScientific Wireless Temperature Monitoring System にプロビジョニングする必要があります。 [SensoScientific Wireless Temperature Monitoring System サポート チーム](https://www.sensoscientific.com/contact-us/)と連携して、SensoScientific Wireless Temperature Monitoring System プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SensoScientific Wireless Temperature Monitoring System] タイルをクリックすると、SSO を設定した SensoScientific Wireless Temperature Monitoring System に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)