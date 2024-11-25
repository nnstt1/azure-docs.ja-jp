---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と OfficeSpace Software の統合 | Microsoft Docs
description: Azure Active Directory と OfficeSpace Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 2dcea2ed649af14bd7e84dbcd6dc17707935640f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300392"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と OfficeSpace Software の統合

このチュートリアルでは、OfficeSpace Software と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と OfficeSpace Software を統合すると、次のことができます。

* OfficeSpace Software にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して OfficeSpace Software に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) 対応の OfficeSpace Software サブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* OfficeSpace Software では、**SP** によって開始される SSO がサポートされます。
* OfficeSpace Software では、[**自動化されたユーザー プロビジョニングとプロビジョニング解除**](officespace-software-provisioning-tutorial.md) (推奨) がサポートされます。
* OfficeSpace Software では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-officespace-software-from-the-gallery"></a>ギャラリーから OfficeSpace Software を追加する

Azure AD への OfficeSpace Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OfficeSpace Software を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**OfficeSpace Software**」と入力します。
1. 結果のパネルから **OfficeSpace Software** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-officespace-software"></a>OfficeSpace Software の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、OfficeSpace Software に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと OfficeSpace Software の関連ユーザーとの間にリンク関係を確立する必要があります。

OfficeSpace Software に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[OfficeSpace Software の SSO の構成](#configure-officespace-software-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[OfficeSpace Software のテスト ユーザーの作成](#create-officespace-software-test-user)** - OfficeSpace Software で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **OfficeSpace Software** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`<company name>.officespacesoftware.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これの値の取得については、[OfficeSpace Software クライアント サポート チーム](mailto:support@officespacesoftware.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. OfficeSpace Software アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 OfficeSpace Software アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. その他に、OfficeSpace Software アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | email | User.mail |
    | name | user.displayname |
    | first_name | User.givenname |
    | last_name | User.surname |

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[Thumbprint Value]\(拇印の値\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[OfficeSpace Software のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に OfficeSpace Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[OfficeSpace Software]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-officespace-software-sso"></a>OfficeSpace Software の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として OfficeSpace Software テナントにサインインします。

2. **[設定]** に移動して、 **[コネクタ]** をクリックします。

    ![[Settings]\(設定\) ドロップダウンで [Connectors]\(コネクタ\) が選択されているスクリーンショット。](./media/officespace-tutorial/settings.png)

3. **[SAML 認証]** をクリックします。

    ![[SAML Authentication]\(SAML 認証\) アクションが選択されている [Authentication]\(認証\) セクションを示すスクリーンショット。](./media/officespace-tutorial/authentication.png)

4. **[SAML Authentication]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/officespace-tutorial/configuration.png)

    a. **[Logout provider url]\(ログアウト プロバイダー URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    b. **[Client idp target url]\(クライアント Idp ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. Azure Portal からコピーした **[拇印]** の値を **[Client IDP certificate fingerprint]\(クライアント IDP 証明書フィンガープリント\)** ボックスに貼り付けます。 

    d. **[設定の保存]** をクリックします。

### <a name="create-officespace-software-test-user"></a>OfficeSpace Software のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを OfficeSpace Software に作成します。 OfficeSpace Software では、Just-In-Time ユーザー プロビジョニングがサポートされます。この設定は既定で有効です。 このセクションでは、ユーザー側で必要な操作はありません。 OfficeSpace Software にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[OfficeSpace Software サポート チーム](mailto:support@officespacesoftware.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる OfficeSpace Software のサインオン URL にリダイレクトされます。 

* OfficeSpace Software のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [OfficeSpace Software] タイルをクリックすると、OfficeSpace Software サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

OfficeSpace Software を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
