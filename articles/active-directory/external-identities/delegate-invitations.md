---
title: B2B 外部コラボレーションの設定を有効にする - Azure AD
description: Active Directory の B2B 外部コラボレーションを有効にして、ゲスト ユーザーを招待できるユーザーを管理する方法について説明します。 ゲスト招待元ロールを使用して、招待を委任します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c6dbac365b10b7abaeea6c4ae600cef24fcd1d2
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821802"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>B2B 外部コラボレーションを有効にしてゲストを招待できるユーザーを管理する

この記事では、Azure Active Directory (Azure AD) B2B コラボレーションを有効にする方法、ゲストを招待できるユーザーを指定する方法、および Azure AD でゲスト ユーザーに付与するアクセス許可を決定する方法について説明します。 

既定では、管理者ロールに割り当てられていなくても、ディレクトリ内のすべてのユーザーとゲストがゲストを招待できます。 外部コラボレーションの設定を使用することで、組織内のユーザーの種類ごとに、ゲストの招待を有効にしたり無効にしたりすることができます。 また、ゲストの招待を許可するロールを割り当てることによって、個々のユーザーに招待を委任することもできます。

Azure AD を使用すると、外部のゲスト ユーザーに表示される Azure AD ディレクトリの内容を制限することができます。 既定では、ゲスト ユーザーはユーザー、グループ、またはその他のディレクトリ リソースの列挙がブロックされ、非表示グループのメンバーシップのみ閲覧可能な、制限付きのアクセス許可レベルに設定されています。 新しいプレビュー設定では、ゲストが自分のプロファイル情報のみを表示できるように、ゲスト アクセスをさらに制限することができます。 詳細については、[ゲスト ユーザーのアクセス制御 (プレビュー)](../enterprise-users/users-restrict-guest-permissions.md) に関する記事を参照してください。

## <a name="configure-b2b-external-collaboration-settings"></a>B2B 外部コラボレーションの設定を構成する

Azure AD B2B コラボレーションでは、テナント管理者が次の招待ポリシーを設定できます。

- 招待を無効にする
- 管理者と、ゲストの招待元ロールのユーザーのみが招待できる
- 管理者、ゲストの招待元ロール、およびメンバーが招待できる
- ゲストを含むすべてのユーザーが招待できる

既定では、ゲストを含むすべてのユーザーが、ゲスト ユーザーを招待できます。

### <a name="to-configure-external-collaboration-settings"></a>外部コラボレーションの設定を構成するには、次のようにします。

1. テナント管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択します。
3. **[外部 ID]**  >  **[外部コラボレーションの設定]** を選択します。

4. **[ゲスト ユーザーのアクセス制限 (プレビュー)]** で、ゲスト ユーザーに付与するアクセス レベルを選択します。
  
   - **Guest users have the same access as members (most inclusive) (ゲスト ユーザーにメンバーと同じアクセス権を付与する (最も包括的))** :このオプションを選択すると、ゲストがメンバー ユーザーと同じように Azure AD リソースとディレクトリ データにアクセスできるようになります。

   - **Guest users have limited access to properties and memberships of directory objects (ゲスト ユーザーに対してディレクトリ オブジェクトのプロパティとメンバーシップへのアクセスを制限する)** :(デフォルト) この設定を選択すると、ゲストは、特定のディレクトリ タスク (ユーザー、グループ、またはその他のディレクトリ リソースを列挙するなど) を実行できなくなります。 ゲストは、非表示でないすべてのグループのメンバーシップを表示できます。

   - **Guest user access is restricted to properties and memberships of their own directory objects (most restrictive) (ゲスト ユーザーのアクセスを、自分のディレクトリ オブジェクトのプロパティとメンバーシップに制限する (最も厳しい制限))** :この設定では、ゲストは自分のプロファイルのみにアクセスできます。 ゲストは、他のユーザーのプロファイル、グループ、またはグループ メンバーシップを参照することはできません。

5. **[Guest invite settings]\(ゲスト招待の設定\)** で、適切な設定を選択します。

    ![ゲスト招待の設定](./media/delegate-invitations/guest-invite-settings.png)

   - **ゲストと非管理者を含む組織内のすべてのユーザーがゲスト ユーザーを招待できる (最も包括的)** : 組織内のゲストが、組織のメンバーでないひとも含めて他のゲストを招待できるようにするには、このオプション ボタンを選択します。
   - **メンバー アクセス許可を持つゲストを含むメンバー ユーザーと特定の管理者ロールに割り当てられたユーザーがゲスト ユーザーを招待できる**: メンバー ユーザーと特定の管理者の役割を持つユーザーがゲストを招待できるようにするには、このオプション ボタンを選択します。
   - **特定の管理者ロールに割り当てられているユーザーのみがゲスト ユーザーを招待できる**: 管理者の役割を持つユーザーだけがゲストを招待できるようにするには、このオプション ボタンを選択します。 管理者の役割には、[全体管理者](../roles/permissions-reference.md#global-administrator)、[ユーザー管理者](../roles/permissions-reference.md#user-administrator)、および[ゲスト招待元](../roles/permissions-reference.md#guest-inviter)が含まれます。
   - **管理者を含む組織内のすべてのユーザーがゲスト ユーザーを招待できない (最も制限的)** : 組織内のだれもがゲストを招待できないようにするには、このオプション ボタンを選択します。
     > [!NOTE]
     > **[メンバーは招待ができる]** が **[いいえ]** に設定され、 **[管理者とゲスト招待元ロールのユーザーは招待ができる]** が **[はい]** に設定されている場合、**ゲスト招待元** ロールのユーザーは引き続きゲストを招待できます。

6. ユーザーがアプリにサインアップできるようにユーザー フローを作成する場合は、 **[Enable guest self-service sign up via user flows]\(ユーザー フローによるゲスト セルフサービス サインアップを有効にする\)** で **[はい]** を選択します。 この設定の詳細については、[アプリへのセルフサービス サインアップ ユーザー フローの追加](self-service-sign-up-user-flow.md)に関するページを参照してください。

    ![ユーザー フローによるセルフサービス サインアップの設定](./media/delegate-invitations/self-service-sign-up-setting.png)

7. **[コラボレーションの制限]** で、指定したドメインへの招待を許可するか拒否するかを選択し、テキスト ボックスに特定のドメイン名を入力できます。 複数ドメインの場合は、それぞれのドメインを新しい行に入力します。 詳細については、「[B2B ユーザーに対する特定組織からの招待を許可またはブロックする](allow-deny-list.md)」を参照してください。

    ![コラボレーションの制限の設定](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>ゲスト招待元ロールをユーザーに割り当てる

ゲスト招待元ロールを使用すると、個々のユーザーにグローバル管理者ロールなどの管理者ロールを割り当てなくても、ゲストを招待する機能を付与できます。 ゲスト招待元ロールを個々のユーザーに割り当てます。 次に、 **[管理者とゲスト招待元ロールのユーザーは招待ができる]** が **[はい]** に設定されていることを確認します。

次の例は、PowerShell を使って、ゲストの招待元ロールにユーザーを追加する方法を示しています。

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [招待を使用せずに B2B コラボレーション ゲスト ユーザーを追加する](add-user-without-invite.md)
- [B2B コラボレーション ユーザーのロールへの追加](./add-users-administrator.md)