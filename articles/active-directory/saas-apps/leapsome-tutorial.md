---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Leapsome の統合 | Microsoft Docs'
description: Azure Active Directory と Leapsome の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: c1db0f6a5167f56517256ee984ab3d20711d9042
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310912"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Leapsome の統合

このチュートリアルでは、Leapsome と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Leapsome を統合すると、次のことができます。

* Leapsome にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Leapsome に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Leapsome のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Leapsome では、**SP と IDP** によって開始される SSO がサポートされます。
* Leapsome では、[自動化されたユーザー プロビジョニング](leapsome-provisioning-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-leapsome-from-the-gallery"></a>ギャラリーから Leapsome を追加する

Azure AD への Leapsome の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Leapsome を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Leapsome**」と入力します。
1. 結果のパネルから **[Leapsome]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-leapsome"></a>Leapsome の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Leapsome に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Leapsome の関連ユーザーとの間にリンク関係を確立する必要があります。

Leapsome に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Leapsome の SSO の構成](#configure-leapsome-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Leapsome テスト ユーザーの作成](#create-leapsome-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを Leapsome で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Leapsome** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://www.leapsome.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login` という形式で URL を入力します。

    > [!NOTE]
    > 上記の [応答 URL] と [サインオン URL] の値は、実際の値ではありません。 これらの値を実際の値に置き換えます。実際の値については後で説明します。

1. Leapsome アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Leapsome アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性 | 名前空間 |
    | ---------------| --------------- | --------- |  
    | firstname | User.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | User.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | 社員の画像への URL | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > 属性 attribute の値は、実際のものではありません。 実際の画像 URL でこの値を更新してください。 この値を取得するには、[Leapsome クライアント サポート チーム](mailto:support@leapsome.com)にお問い合わせください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Leapsome のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Leapsome へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Leapsome]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-leapsome-sso"></a>Leapsome の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Leapsome にセキュリティ管理者としてサインインします。

1. 右上にある設定ロゴをクリックし、**[Admin Settings]\(管理者設定\)** をクリックします。

    ![Leapsome セット](./media/leapsome-tutorial/admin.png)

1. 左側のメニュー バーで **[Single Sign On \(SSO\)]\(シングル サインオン \(SSO\)\)** をクリックし、**[SAML-based single sign-on \(SSO\)]\(SAML ベースのシングル サインオン \(SSO\)\)** ページで、次の手順を実行します。

    ![Leapsome SAML](./media/leapsome-tutorial/settings.png)

    a. **[Enable SAML-based single sign-on]\(SAML ベースのシングル サインオンを有効にする\)** を選択します。

    b. **[Login URL \(point your users here to start login\)]\(ログイン URL \(ログインをスタートする場所としてユーザーにここを案内する\)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    c. **[Reply URL (receives response from your identity provider)]\(応答 URL (ID プロバイダーからの応答をここで受け取る)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキストボックスに貼り付けます。

    d. **[SSO Login URL \(provided by identity provider\)]\(SSO ログイン URL \(ID プロバイダーから提供されたもの\)\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. Azure portal からダウンロードした証明書を `--BEGIN CERTIFICATE and END CERTIFICATE--` コメントなしでコピーして、**[証明書] (ID プロバイダーによって提供されたもの)** テキストボックスに貼り付けます。

    f. **[UPDATE SSO SETTINGS]\(SSO 設定を更新する\)** をクリックします。

### <a name="create-leapsome-test-user"></a>Leapsome テスト ユーザーの作成

このセクションでは、Leapsome で Britta Simon というユーザーを作成します。 [Leapsome クライアント サポート チーム](mailto:support@leapsome.com)と協力して、Leapsome プラットフォームの許可リストに追加する必要があるユーザーまたはドメインを追加します。 ドメインがチームによって追加された場合、ユーザーは Leapsome プラットフォームに自動的にプロビジョニングされます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

Leapsome では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./leapsome-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Leapsome のサインオン URL にリダイレクトされます。  

* Leapsome のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Leapsome に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Leapsome] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Leapsome に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Leapsome を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
