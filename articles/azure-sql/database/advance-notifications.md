---
title: 計画メンテナンス イベントの事前通知 (プレビュー)
description: Azure SQL Database の計画メンテナンスの前に通知を受け取ります。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: scott-kim-sql
ms.author: scottkim
ms.reviewer: mathoma, wiassaf
ms.date: 09/14/2021
ms.openlocfilehash: 89a3f86995d054573897202d2269e4f1f38821e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631193"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>計画メンテナンス イベントの事前通知 (プレビュー)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

事前通知 (プレビュー) は、既定でない[メンテナンス期間 (プレビュー)](maintenance-window.md)を使用するように構成されたデータベースに使用できます。 事前通知を使用すると、顧客は、計画されたイベントの 24 時間前までに通知が送信されるように構成できます。

計画メンテナンスが開始される 24 時間前までにテキスト、電子メール、Azure プッシュ通知、およびボイスメールを受け取ることができるように、通知を構成することができます。 メンテナンスが開始されたとき、およびメンテナンスが終了したときに、追加の通知が送信されます。

<<<<<<< HEAD
> [!Note]
> メンテナンス期間を選択する機能は、Azure SQL Managed Instance では使用できますが、事前通知は、現在のところ、Azure SQL Managed Instance では使用できません。
=======
事前通知は、**システムの既定の** メンテナンス期間オプションに対して構成することはできません。 事前通知を構成して有効にするには、**システムの既定** 以外のメンテナンス期間を選択します。

> [!NOTE]
> メンテナンス期間を選択する機能は、Azure SQL マネージド インスタンスでは使用できますが、事前通知は、現在のところ、Azure SQL マネージド インスタンスでは使用できません。 
>>>>>>> repo_sync_working_branch

## <a name="create-an-advance-notification"></a>事前通知の作成

事前通知は、メンテナンス期間が構成されている Azure SQL データベースで利用できます。 

通知を有効にするには、次の手順を実行します。  

1. [[計画メンテナンス]](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) ページに移動し、 **[正常性アラート]** を選択してから、 **[サービス正常性アラートの追加]** を選択します。

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="正常性アラートの新しいメニュー オプションを作成します":::

2. **[アクション]** セクションで、 **[アクション グループの追加]** を選択します。 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="アクション グループのメニュー オプションを追加します":::

3. **[アクション グループの作成]** フォームに入力し、 **[次へ: 通知]** を選択します。  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="[アクション グループの作成] フォーム":::

1. **[通知]** タブで、 **[通知の種類]** を選択します。 **[メール/SMS メッセージ/プッシュ/音声]** オプションは、最も柔軟性が高く、推奨されるオプションです。 通知を構成するには、ペンを選択します。  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="通知を構成します":::



   1. 開いた *[通知の追加または編集]* フォームに入力して、 **[OK]** を選択します。 

   2. [アクション] と [タグ] は省略できます。 ここでは、トリガーする追加のアクションを構成したり、タグを使用して Azure リソースを分類および整理したりすることができます。 

   4. **[確認および作成]** タブで詳細を確認し、 **[作成]** を選択します。 

7. 作成を選択すると、警告ルールの構成画面が開き、アクション グループが選択されます。 新しい警告ルールに名前を付けて、そのリソース グループを選択し、 **[警告ルールの作成]** を選択します。 

8. **[正常性アラート]** メニュー項目をもう一度クリックすると、アラートの一覧に新しいアラートが表示されます。 


設定が完了しました。 次回 Azure SQL の計画メンテナンス イベントが発生すると、事前通知が送信されます。

## <a name="receiving-notifications"></a>通知の受信

次の表に、受信する可能性がある一般情報の通知を示します。 

|Status|説明|
|:---|:---|
|**計画済みのデプロイ**| メンテナンス イベントの 24 時間前に受信します。 DATE の午後 5 時～午前 8 時 (ローカル時刻) にデータベース xyz のメンテナンスが予定されています。|
|**進行中** | データベース  *xyz*  のメンテナンスが開始されました。| 
|**完了** | データベース *xyz* のメンテナンスが完了しました。 |

次の表に、メンテナンスの進行中に送信される可能性がある追加の通知を示します。 

|Status|説明|
|:---|:---|
|**拡張** | データベース *xyz* のメンテナンスは進行中ですが、完了しませんでした。 メンテナンスは、次のメンテナンス期間に続行されます。| 
|**Canceled**| データベース *xyz* のメンテナンスはキャンセルされ、後で再スケジュールされます。 |
|**［ブロック済み］**|データベース *xyz* のメンテナンス中に問題が発生しました。 再開されると通知されます。| 
|**Resumed**|問題は解決されており、メンテナンスは次のメンテナンス期間に続行されます。|


## <a name="next-steps"></a>次のステップ

- [メンテナンス期間](maintenance-window.md)
- [メンテナンス期間に関する FAQ](maintenance-window-faq.yml)
- [Microsoft Azure のアラートの概要](../../azure-monitor/alerts/alerts-overview.md)
- [Azure Resource Manager のロールへのメール](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)
