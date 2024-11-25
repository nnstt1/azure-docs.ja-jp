---
title: Azure Policy を使用した Azure Web PubSub サービスのコンプライアンス
description: Azure Policy の組み込みポリシーを割り当て、Azure Web PubSub サービス リソースのコンプライアンスを監査します。
author: JialinXin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/25/2021
ms.author: jixin
ms.openlocfilehash: c2cc6b0a3b16d69a4ddeb94a8775ad5abc3afec8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714810"
---
# <a name="audit-compliance-of-azure-web-pubsub-service-resources-using-azure-policy"></a>Azure Policy を使用して Azure Web PubSub サービス リソースのコンプライアンスを監査する

[Azure Policy](../governance/policy/overview.md) は、ポリシーの作成、割り当て、管理に使用する Azure のサービスです。 これらのポリシーは、リソースにさまざまなルールと効果を適用して、それらのリソースが会社の標準とサービス レベル アグリーメントに準拠した状態に保たれるようにします。

この記事では、Azure Web PubSub サービスの組み込みポリシーについて説明します。 これらのポリシーを使用して、新規および既存の Web PubSub リソースのコンプライアンスを監査します。

Azure Policy を使用するのに料金は一切かかりません。

## <a name="built-in-policy-definitions"></a>組み込みのポリシー定義

次の組み込みポリシー定義は Azure Web PubSub サービスに固有のものです。

[!INCLUDE [azure-policy-reference-policies-web-pubsub](../../includes/policy/reference/bycat/policies-web-pubsub.md)]

## <a name="assign-policy-definitions"></a>ポリシー定義を割り当てる

* [Azure portal](../governance/policy/assign-policy-portal.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、[Resource Manager テンプレート](../governance/policy/assign-policy-template.md)、または Azure Policy SDK を使用してポリシー定義を割り当てます。
* ポリシーの割り当て範囲をリソース グループ、サブスクリプション、または [Azure 管理グループ](../governance/management-groups/overview.md)に設定します。 Web PubSub ポリシーの割り当ては、スコープ内の既存および新規の Web PubSub リソースに適用されます。
* [ポリシー適用](../governance/policy/concepts/assignment-structure.md#enforcement-mode)の有効と無効はいつでも切り替えられます。

> [!NOTE]
> ポリシーを割り当てるか、更新した後、定義されている範囲内のリソースに割り当てが適用されるまで少し時間がかかります。 詳細は、[ポリシー評価トリガー](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)を参照してください。

## <a name="review-policy-compliance"></a>ポリシーのコンプライアンスを確認する

Azure portal、Azure コマンドライン ツール、または Azure Policy SDK を使用し、ポリシー割り当てによって生成されたコンプライアンス情報にアクセスします。 詳細については、「[Azure リソースのコンプライアンス データを取得する](../governance/policy/how-to/get-compliance-data.md)」を参照してください。

リソースのコンプライアンス違反には多くの理由が考えられます。 理由や原因となった変更を特定する方法については、「[コンプライアンス違反の原因の特定](../governance/policy/how-to/determine-non-compliance.md)」をご覧ください。

### <a name="policy-compliance-in-the-portal"></a>ポータルのポリシー コンプライアンス:

1. **[すべてのサービス]** を選択し、**ポリシー** を検索します。
1. **[コンプライアンス]** を選択します。
1. フィルターを使用し、コンプライアンスの状態を限定するか、ポリシーを検索します。
   
    [ ![ポータルでのポリシーのコンプライアンス](./media/howto-monitor-azure-policy/azure-policy-compliance.png) ](./media/howto-monitor-azure-policy/azure-policy-compliance.png#lightbox)
2. ポリシーを選択し、集約コンプライアンスの詳細とイベントを確認します。 必要であれば、リソース コンプライアンスに対して特定の Web PubSub を選択します。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI のポリシー コンプライアンス

Azure CLI を利用してコンプライアンス データを取得することもできます。 たとえば、CLI で [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) コマンドを使用し、適用されている Azure Web PubSub サービス ポリシーのポリシー ID を取得します。

```azurecli
az policy assignment list --query "[?contains(displayName,'Web PubSub')].{name:displayName, ID:id}" --output table
```

サンプル出力:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure Web PubSub Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

次に、特定のリソース グループの下にあるリソースすべてのコンプライアンスの状態を JSON 形式で返すために、[az policy state list](/cli/azure/policy/state#az_policy_state_list) を実行します。

```azurecli
az policy state list --g <resourceGroup>
```

または、特定の Web PubSub リソースのコンプライアンスの状態を JSON 形式で返すために、[az policy state list](/cli/azure/policy/state#az_policy_state_list) を実行します。

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/WebPubSub/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>次のステップ

* Azure Policy の[定義](../governance/policy/concepts/definition-structure.md)と[効果](../governance/policy/concepts/effects.md)について知る

* [カスタム ポリシー定義](../governance/policy/tutorials/create-custom-policy-definition.md)を作成する

* Azure の[ガバナンス機能](../governance/index.yml)について知る


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/