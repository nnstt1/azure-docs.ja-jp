---
title: バックアップと復元 - Azure CLI - Azure Database for MySQL
description: Azure CLI を使用して Azure Database for MySQL サーバーをバックアップおよび復元する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f49ca9b65bca0459be2b262892fb272abc6d7227
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652430"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL サーバーのバックアップと復元を行う方法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL サーバーは、復元機能が有効になるように、バックアップが定期的に行われます。 この機能を使用して、新しいサーバー上で、サーバーとそのすべてのデータベースを過去の特定の時点に復元できます。

## <a name="prerequisites"></a>前提条件

この手順を実行するには、以下が必要です。

- [Azure Database for MySQL サーバーとデータベース](quickstart-create-mysql-server-database-using-azure-cli.md)が必要です。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="set-backup-configuration"></a>バックアップ構成の設定

サーバーの作成時に、ローカル冗長バックアップまたは地理冗長バックアップのどちらでサーバーを構成するかを選択します。 

> [!NOTE]
> サーバーの作成後は、冗長の種類 (地理冗長とローカル冗長) を切り替えることはできません。
>

`az mysql server create` コマンドでサーバーを作成するときに、`--geo-redundant-backup` パラメーターでバックアップの冗長オプションを指定します。 `Enabled` を指定すると、地理冗長バックアップが取得されます。 `Disabled` を指定すると、ローカル冗長バックアップが取得されます。 

バックアップのリテンション期間は、`--backup-retention` パラメーターで設定します。 

作成中のこれらの値の設定について詳しくは、[Azure Database for MySQL サーバーの CLI のクイック スタート](quickstart-create-mysql-server-database-using-azure-cli.md)に関するページをご覧ください。

サーバーのバックアップのリテンション期間は、次のようにして変更できます。

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

上記の例では、mydemoserver のバックアップ リテンション期間が 10 日に変更されます。

バックアップのリテンション期間によって、現在からどのくらい遡ってポイントインタイム リストアを取得できるかが管理されます。ポイントインタイム リストアは使用可能なバックアップに基づいているためです。 ポイントインタイム リストアについては、次のセクションで詳しく説明します。

## <a name="server-point-in-time-restore"></a>サーバーのポイントインタイム リストア
サーバーを以前の状態に復元できます。 復元されたデータは新しいサーバーにコピーされ、既存のサーバーはそのまま残されます。 たとえば、今日の正午にテーブルが誤って削除された場合、正午の直前に復元できます。 その後、不足しているテーブルとデータを、サーバーの復元されたコピーから取得できます。 

サーバーを復元するには、Azure CLI コマンド [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) を使用します。

### <a name="run-the-restore-command"></a>復元コマンドを実行する

サーバーを復元するには、Azure CLI コマンド プロンプトで、次のコマンドを入力します。

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` コマンドには、次のパラメーターが必要です。

| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  ソース サーバーが存在するリソース グループ。  |
| name | mydemoserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2018-03-13T13:59:00Z | 復元する特定の時点を選択します。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、`2018-03-13T05:59:00-08:00` など自身のローカル タイム ゾーンを使用できます。 また、`2018-03-13T13:59:00Z` など UTC Zulu 形式も使用できます。 |
| source-server | mydemoserver | 復元元のソース サーバーの名前または ID。 |

サーバーを過去の特定の時点に復元すると、新しいサーバーが作成されます。 特定の時点における元のサーバーとそのデータベースが新しいサーバーにコピーされます。

復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。 

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。 新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

さらに、復元操作の終了後には、復元操作後に既定値にリセットされる (およびプライマリ サーバーからコピーで上書きされない) 2 つのサーバー パラメーターがあります
*   time_zone - この値は、既定値 **SYSTEM** に設定されます
*   event_scheduler - event_scheduler は、復元されたサーバーで **OFF** に設定されます

プライマリ サーバーから値をコピーし、[サーバー パラメーター](howto-server-parameters.md)を再構成して、復元されたサーバーでこれを設定する必要があります

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 この新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="geo-restore"></a>geo リストア
地理冗長バックアップを使用するようにサーバーを構成した場合は、新しいサーバーをその既存のサーバーのバックアップから作成できます。 この新しいサーバーは、Azure Database for MySQL を使用できる任意のリージョンに作成できます。  

地理冗長バックアップを使ってサーバーを作成するには、Azure CLI の `az mysql server georestore` コマンドを使います。

> [!NOTE]
> サーバーが最初に作成された時点では、すぐには geo リストアで使用できない可能性があります。 必要なメタデータが設定されるまで数時間かかる場合があります。
>

サーバーを geo リストアするには、Azure CLI コマンド プロンプトで、次のコマンドを入力します。

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
このコマンドは、*myresourcegroup* に属する *mydemoserver-georestored* という名前の新しいサーバーを米国東部に作成します。 これは、8 個の仮想コアを備えた General Purpose Gen 5 サーバーです。 サーバーは *mydemoserver* の地理冗長バックアップ (これもリソース グループ *myresourcegroup* に含まれます) から作成されます

既存のサーバーとは異なるリソース グループに新しいサーバーを作成する場合は、`--source-server` パラメーターで、次の例のようにサーバー名を修飾します。

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mysql server georestore` コマンドには、次のパラメーターが必要です。

| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
|resource-group| myresourcegroup | 新しいサーバーが属するリソース グループの名前。|
|name | mydemoserver-georestored | 新しいサーバーの名前。 |
|source-server | mydemoserver | 地理冗長バックアップが使われる既存のサーバーの名前。 |
|location | eastus | 新しいサーバーの場所。 |
|sku-name| GP_Gen5_8 | このパラメーターは、新しいサーバーの価格レベル、コンピューティングの世代、および仮想コアの数を設定します。 GP_Gen5_8 は、8 つの仮想コアを備えた汎用 Gen 5 サーバーに対応します。|

geo リストアで新しいサーバーを作成すると、新しいサーバーは元のサーバーと同じストレージ サイズおよび価格レベルを継承します。 作成時にこれらの値を変更することはできません。 新しいサーバーを作成した後、ストレージ サイズをスケールアップすることはできます。

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。 新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 この新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="next-steps"></a>次のステップ
- サービスの[バックアップ](concepts-backup.md)の詳細について確認します
- [レプリカ](concepts-read-replicas.md)について確認します
- [ビジネス継続性](concepts-business-continuity.md)オプションについて確認します
