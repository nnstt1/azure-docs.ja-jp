---
title: サービス プロバイダーのアクティビティを監視する
description: お客様は、ログに記録されたアクティビティを監視することにより、Azure Lighthouse 経由でサービス プロバイダーによって実行されたアクションを確認できます。
ms.date: 07/16/2021
ms.topic: how-to
ms.openlocfilehash: 9f24126bc8d3252de3926e72aea2915092206e38
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389154"
---
# <a name="monitor-service-provider-activity"></a>サービス プロバイダーのアクティビティを監視する

[Azure Lighthouse](../overview.md) に委任されたサブスクリプションのあるお客様は、[Azure アクティビティ ログ データを表示](../../azure-monitor/essentials/platform-logs-overview.md)して、実行されたすべてのアクションを確認できます。 これにより顧客は、サービス プロバイダーが実行している操作と、顧客独自の Azure Active Directory (Azure AD) テナント内のユーザーが実行した操作を完全に可視化できます。

## <a name="view-activity-log-data"></a>アクティビティ ログ データを表示する

[アクティビティ ログ](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)は、Azure portal の **[モニター]** メニューから表示することができます。 特定のサブスクリプションに対して結果を制限するには、フィルターを使用して特定のサブスクリプションを選択します。 また、プログラムで[アクティビティ ログ イベントを表示および取得する](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)こともできます。

> [!NOTE]
> Azure Lighthouse にお客様のテナント内の委任サブスクリプションがオンボードされたときに、サービス プロバイダーのテナント内のユーザーに[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール (または、閲覧者アクセスを含む別の組み込みロール) が付与されている場合、これらのユーザーは、そのサブスクリプションに対するアクティビティ ログの結果を表示することができます。

アクティビティ ログには、操作の名前とその状態が、実行された日付および時刻と共に表示されます。 **[イベント開始者]** 列には、操作を実行したユーザー (Azure Lighthouse を通じて操作しているサービス プロバイダーのテナント内のユーザーと、お客様独自のテナント内のユーザーのどちらの場合も) が表示されます。 テナント、またはそのサブスクリプションについてユーザーに割り当てられているロールではなく、ユーザーの名前が表示されることに注意してください。

ログに記録されたアクティビティは、過去 90 日間について Azure portal に格納されます。 このデータを 90 日間より長く格納する方法については、[Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する](../../azure-monitor/essentials/activity-log.md)ことに関するページを参照してください。

> [!NOTE]
> サービス プロバイダーのユーザーはアクティビティ ログに表示されますが、これらのユーザーとそのロールの割り当ては、 **[アクセス制御 (IAM)]** にも、また API 経由でロールの割り当て情報を取得したときも表示されません。

## <a name="set-alerts-for-critical-operations"></a>重要な操作のアラートを設定する

サービス プロバイダー (またはお客様独自のテナント内のユーザー) が実行している重要な操作を把握するには、[アクティビティ ログ アラート](../../azure-monitor/alerts/activity-log-alerts.md)を作成することをお勧めします。 たとえば、サブスクリプションに対するすべての管理操作を追跡したり、特定のリソース グループ内の仮想マシンが削除されたときに通知を受け取ったりすることができます。 アラートを作成すると、お客様独自のテナント内のユーザーと、すべての管理テナント内のユーザーによって実行されたアクションがアラートに含められます。

詳細については、[アクティビティ ログ アラートの作成と管理](../../azure-monitor/alerts/alerts-activity-log.md)に関するページを参照してください。

## <a name="create-log-queries"></a>ログ クエリを作成する

ログ クエリは、ログに記録されたアクティビティを分析したり、特定の項目に焦点を絞ったりするために役立ちます。 たとえば、監査で、サブスクリプションに対して実行されるすべての管理レベルのアクションに関するレポートを作成することが必要になる場合があります。 これらのアクションのみをフィルター処理するクエリを作成し、結果をユーザー、日付、または別の値で並べ替えることができます。

詳細については、「[Azure Monitor のログ クエリの概要](../../azure-monitor/logs/log-query-overview.md)」を参照してください。

## <a name="view-user-activity-across-domains"></a>複数ドメインにわたってユーザー アクティビティを表示する

複数のドメインにわたって個々のユーザーからアクティビティを表示するには、[[ドメインごとのアクティビティ ログ]](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) サンプル ブックを使用します。

結果はドメイン名でフィルター処理できます。 カテゴリ、レベル、リソース グループなどの追加のフィルターを適用することもできます。

## <a name="next-steps"></a>次のステップ

- [委任を監査および制限する](view-manage-service-providers.md#audit-and-restrict-delegations-in-your-environment)方法について学習します。
- 詳細については、「[Azure Monitor の概要](../../azure-monitor/index.yml)」を参照してください。
- Azure portal で[サービス プロバイダーのサービスを表示および管理する](view-manage-service-providers.md)方法について説明します。
