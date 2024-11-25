---
title: Azure Monitor リソース ログでサポートされているカテゴリ
description: Azure Monitor リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.topic: reference
ms.date: 10/05/2021
ms.openlocfilehash: 11233e33d7dc5dffbdfc65acec9eac9223a96f2b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231534"
---
# <a name="supported-categories-for-azure-monitor-resource-logs"></a>Azure Monitor リソース ログでサポートされているカテゴリ

> [!NOTE]
> この一覧は、主に自動生成されています。 GitHub でこの一覧に対して行われた変更は、警告なしで上書きされる可能性があります。 永続的な更新を行う方法の詳細については、この記事の作成者にお問い合わせください。

[Azure Monitor リソース ログ](../essentials/platform-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor によって使用可能なすべてのリソース ログは、共通の最上位レベルのスキーマを共有します。 各サービスには、独自のイベントに対して一意のプロパティを生成する柔軟性があります。

リソース ログは、以前は診断ログと呼ばれていました。 この名前は、Azure Monitor によって収集されたログの種類が、Azure リソースだけでなくそれ以外のものも含むように移行したため、2019 年 10 月に変更されました。

リソースの種類 (`resourceId` プロパティで使用可能) とカテゴリの組み合わせにより、スキーマが一意に識別されます。 サービス固有のフィールドを含むすべてのリソース ログに共通のスキーマがあり、それぞれのログ カテゴリに対して追加されています。 詳細については、「[Azure リソース ログの共通およびサービス固有のスキーマ](./resource-logs-schema.md)」を参照してください。

## <a name="costs"></a>コスト

[Azure Monitor Log Analytics](https://azure.microsoft.com/pricing/details/monitor/)、[Azure Storage](https://azure.microsoft.com/product-categories/storage/)、[Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)、Azure Monitor に直接統合しているパートナー ([Datadog など](../../partner-solutions/datadog/overview.md)) は、データの取り込みと保存に関連するコストがかかります。 前の文でリンクされている価格ページを調べて、それらのサービスのコストを確認してください。 リソース ログは、それらの場所に送信できるデータの 1 つの種類に過ぎません。 

また、リソース ログの一部のカテゴリをこれらの場所にエクスポートするコストが発生する場合もあります。 ログと考え得るエクスポート コストを、次のセクションで示します。 エクスポートの価格の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページの「**プラットフォーム ログ**」セクションを参照してください。

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ

各リソースの種類で使用可能なログの種類の一覧を次に示します。 

一部のカテゴリは、特定の種類のリソースに対してのみサポートされています。 リソースが不足していると思われる場合は、リソース固有のドキュメントを参照してください。 たとえば、Microsoft.Sql/servers/databases カテゴリは、すべての種類のデータベースで使用できるわけではありません。 詳細については、[SQL Database の診断ログに関する情報](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)を参照してください。 

不足しているものがあると考えられる場合は、この記事の下部にある GitHub コメントを開くことができます。


## <a name="microsoftaaddomainservices"></a>Microsoft.AAD/DomainServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AccountLogon|AccountLogon|いいえ|
|AccountManagement|AccountManagement|いいえ|
|DetailTracking|DetailTracking|いいえ|
|DirectoryServiceAccess|DirectoryServiceAccess|いいえ|
|LogonLogoff|LogonLogoff|いいえ|
|ObjectAccess|ObjectAccess|いいえ|
|PolicyChange|PolicyChange|いいえ|
|PrivilegeUse|PrivilegeUse|いいえ|
|SystemSecurity|SystemSecurity|いいえ|


## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|サインイン|サインイン|はい|


## <a name="microsoftagfoodplatformfarmbeats"></a>Microsoft.AgFoodPlatform/farmBeats

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ApplicationAuditLogs|アプリケーションの監査ログ|はい|
|FarmManagementLogs|ファーム管理のログ|はい|
|FarmOperationLogs|ファーム操作のログ|はい|
|InsightLogs|分析情報ログ|はい|
|JobProcessedLogs|ジョブ処理ログ|はい|
|ModelInferenceLogs|モデル推論ログ|はい|
|ProviderAuthLogs|プロバイダー認証のログ|はい|
|SatelliteLogs|衛星のログ|はい|
|WeatherLogs|気象のログ|はい|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|
|サービス|サービス|いいえ|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayLogs|ApiManagement Gateway に関連するログ|いいえ|
|WebSocketConnectionLogs|Websocket 接続に関連するログ|はい|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|Audit|はい|
|HttpRequest|HTTP 要求|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ApplicationConsole|アプリケーション コンソール|いいえ|
|BuildLogs|ビルド ログ|はい|
|IngressLogs|イングレスのログ|はい|
|SystemLogs|システム ログ|いいえ|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|AuditEvent メッセージ ログ カテゴリ。|いいえ|
|ERR|エラー メッセージ ログ カテゴリ。|いいえ|
|INF|情報メッセージ ログ カテゴリ。|いいえ|
|WRN|警告メッセージ ログ カテゴリ。|いいえ|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|AuditEvent|はい|
|DscNodeStatus|DscNodeStatus|いいえ|
|JobLogs|JobLogs|いいえ|
|JobStreams|JobStreams|いいえ|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft.AutonomousDevelopmentPlatform/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|Audit|はい|
|運用時|運用時|はい|
|要求|要求|はい|


## <a name="microsoftavsprivateclouds"></a>microsoft.avs/privateClouds

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|vmwaresyslog|VMware VCenter Syslog|はい|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ServiceLog|サービス ログ|いいえ|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces
|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|いいえ|
|BaiClusterNodeEvent|BaiClusterNodeEvent|いいえ|
|BaiJobEvent|BaiJobEvent|いいえ|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|いいえ|
|FabricOrderer|Fabric Orderer|いいえ|
|FabricPeer|Fabric Peer|いいえ|
|プロキシ|プロキシ|いいえ|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|いいえ|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BotRequest|チャネルからボットへの要求|いいえ|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ConnectedClientList|接続済みクライアントの一覧|はい|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|WebApplicationFirewallLogs|Web アプリケーション ファイアウォールのログ|いいえ|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AzureCdnAccessLog|Azure Cdn アクセス ログ|いいえ|
|FrontDoorAccessLog|FrontDoor アクセス ログ|Yes|
|FrontDoorHealthProbeLog|FrontDoor 正常性プローブ ログ|Yes|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall ログ|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|いいえ|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ネットワーク セキュリティ グループの規則フロー イベント|ネットワーク セキュリティ グループの規則フロー イベント|いいえ|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|
|RequestResponse|要求と応答のログ|いいえ|
|Trace|トレース ログ|いいえ|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuthOperational|操作認証ログ|はい|
|CallDiagnostics|通話診断ログ|はい|
|CallSummary|通話概要ログ|はい|
|ChatOperational|運用チャット ログ|いいえ|
|SMSOperational|運用 SMS ログ|いいえ|
|使用法|使用法レコード|いいえ|


## <a name="microsoftconnectedvehicleplatformaccounts"></a>Microsoft.ConnectedVehicle/platformAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|MCVP 監査ログ|はい|
|ログ|MCVP ログ|はい|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ContainerRegistryLoginEvents|ログイン イベント|いいえ|
|ContainerRegistryRepositoryEvents|RepositoryEvent ログ|いいえ|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|cluster-autoscaler|Kubernetes クラスター オートスケーラー|いいえ|
|guard|guard|いいえ|
|kube-apiserver|Kubernetes API サーバー|いいえ|
|kube-audit|Kubernetes の監査|いいえ|
|kube-audit-admin|Kubernetes 監査管理ログ|いいえ|
|kube-controller-manager|Kubernetes コントローラー マネージャー|いいえ|
|kube-scheduler|Kubernetes スケジューラ|いいえ|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditLogs|MiniRP 呼び出しの監査ログ|いいえ|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instances

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|イベントを監査する|いいえ|
|運用時|操作イベント|いいえ|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|accounts|Databricks アカウント|いいえ|
|clusters|Databricks クラスター|いいえ|
|dbfs|Databricks ファイル システム|いいえ|
|featureStore|Databricks Feature Store|はい|
|genie|Databricks Genie|はい|
|globalInitScripts|Databricks グローバル Init スクリプト|はい|
|iamRole|Databricks IAM ロール|はい|
|instancePools|インスタンス プール|いいえ|
|jobs|Databricks ジョブ|いいえ|
|mlflowAcledArtifact|Databricks MLFlow Acled 成果物|はい|
|mlflowExperiment|Databricks MLFlow 実験|はい|
|ノートブック|Databricks Notebook|いいえ|
|RemoteHistoryService|Databricks リモート履歴サービス|はい|
|secrets|Databricks シークレット|いいえ|
|sqlanalytics|Databricks SQL Analytics|はい|
|sqlPermissions|Databricks SQLPermissions|いいえ|
|ssh|Databricks SSH|いいえ|
|ワークスペース|Databricks ワークスペース|いいえ|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|CollaborationAudit|コラボレーションの監査|Yes|
|DataAssets|データ資産|いいえ|
|パイプライン|パイプライン|いいえ|
|Proposals|Proposals|いいえ|
|スクリプト|スクリプト|いいえ|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ActivityRuns|パイプライン アクティビティ実行ログ|いいえ|
|PipelineRuns|パイプライン実行ログ|いいえ|
|SandboxActivityRuns|サンドボックス アクティビティ実行ログ|はい|
|SandboxPipelineRuns|サンドボックス パイプライン実行ログ|はい|
|SSISIntegrationRuntimeLogs|SSIS 統合ランタイム ログ|いいえ|
|SSISPackageEventMessageContext|SSIS パッケージ イベント メッセージ コンテキスト|いいえ|
|SSISPackageEventMessages|SSIS パッケージ イベント メッセージ|いいえ|
|SSISPackageExecutableStatistics|SSIS パッケージ実行可能ファイル統計|いいえ|
|SSISPackageExecutionComponentPhases|SSIS パッケージ実行コンポーネント フェーズ|いいえ|
|SSISPackageExecutionDataStatistics|SSIS パッケージ実行データ統計|いいえ|
|TriggerRuns|トリガー実行ログ|いいえ|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|
|Requests|要求ログ|いいえ|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|
|Requests|要求ログ|いいえ|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ReceivedShareSnapshots|受信された共有スナップショット|いいえ|
|SentShareSnapshots|送信された共有スナップショット|いいえ|
|共有|共有|いいえ|
|ShareSubscriptions|共有サブスクリプション|いいえ|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|MySqlAuditLogs|MariaDB 監査ログ|いいえ|
|MySqlSlowLogs|MariaDB サーバー ログ|いいえ|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|MySqlAuditLogs|MySQL 監査ログ|いいえ|
|MySqlSlowLogs|MySQL Slow ログ|いいえ|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|MySqlAuditLogs|MySQL 監査ログ|いいえ|
|MySqlSlowLogs|MySQL サーバーのログ|いいえ|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|いいえ|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|はい|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|いいえ|
|QueryStoreRuntimeStatistics|PostgreSQL クエリ ストアのランタイム統計|いいえ|
|QueryStoreWaitStatistics|PostgreSQL クエリ ストアの待機統計|いいえ|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|いいえ|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Checkpoint|Checkpoint|いいえ|
|エラー|エラー|いいえ|
|管理|管理|いいえ|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|いいえ|
|Checkpoint|Checkpoint|いいえ|
|Connection|Connection|いいえ|
|エラー|エラー|いいえ|
|HostRegistration|HostRegistration|いいえ|
|管理|管理|いいえ|


## <a name="microsoftdesktopvirtualizationscalingplans"></a>Microsoft.DesktopVirtualization/scalingplans

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|自動スケール|自動スケーリングのログ|はい|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Checkpoint|Checkpoint|いいえ|
|エラー|エラー|いいえ|
|フィード|フィード|いいえ|
|管理|管理|いいえ|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|C2DCommands|C2D コマンド|いいえ|
|C2DTwinOperations|C2D ツイン操作|いいえ|
|構成|構成|いいえ|
|接続|接続|いいえ|
|D2CTwinOperations|D2CTwinOperations|いいえ|
|DeviceIdentityOperations|デバイス ID の操作|いいえ|
|DeviceStreams|デバイス ストリーム (プレビュー)|いいえ|
|DeviceTelemetry|デバイス テレメトリ|いいえ|
|DirectMethods|ダイレクト メソッド|いいえ|
|DistributedTracing|分散トレース (プレビュー)|いいえ|
|FileUploadOperations|ファイルのアップロード操作|いいえ|
|JobsOperations|ジョブ操作|いいえ|
|ルート|ルート|いいえ|
|TwinQueries|ツイン クエリ|いいえ|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|C2DCommands|C2D コマンド|いいえ|
|C2DTwinOperations|C2D ツイン操作|いいえ|
|構成|構成|いいえ|
|接続|接続|いいえ|
|D2CTwinOperations|D2CTwinOperations|いいえ|
|DeviceIdentityOperations|デバイス ID の操作|いいえ|
|DeviceStreams|デバイス ストリーム (プレビュー)|いいえ|
|DeviceTelemetry|デバイス テレメトリ|いいえ|
|DirectMethods|ダイレクト メソッド|いいえ|
|DistributedTracing|分散トレース (プレビュー)|いいえ|
|FileUploadOperations|ファイルのアップロード操作|いいえ|
|JobsOperations|ジョブ操作|いいえ|
|ルート|ルート|いいえ|
|TwinQueries|ツイン クエリ|いいえ|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeviceOperations|デバイス操作|いいえ|
|ServiceOperations|サービス操作|いいえ|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|いいえ|
|EventRoutesOperation|EventRoutesOperation|いいえ|
|ModelsOperation|ModelsOperation|いいえ|
|QueryOperation|QueryOperation|いいえ|
|ResourceProviderOperation|ResourceProviderOperation|はい|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/DatabaseAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|CassandraRequests|CassandraRequests|いいえ|
|ControlPlaneRequests|ControlPlaneRequests|いいえ|
|DataPlaneRequests|DataPlaneRequests|いいえ|
|GremlinRequests|GremlinRequests|いいえ|
|MongoRequests|MongoRequests|いいえ|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|いいえ|
|PartitionKeyStatistics|PartitionKeyStatistics|いいえ|
|QueryRuntimeStatistics|QueryRuntimeStatistics|いいえ|
|TableApiRequests|TableApiRequests|はい|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|
|PublishFailures|発行エラーのログ|いいえ|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|
|PublishFailures|発行エラーのログ|いいえ|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|
|PublishFailures|発行エラーのログ|いいえ|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ArchiveLogs|アーカイブ ログ|いいえ|
|AutoScaleLogs|自動スケール ログ|いいえ|
|CustomerManagedKeyUserLogs|顧客管理キーのログ|いいえ|
|EventHubVNetConnectionEvent|VNet/IP フィルタリング接続ログ|いいえ|
|KafkaCoordinatorLogs|Kafka コーディネーター ログ|いいえ|
|KafkaUserErrorLogs|Kafka ユーザー エラー ログ|いいえ|
|OperationalLogs|操作ログ|いいえ|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ExPCompute|ExPCompute|はい|
|要求|要求|いいえ|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditLogs|監査ログ|いいえ|
|DiagnosticLogs|診断ログ|はい|


## <a name="microsofthealthcareapisworkspacesdicomservices"></a>Microsoft.HealthcareApis/workspaces/dicomservices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditLogs|監査ログ|はい|


## <a name="microsofthealthcareapisworkspacesfhirservices"></a>Microsoft.HealthcareApis/workspaces/fhirservices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditLogs|FHIR 監査ログ|はい|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AutoscaleEvaluations|自動スケーリング検証|いいえ|
|AutoscaleScaleActions|自動スケーリングのスケーリング操作|いいえ|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppAvailabilityResults|可用性の結果|いいえ|
|AppBrowserTimings|ブラウザーのタイミング|いいえ|
|AppDependencies|依存関係|いいえ|
|AppEvents|events|いいえ|
|AppExceptions|例外|いいえ|
|AppMetrics|メトリック|いいえ|
|AppPageViews|ページ ビュー|いいえ|
|AppPerformanceCounters|パフォーマンス カウンター|いいえ|
|AppRequests|Requests|いいえ|
|AppSystemEvents|システム イベント|いいえ|
|AppTraces|トレース|いいえ|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|監査イベント|いいえ|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|[監査ログ]|いいえ|
|AzurePolicyEvaluationDetails|Azure Policy の評価の詳細|はい|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|コマンド|コマンド|いいえ|
|FailedIngestion|失敗した取り込み操作|いいえ|
|IngestionBatching|インジェスト バッチ処理|いいえ|
|定期刊行物|定期刊行物|はい|
|クエリ|クエリ|いいえ|
|SucceededIngestion|成功した取り込み操作|いいえ|
|TableDetails|テーブル詳細|いいえ|
|TableUsageStatistics|テーブルの使用状況に関する統計情報|いいえ|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/IntegrationAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|いいえ|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/Workflows

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|WorkflowRuntime|ワークフロー ランタイムの診断イベント|いいえ|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|いいえ|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|いいえ|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|いいえ|
|AmlComputeJobEvent|AmlComputeJobEvent|いいえ|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|いいえ|
|ComputeInstanceEvent|ComputeInstanceEvent|はい|
|DataLabelChangeEvent|DataLabelChangeEvent|はい|
|DataLabelReadEvent|DataLabelReadEvent|はい|
|DataSetChangeEvent|DataSetChangeEvent|はい|
|DataSetReadEvent|DataSetReadEvent|はい|
|DataStoreChangeEvent|DataStoreChangeEvent|はい|
|DataStoreReadEvent|DataStoreReadEvent|はい|
|DeploymentEventACI|DeploymentEventACI|はい|
|DeploymentEventAKS|DeploymentEventAKS|はい|
|DeploymentReadEvent|DeploymentReadEvent|はい|
|EnvironmentChangeEvent|EnvironmentChangeEvent|はい|
|EnvironmentReadEvent|EnvironmentReadEvent|はい|
|InferencingOperationACI|InferencingOperationACI|はい|
|InferencingOperationAKS|InferencingOperationAKS|はい|
|ModelsActionEvent|ModelsActionEvent|はい|
|ModelsChangeEvent|ModelsChangeEvent|はい|
|ModelsReadEvent|ModelsReadEvent|はい|
|PipelineChangeEvent|PipelineChangeEvent|はい|
|PipelineReadEvent|PipelineReadEvent|はい|
|RunEvent|RunEvent|はい|
|RunReadEvent|RunReadEvent|はい|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|KeyDeliveryRequests|キー配信要求|いいえ|
|MediaAccount|メディア アカウントの正常性状態|はい|


## <a name="microsoftmediavideoanalyzers"></a>Microsoft.Media/videoanalyzers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|はい|
|診断|診断ログ|はい|
|運用時|操作ログ|はい|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationgateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|いいえ|
|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|いいえ|
|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|いいえ|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall アプリケーション ルール|いいえ|
|AzureFirewallDnsProxy|Azure Firewall DNS プロキシ|いいえ|
|AzureFirewallNetworkRule|Azure Firewall ネットワーク ルール|いいえ|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BastionAuditLogs|Bastion 監査ログ|いいえ|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PeeringRouteLog|ピアリングのルート テーブルのログ|いいえ|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|FrontdoorAccessLog|Frontdoor アクセス ログ|いいえ|
|FrontdoorWebApplicationFirewallLog|Frontdoor Web アプリケーション ファイアウォール ログ|いいえ|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|いいえ|
|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|いいえ|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|いいえ|
|NetworkSecurityGroupFlowEvent|ネットワーク セキュリティ グループの規則フロー イベント|いいえ|
|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|いいえ|


## <a name="microsoftnetworkp2svpngateways"></a>microsoft.network/p2svpngateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|いいえ|
|IKEDiagnosticLog|IKE 診断ログ|いいえ|
|P2SDiagnosticLog|P2S 診断ログ|いいえ|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DDoSMitigationFlowLogs|DDoS 軽減策に関する意思決定のフロー ログ|いいえ|
|DDoSMitigationReports|DDoS 軽減策のレポート|いいえ|
|DDoSProtectionNotifications|DDoS 保護通知|いいえ|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager プローブの正常性結果イベント|いいえ|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>microsoft.network/virtualnetworkgateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|いいえ|
|IKEDiagnosticLog|IKE 診断ログ|いいえ|
|P2SDiagnosticLog|P2S 診断ログ|いいえ|
|RouteDiagnosticLog|ルート診断ログ|いいえ|
|TunnelDiagnosticLog|トンネル診断ログ|いいえ|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|VMProtectionAlerts|VM 保護アラート|いいえ|


## <a name="microsoftnetworkvpngateways"></a>microsoft.network/vpngateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|いいえ|
|IKEDiagnosticLog|IKE 診断ログ|いいえ|
|RouteDiagnosticLog|ルート診断ログ|いいえ|
|TunnelDiagnosticLog|トンネル診断ログ|いいえ|


## <a name="microsoftnetworkfunctionazuretrafficcollectors"></a>Microsoft.NetworkFunction/azureTrafficCollectors

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ExpressRouteCircuitIpfix|Express Route 回線の IPFIX フロー レコード|はい|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|OperationalLogs|操作ログ|いいえ|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|Audit|はい|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/tenants

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/tenants/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DataSensitivityLogEvent|DataSensitivity|はい|
|ScanStatusLogEvent|ScanStatus|いいえ|
|セキュリティ|PurviewAccountAuditEvents|はい|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AddonAzureBackupAlerts|アドオン Azure Backup アラート データ|いいえ|
|AddonAzureBackupJobs|アドオン Azure Backup ジョブ データ|いいえ|
|AddonAzureBackupPolicy|アドオン Azure Backup ポリシー データ|いいえ|
|AddonAzureBackupProtectedInstance|アドオン Azure Backup 保護されたインスタンス データ|いいえ|
|AddonAzureBackupStorage|アドオン Azure Backup ストレージ データ|いいえ|
|AzureBackupReport|Azure Backup レポート データ|いいえ|
|AzureSiteRecoveryEvents|Azure Site Recovery イベント|いいえ|
|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|いいえ|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery で保護されたディスクのデータ変更頻度|いいえ|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 回復ポイント|いいえ|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|いいえ|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery のレプリケーション データ アップロード速度|いいえ|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery レプリケーション状態|いいえ|
|CoreAzureBackup|コア Azure Backup データ|いいえ|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|HybridConnectionsEvent|HybridConnections イベント|いいえ|
|HybridConnectionsLogs|HybridConnectionsLogs|いいえ|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|OperationLogs|[操作ログ]|いいえ|


## <a name="microsoftsecurityinsightssettings"></a>microsoft.securityinsights/settings

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Analytics|Analytics|はい|
|DataConnectors|データ コレクション – コネクタ|はい|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|OperationalLogs|操作ログ|いいえ|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AllLogs|Azure SignalR Service ログ。|いいえ|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ConnectivityLogs|Azure Web PubSub サービスの接続性のログ。|はい|
|HttpRequestLogs|Azure Web PubSub サービスの Http 要求のログ。|はい|
|MessagingLogs|Azure Web PubSub サービスのメッセージング ログ。|はい|


## <a name="microsoftsingularityaccounts"></a>microsoft.singularity/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|実行|実行ログ|はい|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DevOpsOperationsAudit|Devops 操作の監査ログ|いいえ|
|ResourceUsageStats|リソース使用統計|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エラー|エラー|いいえ|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|いいえ|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|いいえ|
|SQLInsights|SQL Insights|いいえ|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AutomaticTuning|自動チューニング|いいえ|
|Blocks|Blocks|いいえ|
|DatabaseWaitStatistics|データベースの待機統計|いいえ|
|デッドロック|デッドロック|いいえ|
|DevOpsOperationsAudit|Devops 操作の監査ログ|いいえ|
|DmsWorkers|DMS worker|いいえ|
|エラー|エラー|いいえ|
|ExecRequests|実行要求|いいえ|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|いいえ|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|いいえ|
|RequestSteps|要求ステップ|いいえ|
|SQLInsights|SQL Insights|いいえ|
|SqlRequests|SQL 要求|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|
|Timeouts|Timeouts|いいえ|
|待機|待機|いいえ|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Authoring|Authoring|いいえ|
|実行|実行|いいえ|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse ワークスペース

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BuiltinSqlReqsEnded|組み込みの SQL プール要求終了|いいえ|
|GatewayApiRequests|Synapse Gateway API 要求|いいえ|
|IntegrationActivityRuns|統合アクティビティの実行|はい|
|IntegrationPipelineRuns|統合パイプラインの実行|はい|
|IntegrationTriggerRuns|統合トリガーの実行|はい|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|
|SynapseRbacOperations|Synapse RBAC 操作|いいえ|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BigDataPoolAppsEnded|ビッグ データ プール アプリケーション終了|いいえ|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DmsWorkers|DMS worker|いいえ|
|ExecRequests|実行要求|いいえ|
|RequestSteps|要求ステップ|いいえ|
|SqlRequests|SQL 要求|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|
|待機|待機|いいえ|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|イングレス|イングレス|いいえ|
|管理|管理|いいえ|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|イングレス|イングレス|いいえ|
|管理|管理|いいえ|


## <a name="microsoftwebhostingenvironments"></a>Microsoft.Web/hostingEnvironments

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Azure App Service Environment のプラットフォーム ログ|いいえ|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|ウイルス対策監査ログのレポート|いいえ|
|AppServiceAppLogs|App Service アプリケーション ログ|いいえ|
|AppServiceAuditLogs|監査ログへのアクセス|いいえ|
|AppServiceConsoleLogs|App Service コンソール ログ|いいえ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|いいえ|
|AppServiceHTTPLogs|HTTP ログ|いいえ|
|AppServiceIPSecAuditLogs|IPSecurity 監査ログ|いいえ|
|AppServicePlatformLogs|App Service プラットフォーム ログ|いいえ|
|FunctionAppLogs|関数アプリケーション ログ|いいえ|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|ウイルス対策監査ログのレポート|いいえ|
|AppServiceAppLogs|App Service アプリケーション ログ|いいえ|
|AppServiceAuditLogs|監査ログへのアクセス|いいえ|
|AppServiceConsoleLogs|App Service コンソール ログ|いいえ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|いいえ|
|AppServiceHTTPLogs|HTTP ログ|いいえ|
|AppServiceIPSecAuditLogs|IPSecurity 監査ログ|いいえ|
|AppServicePlatformLogs|App Service プラットフォーム ログ|いいえ|
|FunctionAppLogs|関数アプリケーション ログ|いいえ|


## <a name="next-steps"></a>次の手順

* [リソース ログの詳細を確認する](../essentials/platform-logs-overview.md)
* [リソースのリソース ログを **Event Hubs** にストリーミングする](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](./resource-logs.md#send-to-log-analytics-workspace)