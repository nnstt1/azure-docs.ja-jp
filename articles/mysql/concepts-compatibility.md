---
title: ドライバーとツールの互換性 - Azure Database for MySQL
description: この記事では、Azure Database for MySQL との、MySQL ドライバーと管理ツールの互換性について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/4/2021
ms.openlocfilehash: 367591067f8d3d5fa0ed2b8cab45cad0c5fb6015
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724750"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Azure Database for MySQL に対する MySQL ドライバーと管理ツールの互換性

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

この記事では、Azure Database for MySQL Single Server との、ドライバーと管理ツールの互換性について説明します。

> [!NOTE]
> この記事は、ドライバーが Single Server サービスの[接続アーキテクチャ](concepts-connectivity-architecture.md)と互換性があることを確認する方法を説明しており、Azure Database for MySQL Single Server にのみ適用されます。 [Azure Database for MySQL フレキシブル サーバー](./flexible-server/overview.md)は、MySQL Community Edition と互換性がある、サポート対象のすべてのドライバーとツールと互換性があります。

## <a name="mysql-drivers"></a>MySQL ドライバー
Azure Database for MySQL では、MySQL データベースの世界で最も人気のある Community Edition を使用しています。 そのため、さまざまな種類のプログラミング言語とドライバーと互換性があります。 目標は、MySQL ドライバーの 3 つの最新バージョンと、常に MySQL ドライバーの機能と使いやすさを向上させ続けているオープン ソース コミュニティの作成者の努力をサポートすることです。 テストによって Azure Database for MySQL 5.6 および 5.7 と互換性のあることがわかっているドライバーの一覧を次の表に示します。

> [!WARNING]
> MySQL 8.0.27 クライアントは Azure Database for MySQL - 単一サーバーと互換性がありません。 mysql.exe またはワークベンチ経由で作成された MySQL 8.0.27 クライアントからのすべての接続は失敗します。 回避策として、代わりに以前のバージョンのクライアント (MySQL 8.0.27 より前) を使用するか、[Azure Database for MySQL - フレキシブル サーバー](./flexible-server/overview.md)のインスタンスを作成することを検討してください。

| **プログラミング言語** | **[ドライバー]** | **リンク** | **互換性のあるバージョン** | **互換性のないバージョン** | **メモ** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli、pdo_mysql、mysqlnd | https://secure.php.net/downloads.php | 5.5、5.6、7.x | 5.3 | SSL MySQLi との PHP 7.0 接続では、接続文字列に MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT を追加します。 <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO の設定: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` オプションを false に設定します。|
| .NET | .NET 用の非同期 MySQL コネクタ | https://github.com/mysql-net/MySqlConnector <br> [NuGet からのインストール パッケージ](https://www.nuget.org/packages/MySqlConnector/) | 0.27 以降 | 0.26.5 以前 | |
| .NET | MySQL Connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3、7.0、8.0 |  | 一部の非 UTF8 Windows システムでは、エンコードのバグが原因で、接続できない場合があります。 |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> NPM のインストール パッケージ:<br> NPM から `npm install mysql` を実行 | 2.15 | 2.14.1 以前 | |
| Node.js | node-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 以降 | | |
| Go | Go MySQL ドライバー | https://github.com/go-sql-driver/mysql/releases | 1.3、1.4 | 1.2 以前 | バージョン 1.3 では接続文字列で `allowNativePasswords=true` を使用します。 バージョン 1.4 には修正プログラムが含まれているため、`allowNativePasswords=true` は不要になりました。 |
| Python | MySQL コネクタ/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3、2.0、2.1、2.2、8.0.16 以降と MySQL 8.0 を使用  | 1.2.2 以前 | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11、0.8.0、0.8.1、0.9.3 以降 | 0.9.0 ～ 0.9.2 (web2py での回帰) | |
| Java | MariaDB コネクタ/J | https://downloads.mariadb.org/connector-java/ | 2.1、2.0、1.6 | 1.5.5 以前 | | 
| Java | MySQL コネクタ/J | https://github.com/mysql/mysql-connector-j | 5.1.21 以降、8.0.17 以降と MySQL 8.0 を使用 | 5.1.20 以下 | |
| C | MySQL コネクタ/C (libmysqlclient) | https://dev.mysql.com/doc/c-api/5.7/en/c-api-implementations.html | 6.0.2 以降 | | |
| C | MySQL コネクタ/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 以降 | | |
| C++ | MySQL コネクタ/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 以降 | 1.1.3 以下 | | 
| C++ | MySQL++| https://github.com/tangentsoft/mysqlpp | 3.2.3 以降 | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 以降 | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 以降 | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0.7.2 以降 | | |
| Swift | vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1 以降 | | |

## <a name="management-tools"></a>管理ツール
互換性の利点は、データベース管理ツールにも拡張されています。 データベース操作がユーザーのアクセス許可の範囲内で動作する限り、既存のツールは引き続き Azure Database for MySQL でも機能します。 テストによって Azure Database for MySQL 5.6 および 5.7 と互換性のあることがわかっている 3 つの一般的なデータベース管理ツールの一覧を次の表に示します。

|                                     | **MySQL Workbench 6.x 以降** | **Navicat 12** | **PHPMyAdmin 4.x 以降** | **dbForge Studio for MySQL 9.0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **作成、更新、読み取り、書き込み、削除** | X | X | X | X |
| **SSL 接続** | X | X | X | X |
| **SQL クエリのオート コンプリート** | X | X |  | X |
| **データのインポートとエクスポート** | X | X | X | X |
| **複数の形式へのエクスポート** | X | X | X | X |
| **バックアップと復元** |  | X |  | X |
| **サーバー パラメーターの表示** | X | X | X | X |
| **クライアント接続の表示** | X | X | X | X |

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL への接続に関する問題のトラブルシューティング](howto-troubleshoot-common-connection-issues.md)