---
title: データイン レプリケーションを構成する - Azure Database for MySQL
description: この記事では、Azure Database for MySQL のデータイン レプリケーションをセットアップする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 04/08/2021
ms.openlocfilehash: fb12a8617396eb0c582ed2b00f856af7f91765fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611417"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL のデータイン レプリケーションを構成する方法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

この記事では、ソースとレプリカのサーバーを構成することによって、Azure Database for MySQL で[データイン レプリケーション](concepts-data-in-replication.md)を設定する方法について説明します。 この記事は、MySQL サーバーとデータベースに関して、ある程度の使用経験があることを前提としています。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。
>

Azure Database for MySQL サービスでレプリカを作成するために、[データイン レプリケーション](concepts-data-in-replication.md)によって、オンプレミス、仮想マシン (VM)、またはクラウド データベース サービスのソース MySQL サーバーからデータが同期されます。 データイン レプリケーションは、MySQL のネイティブなバイナリ ログ (binlog) ファイルの位置ベースまたは GTID ベースのレプリケーションに基づいています。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。

この記事の手順を実行する前に、データイン レプリケーションの[制限事項と要件](concepts-data-in-replication.md#limitations-and-considerations)を確認してください。

## <a name="create-an-azure-database-for-mysql-single-server-instance-to-use-as-a-replica"></a>レプリカとして使用する Azure Database for MySQL 単一サーバー インスタンスを作成する

1. Azure Database for MySQL 単一サーバーの新しいインスタンスを作成します (例: `replica.mysql.database.azure.com`)。 サーバーの作成については、「[Azure portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)」を参照してください。 このサーバーは、データイン レプリケーション用の "レプリカ" サーバーです。

   > [!IMPORTANT]
   > Azure Database for MySQL サーバーは、General Purpose またはメモリ最適化価格レベルで作成する必要があります。データイン レプリケーションはこれらのレベルでのみサポートされるためです。

2. 同じユーザー アカウントとそれに対応する権限を作成します。

   レプリカ サーバーにソース サーバーのユーザー アカウントはレプリケートされません。 レプリカ サーバーへのアクセス権をユーザーに与えることを検討している場合、この新しく作成した Azure Database for MySQL サーバーに、すべてのアカウントとそれらに対応する権限を手動で作成する必要があります。

3. ソース サーバーの IP アドレスをレプリカのファイアウォール規則に追加します。

   [Azure portal](howto-manage-firewall-using-portal.md) または [Azure CLI](howto-manage-firewall-using-cli.md) を使用してファイアウォール規則を更新します。

4. **省略可能** - ソース サーバーから Azure Database for MySQL レプリカ サーバーへの [GTID ベースのレプリケーション](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)を使用する場合は、以下のポータルの画像に示されているように、Azure Database for MySQL サーバーで次のサーバー パラメーターを有効にする必要があります。

   :::image type="content" source="./media/howto-data-in-replication/enable-gtid.png" alt-text="Azure Database for MySQL サーバーで GTID を有効にする":::

## <a name="configure-the-source-mysql-server"></a>ソース MySQL サーバーを構成する

次の手順では、仮想マシンでオンプレミスでホストされる MySQL サーバーを準備して構成するか、データイン レプリケーションのために他のクラウド プロバイダーによってホストされるデータベース サービスを準備して構成します。 このサーバーは、データイン レプリケーション用の "ソース" となります。

1. 続行する前に、[ソース サーバーの要件](concepts-data-in-replication.md#requirements)をご確認ください。

2. ソース サーバーでポート 3306 での受信と送信の両方のトラフィックが許可されていて、それに対して **パブリック IP アドレス** が割り当てられている、または DNS にパブリックにアクセス可能である、あるいは完全修飾ドメイン名 (FQDN) があることを確かめます。

   別のマシンでホストされている MySQL のコマンド ラインなどのツールから、または Azure portal で使用可能な [Azure Cloud Shell](../cloud-shell/overview.md) から接続を試行して、ソース サーバーへの接続性をテストします。

   組織に厳密なセキュリティ ポリシーがあり、それによって、ソース サーバー上のすべての IP アドレスで Azure からソース サーバーへの通信が許可されない場合は、以下のコマンドを使用して MySQL サーバーの IP アドレスを確認できる可能性があります。

   1. MySQL コマンド ラインなどのツールを使用して、Azure Database for MySQL サーバーにサインインします。

   2. 次のクエリを実行します。

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      出力例を次に示します。

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. MySQL コマンド ラインを終了します。
   4. IP アドレスを取得するには、ping ユーティリティで次のコマンドを実行します。

      ```bash
      ping <output of step 2b>
      ```

      次に例を示します。

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 前のステップで出力された IP アドレスがポート 3306 に含まれるように、ソース サーバーのファイアウォール規則を構成します。

      > [!NOTE]
      > この IP アドレスは、メンテナンスまたはデプロイ操作により変更される可能性があります。 この接続方法は、ポート 3306 上ですべての IP アドレスを許可することができないお客様のみを対象としています。
  
3. バイナリ ログを有効にします。

   ソースでバイナリ ログが有効になっているかどうかを、次のコマンドを実行してチェックします。

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   変数 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) の戻り値が "ON" であった場合、サーバーでバイナリ ログが有効になっています。

   値 "オフ" で `log_bin` が返されたとき、構成ファイル (my.cnf) にアクセスするオンプレミスまたは仮想マシン上でソース サーバーが実行されている場合、以下の手順をご利用いただけます。
   1. ソース サーバーで MySQL 構成ファイル (my.cnf) を見つけます。 例: /etc/my.cnf
   2. 構成ファイルを開いて編集し、ファイル内の **mysqld** セクションを見つけます。
   3. mysqld セクションで、次の行を追加します。

       ```bash
       log-bin=mysql-bin.log
       ```

   4. 変更を有効にするために、MySQL ソース サーバーを再起動します。
   5. サーバーが再起動された後、前と同じクエリを実行して、バイナリ ログが有効になっていることを確認します。

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. ソース サーバーの設定を構成します。

   データイン レプリケーションの場合、ソース サーバーとレプリカ サーバーの間でパラメーター `lower_case_table_names` を一致させる必要があります。 Azure Database for MySQL では、このパラメーターが既定で 1 になっています。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

   **省略可能** - [GTID ベースのレプリケーション](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)を使用する場合は、ソース サーバーで GTID が有効になっているかどうかを確認する必要があります。 ソース MySQL サーバーに対して次のコマンドを実行すると、gtid_mode が ON になっているかどうかを確認できます。

   ```sql
   show variables like 'gtid_mode';
   ```

   >[!IMPORTANT]
   > すべてのサーバーで、gtid_mode が既定値の OFF に設定されています。 データイン レプリケーションを設定するために特にソース MySQL サーバーで GTID を有効にする必要はありません。 GTID がソース サーバーで既に有効になっている場合は、必要に応じて GTID ベースのレプリケーションを使用して、Azure Database for MySQL 単一サーバーでデータイン レプリケーションを設定することもできます。 ソース サーバー上の gitd_mode の構成に関係なく、すべてのサーバーのデータイン レプリケーションを設定するためにファイルベースのレプリケーションを使用できます。

5. 新しいレプリケーション ロールを作成し、権限を設定します。

   レプリケーションの権限を持つように構成されたユーザー アカウントをソース サーバーに作成します。 この作業は、SQL コマンド、または MySQL Workbench などのツールを使用して行うことができます。 SSL を使用するレプリケートを計画するかどうかを検討してください。これはユーザーを作成するときに指定する必要があります。 ソース サーバーに[ユーザー アカウントを追加](https://dev.mysql.com/doc/refman/5.7/en/user-names.html)する方法については、MySQL のドキュメントを参照してください。

   次のコマンドでは、作成した新しいレプリケーション ロールを使用すると、ソース自体をホストするマシンだけでなく、任意のマシンからソースにアクセスできます。 そのため、create user コマンドには "syncuser@'%'" を指定しています。 [アカウント名の指定](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)について詳しくは、MySQL のドキュメントをご覧ください。

   **SQL コマンド**

   *SSL を使用したレプリケーション*

   すべてのユーザー接続に SSL を要求するには、次のコマンドを使用してユーザーを作成します。

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL を使用しないレプリケーション*

   必ずしもすべての接続に SSL が必要でない場合は、次のコマンドを使用してユーザーを作成します。

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   MySQL Workbench でレプリケーション ロールを作成するには、 **[Management]\(管理\)** ウィンドウで **[Users and Privileges]\(ユーザーと権限\)** を選択してから、 **[アカウントの追加]** を選択します。

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="[Users and Privileges]\(ユーザーと権限\)":::

   **[Login Name]\(ログイン名\)** フィールドにユーザー名を入力します。

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="同期ユーザー":::

   **[Administrative Roles]\(管理者ロール\)** パネルを選択して、 **[Global Privileges]\(グローバル権限\)** の一覧から **[Replication Slave]\(レプリケーション スレーブ\)** を選択します。 次に、 **[Apply]\(適用\)** を選択して、レプリケーション ロールを作成します。

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="[Replication Slave]\(レプリケーション スレーブ\)":::

6. ソース サーバーを読み取り専用モードに設定します。

   データベースのダンプを開始する前に、サーバーを読み取り専用モードにする必要があります。 読み取り専用モードの間、ソースは書き込みトランザクションを一切処理できなくなります。 業務への影響を見積もり、必要であればピーク時を外して読み取り専用の時間帯をスケジュールしてください。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. バイナリ ログ ファイルの名前とオフセットを取得します。

   [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) コマンドを実行して、現在のバイナリ ログ ファイルの名前とオフセットを調べます。

   ```sql
    show master status;
   ```
   結果は次のようになります。 バイナリ ファイルの名前はこの後の手順で使用するため、必ずメモしておいてください。

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="master status の結果":::

## <a name="dump-and-restore-the-source-server"></a>ソース サーバーのダンプと復元

1. Azure Database for MySQL にレプリケートするデータベースとテーブルを決定し、ソース サーバーからダンプを実行します。

    mysqldump を使用して、プライマリ サーバーからデータベースをダンプすることができます。 詳細については、「[ダンプと復元](concepts-migrate-dump-restore.md)」を参照してください。 MySQL ライブラリとテスト ライブラリをダンプする必要はありません。

2. **省略可能** - [gtid ベースのレプリケーション](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)を使用する場合は、プライマリで最後に実行されたトランザクションの GTID を識別する必要があります。 次のコマンドを使用して、マスター サーバーで最後に実行されたトランザクションの GTID を確認できます。

   ```sql
   show global variables like 'gtid_executed';
   ```

3. ソース サーバーを読み取り/書き込みモードに設定します。

   データベースのダンプ後、ソース MySQL サーバーを読み取り/書き込みモードに戻します。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. ダンプ ファイルを新しいサーバーに復元します。

   Azure Database for MySQL サービスに作成したサーバーにダンプ ファイルを復元します。 ダンプ ファイルを MySQL サーバーに復元する方法については、「[ダンプと復元](concepts-migrate-dump-restore.md)」を参照してください。 大きいダンプ ファイルは、レプリカ サーバーと同じリージョンにある Azure 内の仮想マシンにアップロードしてください。 そのファイルを仮想マシンから Azure Database for MySQL サーバーに復元します。

5. **省略可能** - 必ずプライマリ サーバーと同じになるように、Azure Database for MySQL で復元されたサーバーの GTID を確認します。 次のコマンドを使用して、Azure Database for MySQL レプリカ サーバー上で GTID の purged 値の GTID を確認できます。 gtid_purged の値は、GTID ベースのレプリケーションを機能させるための手順 2 に示されているマスターの gtid_executed と同じである必要があります。

   ```sql
   show global variables like 'gtid_purged';
   ```

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>ソースとレプリカのサーバーをリンクしてデータイン レプリケーションを開始する

1. ソース サーバーを設定します。

   データイン レプリケーションの機能は、すべてストアド プロシージャによって実現されています。 「[データイン レプリケーションのストアド プロシージャ](./reference-stored-procedures.md)」で、すべてのプロシージャをご覧いただけます。 これらのストアド プロシージャは、MySQL シェルまたは MySQL Workbench で実行できます。

   2 つのサーバーをリンクさせてレプリケーションを開始するには、Azure DB for MySQL サービスにあるレプリケーション先のレプリカ サーバーにログインし、外部インスタンスをソース サーバーとして設定します。 この設定は、Azure DB for MySQL サーバーで `mysql.az_replication_change_master` ストアド プロシージャを使用して行います。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   **省略可能** - [gtid ベースのレプリケーション](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)を使用する場合は、次のコマンドを使用して 2 つのサーバーをリンクする必要があります

    ```sql
   call mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_ssl_ca>');
   ```

   - master_host: ソース サーバーのホスト名
   - master_user: ソース サーバーのユーザー名
   - master_password: ソース サーバーのパスワード
   - master_port: ソース サーバーで接続をリッスンしているポート番号 (3306 は MySQL でリッスンする既定のポートです)。
   - master_log_file: `show master status` を実行することによって得たバイナリ ログ ファイルの名前
   - master_log_pos: `show master status` を実行することによって得たバイナリ ログの位置
   - master_ssl_ca:CA 証明書のコンテキスト。 SSL を使用していない場合は、空の文字列を渡します。

     このパラメーターは変数で渡すことをお勧めします。 詳細については、次の例を参照してください。

   > [!NOTE]
   > ソース サーバーが Azure VM にホストされる場合は、[Azure サービスへのアクセスを許可] を [有効] に設定して、ソースとレプリカのサーバーが相互に通信できるようにします。 この設定は、 **[接続のセキュリティ]** オプションから変更できます。 詳細については、[ポータルを使用してファイアウォール規則を管理する](howto-manage-firewall-using-portal.md)方法に関するページを参照してください。

   **使用例**

   *SSL を使用したレプリケーション*

   変数 `@cert` は、次の MySQL コマンドを実行して作成します。

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   SSL を使用したレプリケーションを、ドメイン "companya.com" にホストされたソース サーバーと Azure Database for MySQL にホストされたレプリカ サーバーとの間で設定します。 このストアド プロシージャはレプリカで実行します。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *SSL を使用しないレプリケーション*

   SSL を使用しないレプリケーションを、ドメイン "companya.com" にホストされたソース サーバーと Azure Database for MySQL にホストされたレプリカ サーバーとの間で設定します。 このストアド プロシージャはレプリカで実行します。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. フィルター処理を設定します。

   マスターからの一部のテーブルのレプリケートをスキップする場合は、レプリカ サーバーの `replicate_wild_ignore_table` サーバー パラメーターを更新します。 コンマ区切りのリストを使用して、複数のテーブル パターンを指定できます。

   このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)を確認してください。

   このパラメーターを更新するには、[Azure portal](howto-server-parameters.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用できます。

3. レプリケーションを開始します。

   `mysql.az_replication_start` ストアド プロシージャを呼び出してレプリケーションを開始します。

   ```sql
   CALL mysql.az_replication_start;
   ```

4. レプリケーションの状態を確認します。

   レプリカ サーバーで [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) コマンドを呼び出して、レプリケーションの状態を確認します。

   ```sql
   show slave status;
   ```

   `Slave_IO_Running` と `Slave_SQL_Running` の状態が "yes" で、`Seconds_Behind_Master` の値が "0" であれば、レプリケーションは正常に機能しています。 `Seconds_Behind_Master` は、レプリカの遅れの程度を示しています。 この値が "0" 以外である場合、レプリカで更新処理が実行されていることを意味します。

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>データイン レプリケーション操作のためのその他の便利なストアド プロシージャ

### <a name="stop-replication"></a>レプリケーションの停止

ソースとレプリカのサーバー間でレプリケーションを停止するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>レプリケーション関係の解除

ソースとレプリカのサーバーの関係を解除するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>レプリケーション エラーのスキップ

レプリケーション エラーをスキップして、レプリケーションを続行できるようにするには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_skip_counter;
```

 **省略可能** - [gtid ベースのレプリケーション](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)を使用する場合は、次のストアド プロシージャを使用してトランザクションをスキップします

```sql
call mysql. az_replication_skip_gtid_transaction(‘<transaction_gtid>’)
```

このプロシージャで指定された GTID のトランザクションをスキップできます。 GTID 形式が適切でない場合、または GTID トランザクションが既に実行されている場合、プロシージャの実行は失敗します。 トランザクションの GTID は、バイナリ ログを解析してトランザクション イベントを確認することによって判別できます。 MySQL には、バイナリ ログを解析し、その内容をテキスト形式で表示するユーティリティ [mysqlbinlog](https://dev.mysql.com/doc/refman/5.7/en/mysqlbinlog.html) が用意されています。これを使用して、トランザクションの GTID を識別できます。

現在のレプリケーションの位置の後の次のトランザクションをスキップするには、以下に示されているように、以下のコマンドを使用して次のトランザクションの GTID を識別します。

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/howto-data-in-replication/show-binary-log.png" alt-text="バイナリ ログの結果を表示する":::

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL の[データイン レプリケーション](concepts-data-in-replication.md)について確認します。
