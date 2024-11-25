---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Jamf Pro の統合 | Microsoft Docs
description: Azure Active Directory と Jamf Pro の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 1cf0285bc0308e2a88f02b516d048bd02757895b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132295438"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>チュートリアル:Azure Active Directory SSO と Jamf Pro の統合

このチュートリアルでは、Jamf Pro と Azure Active Directory (Azure AD) を統合する方法について説明します。 Jamf Pro を Azure AD に統合すると、次のことができます。

* Jamf Pro にアクセスできるユーザーを Azure AD を使用して管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Jamf Pro に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Jamf Pro サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* Jamf Pro では、**SP Initiated** SSO と **IdP Initiated** SSO がサポートされます。

## <a name="add-jamf-pro-from-the-gallery"></a>ギャラリーからの Jamf Pro の追加

Azure AD への Jamf Pro の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Jamf Pro を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のウィンドウで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「*Jamf Pro*」と入力します。
1. 結果のパネルから **[Jamf Pro]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Jamf Pro に対する Azure AD の SSO の構成とテスト

B. Simon というテスト ユーザーを使用して、Jamf Pro に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Jamf Pro の関連ユーザーとの間にリンク関係を確立する必要があります。

このセクションでは、Jamf Pro に対する Azure AD SSO を構成してテストします。

