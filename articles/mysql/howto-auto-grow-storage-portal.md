---
title: ストレージの自動拡張 - Azure portal - Azure Database for MySQL
description: この記事では、Azure portal を使用して Azure Database for MySQL のストレージの自動拡張を有効にする方法について説明します
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 1bca7aadcdd1fb85bca6c794f4a5840f1a85db95
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652531"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL のストレージの自動拡張

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
この記事では、Azure Database for MySQL サーバーのストレージを、ワークロードに影響を与えることなく拡張されるように構成する方法について説明します。

サーバーが、割り当てられたストレージの上限に達すると、そのサーバーは読み取り専用としてマークされます。 ただし、ストレージの自動拡張を有効にした場合、サーバーのストレージは、増大するデータに合わせて拡張されます。 プロビジョニングされたストレージが 100 GB 未満のサーバーでは、空きストレージがプロビジョニングされたストレージの 1 GB または 10% のどちらか大きい方を下回ると、プロビジョニングされたストレージ サイズが 5 GB 単位ですぐに拡張されます。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、プロビジョニングされたストレージ サイズのうちの空きストレージ領域が 10 GB を下回ると、プロビジョニングされたストレージ サイズは 5% 増加します。 [こちら](./concepts-pricing-tiers.md#storage)で指定されているストレージの上限が適用されます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>ストレージの自動拡張を有効にする 

MySQL サーバー ストレージの自動拡張を設定するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL サーバーを選択します。

2. MySQL サーバー ページの **[設定]** の見出しの下にある **[価格レベル]** をクリックして、価格レベル ページを開きます。

3. [自動拡張] セクションで **[はい]** を選択して、ストレージの自動拡張を有効にします。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL - 価格レベルの設定 - 自動拡張":::

4. **[OK]** をクリックして変更を保存します。

5. 自動拡張が正常に有効化されたことを確認する通知が表示されます。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL - 自動拡張の成功":::

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。
