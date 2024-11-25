---
title: 'チュートリアル: Azure AD SSO と Profit.co の統合'
description: Azure Active Directory と Profit.co の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: ae8e76d33dbab90478a361c16f8f3abbb3346e36
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341770"
---
# <a name="tutorial-azure-ad-sso-integration-with-profitco"></a>チュートリアル: Azure AD SSO と Profit.co の統合

このチュートリアルでは、Profit.co と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Profit.co を統合すると、次のことができます。

* Profit.co にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Profit.co に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Profit.co でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Profit.co では、IDP Initiated SSO がサポートされます。

## <a name="add-profitco-from-the-gallery"></a>ギャラリーからの Profit.co の追加

Azure AD への Profit.co の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Profit.co を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Profit.co**」と入力します。
1. 結果のパネルから **[Profit.co]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-profitco"></a>Profit.co の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Profit.co に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Profit.co の関連ユーザーとの間にリンク関係を確立します。

Profit.co に対する Azure AD SSO を構成してテストする一般的な手順を次に示します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Profit.co の SSO の構成](#configure-profitco-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Profit.co のテスト ユーザーの作成](#create-a-profitco-test-user)** - Profit.co で B.Simon に対応するユーザーを作成します。このユーザーを Azure AD の対応するユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Profit.co** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML でシングル サインオンをセットアップします] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 ユーザーは、 **[保存]** を選択して構成を保存する必要があります。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[コピー]** を選択します。 これにより、**アプリのフェデレーション メタデータ URL** がコピーされ、お使いのコンピューターに保存されます。

    ![コピー ボタンが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** フィールドに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Profit.co へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧から **[Profit.co]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-profitco-sso"></a>Profit.co の SSO の構成

Profit.co 側でシングル サインオンを構成するには、アプリのフェデレーション メタデータ URL を [Profit.co サポート チーム](mailto:support@profit.co)に送信する必要があります。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。

### <a name="create-a-profitco-test-user"></a>Profit.co のテスト ユーザーの作成

このセクションでは、Profit.co で B.Simon というユーザーを作成します。[Profit.co サポート チーム](mailto:support@profit.co)と協力して、Profit.co プラットフォームにユーザーを追加します。 ユーザーを作成してアクティブ化するまでは、シングル サインオンを使用できません。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Profit.co に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Profit.co] タイルをクリックすると、SSO を設定した Profit.co に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Profit.co を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
