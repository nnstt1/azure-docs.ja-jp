---
title: 'チュートリアル: Azure Active Directory と ON24 Virtual Environment SAML Connection の統合 | Microsoft Docs'
description: Azure Active Directory と ON24 Virtual Environment SAML Connection の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/14/2021
ms.author: jeedes
ms.openlocfilehash: 654673a394550d396e08a8de5e7914befddb480b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330010"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>チュートリアル: Azure Active Directory と ON24 Virtual Environment SAML Connection の統合

このチュートリアルでは、ON24 Virtual Environment SAML Connection と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と ON24 Virtual Environment SAML Connection を統合すると、次のことができます。

* ON24 Virtual Environment SAML Connection へのアクセス権を持つユーザーを Azure AD 内で制御します。
* ユーザーが自分の Azure AD アカウントを使用して自動的に ON24 Virtual Environment SAML Connection にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ON24 Virtual Environment SAML Connection シングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ON24 Virtual Environment SAML Connection では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-on24-virtual-environment-saml-connection-from-the-gallery"></a>ギャラリーからの ON24 Virtual Environment SAML Connection の追加

Azure AD への ON24 Virtual Environment SAML Connection の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ON24 Virtual Environment SAML Connection を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ON24 Virtual Environment SAML Connection**」と入力します。
1. 結果のパネルから **[ON24 Virtual Environment SAML Connection]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-on24-virtual-environment-saml-connection"></a>ON24 Virtual Environment SAML Connection の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ON24 Virtual Environment SAML Connection に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと ON24 Virtual Environment SAML Connection の関連ユーザーとの間にリンク関係を確立する必要があります。

ON24 Virtual Environment SAML Connection に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ON24 Virtual Environment SAML Connection の SSO の構成](#configure-on24-virtual-environment-saml-connection-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[ON24 Virtual Environment SAML Connection テスト ユーザーの作成](#create-on24-virtual-environment-saml-connection-test-user)** - ON24 Virtual Environment SAML Connection 上で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ON24 Virtual Environment SAML Connection** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、次のいずれかの値を入力します。

    | **運用環境 URL** |
    |------|
    | `SAML-VSHOW.on24.com` |
    | `SAML-Gateway.on24.com` |
    | `SAP PROD SAML-EliteAudience.on24.com` |
    |
                
    | **QA 環境 URL** |
    |-----|
    | `SAMLQA-VSHOW.on24.com` |
    | `SAMLQA-Gateway.on24.com` |
    | `SAMLQA-EliteAudience.on24.com` |
    |

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。

    | **運用環境 URL** |
    |-----|
    | `https://federation.on24.com/sp/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2` |
    |

    | **QA 環境 URL** |
    |-------|
    | `https://qafederation.on24.com/sp/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` |
    |

    c. **[追加の URL を設定します]** をクリックします。 

    d. **[リレー状態]** ボックスに、`https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>` のパターンで URL を入力します。

5.  **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://vshow.on24.com/vshow/<INSTANCE_NAME>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のリレー状態とサインオン URL で更新してください。 これらの値を取得するには、[ON24 Virtual Environment SAML Connection クライアント サポート チーム](https://www.on24.com/contact-us/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[ON24 Virtual Environment SAML Connection の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に ON24 Virtual Environment SAML Connection へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[ON24 Virtual Environment SAML Connection]\(ON24 Virtual Environment SAML Connection\)** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-on24-virtual-environment-saml-connection-sso"></a>ON24 Virtual Environment SAML Connection SSO の構成

**ON24 Virtual Environment SAML Connection** 側でシングル サインオンを構成するには、Azure portal からダウンロードした **フェデレーション メタデータ XML** と コピーした適切な URL を [ON24 Virtual Environment SAML Connection サポート チーム](https://www.on24.com/about-us/support/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>ON24 Virtual Environment SAML Connection テスト ユーザーの作成

このセクションでは、ON24 Virtual Environment SAML Connection に Britta Simon というユーザーを作成します。 [ON24 Virtual Environment SAML Connection サポート チーム](https://www.on24.com/about-us/support/)と協力して、ON24 Virtual Environment SAML Connection プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる ON24 Virtual Environment SAML Connection のサインオン URL にリダイレクトされます。  

* ON24 Virtual Environment SAML Connection のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した ON24 Virtual Environment SAML Connection に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [ON24 Virtual Environment SAML Connection] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した ON24 Virtual Environment SAML Connection に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ON24 Virtual Environment SAML Connection を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
