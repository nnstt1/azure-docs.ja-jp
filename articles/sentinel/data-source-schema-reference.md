---
title: Microsoft Sentinel データ ソース スキーマのリファレンス
description: この記事では、Microsoft Sentinel でサポートされている Azure とサードパーティのデータ ソース スキーマの一覧と、リファレンス ドキュメントへのリンクを示します。
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.topic: reference
ms.custom: ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 0ea3809ec96d956e50d698c85cabbcf99420335d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724427"
---
# <a name="data-source-schema-reference"></a>データ ソース スキーマ リファレンス

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、サポートされている Azure とサードパーティのデータ ソース スキーマの一覧と、リファレンス ドキュメントへのリンクを示します。

## <a name="azure-data-sources"></a>Azure データ ソース

| Type                             | データ ソース             | Log Analytics テーブル名 | スキーマ参照 |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Azure AD アクティビティ レポートのサインイン プロパティ](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor AuditLogs のリファレンス](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor AzureActivity のリファレンス](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 管理アクティビティ API のスキーマ: <br>- [共通スキーマ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange 管理のスキーマ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange メールボックスのスキーマ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint ベースのスキーマ](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint ファイル操作](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor AzureDiagnostics のリファレンス](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | syslog                 | [Azure Monitor Syslog のリファレンス](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | IIS ログ               | W3CIISLog              | [Azure Monitor W3CIISLog のリファレンス](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [Azure Monitor VMConnection のリファレンス](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | Wire Data ソリューション     | WireData               | [Azure Monitor WireData のリファレンス](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | NSG フロー ログ          | AzureNetworkAnalytics  | [Traffic Analytics のスキーマとデータ集計](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> 詳細については、「[Azure Monitor データ参照](/azure/azure-monitor/reference/)」全体をご覧ください。
>
## <a name="3rd-party-vendor-data-sources"></a>サードパーティ ベンダーのデータ ソース

次の表に、サポートされるサードパーティ ベンダーとそれらの Syslog、またはサポートされる各種ログ タイプの Common Event Format (CEF) マッピングのドキュメントを示します。カテゴリの種類ごとに CEF フィールド マッピングとサンプル ログが含まれます。

| Type |    ベンダー |    Product | Log Analytics テーブル名 | CEF フィールド マッピングのリファレンス  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | Palo Alto   | PAN OS    | CommonSecurityLog |   [PAN-OS 9.0 Common Event Format 統合ガイド](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (*CEF- style Log Formats* を検索) |
| **Network** | Check Point  |ALL   | CommonSecurityLog | [ログ フィールドの説明](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | Fortigate   | ALL   | CommonSecurityLog | [ログ スキーマの構造](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | Web アプリケーション ファイアウォール |  CommonSecurityLog   | [Syslog と他のログを構成する方法](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | Cisco | ASA | CommonSecurityLog | [Cisco ASA シリーズの Syslog メッセージ](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | Cisco | Firepower   | CommonSecurityLog | [Cisco Firepower Threat Defense の Syslog メッセージ](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.html)    |
| **Network** | Cisco   | Umbrella  | カスタム ログ テーブル  | [ログの形式とバージョン管理](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | Cisco | Meraki    | CommonSecurityLog |   [Syslog イベントの種類とログ サンプル](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | Nano Streaming Service (NSS)|   CommonSecurityLog | [NSS フィードの書式設定](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (Web、ファイアウォール、DNS、トンネルのログのみ) |
| **Network**   |F5 | BigIP LTM|    CommonSecurityLog|  [イベント メッセージと攻撃の種類](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | BigIP ASM|    CommonSecurityLog|  [アプリケーションのセキュリティ イベントのログ記録](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |Web アプリ ファイアウォール   | CommonSecurityLog|    [アプリケーション ファイアウォールでの Common Event Format (CEF) のログ記録のサポート](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12.0 の Syslog メッセージのリファレンス](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Symantec Endpoint Protection Manager の外部ログ設定とログ イベントの重大度レベル](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |All |CommonSecurityLog | [Syslog コンテンツのマッピング - CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> 詳細については、[CEF および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)に関するページも参照してください。
> 
## <a name="next-steps"></a>次のステップ

CEF、Syslog、ダイレクト、エージェント、カスタム コネクタなど、サポートされている Microsoft Sentinel コネクタについて学習します。

- [データ ソースの接続](connect-data-sources.md)

- [Microsoft Sentinel の Syslog、CEF、およびその他のサードパーティ製のコネクタ](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)
