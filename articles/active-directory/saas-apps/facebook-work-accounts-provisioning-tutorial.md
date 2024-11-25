---
title: 'チュートリアル: Azure Active Directory を使用した自動ユーザー プロビジョニングのために Facebook Work Accounts を構成する | Microsoft Docs'
description: Azure AD から Facebook Work Accounts に対するユーザー アカウントのプロビジョニング/プロビジョニング解除を自動的に行う方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6563eaea-0328-4923-9a08-f1f07d38998b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2021
ms.author: Zhchia
ms.openlocfilehash: ed6686374dc36a9fa4b1d3250d359fb2272a679c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063859"
---
# <a name="tutorial-configure-facebook-work-accounts-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングのために Facebook Work Accounts を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために、Facebook Work Accounts と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD プロビジョニング サービスを使用して、[Facebook Work Accounts](https://work.facebook.com) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が、Azure AD によって自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Facebook Work Accounts でユーザーを作成する
> * アクセスが不要になった場合に Facebook Work Accounts のユーザーを削除する
> * Azure AD と Facebook Work Accounts の間でユーザー属性の同期を維持する
> * Facebook Work Accounts に[シングル サインオン](facebook-work-accounts-tutorial.md)する (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* 会社の設定を変更して統合を構成するためのアクセス許可を持っている、職場アカウント内の管理者アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Vonage の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。


## <a name="step-2-add-facebook-work-accounts-from-the-azure-ad-application-gallery"></a>手順 2. Azure AD アプリケーション ギャラリーから Facebook Work Accounts を追加する

Azure AD アプリケーション ギャラリーから Facebook Work Accounts を追加して、Facebook Work Accounts に対するプロビジョニングの管理を開始します。 以前、SSO のために Facebook Work Accounts を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>手順 3. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。

* Facebook Work Accounts にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。


## <a name="step-4-configure-automatic-user-provisioning-to-facebook-work-accounts"></a>手順 4. Facebook Work Accounts に対する自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-facebook-work-accounts-in-azure-ad"></a>Azure AD で Facebook Work Accounts のために自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Facebook Work Accounts]** を選択します。

    ![アプリケーションの一覧の Facebook Work Accounts](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクション下にある **[承認する]** をクリックします。 **Facebook Work Accounts** の承認ページにリダイレクトされます。 Facebook Work Accounts のユーザー名を入力して、 **[続行]** ボタンをクリックします。 **[テスト接続]** をクリックして、Azure AD から Facebook Work Accounts に接続できることを確認します。 接続が失敗した場合、お使いの Facebook Work Accounts アカウントに Admin アクセス許可があることを確認し、再試行してください。

    :::image type="content" source="media/facebook-work-accounts-provisioning-tutorial/azure-connect.png" alt-text="OAuth の画面":::

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの下にある **[Azure Active Directory ユーザーを Facebook Work Accounts に同期する]** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Facebook Work Accounts に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作のために、Facebook Work Accounts 内のユーザー アカウントの照合に使用されます。 [一致している対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が、Facebook Work Accounts API によってサポートされていることを確認する必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

    |属性|Type|フィルター処理のサポート|
    |---|---|---|
    |userName|String|&check;
    |externalId|String|
    |active|Boolean|
    |title|String|
    |emails[type eq "work"].value|String|
    |preferredLanguage|String|
    |name.givenName|String|
    |name.familyName|String|
    |name.formatted|String|
    |addresses[type eq "work"].formatted|String|
    |addresses[type eq "work"].streetAddress|String|
    |addresses[type eq "work"].locality|String|
    |addresses[type eq "work"].region|String|
    |addresses[type eq "work"].postalCode|String|
    |addresses[type eq "work"].country|String|
    |phoneNumbers[type eq "work"].value|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Facebook Work Accounts のために Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで、 **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で必要な値を選択して、Facebook Work Accounts にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-5-monitor-your-deployment"></a>手順 5. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
