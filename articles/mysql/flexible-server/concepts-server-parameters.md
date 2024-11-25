---
title: サーバー パラメーター - Azure Database for MySQL - フレキシブル サーバー
description: このトピックでは、Azure Database for MySQL - フレキシブル サーバーでのサーバー パラメーターの構成に関するガイドラインを示します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 59bb5a6a2a544eb72d1438c38ad3040c2ac43476
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468368"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーのサーバー パラメーター

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、Azure Database for MySQL フレキシブル サーバーでサーバー パラメーターを構成するための考慮事項とガイドラインを示します。

## <a name="what-are-server-variables"></a>サーバー変数とは

MySQL エンジンには、エンジンの動作を構成および調整するために使用できるさまざまな[サーバー変数やパラメーター](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html)が用意されています。 実行時に動的に設定できるパラメーターもあれば、適用するためにサーバーを再起動する必要がある "静的" なものもあります。

Azure Database for MySQL フレキシブル サーバーでは、さまざまな MySQL サーバー パラメーターの値を、[Azure portal](./how-to-configure-server-parameters-portal.md) および [Azure CLI](./how-to-configure-server-parameters-cli.md) を使ってワークロードのニーズに合わせて変更できる機能が公開されています。

## <a name="configurable-server-parameters"></a>構成可能なサーバー パラメーター

サーバー パラメーターを使用して Azure Database for MySQL フレキシブル サーバー構成を管理できます。 このサーバー パラメーターは、サーバーの作成時に既定値と推奨値を使用して構成されます。 Azure portal の [サーバー パラメーター] ブレードには、変更可能および変更不可のサーバー パラメーターの両方が表示されます。 変更不可のサーバー パラメーターはグレーで表示されます。

サポートされるサーバー パラメーターの一覧は、拡大を続けています。 Azure portal の [サーバー パラメーター] タブを使用して完全な一覧を表示し、サーバー パラメーターの値を構成できます。

よく更新されるいくつかのサーバー パラメーターに関する制限の詳細については、以下のセクションを参照してください。 制限は、サーバーのコンピューティング レベルとサイズ (仮想コア) によって決まります。

> [!NOTE]
> 変更不可だが、お使いの環境に合わせて変更可能と見なしたいサーバー パラメーターの変更を検討している場合は、[UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0) 項目を開くか、フィードバックが既に存在する場合は投票してください。これは Microsoft が優先順位を付けるのに役立ちます。

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL フレキシブル サーバーの場合、バイナリ ログは常に有効になっています (つまり、`log_bin` が ON に設定されています)。 フレキシブル サーバーでは、log_bin_trust_function_creators は既定で ON に設定されています。

バイナリ ログ形式は常に **行** であり、サーバーへのすべての接続では **常に** 行ベースのバイナリ ログが使用されます。 行ベースのバイナリ ログを使用すると、セキュリティ上の問題が存在せず、バイナリ ログを中断できないため、安全に [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) を **オン** のままにしておくことができます。

[`log_bin_trust_function_creators`] がオフに設定されている場合、トリガーの作成を試みると、"*SUPER 特権を持っておらず、バイナリ ログが有効になっています (より安全度の低い`log_bin_trust_function_creators` 変数を使用することもできます)* " のようなエラーが表示されます。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)を確認してください。

