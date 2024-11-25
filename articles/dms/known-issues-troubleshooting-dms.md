---
title: 一般的な問題 - Azure Database Migration Service
description: Azure Database Migration Service の使用に関連した一般的な既知の問題やエラーのトラブルシューティング方法について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: e5c10b4830a1bba5ff4db07b81ee447e5d33b731
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750833"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Azure Database Migration Service の一般的な問題やエラーのトラブルシューティング

この記事では、Azure Database Migration Service のユーザーが直面する可能性のある一般的な問題やエラーをいくつか説明します。 この記事には、これらの問題やエラーの解決方法に関する情報も含まれています。

> [!NOTE]
> バイアスフリーなコミュニケーション
>
> Microsoft は、多様性を尊重する環境をサポートします。 この記事には、_スレーブ_ という単語への言及があります。 Microsoft の[バイアスフリーなコミュニケーションに関するスタイル ガイド](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)では、これを排他的な単語と認めています。 この単語は現在、ソフトウェアに表示される単語であるため、一貫性を保つためにこの記事で使用されています。 単語を削除するためにソフトウェアを更新するのに合わせて、この記事は更新されます。
>

## <a name="migration-activity-in-queued-state"></a>キューに入った状態の移行アクティビティ

Azure Database Migration Service プロジェクトに新しいアクティビティを作成すると、アクティビティがキューに入った状態のままになります。

| 原因         | 解決方法 |
| ------------- | ------------- |
| この問題は、Azure Database Migration Service インスタンスが、同時に実行される進行中のタスクの最大容量に達したときに発生します。 新しいアクティビティはどれも、容量が利用可能になるまでキューに入れられます。 | Data Migration Service インスタンスに、プロジェクトをまたぐ実行中のアクティビティがあることを確認します。 実行のためのキューに自動的に追加される新しいアクティビティの作成を続行することができます。 既存の実行中のアクティビティのいずれかが完了するとすぐに、キュー内にある次のアクティビティの実行が開始され、状態が自動的に実行状態に変わります。 キューに入れられたアクティビティの移行を開始するために、追加の操作を行う必要はありません。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>移行のために選択されたデータベースの最大数

Azure SQL Database または Azure SQL Managed Instance に移動するためのデータベース移行プロジェクトのアクティビティを作成すると、次のエラーが発生します。

* **Error**: 移行の設定の検証エラー、"errorDetail": "'Databases' のオブジェクトの最大数 '4' を超えるオブジェクトが移行用に選択されています。"

| 原因         | 解決方法 |
| ------------- | ------------- |
| このエラーは、1 つの移行アクティビティに対して 4 つを超えるデータベースを選択したときに表示されます。 現在、各移行アクティビティのデータベース数は 4 つまでに制限されます。 | 移行アクティビティごとに、4 つ以下のデータベースを選択してください。 4 つを超えるデータベースを並列で移行する必要がある場合は、Azure Database Migration Service のインスタンスをもう 1 つプロビジョニングします。 現在、各サブスクリプションでサポートされる Azure Database Migration Service インスタンスは最大 2 つです。<br><br> |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure Database Migration Service を停止しようとしたときのエラー

Azure Database Migration Service インスタンスを停止すると、次のエラーが表示されます。

* **Error**: サービスを停止できませんでした。 エラー: {'error':{'code':'InvalidRequest','message':'1 つ以上のアクティビティが現在実行中です。 サービスを停止するには、アクティビティが完了するまで待つか、それらのアクティビティを手動で停止して、もう一度お試しください。'}}

| 原因         | 解決方法 |
| ------------- | ------------- |
| このエラーは、停止しようとしているサービス インスタンスに、まだ実行中のアクティビティや移行プロジェクト内に存在するアクティビティが含まれている場合に表示されます。 <br><br><br><br><br><br> | 停止しようとしている Azure Database Migration Service のインスタンスには実行中のアクティビティがないようにしてください。 また、サービスを停止する前に、アクティビティまたはプロジェクトを削除することもできます。 次の手順では、実行中のすべてのタスクを削除することでプロジェクトを削除し、移行サービス インスタンスをクリーンアップする方法を示します。<br>1.Install-Module -Name AzureRM.DataMigration <br>2.Login-AzureRmAccount <br>3.Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4.Remove-AzureRmDataMigrationProject -Name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Azure Database Migration Service を開始しようとしたときのエラー

