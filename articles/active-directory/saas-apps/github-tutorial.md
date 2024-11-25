---
title: 'チュートリアル: Azure AD SSO と GitHub Enterprise Cloud Organization の統合 | Microsoft Docs'
description: Azure Active Directory と GitHub Enterprise Cloud Organization の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: e6a56077adc6456d5d1ea6d5180a21560e193bb8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285741"
---
# <a name="tutorial-azure-ad-sso-integration-with-a-github-enterprise-cloud-organization"></a>チュートリアル: Azure AD SSO と GitHub Enterprise Cloud Organization の統合

このチュートリアルでは、GitHub Enterprise Cloud **Organization** と Azure Active Directory (Azure AD) を統合する方法について説明します。 GitHub Enterprise Cloud Organization と Azure AD を統合すると、次のことができます。

* GitHub Enterprise Cloud Organization にアクセスできるユーザーを Azure AD で制御する。
* GitHub Enterprise Cloud Organization へのアクセスを 1 つの中央サイト (Azure portal) で管理する。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) に作成された GitHub 組織 ([GitHub Enterprise 課金プラン](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)が必要)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* GitHub では、**SP** Initiated SSO がサポートされます。

* GitHub では、[**自動化された** ユーザー プロビジョニング (組織の招待)](github-provisioning-tutorial.md) がサポートされます。

## <a name="adding-github-from-the-gallery"></a>ギャラリーからの GitHub の追加

Azure AD への GitHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GitHub を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**GitHub**」と入力します。
1. 結果のパネルから **[GitHub Enterprise Cloud - Organization]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-github"></a>GitHub の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、GitHub に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと GitHub の関連ユーザーとの間にリンク関係を確立する必要があります。

GitHub に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[GitHub の SSO の構成](#configure-github-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[GitHub テスト ユーザーの作成](#create-github-test-user)** - GitHub で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **GitHub** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/orgs/<Organization ID>`

    b. **[応答 URL]** ボックスに、`https://github.com/orgs/<Organization ID>/saml/consume` のパターンを使用して URL を入力します

    c. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/orgs/<Organization ID>/sso`

    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際の識別子、応答 URL、サインオン URL にこれらの値を置き換える必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、GitHub 管理者セクションに移動します。

5. GitHub アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、 **[Unique User Identifier (Name ID)]\(一意のユーザー ID (名前 ID)\)** は **user.userprincipalname** にマップされています。 GitHub アプリケーションでは、 **[Unique User Identifier (Name ID)]\(一意のユーザー ID (名前 ID)\)** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![[編集] アイコンが選択されている [ユーザー属性] セクションを示すスクリーンショット。](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[GitHub のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に GitHub へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **GitHub** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。

    ![ユーザー ロール](./media/github-tutorial/user-role.png)

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-github-sso"></a>GitHub の SSO の構成

1. 別の Web ブラウザー ウィンドウで、GitHub 組織サイトに管理者としてサインインします。

2. **[Settings]\(設定\)** に移動し、 **[Security]\(セキュリティ\)** をクリックします。

    ![[セキュリティ] が選択されている GitHub の [Organization settings]\(組織の設定\) メニューを示すスクリーンショット。](./media/github-tutorial/security.png)

3. **[Enable SAML authentication]\(SAML 認証を有効にする\)** チェック ボックスをオンにしてシングル サインオンの構成フィールドを表示し、次の手順を実行します。

    ![[Enable S A M L authentication (S A M L 認証を有効にする)] と U R L のテキスト ボックスが強調表示されている [S A M L single sign-on]\(S A M L シングル サインオン\) セクションを示すスクリーンショット。](./media/github-tutorial/authentication.png)

    a. **シングル サインオン URL** の値をコピーして、この値を、Azure portal の **[基本的な SAML 構成]** の **[サインオン URL]** テキスト ボックスに貼り付けます。
    
    b. **アサーション コンシューマー サービス URL** の値をコピーして、この値を、Azure portal の **[基本的な SAML 構成]** にある **[返信 URL]** テキスト ボックスに貼り付けます。

4. 次のフィールドを構成します。

    ![[Sign on URL]\(サインオン URL\)、[Issuer]\(発行者\)、および [Public certificate]\(公開証明書\) の各テキスト ボックスを示すスクリーンショット。](./media/github-tutorial/configure.png)

    a. **[Sign on URL]\(シングル サインオン URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[Issuer](発行者\)** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書 をメモ帳で開き、その内容を **[Public Certificate]\(公開証明書\)** ボックスに貼り付けます。

    d. **[Edit]\(編集\)** アイコンをクリックし、 **[Signature Method]\(署名方法\)** と **[Digest Method]\(ダイジェスト方法\)** を編集して、**RSA-SHA1** および **SHA1** から **RSA-SHA256** および **SHA256** に変更します (下図参照)。
    
    e. GitHub の URL が Azure アプリ登録の URL と一致するように、**Assertion Consumer Service URL** (応答 URL) を既定の URL から更新します。

    ![画像を示すスクリーンショット。](./media/github-tutorial/certificate.png)

5. **[Test SAML configuration (SAML 構成のテスト)]** をクリックして、SSO の際に検証の失敗やエラーがないことを確認します。

    ![設定を示すスクリーンショット。](./media/github-tutorial/test.png)

6. **[保存]**

> [!NOTE]
> GitHub でのシングル サインオンは GitHub で特定の組織を認証するものです。GitHub そのものの認証に取って代わることはできません。 つまり、ユーザーの github.com セッションの有効期限が切れた場合は、シングル サインオン プロセス中に GitHub の ID とパスワードで認証するように求められることがあります。

### <a name="create-github-test-user"></a>GitHub テスト ユーザーの作成

このセクションの目的は、GitHub で Britta Simon というユーザーを作成することです。 GitHub では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](github-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. GitHub 企業サイトに管理者としてログインします。

2. **[People]\(ユーザー\)** をクリックします。

    ![[ユーザー] が選択されている GitHub サイトを示すスクリーンショット。](./media/github-tutorial/people.png "ユーザー")

3. **[Invite member]\(メンバーの招待\)** をクリックします。

    ![[Invite Users]\(ユーザーの招待\) を示すスクリーンショット。](./media/github-tutorial/invite-member.png "ユーザーの招待")

4. **[メンバーの招待]** ダイアログ ページで、次の手順を実行します。

    a. **[Email]\(電子メール\)** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    ![[Invite People]\(ユーザーの招待\) を示すスクリーンショット。](./media/github-tutorial/email-box.png "[ユーザーの招待]")

    b. **[Send Invitation]\(招待状の送信\)** をクリックします。

    ![[Member]\(メンバー\) が選択され、[Send invitation]\(招待状の送信\) ボタンが選択されている [メンバーの招待] ダイアログ ページを示すスクリーンショット。](./media/github-tutorial/send-invitation.png "[ユーザーの招待]")

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる GitHub のサインオン URL にリダイレクトされます。 

* GitHub のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [GitHub] タイルをクリックすると、GitHub のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

GitHub を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
