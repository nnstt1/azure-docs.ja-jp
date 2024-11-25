---
title: クイックスタート - ポータルでのレジストリの作成
<<<<<<< HEAD
description: Azure portal を使用してプライベート Azure Container Registry を作成する方法について簡単に説明します。
ms.date: 08/04/2020
=======
description: Azure portal を使用してプライベート Azure コンテナー レジストリを作成する方法について簡単に説明します。
ms.date: 06/23/2021
>>>>>>> repo_sync_working_branch
ms.topic: quickstart
ms.custom: mvc, mode-portal, contperf-fy21q4
ms.openlocfilehash: 8ac194e2343120dea1daec688bf383162846bdfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043549"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure Container Registry を作成する

Azure Container Registry は、コンテナー イメージおよび関連アーティクルのビルド、保管、管理をするための、プライベート レジストリ サービスです。 このクイックスタートでは、Azure portal を使用して Azure コンテナー レジストリ インスタンスを作成します。 次に、Docker コマンドを使用してコンテナー イメージをレジストリにプッシュし、最後にレジストリからイメージをプルして実行します。

レジストリにログインし、コンテナー イメージを操作するために、このクイック スタートでは、Azure CLI を実行している必要があります (バージョン 2.0.55 以降を推奨)。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

**[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="ポータルでコンテナー レジストリに移動する":::

**[基本]** タブで **[リソース グループ]** と **[レジストリ名]** の値を入力します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このクイック スタートでは、`myResourceGroup` という名前の新しいリソース グループを `West US` に作成し、 **[SKU]** には [Basic] を選択します。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="ポータルでコンテナー レジストリを作成する":::

残りの設定は既定値のままにします。 次に、 **[Review + create]\(確認と作成\)** を選択します。 設定を確認したら **[作成]** を選択します。

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

**"デプロイメントに成功しました"** というメッセージが表示されたら、ポータルでコンテナー レジストリを選択します。 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="ポータルでのコンテナー レジストリの概要":::

レジストリ名と **ログイン サーバー** の値をメモします。これは、Azure クラウドの `azurecr.io` で終わる完全修飾名です。 後続の手順で Docker でイメージをプッシュし、プルするとき、これらの値を使用します。

## <a name="log-in-to-registry"></a>レジストリへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめレジストリ インスタンスにログインしておく必要があります。 ローカル コンピューターで [Azure CLI にサインイン][get-started-with-azure-cli]し、[az acr login][az-acr-login] コマンドを実行します。 Azure CLI でログインする際は、レジストリ リソース名のみを指定します。 完全修飾ログイン サーバー名は使用しません。

```azurecli
az acr login --name <registry-name>
```

例:

```azurecli
az acr login --name mycontainerregistry
```

このコマンドは、完了すると `Login Succeeded` を返します。 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>コンテナー イメージの一覧表示

レジストリ内のイメージを一覧表示するには、ポータルでレジストリに移動し、 **[リポジトリ]** を選択してから、`docker push` で作成した **hello-world** リポジトリを選択します。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="ポータルでコンテナー イメージを一覧表示する":::

**hello-world** リポジトリを選択することによって、 **[タグ]** に `v1` タグが付いたイメージが表示されます。

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースをクリーンアップするには、ポータルで **myResourceGroup** リソース グループに移動します。 リソース グループが読み込まれたら、 **[リソース グループの削除]** をクリックして、リソース グループ、コンテナー レジストリ、およびそこに格納されているコンテナー イメージを削除します。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="ポータルでリソース グループを削除する":::


## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure portal を使って Azure Container Registry を作成し、コンテナー イメージをプッシュしてから、レジストリからイメージをプルして実行しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry タスクのチュートリアル][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
