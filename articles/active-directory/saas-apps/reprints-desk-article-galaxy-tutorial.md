---
title: 'チュートリアル: Reprints Desk - Article Galaxy と Azure Active Directory のシングル サインオン (SSO) 統合 | Microsoft Docs'
description: Azure Active Directory と Reprints Desk - Article Galaxy の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 33077902722a32f792c12598f7d555880050a574
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309021"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>チュートリアル: Reprints Desk - Article Galaxy と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、Reprints Desk - Article Galaxy と Azure Active Directory (Azure AD) を統合する方法について説明します。 Reprints Desk - Article Galaxy と Azure AD を統合すると、次のことができます。

* Reprints Desk - Article Galaxy にアクセスできる Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Reprints Desk - Article Galaxy に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Reprints Desk - Article Galaxy でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Reprints Desk - Article Galaxy では、**IDP** Initiated SSO がサポートされます

* Reprints Desk - Article Galaxy では、**ジャスト イン タイム** のユーザー プロビジョニングがサポートされます

* [Reprints Desk - Article Galaxy を構成すると、セッション制御を適用できます。これにより、組織の機密データの流出と侵入行動がリアルタイムで保護されます。セッション制御は、条件付きアクセスから拡張します。Microsoft Defender for Cloud Apps でセッション制御を強制する方法について説明](/cloud-app-security/proxy-deployment-any-app)します。

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>ギャラリーからの Reprints Desk - Article Galaxy の追加

Reprints Desk - Article Galaxy の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Reprints Desk - Article Galaxy を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Reprints Desk - Article Galaxy**」と入力します。
1. 結果のパネルから **[Reprints Desk - Article Galaxy]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Reprints Desk - Article Galaxy の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Reprints Desk - Article Galaxy に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Reprints Desk - Article Galaxy の関連ユーザーとの間にリンク関係を確立する必要があります。

Reprints Desk - Article Galaxy に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Reprints Desk - Article Galaxy の SSO の構成](#configure-reprints-desk-article-galaxy-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Reprints Desk - Article Galaxy のテスト ユーザーの作成](#create-reprints-desk-article-galaxy-test-user)** - Reprints Desk - Article Galaxy で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Reprints Desk - Article Galaxy** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。


1. Reprints Desk - Article Galaxy アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Reprints Desk - Article Galaxy アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ------------ | --------- |
    | firstname | User.givenname |
    | lastname | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Reprints Desk - Article Galaxy のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Reprints Desk - Article Galaxy へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Reprints Desk - Article Galaxy]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Reprints Desk - Article Galaxy SSO の構成

**Reprints Desk - Article Galaxy** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Reprints Desk - Article Galaxy サポート チーム](mailto:customersupport@reprintsdesk.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Reprints Desk - Article Galaxy テスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Reprints Desk - Article Galaxy に作成します。 Reprints Desk - Article Galaxy では、ジャスト イン タイムのユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Reprints Desk - Article Galaxy にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Reprints Desk - Article Galaxy] タイルをクリックすると、SSO を設定した Reprints Desk - Article Galaxy に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD を使用して Reprints Desk - Article Galaxy を試す](https://aad.portal.azure.com/)

- [Microsoft Defender for Cloud Apps でのセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Reprints Desk - Article Galaxy を保護する方法](/cloud-app-security/proxy-intro-aad)
