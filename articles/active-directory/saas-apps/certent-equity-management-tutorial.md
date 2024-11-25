---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Certent Equity Management の統合 | Microsoft Docs
description: Azure Active Directory と Certent Equity Management の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 64ade20c484e6ab14d5b9f3d89919872cae1bade
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-certent-equity-management"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Certent Equity Management の統合

このチュートリアルでは、Certent Equity Management と Azure Active Directory (Azure AD) を統合する方法について説明します。 Certent Equity Management と Azure AD を統合すると、次のことができます。

- Certent Equity Management にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで Certent Equity Management に自動的にサインインできるようにすることができます。
- 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
- Certent Equity Management のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

- Certent Equity Management では、**IDP** Initiated SSO がサポートされます

## <a name="adding-certent-equity-management-from-the-gallery"></a>ギャラリーからの Certent Equity Management の追加

Azure AD への Certent Equity Management の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Certent Equity Management を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Certent Equity Management**」と入力します。
1. 結果パネルから **Certent Equity Management** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-certent-equity-management"></a>Certent Equity Management の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Certent Equity Management に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Certent Equity Management の関連ユーザーとの間にリンク関係を確立する必要があります。

Certent Equity Management に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
   1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
   1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Certent Equity Management の SSO の構成](#configure-certent-equity-management-sso)** - アプリケーション側でシングル サインオン設定を構成します。
   1. **[Certent Equity Management のテスト ユーザーの作成](#create-certent-equity-management-test-user)** - Certent Equity Management で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Certent Equity Management** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

   a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.certent.com/sys/sso/saml/acs.aspx` の形式で URL を入力します。

   b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.certent.com/sys/sso/saml/acs.aspx` のパターンを使用して URL を入力します

   > [!NOTE]
   > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、顧客対応マネージャーによって割り当てられた Certent 統合アナリストにお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Certent Equity Management アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

   ![image](common/default-attributes.png)

1. その他に、Certent Equity Management アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

   | 名前    | ソース属性       |
   | ------- | ---------------------- |
   | COMPANY | user.companyname       |
   | User    | user.userprincipalname |
   | ROLE    | user.assignedroles     |

   > [!NOTE]
   > Azure AD で **役割** を構成する方法については、[ここ](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)をクリックしてください。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Certent Equity Management のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Certent Equity Management へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Certent Equity Management]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-certent-equity-management-sso"></a>Certent Equity Management の SSO の構成

**Certent Equity Management** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を、顧客対応マネージャーによって割り当てられた Certent 統合アナリストに送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-certent-equity-management-test-user"></a>Certent Equity Management テスト ユーザーの作成

このセクションでは、Certent Equity Management で Britta Simon というユーザーを作成します。 顧客対応マネージャーによって割り当てられた Certent 統合アナリストと連携し、Certent Equity Management プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

- Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Certent Equity Management に自動的にサインインされます

- Microsoft マイ アプリを使用することができます。 マイ アプリで [Certent Equity Management] タイルをクリックすると、SSO を設定した Certent Equity Management に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Certent Equity Management を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