1. [Azure AD の SSO を構成](#configure-sso-in-azure-ad)して、その機能をユーザーが使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)し、B.Simon のアカウントを使用して Azure AD SSO をテストします。
    1. [Azure AD のテスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD の SSO を使用できるようにします。
1. [Jamf Pro の SSO を構成](#configure-sso-in-jamf-pro)して、アプリケーション側で SSO 設定を構成します。
    1. [Jamf Pro のテスト ユーザーを作成](#create-a-jamf-pro-test-user)します。Jamf Pro で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO の構成をテスト](#test-the-sso-configuration)して、この構成が機能することを確認します。

## <a name="configure-sso-in-azure-ad"></a>Azure AD での SSO の構成

このセクションでは、Azure portal で Azure AD の SSO を有効にします。

1. Azure portal の **Jamf Pro** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![[基本的な SAML 構成] ページを編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IdP-Initiated** モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/metadata` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/SSO` という形式で URL を入力します。

1. **[追加の URL を設定します]** を選択します。 **SP-Initiated** モードでアプリケーションを構成する場合は、**[サインオン URL]** ボックスに `https://<subdomain>.jamfcloud.com` 形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 実際の識別子の値を Jamf Pro ポータルの **[シングル サインオン]** セクションから取得します。これについては、このチュートリアルで後ほど説明します。 実際のサブドメインの値を識別子の値から抽出し、サインオン URL と応答 URL としてそのサブドメイン情報を使用できます。 Azure portal の **[基本的な SAML 構成]** セクションに示されている形式を参照することもできます。

1. **[Set up Single Sign-On with SAML]\(SAML によるシングル サインオンのセットアップ\)** ページの **[SAML 署名証明書]** セクションに移動し、**コピー** ボタンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーし、お使いのコンピューターに保存します。

    ![SAML 署名証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。
   1. **[ユーザー名]** フィールドに、「<name>@<companydomain>.<extension>」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Jamf Pro へのアクセスを許可します。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Jamf Pro]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスの [ユーザー] の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンを選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

## <a name="configure-sso-in-jamf-pro"></a>Jamf Pro の SSO の構成

1. Jamf Pro 内での構成を自動化するには、**[拡張機能のインストール]** を選択して **My Apps Secure Sign-in ブラウザー拡張機能** をインストールします。

    ![[My Apps Secure Sign-in ブラウザー拡張機能] ページ](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、**[Set up Jamf Pro]\(Jamf Pro のセットアップ\)** を選択します。 Jamf Pro アプリケーションが開いたら、管理者の資格情報を入力してサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 7 までが自動化されます。

    ![Jamf Pro のセットアップ構成ページ](common/setup-sso.png)

3. Jamf Pro を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Jamf Pro 企業サイトにサインインします。 その後、次の手順を行います。

4. ページの右上隅にある **[Settings]\(設定\)** アイコンを選択します。

    ![Jamf Pro の設定アイコンを選択する](./media/jamfprosamlconnector-tutorial/configure1.png)

5. **[Single Sign-On]\(シングル サインオン\)** を選択します。

    ![Jamf Pro で [Single Sign-On]\(シングル サインオン\) を選択する](./media/jamfprosamlconnector-tutorial/configure2.png)

6. **[Single Sign On]\(シングル サインオン\)** ページで、次の手順を実行します。

    ![Jamf Pro の [Single Sign-On]\(シングル サインオン\) ページ](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **[Edit]\(編集\)** を選択します。

    b. **[Enable Single Sign-On Authentication]\(シングル サインオン認証を有効にする\)** チェック ボックスをオンにします。

    c. **[Identity Provider]\(ID プロバイダー\)** ドロップダウン メニューからオプションとして **[Azure]** を選択します。

    d. **[ENTITY ID]\(エンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子 (エンティティ ID)]** フィールドに貼り付けます。

    > [!NOTE]
    > `<SUBDOMAIN>` フィールドの値を使用して、Azure portal の **[基本的な SAML 構成]** セクションにおけるサインオン URL と応答 URL を完成させる必要があります。

    e. **[Identity Provider Metadata Source]\(ID プロバイダー メタデータ ソース\)** ドロップダウン メニューから **[Metadata URL]\(メタデータ URL\)** を選択します。 表示されたフィールドに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    f. (任意) トークンの有効期限の値を編集するか、[Disable SAML token expiration]\(SAML トークンの有効期限を無効にする\) を選択します。

7. 同じページで下へスクロールし、**[User Mapping]\(ユーザー マッピング\)** セクションを表示します。 その後、次の手順を行います。

    ![Jamf Pro の [Single Sign-On]\(シングル サインオン\) ページの [User Mapping]\(ユーザー マッピング\) セクション](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. **[Identity Provider User Mapping]\(ID プロバイダー ユーザー マッピング\)** で **[NameID]** オプションを選択します。 既定では、このオプションは **[NameID]** に設定されていますが、カスタム属性を定義することもできます。

    b. **[Jamf Pro User Mapping]\(JAMF PRO ユーザー マッピング\)** の **[Email]\(電子メール\)** を選択します。 Jamf Pro は、IdP によって送信された SAML 属性をまずユーザー別でマップし、次にグループ別でマップします。 ユーザーが Jamf Pro にアクセスしようとすると、Jamf Pro がユーザーに関する情報を ID プロバイダーから取得し、それを Jamf Pro の全ユーザー アカウントと照合します。 受信したユーザー アカウントが見つからない場合は、グループ名でのマッチングが試みられます。

    c. **[IDENTITY PROVIDER GROUP ATTRIBUTE NAME]\(ID プロバイダー グループ属性名\)** フィールドに値 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` を貼り付けます。

    d. 同じページで、**[Security]\(セキュリティ\)** セクションまで下にスクロールし、**[Allow users to bypass the Single Sign-On authentication]\(ユーザーがシングル サインオン認証をバイパスできるようにする\)** を選択します。 この場合、ユーザーは ID プロバイダーのサインイン ページにリダイレクトされて認証されるのではなく、Jamf Pro に直接サインインできるようになります。 ユーザーが ID プロバイダーを経由して Jamf Pro にアクセスしようとした場合は、IdP によって開始される SSO 認証と承認が発生します。

    e. **[保存]** を選択します。

### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro テスト ユーザーの作成

Azure AD ユーザーが Jamf Pro にサインインできるようにするには、ユーザーを Jamf Pro にプロビジョニングする必要があります。 Jamf Pro へのプロビジョニングは手動での作業となります。

ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Jamf Pro の企業サイトに管理者としてサインインします。

2. ページの右上隅にある **[Settings]\(設定\)** アイコンを選択します。

    ![Jamf Pro の設定アイコン](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **[Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\)** を選択します。

    ![Jamf Pro の設定の [Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\) アイコン](./media/jamfprosamlconnector-tutorial/user1.png)

4. **[新規]** を選択します。

    ![[Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\) システム設定ページ](./media/jamfprosamlconnector-tutorial/user2.png)

5. **[Create Standard Account]\(標準アカウントの作成\)** を選択します。

    ![[Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\) ページの [Create Standard Account]\(標準アカウントの作成\) オプション](./media/jamfprosamlconnector-tutorial/user3.png)

6. **[New Account]\(新しいアカウント\)** ダイアログ ボックスで、次の手順を実行します。

    ![Jamf Pro システム設定の [New Account]\(新しいアカウント\) の設定オプション](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **[USERNAME]\(ユーザー名\)** フィールドに「`Britta Simon`」(テスト ユーザーのフル ネーム) と入力します。

    b. **[ACCESS LEVEL]\(アクセス レベル\)**、**[PRIVILEGE SET]\(特権セット\)**、**[ACCESS STATUS]\(アクセス ステータス\)** で、貴社の組織に合ったオプションを選択します。

    c. **[FULL NAME]\(フル ネーム\)** フィールドに「`Britta Simon`」と入力します。

    d. **[EMAIL ADDRESS]\(メール アドレス\)** フィールドに、Britta Simon アカウントのメール アドレスを入力します。

    e. **[PASSWORD]\(パスワード\)** フィールドに、ユーザーのパスワードを入力します。

    f. **[VERIFY PASSWORD]\(パスワードの確認\)** フィールドに、もう一度ユーザーのパスワードを入力します。

    g. **[保存]** を選択します。

## <a name="test-the-sso-configuration"></a>SSO の構成のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Jamf Pro のサインオン URL にリダイレクトされます。  

* Jamf Pro のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Jamf Pro に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Jamf Pro] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Jamf Pro に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Jamf Pro を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