|**価格レベル**|**仮想コア数**|**メモリ サイズ (GiB)**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|---|
|バースト可能 (B1s)|1|1|134217728|33554432|134217728|
|バースト可能 (B1ms)|1|2|536870912|134217728|536870912|
|バースト可能|2|4|2147483648|134217728|2147483648|
|General Purpose|2|8|6442450944|134217728|6442450944|
|General Purpose|4|16|12884901888|134217728|12884901888|
|General Purpose|8|32|25769803776|134217728|25769803776|
|General Purpose|16|64|51539607552|134217728|51539607552|
|General Purpose|32|128|103079215104|134217728|103079215104|
|General Purpose|48|192|154618822656|134217728|154618822656|
|General Purpose|64|256|206158430208|134217728|206158430208|
|メモリ最適化|2|16|12884901888|134217728|12884901888|
|メモリ最適化|4|32|25769803776|134217728|25769803776|
|メモリ最適化|8|64|51539607552|134217728|51539607552|
|メモリ最適化|16|128|103079215104|134217728|103079215104|
|メモリ最適化|32|256|206158430208|134217728|206158430208|
|メモリ最適化|48|384|309237645312|134217728|309237645312|
|メモリ最適化|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL では、テーブルの作成時に指定した構成に基づいて、InnoDB テーブルが異なるテーブルスペースに格納されます。 [システム テーブルスペース](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)は、InnoDB データ辞書のストレージ領域です。 [file-per-table テーブルスペース](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)は、1 つの InnoDB テーブルに対するデータとインデックスを含み、固有のデータ ファイル内のファイル システムに格納されています。 この動作は、`innodb_file_per_table` サーバー パラメーターによって制御されています。 `innodb_file_per_table` を `OFF` に設定すると、InnoDB ではシステム テーブルスペースにテーブルが作成されます。 それ以外の場合、InnoDB では file-per-table テーブルスペースにテーブルが作成されます。

Azure Database for MySQL フレキシブル サーバーは、1 つのデータ ファイル内で、最大 **4 TB** までをサポートしています。 データベースのサイズが 4 TB を超える場合は、[innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) テーブルスペースにテーブルを作成する必要があります。 1 つのテーブル サイズが 4 TB を超える場合は、パーティション テーブルを使用する必要があります。

### <a name="innodb_log_file_size"></a>innodb_log_file_size

[innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) は、[ログ グループ](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_log_group)内の各[ログ ファイル](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_log_file)のサイズ (バイト単位) です。 ログ ファイルの合計サイズ ([innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) * [innodb_log_files_in_group](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_files_in_group)) は、最大値 (512 GB 弱) を超えることはできません。 ログ ファイルのサイズを大きくすると、パフォーマンスが向上しますが、クラッシュ後の復旧時間が長くなるという欠点があります。 まれに発生するクラッシュ後の復旧の復旧時間と、ピーク操作中のスループットの最大化との間でバランスを取る必要があります。 これにより、再起動時間が長くなることもあります。 Azure Database for MySQL フレキシブル サーバーでは、innodb_log_file_size を 256 MB、512 MB、1 GB、2 GB のいずれかの値に構成できます。 このパラメーターは静的であり、再起動が必要です。

> [!NOTE]
> innodb_log_file_size パラメーターを既定値から変更した場合は、再起動の遅延を回避するため、"show global status like 'innodb_buffer_pool_pages_dirty'" の値が 30 秒間 0 のままであるかどうかを確認してください。



### <a name="max_connections"></a>max_connections

max_connection の値は、サーバーのメモリ サイズによって決まります。

|**価格レベル**|**仮想コア数**|**メモリ サイズ (GiB)**|**既定値**|**最小値**|**最大値**|
|---|---|---|---|---|---|
|バースト可能 (B1s)|1|1|85|10|171|
|バースト可能 (B1ms)|1|2|171|10|341|
|バースト可能|2|4|341|10|683|
|General Purpose|2|8|683|10|1365|
|General Purpose|4|16|1365|10|2731|
|General Purpose|8|32|2731|10|5461|
|General Purpose|16|64|5461|10|10923|
|General Purpose|32|128|10923|10|21845|
|General Purpose|48|192|16384|10|32768|
|General Purpose|64|256|21845|10|43691|
|メモリ最適化|2|16|1365|10|2731|
|メモリ最適化|4|32|2731|10|5461|
|メモリ最適化|8|64|5461|10|10923|
|メモリ最適化|16|128|10923|10|21845|
|メモリ最適化|32|256|21845|10|43691|
|メモリ最適化|48|384|32768|10|65536|
|メモリ最適化|64|504|43008|10|86016|

接続数が制限を超えると、次のエラーが表示される場合があります。
> ERROR 1040 (08004):Too many connections (接続が多すぎます)

> [!IMPORTANT]
>最適なエクスペリエンスを得るために、ProxySQL のような接続プーラーを使用して、接続を効率的に管理することをお勧めします。

MySQL への新しいクライアント接続を作成するには時間がかかり、一度確立されると、アイドル状態のときでも、これらの接続によってデータベース リソースが消費されます。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 アイドル状態の接続を減らして既存の接続を再利用する接続プーラーは、これを回避するのに役立ちます。 ProxySQL の設定については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)を参照してください。

