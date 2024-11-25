---
title: 'チュートリアル: Real Links を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から Real Links に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: a024c7db-ffe6-4fc9-a0ec-7075930bbf75
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: thwimmer
ms.openlocfilehash: bd15b2e58eb1959287b64397e4f99ba807c2be33
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495228"
---
# <a name="tutorial-configure-real-links-for-automatic-user-provisioning"></a>チュートリアル: Real Links を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Real Links と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [Real Links](https://www.reallinks.io) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Real Links でユーザーを作成する
> * アクセスが不要になった場合に Real Links のユーザーを削除する
> * Azure AD と Real Links の間でユーザー属性の同期を維持する
> * Real Links に[シングル サインオン](real-links-tutorial.md)する (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [Real Links](https://www.reallinks.io/)サブスクリプション - すべての階層に自動ユーザー プロビジョニングが含まれます。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Real Links の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-real-links-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように Real Links を構成する

Real Links プラットフォームでプロビジョニングを構成するには、 [Real Links サポートチーム](mailto:support@reallinks.io)に連絡し、SCIM-v2 のプロビジョニングの詳細を要求する必要があります。 これには以下が含まれます。

* お使いのプラットフォームのテナントの URL
* 一意のシークレットトークン

## <a name="step-3-add-real-links-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Real Links を追加する

Azure AD アプリケーション ギャラリーから Real Links を追加して、Real Links へのプロビジョニングの管理を開始します。 SSO のために Real Links を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Real Links にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-real-links"></a>手順 5. Real Links への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Real Links のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-real-links-in-azure-ad"></a>Azure AD で Real Links の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Real Links]** を選択します。

    ![アプリケーションの一覧の Real Links のリンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Real Links の [テナントの URL] と [シークレット トークン] を入力します。 **[テスト接続]** をクリックして、Azure AD から Real Links への接続を確保します。 接続できない場合は、使用している Real Links アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Real Links]\(Azure Active Directory ユーザーを Real Links に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Real Links に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Real Links のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Real Links API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|Real Links で必須|
   |---|---|---|---|
   |userName|String|&check;|&check;
   |active|Boolean||&check;
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:firstName|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:lastName|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:emailAddress|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:phoneNumber|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:title|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:preferredLanguage|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:real-links:2.0:User:countryCode|String|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. Real Links に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Real Links にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)