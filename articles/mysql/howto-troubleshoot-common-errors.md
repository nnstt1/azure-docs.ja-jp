---
title: 一般的なエラーのトラブルシューティング - Azure Database for MySQL
description: Azure Database for MySQL サービスの新しいユーザーが遭遇する一般的な移行エラーのトラブルシューティングの方法について説明します
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 5/21/2021
ms.openlocfilehash: e66940b74110e5870acfffb255e7de4942ad1b71
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453404"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL への移行中または移行後によく発生するエラー

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL は、MySQL のコミュニティ バージョンを利用したフル マネージド サービスです。 マネージド サービス環境での MySQL エクスペリエンスは、独自の環境で MySQL を実行する場合と異なることがあります。 この記事では、初めて Azure Database for MySQL への移行や開発を行うときにユーザーが遭遇する可能性のある一般的なエラーについて説明します。

## <a name="common-connection-errors"></a>一般的な接続エラー

### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>ERROR 1184 (08S01): Aborted connection 22 to db: 'db-name' user: 'user' host: 'hostIP' (init_connect command failed) (エラー 1184 (08S01): db への接続 22 を中止: 'db-name' ユーザー: 'user' ホスト: 'hostIP' (init_connect コマンドに失敗しました))

上記のエラーは、サインインに成功後、セッションが確立されたとき、いずれかのコマンドを実行する前に発生します。 上記のメッセージは、`init_connect` サーバー パラメーターに設定した値に誤りがあり、それが原因でセッションの初期化に失敗していることを示しています。

セッション レベルではサポートされないサーバー パラメーター (`require_secure_transport` など) がいくつかあります。そのため、MySQL サーバーへの接続時に、`init_connect` を使用してそれらのパラメーターを値を変更しようとすると、エラー 1184 が発生します。以下にその例を示します。

mysql> show databases; ERROR 2006 (HY000):MySQL server has gone away No connection. Trying to reconnect...Connection id:    64897 Current database: *** NONE *** ERROR 1184 (08S01): Aborted connection 22 to db: 'db-name' user: 'user' host: 'hostIP' (init_connect command failed)

**解決方法**: Azure portal の [サーバー パラメーター] タブで `init_connect` の値をリセットし、サポートされているサーバー パラメーターのみを init_connect パラメーターで設定する必要があります。

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>SUPER 権限と DBA ロールの欠如によるエラー

SUPER 権限と DBA ロールはサービスでサポートされていません。 その結果、次のような一般的なエラーが発生する可能性があります。

### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERROR 1419 (エラー 1419):  (SUPER 権限を持っておらず、バイナリ ログが有効になっています (より安全度の低い log_bin_trust_function_creators 変数を使用 "*することもできます*"))

上記のエラーは、以下のような関数およびトリガーの作成中、またはスキーマのインポート中に発生する可能性があります。 CREATE FUNCTION や CREATE TRIGGER などの DDL ステートメントはバイナリ ログに書き込まれるため、セカンダリ レプリカでそれらを実行することができます。 レプリカ SQL スレッドには完全な特権があり、これを利用して特権を昇格できます。 バイナリ ログが有効になっているサーバーのこの危険を防ぐため、MySQL エンジンでは、ストアド関数の作成者が通常の CREATE ROUTINE 特権に加えて SUPER 特権を保有していることが求められます。

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**解決方法**: このエラーを解決するには、ポータルの [[サーバー パラメーター]](howto-server-parameters.md) ブレードで `log_bin_trust_function_creators` を 1 に設定するか、DDL ステートメントを実行するか、またはスキーマをインポートして目的のオブジェクトを作成します。 サーバーの `log_bin_trust_function_creators` を 1 のままにすることで、今後このエラーの発生を防ぐことができます。 Microsoft では、`log_bin_trust_function_creators` を設定することをお勧めします。bin ログが脅威にさらされずに済むため、[MySQL コミュニティのドキュメント](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)で大きく取り上げられている Azure Database for MySQL サービスのセキュリティ リスクが最小限で済みます。

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERROR 1227 (42000) at line 101 (エラー 1227 (42000)、行 101): Access denied; you need (at least one of) the SUPER privilege(s) for this operation. (アクセスが拒否されました。この操作には、(少なくとも 1 つの) SUPER 権限が必要です。) Operation failed with exitcode 1 (終了コード 1 で操作に失敗しました)

上記のエラーは、ダンプ ファイルのインポート中、または [definer](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html) を含むプロシージャの作成中に発生する可能性があります。

