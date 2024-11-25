---
title: Kubernetes on Azure のチュートリアル - アプリケーションの更新
description: この Azure Kubernetes Service (AKS) チュートリアルでは、AKS にデプロイされている既存のアプリケーションを新しいバージョンのアプリケーション コードで更新する方法について説明します。
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: eaa7f6b0f99a856ea9210be3fdb6d2de1dd87d2a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697949"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションの更新

Kubernetes でアプリケーションをデプロイした後で、新しいコンテナー イメージまたはイメージ バージョンを指定することによってアプリケーションを更新できます。 更新は、デプロイの全体が一度に更新されないように、段階的に行われます。 この段階的な更新プログラムを使用すると、アプリケーションの更新中も引き続きアプリケーションを実行することができます。 デプロイ エラーが発生した場合のロールバック メカニズムも提供されています。

この 7 部構成の 6 番目のチュートリアルでは、サンプルの Azure Vote アプリを更新します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * フロントエンド アプリケーションのコードを更新する
> * 更新後のコンテナー イメージを作成する
> * Azure Container Registry にコンテナー イメージをプッシュする
> * 更新したコンテナー イメージをデプロイする

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化しました。 このイメージを Azure Container Registry にアップロードし、AKS クラスターを作成しました。 その後、AKS クラスターにアプリケーションをデプロイしました。

アプリケーション リポジトリも複製しましたが、それにはアプリケーションのソース コードと、このチュートリアルで使用する事前作成された Docker Compose ファイルが含まれています。 リポジトリの複製を作成したこと、およびディレクトリを複製ディレクトリに変更したことを確認します。 これらの手順を行っておらず、順番に進めたい場合は、[チュートリアル 1 – コンテナー イメージを作成する][aks-tutorial-prepare-app]に関するページから始めてください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

このチュートリアルでは、Azure PowerShell バージョン 5.9.0 以降を実行している必要があります。 バージョンを確認するには、`Get-InstalledModule -Name Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell ][azure-powershell-install]のインストールに関するページをご覧ください。

---

## <a name="update-an-application"></a>アプリケーションを更新する

サンプル アプリケーションに変更を加えたうえで、AKS クラスターにデプロイ済みのバージョンを更新してみましょう。 複製された *azure-voting-app-redis* ディレクトリにいることを確認します。 その場合、サンプル アプリケーションのソース コードは、*azure-vote* ディレクトリ内にあります。 `vi` などのエディターで *config_file.cfg* ファイルを開きます。

```console
vi azure-vote/azure-vote/config_file.cfg
```

*VOTE1VALUE* と *VOTE2VALUE* の値を、色などの別の値に変更します。 次の例は、更新後の値を示しています。

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

ファイルを保存して閉じます。 `vi` では、`:wq` を使用します。

## <a name="update-the-container-image"></a>コンテナー イメージを更新する

フロントエンド イメージを再作成し、更新したアプリケーションをテストするには、[docker-compose][docker-compose] を使用します。 引数 `--build` は、Docker Compose にアプリケーション イメージの再作成を指示するために使用されます。

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>ローカルでアプリケーションをテストする

更新後のコンテナー イメージに変更内容が反映されていることを確認するために、ローカル Web ブラウザーで `http://localhost:8080` を開きます。

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated.png" alt-text="ローカル Web ブラウザーで開かれた、ローカルで実行されている Azure 投票アプリの更新されたコンテナー イメージの例を示すスクリーンショット":::

実行中のアプリケーションに、*config_file.cfg* ファイルで指定した更新後の値が表示されます。

## <a name="tag-and-push-the-image"></a>イメージにタグを付けてプッシュする

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

更新したイメージを正しく使用するために、*azure-vote-front* イメージに ACR レジストリのログイン サーバー名のタグを付けます。 [az acr list](/cli/azure/acr) コマンドを使用して、ログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

更新したイメージを正しく使用するために、*azure-vote-front* イメージに ACR レジストリのログイン サーバー名のタグを付けます。 [Get-AzContainerRegistry][get-azcontainerregistry] コマンドレットを使用して、ログイン サーバー名を取得します。

```azurepowershell
(Get-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrName>).LoginServer
```

---


[docker tag][docker-tag] を使用してイメージにタグを付けます。 `<acrLoginServer>` を実際の ACR ログイン サーバー名またはパブリック レジストリのホスト名に置き換え、イメージのバージョンを次のように *:v2* に更新します。

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v2
```

次に、[docker push][docker-push] を使用してご利用のレジストリにイメージをアップロードします。 `<acrLoginServer>` は、実際の ACR ログイン サーバー名に置き換えてください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> ACR レジストリにプッシュする際に問題が発生する場合は、まだログインしていることを確認してください。 「[Azure Container Registry を作成する](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry)」手順で作成した Azure Container Registry の名前を使用して [az acr login][az-acr-login] コマンドを実行します。 たとえば、「 `az acr login --name <azure container registry name>` 」のように入力します。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!NOTE]
> ACR レジストリにプッシュする際に問題が発生する場合は、まだログインしていることを確認してください。 「[Azure Container Registry を作成する](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry)」手順で作成した Azure Container Registry の名前を使用して [Connect-AzContainerRegistry][connect-azcontainerregistry] コマンドレットを実行します。 たとえば、「 `Connect-AzContainerRegistry -Name <azure container registry name>` 」のように入力します。

---

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>更新したアプリケーションをデプロイする

最大限のアップタイムを提供するには、アプリケーション ポッドの複数のインスタンスを実行する必要があります。 実行中のフロントエンド インスタンスの数を [kubectl get pods][kubectl-get] コマンドを使用して確認します。

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

複数のフロントエンド ポッドがない場合は、次のように *azure-vote-front* デプロイをスケーリングします。

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

アプリケーションを更新するには、[kubectl set][kubectl-set] コマンドを使用します。 実際のコンテナー レジストリのログイン サーバー名またはホスト名で `<acrLoginServer>` を更新し、アプリケーション バージョンとして *v2* を指定します。

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

デプロイを監視するには、[kubectl get pod][kubectl-get] コマンドを使います。 更新されたアプリケーションがデプロイされると、ポッドが終了されて、新しいコンテナー イメージで再作成されます。

```console
kubectl get pods
```

次の出力例には、デプロイの進行状況として、終了状態のポッドと実行状態の新しいインスタンスが示されています。

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>更新したアプリケーションをテストする

更新したアプリケーションを表示するには、まず、`azure-vote-front` サービスの外部 IP アドレスを取得します。

```console
kubectl get service azure-vote-front
```

次に、Web ブラウザーを開いて、サービスの IP アドレスに移動します。

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated-external.png" alt-text="ローカル Web ブラウザーで開かれた、AKS クラスターで実行されている Azure 投票アプリの更新されたイメージの例を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーションを更新し、この更新を AKS クラスターにロールアウトしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * フロントエンド アプリケーションのコードを更新する
> * 更新後のコンテナー イメージを作成する
> * Azure Container Registry にコンテナー イメージをプッシュする
> * 更新したコンテナー イメージをデプロイする

次のチュートリアルに進んで、AKS クラスターを新しいバージョンの Kubernetes にアップグレードする方法を学習してください。

> [!div class="nextstepaction"]
> [Kubernetes のアップグレード][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-powershell-install]: /powershell/azure/install-az-ps
[get-azcontainerregistry]: /powershell/module/az.containerregistry/get-azcontainerregistry
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
