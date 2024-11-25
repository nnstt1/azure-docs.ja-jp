---
title: Azure Automation のロールのアクセス許可とセキュリティを管理する
description: この記事では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを管理する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 67f7076852ffe810e213fcc7d8cb6188d6db405d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057854"
---
# <a name="manage-role-permissions-and-security-in-automation"></a>Azure Automation のロールのアクセス許可とセキュリティを管理する

Azure のリソースに対するアクセスは、Azure ロールベースのアクセス制御 (Azure RBAC) で管理できます。 [Azure RBAC](../role-based-access-control/overview.md) を使用して、チーム内の職務を分離し、それぞれの職務に必要なアクセス権のみをユーザー、グループ、アプリケーションに付与することができます。 Azure portal、Azure コマンドライン ツール、Azure Management API を使用して、ロールベースのアクセス権をユーザーに付与できます。

## <a name="roles-in-automation-accounts"></a>Automation アカウントのロール

Azure Automation でアクセス権を付与するには、Automation アカウント スコープのユーザー、グループ、アプリケーションに適切な Azure ロールを割り当てます。 Automation アカウントでは次の組み込みロールがサポートされています。

| **ロール** | **説明** |
|:--- |:--- |
| 所有者 |Automation アカウント内のすべてのリソースおよびアクションへのアクセスは、所有者ロールによって許可されます。Automation アカウントを管理するためのアクセス権を他のユーザー、グループ、アプリケーションに付与することもできます。 |
| Contributor |Automation アカウントに対する他のユーザーのアクセス許可に変更を加えることを除くすべての作業は共同作成者ロールで行うことができます。 |
| Reader |閲覧者ロールでは、Automation アカウントのすべてのリソースを表示できますが、それらに変更を加えることはできません。 |
| Automation 共同作成者 | Automation 共同作成者ロールを使用すると、Automation アカウントに対する他のユーザーのアクセス許可を変更する以外の、Automation アカウント内のすべてのリソースを管理できます。 |
| Automation Operator |Automation オペレーター ロールでは、Runbook の名前とプロパティの表示、Automation アカウント内のすべての Runbook のジョブの作成と管理を実行できます。 ご利用の Automation アカウントのリソース (資格情報アセットや Runbook など) を閲覧したり改変したりできないよう保護したうえで、同じ組織のメンバーにのみ、それらの Runbook の実行を許可する必要がある場合、このロールを活用できます。 |
|Automation ジョブ オペレーター|Automation ジョブ オペレーター ロールでは、Automation アカウント内のすべての Runbook のジョブの作成と管理を実行できます。|
|Automation Runbook オペレーター|Automation Runbook オペレーター ロールでは、Runbook の名前とプロパティを表示できます。|
| Log Analytics 共同作成者 | Log Analytics 共同作成者ロールでは、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、Azure Automation 機能の追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。|
| Log Analytics 閲覧者 | Log Analytics 閲覧者ロールでは、すべての監視データの表示と検索、および監視設定の表示を行うことができます。 これには、すべての Azure リソースに対する Azure Diagnostics の構成の表示が含まれます。 |
| Monitoring Contributor | 共同作成者の監視ロールでは、すべての監視データを読み取り、監視設定を更新できます。|
| Monitoring Reader | 閲覧者の監視ロールでは、すべての監視データを読み取ることができます。 |
| User Access Administrator |Azure Automation アカウントに対するユーザー アクセスは、ユーザー アクセスの管理者ロールで管理できます。 |

## <a name="role-permissions"></a>ロールのアクセス許可

以降の表は、各ロールに割り当てられている具体的なアクセス許可の説明です。 アクセス許可を与える Actions のほか、それらを制限する NotActions が含まれている場合があります。

### <a name="owner"></a>所有者

所有者は、アクセス権を含めすべてを管理できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|Actions|説明|
|---|---|
|Microsoft.Automation/automationAccounts/|あらゆる種類のリソースの作成と管理。|

