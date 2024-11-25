---
title: ブループリント オペレーター用の環境を設定する
description: ブループリント オペレーターの Azure 組み込みロールで使用するように Azure 環境を構成する方法について説明します。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: 5c2da686a2a145c42e53260e96b95b2ae14c4770
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323874"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>ブループリント オペレーター用の環境を構成する

ブループリント定義とブループリント割り当ての管理は、異なるチームに割り当てることができます。 設計者やガバナンス チームがブループリント定義のライフサイクル管理を担当し、運用チームがこれらの集中管理されたブループリント定義の割り当ての管理を担当することがよくあります。

**ブループリント オペレーター** 組み込みロールは、この種のシナリオ専用に設計されています。 このロールにより、運用タイプのチームは組織のブループリント定義の割り当てを管理できますが、定義を変更することはできません。 これを行うには、Azure 環境でいくつかの構成が必要です。この記事では必要な手順について説明します。

## <a name="grant-permission-to-the-blueprint-operator"></a>ブループリント オペレーターにアクセス許可を付与する

最初の手順では、ブループリントの割り当てを行う予定のアカウントまたはセキュリティ グループ (推奨) に **ブループリント オペレーター** ロールを付与します。 この操作は、運用チームがブループリント割り当てのアクセス権を必要とするすべての管理グループとサブスクリプションを含む、管理グループ階層の最上位レベルで実行する必要があります。 これらのアクセス許可を付与する場合は、最小限の特権の原則に従うことをお勧めします。

1. (推奨) [セキュリティ グループを作成してメンバーを追加します](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. アカウントまたはセキュリティ グループに **ブループリント オペレーター** の [Azure ロールを割り当てます](../../../role-based-access-control/role-assignments-portal.md)

## <a name="user-assign-managed-identity"></a>ユーザー割り当てマネージド ID

ブループリント定義では、システム割り当てまたはユーザー割り当てのマネージド ID を使用できます。 ただし、**ブループリント オペレーター** ロールを使用する場合は、ユーザー割り当てのマネージド ID を使用するようにブループリント定義を構成する必要があります。 さらに、**ブループリント オペレーター** ロールを付与するアカウントまたはセキュリティ グループには、ユーザー割り当てのマネージド ID に **マネージド ID オペレーター** ロールが付与されている必要があります。 このアクセス許可がないと、アクセス許可がないためにブループリントの割り当てが失敗します。

1. 割り当てられたブループリントで使用する[ユーザー割り当てのマネージド ID を作成します](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

1. 目的のスコープのブループリント定義で必要なロールまたはアクセス許可を、ユーザー割り当てのマネージド ID に付与します。

1. アカウントまたはセキュリティ グループに **マネージド ID オペレーター** の [Azure ロールを割り当てます](../../../role-based-access-control/role-assignments-portal.md)。 ロールの割り当ての範囲を、新しいユーザー割り当てのマネージド ID に設定します。

1. **ブループリント オペレーター** として、新しいユーザー割り当て管理対象 ID を使用する [ブループリントを割り当てます](../create-blueprint-portal.md#assign-a-blueprint)。

## <a name="next-steps"></a>次のステップ

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。