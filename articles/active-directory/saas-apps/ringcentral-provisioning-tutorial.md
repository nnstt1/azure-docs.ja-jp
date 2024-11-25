---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニング用に RingCentral を構成する | Microsoft Docs
description: Azure AD から RingCentral に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: thwimmer
ms.openlocfilehash: 4999669aee35b9e0d7303ae1156f10318c775562
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989776"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に RingCentral を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために RingCentral と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[RingCentral](https://www.ringcentral.com/office/plansandpricing.html) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * RingCentral でユーザーを作成する
> * アクセスが不要になった場合に RingCentral のユーザーを削除する
> * Azure AD と RingCentral の間でユーザー属性の同期を維持する
> * RingCentral への[シングル サインオン](./ringcentral-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [RingCentral テナント](https://www.ringcentral.com/office/plansandpricing.html)
* 管理者アクセス許可がある RingCentral のユーザー アカウント。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と RingCentral の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように RingCentral を構成する

手順 5. の [管理者資格情報] セクションで承認を行うためには、[RingCentral](https://www.ringcentral.com/office/plansandpricing.html) 管理者アカウントが必要です。

RingCentral の管理ポータルの [Account Settings]\(アカウント設定\) -> [Directory Integrations]\(ディレクトリの統合\) で、 *[Directory Provider]* \(ディレクトリ プロバイダー\) 設定を *[SCIM]* 
![画像](https://user-images.githubusercontent.com/49566142/134523440-20320d8e-3c25-4358-9ace-d4888ce8e4ea.png) に設定します。


> [!NOTE]
> ユーザーにライセンスを割り当てる方法については、[こちら](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language)のビデオ リンクをご覧ください。

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから RingCentral を追加する

Azure AD アプリケーション ギャラリーから RingCentral を追加して、RingCentral へのプロビジョニングの管理を開始します。 SSO のために RingCentral を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* RingCentral にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>手順 5. RingCentral への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Azure AD で RingCentral の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[RingCentral]** を選択します。

    ![アプリケーションの一覧の RingCentral のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクション下にある **[承認する]** をクリックします。 RingCentral のサインイン ページにリダイレクトされます。 メールまたは電話番号を入力し、パスワードを入力して、 **[サインイン]** ボタンをクリックします。 RingCentral の **[Access Request]\(アクセス要求\)** ページで **[Authorize]\(承認\)** をクリックします。 **[テスト接続]** をクリックして、Azure AD から RingCentral への接続を確認します。 接続できない場合は、使用中の RingCentral アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

   ![AAD](./media/ringcentral-provisioning-tutorial/admincredentials.png)

   ![アクセス](./media/ringcentral-provisioning-tutorial/authorize.png)

   ![承認します](./media/ringcentral-provisioning-tutorial/accessrequest.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to RingCentral]\(Azure Active Directory ユーザーを RingCentral に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から RingCentral に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で RingCentral のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に RingCentral API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |externalId|String|
   |active|Boolean|
   |title|String|
   |emails[type eq "work"].value|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].streetAddress|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. RingCentral に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、RingCentral にプロビジョニングするユーザー、グループ、またはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="change-log"></a>ログの変更

* 2020 年 9 月 10 日 - "displayName" および "manager" 属性のサポートを削除しました。
* 03/15/2021 - 承認方法を永続的なベアラー トークンから OAuth コード付与フローに更新しました。
* 10/28/2021 - 既定のマッピングを **mail-> emails[type eq “work”].value** に更新しました。
* 10/28/2021 - レート制限が読み取りで毎分 300、書き込みで毎分 1000 に更新されました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
