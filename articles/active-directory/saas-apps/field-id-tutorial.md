---
title: 'チュートリアル: Azure AD SSO と Field iD の統合'
description: Azure Active Directory と Field iD の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2021
ms.author: jeedes
ms.openlocfilehash: ea79e2dfe4a66fe7fe05a29ccf20db7c99a33206
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337954"
---
# <a name="tutorial-azure-ad-sso-integration-with-field-id"></a>チュートリアル: Azure AD SSO と Field iD の統合

このチュートリアルでは、Field iD と Azure Active Directory (Azure AD) を統合する方法について説明します。 Field iD と Azure AD を統合すると、次のことができます。

* Field iD にアクセスできる人を Azure AD で制御します。
* ユーザーが Azure AD アカウントで Field iD に自動サインインできるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Field iD でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Field iD では、IDP Initiated SSO がサポートされます。

## <a name="add-field-id-from-the-gallery"></a>ギャラリーからの Field iD の追加

Field iD を Azure AD に統合するには、マネージド SaaS アプリの一覧にギャラリーから Field iD を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Field iD**」と入力します。
1. [結果] パネルから **[Field iD]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-field-id"></a>Field iD のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Field iD に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Field iD の関連ユーザーとの間にリンク関係を確立する必要があります。

Field iD に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
   1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
   1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [Field iD の SSO を構成](#configure-field-id-sso)し、アプリケーション側でシングル サインオン設定を構成します。
   1. [Field iD のテスト ユーザーを作成](#create-a-field-id-test-user)します。Field iD で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Field iD** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 続けて、 **[single sign-on]\(シングル サインオン\)** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML によるシングル サインオンのセットアップ] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

   a. **[識別子]** ボックスに、`https://<tenantname>.fieldid.com/fieldid` という形式で URL を入力します。

   b. **[応答 URL]** ボックスに、次のパターンを使用する URL を入力します: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[Field iD サポート チーム](mailto:support@ecompliance.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー アイコンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーします。 それを自分のコンピューターに保存します。

    ![コピー アイコンが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **名前** には、`B.Simon`を入力します。  
   1. **[ユーザー名]** に「username@companydomain.extension」と入力します (たとえば、`B.Simon@contoso.com`)。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Field iD へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Field iD]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスの **ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-field-id-sso"></a>Field iD SSO の構成

Field iD 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Field iD サポート チーム](mailto:support@ecompliance.com)に送信してください。 サポート チームにより、SAML シングル サインオン接続が両方の側で確実に正しく設定されます。

### <a name="create-a-field-id-test-user"></a>Field iD のテスト ユーザーの作成

このセクションでは、Field iD で Britta Simon というユーザーを作成します。 [Field iD サポート チーム](mailto:support@ecompliance.com)と協力して、Field iD プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Field iD に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Field iD] タイルをクリックすると、SSO を設定した Field iD に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Field iD を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
