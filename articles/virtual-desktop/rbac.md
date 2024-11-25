---
title: 組み込みロール Azure Virtual Desktop - Azure
description: Azure RBAC で使用可能な Azure Virtual Desktop の組み込みロールの概要について説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 25628d4982e2675a6ea818284f4c629b16f22064
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747941"
---
# <a name="built-in-roles-for-azure-virtual-desktop"></a>Azure Virtual Desktop の組み込みロール

Azure Virtual Desktop では、Azure ロールベースのアクセス制御 (RBAC) を使用して、ユーザーと管理者にロールを割り当てます。 これらのロールにより、特定のタスクを実行するための管理者権限が付与されます。 Azure RBAC の組み込みロールの詳細については、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

Azure の標準の組み込みロールは、所有者、共同作成者、および閲覧者です。 一方 Azure Virtual Desktop には、ホスト プール、アプリ グループ、およびワークスペースの管理者ロールを分けるための追加のロールがあります。 この分離により、管理タスクをより細かく制御できます。 これらのロールには、Azure の標準ロールと最小限の特権の手法に準拠した名前が付けられています。

Azure Virtual Desktop には、特定の所有者ロールはありません。 しかし、サービス オブジェクトには標準の所有者ロールを使用できます。

## <a name="desktop-virtualization-contributor"></a>デスクトップ仮想化共同作成者

デスクトップ仮想化共同作成者ロールを使用すると、デプロイのすべての側面を管理できます。 ただし、コンピューティング リソースへのアクセス許可は付与されません。 また、ユーザーまたはユーザー グループにアプリ グループを公開するには、ユーザー アクセス管理者ロールが必要です。


- Microsoft.DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>デスクトップ仮想化閲覧者

デスクトップ仮想化閲覧者ロールを使用すると、デプロイ内のすべてを表示できますが、変更を加えることはできません。

- Microsoft.DesktopVirtualization/\*/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-host-pool-contributor"></a>Desktop Virtualization Host Pool Contributor

ホスト プール共同作成者ロールを使用すると、リソースへのアクセスを含め、ホスト プールのすべての側面を管理できます。 仮想マシンを作成するには、追加の共同作成者ロールである仮想マシン共同作成者が必要です。 ポータルを使用してホスト プールを作成するには、AppGroup とワークスペース共同作成者ロールが必要です。または、デスクトップ仮想化共同作成者ロールを使用できます。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-host-pool-reader"></a>Desktop Virtualization Host Pool Reader

ホスト プール閲覧者ロールを使用すると、ホスト プール内のすべてを表示できますが、変更を加えることはできません。

- Microsoft.DesktopVirtualization/hostpools/\*/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-application-group-contributor"></a>Desktop Virtualization Application Group Contributor

アプリケーション グループ共同作成者ロールを使用すると、アプリ グループのすべての側面を管理できます。 アプリ グループをユーザーまたはユーザー グループに公開する場合は、ユーザー アクセス管理者ロールが必要です。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/applicationgroups/\*
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-application-group-reader"></a>Desktop Virtualization Application Group Reader

アプリケーション グループ閲覧者ロールを使用すると、アプリ グループ内のすべてを表示できますが、変更を加えることはできません。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/applicationgroups/\*/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-workspace-contributor"></a>Desktop Virtualization Workspace Contributor

ワークスペース共同作成者ロールを使用すると、ワークスペースのすべての側面を管理できます。 アプリ グループに追加されたアプリケーションに関する情報を取得するには、アプリケーション グループ閲覧者ロールも割り当てられている必要があります。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/workspaces/\*
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-workspace-reader"></a>Desktop Virtualization Workspace Reader

ワークスペース閲覧者ロールを使用すると、ワークスペース内のすべてを表示できますが、変更を加えることはできません。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/workspaces/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-user-session-operator"></a>Desktop Virtualization User Session Operator

ユーザー セッション オペレーター ロールを使用すると、メッセージを送信し、セッションを切断し、"ログオフ" 機能を使用してセッション ホストからセッションをサインアウトすることができます。 ただし、このロールを使用しても、セッション ホストの削除やドレイン モードの変更などのセッション ホスト管理を実行することはできません。 このロールでは割り当てを表示することはできますが、管理者を変更することはできません。 このロールは、特定のホスト プールに割り当てることをお勧めします。 このアクセス許可をリソース グループ レベルで付与すると、管理者には、リソース グループの配下にあるすべてのホスト プールに対する読み取りアクセス許可が付与されます。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-session-host-operator"></a>Desktop Virtualization Session Host Operator

セッション ホスト オペレーター ロールを使用すると、セッション ホストを表示および削除したり、ドレイン モードを変更したりできます。 ホスト プール オブジェクトに対する書き込みアクセス許可がないため、Azure portal を使用してセッション ホストを追加することはできません。 登録トークンが有効 (生成され、期限切れではない) で、仮想マシン共同作成者ロールを通じて管理者にコンピューティングのアクセス許可が付与されている場合は、このロールを使用して、Azure portal の外部のホスト プールにセッション ホストを追加できます。

次の一覧では、このロールがアクセスできるアクセス許可について説明しています。

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
