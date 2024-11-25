---
title: Azure RBAC のベスト プラクティス
description: Azure のロールベースのアクセス制御 (Azure RBAC) を使用するためのベスト プラクティス。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 11/15/2021
ms.author: rolyon
ms.openlocfilehash: 947645848fd60a6d2864a1715ddc32424a683ce8
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524837"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC のベスト プラクティス

この記事では、Azure のロールベースのアクセス制御 (Azure RBAC) を使用するためのベスト プラクティスについていくつか説明します。 これらのベスト プラクティスは、Azure RBAC に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

## <a name="only-grant-the-access-users-need"></a>ユーザーが必要なアクセスだけを許可する

Azure RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定のスコープで特定の操作のみを許可することができます。

アクセス制御戦略を計画する場合のベスト プラクティスは、ユーザーの作業を実行できる最低限の特権をユーザーに付与することです。 当初、より広範なスコープでより広範なロールを割り当てる方が便利に思われたとしても、避けてください。 カスタム ロールを作成する場合は、ユーザーが必要とするアクセス許可のみが含まれます。 ロールとスコープを制限することで、セキュリティ プリンシパルが侵害された場合にリスクにさらされるリソースを制限することができます。

次の図は、Azure RBAC を使用するための推奨パターンを示しています。

![Azure RBAC と最小限の特権](./media/best-practices/rbac-least-privilege.png)

ロールの割り当て方法について詳しくは、「[Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)」をご覧ください。

## <a name="limit-the-number-of-subscription-owners"></a>サブスクリプションの所有者の数を制限する

侵害された所有者による侵害の可能性を減らすため、サブスクリプション所有者は最大 3 人までにします。 この推奨事項は Microsoft Defender for Cloud で監視できます。 Defender for Cloud での他の ID とアクセスの推奨事項については、「[セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)」を参照してください。

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を使用する

悪意のあるサイバー攻撃から特権アカウントを保護するために、Azure Active Directory Privileged Identity Management (PIM) を使用して特権の露出時間を短縮し、レポートとアラートによって特権使用の可視性を向上できます。 PIM は、Azure AD と Azure のリソースへの Just-In-Time の特権アクセスを提供することで、特権アカウントを保護するのに役立ちます。 アクセスは、期限を決めて、その期限が過ぎると権限が自動的に取り消されるようにすることができます。 

詳しくは、「[Azure AD Privileged Identity Management とは](../active-directory/privileged-identity-management/pim-configure.md)」をご覧ください。

## <a name="assign-roles-to-groups-not-users"></a>ユーザーではなくグループにロールを割り当てる

ロール割り当てをより管理しやすくするために、ロールをユーザーに直接割り当てないようにします。 代わりに、グループにロールを割り当ててください。 [サブスクリプションあたりのロール割り当て数の制限](troubleshooting.md#azure-role-assignments-limit)がありますが、ロールをユーザーでなくグループに割り当てることで、この割り当て数を最小限に抑えることもできます。

## <a name="assign-roles-using-the-unique-role-id-instead-of-the-role-name"></a>ロール名の代わりに一意のロール ID を使用してロールを割り当てる

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

詳細については、[一意のロール ID および Azure PowerShell を使用したロールの割り当て](role-assignments-powershell.md#assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope)および[一意のロール ID および Azure CLI を使用したロールの割り当て](role-assignments-cli.md#assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure RBAC のトラブルシューティング](troubleshooting.md)
