---
title: Azure CLI:単一データベースを作成する
description: この Azure CLI サンプル スクリプトを使用して単一データベースを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 76f7d0314e382508c6290e80db4960addbc15c24
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110688962"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Azure CLI を使用して単一データベースを作成し、ファイアウォール規則を構成する

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この Azure CLI サンプル スクリプトは、Azure SQL Database に単一データベースを作成し、サーバーレベルのファイアウォール規則を構成します。 スクリプトが正常に実行されると、すべての Azure サービスおよび構成済み IP アドレスからデータベースにアクセスできるようになります。

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | 説明 |
|---|---|
| [az sql server](/cli/azure/sql/server#az_sql_server_create) | サーバーのコマンド。 |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | サーバーのファイアウォール コマンド。 |
| [az sql db](/cli/azure/sql/db#az_sql_db_create) | データベースのコマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../az-cli-script-samples-content-guide.md)のページにあります。
