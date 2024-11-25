---
title: REST API を使用して Azure ロールを割り当てる - Azure RBAC
description: REST API と Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 0c59db98f3f38a7e715c0dce77c397f0e2562343
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356661"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>REST API を使用して Azure ロールを割り当てる

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] この記事では、REST API を使用してロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Azure ロールを割り当てる

ロールを割り当てるには、[ロールの割り当て - 作成](/rest/api/authorization/roleassignments/create) REST API を使用し、セキュリティ プリンシパル、ロールの定義、スコープを指定します。 この API を呼び出すには、`Microsoft.Authorization/roleAssignments/write` アクションのアクセス権が必要です。 組み込みロールのうち、このアクションのアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。

1. [ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list) REST API を使用するか、[組み込みロール](built-in-roles.md)を参照し、割り当てるロール定義の識別子を取得します。

1. GUID ツールを使用し、ロール割り当て識別子に使用する一意の識別子を生成します。 この識別子の形式は `00000000-0000-0000-0000-000000000000` になります。

1. 次の要求と本文から開始します。

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. URI の *{scope}* をロールの割り当てのスコープに変更します。

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    > | `subscriptions/{subscriptionId1}` | サブスクリプション |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | リソース |

    前の例で、microsoft.web は App Service インスタンスを参照するリソース プロバイダーです。 同様に、他の任意のリソース プロバイダーを使用してスコープを指定できます。 詳細については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」およびサポートされている「[Azure リソース プロバイダーの操作](resource-provider-operations.md)」を参照してください。  

1. *{roleAssignmentId}* を、ロールの割り当ての GUID 識別子に置き換えます。

1. 要求本文内で、 *{scope}* をロールの割り当てのスコープに変更します。

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    > | `subscriptions/{subscriptionId1}` | サブスクリプション |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* を、ロールの定義の識別子に置き換えます。

1. *{principalId}* を、ロールが割り当てられるユーザー、グループ、またはサービス プリンシパルのオブジェクト識別子に置き換えます。

次の要求と本文では、サブスクリプションのスコープでユーザーに[バックアップ リーダー](built-in-roles.md#backup-reader) ロールが割り当てられます。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

出力例を次に示します。

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>新しいサービス プリンシパル

新しいサービス プリンシパルを作成し、そのサービス プリンシパルにロールをすぐに割り当てようとすると、場合によってはそのロールの割り当てが失敗することがあります。 たとえば、新しいマネージド ID を作成し、そのサービス プリンシパルにロールを割り当てようとすると、ロールの割り当てが失敗する可能性があります。 このエラーの原因は、レプリケーションの遅延である可能性があります。 サービス プリンシパルは 1 つのリージョンに作成されます。ただし、ロールの割り当ては、サービス プリンシパルがまだレプリケートされていない別のリージョンで発生する可能性があります。

このシナリオに対処するには、[Role Assignments - Create](/rest/api/authorization/roleassignments/create) REST API を使用し、`principalType` プロパティを `ServicePrincipal` に設定します。 また、`apiVersion` を `2018-09-01-preview` 以降に設定する必要もあります。

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>次のステップ

- [REST API を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-rest.md)
- [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API リファレンス](/rest/api/azure/)
- [REST API を使用して Azure カスタム ロールを作成または更新する](custom-roles-rest.md)