### <a name="contributor"></a>Contributor

共同作業者は、アクセス権以外のすべてを管理できます。 次の表は、このロールに付与および否認されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|あらゆる種類のリソースの作成と管理|
|**否認されるアクション**||
|Microsoft.Authorization/*/Delete| ロールとロール割り当ての削除。       |
|Microsoft.Authorization/*/Write     |  ロールとロール割り当ての作成。       |
|Microsoft.Authorization/elevateAccess/Action    | ユーザー アクセス管理者を作成する機能の拒否。       |

### <a name="reader"></a>Reader

閲覧者は、Automation アカウントのすべてのリソースを表示できますが、それらに変更を加えることはできません。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Automation アカウントのすべてのリソースの表示。 |

### <a name="automation-contributor"></a>Automation 共同作成者

Automation 共同作成者は、アクセス権以外の、Automation アカウント内のすべてのリソースを管理できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/*|Automation アカウントのあらゆる種類のリソースの作成と管理。|
|Microsoft.Authorization/*/read|ロールとロール割り当ての読み取り。|
|Microsoft.Resources/deployments/*|リソース グループ デプロイの作成と管理。|
|Microsoft.Resources/subscriptions/resourceGroups/read|リソース グループ デプロイの読み取り。|
|Microsoft.Support/*|サポート チケットの作成と管理。|
|Microsoft.Insights/ActionGroups/*|アクション グループの読み取り/書き込み/削除を実行します。|
|Microsoft.Insights/ActivityLogAlerts/*|アクティビティ ログ アラートの読み取り/書き込み/削除を実行します。|
|Microsoft.Insights/diagnosticSettings/*|診断設定の読み取り/書き込み/削除を実行します。|
|Microsoft.Insights/MetricAlerts/*|ほぼリアルタイムのメトリック アラートの読み取り/書き込み/削除を実行します。|
|Microsoft.Insights/ScheduledQueryRules/*|Azure Monitor のログ アラートを読み取り/書き込み/削除します。|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics ワークスペースのキーを一覧表示します|

> [!NOTE]
> Automation 共同作成者ロールを使用すると、マネージド ID を使用する (ターゲット リソースに対して適切なアクセス許可が設定されている場合) か、実行アカウントを使用して、任意のリソースにアクセスできます。 Automation 実行アカウントは、既定では、そのサブスクリプションに対する共同作成者権限で構成されます。 最小特権の原則に従って、Runbook を実行するのに必要なアクセス許可のみを慎重に割り当てます。 たとえば、Automation アカウントが Azure VM の開始または停止を行うためにのみ必要な場合は、その実行アカウントまたはマネージド ID に割り当てられるアクセス許可は、VM の開始または停止のためのみのものである必要があります。 同様に、Runbook が BLOB ストレージから読み取りを行う場合は、読み取り専用アクセス許可を割り当てます。
> 
> アクセス許可を割り当てる場合は、マネージド ID に割り当てられた Azure ロール ベースのアクセス制御 (RBAC) を使用することをお勧めします。 システムまたはユーザー割り当てのマネージド ID の使用について、その有効期間中の管理とガバナンスを含む、[ベスト アプローチ](../active-directory/managed-identities-azure-resources/managed-identity-best-practice-recommendations.md)に関する推奨事項をご確認ください。

### <a name="automation-operator"></a>Automation Operator

Automation オペレーターは、ジョブの作成と管理、Automation アカウント内のすべての Runbook の名前とプロパティの読み取りを実行できます。

>[!NOTE]
>個々の Runbook へのオペレーターのアクセスを制御する場合は、このロールを設定しないでください。 代わりに、**Automation ジョブ オペレーター** ロールと **Automation Runbook オペレーター** ロールを組み合わせて使用します。

次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Authorization/*/read|承認の読み取り。|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker リソースを読み取ります。|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook のジョブの一覧表示。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|一時停止されているジョブの再開。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|進行中のジョブの取り消し。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|ジョブ ストリームと出力の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/output/read|ジョブの出力を取得します。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|進行中のジョブの一時停止。|
|Microsoft.Automation/automationAccounts/jobs/write|ジョブの作成。|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Azure Automation ジョブ スケジュールを取得します。|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Azure Automation ジョブ スケジュールを作成します。|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Automation アカウントにリンクされているワークスペースを取得します。|
|Microsoft.Automation/automationAccounts/read|Azure Automation アカウントを取得します。|
|Microsoft.Automation/automationAccounts/runbooks/read|Azure Automation Runbook を取得します。|
|Microsoft.Automation/automationAccounts/schedules/read|Azure Automation スケジュール資産を取得します。|
|Microsoft.Automation/automationAccounts/schedules/write|Azure Automation スケジュール資産を作成または更新します。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |ロールとロール割り当ての読み取り。         |
|Microsoft.Resources/deployments/*      |リソース グループ デプロイの作成と管理。         |
|Microsoft.Insights/alertRules/*      | アラート ルールの作成と管理。        |
|Microsoft.Support/* |サポート チケットの作成と管理。|

### <a name="automation-job-operator"></a>Automation ジョブ オペレーター

Automation ジョブ オペレーター ロールは、Automation アカウントのスコープで付与されます。 これにより、アカウント内のすべての Runbook に対してジョブの作成と管理を行うためのアクセス許可がオペレーターに与えられます。 ジョブ オペレーター ロールに Automation アカウントを含むリソース グループに対する読み取りアクセス許可が付与されている場合、そのロールのメンバーは Runbook を開始できます。 ただし、それらを作成、編集、または削除することはできません。

次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Authorization/*/read|承認の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook のジョブの一覧表示。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|一時停止されているジョブの再開。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|進行中のジョブの取り消し。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|ジョブ ストリームと出力の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|進行中のジョブの一時停止。|
|Microsoft.Automation/automationAccounts/jobs/write|ジョブの作成。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  ロールとロール割り当ての読み取り。       |
|Microsoft.Resources/deployments/*      |リソース グループ デプロイの作成と管理。         |
|Microsoft.Insights/alertRules/*      | アラート ルールの作成と管理。        |
|Microsoft.Support/* |サポート チケットの作成と管理。|

### <a name="automation-runbook-operator"></a>Automation Runbook オペレーター

Automation Runbook オペレーター ロールは、Runbook のスコープで付与されます。 Automation Runbook オペレーターは、Runbook の名前とプロパティを表示できます。 このロールと **Automation ジョブ オペレーター** ロールを組み合わせると、オペレーターは、Runbook に対するジョブの作成と管理も実行できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Runbook の一覧表示。        |
|Microsoft.Authorization/*/read      | 承認の読み取り。        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |ロールとロール割り当ての読み取り。         |
|Microsoft.Resources/deployments/*      | リソース グループ デプロイの作成と管理。         |
|Microsoft.Insights/alertRules/*      | アラート ルールの作成と管理。        |
|Microsoft.Support/*      | サポート チケットの作成と管理。        |

### <a name="log-analytics-contributor"></a>Log Analytics 共同作成者

Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、機能の追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.Automation/automationAccounts/*|Automation アカウントの管理。|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|仮想マシン拡張機能の作成と管理。|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|従来のストレージ アカウントの一覧表示。|
|Microsoft.Compute/virtualMachines/extensions/*|従来の仮想マシン拡張機能の作成と管理。|
|Microsoft.Insights/alertRules/*|アラート ルールの読み取り/書き込み/削除を実行します。|
|Microsoft.Insights/diagnosticSettings/*|診断設定の読み取り/書き込み/削除を実行します。|
|Microsoft.OperationalInsights/*|Azure Monitor ログの管理。|
|Microsoft.OperationsManagement/*|ワークスペースでの Azure Automation 機能の管理。|
|Microsoft.Resources/deployments/*|リソース グループ デプロイの作成と管理。|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|リソース グループ デプロイの作成と管理。|
|Microsoft.Storage/storageAccounts/listKeys/action|ストレージ アカウント キーの一覧表示。|
|Microsoft.Support/*|サポート チケットの作成と管理。|

### <a name="log-analytics-reader"></a>Log Analytics 閲覧者

Log Analytics 閲覧者は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure Diagnostics 構成の表示など、監視設定の表示を行うことができます。 次の表は、このロールに付与または否認されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Azure Monitor ログのクエリの管理。|
|Microsoft.OperationalInsights/workspaces/search/action|Azure Monitor ログのデータの検索。|
|Microsoft.Support/*|サポート チケットの作成と管理。|
|**否認されるアクション**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|共有アクセス キーを読み取ることはできません。|

### <a name="monitoring-contributor"></a>Monitoring Contributor

"共同作成者の監視" は、すべての監視データを読み取り、監視設定を更新できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.AlertsManagement/alerts/*|アラートの管理。|
|Microsoft.AlertsManagement/alertsSummary/*|アラート ダッシュボードの管理。|
|Microsoft.Insights/AlertRules/*|アラート ルールの管理。|
|Microsoft.Insights/components/*|Application Insights コンポーネントの管理。|
|Microsoft.Insights/DiagnosticSettings/*|診断設定の管理。|
|Microsoft.Insights/eventtypes/*|サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。|
|Microsoft.Insights/LogDefinitions/*|このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。|
|Microsoft.Insights/MetricDefinitions/*|メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。|
|Microsoft.Insights/Metrics/*|リソースのメトリックを読み取ります。|
|Microsoft.Insights/Register/Action|Microsoft Insights プロバイダーを登録します。|
|Microsoft.Insights/webtests/*|Application Insights の Web テストの管理。|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Azure Monitor ログのソリューション パックの管理。|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Azure Monitor ログの保存した検索条件の管理。|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics ワークスペースを検索します。|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics ワークスペースのキーを一覧表示します。|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Azure Monitor ログのストレージ分析情報構成の管理。|
|Microsoft.Support/*|サポート チケットの作成と管理。|
|Microsoft.WorkloadMonitor/workloads/*|ワークロードの管理。|

### <a name="monitoring-reader"></a>Monitoring Reader

"閲覧者の監視" は、すべての監視データを読み取ることができます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics ワークスペースを検索します。|
|Microsoft.Support/*|サポート チケットの作成と管理|

### <a name="user-access-administrator"></a>User Access Administrator

ユーザー アクセス管理者は、Azure リソースへのユーザー アクセスを管理できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|すべてのリソースの読み取り|
|Microsoft.Authorization/*|承認の管理|
|Microsoft.Support/*|サポート チケットの作成と管理|

## <a name="feature-setup-permissions"></a>機能のセットアップのアクセス許可

以降のセクションでは、Update Management および変更履歴とインベントリの機能を有効にするうえで最低限必要なアクセス許可について説明します。

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>VM から Update Management および変更履歴とインベントリを有効にするためのアクセス許可

|**操作**  |**権限**  |**最小スコープ**  |
|---------|---------|---------|
|新しいデプロイを記述する      | Microsoft.Resources/deployments/*          |サブスクリプション          |
|新しいリソース グループを記述する      | Microsoft.Resources/subscriptions/resourceGroups/write        | サブスクリプション          |
|新しい既定のワークスペースを作成する      | Microsoft.OperationalInsights/workspaces/write         | Resource group         |
|新しいアカウントを作成する      |  Microsoft.Automation/automationAccounts/write        |Resource group         |
|ワークスペースとアカウントをリンクする      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|ワークスペース</br>Automation アカウント
|MMA 拡張機能を作成する      | Microsoft.Compute/virtualMachines/write         | 仮想マシン         |
|保存した検索条件を作成する      | Microsoft.OperationalInsights/workspaces/write          | ワークスペース         |
|スコープ構成を作成する      | Microsoft.OperationalInsights/workspaces/write          | ワークスペース         |
|オンボード状態の確認 - ワークスペースを読み取る      | Microsoft.OperationalInsights/workspaces/read         | ワークスペース         |
|オンボード状態の確認 - アカウントのリンクされたワークスペースのプロパティを読み取る     | Microsoft.Automation/automationAccounts/read      | Automation アカウント        |
|オンボード状態の確認 - ソリューションを読み取る      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | 解決策         |
|オンボード状態の確認 - VM を読み取る      | Microsoft.Compute/virtualMachines/read         | 仮想マシン         |
|オンボード状態の確認 - アカウントを読み取る      | Microsoft.Automation/automationAccounts/read  |  Automation アカウント   |
| VM のオンボード ワークスペース確認<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | サブスクリプション         |
| Log Analytics プロバイダーの登録 |Microsoft.Insights/register/action | サブスクリプション|

<sup>1</sup> VM ポータルのエクスペリエンスで機能を有効にするには、このアクセス許可が必要です。

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Automation アカウントから Update Management および変更履歴とインベントリを有効にするためのアクセス許可

|**操作**  |**権限** |**最小スコープ**  |
|---------|---------|---------|
|新しいデプロイを作成する     | Microsoft.Resources/deployments/*        | サブスクリプション         |
|新しいリソース グループの作成     | Microsoft.Resources/subscriptions/resourceGroups/write         | サブスクリプション        |
|AutomationOnboarding ブレード - 新しいワークスペースを作成する     |Microsoft.OperationalInsights/workspaces/write           | Resource group        |
|AutomationOnboarding ブレード - リンクされたワークスペースを読み取る     | Microsoft.Automation/automationAccounts/read        | Automation アカウント       |
|AutomationOnboarding ブレード - ソリューションを読み取る     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | 解決策        |
|AutomationOnboarding ブレード - ワークスペースを読み取る     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | ワークスペース        |
|ワークスペースとアカウントのリンクを作成する     | Microsoft.OperationalInsights/workspaces/write        | ワークスペース        |
|Shoebox のアカウントを記述する      | Microsoft.Automation/automationAccounts/write        | Account        |
|保存した検索条件を作成および編集する     | Microsoft.OperationalInsights/workspaces/write        | ワークスペース        |
|スコープ構成を作成および編集する     | Microsoft.OperationalInsights/workspaces/write        | ワークスペース        |
| Log Analytics プロバイダーの登録 |Microsoft.Insights/register/action | サブスクリプション|
|**手順 2 - 複数の VM を有効にする**     |         |         |
|VMOnboarding ブレード - MMA 拡張機能を作成する     | Microsoft.Compute/virtualMachines/write           | 仮想マシン        |
|保存した検索条件を作成および編集する     | Microsoft.OperationalInsights/workspaces/write           | ワークスペース        |
|スコープ構成を作成および編集する  | Microsoft.OperationalInsights/workspaces/write   | ワークスペース|

## <a name="custom-azure-automation-contributor-role"></a>カスタム Azure Automation 共同作成者ロール

Microsoft は、Log Analytics 共同作成者ロールから Automation アカウントの権限を削除する予定です。 現在、前述した組み込みの [Log Analytics 共同作成者](#log-analytics-contributor)ロールは特権をサブスクリプションの [Contributor](./../role-based-access-control/built-in-roles.md#contributor) ロールに昇格させることができます。 Automation アカウントの実行アカウントは最初、サブスクリプションの共同作成者権限で構成されるため、攻撃者がそれを利用し、サブスクリプションの共同作成者として新しい Runbook を作成し、コードを実行できます。

このセキュリティ リスクの結果として、Log Analytics 共同作成者ロールを利用して Automation ジョブを実行しないことをお勧めします。 代わりに、Azure Automation 共同作成者カスタム ロールを作成し、Automation アカウント関連のアクションに利用してください。 このカスタム ロールは、次の手順に従って作成します。

### <a name="create-using-the-azure-portal"></a>Azure Portal を使用した作成

以下の手順に従って、Azure portal で Azure Automation カスタム ロールを作成します。 詳細については、「[Azure カスタム ロール](./../role-based-access-control/custom-roles.md)」を参照してください。

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。 JSON ファイルで、**assignableScopes** プロパティの値をサブスクリプションの GUID に置き換えます。

   ```json
   {
    "properties": {
        "roleName": "Automation Account Contributor (Custom)",
        "description": "Allows access to manage Azure Automation and its resources",
        "assignableScopes": [
            "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Authorization/*/read",
                    "Microsoft.Insights/alertRules/*",
                    "Microsoft.Insights/metrics/read",
                    "Microsoft.Insights/diagnosticSettings/*",
                    "Microsoft.Resources/deployments/*",
                    "Microsoft.Resources/subscriptions/resourceGroups/read",
                    "Microsoft.Automation/automationAccounts/*",
                    "Microsoft.Support/*"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
      }
   }
   ```

1. 「[Azure portal を使用して Azure カスタム ロールを作成または更新する](../role-based-access-control/custom-roles-portal.md#start-from-json)」の説明に従って、残りの手順を完了します。 「[手順 3: 基本](../role-based-access-control/custom-roles-portal.md#step-3-basics)」では、次の点に注意してください。

    -  **[カスタム ロール名]** フィールドに「**Automation account Contributor (custom)** 」と入力するか、ご利用の名前付け規則に合った名前を入力します。
    - **[ベースラインのアクセス許可]** で **[Start from JSON]\(JSON から始める\)** を選択します。 次に、前に保存したカスタム JSON ファイルを選択します。

1. 残りの手順を完了し、カスタム ロールを確認して作成します。 カスタム ロールがすべての場所に表示されるまで、数分かかることがあります。

### <a name="create-using-powershell"></a>PowerShell を使用して作成する

以下の手順に従って、PowerShell で Azure Automation カスタム ロールを作成します。 詳細については、「[Azure カスタム ロール](./../role-based-access-control/custom-roles.md)」を参照してください。

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。 JSON ファイルで、**AssignableScopes** プロパティの値をサブスクリプションの GUID に置き換えます。

   ```json
   { 
       "Name": "Automation account Contributor (custom)",
       "Id": "",
       "IsCustom": true,
       "Description": "Allows access to manage Azure Automation and its resources",
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Insights/alertRules/*",
           "Microsoft.Insights/metrics/read",
           "Microsoft.Insights/diagnosticSettings/*",
           "Microsoft.Resources/deployments/*",
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Automation/automationAccounts/*",
           "Microsoft.Support/*"
       ],
       "NotActions": [],
       "AssignableScopes": [
           "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
       ] 
   } 
   ```

1. 「[Azure PowerShell を使用して Azure カスタム ロールを作成または更新する](./../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template)」の説明に従って、残りの手順を完了します。 カスタム ロールがすべての場所に表示されるまで、数分かかることがあります。

## <a name="update-management-permissions"></a>Update Management のアクセス許可

Update Management を使用して、同じ Azure Active Directory (Azure AD) テナントの複数のサブスクリプションのマシンまたは Azure Lighthouse を使用している複数のテナントに対する更新プログラムのデプロイの評価とスケジュールを実行できます。 次の表は、更新プログラムのデプロイを管理するために必要なアクセス許可の一覧です。

|**リソース** |**ロール** |**スコープ** |
|---------|---------|---------|
|Automation アカウント |[カスタム Azure Automation 共同作成者ロール](#custom-azure-automation-contributor-role) |Automation アカウント |
|Automation アカウント |Virtual Machine Contributor  |アカウントのリソース グループ  |
|Log Analytics ワークスペース  Log Analytics 共同作成者|Log Analytics ワークスペース |
|Log Analytics ワークスペース |Log Analytics 閲覧者|サブスクリプション|
|解決策 |Log Analytics 共同作成者 |解決策|
|仮想マシン |Virtual Machine Contributor |仮想マシン |
|**仮想マシン上でのアクション** | | |
|更新スケジュールの実行履歴の表示 ([ソフトウェア更新プログラムの構成マシン実行](/rest/api/automation/softwareupdateconfigurationmachineruns)) |Reader |Automation アカウント |
|**仮想マシン上でのアクション** |**権限** | |
|更新スケジュールの作成 ([ソフトウェア更新プログラムの構成](/rest/api/automation/softwareupdateconfigurations)) |Microsoft.Compute/virtualMachines/write |静的 VM リストとリソース グループの場合 |
|更新スケジュールの作成 ([ソフトウェア更新プログラムの構成](/rest/api/automation/softwareupdateconfigurations)) |Microsoft.OperationalInsights/workspaces/analytics/query/action |Azure 以外の動的リスト使用時のワークスペース リソース ID の場合。|

## <a name="configure-azure-rbac-for-your-automation-account"></a>Automation アカウントの Azure RBAC を構成する

次のセクションでは、[Azure portal](#configure-azure-rbac-using-the-azure-portal) と [PowerShell](#configure-azure-rbac-using-powershell) を使用してご利用の Automation アカウントの Azure RBAC を構成する方法について説明します。

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Azure Portal を使用した Azure RBAC の構成

1. [Azure Portal](https://portal.azure.com/) にログインし、[Automation アカウント] ページから、ご利用の Automation アカウントを開きます。
2. **アクセス制御 (IAM)** をクリックし、[アクセス制御 (IAM)] ページを開きます。 このページを使用すると、ご利用の Automation アカウントを管理するための新しいユーザー、グループ、アプリケーションを追加できるほか、その Automation アカウント用に構成できる既存のロールを確認できます。
3. **[ロールの割り当て]** タブをクリックします。

   ![Access button](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>新しいユーザーの追加とロールの割り当て

1. [アクセス制御 (IAM)] ページで、 **[+ ロールの割り当ての追加]** をクリックします。 この操作により、[ロールの割り当ての追加] ページが開きます。そこでは、ユーザー、グループ、またはアプリケーションを追加し、対応するロールを割り当てることができます。

2. 利用可能なロールの一覧からロールを選択します。 Automation アカウントでサポートされている任意の組み込みロールを選択してもかまいません。また、自分で定義したカスタム ロールを選択することもできます。

3. アクセス許可を付与するユーザーの名前を **[選択]** フィールドに入力します。 一覧からユーザーを選択し、 **[保存]** をクリックします。

   ![Add users](media/automation-role-based-access-control/automation-04-add-users.png)

   ここで、そのユーザーが [ユーザー] ページに追加され、選択したロールが割り当てられていることを確認する必要があります。

   ![List users](media/automation-role-based-access-control/automation-05-list-users.png)

   [ロール] ページから、ユーザーにロールを割り当てることもできます。

4. [アクセス制御 (IAM)] ページから **[ロール]** をクリックして [ロール] ページを開きます。 ロールの名前と、そのロールに割り当てられているユーザー数およびグループ数を確認できます。

    ![[ユーザー] ページからのロールの割り当て](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > ロールベースのアクセス制御は、Automation アカウント スコープでのみ設定でき、Automation アカウントより下のリソースで設定することはできません。

#### <a name="remove-a-user"></a>ユーザーの削除

Automation アカウントの管理に関与しないユーザーや既に退社したユーザーについては、アクセス権を削除することができます。 ユーザーを削除する手順を次に示します。

1. [アクセス制御 (IAM)] ページで、削除するユーザーを選択し、 **[削除]** をクリックします。
2. 割り当ての詳細ウィンドウで、 **[削除]** ボタンをクリックします。
3. **[はい]** をクリックして削除を確定します。

   ![Remove users](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>PowerShell を使用した Azure RBAC の構成

Automation アカウントに対するロールベースのアクセスは、次の [Azure PowerShell コマンドレット](../role-based-access-control/role-assignments-powershell.md)を使用して構成することもできます。

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) を使用すると、Azure Active Directory で利用できるすべての Azure ロールの一覧が表示されます。 このコマンドレットを `Name` パラメーターと共に使用すると、特定のロールで実行できるすべての操作を一覧表示できます。

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

出力の例を次に示します。

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用すると、特定のスコープにおける Azure ロールの割り当ての一覧が表示されます。 このコマンドレットにパラメーターを指定しなかった場合、対象サブスクリプションで行われたすべてのロールの割り当てが返されます。 指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを一覧表示するには、`ExpandPrincipalGroups` パラメーターを使用します。

**例:** Automation アカウント内のすべてのユーザーとそのロールを一覧表示するには、次のコマンドレットを使用します。

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

出力の例を次に示します。

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

特定のスコープのユーザー、グループ、アプリケーションにアクセス権を割り当てるには、[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) を使用します。

**例:** Automation アカウント スコープのユーザーに対して "Automation オペレーター" ロールを割り当てるには、次のコマンドを使用します。

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

出力の例を次に示します。

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

[Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment): 特定のスコープの指定したユーザー、グループ、またはアプリケーションのアクセス権を削除します。

**例:** Automation アカウント スコープの Automation オペレーター ロールからユーザーを削除するには、次のコマンドを使用します。

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

前の例では、`sign-in ID of a user you wish to remove`、`SubscriptionID`、`Resource Group Name`、`Automation account name` をアカウントの詳細に置き換えます。 ユーザー ロールの割り当ての削除を続行する前に確認を求められた場合は、 **[はい]** を選択します。

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Automation オペレーター ロールのユーザー エクスペリエンス - Automation アカウント

Automation アカウント スコープ ビューで Automation オペレーター ロールに割り当てられているユーザーが、自分が割り当てられている Automation アカウントを表示した場合、そのユーザーに表示されるのはその Automation アカウントで作成された Runbook、Runbook ジョブ、およびスケジュールの一覧のみです。 このユーザーは、それらの項目の定義を表示することはできません。 このユーザーは、Runbook ジョブの開始、停止、一時停止、再開、スケジュール設定を行うことができます。 ただし、構成、Hybrid Runbook Worker グループ、DSC ノードなど、Automation の他のリソースにアクセスすることはできません。

![リソースへのアクセス権がない](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Runbook のための Azure RBAC の構成

Azure Automation では、Azure ロールを特定の Runbook に割り当てることができます。 これには、次のスクリプトを実行して、ユーザーを特定の Runbook に追加します。 このスクリプトを実行できるのは、Automation アカウント管理者またはテナント管理者です。

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

スクリプトが実行されたら、ユーザーに Azure portal にログインしてもらい、さらに **[すべてのリソース]** を選択してもらいます。 一覧内で、ユーザーは自分が Automation Runbook オペレーターとして追加された Runbook を確認することができます。

![ポータルの Runbook Azure RBAC](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Automation オペレーター ロールのユーザー エクスペリエンス - Runbook

Runbook スコープで Automation オペレーター ロールに割り当てられたユーザーが、割り当てられた Runbook を表示する場合、そのユーザーが行えるのは Runbook を開始して Runbook ジョブを表示することだけです。

![開始のみにアクセス](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>次のステップ

* PowerShell を利用し、Azure RBAC に関する詳細を見つける方法については、「[Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-powershell.md)」を参照してください。
* Runbook の種類について詳しくは、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
* Runbook を開始するには、「[Azure Automation で Runbook を開始する](start-runbooks.md)」を参照してください。
