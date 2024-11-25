---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と RightCrowd Workforce Management の統合 | Microsoft Docs
description: Azure Active Directory と RightCrowd Workforce Management の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: d47f2607cd00f34193b327dbb41a3df5abb55e40
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329419"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rightcrowd-workforce-management"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と RightCrowd Workforce Management の統合

このチュートリアルでは、RightCrowd Workforce Management と Azure Active Directory (Azure AD) を統合する方法について説明します。 RightCrowd Workforce Management と Azure AD を統合すると、次のことができます。

* RightCrowd Workforce Management にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで RightCrowd Workforce Management に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* RightCrowd Workforce Management のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。



* RightCrowd Workforce Management では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* RightCrowd Workforce Management では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-rightcrowd-workforce-management-from-the-gallery"></a>ギャラリーからの RightCrowd Workforce Management の追加

Azure AD への RightCrowd Workforce Management の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に RightCrowd Workforce Management を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**RightCrowd Workforce Management**」と入力します。
1. 結果パネルから **RightCrowd Workforce Management** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-rightcrowd-workforce-management"></a>RightCrowd Workforce Management の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、RightCrowd Workforce Management に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと RightCrowd Workforce Management の関連ユーザーとの間にリンク関係を確立する必要があります。

RightCrowd Workforce Management に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[RightCrowd Workforce Management の SSO の構成](#configure-rightcrowd-workforce-management-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[RightCrowd Workforce Management のテスト ユーザーの作成](#create-rightcrowd-workforce-management-test-user)** - RightCrowd Workforce Management で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **RightCrowd Workforce Management** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次のいずれかの URL を入力します。`http://<SUBDOMAIN>.rightcrowdcustomerdomain.com`

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。`https://<SUBDOMAIN>.rightcrowdcustomerdomain.com/RightCrowd/Saml2/Auth/AssertionComsumerService`


1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。`http://<SUBDOMAIN>.rightcrowdcustomerdomain.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[RightCrowd Workforce Management サポート チーム](mailto:info@rightcrowd.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[RightCrowd Workforce Management のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に RightCrowd Workforce Management へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[RightCrowd Workforce Management]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-rightcrowd-workforce-management-sso"></a>RightCrowd Workforce Management の SSO の構成

**RightCrowd Workforce Management** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と、Azure portal からコピーした適切な URL を [RightCrowd Workforce Management サポート チーム](mailto:info@rightcrowd.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-rightcrowd-workforce-management-test-user"></a>RightCrowd Workforce Management のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを RightCrowd Workforce Management に作成します。 RightCrowd Workforce Management では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 RightCrowd Workforce Management にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる RightCrowd Workforce Management のサインオン URL にリダイレクトされます。  

* RightCrowd Workforce Management のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した RightCrowd Workforce Management に自動的にサインインします 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで RightCrowd Workforce Management タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した RightCrowd Workforce Management に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

RightCrowd Workforce Management を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
