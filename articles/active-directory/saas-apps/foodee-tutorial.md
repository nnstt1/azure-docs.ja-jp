---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Foodee の統合 | Microsoft Docs'
description: Azure Active Directory と Foodee の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 84cb6886c802208d414cb3b8f5281971e0b1d8aa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317866"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-foodee"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Foodee の統合

このチュートリアルでは、Foodee と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Foodee を統合すると、次のことができます。

* Foodee にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Foodee に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Foodee サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Foodee では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* Foodee では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* Foodee では、[自動化されたユーザー プロビジョニング](foodee-provisioning-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-foodee-from-the-gallery"></a>ギャラリーからの Foodee の追加

Azure AD への Foodee の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Foodee を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Foodee**」と入力します。
1. 結果のパネルから **[Foodee]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-foodee"></a>Foodee の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Foodee に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Foodee の関連ユーザーとの間にリンク関係を確立する必要があります。

Foodee に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Foodee SSO の構成](#configure-foodee-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Foodee のテスト ユーザーの作成](#create-foodee-test-user)** - Foodee で B. Simon に対応するユーザーを作成し、Azure AD の B. Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Foodee** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://concierge.food.ee/sso/saml/<INSTANCENAME>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 この値を取得するには、[Foodee クライアント サポート チーム](mailto:dev@food.ee)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Foodee]\(Foodee の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Foodee へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Foodee]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-foodee-sso"></a>Foodee の SSO の構成

1. Foodee 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、**[Foodee のセットアップ]** をクリックすると、Foodee アプリケーションに移動します。 そこから、管理者の資格情報を入力して Foodee にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 4 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Foodee を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Foodee 企業サイトにサインインして、次の手順を実行します。

4. ページの右上隅の **プロファイル ロゴ** をクリックし、**[Single Sign On]\(シングル サインオン\)** に移動して以下の手順を実行します。

   ![Foodee の構成](./media/foodee-tutorial/profile-logo.png)

   1. **[IDP NAME]\(IDP 名\)** ボックスに「Azure」のような名前を入力します。
   1. フェデレーション メタデータ XML をメモ帳で開き、その内容をコピーして **[IDP METADATA XML]\(IDP メタデータ XML\)** ボックスに貼り付けます。
   1. **[保存]** をクリックします。

### <a name="create-foodee-test-user"></a>Foodee のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Foodee に作成します。 Foodee では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Foodee に存在しない場合は、Foodee にアクセスしようとしたときに新しいユーザーが作成されます。

Foodee では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./foodee-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

SP Initiated:

* Azure portal で [このアプリケーションをテストします] をクリックします。 これにより、ログイン フローを開始できる Foodee のサインオン URL にリダイレクトされます。

* Foodee のサインオン URL に直接移動し、そこからログイン フローを開始します。

IDP Initiated:

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Foodee に自動的にサインインします

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Foodee] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Foodee に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Foodee を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