**解決方法**:このエラーを解決するために、管理者ユーザーは、次の例のように GRANT コマンドを実行して、プロシージャを作成または実行する権限を付与できます。

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```

または、次に示すように、definer を、インポート プロセスを実行している管理者ユーザーの名前に置き換えることもできます。

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>ERROR 1227 (42000) at line 295 (エラー 1227 (42000)、行 295): Access denied; you need (at least one of) the SUPER or SET_USER_ID privilege(s) for this operation (アクセスが拒否されました。この操作には、(少なくとも 1 つの) SUPER または SET_USER_ID 特権が必要です)

上記のエラーは、ダンプ ファイルのインポートまたはスクリプトの実行の一環として CREATE VIEW with DEFINER ステートメントを実行しているときに、発生する可能性があります。 Azure Database for MySQL では、SUPER 特権または SET_USER_ID 特権はどのユーザーに対しても許可されません。

**解決方法**:

* 可能であれば、definer ユーザーを使用して CREATE VIEW を実行します。 異なるアクセス許可を持つさまざまな definer によるビューが多数存在する可能性があるため、これは実現できない場合があります。  OR
* ダンプ ファイルまたは CREATE VIEW スクリプトを編集し、ダンプ ファイルから DEFINER= ステートメントを削除します 
* ダンプ ファイルまたは CREATE VIEW スクリプトを編集し、definer の値を、スクリプト ファイルのインポートまたは実行を行っている、管理者アクセス許可を持つユーザーで置き換えます。

> [!Tip]
> DEFINER= ステートメントを置き換えるには、sed または perl を使用してダンプ ファイルまたは SQL スクリプトを変更します

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>ERROR 1227 (42000) at line 18 (エラー 1227 (42000)、行 18): Access denied; you need (at least one of) the SUPER privilege(s) for this operation. (アクセスが拒否されました。この操作には、(少なくとも 1 つの) SUPER 権限が必要です。)

GTID が有効にされている MySQL サーバーからターゲット Azure Database for MySQL サーバーにダンプ ファイルをインポートしようとすると、上記のエラーが発生することがあります。 Mysqldump では、GTID が使用されているサーバーからのダンプ ファイルに SET @@SESSION.sql_log_bin=0 ステートメントが追加されます。これによって、ダンプ ファイルがリロードされている間のバイナリ ログが無効になります。

**解決方法**: インポート中のこのエラーを解決するには、万全を期すために、mysqldump ファイルにある以下の行を削除するかコメント アウトしてから、再度インポートを実行してください。

SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN; SET @@SESSION.SQL_LOG_BIN= 0; SET @@GLOBAL.GTID_PURGED=''; SET @@SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN;

## <a name="common-connection-errors-for-server-admin-sign-in"></a>サーバー管理者サインインの一般的な接続エラー

Azure Database for MySQL サーバーが作成されると、サーバー管理者サインインは、サーバーの作成時にエンド ユーザーによって提供されます。 サーバー管理者サインインを使用すると、新しいデータベースの作成、新しいユーザーの追加、アクセス許可の付与を行うことができます。 サーバー管理者サインインが削除された場合、そのアクセス許可が取り消された場合、またはパスワードが変更された場合、接続中にアプリケーションで接続エラーが表示され始める可能性があります。 一般的なエラーの一部を次に示します

### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>ERROR 1045 (28000) (エラー 1045 (28000)): Access denied for user "username"@"IP address" (using password: YES) (次のユーザーのアクセスが拒否されました: "username"@"IP address" (使用したパスワード:Yes))

上記のエラーは、次の場合に発生します。

* username が存在しません。
* ユーザー username が削除されました。
* パスワードが変更またはリセットされている。

**解決方法**:

* サーバーに有効なユーザーとして "username" が存在するか、または誤って削除されていないかを確認します。 Azure Database for MySQL ユーザーにログインすることで、次のクエリを実行できます。

  ```sql
  select user from mysql.user;
  ```

* MySQL にサインインして上記のクエリ自体を実行できない場合は、[Azure portal を使用して管理者パスワードをリセットする](howto-create-manage-server-portal.md)ことをお勧めします。 Azure portal の [パスワードのリセット] オプションを使用すると、ユーザーの再作成、パスワードのリセット、管理者のアクセス許可の復元を実行できます。これにより、サーバー管理者を使用してサインインし、追加の操作を実行することができます。

## <a name="next-steps"></a>次のステップ

探している回答が見つからない場合は、次のオプションを検討してください。

* [Microsoft Q&A 質問ページ](/answers/topics/azure-database-mysql.html)または [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql) で質問を投稿する。
* Azure Database for MySQL チーム [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com) に電子メールを送信する。 このメール アドレスはテクニカル サポートのエイリアスではありません。
* Azure サポートに連絡し、[Azure portal からチケットを申請する](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
* フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0) でエントリを作成します。
