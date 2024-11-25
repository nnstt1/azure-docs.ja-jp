---
title: 'チュートリアル: Azure AD SSO と Chargebee の統合'
description: Azure Active Directory と Chargebee の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: d70072c66ae3ea7e655e5de4c245aca276e8dec8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296473"
---
# <a name="tutorial-azure-ad-sso-integration-with-chargebee"></a>チュートリアル: Azure AD SSO と Chargebee の統合

このチュートリアルでは、Chargebee と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Chargebee を統合すると、次のことができます。

* Chargebee にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Chargebee に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Chargebee でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Chargebee では、**SP と IDP** Initiated SSOがサポートされます。

## <a name="add-chargebee-from-the-gallery"></a>ギャラリーから Chargebee を追加する

Azure AD への Chargebee の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Chargebee を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Chargebee**」と入力します。
1. 結果のパネルから **[Chargebee]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-chargebee"></a>Chargebee に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Chargebee に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Chargebee の関連ユーザーの間で、リンク関係を確立する必要があります。

Chargebee に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Chargebee の SSO の構成](#configure-chargebee-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Chargebee のテスト ユーザーの作成](#create-chargebee-test-user)** - Chargebee で B.Simon に対応するユーザーを作成し、Chargebee の B.Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Chargebee** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<domainname>.chargebee.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.chargebee.com/saml/<domainname>/acs` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<domainname>.chargebee.com` という形式で URL を入力します。

    > [!NOTE]
    > `<domainname>` は、アカウントの要求後にユーザーが作成するドメインの名前です。 その他の情報については、[Chargebee クライアント サポート チーム](mailto:support@chargebee.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Set up Chargebee]\(Chargebee の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Chargebee へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Chargebee]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-chargebee-sso"></a>Chargebee の SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、Chargebee 企業サイトに管理者としてサインインします。

4. メニューの左側から **[設定]**  >  **[セキュリティ]**  >  **[管理]** の順にクリックします。

    ![スクリーンショットは、[設定]、[セキュリティ]、および [管理] が選択されている Chargebee 企業サイトを示しています。](./media/chargebee-tutorial/security.png)

5. **[シングル サインオン]** ポップアップで、次の手順を実行します。

    ![スクリーンショットは [シングル サインオン] ダイアログ ボックスを示しています。[SAML] が選択され、確認が必要なオプションが表示されています。](./media/chargebee-tutorial/settings.png)

    a. **[SAML]** を選択します。

    b. **[ログイン URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[SAML 証明書]** テキスト ボックスに貼り付けます。

    d. **[Confirm]\(確認\)** をクリックします。

### <a name="create-chargebee-test-user"></a>Chargebee のテスト ユーザーの作成

Azure AD ユーザーが Chargebee にサインインできるようにするには、ユーザーを Chargebee にプロビジョニングする必要があります。 Chargebee では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 別の Web ブラウザー ウィンドウで、Chargebee にセキュリティ管理者としてサインインします。

2. メニューの左側の **[Customers]\(顧客\)** をクリックし、 **[Create a New Customer]\(新しい顧客の作成\)** に移動します。

    ![スクリーンショットは Chargebee サイトを示しています。[Customers]\(顧客\) および [Create a New Customer]\(新しい顧客の作成\) が選択されています。](./media/chargebee-tutorial/menu.png)

3. **[New Customer]\(新しい顧客\)** ページで、以下に示した各フィールドの内容を入力し、 **[Create Customer]\(顧客の作成\)** をクリックしてユーザーを作成します。

    ![スクリーンショットは、顧客情報を入力できる [New Customer]\(新しい顧客\) ページを示しています。](./media/chargebee-tutorial/customers.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Chargebee のサインオン URL にリダイレクトされます。  

* Chargebee のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Chargebee に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Chargebee] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Chargebee に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Chargebee を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
