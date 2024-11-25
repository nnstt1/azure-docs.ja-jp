---
title: Azure App Service のネットワーク構成の同期
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance を使用して、Azure App Service のホスティング プラン用にネットワーク構成を同期する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 12/13/2018
ms.openlocfilehash: d1421b84c9156862f25c6dcf35b70b42dd8f2862
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239367"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance を使用してネットワーク構成を Azure App Service のホスティング プラン用に同期する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

場合によっては、[アプリを Azure Virtual Network と統合したにもかかわらず](../../app-service/overview-vnet-integration.md)、SQL Managed Instance への接続を確立できないことがあります。 サービス プランのネットワーク構成を更新または同期することで、この問題を解決できる場合があります。 

## <a name="sync-network-configuration"></a>ネットワーク構成の同期 

そのためには、次の手順に従います。  

1. Web アプリの App Service プランに移動します。

   ![App Service プランのスクリーンショット](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. **[ネットワーク]** を選択し、 **[管理するにはここをクリック]** を選択します。

   ![サービス プランの管理のスクリーンショット](./media/azure-app-sync-network-configuration/manage-plan.png)

3. 目的の **VNet** を選択し、 **[ネットワークの同期]** をクリックします。

   ![ネットワークの同期のスクリーンショット](./media/azure-app-sync-network-configuration/sync.png)

4. 同期が完了するまで待機します。
  
   ![同期の完了のスクリーンショット](./media/azure-app-sync-network-configuration/sync-done.png)

これで、SQL Managed Instance への接続を再確立する準備が整いました。

## <a name="next-steps"></a>次のステップ

- VNet を SQL Managed Instance 用に構成する方法については、[SQL Managed Instance VNet アーキテクチャ](connectivity-architecture-overview.md)および[既存の VNet を構成する方法](vnet-existing-add-subnet.md)に関する記事を参照してください。