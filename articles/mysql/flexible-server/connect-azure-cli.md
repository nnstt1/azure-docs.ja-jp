---
title: 'クイックスタート: Azure CLI を使用して接続する - Azure Database for MySQL - フレキシブル サーバー'
description: このクイックスタートでは、Azure CLI を使用して、Azure Database for MySQL - フレキシブル サーバーに接続するいくつかの方法を紹介します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: 26c25afc997ee86f0fe23f944ae5afad34269e92
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468121"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>クイックスタート: Azure CLI から Azure Database for MySQL - フレキシブル サーバーに接続してクエリを実行する

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

このクイックスタートでは、Azure CLI で ```az mysql flexible-server connect``` を使用して Azure Database for MySQL フレキシブル サーバーに接続し、```az mysql flexible-server execute``` コマンドで単一のクエリまたは SQL ファイルを実行する方法を示します。 このコマンドを使用すると、データベース サーバーとの接続をテストしたり、クエリを実行したりすることができます。 対話モードを使用して、複数のクエリを実行することもできます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli) の最新バージョン (2.20.0 以降) をインストールする
- Azure CLI を使用して ```az login``` コマンドでログインする
- ```az config param-persist on``` を使用してパラメーターの永続化を有効にする。 パラメーターの永続化により、リソース グループや場所など、多数の引数を繰り返さなくてもローカル コンテキストを使用できるようになります。

## <a name="create-a-mysql-flexible-server"></a>MySQL フレキシブル サーバーの作成

最初に作成するのは、マネージド MySQL サーバーです。 [Azure Cloud Shell](https://shell.azure.com/) から次のスクリプトを実行して、このコマンドから返された **サーバー名**、**ユーザー名**、**パスワード** をメモします。

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

このコマンドは、さらに引数を追加することでカスタマイズできます。 すべての引数については、[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create) を参照してください。

## <a name="create-a-database"></a>データベースを作成する
次のコマンドを実行して、**newdatabase** というデータベースを作成します (まだ作成していない場合)。

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>すべての引数を確認する
```--help``` 引数を指定すると、このコマンドのすべての引数を確認できます。

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>データベース サーバー接続をテストする
開発環境からデータベースへの接続をテストして検証するには、次のスクリプトを実行します。

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**例:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

接続に成功すると、次の出力が表示されます。

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
接続に失敗した場合は、これらの解決策を試してください。
- クライアント マシンでポート 3306 が開放されているかどうかを確認します。
- サーバー管理者のユーザー名とパスワードが正しいかどうかを確認します。
- クライアント マシンにファイアウォール規則が構成されているかどうかを確認します。
- 仮想ネットワークにプライベート アクセスを使用してサーバーを構成した場合、クライアント マシンが同じ仮想ネットワークに存在することを確認します。

## <a name="run-multiple-queries-using-interactive-mode"></a>対話モードを使用して複数のクエリを実行する
対話 (**interactive**) モードを使用して、複数のクエリを実行することができます。 対話モードを有効にするには、次のコマンドを実行します。

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**例:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

次に示すように、**MySQL** シェルのエクスペリエンスが表示されます。

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

## <a name="run-single-query"></a>単一のクエリを実行する
単一のクエリを実行するには、次のコマンドに ```--querytext``` 引数 (```-q```) を指定して実行します。

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**例:**
```azurecli
az mysql flexible-server execute -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

出力は次のようになります。

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-sql-file"></a>SQL ファイルを実行する
SQL ファイルを実行するには、コマンドで ```--file-path``` 引数 (```-q```) を使用します。

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --file-path "<file-path>"
```

**例:**
```azurecli
az mysql flexible-server execute -n mysqldemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -f "./test.sql"
```

出力は次のようになります。

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Running sql file '.\test.sql'...
Successfully executed the file.
Closed the connection to mysqldemoserver.
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
* [暗号化された接続を使用して Azure Database for MySQL - フレキシブル サーバーに接続する](how-to-connect-tls-ssl.md)
* [サーバーを管理する](./how-to-manage-server-cli.md)

