---
title: 専用 SQL プールでのテーブルのパーティション分割
description: 専用 SQL プールでのテーブル パーティションの使用に関するレコメンデーションと例。
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/02/2021
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: af068f531a07be05da8ff8911ffff68242f7f4cf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504742"
---
# <a name="partitioning-tables-in-dedicated-sql-pool"></a>専用 SQL プールでのテーブルのパーティション分割

専用 SQL プールでのテーブル パーティションの使用に関するレコメンデーションと例。

## <a name="what-are-table-partitions"></a>テーブル パーティションの概要

テーブル パーティションを使用すると、データを小さなデータ グループに分割できます。 ほとんどの場合、テーブル パーティションはデータ列に作成されます。 パーティション分割は、クラスター化列ストア、クラスター化インデックス、ヒープなど、専用 SQL プールのすべてのテーブル型でサポートされます。 パーティション分割は、ハッシュ分散とラウンド ロビン分散の両方を含むあらゆる種類のディストリビューションでもサポートされます。  

パーティション分割をすると、データのメンテナンスとクエリのパフォーマンスでメリットを得ることができます。 両方のメリットを得られるか、片方のみかは、データの読み込み方法と、同じ列を両方の目的で使用できるかどうかによります。その理由は、パーティション分割を実行できるのが 1 つの列のみであるためです。

### <a name="benefits-to-loads"></a>読み込みに対するメリット

専用 SQL プールでパーティション分割する主なメリットは、パーティションの削除、切り替え、および結合の使用による、データの読み込みの効率性とパフォーマンスの向上です。 ほとんどの場合、データは、データが SQL プールに読み込まれる順序に密接に関連付けられている日付列でパーティション分割されます。 データを保持するためにパーティションを使用する最大のメリットの 1 つが、トランザクション ログの回避です。 単にデータを挿入、更新、または削除するのは最も簡単なアプローチですが、少しの配慮と労力を注いで読み込みプロセス中にパーティション分割を使用すると、大幅にパフォーマンスを向上できます。

テーブルのセクションを手早く削除したり置き換えたりするには、パーティションの切り替えを使用できます。  たとえば、売上のファクト テーブルに過去 36 か月のデータのみが含まれるとします。 毎月末に、最も古い月の売上データがテーブルから削除されます。  このデータは、最も古い月のデータを削除する delete ステートメントを使用して削除できます。 

ただし、大量のデータを delete ステートメントで行単位で削除すると、非常に長い時間がかかるだけでなく、問題が生じた場合に、トランザクションが巨大なことにより、ロールバックに時間がかかるリスクが生じる可能性があります。 より最適なアプローチは、データの最も古いパーティションを削除する方法です。 個々の行の削除に時間がかかる可能性がある場合、パーティション全体を削除すると、わずかな時間で終了する可能性があります。

### <a name="benefits-to-queries"></a>クエリに対するメリット

パーティション分割により、クエリのパフォーマンスを向上させることもできます。 パーティション分割されたデータにフィルターを適用するクエリは、該当するパーティションのみにスキャンを制限できます。 このフィルター処理方法では、テーブルのフル スキャンを回避でき、データの小さいサブセットのみをスキャンすることができます。 クラスター化列ストア インデックスの導入では、述語の削除によるパフォーマンスのメリットはあまりありませんが、クエリにメリットをもたらす場合があります。 

たとえば、売上のファクト テーブルが販売日フィールドを使用して 36 か月にパーティション分割されている場合は、販売日をフィルター処理するクエリによって、フィルターと一致しないパーティションの検索を省略できます。

## <a name="partition-sizing"></a>パーティションのサイズ設定

パーティション分割を使用すると一部のシナリオのパフォーマンスが向上する可能性がありますが、パーティションが **多すぎる** テーブルを作成すると、特定の状況でパフォーマンスが低下する可能性があります。  これらの問題は、特にクラスター化列ストア テーブルに当てはまります。 

パーティション分割が役立つように、パーティション分割を使用する時期と作成するパーティション数を把握することが重要です。 パーティションの数が多すぎるかどうかについて厳格なルールはなく、データと、同時に読み込むパーティションの数によります。 パーティション分割構成が成功すると、通常、パーティションの数は数十個から数百個程度であり、数千個にまでなることはありません。

