---
title: 'チュートリアル: Azure AD SSO と Korn Ferry ALP の統合'
description: Azure Active Directory と Korn Ferry ALP の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2021
ms.author: jeedes
ms.openlocfilehash: ce4ba44c1c3096bad6b936f293b47ac5ff9a30c7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285198"
---
# <a name="tutorial-azure-ad-sso-integration-with-korn-ferry-alp"></a>チュートリアル: Azure AD SSO と Korn Ferry ALP の統合

このチュートリアルでは、Korn Ferry ALP と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Korn Ferry ALP を統合すると、次のことができます。

* Korn Ferry ALP にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Korn Ferry ALP に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Korn Ferry ALP でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Korn Ferry ALP では、**SP** によって開始される SSO がサポートされます。

## <a name="add-korn-ferry-alp-from-the-gallery"></a>ギャラリーからの Korn Ferry ALP の追加

Azure AD への Korn Ferry ALP の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Korn Ferry ALP を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Korn Ferry ALP**」と入力します。
1. 結果パネルで **[Korn Ferry ALP]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-korn-ferry-alp"></a>Korn Ferry ALP に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Korn Ferry ALP に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Korn Ferry ALP の関連ユーザーとの間にリンク関係を確立する必要があります。

Korn Ferry ALP に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Korn Ferry ALP の SSO の構成](#configure-korn-ferry-alp-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Korn Ferry ALP のテスト ユーザーの作成](#create-korn-ferry-alp-test-user)** - Korn Ferry ALP で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Korn Ferry ALP** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。
   
    a. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。
    
   | **Identifier** |
   |-------|
   | `https://intappextin01/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://qaassessment.kfnaqa.com/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://assessments.kornferry.com/portalweb/sso/client/audience?guid=<customerguid>` |
    
    b. **[サインオン URL]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

   | **サインオン URL** |
   |------|
   | `https://intappextin01/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://qaassessment.kfnaqa.com/portalweb/sso/client/audience?guid=<customerguid>` |
   | `https://assessments.kornferry.com/portalweb/sso/client/audience?guid=<customerguid>` |

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[Korn Ferry ALP クライアント サポート チーム](mailto:noreply@kornferry.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

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

このセクションでは、B.Simon に Korn Ferry ALP へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Korn Ferry ALP]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-korn-ferry-alp-sso"></a>Korn Ferry ALP の SSO の構成

**Korn Ferry ALP** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Korn Ferry ALP サポート チーム](mailto:noreply@kornferry.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-korn-ferry-alp-test-user"></a>Korn Ferry ALP のテスト ユーザーの作成

このセクションでは、Korn Ferry ALP で Britta Simon というユーザーを作成します。 [Korn Ferry ALP サポート チーム](mailto:noreply@kornferry.com)と連携して、Korn Ferry ALP プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Korn Ferry ALP のサインオン URL にリダイレクトされます。 

* Korn Ferry ALP のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Korn Ferry ALP] タイルをクリックすると、Korn Ferry ALP のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Korn Ferry ALP を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
