---
title: 専用 SQL プールのベスト プラクティス
description: 専用 SQL プールを操作する場合に知っておくべき推奨事項とベスト プラクティス。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/02/2021
ms.author: martinle
ms.reviewer: wiassaf
ms.openlocfilehash: a6ebaa9f6afe9afe007c5ffb2eb0a164d3f2ac75
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500547"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics での専用 SQL プールのベスト プラクティス

この記事には、Azure Synapse Analytics で専用 SQL プールの最適なパフォーマンスを実現するのに役立つベスト プラクティスがまとめられています。 サーバーレス SQL プールを使用している場合は、[サーバーレス SQL プールのベスト プラクティス](best-practices-serverless-sql-pool.md)に関する記事で、具体的なガイダンスをご覧ください。 ここでは、ソリューションを構築するときに重視すべき基本的なガイダンスと重要な領域について説明します。 各セクションでは、概念と、その概念について詳しく説明している詳細な記事を紹介します。

## <a name="dedicated-sql-pools-loading"></a>専用 SQL プールの読み込み

専用 SQL プールの読み込みのガイダンスについては、[データの読み込みに関するガイダンス](data-loading-best-practices.md)をご覧ください。

## <a name="reduce-cost-with-pause-and-scale"></a>一時停止とスケールでコストを削減する

一時停止とスケーリングを通じてコストを削減する方法については、[コンピューティングの管理](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?context=/azure/synapse-analytics/context/context)に関する記事をご覧ください。

## <a name="maintain-statistics"></a>統計を管理する

列の統計を自動的に検出して作成するように、専用 SQL プールを構成できます。  オプティマイザーによって作成されたクエリ プランの有効性は、使用可能な統計によって決まります。  

ご使用のデータベースに対して AUTO_CREATE_STATISTICS を有効にし、クエリで使用されている列の統計が確実に最新の状態になるように、統計を毎日または読み込みのたびに更新することをお勧めします。

統計の管理時間を短縮するには、統計情報が含まれる列、つまり更新を頻繁に行う必要がある列を限定します。 たとえば、毎日新しい値が追加される可能性のある日付列を更新することをお勧めします。 結合にかかわっている列、WHERE 句で使用されている列、GROUP BY で見つかった列などに関する統計の作成に重点を置いてください。

