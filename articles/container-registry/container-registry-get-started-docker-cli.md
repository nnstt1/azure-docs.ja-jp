---
title: コンテナー イメージのプッシュとプル
description: Docker CLI を使用した、Azure のプライベート コンテナー レジストリに対する Docker イメージのプッシュとプル
ms.topic: article
ms.date: 05/12/2021
ms.custom: seodec18, H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 0fd44ae001bd7f120b6c903a4109dd0e6268e19e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062958"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Docker CLI を使用した、Azure のコンテナー レジストリへの最初のイメージのプッシュ

[Docker Hub](https://hub.docker.com/) で公開 Docker コンテナー イメージを格納するように、Azure Container Registry ではプライベート コンテナー イメージとその他の成果物を格納および管理します。 コンテナー レジストリに対する[ログイン](https://docs.docker.com/engine/reference/commandline/login/)、[プッシュ](https://docs.docker.com/engine/reference/commandline/push/)、[プル](https://docs.docker.com/engine/reference/commandline/pull/)などのコンテナー イメージ操作には、[Docker コマンド ライン インターフェイス](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) を使用できます。

以下の手順では、公開 [Nginx イメージ](https://store.docker.com/images/nginx)をダウンロードし、プライベート Azure Container Registry にタグ付けしてレジストリにプッシュした後、レジストリからもう一度プルします。

## <a name="prerequisites"></a>前提条件

* **Azure Container Registry** - コンテナー レジストリは、Azure サブスクリプションに作成します。 たとえば、[Azure portal](container-registry-get-started-portal.md)、[Azure CLI](container-registry-get-started-azure-cli.md)、または [Azure PowerShell](container-registry-get-started-powershell.md) を使用します。
* **Docker CLI** - Docker もローカルにインストールする必要があります。 Docker では、[macOS][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="log-in-to-a-registry"></a>レジストリへのログイン

プライベート コンテナー レジストリで[認証するさまざまな方法](container-registry-authentication.md)があります。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

コマンド ラインで作業するときに推奨される方法は、Azure CLI コマンドの [az acr login](/cli/azure/acr#az_acr_login)を使用することです。 たとえば、*myregistry* という名前のレジストリにログインするには、Azure CLI にログインし、レジストリに対して認証を行います。

```azurecli
az login
az acr login --name myregistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

PowerShell で作業する場合に推奨される方法は、Azure PowerShell のコマンドレット [Connect-AzContainerRegistry](/powershell/module/az.containerregistry/connect-azcontainerregistry) を使用することです。 たとえば、*myregistry* という名前のレジストリにログインするには、Azure にログインし、レジストリに対して認証を行います。

```azurepowershell
Connect-AzAccount
Connect-AzContainerRegistry -Name myregistry
```

---

[docker login](https://docs.docker.com/engine/reference/commandline/login/) でログインすることもできます。 たとえば、オートメーション シナリオで、[レジストリにサービス プリンシパルを割り当てる](container-registry-authentication.md#service-principal)ことができます。 次のコマンドを実行するときは、サービス プリンシパルの appID (ユーザー名) とパスワードの入力を求められたら、対話形式で入力します。 ログイン資格情報の管理のベスト プラクティスについては、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

```
docker login myregistry.azurecr.io
```

どちらのコマンドも、完了すると `Login Succeeded` が返されます。
> [!NOTE]
>* Docker 拡張機能を備えた Visual Studio Code を使用すると、ログインがより高速かつ便利になります。

> [!TIP]
> `docker login` を使用する場合とレジストリにプッシュするために画像にタグ付けする場合は、常にレジストリの完全修飾名 (すべて小文字) を指定してください。 この記事の例では、完全修飾名は *myregistry.azurecr.io* です。

## <a name="pull-a-public-nginx-image"></a>公開 Nginx イメージをプルする

まず、公開 Nginx イメージをローカル コンピューターにプルします。 この例では、Microsoft Container Registry からイメージをプルします。

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

次の [docker run](https://docs.docker.com/engine/reference/run/) コマンドを実行して、Nginx コンテナーのローカル インスタンスを 対話形式でポート 8080 で起動します (`-it`)。 `--rm` 引数は、コンテナーが停止されたときに、それを削除するように指定します。

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

`http://localhost:8080` に移動して、実行中のコンテナーの Nginx によって提供される既定の Web ページを表示します。 次のようなページが表示されます。

![ローカル コンピューター上の Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

`-it` を指定して対話形式でコンテナーを開始したため、ブラウザーでそれに移動すると、コマンド ラインに Nginx サーバーの出力が表示されます。

コンテナーを停止して削除するには、`Control`+`C` を押します。

## <a name="create-an-alias-of-the-image"></a>イメージのエイリアスを作成する

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) でレジストリへの完全修飾パスを使用して、イメージのエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、`samples` 名前空間を指定しています。

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

名前空間を持つタグ付けの詳細については、「[Azure Container Registry のベスト プラクティス](container-registry-best-practices.md)」の「[リポジトリの名前空間](container-registry-best-practices.md#repository-namespaces)」セクションを参照してください。

## <a name="push-the-image-to-your-registry"></a>イメージをレジストリにプッシュする

これで、完全修飾パスを使用してイメージにプライベート レジストリへのタグが付けられたので、[docker push](https://docs.docker.com/engine/reference/commandline/push/) を使用してレジストリにプッシュできます。

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>レジストリからイメージをプルする

レジストリからイメージをプルするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Nginx コンテナーを起動する

レジストリからプルしたイメージを実行するには、[docker run](https://docs.docker.com/engine/reference/run/) コマンドを使用します。

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

ブラウザーで `http://localhost:8080` に移動して、実行中のコンテナーを表示します。

コンテナーを停止して削除するには、`Control`+`C` を押します。

## <a name="remove-the-image-optional"></a>イメージを削除する (任意指定)

Nginx イメージが不要になった場合は、[docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) コマンドを使用して、ローカルに削除できます。

```
docker rmi myregistry.azurecr.io/samples/nginx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Container Registry からイメージを削除するには、Azure CLI コマンド [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete) を使用できます。 たとえば、次のコマンドは、`samples/nginx:latest` タグによって参照されるマニフェスト、固有のレイヤー データ、およびそのマニフェストを参照するその他すべてのタグを削除します。

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Az.ContainerRegistry](/powershell/module/az.containerregistry) Azure PowerShell モジュールには、コンテナー インスタンスからイメージを削除するための複数のコマンドが含まれています。 [Remove-AzContainerRegistryRepository](/powershell/module/az.containerregistry/remove-azcontainerregistryrepository) を使用すると、特定の名前空間 (`samples:nginx` など) のすべてのイメージが削除され、[Remove-AzContainerRegistryManifest](/powershell/module/az.containerregistry/remove-azcontainerregistrymanifest) を使用すると、特定のタグまたはマニフェストが削除されます。

次の例では、`Remove-AzContainerRegistryRepository` コマンドレットを使用して、`samples:nginx` 名前空間のすべてのイメージを削除します。

```azurepowershell
Remove-AzContainerRegistryRepository -RegistryName myregistry -Name samples/nginx
```

次の例では、`Remove-AzContainerRegistryManifest` コマンドレットを使用して、`samples/nginx:latest` タグによって参照されるマニフェスト、固有のレイヤー データ、およびそのマニフェストを参照するその他すべてのタグを削除します。

```azurepowershell
Remove-AzContainerRegistryManifest -RegistryName myregistry -RepositoryName samples/nginx -Tag latest
```

---

## <a name="next-steps"></a>次のステップ

基本を理解したので、レジストリの使用を開始する準備ができました。 たとえば、レジストリから次の宛先にコンテナー イメージをデプロイします。

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Azure Container Registry を操作するには、必要に応じて [Visual Studio Code 用の Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)と [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能をインストールします。 Azure Container Registry との間でイメージをプルおよびプッシュしたり、ACR タスクを実行したりします。すべて Visual Studio Code 内で実行します。


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
