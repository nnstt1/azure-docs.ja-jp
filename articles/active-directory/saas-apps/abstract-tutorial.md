---
title: 'チュートリアル: Azure Active Directory と Abstract の統合 | Microsoft Docs'
description: Azure Active Directory と Abstract の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: f7511c6edd8f0e52a1e1f51110ed5e558c988286
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292473"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>チュートリアル: Azure Active Directory と Abstract の統合

このチュートリアルでは、Abstract と Azure Active Directory (Azure AD) を統合する方法について説明します。 Abstract を Azure AD と統合すると、次のことが可能になります。

* Abstract にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Abstract に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Abstract でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Abstract では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-abstract-from-the-gallery"></a>ギャラリーからの Abstract の追加

Azure AD への Abstract の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Abstract を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Abstract**」と入力します。
1. 結果のパネルから **[Abstract]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-abstract"></a>Abstract の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Abstract に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Abstract の関連ユーザーとの間にリンク関係を確立する必要があります。

Abstract に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Abstract SSO の構成](#configure-abstract-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Abstract テスト ユーザーの作成](#create-abstract-test-user)** - Abstract で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Abstract** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.abstract.com/signin`」と入力します。

4. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Abstract へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Abstract]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-abstract-sso"></a>Abstract SSO の構成

Abstract で SSO を構成するために必要になるので、Azure portal から `App Federation Metadata Url` と `Azure AD Identifier` を取得しておきます。

これらの情報は、**[SAML でシングル サインオンをセットアップします]** ページに表示されます。

* `App Federation Metadata Url` は、**[SAML 署名証明書]** セクションにあります。
* `Azure AD Identifier` は、**[Abstract の設定]** セクションにあります。

これで、Abstract 上で SSO を構成する準備ができました。

>[!Note]
>Abstract の SSO 設定にアクセスするには、組織の管理者アカウントを使用して認証を行う必要があります。

1. [Abstract Web アプリ](https://app.abstract.com/)を開きます。
2. 左側のバーにある **[Permissions]\(アクセス許可\)** ページに移動します。
3. **[Configure SSO]\(SSO の構成\)** セクションで、**[Metadata URL]\(メタデータ URL\)** と **[Entity ID]\(エンティティ ID\)** を入力します。
4. 手動による例外がある場合は、それを入力します。 手動による例外セクションに一覧表示されるメール アドレスでは、SSO がバイパスされ、そのメール アドレスとパスワードでログインできるようになります。 
5. **[変更を保存]** をクリックします。

>[!Note] 
>手動による例外の一覧には、プライマリ メール アドレスを使用する必要があります。 一覧表示されているメール アドレスがユーザーのセカンダリ メールである場合、SSO のアクティブ化は失敗します。 その場合は、失敗したアカウントのプライマリ メール アドレスを含むエラー メッセージが表示されます。 お客様がそのユーザーを知っていると確認した後に、そのプライマリ メール アドレスを手動による例外一覧に追加します。

### <a name="create-abstract-test-user"></a>Abstract テスト ユーザーの作成

Abstract で SSO をテストするには:

1. [Abstract Web アプリ](https://app.abstract.com/)を開きます。
2. 左側のバーにある **[Permissions]\(アクセス許可\)** ページに移動します。
3. **[Test with my Account]\(自分のアカウントでテストする\)** をクリックします。 テストが失敗する場合は、[Microsoft のサポート チーム](https://help.abstract.com/hc/)にお問い合わせください。

>[!Note]
>Abstract の SSO 設定にアクセスするには、組織の管理者アカウントを使用して認証を行う必要があります。
この組織の管理者アカウントは、Azure portal 上で Abstract に割り当てる必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Abstract のサインオン URL にリダイレクトされます。  

* Abstract のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Abstract に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Abstract] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Abstract に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Abstract を構成すると、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