**クラスター化列ストア** テーブルでパーティションを作成するときは、各パーティションに属している行数が重要になります。 クラスター化列ストア テーブルの圧縮とパフォーマンスを最適化するためには、ディストリビューションおよびパーティションあたり少なくとも 100 万行が必要です。 専用 SQL プールでは、パーティションが作成される前に、各テーブルが 60 個の分散データベースに既に分割されています。 

テーブルに追加されるすべてのパーティション分割は、バックグラウンドで作成されたディストリビューションに追加されたものです。 この例では、売上のファクト テーブルに 36 か月のパーティションが含まれる場合、専用 SQL プールに 60 のディストリビューションがあるとすると、売上のファクト テーブルには 1 か月あたり 6 千万行、すべての月を指定する場合は 21 億行を含める必要があります。 テーブルに含まれる行が、パーティションごとの推奨される最小の行数よりも少ない場合、パーティションあたりの行数を増やすためにパーティション数を少なくすることを検討する必要があります。 

詳細については、[インデックス作成](sql-data-warehouse-tables-index.md)に関する記事を参照してください。この記事には、クラスター列ストア インデックスの質を評価できるクエリについて記載されています。

## <a name="syntax-differences-from-sql-server"></a>SQL Server との構文の相違点

専用 SQL プールには、SQL Server よりも簡単なパーティションの定義方法が導入されています。 パーティション関数とパーティション構成は、SQL Server のものであるため、専用 SQL プールでは使用されません。 代わりに、必要なのは、パーティション分割された列と境界点を特定することだけです。 

