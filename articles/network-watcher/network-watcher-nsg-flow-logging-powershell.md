---
title: NSG フロー ログの管理 - Azure PowerShell
titleSuffix: Azure Network Watcher
description: このページでは、PowerShell を使用して Azure Network Watcher でネットワーク セキュリティ グループのフローのログを管理する方法を説明します。
author: damendo
ms.service: network-watcher
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 94a604c687326d7fb43b4b9e44aaad682d7e5ee4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585816"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>PowerShell を使用したネットワーク セキュリティ グループのフローのログの構成

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

ネットワーク セキュリティ グループ フロー ログは、ネットワーク セキュリティ グループを介して IP トラフィックの送信と受信に関する情報を表示できるようにする Network Watcher の機能です。 これらのフローのログは json 形式で記述され、ルールごとの送信、受信フロー、フロー (送信元/送信先 IP、送信元/送信先ポート、プロトコル) についての 5 組の情報が適用される NIC、トラフィックが許可されているか、拒否されているかを示しています。

さまざまなバージョンの AzPowerShell のすべての NSG フロー ログ コマンドの詳細な仕様については、[こちら](/powershell/module/az.network/#network-watcher)を参照してください

> [!NOTE]
> - このドキュメントで使用されているコマンド [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus) および [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) には、Network Watcher のリソース グループに追加の "閲覧者" アクセス許可が必要です。 また、これらのコマンドは古いものであり、間もなく非推奨となる可能性があります。
> - 代わりに、新しい [Get-AzNetworkWatcherFlowLog](/powershell/module/az.network/get-aznetworkwatcherflowlog) コマンドと [Set-AzNetworkWatcherFlowLog](/powershell/module/az.network/set-aznetworkwatcherflowlog) コマンドを使用することが推奨されます。
> - 新しい [Get-AzNetworkWatcherFlowLog](/powershell/module/az.network/get-aznetworkwatcherflowlog) コマンドには、柔軟性のために 4 つのバリアントが用意されています。 このコマンドの "Location \<String\>" バリアントを使用する場合は、Network Watcher のリソース グループに追加の "閲覧者" アクセス許可が必要になります。 その他のバリアントの場合、追加のアクセス許可は必要ありません。 

## <a name="register-insights-provider"></a>Insights プロバイダーの登録

フローのログ記録を成功させるには、**Microsoft.Insights** プロバイダーを登録する必要があります。 **Microsoft.Insights** プロバイダーが登録されているかどうか不明な場合は、次のスクリプトを実行します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>ネットワーク セキュリティ グループのフロー ログと Traffic Analytics を有効にする

フローのログを有効にするコマンドを次の例に示します。

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

指定するストレージ アカウントに対しては、Microsoft サービスまたは特定の仮想ネットワークのみにネットワーク アクセスを制限するネットワーク規則が構成されていてはなりません。 ストレージ アカウントは、フロー ログを有効化した NSG と同じ Azure サブスクリプションにも、異なる Azure サブスクリプションにも設定できます。 異なるサブスクリプションを使用する場合は、それらが同じ Azure Active Directory テナントに関連付けられている必要があります。 各サブスクリプションで使用するアカウントは、[必要なアクセス許可](required-rbac-permissions.md)を持っている必要があります。

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Traffic Analytics とネットワーク セキュリティ グループのフロー ログを無効にする

Traffic Analytics とフロー ログを無効にするには、次の例を使用します。

```powershell
#Disable Traffic Analytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>フローのログをダウンロードする

フローのログの保存場所は作成時に定義されます。 ストレージ アカウントに保存されているこれらのフローのログにアクセスする際の便利なツールが Microsoft Azure Storage Explorer です。このツールは、https://storageexplorer.com/ からダウンロードできます。

ストレージ アカウントが指定されている場合、フロー ログ ファイルは、次の場所にあるストレージ アカウントに保存されます。

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

ログの構造については、[ネットワーク セキュリティ グループのフローのログの概要](network-watcher-nsg-flow-logging-overview.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

[PowerBI を使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-power-bi.md)方法を確認する

[オープン ソース ツールを使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)方法を確認する
