---
title: Azure CLI のサンプル スクリプト - Batch アカウントの作成 - ユーザー サブスクリプション | Microsoft Docs
description: ユーザー サブスクリプション モードで Azure Batch アカウントを作成する方法について説明します。 このアカウントを使うと、サブスクリプションにコンピューティング ノードを割り当てられます。
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: バッチ, azure cli サンプル, azure cli の例, azure cli のコード サンプル
ms.openlocfilehash: 087ba199787e36dccba58f37dd1ebb83c715d729
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658661"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI の例: ユーザー サブスクリプション モードでの Batch アカウントの作成

このスクリプトでは、ユーザー サブスクリプション モードで Azure Batch アカウントを作成します。 サブスクリプションにコンピューティング ノードを割り当てるアカウントは、Azure Active Directory トークンを使用して認証される必要があります。 割り当てられたコンピューティング ノードは、サブスクリプションの vCPU (コア) クォータに対してカウントされます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。  

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az role assignment create](/cli/azure/role) | ユーザー、グループ、またはサービス プリンシパルに対して、新しいロール割り当てを作成します。 |
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | Key Vault を作成します。 |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | 指定した Key Vault のセキュリティ ポリシーを更新します。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch アカウントを作成します。  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