パーティション分割の構文は、SQL Server と若干異なる場合がありますが、基本的な概念は同じです。 SQL Server および専用 SQL プールでは、テーブルごとに 1 つのパーティション列がサポートされます。このパーティション列で、範囲指定によるパーティションを指定することができます。 パーティション分割の詳細については、「[パーティション テーブルとパーティション インデックス](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。

次の例では、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ステートメントを使用して、`FactInternetSales` テーブルを `OrderDateKey` 列でパーティション分割します。

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrate-partitions-from-sql-server"></a>SQL Server からパーティションを移行する

SQL Server のパーティション定義を専用 SQL プールに移行するには、次の操作を行います。

- SQL Server の[パーティション構成](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)を除去します。
- [パーティション関数](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)の定義を CREATE TABLE に追加します。

パーティション分割されたテーブルを SQL Server インスタンスから移行する場合、各パーティションに含まれる行数を調べるうえで以下の SQL が役立つ場合があります。 専用 SQL プールで同じパーティション分割の粒度を使用する場合、パーティションごとの行数が 60 の倍数で減少することに注意してください。  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>パーティションの切り替え

専用 SQL プールでは、パーティションの分割、結合、および切り替えがサポートされています。 これらの各機能は、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ステートメントを使用して実行されます。

2 つのテーブル間でパーティションを切り替えるには、それぞれの境界に合わせてパーティションが配置されていることと、テーブル定義が一致していることを確認する必要があります。 テーブルで値の範囲を適用する際に CHECK 制約は使用できないため、ソース テーブルにターゲットテーブルと同じパーティション境界が含まれている必要があります。 パーティション境界が同じでない場合、パーティションのメタデータが同期されないため、パーティションの切り替えは失敗します。

パーティション分割では、テーブルにクラスター化列ストア インデックス (CCI) がある場合、それぞれのパーティション (必ずしもテーブル全体ではなく) が空である必要があります。 同じテーブル内の他のパーティションには、データを含めることができます。 データが含まれるパーティションは分割できず、次のエラーが発生します: `ALTER PARTITION statement failed because the partition is not empty. Only empty partitions can be split in when a columnstore index exists on the table. Consider disabling the columnstore index before issuing the ALTER PARTITION statement, then rebuilding the columnstore index after ALTER PARTITION is complete.`データが含まれるパーティション分割の回避策については、「[データが含まれたパーティションを分割する方法](#how-to-split-a-partition-that-contains-data)」を参照してください。 

### <a name="how-to-split-a-partition-that-contains-data"></a>データが含まれたパーティションを分割する方法

既にデータが含まれているパーティションを分割する最も効率的な方法は、 `CTAS` ステートメントを使用することです。 パーティション テーブルがクラスター化列ストアの場合、テーブルのパーティションを分割するには、パーティションを空にしておく必要があります。

次の例は、パーティション分割された列ストア テーブルを作成します。 各パーティションに 1 つの行を挿入します。

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

次のクエリは、`sys.partitions` カタログ ビューを使用して、行数を検索します。

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

次の分割コマンドは、エラー メッセージを受信します。

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

```
Msg 35346, Level 15, State 1, Line 44
SPLIT clause of ALTER PARTITION statement failed because the partition is not empty. Only empty partitions can be split in when a columnstore index exists on the table. Consider disabling the columnstore index before issuing the ALTER PARTITION statement, then rebuilding the columnstore index after ALTER PARTITION is complete.
```

ただし、`CTAS` を使用して、データを保持する新しいテーブルを作成できます。

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

パーティション境界が配置されているので、切り替えが許可されます。 これにより、後で分割できる空のパーティションを含むソース テーブルをそのままにしておくことができます。

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

後は、`CTAS` を使用して新しいパーティション境界に合わせてデータを配置し、データをメイン テーブルに切り替えるだけです。

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

データの移動が完了したら、ターゲット テーブルの統計を更新することをお勧めします。 統計を更新することで、各パーティション内のデータの新しい分散が統計に正確に反映されます。

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>データを含むパーティションにワンステップで新しいデータを読み込む

パーティションの切り替えを使用したパーティションへのデータの読み込みは、ユーザーには表示されないテーブルに新しいデータをステージングできる便利な方法です。  ビジー システムでは、パーティションの切り替えに関連したロックの競合への対応が困難な場合があります。  

パーティション内の既存のデータを取り除くには、データをスイッチアウトするために `ALTER TABLE` が必要でした。  それから、新しいデータにスイッチインするために別の `ALTER TABLE` が必要でした。  

専用 SQL プールでは、`ALTER TABLE` コマンドで `TRUNCATE_TARGET` オプションがサポートされています。  `TRUNCATE_TARGET` により、`ALTER TABLE` コマンドは、パーティション内の既存のデータを新しいデータで上書きします。  以下の例は、`CTAS` を使用して、既存のデータで新しいテーブルを作成して新しいデータを挿入し、次に、既存のデータを上書きして、すべてのデータをターゲット テーブルに切り替えています。

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>テーブル パーティションのソース管理

> [!NOTE]
> パーティション スキーマを無視するようにソース管理ツールが構成されていない場合、パーティションを更新するようにテーブルのスキーマを変更すると、デプロイの一環としてテーブルがドロップされ、再作成されますが、実行されないことがあります。 次に示すように、このような変更を実装するカスタム ソリューションが必要になる場合があります。 継続的インテグレーション/継続的配置 (CI/CD) ツールを使用できるかどうかを確認します。 SQL Server Data Tools (SSDT) で "パーティション スキームを無視する" 公開詳細設定を探し、テーブルのドロップと再作成を引き起こすスクリプトの生成を回避します。

この例は、空のテーブルのパーティション スキーマを更新する場合に役立ちます。 データが含まれるテーブルでパーティション変更を継続的にデプロイするには、パーティション SPLIT RANGE の適用前に各パーティションの外にデータを一時的に移動するデプロイと並行して、「[データが含まれたパーティションを分割する方法](#how-to-split-a-partition-that-contains-data)」の手順に従います。 これは、CI/CD ツールではデータが含まれるパーティションが認識されないため、必要になります。

ソース管理システムでテーブル定義が **古く** ならないように、次の方法を検討することをお勧めします。

1. パーティション値を含まないパーティション テーブルとしてテーブルを作成します。

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` (分割) します。

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

この方法では、ソース管理のコードを静的なコードとして維持し、パーティション境界値は動的にすることが可能になるので、時間の経過に伴って、SQL プールとともに進化させることができます。

## <a name="next-steps"></a>次のステップ

テーブルの開発の詳細については、[テーブルの概要](sql-data-warehouse-tables-overview.md)に関する記事を参照してください。
