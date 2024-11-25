---
title: sys_schema を利用する - Azure Database for MySQL
description: sys_schema を使ってパフォーマンスの問題を見つけ、Azure Database for MySQL のデータベースを保守する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: a50b2e8966a2f34a6e14caf98784291b0c536ec1
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088424"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Azure Database for MySQL でパフォーマンスのチューニングとデータベースのメンテナンスに sys_schema を使用する方法

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

MySQL 5.5 で初めて導入された MySQL performance_schema では、メモリ割り当て、ストアド プログラム、メタデータ ロックなど、多くの重要なサーバー リソースのためのインストルメンテーションが提供されています。ただし、performance_schema には 80 以上のテーブルが含まれ、必要な情報を入手するには performance_schema 内のテーブルや information_schema のテーブルの結合が必要になることがよくあります。 performance_schema と information_schema の両方を基にして作成されている sys_schema は、読み取り専用のデータベースで[ユーザー フレンドリなビュー](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html)の強力なコレクションを提供し、Azure Database for MySQL バージョン 5.7 では完全に有効になっています。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="sys_schema のビュー":::

sys_schema には 52 個のビューがあり、各ビューには次のいずれかのプレフィックスが付いています。

- host_summary または io: I/O 関連の待機時間。
- innoDB: InnoDB バッファーの状態とロック。
- メモリ:ホストとユーザーによるメモリ使用量。
- スキーマ:自動インクリメントやインデックスなどのスキーマ関連の情報。
- statement: SQL ステートメントに関する情報。フル テーブル スキャンや長いクエリ時間が発生するステートメントである場合があります。
- ユーザー:消費され、ユーザーごとにグループ化されたリソース。 ファイル I/O、接続、メモリなどです。
- wait: ホストまたはユーザーごとにグループ化された待機イベント。

では、sys_schema の一般的な使用パターンをいくつか見てみましょう。 まず、使用パターンを **パフォーマンスのチューニング** と **データベース メンテナンス** の 2 つのカテゴリにグループ化します。

## <a name="performance-tuning"></a>パフォーマンスのチューニング

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO は、データベースで最もコストのかかる操作です。 *sys.user_summary_by_file_io* ビューのクエリを行うことにより、平均 IO 待機時間がわかります。 既定値の 125 GB でプロビジョニングされたこの例のストレージでは、IO 待機時間は約 15 秒です。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="io latency: 125 GB":::

Azure Database for MySQL ではストレージに応じて IO が増減するので、このプロビジョニングされたストレージを 1 TB に増やすと、IO 待機時間は 571 ミリ秒に減ります。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="io latency: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

慎重に計画しても、多くのクエリでフル テーブル スキャンが行われる可能性があります。 インデックスの種類とそれらを最適化する方法の詳細については、[クエリのパフォーマンスをトラブルシューティングする方法](./howto-troubleshoot-query-performance.md)に関する記事を参照できます。 フル テーブル スキャンはリソースを大量に消費し、データベースのパフォーマンスを低下させます。 フル テーブル スキャンが行われたテーブルを調べる最も簡単な方法は、*sys.schema_tables_with_full_table_scans* ビューのクエリを行うことです。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="フル テーブル スキャン":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

データベースのパフォーマンスの問題をトラブルシューティングするには、データベースの内部で起こっているイベントを明らかにすると役に立つ場合があり、*sys.user_summary_by_statement_type* ビューがそれに使えることがあります。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="ステートメントごとの概要":::

この例の Azure Database for MySQL は、クエリ ログを 44579 回フラッシュするのに 53 分かかっています。 それは、長い時間と多数の IO です。 Azure Portal で低速のクエリ ログを無効にするか、低速のクエリ ログの頻度を減らすことにより、このアクティビティを削減できます。

## <a name="database-maintenance"></a>データベース メンテナンス

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> このビューにクエリを実行すると、パフォーマンスに影響する場合があります。 このトラブルシューティングは、ピーク時以外の営業時間に実行することをお勧めします。

InnoDB バッファー プールはメモリ内に存在し、DBMS とストレージの間の主なキャッシュ メカニズムです。 InnoDB バッファー プールのサイズはパフォーマンス レベルに関連付けられており、別の製品 SKU を選ばない限り変更できません。 オペレーティング システムのメモリと同様に、古いページはスワップ アウトされて新しいデータのための領域が確保されます。 InnoDB バッファー プールのメモリを最も多く消費しているテーブルを調べるには、*sys.innodb_buffer_stats_by_table* ビューのクエリを行います。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="InnoDB バッファーの状態":::

上の図では、システム テーブルとビューを除くと、WordPress サイトの 1 つをホストしている mysqldatabase033 データベース内の各テーブルが、16 KB つまり 1 ページのデータでメモリを占めていることがわかります。

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

インデックスは、読み取りのパフォーマンスを向上させる優れたツールですが、挿入とストレージの追加コストがかかります。 *Sys.schema_unused_indexes* と *sys.schema_redundant_indexes* は、使われていないインデックスまたは重複するインデックスについての詳しい情報を提供します。

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="使われていないインデックス":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="冗長なインデックス":::

## <a name="conclusion"></a>まとめ

まとめると、sys_schema はパフォーマンスのチューニングとデータベースのメンテナンスの両方に対して優れたツールです。 お使いの Azure Database for MySQL でこの機能を活用してください。 

## <a name="next-steps"></a>次のステップ

- 最も気になる質問への回答を探したり、新しい質問や回答を投稿したりするには、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql) をご覧ください。
