---
title: 'チュートリアル: Azure AD SSO と BenSelect の統合'
description: Azure Active Directory と BenSelect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: db5bf1678d64bb90466ba644c6ad70093bcc09e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349207"
---
# <a name="tutorial-azure-ad-sso-integration-with-benselect"></a>チュートリアル: Azure AD SSO と BenSelect の統合

このチュートリアルでは、BenSelect と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と BenSelect を統合すると、次のことができます。

* BenSelect にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BenSelect に自動的にサインインできるようにすることができます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* BenSelect でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* BenSelect では、**IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-benselect-from-the-gallery"></a>ギャラリーからの BenSelect の追加

Azure AD への BenSelect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BenSelect を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**BenSelect**」と入力します。
1. 結果パネルから「**BenSelect**」を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-benselect"></a>BenSelect のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、BenSelect に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと BenSelect の関連ユーザーとの間にリンク関係を確立する必要があります。

BenSelect 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[BenSelect の SSO の構成](#configure-benselect-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[BenSelect テスト ユーザーの作成](#create-benselect-test-user)** - Azure AD でのユーザーにリンクされた、BenSelect での B.Simon の対応するユーザーを作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **BenSelect** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[応答 URL]** ボックスに、`https://www.benselect.com/enroll/login.aspx?Path=<tenant name>` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 値を取得するには、[BenSelect クライアント サポート チーム](mailto:support@selerix.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. BenSelect アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![スクリーンショットは [ユーザー属性] を示しています。[Givenname]\(名\) の user.givenname や [Emailaddress]\(メール アドレス\) の user.mail など、既定の属性が表示されています。](common/edit-attribute.png)

1. **[編集]** アイコンをクリックして、 **[名前識別子の値]** を編集します。

    ![スクリーンショットは、[編集] アイコンが選択されている [ユーザー属性とクレーム] ペインを示しています。](media/benselect-tutorial/mail-prefix1.png)

1. **[ユーザー要求の管理]** セクションで、以下の手順を実行します。

    ![スクリーンショットは、この手順で説明した値を入力できる [ユーザー要求の管理] を示しています。](media/benselect-tutorial/mail-prefix2.png)

    a. **[ソース]** として **[変換]** を選択します。

    b. **[変換]** ドロップダウン リストで、 **[ExtractMailPrefix()]** を選択します。

    c. **[パラメーター 1]** ドロップダウン リストで、 **[user.userprincipalname]** を選択します。

    d. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[BenSelect のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に BenSelect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[BenSelect]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-benselect-sso"></a>BenSelect SSO の構成

**BenSelect** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (未加工)** と Azure portal からコピーした適切な URL を [BenSelect サポート チーム](mailto:support@selerix.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!NOTE]
> 適切なサーバー (app2101 など) で SSO を設定するために、この統合には SHA256 アルゴリズム (SHA1 はサポート対象外) が必要であるという点を伝える必要があります。

### <a name="create-benselect-test-user"></a>BenSelect テスト ユーザーの作成

このセクションでは、BenSelect で Britta Simon というユーザーを作成します。 [BenSelect サポート チーム](mailto:support@selerix.com)と協力して、BenSelect プラットフォームでユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した BenSelect に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [BenSelect] タイルをクリックすると、SSO を設定した BenSelect に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

BenSelect を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
