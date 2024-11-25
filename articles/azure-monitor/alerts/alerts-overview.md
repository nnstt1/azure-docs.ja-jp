---
title: Azure でのアラートと通知監視の概要
description: Azure Monitor のアラートの概要
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ee0d6cd4c895bbcd78767776a86bd63cfa4f5242
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747714"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure のアラートの概要 

この記事では、アラートの概要、利点、および使用方法について説明します。  

## <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure のアラートの概要

Azure Monitor の監視データを使用してインフラストラクチャまたはアプリケーションに問題が見つかったとき、アラートによって事前通知されます。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 

## <a name="overview"></a>概要

次の図は、アラートのフローを示したものです。 

![アラート フローの図](media/alerts-overview/Azure-Monitor-Alerts.svg)

アラート ルールは、アラート、およびアラートが発生したときに実行されるアクションとは別になっています。 アラート ルールによってアラートのターゲットと条件が取得されます。 アラート ルールは、有効または無効の状態にすることができます。 有効になっているアラートだけが発生します。 

アラート ルールの主な属性は次のとおりです。

**ターゲット リソース** - アラートに使用できるスコープとシグナルを定義します。 ターゲットには、任意の Azure リソースを指定できます。 ターゲットの例:

- 仮想マシン
- ストレージ アカウント。
- Log Analytics ワークスペース。
- Application Insights。 

特定のリソース (仮想マシンなど) では、アラート ルールのターゲットとして複数のリソースを指定できます。

**シグナル** - ターゲット リソースによって出力されます。 シグナルにすることができるのは、メトリック、アクティビティ ログ、Application Insights、およびログです。

**条件** - ターゲット リソースに適用されるシグナルとロジックの組み合わせ。 例 : 

- CPU 使用率 > 70%
- サーバー応答時間 > 4 ミリ秒 
- ログ クエリの結果数 > 100

**アラート名** - ユーザーによって構成されたアラート ルールの特定の名前です。

**アラートの説明** - ユーザーによって構成されたアラート ルールの説明です。

**重大度** - アラート ルールで指定されている条件が満たされた場合のアラートの重大度。 重大度の範囲は 0 ～ 4 です。

- 重大度 0 = 重大
- 重大度 1 = エラー
- 重大度 2 = 警告
- 重大度 3 = 情報
- 重大度 4 = 詳細情報 

**アクション** - アラートが発生したときに特定のアクションが実行されます。 詳細については、[アクション グループ](../alerts/action-groups.md)に関する記事をご覧ください。

## <a name="what-you-can-alert-on"></a>アラートできるもの

[データ ソースの監視](./../agents/data-sources.md)に関するページで説明されているように、メトリックとログについてアラートできます。 シグナルには次が含まれていますが、これらに限定されません。

- メトリックの値
- ログの検索クエリ
- アクティビティ ログのイベント
- 基になっている Azure プラットフォームの正常性
- Web サイトの可用性のテスト

## <a name="manage-alerts"></a>Manage alerts

アラートの状態を設定して、それが解決プロセス内のどこにあるかを指定できます。 アラート ルールで指定されている基準が満たされると、アラートが作成または生成されます。アラートの状態は "*新規*" です。 アラートを確認した場合や、アラートを終了した場合は、その状態を変更できます。 状態の変更はすべて、アラートの履歴に格納されます。

次のアラートの状態がサポートされています。

| State | 説明 |
|:---|:---|
| 新規 | 問題が検出されていますが、まだレビューされていません。 |
| [Acknowledged] (確認済み) | 管理者がアラートをレビューし、それに対する作業を開始しました。 |
| クローズ | 問題が解決されました。 アラートが終了されされた後、それを再び開いて別の状態に変更できます。 |

*アラートの状態* は異なり、*監視条件* には依存しません。 アラートの状態は、ユーザーによって設定されます。 監視条件は、システムによって設定されます。 アラートが発生すると、アラートの監視条件は *Fired* に設定されます。アラート発生の原因になった状態が解消されると、監視条件は *Resolved* に設定されます。 

