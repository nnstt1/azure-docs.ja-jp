---
title: 'チュートリアル: Azure Active Directory と LoginRadius の統合 | Microsoft Docs'
description: Azure Active Directory と LoginRadius の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 1774e5f7099d130fe1692911fbcf1f7794fa7e20
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285121"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>チュートリアル: Azure Active Directory と LoginRadius の統合

このチュートリアルでは、LoginRadius と Azure Active Directory (Azure AD) を統合する方法について説明します。 LoginRadius を Azure AD に統合すると、次のことができます。

* LoginRadius にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して LoginRadius に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

LoginRadius と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LoginRadius でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LoginRadius では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-loginradius-from-the-gallery"></a>ギャラリーからの LoginRadius の追加

Azure AD への LoginRadius の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LoginRadius を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LoginRadius**」と入力します。
1. 結果のパネルから **[LoginRadius]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-loginradius"></a>LoginRadius の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LoginRadius に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと LoginRadius の関連ユーザーとの間にリンク関係を確立する必要があります。

LoginRadius に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LoginRadius SSO の構成](#configure-loginradius-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LoginRadius のテスト ユーザーの作成](#create-loginradius-test-user)** - LoginRadius で B.Simon に対応するユーザーを作成し、Azure AD のユーザーの表現にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LoginRadius** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

   1. **[識別子 (エンティティ ID)]** ボックスに URL (`https://lr.hub.loginradius.com/`) を入力します。

   1. **[応答 URL (Assertion Consumer Service URL)]** ボックスに、LoginRadius の ACS URL (`https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx`) を入力します。 

   1. **[サインオン URL]** ボックスに URL (`https://secure.loginradius.com/login`) を入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[LoginRadius のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B. Simon に LoginRadius へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LoginRadius]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-loginradius-sso"></a>LoginRadius SSO の構成

このセクションでは、LoginRadius 管理コンソール上で Azure AD のシングル サインオンを有効にします。

1. LoginRadius [管理コンソール](https://adminconsole.loginradius.com/login) アカウントにログインします。

2. [LoginRadius 管理コンソール](https://secure.loginradius.com/account/team)の **[Team Management]\(チーム管理\)** セクションに移動します。

3. **[Single Sign-On]\(シングル サインオン\)** タブを選択し、 **[Azure AD]** を選択します。

   ![LoginRadius の [Team Management]\(チーム管理\) コンソールにあるシングル サインオン メニューを示すスクリーンショット](./media/loginradius-tutorial/azure-ad.png)

4. Azure AD のセットアップ ページで、次の手順を実行します。

   ![LoginRadius の [Team Management]\(チーム管理\) コンソールにある Azure Active Directory の構成を示すスクリーンショット](./media/loginradius-tutorial/single-sign-on.png)

    1. **[ID Provider Location]\(ID プロバイダーの場所\)** に、Azure AD アカウントから取得したサインオン エンドポイントを入力します。

    1. **[ID Provider Logout URL]\(ID プロバイダー ログアウト URL\)** に、Azure AD アカウントから取得したサインアウト エンドポイントを入力します。
 
    1. **[ID Provider Certificate]\(ID プロバイダー証明書\)** に、Azure AD アカウントから取得した Azure AD の証明書を入力します。 ヘッダーとフッターを付けて証明書の値を入力します。 例: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. **[Service Provider Certificate]\(サービス プロバイダー証明書\)** と **[Server Provider Certificate Key]\(サーバー プロバイダー証明書キー\)** に、自分の証明書とキーを入力します。 

       自己署名証明書は、コマンド ラインから次のコマンドを実行して作成できます (Linux または Mac)。

       - SP の証明書キーを取得するコマンド: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - SP の証明書を取得するコマンド: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > 証明書と証明書キーの値は、必ずヘッダーとフッターを付けて入力してください。
       > - 証明書の値の形式 (入力例): `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - 証明書キーの値の形式 (入力例): `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. **[Data Mapping]\(データ マッピング\)** セクションでフィールド (SP フィールド) を選択し、対応する Azure AD フィールド (IdP フィールド) を入力します。

    次の表は、Azure AD に使用されるいくつかのフィールド名の一覧です。

    | フィールド    | プロファイル キー                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > **Email** フィールドのマッピングは必須です。 **FirstName** フィールドと **LastName** フィールドのマッピングは省略可能です。

### <a name="create-loginradius-test-user"></a>LoginRadius のテスト ユーザーの作成

1. LoginRadius [管理コンソール](https://adminconsole.loginradius.com/login) アカウントにログインします。

2. LoginRadius 管理コンソールのチーム管理セクションに移動します。

   ![LoginRadius 管理コンソールを示すスクリーンショット](./media/loginradius-tutorial/team-management.png)

3. サイド メニューの **[Add Team Member]\(チーム メンバーの追加\)** を選択してフォームを開きます。 

4. **[Add Team Member]\(チーム メンバーの追加\)** フォームで、LoginRadius サイトに Britta Simon というユーザーを作成します。このユーザーの詳細を入力し、必要なアクセス許可を割り当ててください。 ロールに基づくアクセス許可の詳細については、LoginRadius の「[チーム メンバーの管理](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)」ドキュメントの「[ロールのアクセス権](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)」セクションを参照してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、マイ アプリを使用して Azure AD のシングル サインオン構成をテストします。

1. ブラウザーで https://accounts.loginradius.com/auth.aspx に移動し、 **[Fed SSO log in]\(フェデレーション SSO ログイン\)** を選択します。
2. ご利用の LoginRadius アプリの名前を入力し、 **[Login]\(ログイン\)** を選択します。
3. Azure AD アカウントへのサインインを求めるポップアップが表示されます。
4. 認証後、ポップアップが閉じて LoginRadius 管理コンソールにログインされます。

## <a name="next-steps"></a>次のステップ

LoginRadius を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