>[!Note]
>ProxySQL はオープンソースのコミュニティ ツールです。 Microsoft によってベスト エフォート方式でサポートされています。 信頼のあるガイダンスでの運用サポートを受けるには、[ProxySQL 製品サポート](https://proxysql.com/services/support/)を確認して問い合わせることができます。

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"Row size too large (> 8126) (行のサイズが大きすぎます (> 8126))" などのエラーが表示された場合は、**innodb_strict_mode** パラメーターをオフにすることができます。 サーバー パラメーター **innodb_strict_mode** をサーバー レベルでグローバルに変更することはできません。行データのサイズが 8 kb を超える場合、エラーが表示されずにデータが切り捨てられ、データが失われる可能性があるためです。 ページ サイズの制限に合うようにスキーマを変更することをお勧めします。

このパラメーターは、`init_connect` を使用してセッション レベルで設定できます。 セッション レベルで **innodb_strict_mode** を設定するには、「[設定パラメーターが一覧に含まれていない](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)」を参照してください。

> [!NOTE]
> 読み取りレプリカ サーバーがある場合は、ソース サーバーのセッション レベルで **innodb_strict_mode** をオフに設定すると、レプリケーションが中断されます。 読み取りレプリカがある場合は、このパラメーターをオフに設定されたままにすることをお勧めします。

### <a name="time_zone"></a>time_zone

初期デプロイの時点で、Azure for MySQL フレキシブル サーバーにはタイム ゾーン情報のシステム テーブルが含まれていますが、これらのテーブルには値が設定されていません。 タイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。 ストアド プロシージャを呼び出す方法とグローバル レベルまたはセッション レベルのタイム ゾーンを設定する方法については、[Azure portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) または [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) の記事を参照してください。

### <a name="binlog_expire_logs_seconds"></a>binlog_expire_logs_seconds 

Azure Database for MySQL では、このパラメーターは、サービスがバイナリ ログ ファイルを消去するまでに待機する秒数を指定します。

バイナリ ログには、テーブルの作成操作やテーブル データの変更などのデータベースの変更を記述する "イベント" が含まれます。 また、変更を加えた可能性のあるステートメントのイベントも含まれます。 バイナリ ログは、主にレプリケーション操作とデータの復旧操作の 2 つの目的で使用されます。  通常、バイナリ ログは、ハンドルがサービス、バックアップ、またはレプリカ セットから解放されるとすぐに消去されます。 複数のレプリカの場合、最も遅いレプリカが変更を読み取るまで待機してから消去されます。 バイナリ ログをより長期間保持するには、binlog_expire_logs_seconds パラメーターを構成します。 binlog_expire_logs_seconds が既定値の 0 に設定されている場合、バイナリ ログは、ハンドルが解放されるとすぐに消去されます。 binlog_expire_logs_seconds が 0 を超える場合、設定された秒数待機してから消去されます。 Azure Database for MySQL の場合、バックアップおよび読み取りレプリカでのバイナリ ファイルの消去などのマネージド機能は内部で処理されます。 Azure Database for MySQL サービスからデータ出力をレプリケートする場合は、レプリカによってプライマリから変更が読み取られる前にバイナリ ログが消去されるのを回避するために、プライマリでこのパラメーターを設定する必要があります。 binlog_expire_logs_seconds に大きな値を設定すると、バイナリ ログがすぐに消去されず、ストレージの課金が増加する可能性があります。 

## <a name="non-modifiable-server-parameters"></a>変更不可のサーバー パラメーター

Azure portal の [サーバー パラメーター] ブレードには、変更可能および変更不可のサーバー パラメーターの両方が表示されます。 変更不可のサーバー パラメーターはグレーで表示されます。セッション レベルで変更不可のサーバー パラメーターを構成する場合、`init_connect` を使用して接続レベルでパラメーターを設定する方法については、[Azure portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) または [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) の記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ポータルでサーバー パラメーター](./how-to-configure-server-parameters-portal.md)を構成する方法
- [Azure CLI でサーバー パラメーター](./how-to-configure-server-parameters-cli.md)を構成する方法
