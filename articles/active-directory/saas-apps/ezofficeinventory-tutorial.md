---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と EZOfficeInventory の統合 | Microsoft Docs
description: Azure Active Directory と EZOfficeInventory の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: jeedes
ms.openlocfilehash: 40bf71710c14ddf9ff06156403abb00a44c4a361
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285680"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と EZOfficeInventory の統合

このチュートリアルでは、EZOfficeInventory と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と EZOfficeInventory を統合すると、次のことができます。

* EZOfficeInventory にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して EZOfficeInventory に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* EZOfficeInventory でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* EZOfficeInventory では、**SP** Initiated SSO がサポートされます。
* EZOfficeInventory では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-ezofficeinventory-from-the-gallery"></a>ギャラリーからの EZOfficeInventory の追加

Azure AD への EZOfficeInventory の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に EZOfficeInventory を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**EZOfficeInventory**」と入力します。
1. 結果のパネルから **EZOfficeInventory** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-ezofficeinventory"></a>EZOfficeInventory の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、EZOfficeInventory に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと EZOfficeInventory の関連ユーザーとの間にリンク関係を確立する必要があります。

EZOfficeInventory に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[EZOfficeInventory の SSO の構成](#configure-ezofficeinventory-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[EZOfficeInventory のテスト ユーザーの作成](#create-ezofficeinventory-test-user)** - EZOfficeInventory で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **EZOfficeInventory** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[EZOfficeInventory クライアント サポート チーム](mailto:support@ezofficeinventory.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. EZOfficeInventory アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、EZOfficeInventory アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | first_name | User.givenname |
    | last_name | User.surname |
    | email | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[EZOfficeInventory のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に EZOfficeInventory へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[EZOfficeInventory]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ezofficeinventory-sso"></a>EZOfficeInventory の SSO の構成

1. EZOfficeInventory 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[EZOfficeInventory のセットアップ]** をクリックすると、EZOfficeInventory アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して EZOfficeInventory にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. EZOfficeInventory を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として EZOfficeInventory 企業サイトにサインインして、次の手順を実行します。

1. ページの右上隅の **[Profile]\(プロファイル\)** をクリックし、 **[Settings]\(設定\)**  >  **[Add Ons]\(アドオン\)** に移動します。

    ![[Add Ons]\(アドオン\) アクションが選択されている [Settings]\(設定\) ページを示すスクリーンショット。](./media/ezofficeinventory-tutorial/settings.png)

1. 下へスクロールして **[SAML Integration]\(SAML 統合\)** セクションに移動し、次の手順に従います。

    ![EZOfficeInventory の構成](./media/ezofficeinventory-tutorial/integration.png)

    a. **[Enabled]\(有効\)** オプションをオンにします。

    b. **[Identity Provider URL]\(ID プロバイダーの URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[Identity Provider Certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。

    d. **[Login Button Text]\(ログイン ボタン テキスト\)** ボックスに、ログイン ボタンのテキストを入力します。

    e. **[First Name]\(名\)** ボックスに「**first_name**」と入力します。

    f. **[Last Name]\(姓\)** ボックスに「**last_name**」と入力します。

    g. **[Email]\(メール\)** ボックスに「**email**」と入力します。

    h. **[EZOfficeInventory Role By default]\(既定の EZOfficeInventory ロール\)** オプションから要件に従って自分のロールを選択します。

    i. **[Update]** をクリックします。

### <a name="create-ezofficeinventory-test-user"></a>EZOfficeInventory のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを EZOfficeInventory に作成します。 EZOfficeInventory では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 EZOfficeInventory にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる EZOfficeInventory のサインオン URL にリダイレクトされます。 

* EZOfficeInventory のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [EZOfficeInventory] タイルをクリックすると、EZOfficeInventory のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

EZOfficeInventory を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
