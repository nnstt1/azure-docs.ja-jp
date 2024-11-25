---
title: サーバーの再起動 - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを再起動する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 35c9650d7e476fd0e7c498d2c3db7102a8801ff1
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653122"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを再起動する

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

このトピックでは、Azure Database for MySQL フレキシブル サーバーを再起動する方法について説明します。 メンテナンス上の理由でサーバーの再起動が必要な場合があります。これを行うと、サーバーが操作を実行しているときに短時間の停止が発生します。

サービスがビジー状態の場合、サーバーの再起動はブロックされます。 たとえば、仮想コアのスケーリングなどの前に要求した操作がサービスで処理中である場合があります。

再起動を完了するために必要な時間は、MySQL の回復プロセスに依存しています。 再起動の時間を短縮するために、再起動の実行前にサーバー上で発生しているアクティビティの量を最小限に抑えることをお勧めします。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MySQL フレキシブル サーバー](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>サーバーの再起動を実行する

次の手順で MySQL サーバーを再起動します。

1. Azure portal で、Azure Database for MySQL フレキシブル サーバーを選択します。

2. サーバーの **[概要]** ページのツール バーで、 **[再起動]** をクリックします。

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL - [概要] - [再起動] ボタン":::

3. **[はい]** をクリックして、サーバーを再起動することを確認します。

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL - 再起動の確認":::

4. サーバーの状態が [再起動中] に変わることを確認します。

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL - 再起動の状態":::

5. サーバーの再起動が成功したことを確認します。

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL - 再起動の成功":::

## <a name="next-steps"></a>次のステップ

[クイック スタート: Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する](quickstart-create-server-portal.md)
