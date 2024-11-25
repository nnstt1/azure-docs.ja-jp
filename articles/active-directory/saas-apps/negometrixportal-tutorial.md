---
title: チュートリアル:Azure AD SSO と NegometrixPortal Single Sign On (SSO) の統合
description: Azure Active Directory と NegometrixPortal Single Sign On (SSO) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2021
ms.author: jeedes
ms.openlocfilehash: 53704c5d132ae2943cc8fd2a0c577e2a58cd0a06
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294493"
---
# <a name="tutorial-azure-ad-sso-integration-with-negometrixportal-single-sign-on-sso"></a>チュートリアル:Azure AD SSO と NegometrixPortal Single Sign On (SSO) の統合

このチュートリアルでは、NegometrixPortal Single Sign On (SSO) と Azure Active Directory (Azure AD) を統合する方法について説明します。 NegometrixPortal Single Sign On (SSO) を Azure AD と統合すると、次のことができます。

* NegometrixPortal Single Sign On (SSO) にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して NegometrixPortal Single Sign On (SSO) に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* NegometrixPortal Single Sign On (SSO) シングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* NegometrixPortal Single Sign On (SSO) では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-negometrixportal-single-sign-on-sso-from-the-gallery"></a>ギャラリーから NegometrixPortal Single Sign On (SSO) を追加する

Azure AD への NegometrixPortal Single Sign On (SSO) の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に NegometrixPortal Single Sign On (SSO) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**NegometrixPortal Single Sign On (SSO)** 」と入力します。
1. 結果パネルから **[NegometrixPortal Single Sign-on (SSO)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-negometrixportal-single-sign-on-sso"></a>NegometrixPortal Single Sign On (SSO) に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、NegometrixPortal Single Sign On (SSO) に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと NegometrixPortal Single Sign On (SSO) の関連ユーザーとの間にリンク関係を確立する必要があります。

NegometrixPortal Single Sign On (SSO) に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[NegometrixPortal Single Sign On (SSO) のシングル サインオンの構成](#configure-negometrixportal-single-sign-on-sso-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[NegometrixPortal Single Sign On (SSO) テスト ユーザーの作成](#create-negometrixportal-single-sign-on-sso-test-user)** - NegometrixPortal Single Sign On (SSO) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **NegometrixPortal Single Sign On (SSO)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://portal.negometrix.com/sso/<CUSTOMURL>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[NegometrixPortal Single Sign On (SSO) クライアント サポート チーム](mailto:sander.hoek@negometrix.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. NegometrixPortal Single Sign On (SSO) アプリケーションは特定の形式の SAML アサーションを予測しているため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、NegometrixPortal Single Sign On (SSO) アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------|  --------- |
    | upn | user.userprincipalname |

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

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

このセクションでは、B.Simon に NegometrixPortal Single Sign On (SSO) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[NegometrixPortal Single Sign On (SSO)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-negometrixportal-single-sign-on-sso-sso"></a>NegometrixPortal Single Sign On (SSO) のシングル サインオンを構成する

**NegometrixPortal Single Sign On (SSO)** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [NegometrixPortal Single Sign On (SSO) サポートチーム](mailto:sander.hoek@negometrix.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-negometrixportal-single-sign-on-sso-test-user"></a>NegometrixPortal Single Sign On (SSO) のテスト ユーザーを作成する

このセクションでは、NegometrixPortal Single Sign On (SSO) で B.Simon というユーザーを作成します。 [NegometrixPortal Single Sign On (SSO) のサポートチーム](mailto:sander.hoek@negometrix.com)と連携し、NegometrixPortal Single Sign On (SSO) プラットフォームでユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる NegometrixPortal Single Sign On (SSO) のサインオン URL にリダイレクトされます。 

* NegometrixPortal Single Sign On (SSO) のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイアプリで [NegometrixPortal Single Sign On (SSO)] タイルをクリックすると、NegometrixPortal Single Sign On (SSO) のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

NegometrixPortal Single Sign On (SSO) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
