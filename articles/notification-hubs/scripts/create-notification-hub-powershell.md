---
title: PowerShell を使用して Azure 通知ハブを作成する |Microsoft Docs
description: PowerShell スクリプトを使用して Azure 通知ハブを作成する方法について説明します。
author: femila
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: femila
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74dff49ff26fd6b4a283cda4d3c3cc2524362a93
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458293"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>PowerShell を使用して Azure 通知ハブを作成する

この PowerShell のサンプル スクリプトは、Azure 通知ハブのサンプルを作成します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に [無料のアカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

このサンプル スクリプトを実行した後、次のコマンドを使用して、リソース グループとそれに関連付けられたすべてのリソースを削除できます。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | 通知ハブの名前空間を作成します。 |
| [New-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | 通知ハブを作成します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。
