---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーのゾーン冗長による高可用性を構成する
description: この記事では、Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバーでゾーン冗長による高可用性を構成する方法について説明します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: c24871aff30c34398a07604749551bf53b428d36
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844096"
---
# <a name="configure-zone-redundant-high-availability-in-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバーのゾーン冗長による高可用性を構成する

このサンプルは、Azure Database for MySQL - フレキシブル サーバーで[ゾーン冗長による高可用性](../concepts-high-availability.md)を構成および管理する CLI スクリプトです。 ゾーン冗長による高可用性は、フレキシブル サーバーの作成時にのみ有効にでき、いつでも無効にできます。 プライマリとスタンバイのレプリカの可用性ゾーンを選択することもできます。 

現在、ゾーン冗長による高可用性は、汎用およびメモリ最適化の価格レベルでのみサポートされています。


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/zone-redundant-ha.sh?highlight=7,10-11,13-14 "Configure Zone-Redundant High Availability.")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースのホストとなるフレキシブル サーバーを作成します。|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|フレキシブル サーバーを更新します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。