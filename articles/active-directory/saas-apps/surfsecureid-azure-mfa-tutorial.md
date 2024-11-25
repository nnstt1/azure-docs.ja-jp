---
title: 'チュートリアル: Azure AD SSO と SURFsecureID - Azure MFA の統合'
description: Azure Active Directory と SURFsecureID - Azure MFA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/20/2021
ms.author: jeedes
ms.openlocfilehash: 8034af23b35545e1e5f5508632b0b8737537b0f5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063857"
---
# <a name="tutorial-azure-ad-sso-integration-with-surfsecureid---azure-mfa"></a>チュートリアル: Azure AD SSO と SURFsecureID - Azure MFA の統合

このチュートリアルでは、SURFsecureID - Azure MFA を Azure Active Directory (Azure AD) と統合する方法について説明します。 SURFsecureID - Azure MFA を Azure AD と統合すると、次のことができます。

* SURFsecureID - Azure MFA にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して SURFsecureID - Azure MFA に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SURFsecureID - Azure MFA でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SURFsecureID - Azure MFA では、**SP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-surfsecureid---azure-mfa-from-the-gallery"></a>ギャラリーから SURFsecureID - Azure MFA を追加する

Azure AD への SURFsecureID - Azure MFA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SURFsecureID - Azure MFA を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SURFsecureID - Azure MFA**」と入力します。
1. 結果のパネルから **[SURFsecureID - Azure MFA]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-surfsecureid---azure-mfa"></a>SURFsecureID - Azure MFA の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SURFsecureID - Azure MFA に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと SURFsecureID - Azure MFA の関連ユーザーとの間にリンク関係を確立する必要があります。

SURFsecureID - Azure MFA に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SURFsecureID - Azure MFA の SSO の構成](#configure-surfsecureid---azure-mfa-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SURFsecureID - Azure MFA のテスト ユーザーの作成](#create-surfsecureid---azure-mfa-test-user)** - SURFsecureID - Azure MFA で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SURFsecureID - Azure MFA** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかの URL を入力します。

    | **環境** | **URL** |
    |-------|------|
    | ステージング | `https://azuremfa.test.surfconext.nl/saml/metadata` |
    | Production | `https://azuremfa.surfconext.nl/saml/metadata` |

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。

    | **環境** | **URL** |
    |-------|------|
    | ステージング | `https://azuremfa.test.surfconext.nl/saml/acs` |
    | Production | `https://azuremfa.surfconext.nl/saml/acs` |

    b. **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。
    
    | **環境** | **URL** |
    |-------|------|
    | ステージング | `https://sa.test.surfconext.nl` |
    | Production | `https://sa.surfconext.nl` |

1. SURFsecureID - Azure MFA アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、SURFsecureID - Azure MFA アプリケーションでは、さらにいくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性 |
    | --------- | --------- |
    | urn:mace:dir:attribute-def:mail | User.mail |

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

このセクションでは、B. Simon に SURFsecureID - Azure MFA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[SURFsecureID - Azure MFA]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-surfsecureid---azure-mfa-sso"></a>SURFsecureID - Azure MFA の SSO の構成

**SURFsecureID - Azure MFA** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [SURFsecureID - Azure MFA サポート チーム](mailto:support@surfconext.nl)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-surfsecureid---azure-mfa-test-user"></a>SURFsecureID - Azure MFA のテスト ユーザーの作成

このセクションでは、SURFsecureID - Azure MFA で Britta Simon というユーザーを作成します。 [SURFsecureID - Azure MFA サポート チーム](mailto:support@surfconext.nl)と連携して、SURFsecureID - Azure MFA プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SURFsecureID - Azure MFA のサインオン URL にリダイレクトされます。 

* SURFsecureID - Azure MFA のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SURFsecureID - Azure MFA] タイルをクリックすると、SURFsecureID - Azure MFA のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SURFsecureID - Azure MFA を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。