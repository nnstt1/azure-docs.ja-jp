---
title: Kubernetes on Azure のチュートリアル - コンテナー レジストリを作成する
description: この Azure Kubernetes Service (AKS) チュートリアルでは、Azure Container Registry インスタンスを作成し、サンプルのアプリケーション コンテナー イメージをアップロードします。
services: container-service
ms.topic: tutorial
ms.date: 05/20/2021
ms.custom: mvc, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 157aae551f4fa77c4739670081804af1b8dbf16b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697762"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>チュートリアル: Azure Container Registry をデプロイして使用する

Azure Container Registry (ACR) は、コンテナー イメージ用のプライベート レジストリです。 プライベート コンテナー レジストリを使用すると、アプリケーションとカスタム コードを安全にビルドおよびデプロイすることができます。 7 つのパートのうちの 2 番目のこのチュートリアルでは、ACR インスタンスをデプロイして、それにコンテナー イメージをプッシュします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry (ACR) インスタンスを作成する
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする
> * レジストリ内のイメージを表示する

後続のチュートリアルでは、この ACR インスタンスが AKS の Kubernetes クラスターと統合され、アプリケーションがイメージからデプロイされます。

## <a name="before-you-begin"></a>開始する前に

[前のチュートリアル][aks-tutorial-prepare-app]では、単純な Azure Voting アプリケーション用のコンテナー イメージを作成しました。 Azure Voting アプリのイメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成][aks-tutorial-prepare-app]に関するページに戻ってください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

このチュートリアルでは、Azure PowerShell バージョン 5.9.0 以降を実行している必要があります。 バージョンを確認するには、`Get-InstalledModule -Name Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell ][azure-powershell-install]のインストールに関するページをご覧ください。

---

## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

Azure Container Registry を作成するには、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create][az-acr-create] コマンドを使用して Azure Container Registry インスタンスを作成し、独自のレジストリ名を指定します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このチュートリアルの残りの部分では、コンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。 独自の一意のレジストリ名を指定します。 *Basic* SKU は、ストレージとスループットのバランスが取れた、開発目的のコスト最適化されたエントリ ポイントです。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[New-AzResourceGroup][new-azresourcegroup] コマンドレットでリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

[New-AzContainerRegistry][new-azcontainerregistry] コマンドレットを使用して Azure Container Registry インスタンスを作成し、独自のレジストリ名を指定します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このチュートリアルの残りの部分では、コンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。 独自の一意のレジストリ名を指定します。 *Basic* SKU は、ストレージとスループットのバランスが取れた、開発目的のコスト最適化されたエントリ ポイントです。

```azurepowershell
New-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrname> -Sku Basic
```

---

## <a name="log-in-to-the-container-registry"></a>コンテナー レジストリにログインする

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ACR インスタンスを使用するには、まずログインする必要があります。 [az acr login][az-acr-login] コマンドを使用して、前の手順でコンテナー レジストリに設定した一意の名前を指定します。

```azurecli
az acr login --name <acrName>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

ACR インスタンスを使用するには、まずログインする必要があります。 [Connect-AzContainerRegistry][connect-azcontainerregistry] コマンドレットを使用して、前の手順でコンテナー レジストリに設定した一意の名前を指定します。

```azurepowershell
Connect-AzContainerRegistry -Name <acrName>
```

---

このコマンドは、完了すると *"Login Succeeded (ログインに成功しました)"* というメッセージを返します。

## <a name="tag-a-container-image"></a>コンテナー イメージにタグを付ける

現在のローカル イメージの一覧を表示するには、[docker images][docker-images] コマンドを使用します。

```console
docker images
```
上記のコマンドの出力では、現在のローカル イメージのリストが示されます。

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

ACR で *azure-vote-front* コンテナー イメージを使用するには、イメージにレジストリのログイン サーバー アドレスでタグを付ける必要があります。 このタグは、イメージ レジストリにコンテナー イメージをプッシュするときに、ルーティングするために使用されます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ログイン サーバー アドレスを取得するには、[az acr list][az-acr-list] コマンドを使用し、*loginServer* に対して次のようにクエリを実行します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

