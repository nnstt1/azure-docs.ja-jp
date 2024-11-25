---
title: サーバーの管理 - Azure CLI - Azure Database for MySQL
description: Azure CLI から Azure Database for MySQL サーバーを管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 51e0913404fd0cf853eaeae39a91f648ece17341
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653321"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL サーバーを管理する

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

この記事では、Azure でデプロイされた単一サーバーを管理する方法を示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/reference-index#az_login) コマンドを使用して、アカウントにログインする必要があります。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 コマンドの **subscription** 引数の値として使用する、**az login** 出力の **id** 値をメモしておきます。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](/cli/azure/account#az_account_list) を使用します。

```azurecli
az account set --subscription <subscription id>
```

まだサーバーを作成していない場合は、この[クイックスタート](quickstart-create-mysql-server-database-using-azure-cli.md)を参照して作成してください。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング
次のコマンドを使用して、価格レベル、コンピューティング、ストレージを簡単にスケールアップできます。 [az mysql server overview](/cli/azure/mysql/server) で、実行できるすべてのサーバー操作を表示できます

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

上記の引数の詳細を次に示します。

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | Azure Database for MySQL サーバーの一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
sku-name|GP_Gen5_2|価格レベルとコンピューティング構成の名前を入力します。 省略表現の {価格レベル} _{コンピューティング世代}_ {仮想コア} という規則に従います。 詳細については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。
storage-size | 6144 | サーバーのストレージ容量 (単位はメガバイト)。 最小値は 5120 で、1024 ずつ増加します。

> [!Important]
> - ストレージをスケールアップすることはできますが、ストレージをスケールダウンすることはできません
> - Basic から汎用またはメモリ最適化への価格レベルのスケールアップはサポートされていません。 [Bash スクリプトを使用](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)するか、または [MySQL Workbench を使用](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)して、手動でスケールアップできます


## <a name="manage-mysql-databases-on-a-server"></a>サーバーで MySQL データベースを管理する
これらのコマンドのいずれかを使用して、サーバー上のデータベースのデータベース プロパティの作成、削除、一覧表示、表示を行うことができます

| コマンドレット | 使用法| 説明 |
| --- | ---| --- |
|[az mysql db create](/cli/azure/sql/db#az_mysql_db_create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |データベースを作成します。|
|[az mysql db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|サーバーからデータベースを削除します。 このコマンドでは、サーバーは削除されません。 |
|[az mysql db list](/cli/azure/sql/db#az_mysql_db_list)|```az mysql db list -g myresourcegroup -s mydemoserver```|サーバー上のすべてのデータベースの一覧を表示します|
|[az mysql db show](/cli/azure/sql/db#az_mysql_db_show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|データベースの詳細を表示します|

## <a name="update-admin-password"></a>管理パスワードの更新
このコマンドで、管理者ロールのパスワードを変更できます
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  パスワードは、8 文字以上 128 文字以下にしてください。
> パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。

## <a name="delete-a-server"></a>サーバーの削除
MySQL 単一サーバーを削除するだけの場合は、[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) コマンドを実行します。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ
- [サーバーを再起動する](howto-restart-server-cli.md)
- [正常な状態でないサーバーを復元する](howto-restore-server-cli.md)
- [サーバーを監視して調整する](concepts-monitoring.md)