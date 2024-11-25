---
title: 'CLI: Cosmos DB にアプリを接続する'
description: Azure CLI を使用して App Service アプリのデプロイと管理を自動化する方法について説明します。 このサンプルでは、MongoDB (Cosmos DB) にアプリを接続する方法を紹介しています。
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 6beaafe19184e9c7b27c4e533f20c023948e9209
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736120"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>CLI を使った App Service アプリと Cosmos DB の接続

このサンプル スクリプトでは、Azure Cosmos DB の MongoDB 用 API と App Service アプリを使用して、Azure Cosmos DB アカウントを作成します。 その後、アプリの設定を使用して、MongoDB 接続文字列を Web アプリにリンクします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、App Service アプリ、Cosmos DB、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service アプリを作成します。 |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) | Cosmos DB アカウントを作成します。 |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | 指定された Cosmos DB アカウントの接続文字列を一覧表示します。 |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | App Service アプリのアプリ設定を作成または更新します。 アプリ設定は、アプリの環境変数として公開されます (「[環境変数とアプリ設定のリファレンス](../reference-app-settings.md)」を参照)。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