ログイン サーバー アドレスを取得するには、[Get-AzContainerRegistry][get-azcontainerregistry] コマンドレットを使用し、*loginServer* に対して次のようにクエリを実行します。

```azurepowershell
(Get-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrName>).LoginServer
```

---

ここで、ローカルの *azure-vote-front* イメージに、コンテナー レジストリの *acrLoginServer* アドレスでタグを付けます。 イメージのバージョンを示すには、イメージ名の最後に *:v1* を追加します。

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

タグが適用されたことを確認するには、[docker イメージ][docker-images]を再実行します。

```console
docker images
```

イメージに ACR インスタンスのアドレスとバージョン番号でタグが付けられています。

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>イメージをレジストリにプッシュ

ビルドしてタグ付けしたイメージと共に、*azure-vote-front* イメージを ACR インスタンスにプッシュします。 [docker push][docker-push] を使用して、次のように、イメージ名に独自の *acrLoginServer* アドレスを指定します。

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

ACR へのイメージのプッシュが完了するまでに、数分かかることがあります。

## <a name="list-images-in-registry"></a>レジストリ内のイメージの一覧表示

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ご利用の ACR インスタンスにプッシュされたイメージの一覧を返すには、[az acr repository list][az-acr-repository-list] コマンドを使用します。 実際の `<acrName>` を次のように指定します。

```azurecli
az acr repository list --name <acrName> --output table
```

次の出力例では、レジストリ内で利用可能なイメージとして *azure-vote-front* を示しています。

```output
Result
----------------
azure-vote-front
```

特定のイメージのタグを表示するには、次のように [az acr repository show-tags][az-acr-repository-show-tags] コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

次の出力例は、前の手順でタグを付けた *v1* イメージを示しています。

```output
Result
--------
v1
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

ご利用の ACR インスタンスにプッシュされたイメージの一覧を返すには、[Get-AzContainerRegistryManifest][get-azcontainerregistrymanifest] コマンドレットを使用します。 実際の `<acrName>` を次のように指定します。

```azurepowershell
Get-AzContainerRegistryManifest -RegistryName <acrName> -RepositoryName azure-vote-front
```

次の出力例では、レジストリ内で利用可能なイメージとして *azure-vote-front* を示しています。

```output
Registry  ImageName        ManifestsAttributes
--------  ---------        -------------------
<acrName> azure-vote-front {Microsoft.Azure.Commands.ContainerRegistry.Models.PSManifestAttributeBase}
```

特定のイメージのタグを表示するには、次のように [Get-AzContainerRegistryTag][get-azcontainerregistrytag] コマンドレットを使用します。

```azurepowershell
Get-AzContainerRegistryTag -RegistryName <acrName> -RepositoryName azure-vote-front
```

次の出力例は、前の手順でタグを付けた *v1* イメージを示しています。

```output
Registry  ImageName        Tags
--------  ---------        ----
<acrName> azure-vote-front {v1}
```

---

これで、プライベートの Azure Container Registry インスタンスに格納されているコンテナー イメージができました。 このイメージは、次のチュートリアルで、ACR から Kubernetes クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Registry を作成し、AKS クラスターで使用するためのイメージをプッシュしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure Container Registry (ACR) インスタンスを作成する
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする
> * レジストリ内のイメージを表示する

Azure に Kubernetes クラスターをデプロイする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Kubernetes クラスターのデプロイ][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-list]: /cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azcontainerregistry]: /powershell/module/az.containerregistry/new-azcontainerregistry
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
[get-azcontainerregistry]: /powershell/module/az.containerregistry/get-azcontainerregistry
[get-azcontainerregistrymanifest]: /powershell/module/az.containerregistry/get-azcontainerregistrymanifest
[get-azcontainerregistrytag]: /powershell/module/az.containerregistry/get-azcontainerregistrytag
