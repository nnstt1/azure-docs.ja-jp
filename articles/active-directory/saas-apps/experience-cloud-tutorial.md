---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Experience Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Experience Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: b2a3160f18177fb9806fb679d83473c3ac290971
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285742"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-experience-cloud"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Experience Cloud の統合

このチュートリアルでは、Experience Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 Experience Cloud と Azure AD を統合すると、次のことができます。

* Experience Cloud にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Experience Cloud に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Experience Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Experience Cloud では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Experience Cloud を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。


## <a name="adding-experience-cloud-from-the-gallery"></a>ギャラリーからの Experience Cloud の追加

Azure AD への Experience Cloud の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Experience Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Experience Cloud**」と入力します。
1. 結果のパネルから **[Experience Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-experience-cloud"></a>Experience Cloud の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Experience Cloud に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Experience Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

Experience Cloud で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Experience Cloud SSO の構成](#configure-experience-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Experience Cloud のテスト ユーザーの作成](#create-experience-cloud-test-user)** - Experience Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Experience Cloud** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。
    
    ```http
    https://<cluster>.medallia.com/sso/<company>
    https://<cluster>.medallia.ca/sso/<company>
    https://<cluster>.medallia.eu/sso/<company>
    https://<cluster>.medallia.au/sso/<company>
    ```

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://<cluster>.medallia.com/sso/<company>/logonSubmit.do
    https://<cluster>.medallia.ca/sso/<company>/logonSubmit.do
    https://<cluster>.medallia.eu/sso/<company>/logonSubmit.do
    https://<cluster>.medallia.au/sso/<company>/logonSubmit.do
    ```

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://<cluster>.medallia.com/sso/<company>
    https://<cluster>.medallia.ca/sso/<company>
    https://<cluster>.medallia.eu/sso/<company>
    https://<cluster>.medallia.au/sso/<company>
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Experience Cloud クライアント サポート チーム](mailto:support@medallia.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Experience Cloud のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Experience Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Experience Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-experience-cloud-sso"></a>Experience Cloud SSO の構成

**Experience Cloud** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Experience Cloud サポート チーム](mailto:support@medallia.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-experience-cloud-test-user"></a>Experience Cloud のテスト ユーザーの作成

このセクションでは、Experience Cloud で B.Simon というユーザーを作成します。 [Experience Cloud サポート チーム](mailto:support@medallia.com)と連携して、Experience Cloud プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Experience Cloud] タイルをクリックすると、SSO を設定した Experience Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Experience Cloud を試す](https://aad.portal.azure.com/)

- [Microsoft Defender for Cloud Apps でのセッション制御とは](/cloud-app-security/proxy-intro-aad)