統計の追加情報については、[テーブルの統計の管理](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関する記事をご覧ください。

## <a name="tune-query-performance"></a>クエリ パフォーマンスの調整

- [具体化されたビューを使用したパフォーマンスのチューニング](../sql-data-warehouse/performance-tuning-materialized-views.md)
- [順序指定クラスター化列ストア インデックスを使用したパフォーマンスのチューニング](../sql-data-warehouse/performance-tuning-ordered-cci.md)
- [結果セットのキャッシュを使用したパフォーマンスのチューニング](../sql-data-warehouse/performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>INSERT ステートメントをバッチにグループ化する

`INSERT INTO MyLookup VALUES (1, 'Type 1')` などの INSERT ステートメントで小さなテーブルに 1 回だけ読み込むことは、ニーズによっては最適な方法である場合があります。 ただし、1 日を通して数千から数百万もの行を読み込む必要がある場合、シングルトンの INSERT は適していない可能性があります。

この問題を解決する方法の 1 つは、ファイルに書き込む 1 つのプロセスを開発してから、そのファイルを定期的に読み込む別のプロセスを開発することです。 詳しくは、[INSERT](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関する記事をご覧ください。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase を使用して、データの読み込みとエクスポートをすばやく実行する

専用 SQL プールでは、Azure Data Factory、PolyBase、BCP など、いくつかのツールを使用したデータの読み込みとエクスポートがサポートされています。  パフォーマンスが重要でない少量のデータについては、どのツールでもニーズに十分応えることができます。  

> [!NOTE]
> PolyBase は、大量のデータを読み込んだり、エクスポートしたりする場合や、パフォーマンスを向上させる必要がある場合に最適な選択肢です。

PolyBase の読み込みは、CTAS または INSERT INTO を使用して実行できます。 CTAS を使用すると、トランザクション ログが最小限に抑えられ、データを最も高速に読み込むことができます。 Azure Data Factory では PolyBase の読み込みもサポートされており、CTAS と同様のパフォーマンスを実現できます。 PolyBase では、Gzip ファイルなど、さまざまなファイル形式をサポートしています。

Gzip テキスト ファイルを使用する場合にスループットを最大限引き上げるには、ファイルを 60 個以上に分割して、読み込みの並列処理を最大化してください。 全体のスループットを引き上げるには、データを同時に読み込むことを検討してください。 このセクションに関連する追加情報については、次の記事をご覧ください。

- [データの読み込み](../sql-data-warehouse/design-elt-data-loading.md?context=/azure/synapse-analytics/context/context)
- [PolyBase の使い方ガイド](data-loading-best-practices.md)
- [専用 SQL プールの読み込みパターンと戦略](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Azure Data Factory を使用してデータを読み込む](../../data-factory/load-azure-sql-data-warehouse.md?context=/azure/synapse-analytics/context/context)
- [Azure Data Factory を使用したデータ移動](../../data-factory/transform-data-using-machine-learning.md?context=/azure/synapse-analytics/context/context)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)

## <a name="load-then-query-external-tables"></a>外部テーブルを読み込んで、クエリを実行する

PolyBase はクエリには適していません。 専用 SQL プールの PolyBase テーブルで現在サポートされているのは、Azure BLOB ファイルと Azure Data Lake ストレージのみです。 こうしたファイルには、それをバックアップするためのコンピューティング リソースがありません。 つまり、専用 SQL プールではこの作業をオフロードできないため、データを読み取れるように、ファイルを `tempdb` に読み込んで、ファイル全体を読み取る必要があります。

このデータに対して複数のクエリを実行する場合は、データを一度読み込み、クエリでローカル テーブルが使用されるように指定することをお勧めします。 PolyBase に関する詳細なガイダンスについては、[PolyBase を使用するためのガイド](data-loading-best-practices.md)に関する記事をご覧ください。

## <a name="hash-distribute-large-tables"></a>ハッシュで大規模なテーブルを分散させる

既定では、テーブルはラウンド ロビン分散です。 この既定値により、ユーザーはテーブルの分散方法を決定しなくてもテーブルの作成を簡単に開始できます。 ラウンド ロビン テーブルは、一部のワークロードでは十分なパフォーマンスを示す可能性があります。 しかし、多くの場合、分散列の方がより優れたパフォーマンスを得られます。  

列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルを上回る最も一般的な例として、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。  

たとえば、orders テーブルが order_id で分散されており、transactions テーブルも order_id で分散されている場合に、orders テーブルを transactions テーブルに order_id で結合すると、このクエリはパススルー クエリになります。 その後、データの移動処理が行われなくなります。 手順が減るため、クエリは高速になります。 また、データの移動の減少もクエリの高速化に貢献します。

> [!TIP]
> 分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないようにしてください。 これを行うと、読み込みが遅くなります。

次に示す記事のリンクでは、分散列を選択してパフォーマンスを向上させる方法について詳しく説明しています。 また、CREATE TABLE ステートメントの WITH 句で分散テーブルを定義する方法についても説明しています。

- [テーブルの概要](develop-tables-overview.md)
- [テーブルのディストリビューション](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)
- [テーブル分散の選択](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>パーティション分割しすぎないようにする

データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。  多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、専用 SQL プールでは効果的ではありません。  

パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性があります。 専用 SQL プールでは、データが 60 個のデータベースに自動的にパーティション分割されます。 そのため、パーティションが 100 個あるテーブルを作成すると、パーティションが 6000 個になります。 ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。  

考慮すべき選択肢の 1 つは、SQL Server を使用して実装したものよりも低い粒度を使用することです。 たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

パーティション分割について詳しくは、[テーブルのパーティション分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)に関する記事をご覧ください。

## <a name="minimize-transaction-sizes"></a>トランザクション サイズを最小限に抑える

INSERT、UPDATE、DELETE の各ステートメントはトランザクションで実行されます。 失敗した場合は、それらをロールバックする必要があります。 ロールバック時間が長くならないようにするには、トランザクション サイズをできる限り最小限に抑えます。  トランザクション サイズを最小限に抑えるには、INSERT、UPDATE、DELETE の各ステートメントを複数に分割します。 たとえば、INSERT に 1 時間かかると予測される場合は、INSERT を 4 つに分割できます。 その結果、それぞれの実行時間は 15 分に短縮されます。  

> [!TIP]
> CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。  

ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。  たとえば、DELETE ステートメントを実行して、テーブル内の order_date が 2001 年 10 月のすべての行を削除するのではなく、データを月単位でパーティション分割できます。 その後、データを含むパーティションを別のテーブルの空のパーティションに切り替えることができます (ALTER TABLE の例をご覧ください)。  

パーティション分割されていないテーブルでは、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことをご検討ください。  CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS の方がはるかに安全に実行できます。

このセクションに関連する内容について詳しくは、以下の記事をご覧ください。

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)
- [トランザクションについて](develop-transactions.md)
- [トランザクションの最適化](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)
- [テーブル パーティション](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>クエリ結果のサイズを縮小する

クエリ結果のサイズを縮小することは、大きなクエリ結果によって発生するクライアント側の問題を回避するのに役立ちます。  クエリを編集して、返される行の数を減らすことができます。 クエリ生成ツールによって、各クエリに "上位 N" 構文を追加することができます。  また、クエリ結果を一時テーブルに CETAS を行ってから、ダウンレベル処理に PolyBase エクスポートを使用することもできます。

## <a name="use-the-smallest-possible-column-size"></a>できる限り最小の列サイズを使用する

DDL を定義するときは、データをサポートしている最小のデータ型を使用します。これにより、クエリのパフォーマンスが向上します。  この推奨事項は、CHAR 列と VARCHAR 列では特に重要です。  列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。  すべての文字列を既定の長さで定義しないようにします。  さらに、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

上記の情報に関連する重要な概念について詳しくは、[テーブルの概要](develop-tables-overview.md)、[テーブルのデータ型](develop-tables-data-types.md)、および [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) に関する記事をご覧ください。

## <a name="use-temporary-heap-tables-for-transient-data"></a>一時的なデータには一時的なヒープ テーブルを使用する

データを一時的に専用 SQL プールに読み込む場合、通常はヒープ テーブルによってプロセス全体が高速になります。  さらに変換を実行する前で、ステージングにのみデータを読み込んでいる場合は、ヒープ テーブルにデータを読み込む方が、クラスター化列ストア テーブルにデータを読み込むよりも高速になります。  

同様に、テーブルを永続ストレージに読み込むよりも、データを一時テーブルに読み込んだ方が読み込み速度が大幅に向上します。  一時テーブルは、"#" で始まり、作成元のセッションからしかアクセスできません。 そのため、一部のシナリオでしか機能しない可能性があります。 ヒープ テーブルは、CREATE TABLE の WITH 句で定義します。  一時テーブルを使用する場合は、その一時テーブルの統計も必ず作成してください。

詳細については、[一時テーブル](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) に関する記事をご覧ください。

## <a name="optimize-clustered-columnstore-tables"></a>クラスター化列ストア テーブルを最適化する

クラスター化列ストア インデックスは、専用 SQL プールにデータを格納する最も効率的な方法の 1 つです。  既定では、専用 SQL プールのテーブルは、クラスター化された ColumnStore として作成されます。  列ストア テーブルに対するクエリのパフォーマンスを最大限に引き出すには、セグメントの質が高いことが重要です。  行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  

セグメントの品質は、圧縮後の行グループに含まれる行の数を使って判断できます。 クラスター化列ストア テーブルのセグメントの質を検出して向上させる詳細な手順については、[テーブル インデックス](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)に関するページの「[列ストア インデックスの品質の低さの原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#causes-of-poor-columnstore-index-quality)」を参照してください。  

列ストア セグメントの質を高めることが非常に重要であるため、中規模または大規模リソース クラスのユーザー ID を使用してデータを読み込むことをお勧めします。 低い[データ ウェアハウス ユニット](resource-consumption-models.md)を使用すると、大きいリソース クラスを読み込みユーザーに割り当てることになります。

通常、テーブルあたりの行数が 100 万を超えるまで、列ストア テーブルでは圧縮された列ストア セグメントにデータをプッシュしません。 各専用 SQL プール テーブルは 60 個のテーブルにパーティション分割されます。 そのため、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。  

> [!TIP]
> 6,000 万行未満のテーブルについては、列ストア インデックスを使用しても最適なソリューションを得られない可能性があります。  

データをパーティション分割する場合、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要になります。  100 個のパーティションがあるテーブルについては、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション *100 個のパーティション* 100 万行)。  

テーブルに 60 億行もない場合は、2 つの主な選択肢があります。 パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。  列ストア テーブルの代わりに、ヒープ テーブルをセカンダリ インデックスとともに使用して、パフォーマンスが向上するかどうかを試してみる価値もあります。

列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。  テーブルおよび列ストアのインデックスの詳細については、下の記事を参照してください。
- [列ストア インデックスの品質の低さの原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)
- [列ストア インデックス ガイド](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)
- [列ストア インデックスの再構築](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuild-indexes-to-improve-segment-quality) 
- [順序指定クラスター化列ストア インデックスを使用したパフォーマンスのチューニング](../sql-data-warehouse/performance-tuning-ordered-cci.md)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>大きなリソース クラスを使用して、クエリのパフォーマンスを向上させる

SQL プールでは、クエリにメモリを割り当てる方法としてリソース グループが使用されます。 最初は、ディストリビューションごとに 100 MB のメモリが与えられる小さいリソース クラスにすべてのユーザーが割り当てられます。  常に 60 個のディストリビューションが存在します。 各ディストリビューションには、最低 100 MB が割り当てられます。 システム全体のメモリ割り当ての合計は、6,000 MB (6 GB 弱) です。  

大規模な結合やクラスター化列ストア テーブルへの読み込みなど、特定のクエリについては、割り当てるメモリを増やすと効果的です。  純粋なスキャンなどのクエリでは、効果はありません。 より大きなリソース クラスを利用すると、コンカレンシーに影響します。 そのため、すべてのユーザーを大きなリソース クラスに移行する前に、こうした点に注意する必要があります。

リソース クラスについて詳しくは、[ワークロード管理用のリソース クラス](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context)に関する記事をご覧ください。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>小さいリソース クラスを使用して、コンカレンシーを増やす

ユーザー クエリの遅延が長いと感じられる場合は、ユーザーが大きなリソース クラスで実行している可能性があります。 このシナリオでは、コンカレンシー スロットが大量に使用されており、それが原因で他のクエリがキューに配置される可能性があります。  ユーザー クエリがキューに配置されているかどうかを判断するには、`SELECT * FROM sys.dm_pdw_waits` を実行して、行が返されるかどうかを確認します。

詳しくは、[ワークロード管理用のリソース クラス](../sql-data-warehouse/resource-classes-for-workload-management.md)と [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関する記事をご覧ください。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV を使用して、クエリを監視および最適化する

専用 SQL プールには、クエリの実行を監視するために使用できる DMV がいくつか用意されています。  以下の監視に関する記事では、実行中のクエリの詳細を確認する方法について順を追って説明しています。  これらの DMV でクエリをすばやく見つけるには、クエリで LABEL オプションを使用すると便利です。 詳しくは、以下の一覧に記載されている記事をご覧ください。

- [DMV を利用してワークロードを監視する](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>次のステップ

一般的な問題と解決方法については、[トラブルシューティング](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?context=/azure/synapse-analytics/context/context)に関する記事もご覧ください。

この記事に記載されていない情報が必要な場合は、[Azure Synapse に関する Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html)を検索して、他のユーザーや Azure Synapse Analytics 製品グループに質問することができます。  

Microsoft では、このフォーラムを積極的に監視し、お客様からの質問に他のユーザーや Microsoft のスタッフが回答しているかどうかを確認しています。  Stack Overflow で質問したい方のために、[Azure Synapse Analytics Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-synapse)も用意しています。