アラートの状態は、ユーザーが変更するまで変わりません。 [アラートとスマート グループの状態を変更する方法](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)について参照してください。

## <a name="alerts-experience"></a>アラート エクスペリエンス 
既定の [アラート] ページには、特定の時間枠内に作成されたアラートの概要が表示されます。 ここには、重大度ごとのアラートの合計が、重大度ごとの各状態にあるアラートの総数を識別する列と共に表示されます。 任意の重大度を選択すると、その重大度でフィルター処理された [[すべてのアラート]](#all-alerts-page) ページが開きます。

代わりに、[REST API を使用し、サブスクリプションに生成されたアラート インスタンスをプログラムで列挙](#manage-your-alert-instances-programmatically)できます。

> [!NOTE]
   >  過去 30 日以内に生成されたアラートのみにアクセスできます。

サブスクリプションまたはフィルター パラメーターを変更して、ページを更新することができます。

![[アラート] ページのスクリーンショット](media/alerts-overview/alerts-page.png)

ページの上部にあるドロップダウン メニューで値を選択することによって、このビューをフィルター処理できます。

| 列 | 説明 |
|:---|:---|
| サブスクリプション | アラートを表示する Azure サブスクリプションを選択します。 必要に応じて、すべてのサブスクリプションを選択できます。 このビューには、選択したサブスクリプション内のアクセス権のあるアラートのみが含まれます。 |
| Resource group | 1 つのリソース グループを選択します。 このビューには、選択されたリソース グループ内のターゲットを含むアラートのみが含まれます。 |
| 時間の範囲 | このビューには、選択された時間枠内に発生したアラートのみが含まれます。 サポートされる値は、過去 1 時間、過去 24 時間、過去 7 日間、および過去 30 日間です。 |

[アラート] ページの上部にある次の値を選択すると、別のページが開きます。

| 値 | 説明 |
|:---|:---|
| アラート合計数 | 選択された条件に一致するアラートの総数。 この値を選択すると、フィルター処理されていない [すべてのアラート] ビューが開きます。 |
| スマート グループ | 選択された条件に一致する、アラートから作成されたスマート グループの総数。 この値を選択すると、[すべてのアラート] ビューにスマート グループの一覧が開きます。
| アラート ルールの合計 | 選択されたサブスクリプションおよびリソース グループ内のアラート ルールの総数。 この値を選択すると、選択されたサブスクリプションおよびリソース グループに対してフィルター処理された [ルール] ビューが開きます。


## <a name="manage-alert-rules"></a>アラート ルールの管理
**[ルール]** ページを表示するには、 **[アラート ルールの管理]** を選択します。 [ルール] ページは、複数の Azure サブスクリプションにまたがるすべてのアラート ルールを管理するための単一の場所です。 ここにはすべてのアラート ルールが一覧表示され、それをターゲット リソース、リソース グループ、ルール名、または状態に基づいて並べ替えることができます。 このページで、アラート ルールの編集、有効化、または無効化を行うことができます。  

 ![[ルール] ページのスクリーンショット](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>アラート ルールを作成する
アラート ルールは、監視サービスやシグナルの種類には関係なく、一貫した方法で作成できます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
新しいアラート ルールを作成する方法は、次のとおりです。
1. アラートの _ターゲット_ を選択します。
1. そのターゲットに使用できるシグナルから _シグナル_ を選択します。
1. そのシグナルからのデータに適用される _ロジック_ を指定します。

この簡素化された作成プロセスでは、ユーザーは Azure リソースを選択する前に、サポートされている監視ソースやシグナルを把握しておく必要はなくなります。 使用可能なシグナルの一覧は、ユーザーが選択したターゲット リソースに基づいて自動的にフィルター処理されます。 また、そのターゲットに基づき、アラート ルールのロジックの定義を自動的に案内されます。  

アラート ルールを作成する方法の詳細については、「[Azure Monitor を使用してアラートを作成、表示、管理する](../alerts/alerts-metric.md)」を参照してください。

アラートは複数の Azure 監視サービス全体で使用できます。 これらの各サービスを使用する方法およびタイミングについては、「[Azure のアプリケーションおよびリソースの監視](../overview.md)」を参照してください。 


## <a name="all-alerts-page"></a>[すべてのアラート] ページ 
**[すべてのアラート]** ページを表示するには、 **[アラート合計数]** を選択します。 ここでは、選択された時間枠内に作成されたアラートの一覧を表示できます。 個々のアラートの一覧、またはそれらのアラートを含むスマート グループの一覧のどちらかを表示できます。 ページの上部にあるバナーを選択すると、ビューが切り替わります。

![[すべてのアラート] ページのスクリーンショット](media/alerts-overview/all-alerts-page.png)

ページの上部にあるドロップダウン メニュー内の次の値を選択することによって、ビューをフィルター処理できます。

| 列 | 説明 |
|:---|:---|
| サブスクリプション | アラートを表示する Azure サブスクリプションを選択します。 必要に応じて、すべてのサブスクリプションを選択できます。 このビューには、選択したサブスクリプション内のアクセス権のあるアラートのみが含まれます。 |
| Resource group | 1 つのリソース グループを選択します。 このビューには、選択されたリソース グループ内のターゲットを含むアラートのみが含まれます。 |
| リソースの種類 | 1 つ以上のリソースの種類を選択します。 このビューには、選択された種類のターゲットを含むアラートのみが含まれます。 この列は、リソース グループを指定した後でのみ使用できます。 |
| リソース | リソースを選択します。 このビューには、そのリソースをターゲットとして含むアラートのみが含まれます。 この列は、リソースの種類を指定した後でのみ使用できます。 |
| 重大度 | アラートの重大度を選択するか、または **[すべて]** を選択してすべての重大度のアラートを含めます。 |
| 監視条件 | 監視条件を選択するか、 **[すべて]** を選択してすべての条件のアラートを含めます。 |
| アラートの状態 | アラートの状態を選択するか、 **[すべて]** を選択してすべての状態のアラートを含めます。 |
| サービスの監視 | サービスを選択するか、または **[すべて]** を選択してすべてのサービスを含めます。 そのサービスをターゲットとして使用してルールによって作成されたアラートのみが含まれます。 |
| 時間の範囲 | このビューには、選択された時間枠内に発生したアラートのみが含まれます。 サポートされる値は、過去 1 時間、過去 24 時間、過去 7 日間、および過去 30 日間です。 |

表示する列を選択するには、ページの上部にある **[列]** を選択します。 

## <a name="alert-details-page"></a>[アラートの詳細] ページ
アラートを選択すると、このページにアラートの詳細が表示され、その状態を変更できます。

![[アラートの詳細] ページのスクリーンショット](media/alerts-overview/alert-detail2.png)

[アラートの詳細] ページには、以下のセクションが含まれています。

| Section | 説明 |
|:---|:---|
| まとめ | アラートに関するプロパティやその他の重大な情報を表示します。 |
| 履歴 | アラートによって実行された各アクションと、アラートに加えられたすべての変更を一覧表示します。 現在は、状態の変更に制限されています。 |
| 診断 | アラートが含まれているスマート グループに関する情報。 "*アラートの数*" は、そのスマート グループに含まれているアラートの数を示します。 アラートの一覧ページ内の時間フィルターに関係なく、過去 30 日間に作成された、同じスマート グループ内の他のアラートが含まれます。 アラートを選択すると、その詳細が表示されます。 |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>アラート インスタントの Azure ロールベースのアクセス制御 (Azure RBAC)

アラート インスタンスを使用および管理するには、ユーザーに[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)または[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader)のいずれかの Azure 組み込みロールが必要です。 これらのロールは、サブスクリプション レベルからリソース レベルでの詳細な割り当てまで、Azure Resource Manager のすべてのスコープでサポートされています。 たとえば、ユーザーが仮想マシン `ContosoVM1` に対する "監視共同作成者" アクセス権のみを持っている場合は、`ContosoVM1` で生成されたアラートのみを使用および管理できます。

## <a name="manage-your-alert-instances-programmatically"></a>プログラムによるアラート インスタンスの管理

サブスクリプションに対して生成されたアラートをプログラムで照会したい場合があります。 たとえば、Azure portal の外部でカスタム ビューを作成したり、アラートを分析してパターンと傾向を特定したりする場合です。

発生したアラートのクエリには、[Azure Resource Graph](../../governance/resource-graph/overview.md) と `AlertsManagementResources` スキーマを使用することをお勧めします。 Resource Graph は、複数のサブスクリプションにわたって生成されたアラートを管理しなければならない場合にお勧めします。

次の Resource Graph REST API の要求例では、1 つのサブスクリプション内の前日のアラートを返します。

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "alertsmanagementresources | where properties.essentials.lastModifiedDateTime > ago(1d) | project alertInstanceId = id, parentRuleId = tolower(tostring(properties['essentials']['alertRule'])), sourceId = properties['essentials']['sourceCreatedId'], alertName = name, severity = properties.essentials.severity, status = properties.essentials.monitorCondition, state = properties.essentials.alertState, affectedResource = properties.essentials.targetResourceName, monitorService = properties.essentials.monitorService, signalType = properties.essentials.signalType, firedTime = properties['essentials']['startDateTime'], lastModifiedDate = properties.essentials.lastModifiedDateTime, lastModifiedBy = properties.essentials.lastModifiedUserName"
}
```

この Resource Graph クエリの結果は、ポータルで Azure Resource Graph Explorer を使用して確認することもできます: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/alertsmanagementresources%0A%7C%20where%20properties.essentials.lastModifiedDateTime%20%3E%20ago(1d)%0A%7C%20project%20alertInstanceId%20%3D%20id%2C%20parentRuleId%20%3D%20tolower(tostring(properties%5B 'essentials'%5D%5B'alertRule'%5D))%2C%20sourceId%20%3D%20properties%5B'essentials'%5D%5B'sourceCreatedId'%5D%2C%20alertName%20%3D%20name%2C%20severity%20%3D%20properties.essentials.severity%2C%20status%20%3D%20properties.essentials.monitorCondition%2C%20state%20%3D%20properties.essentials.alertState%2C%20affectedResource%20%3D%20properties.essentials.targetResourceName%2C%20monitorService%20%3D%20properties.essentials.monitorService%2C%20signalType%20%3D%20properties.essentials.signalType%2C%20firedTime%20%3D%20properties%5B'essentials'%5D%5B'startDateTime'%5D%2C%20lastModifiedDate%20%3D%20properties.essentials.lastModifiedDateTime%2C%20lastModifiedBy%20%3D%20properties.essentials.lastModifiedUserName)

より小規模なクエリのシナリオでは、または、発生したアラートの更新が目的である場合は、[Alert Management REST API](/rest/api/monitor/alertsmanagement/alerts) を使用することもできます。

## <a name="smart-groups"></a>スマート グループ

スマート グループは機械学習アルゴリズムに基づくアラートの集計であり、アラートのノイズの削減とトラブルシューティングに役立ちます。 [スマート グループに関する詳細](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)および[スマート グループを管理する方法](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)を参照してください。

## <a name="next-steps"></a>次のステップ

- [スマート グループの詳細](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [アクション グループの詳細](../alerts/action-groups.md)
- [Azure でのアラート インスタンスの管理](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [スマート グループの管理](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Azure アラートの価格についての詳細](https://azure.microsoft.com/pricing/details/monitor/)
