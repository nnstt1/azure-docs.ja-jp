---
title: 'チュートリアル: Azure Active Directory を使用した自動ユーザー プロビジョニングに対応するように Appaegis Isolation Access Cloud を構成する | Microsoft Docs'
description: Azure AD から Appaegis Isolation Access Cloud へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: c845e98a-6fcd-4285-94b7-a72a2175ca7e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: thwimmer
ms.openlocfilehash: c1700980368095e31da1d41b31ddccdf7c3ea89b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894903"
---
# <a name="tutorial-configure-appaegis-isolation-access-cloud-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングに対応するように Appaegis Isolation Access Cloud を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Appaegis Isolation Access Cloud と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD で Azure AD プロビジョニング サービスを使用して、[Appaegis Isolation Access Cloud](https://www.appaegis.com) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に実行されます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="supported-capabilities"></a>サポートされる機能
> [!div class="checklist"]
> * Appaegis Isolation Access Cloud のユーザーを作成する
> * アクセスが不要になったときに、Appaegis Isolation Access Cloud のユーザーを削除する
> * Azure AD と Appaegis Isolation Access Cloud の間でユーザー属性の同期を維持する
> * Appaegis Isolation Access Cloud への[シングル サインオン](appaegis-isolation-access-cloud-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* Professional レベルのサブスクリプションを持つ [Appaegis Cloud](https://www.appaegis.com) アカウント。 
* **全体管理者** アクセス許可が付与されている Appaegis Cloud ユーザー アカウント。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Appaegis Isolation Access Cloud の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-appaegis-isolation-access-cloud-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように Appaegis Isolation Access Cloud を構成する

1. Appaegis Cloud で [SSO](appaegis-isolation-access-cloud-tutorial.md) を有効化します。
2. **[Identity Provider Details]\(ID プロバイダーの詳細\)** ページ (ACS URL とエンティティ ID が一覧表示されているページ) で、SCIM URL と SCIM トークンが表示されます。

## <a name="step-3-add-appaegis-isolation-access-cloud-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Appaegis Isolation Access Cloud を追加する

Appaegis Isolation Access Cloud を Azure AD アプリケーション ギャラリーから追加して、Appaegis Isolation Access Cloud に対してプロビジョニングの管理を開始します。 SSO のために Appaegis Isolation Access Cloud を以前に設定している場合は、その同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Appaegis Isolation Access Cloud にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-appaegis-isolation-access-cloud"></a>手順 5. Appaegis Isolation Access Cloud に対して自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて TestApp のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-appaegis-isolation-access-cloud-in-azure-ad"></a>Azure AD で Appaegis Isolation Access Cloud に対して自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Appaegis Isolation Access Cloud]** を選択します。

    ![アプリケーションの一覧の [Appaegis Isolation Access Cloud]](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Appaegis Isolation Access Cloud の [テナントの URL] と [シークレット トークン] を入力します。 **[接続テスト]** をクリックして、Azure AD が Appaegis Isolation Access Cloud に接続できることを確認します。 接続できない場合は、使用中の Appaegis Isolation Access Cloud アカウントに管理者アクセス許可があることを確認してからもう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Appaegis Isolation Access Cloud]\(Azure Active Directory ユーザーを Appaegis Isolation Access Cloud に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Appaegis Isolation Access Cloud に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Appaegis Isolation Access Cloud のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Appaegis Isolation Access Cloud API でサポートされていることを確認する必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

    |属性|Type|フィルター処理のサポート|
    |---|---|---|
    |userName|String|&check;
    |active|Boolean|
    |displayName|String|
    |name.givenName|String|
    |name.familyName|String|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Contoso] (Azure Active Directory グループを Contoso に同期する)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Contoso に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Contoso のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Appaegis Isolation Access Cloud に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Appaegis Isolation Access Cloud にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
