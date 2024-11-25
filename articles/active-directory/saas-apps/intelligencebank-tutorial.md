---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と IntelligenceBank の統合 | Microsoft Docs
description: Azure Active Directory と IntelligenceBank の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 1721b14717b055d0e6f305a3e24efb177cf2f7b9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313873"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と IntelligenceBank の統合

このチュートリアルでは、IntelligenceBank と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と IntelligenceBank を統合すると、次のことができます。

* IntelligenceBank にアクセスする Azure AD を制御します。
* ユーザーが自分の Azure AD アカウントを使用して IntelligenceBank に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* IntelligenceBank でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* IntelligenceBank では、**SP** で開始される SSO がサポートされます。

## <a name="add-intelligencebank-from-the-gallery"></a>ギャラリーからの IntelligenceBank の追加

Azure AD への IntelligenceBank の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IntelligenceBank を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**IntelligenceBank**」と入力します。
1. 結果のパネルから **[IntelligenceBank]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-intelligencebank"></a>IntelligenceBank の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、IntelligenceBank に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと IntelligenceBank の関連ユーザーとの間にリンク関係を確立する必要があります。

IntelligenceBank に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[IntelligenceBank の構成](#configure-intelligencebank-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[IntelligenceBank テスト ユーザーの作成](#create-intelligencebank-test-user)** - IntelligenceBank で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **IntelligenceBank** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | **Identifier** |
    |-----|
    | `IB` |
    | `IntelligenceBank` |
    | `https://<SUBDOMAIN>.intelligencebank.com` |

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.intelligencebank.com/auth` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.intelligencebank.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[IntelligenceBank クライアント サポート チーム](mailto:helpdesk@intelligencebank.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[IntelligenceBank のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に IntelligenceBank へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[IntelligenceBank]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-intelligencebank-sso"></a>IntelligenceBank SSO の構成

1. 別の Web ブラウザー ウィンドウで、IntelligenceBank 企業サイトに管理者としてサインインします。

1. **[Authenticator]** をクリックし、 **[新規追加]** をクリックします。

    ![選択された [Administrator]\(管理者\) タブと [Add New]\(新規追加\) アイコンを示すスクリーンショット。](./media/intelligencebank-tutorial/authenticator.PNG)

1. 次の手順に従います。

    ![この手順の情報を入力するフィールドを示すスクリーンショット。](./media/intelligencebank-tutorial/fields.PNG)

    a. **[名前]** ボックスに、名前 (`azureadsso` など) を入力します。

    b. **[説明]** ボックスに確かな説明を入力します

    c. **[種類]** として、ドロップダウンから **[SAML]** を選択します。

    d. **[リモート URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. **[ホスト]** ボックスに、Azure portal からコピーした **エンティティ ID** の値を貼り付けます。

    f. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[CertData] (証明書データ)** ボックスに貼り付けます。

    g. **[SingleLogoutService]** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

    h. **[保存]** ボタンをクリックします。

### <a name="create-intelligencebank-test-user"></a>IntelligenceBank テスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、IntelligenceBank 企業サイトに管理者としてサインインします。

1. **[管理]**  ->  **[ユーザー]** と移動し、 **[新しいユーザーの追加]** アイコンを選択して **ユーザー** を追加します。

    ![[Users]\(ユーザー\) タブで [Users]\(ユーザー\) アイコンが選択されているスクリーンショット。](./media/intelligencebank-tutorial/creating-user.PNG)

1. 組織の要件に従って必要なフィールドに入力し、 **[保存]** をクリックします。

    ![ユーザー情報を入力する [Add New User]\(新しいユーザーの追加\) ページを示すスクリーンショット。](./media/intelligencebank-tutorial/user.PNG)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる IntelligenceBank のサインオン URL にリダイレクトされます。 

* IntelligenceBank のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [IntelligenceBank] タイルをクリックすると、IntelligenceBank のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

IntelligenceBank を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
