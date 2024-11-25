---
title: Azure Virtual Desktop (クラシック) 内の委任されたアクセス - Azure
description: Azure Virtual Desktop (クラシック) デプロイ上で管理機能を委任する方法 (例を含む)。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9db8ad454ad38f24f32e05bf2f72d67ef7db1971
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752047"
---
# <a name="delegated-access-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop (クラシック) 内の委任されたアクセス

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../delegated-access-virtual-desktop.md)を参照してください。

Azure Virtual Desktop には委任されたアクセス モデルが用意され、これにより特定のユーザーにロールを割り当てることで、それらのユーザーが持つことのできるアクセス数を定義できます。 ロールの割り当てには、セキュリティ プリンシパル、ロールの定義、スコープの 3 つのコンポーネントがあります。 Azure Virtual Desktop の委任されたアクセス モデルは、Azure RBAC モデルに基づいています。 特定のロールの割り当てとそのコンポーネントの詳細については、「[Azure ロールベースのアクセス制御の概要](../../role-based-access-control/built-in-roles.md)」を参照してください。

Azure Virtual Desktop の委任されたアクセスでは、ロール割り当ての要素ごとに次の値がサポートされています。

* セキュリティ プリンシパル
    * ユーザー
    * サービス プリンシパル
* ロール定義
    * 組み込みのロール
* Scope
    * テナント グループ
    * テナント
    * ホスト プール
    * アプリ グループ

## <a name="built-in-roles"></a>組み込みのロール

Azure Virtual Desktop 内の委任されたアクセスには、ユーザーおよびサービス プリンシパルに割り当てることのできる組み込みロールの定義がいくつかあります。

* RDS 所有者は、リソースへのアクセスを含め、すべてを管理できます。
* RDS 共同作成者は、すべてを管理できますが、リソースにはアクセスできません。
* RDS 閲覧者は、すべてを閲覧できますが、変更を加えることはできません。
* RDS オペレーターは、診断アクティビティを表示できます。

## <a name="powershell-cmdlets-for-role-assignments"></a>ロール割り当てを目的とした PowerShell のコマンドレット

次のコマンドレットを実行して、ロールの割り当てを作成、表示、削除できます。

* **Get-RdsRoleAssignment** はロール割り当ての一覧を表示します。
* **New-RdsRoleAssignment** は新しいロール割り当てを作成します。
* **Remove-RdsRoleAssignment** はロールの割り当てを削除します。

### <a name="accepted-parameters"></a>使用できるパラメーター

基本的な 3 つのコマンドレットを次のパラメーターで変更できます。

* **AadTenantId**: サービス プリンシパルがメンバーとなる Azure Active Directory テナント ID を指定します。
* **AppGroupName**: リモート デスクトップのアプリ グループの名前です。
* **Diagnostics**: 診断のスコープを示します (**Infrastructure** または **Tenant** パラメーターのいずれかと組み合わせる必要があります)。
* **HostPoolName**: リモート デスクトップのホスト プールの名前です。
* **Infrastructure**: インフラストラクチャのスコープを示します。
* **RoleDefinitionName**: ユーザー、グループ、またはアプリに割り当てられているリモート デスクトップ サービスのロールベース アクセス制御のロールの名前 (リモート デスクトップ サービスの所有者、リモート デスクトップ サービスの閲覧者など)。
* **ServerPrincipleName**: Azure Active Directory アプリケーションの名前。
* **SignInName**: ユーザーの電子メール アドレスまたはユーザー プリンシパル名。
* **TenantName**: リモート デスクトップ テナントの名前。

## <a name="next-steps"></a>次のステップ

各ロールで使用できる PowerShell コマンドレットのより詳細な一覧については、「[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)」を参照してください。

Azure Virtual Desktop 環境を設定する方法のガイドについては、「[Azure Virtual Desktop 環境](environment-setup-2019.md)」を参照してください。
