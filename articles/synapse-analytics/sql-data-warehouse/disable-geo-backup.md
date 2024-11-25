---
title: geo バックアップを無効にする
description: Azure Synapse Analytics 内の専用 SQL プール (以前の SQL DW) の geo バックアップを無効にするための攻略ガイド
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2cebee3ad9b515c6f40529fe5d25da687fd53687
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786287"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics 内の[専用 SQL プール (以前の SQL DW)](sql-data-warehouse-overview-what-is.md) の geo バックアップを無効にする

この記事では、Azure portal で専用 SQL プール (以前の SQL DW) の geo バックアップを無効にする方法について説明します。

## <a name="disable-geo-backups-through-azure-portal"></a>Azure portal を使用して geo バックアップを無効にする

専用 SQL プール (以前の SQL DW) の geo バックアップを無効にするには、次の手順に従います。

> [!NOTE]
> geo バックアップを無効にすると、専用 SQL プール (以前の SQL DW) を別の Azure リージョンに復旧できなくなります。 
> 

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。
1. geo バックアップを無効にする専用 SQL プール (以前の SQL DW) リソースを選択します。 
1. 左側のナビゲーション パネルの **[設定]** で、 **[geo バックアップ ポリシー]** を選択します。

   ![geo バックアップを無効にする](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. geo バックアップを無効にするために、 **[無効]** を選択します。 

   ![無効にされた geo バックアップ](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. *[保存]* を選択して、設定が確実に保存されるようにします。 

   ![geo バックアップの設定を保存する](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>次のステップ

- [既存の専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-active-paused-dw.md)
- [削除された専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-deleted-dw.md)
