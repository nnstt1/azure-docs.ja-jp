---
title: 'PowerShell スクリプト: Azure Data Share の使用状況を監視する'
description: この PowerShell スクリプトでは、送信されたデータ共有の使用状況メトリックを取得します。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c37875f912bf509b3a1edc878e73b1b5b1d5f407
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703903"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell を使用して、送信されたデータ共有の使用状況を監視する

この PowerShell スクリプトでは、送信されたデータ共有の同期を一覧表示し、特定の同期の詳細を取得することで、データの使用状況を監視します。

## <a name="sample-script"></a>サンプル スクリプト


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | Notes |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.datashare/get-azdatasharesynchronization) | 共有の同期を一覧表示します。 |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail) | 共有同期の同期の詳細を取得します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
