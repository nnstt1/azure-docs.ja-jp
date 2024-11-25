---
title: Azure Functions のための Azure CLI サンプル | Microsoft Docs
description: Azure CLI を使用する Azure Functions 用の Bash スクリプトへのリンクを見つけます。 統合とデプロイを可能にする関数アプリを作成する方法について説明します。
ms.assetid: 577d2f13-de4d-40d2-9dfc-86ecc79f3ab0
ms.topic: sample
ms.date: 09/17/2021
ms.custom: mvc, devx-track-azurecli, seo-azure-cli
keywords: 関数, Azure CLI サンプル, Azure CLI の例, Azure CLI のコード サンプル
ms.openlocfilehash: 8f6f5e45be61746cf33bf79e144403631892da9d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618706"
---
# <a name="azure-cli-samples"></a>Azure CLI のサンプル

次の表には、Azure CLI を使用する Azure Functions 用の Bash スクリプトへのリンクが含まれています。

<a id="create"></a>

| アプリを作成する | 説明 |
|---|---|
| [サーバーレス実行用の Function App の作成](scripts/functions-cli-create-serverless.md) | 従量課金プランで関数アプリを作成します。  |
| [サーバーレス Python 関数アプリの作成](scripts/functions-cli-create-serverless-python.md) | 従量課金プランで Python 関数アプリを作成します。 |
| [スケーラブル Premium プランでの関数アプリの作成](scripts/functions-cli-create-premium-plan.md) | Premium プランで関数アプリを作成します。 |
| [専用 (App Service) プランでの関数アプリの作成](scripts/functions-cli-create-app-service-plan.md) | 専用の App Service プランで関数アプリを作成します。 |

| 統合 | 説明|
|---|---|
| [Function App の作成とストレージ アカウントへの接続](scripts/functions-cli-create-function-app-connect-to-storage-account.md) | Function App を作成し、ストレージ アカウントに接続します。 |
| [Function App の作成と Azure Cosmos DB への接続](scripts/functions-cli-create-function-app-connect-to-cosmos-db.md) | 関数アプリを作成し Azure Cosmos DB に接続します。 |
| [Python 関数アプリの作成と Azure Files 共有へのマウント](scripts/functions-cli-mount-files-storage-linux.md) | Linux 関数アプリに共有をマウントすることにより、既存の機械学習モデルや、関数内のその他のデータを活用できます。 | 

| 継続的なデプロイ | 説明|
|---|---|
| [GitHub からのデプロイ](scripts/functions-cli-create-function-app-github-continuous.md) | GitHub リポジトリからデプロイする関数アプリを作成します。  |
| [Azure DevOps からのデプロイ](scripts/functions-cli-create-function-app-vsts-continuous.md) | Azure DevOps リポジトリからデプロイする関数アプリを作成します。  |
