---
title: 'チュートリアル: Azure AD SSO と Projectplace の統合'
description: Azure Active Directory と Projectplace の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: ea2cfac86a601305cd63414582721b6b6df864bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333886"
---
# <a name="tutorial-azure-ad-sso-integration-with-projectplace"></a>チュートリアル: Azure AD SSO と Projectplace の統合

このチュートリアルでは、Projectplace と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Projectplace を統合すると、次のことができます。

* Projectplace にアクセスできるユーザーを Azure AD 上で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Projectplace に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。
* ユーザーを Projectplace に自動的にプロビジョニングできる。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Projectplace でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Projectplace では、**SP Initiated SSO と IDP Initiated SSO** のほか、**ジャスト イン タイム** ユーザー プロビジョニングがサポートされます。

## <a name="add-projectplace-from-the-gallery"></a>ギャラリーからの Projectplace の追加

Azure AD への Projectplace の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Projectplace を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Projectplace**」と入力します。
1. 結果のパネルから **[Projectplace]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-projectplace"></a>Projectplace に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Projectplace に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Projectplace の関連ユーザーとの間にリンク関係を確立する必要があります。

Projectplace に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
   1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
   1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Projectplace SSO の構成](#configure-projectplace-sso)** - アプリケーション側でシングル サインオン設定を構成します。
   1. **[Projectplace テスト ユーザーの作成](#create-projectplace-test-user)** - Projectplace で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Projectplace** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **IDP** 開始モードでアプリケーションを構成したい場合、 **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://service.projectplace.com`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー **アイコン** をクリックし、要件に従って **[アプリのフェデレーション メタデータ URL]** をコピーして、メモ帳に保存します。

   ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

1. **[Projectplace のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B. Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Projectplace へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Projectplace]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B. Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-projectplace-sso"></a>Projectplace SSO を構成する

**Projectplace** 側でシングル サインオンを構成するには、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** を [Projectplace サポート チーム](https://success.planview.com/Projectplace/Support)に送信する必要があります。 このチームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

>[!NOTE]
>シングル サインオンの構成は、[Projectplace サポート チーム](https://success.planview.com/Projectplace/Support)が実行する必要があります。 構成が完了すると直ちに、通知が届きます。 

### <a name="create-projectplace-test-user"></a>Projectplace のテスト ユーザーの作成

>[!NOTE]
>Projectplace でプロビジョニングが有効な場合、この手順はスキップしてかまいません。 [Projectplace のサポート チーム](https://success.planview.com/Projectplace/Support)にプロビジョニングの有効化を依頼することができ、それが完了したら、最初のログイン中にユーザーが Projectplace に作成されます。

Azure AD ユーザーが Projectplace にサインインできるようにするには、それらを Projectplace に追加する必要があります。 手動で追加する必要があります。

**ユーザー アカウントを作成するには、以下の手順に従います。**

1. **Projectplace** 企業サイトに管理者としてサインインします。

2. **[People]\(ユーザー\)** に移動し、**[Members]\(メンバー\)** を選択します。
   
    ![[People]\(ユーザー\) に移動し、[Members]\(メンバー\) を選択します](./media/projectplace-tutorial/members.png "ユーザー")

3. **[Add Member]\(メンバーの追加\)** を選択します。
   
    ![[Add Member]\(メンバーの追加\) を選択します](./media/projectplace-tutorial/issues.png "メンバーの追加")

4. **[Add Member]\(メンバーの追加\)** セクションで、次の手順に従います。
   
    ![[Add Member]\(メンバーの追加\) セクション](./media/projectplace-tutorial/account.png "[New Members]")
   
    1. **[New Members]\(新しいメンバー\)** ボックスに、追加する有効な Azure AD アカウントの電子メール アドレスを入力します。
   
    1. **[Send]** を選択します。

   Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含むメールが送信されます。

>[!NOTE]
>Projectplace から提供されている他の任意のユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントを追加することもできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Projectplace のサインオン URL にリダイレクトされます。  

* Projectplace のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Projectplace に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Projectplace] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーションのサインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Projectplace に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Projectplace を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
