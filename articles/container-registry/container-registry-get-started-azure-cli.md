---
title: クイックスタート - レジストリの作成 - Azure CLI
description: Azure CLI を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: ba0bb7ec21a26603db261a949566186e3c4469e7
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894250"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用したプライベート コンテナー レジストリの作成

Azure Container Registry は、コンテナー イメージおよび関連アーティクルのビルド、保管、管理をするための、プライベート レジストリ サービスです。 このクイックスタートでは、Azure CLI を使用して Azure コンテナー レジストリ インスタンスを作成します。 次に、Docker コマンドを使用してコンテナー イメージをレジストリにプッシュし、最後にレジストリからイメージをプルして実行します。

このクイック スタートでは、Azure CLI を実行している必要があります (バージョン 2.0.55 以降を推奨)。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[macOS][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、必要な Docker コンポーネント (`dockerd` デーモン) すべてが含まれていないため、このクイックスタートで Cloud Shell を使用することはできません。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このクイック スタートでは、*Basic* レジストリを作成します。これは、Azure Container Registry について学習している開発者にとって、コストが最適なオプションです。 利用可能なサービス レベルの詳細については、[コンテナー レジストリのサービス レベル][container-registry-skus]に関するページを参照してください。

[az acr create][az-acr-create] コマンドを使用して ACR インスタンスを作成します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、*myContainerRegistry007* を使用します。 これを一意の値に更新します。

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

レジストリが作成されると、出力は次のようになります。

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

出力の `loginServer` をメモしておいてください。これは、完全修飾レジストリ名です (すべて小文字)。 以降このクイックスタートでは、コンテナー レジストリ名のプレースホルダーとして `<registry-name>` を、レジストリのログイン サーバー名のプレースホルダーとして `<login-server>` を使用します。

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

## <a name="log-in-to-registry"></a>レジストリへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめレジストリにログインしておく必要があります。 そのためには、[az acr login][az-acr-login] コマンドを使用します。 Azure CLI でログインする際は、レジストリ リソース名のみを指定します。 完全修飾ログイン サーバー名は使用しません。 

```azurecli
az acr login --name <registry-name>
```

例:

```azurecli
az acr login --name mycontainerregistry
```

このコマンドが完了すると、`Login Succeeded` というメッセージが返されます。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>コンテナー イメージの一覧表示

次の例は、レジストリ内のリポジトリを一覧表示します。

```azurecli
az acr repository list --name <registry-name> --output table
```

出力:

```
Result
----------------
hello-world
```

次の例は、**hello-world** リポジトリのタグを一覧表示します。

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

出力:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、コンテナー レジストリ、そこに格納されているコンテナー イメージを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure CLI を使って Azure Container Registry を作成し、レジストリにコンテナー イメージをプッシュしてから、レジストリからイメージをプルして実行しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry タスクのチュートリアル][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
