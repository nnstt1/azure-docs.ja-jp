---
title: Visual Studio と SSDT を使用して Synapse SQL に接続してクエリを実行する
description: Visual Studio を使用して Azure Synapse Analytics を使用した専用 SQL プールに対するクエリを実行します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7d534794737f93cd71ee9820f72e6c5ead08ca2b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860999"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Visual Studio と SSDT を使用して Synapse SQL に接続する

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Visual Studio を使用して Azure Synapse Analytics を使用した専用 SQL プールに対するクエリを実行します。 この方法では、Visual Studio 2019 の SQL Server Data Tools (SSDT) 拡張機能が使用されます。 

> [!NOTE]
> サーバーレス SQL プールは、SSDT ではサポートされていません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを使用するには、次のコンポーネントが必要です。

- 既存の Synapse ワークスペース。作成する必要がある場合は、「[Synapse ワークスペースの作成](../get-started-create-workspace.md)」を参照してください
- 専用 SQL プール。 まだ無い場合は、[専用 SQL プールの作成](../get-started-analyze-sql-pool.md#create-a-dedicated-sql-pool)に関するページを参照してください。
- Visual Studio 用の SSDT。 Visual Studio をお持ちの方は既にこのコンポーネントを所有していると思われます。 インストールの手順とオプションの詳細については、 [Visual Studio と SSDT のインストール](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?context=/azure/synapse-analytics/context/context)に関するページを参照してください。
- 完全修飾 SQL サーバー名。 このサーバー名を確認するには、[専用 SQL プールへの接続](connect-overview.md)に関するページを参照してください。

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1.専用 SQL プールに接続する
1. Visual Studio 2019 を開きます。
2. **[表示]**  >  **[SQL Server オブジェクト エクスプローラー]** の順に選択して、SQL Server オブジェクト エクスプローラーを開きます。
   
    ![[SQL Server オブジェクト エクスプローラー]](./media/get-started-visual-studio/open-ssdt.png)
3. **[SQL Server の追加]** アイコンをクリックします。
   
    ![[SQL Server の追加]](./media/get-started-visual-studio/add-server.png)
4. [サーバーへの接続] ウィンドウのフィールドに入力します。
   
    ![[サーバーへの接続]](./media/get-started-visual-studio/connection-dialog.png)
   
   * **[サーバー名]** : 前の手順で特定した **サーバー名** を入力します。
   * **認証**: **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]** : 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[Connect]** をクリックします。
5. 確認のために、Azure SQL Server を展開します。 サーバーに関連付けられているデータベースが表示されます。 AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。
   
    ![Explore AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.サンプル クエリの実行
これで、データベースへの接続が確立されました。次はクエリを記述します。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。
   
    ![[新しいクエリ]](./media/get-started-visual-studio/new-query2.png)
3. 次の T-SQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 緑色の矢印をクリックしてクエリを実行するか、ショートカット キー `CTRL`+`SHIFT`+`E` を使用します。
   
    ![Run query](./media/get-started-visual-studio/run-query.png)
5. クエリ結果を確認します。 この例では、FactInternetSales テーブルに 60,398 行が含まれています。
   
    ![Query results](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>次のステップ
これで接続してクエリを実行することができます。[Power BI でデータを視覚化](get-started-power-bi-professional.md)してみてください。
Azure Active Directory 認証を使用するために環境を構成する方法については、[専用 SQL プールの認証](sql-authentication.md?tabs=provisioned)に関するページを参照してください。
 