Azure Database Migration Service インスタンスを開始すると、次のエラーが表示されます。

* **Error**: Service fails to Start. Error: {'errorDetail':'The service failed to start, please contact Microsoft support'} (サービスを開始できません。エラー: {'errorDetail':'サービスを開始できませんでした。Microsoft サポートにお問い合わせください'})

| 原因         | 解決方法 |
| ------------- | ------------- |
| このエラーは、前のインスタンスが内部で失敗したときに表示されます。 このエラーはまれに発生することがあり、エンジニア チームはそのことを認識しています。 <br> | 開始できないサービスのインスタンスを削除してから、それを置き換える新しいものをプロビジョニングします。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>SQL から Azure SQL DB マネージド インスタンスに移行中のデータベース復元エラー

SQL Server から Azure SQL Managed Instance へのオンライン移行を実行すると、一括移行に失敗し、次のエラーが表示されます。

* **Error**: 操作 ID 'operationId' の復元操作に失敗しました。 コード 'AuthorizationFailed'、メッセージ 'オブジェクト ID 'objectId' のクライアント 'clientId' は、スコープ '/subscriptions/subscriptionId' に対するアクション 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' の実行を承認されていません。'。

| 原因         | 解決方法    |
| ------------- | ------------- |
| このエラーは、SQL Server から SQL Managed Instance へのオンライン移行に使用されているアプリケーション プリンシパルに、サブスクリプションに対する共同作成アクセス許可がないことを示しています。 現在、マネージド インスタンスを使用した特定の API 呼び出しでは、復元操作のために、サブスクリプションに対するこのアクセス許可が必要です。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | エラー メッセージから取得できる `-ObjectId` を指定して `Get-AzureADServicePrincipal` PowerShell コマンドレットを使用すると、使用されているアプリケーション ID の表示名が表示されます。<br><br> このアプリケーションに対するアクセス許可を確認し、それがサブスクリプション レベルで[共同作成者ロール](../role-based-access-control/built-in-roles.md#contributor)を持っていることを確認してください。 <br><br> Azure Database Migration Service のエンジニアリング チームは、サブスクリプションの現在の共同作成者ロールから必要とされるアクセスを制限するように取り組んでいます。 共同作成者ロールの使用を許可しないビジネス要件がある場合は、Azure サポートに追加の支援を要請してください。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Azure Database Migration Service に関連付けられた NIC を削除するときのエラー

Azure Database Migration Service に関連付けられたネットワーク インターフェイス カードを削除しようとすると、削除の試行が失敗し、次のエラーが表示されます。

* **Error**: Cannot delete the NIC associated to Azure Database Migration Service due to the DMS service utilizing the NIC (DMS サービスで NIC が使用されているため、Azure Database Migration Service に関連付けられた NIC を削除できません)

| 原因         | 解決方法    |
| ------------- | ------------- |
| この問題は、Azure Database Migration Service インスタンスがまだ存在し、NIC を使用している可能性がある場合に発生します。 <br><br><br><br><br><br><br><br> | この NIC を削除するには、DMS サービス インスタンスを削除すると、サービスで使用されている NIC が自動的に削除されます。<br><br> **重要**:削除対象の Azure Database Migration Service インスタンスに実行中のアクティビティがないことを確認してください。<br><br> Azure Database Migration Service インスタンスに関連付けられているすべてのプロジェクトとアクティビティが削除された後に、サービス インスタンスを削除できます。 サービス インスタンスによって使用されている NIC は、サービスの削除の一環として自動的に削除されます。 |

## <a name="connection-error-when-using-expressroute"></a>ExpressRoute の使用時の接続エラー

Azure Database Migration Service プロジェクト ウィザードでソースに接続しようとすると、ソースが接続に ExpressRoute を使用している場合、長時間経過してタイムアウトした後に接続に失敗します。

| 原因         | 解決方法    |
| ------------- | ------------- |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) を使用している場合、Azure Database Migration Service では、そのサービスに関連付けられた仮想ネットワーク サブネット上に次の 3 つのサービス エンドポイントをプロビジョニングする[必要があります](./tutorial-sql-server-to-azure-sql.md)。<br> -- サービス バス エンドポイント<br> -- ストレージ エンドポイント<br> -- ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)<br><br><br><br><br> | ソースと Azure Database Migration Service の間の ExpressRoute 接続に必要なサービス エンドポイントを[有効](./tutorial-sql-server-to-azure-sql.md)にします。 <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>MySQL データベースを Azure DB for MySQL に移行するときのロック待機タイムアウト エラー

