---
title: コンプライアンス違反の原因の特定
description: リソースのコンプライアンス違反には多くの理由が考えられます。 コンプライアンス違反の原因を確認する方法について説明します。
ms.date: 09/01/2021
ms.topic: how-to
ms.openlocfilehash: 3c4076673adfe3253a418cc648592e72a8979b5a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433812"
---
# <a name="determine-causes-of-non-compliance"></a>コンプライアンス違反の原因の特定

Azure リソースにポリシー ルールへのコンプライアンス違反が確認された場合は、リソースがルールのどの部分に準拠していないのかを理解することをお勧めします。 また、どのような変更によって、以前は準拠していたリソースがコンプライアンス違反となったのかを把握することも有用です。 この情報を検索するには 2 つの方法があります。

- [コンプライアンスの詳細](#compliance-details)
- [変更履歴 (プレビュー)](#change-history)

## <a name="compliance-details"></a>コンプライアンスの詳細

リソースがコンプライアンス違反となった場合は、そのリソースのコンプライアンスの詳細を **[Policy compliance]** (ポリシー コンプライアンス) ページで確認できます。 [ポリシー準拠情報の詳細] ウィンドウには、次の情報が含まれています。

- 名前、種類、場所、リソース ID などのリソースの詳細
- コンプライアンス対応状態と、現在のポリシー割り当ての最後の評価のタイムスタンプ
- リソースのコンプライアンス違反の _理由_ の一覧

> [!IMPORTANT]
> _コンプライアンス違反_ コンプライアンス違反のリソースのコンプライアンスの詳細に、該当のリソースでのプロパティの現在の値が表示されるときには、ユーザーにその **種類** のリソースに対する **読み取り** 操作が必要です。 たとえば、_コンプライアンス違反_ のリソースが **Microsoft.Compute/virtualMachines** の場合、ユーザーには、**Microsoft.Compute/virtualMachines/read** 操作が必要です。 ユーザーに必要な操作がない場合は、アクセス エラーが表示されます。

コンプライアンスの詳細を表示するには、次の手順に従います。

1. Azure portal で **[すべてのサービス]** を選択し、 **[Policy]** を検索して選択することで、Azure Policy サービスを起動します。

1. **[概要]** ページまたは **[コンプライアンス]** ページで、 **[コンプライアンスの状態]** が _[非対応]_ になっているポリシーを選択します。

1. **[ポリシーのコンプライアンス]** ページの **[リソースのコンプライアンス]** タブで、 **[コンプライアンスの状態]** が _[非対応]_ になっているリソースを選択したままにする (または右クリックする) か、リソースの省略記号を選択します。 次に、 **[ポリシー準拠状況の詳細]** を選択します。

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="[リソース コンプライアンス] タブの 'ポリシー準拠状況の詳細' リンクのスクリーンショット。" border="false":::

1. **[ポリシー準拠状況の詳細]** ウィンドウに、現在のポリシー割り当てに対するリソースの最新の評価からの情報が表示されます。 この例では、フィールド **Microsoft.Sql/servers/version** が _12.0_ であることが検出されていますが、ポリシー定義では _14.0_ を必要としています。 リソースのコンプライアンス違反の理由が複数ある場合は、このウィンドウにそれぞれの理由が表示されます。

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="[コンプライアンスの詳細] ウィンドウと、現在の値が 12 で対象の値が 14 であるというコンプライアンス違反の理由を示したスクリーンショット。" border="false":::

   **AuditIfNotExists** または **deployIfNotExists** ポリシー定義について、詳細に **details.type** プロパティと省略可能なプロパティが含まれます。 一覧については、「[auditIfNotExists プロパティ](../concepts/effects.md#auditifnotexists-properties)」と「[deployIfNotExists プロパティ](../concepts/effects.md#deployifnotexists-properties)」を参照してください。 **最後に評価されたリソース** は、定義の **details** セクションからの関連リソースです。

   部分的な **deployIfNotExists** 定義の例:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="評価されたリソース数を含む ifNotExists の [コンプライアンスの詳細] ウィンドウのスクリーンショット。" border="false":::

> [!NOTE]
> データを保護するために、プロパティ値が _secret_ の場合は現在の値にアスタリスクが表示されます。

これらの詳細は、リソースが現在コンプライアンス違反であることの理由を説明するものですが、コンプライアンス違反の原因となった変更がリソースに対して行われたタイミングでは表示しません。 この情報を得るには、「[変更履歴 (プレビュー)](#change-history)」を参照してください。

### <a name="compliance-reasons"></a>コンプライアンスの理由

[リソース マネージャー モード](../concepts/definition-structure.md#resource-manager-modes)と [リソース プロバイダー モード](../concepts/definition-structure.md#resource-provider-modes)では、コンプライアンス違反の "_理由_" が異なります。

#### <a name="general-resource-manager-mode-compliance-reasons"></a>リソース マネージャー モードの一般的なコンプライアンスの理由

次の表は、[リソース マネージャー モード](../concepts/definition-structure.md#resource-manager-modes)の各 "_理由_" と、ポリシー定義内でのその[条件](../concepts/definition-structure.md#conditions)の対応を示しています。

|理由 |条件 |
|-|-|
|現在の値には、キーとしてターゲット値を含める必要があります。 |containsKey、または notContainsKey **ではない** |
|現在の値には、ターゲット値を含める必要があります。 |contains、または notContains **ではない** |
|現在の値は、ターゲット値と等しくなければなりません。 |equals、または notEquals **ではない** |
|現在の値は、ターゲット値より小さくなければなりません。 |less、または greaterOrEquals **ではない** |
|現在の値は、ターゲット値以上でなければなりません。 |greaterOrEquals、または less **ではない** |
|現在の値は、ターゲット値より大きくなければなりません。 |greater、または lessOrEquals **ではない** |
|現在の値は、ターゲット値以下でなければなりません。 |lessOrEquals、または greater **ではない** |
|現在の値は存在している必要があります。 |exists |
|現在の値は、ターゲット値に含まれている必要があります。 |in、または notIn **ではない** |
|現在の値は、ターゲット値とパターン一致する必要があります。 |like、または notLike **ではない** |
|現在の値は、ターゲット値と一致する必要があります (大文字/小文字を区別する)。 |match、または notMatch **ではない** |
|現在の値は、ターゲット値と一致する必要があります (大文字/小文字を区別しない)。 |matchInsensitively、または notMatchInsensitively **ではない** |
|現在の値には、キーとしてターゲット値を含めることはできません。 |notContainsKey、または containsKey **ではない**|
|現在の値には、ターゲット値を含めることはできません。 |notContains、または contains **ではない** |
|現在の値を、ターゲット値と等しくすることはできません。 |notEquals、または equals **ではない** |
|現在の値は存在してはなりません。 |exists **ではない**  |
|現在の値は、ターゲット値に含まれていてはなりません。 |notIn、または in **ではない** |
|現在の値は、ターゲット値とパターン一致してはなりません。 |notLike、または like **ではない** |
|現在の値は、ターゲット値と一致してはなりません (大文字/小文字を区別する)。 |notMatch、または match **ではない** |
|現在の値は、ターゲット値と一致してはなりません (大文字/小文字を区別しない)。 |notMatchInsensitively、または matchInsensitively **ではない** |
|ポリシー定義の効果の詳細と一致する関連リソースがありません。 |**then.details.type** で定義されている種類のリソース、およびポリシー ルールの **if** 部分に定義されているリソースに関連したリソースが存在しません。 |

#### <a name="aks-resource-provider-mode-compliance-reasons"></a>AKS リソース プロバイダー モードのコンプライアンスの理由

次の表は、`Microsoft.Kubernetes.Data`
[リソース プロバイダー モード](../concepts/definition-structure.md#resource-provider-modes)の各 "_理由_" と、ポリシー定義内での[制約テンプレート](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates)の状態との対応を示しています。

|理由 |制約テンプレートの理由の説明 |
|-|-|
|Constraint/TemplateCreateFailed |制約またはテンプレートがリソースのメタデータ名によるクラスター上の既存の制約またはテンプレートと一致していないポリシー定義に対して、リソースが作成に失敗しました。 |
|Constraint/TemplateUpdateFailed |制約またはテンプレートがリソースのメタデータ名によるクラスター上の既存の制約またはテンプレートと一致しているポリシー定義に対して、制約またはテンプレートが更新に失敗しました。 |
|Constraint/TemplateInstallFailed |制約またはテンプレートのビルドに失敗し、作成または更新操作のためにクラスターにインストールできませんでした。 |
|ConstraintTemplateConflicts |テンプレートが、ソースの異なる同じテンプレート名を使用して、1 つ以上のポリシー定義と競合しています。 |
|ConstraintStatusStale |既存の "監査" 状態はありますが、過去 1 時間以内に Gatekeeper による監査は実行されていません。 |
|ConstraintNotProcessed |状態は存在せず、過去 1 時間以内に Gatekeeper による監査は実行されていません。 |
|InvalidConstraint/Template |API サーバーで、YAML が正しくないためにリソースが拒否されました。 この理由は、パラメーターの型の不一致が原因になる場合もあります (整数型に文字列が指定された場合など)

> [!NOTE]
> クラスター上に既存のポリシー割り当てと制約テンプレートが既にある場合、その制約またはテンプレートが失敗すると、既存の制約またはテンプレートを維持することでクラスターが保護されます。 クラスターでは、ポリシー割り当てまたはアドオンの自己復旧でエラーが解決されるまで、非対応として報告します。 競合の処理の詳細については、「[制約テンプレートの競合](../concepts/policy-for-kubernetes.md#constraint-template-conflicts)」を参照してください。

## <a name="component-details-for-resource-provider-modes"></a>リソース プロバイダー モードのコンポーネントの詳細

[リソース プロバイダー モード](../concepts/definition-structure.md#resource-provider-modes)による割り当てについては、_準拠していない_ リソースを選択して、詳細なビューを開きます。 **[コンポーネント コンプライアンス]** タブの下に、割り当てられたポリシーのリソース プロバイダー モードに固有の追加情報があり、_準拠していない_**コンポーネント** と **コンポーネント ID** が表示されます。

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="[Component Compliance]\(コンポーネントのコンプライアンス\) タブと、リソース プロバイダー モードの割り当てに対するコンプライアンスの詳細のスクリーンショット。" border="false":::

## <a name="compliance-details-for-guest-configuration"></a>ゲスト構成のコンプライアンスの詳細

"_ゲスト構成_" カテゴリ内のポリシー定義については、仮想マシン内で評価された複数の設定が存在する可能性があり、設定ごとの詳細を表示する必要があります。 たとえば、セキュリティ設定の一覧を監査していて、そのうちの 1 つだけが "_非対応_" 状態の場合は、対応していない特定の設定とその理由を把握しておく必要があります。

また、仮想マシンに直接サインインするアクセス権を持たない可能性もありますが、仮想マシンが _準拠していない_ 理由についてレポートする必要があります。

### <a name="azure-portal"></a>Azure portal

ポリシー準拠の詳細を表示するには、前のセクションと同じ手順に従ってください。

[コンプライアンスの詳細] ウィンドウ ビューで、 **[前回の評価済みリソース]** のリンクを選択します。

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="auditIfNotExists 定義のコンプライアンスの詳細を表示したスクリーンショット。" border="false":::

**[ゲスト割り当て]** ページには、利用可能なコンプライアンスの詳細すべてが表示されます。 ビューの行はそれぞれ、マシン内で実行された評価を表します。 **[理由]** 列には、ゲストの割り当てが "_非準拠_" である理由が示されています。 たとえば、パスワード ポリシーを監査する場合、 **[理由]** 列には、各設定の現在の値を含むテキストが表示されます。

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="ゲスト割り当てのコンプライアンスの詳細のスクリーンショット。" border="false":::

### <a name="view-configuration-assignment-details-at-scale"></a>大規模な構成割り当ての詳細の表示

ゲスト構成機能は、Azure Policy の割り当ての外部で使用できます。
たとえば、[Azure AutoManage](../../../automanage/automanage-virtual-machines.md) でゲスト構成割り当てが作成されたり、[マシンを配置するときに構成を割り当て](guest-configuration-create-assignment.md)たりする場合があります。

テナント全体のゲスト構成割り当てをすべて表示するには、Azure portal から **[ゲスト割り当て]** ページを開きます。 詳細なコンプライアンス情報を表示するには、"名前" 列のリンクを使用して各割り当てを選択します。

:::image type="content" source="../media/determine-non-compliance/guest-config-assignment-view.png" alt-text="[ゲスト割り当て] ページのスクリーンショット。" border="true":::

## <a name="change-history-preview"></a><a name="change-history"></a>変更履歴 (プレビュー)

新しい **パブリック プレビュー** の一環として、[完全モードの削除](../../../azure-resource-manager/templates/deployment-complete-mode-deletion.md)をサポートするすべての Azure リソースについて、過去 14 日間の変更履歴が使用可能です。 変更履歴では、変更が検出された日時についての詳細と、各変更の "_差分表示_" が提供されます。 変更の検出は、Azure Resource Manager のプロパティが追加、削除、変更されるとトリガーされます。

1. Azure portal で **[すべてのサービス]** を選択し、 **[Policy]** を検索して選択することで、Azure Policy サービスを起動します。

1. **[概要]** ページまたは **[コンプライアンス]** ページで、任意の **[コンプライアンスの状態]** のポリシーを選択します。

1. **[Policy compliance]** (ポリシー コンプライアンス) ページの **[リソースのコンプライアンス]** タブで、リソースを選択します。

1. **[リソース コンプライアンス]** ページで **[Change History (preview)]\(変更履歴 (プレビュー)\)** タブを選択します。 検出された変更がある場合は、その一覧が表示されます。

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="[リソースのコンプライアンス] ページの [変更履歴] タブと検出された変更の時刻のスクリーンショット。" border="false":::

1. 検出された変更のいずれかを選択します。 **[変更履歴]** ページに、リソースの _差分表示_ が示されます。

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="[変更履歴] ページにおけるプロパティの前後の状態の変更履歴の差分表示を示したスクリーンショット。" border="false":::

"_差分表示_" は、リソースの変更を識別するのに役立ちます。 検出された変更が、リソースの現在のコンプライアンス対応状態に関連していない場合があります。

変更履歴データは、[Azure Resource Graph](../../resource-graph/overview.md) によって提供されます。 Azure portal の外部でこの情報を照会するには、「[Get resource changes (リソース変更を取得する)](../../resource-graph/how-to/get-resource-changes.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。