Azure Database Migration Service を介して MySQL データベースを Azure Database for MySQL インスタンスに移行すると、次のロック待機タイムアウト エラーが発生して、移行が失敗します。

* **Error**: Database migration error - Failed to load file - Failed to start load process for file 'n' (データベースの移行エラー - ファイルを読み込めませんでした - ファイル 'n' の読み込みプロセスを開始できませんでした) RetCode: SQL_ERROR SqlState:HY000 NativeError: 1205 メッセージ: [MySQL][ODBC Driver][mysqld] Lock wait timeout exceeded; try restarting transaction (ロック待機のタイムアウトを超えました。トランザクションを再開してみてください)

| 原因         | 解決方法    |
| ------------- | ------------- |
| このエラーは、移行中のロック待機がタイムアウトしたため、移行が失敗したときに発生します。 | サーバー パラメーター **'innodb_lock_wait_timeout'** の値を大きくすることを検討してください。 最大許容値は 1073741824 です。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>動的ポートまたは名前付きインスタンスを使用しているときのソース SQL Server への接続エラー

Azure Database Migration Service を、名前付きインスタンスまたは動的ポートで実行される SQL Server ソースに接続しようとすると、次のエラーで接続が失敗します。

* **エラー**: -1 - SQL 接続できませんでした。 SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。 インスタンス名が正しいこと、および SQL Server がリモート接続を許可するように構成されていることを確認してください。 (プロバイダー:SQL Network Interfaces、エラー:26 - 指定されたサーバーまたはインスタンスの位置を特定しているときにエラーが発生しました)

| 原因         | 解決方法    |
| ------------- | ------------- |
| この問題は、Azure Database Migration Service が接続しようとしているソース SQL Server インスタンスで、動的ポートまたは名前付きインスタンスが使用されているときに発生します。 SQL Server Browser サービスでは、名前付きインスタンスに対する着信接続に対して、または動的ポートが使用されているときに、UDP ポート 1434 がリッスンされます。 動的ポートは、SQL Server サービスを再起動するたびに変わる可能性があります。 ネットワーク構成を介してインスタンスに割り当てられている動的ポートは、SQL Server 構成マネージャーで確認できます。<br><br><br> |Azure Database Migration Service が UDP ポート 1434 でソース SQL Server Browser サービスに接続できること、また、必要に応じて、動的に割り当てられた TCP ポート経由で SQL Server インスタンスに接続できることを確認します。 |

## <a name="additional-known-issues"></a>その他の既知の問題

* [Azure SQL Database へのオンライン移行に関する既知の問題と移行の制限事項](./index.yml)
* [Azure Database for PostgreSQL へのオンライン移行に関する既知の問題と移行の制限事項](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>次のステップ

* [Azure Database Migration Service PowerShell](/powershell/module/azurerm.datamigration#data_migration) に関する記事を確認する。
* 「[Azure portal を使用して Azure Database for MySQL のサーバー パラメーターを構成する方法](../mysql/howto-server-parameters.md)」を確認する。
* 「[Azure Database Migration Service を使用するための前提条件の概要](./pre-reqs.md)」を確認する。
* 「[Azure Database Migration Service の使用に関する FAQ](./faq.yml)」を確認する。