---
title: Azure Container Registry と Azure Kubernetes Service を統合する
description: Azure Kubernetes Service (AKS) と Azure Container Registry (ACR) を統合する方法について説明します。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62dd73e216b8b1ff1d9bb61210c90f8457b5a4fe
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105707"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service から Azure Container Registry の認証を受ける

Azure Kubernetes Service (AKS) で Azure Container Registry (ACR) を使用する場合は、認証メカニズムを確立する必要があります。 この操作は、必要なアクセス許可を ACR に付与することで、CLI、PowerShell、およびポータルのエクスペリエンスの一部として実装されます。 この記事では、これら 2 つの Azure サービス間の認証を構成する例を示します。

Azure CLI または Azure PowerShell を使用して、いくつかの単純なコマンドで AKS から ACR への統合を設定できます。 この統合により、AKS クラスターに関連付けられているマネージド ID に AcrPull ロールが割り当てられます。

> [!NOTE]
> この記事では、AKS と ACR の間の自動認証について説明します。 プライベート外部レジストリからイメージをプルする必要がある場合は、[イメージのプル シークレット][Image Pull Secret]を使用します。

## <a name="before-you-begin"></a>開始する前に

これらの例には以下のものが必要です。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* **Azure サブスクリプション** の **所有者**、**Azure アカウント管理者**、または **Azure 共同管理者** ロール
* Azure CLI バージョン 2.7.0 以降

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* **Azure サブスクリプション** の **所有者**、**Azure アカウント管理者**、または **Azure 共同管理者** ロール
* Azure PowerShell バージョン 5.9.0 以降

---

**所有者**、**Azure アカウント管理者**、または **Azure 共同管理者** ロールを必要としないようにするには、既存のマネージド ID を使用して AKS から ACR を認証します。 詳細については、「[Azure マネージド ID を使用して Azure コンテナー レジストリに対して認証する](../container-registry/container-registry-authentication-managed-identity.md)」を参照してください。

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR 統合を使用して新しい AKS クラスターを作成する

AKS クラスターの初期作成中に AKS と ACR の統合を設定できます。  AKS クラスターが ACR と対話できるようにするために、Azure Active Directory の **マネージド ID** が使用されます。 次のコマンドを使用すると、サブスクリプション内の既存の ACR を承認し、マネージド ID 用の適切な **ACRPull** ロールを構成できます。 下のパラメーターの有効な値を指定してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

または、ACR リソース ID を使用して ACR の名前を指定することもできます。その形式は次のようになります。

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> AKS クラスターから別のサブスクリプションにある ACR を使用している場合は、AKS クラスターから接続または接続解除するときに ACR リソース ID を使用します。

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR = 'myContainerRegistry'

# Run the following line to create an Azure Container Registry if you do not already have one
New-AzContainerRegistry -Name $MYACR -ResourceGroupName myContainerRegistryResourceGroup -Sku Basic

# Create an AKS cluster with ACR integration
New-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -GenerateSshKey -AcrNameToAttach $MYACR
```

---

この手順は、完了するまでに数分かかることがあります。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>既存の AKS クラスターに対する ACR 統合を構成する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

下の **acr-name** または **acr-resource-id** に有効な値を指定することによって、既存の ACR と既存の AKS クラスターを統合します。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

または、

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

> [!NOTE]
> `az aks update --attach-acr` を実行すると、コマンドを実行しているユーザーのアクセス許可を使用して、ACR 割り当てのロールが作成されます。 このロールは、kubelet マネージド ID に割り当てられます。 AKS マネージド ID の詳細については、「[マネージド ID の概要][summary-msi]」を参照してください。

次を使用して、ACR と AKS クラスター間の統合を削除することもできます。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

or

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

下の **acr-name** に有効な値を指定することによって、既存の ACR と既存の AKS クラスターを統合します。

```azurepowershell
Set-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -AcrNameToAttach <acr-name>
```

> [!NOTE]
> `Set-AzAksCluster -AcrNameToAttach` を実行すると、コマンドを実行しているユーザーのアクセス許可を使用して、ACR 割り当てのロールが作成されます。 このロールは、kubelet マネージド ID に割り当てられます。 AKS マネージド ID の詳細については、「[マネージド ID の概要][summary-msi]」を参照してください。

次を使用して、ACR と AKS クラスター間の統合を削除することもできます。

```azurepowershell
Set-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -AcrNameToDetach <acr-name>
```

---

## <a name="working-with-acr--aks"></a>ACR および AKS の操作

### <a name="import-an-image-into-your-acr"></a>イメージを ACR にインポートする

次を実行して、Docker Hub から ACR にイメージをインポートします。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName <acr-name> -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage library/nginx:latest
```

---

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>ACR から AKS にサンプル イメージをデプロイする

適切な AKS 資格情報を持っていることを確認します

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

---

次を含む **acr-nginx.yaml** という名前のファイルを作成します。 レジストリのリソース名の代わりに **acr-name** を使用します。 例: *myContainerRegistry*。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

次に、AKS クラスターでこのデプロイを実行します。

```console
kubectl apply -f acr-nginx.yaml
```

次を実行して、デプロイを監視できます。

```console
kubectl get pods
```

2 つのポッドが実行されているはずです。

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>トラブルシューティング
* [az aks check-acr](/cli/azure/aks#az_aks_check_acr) コマンドを実行して、レジストリに AKS クラスターからアクセスできることを確認します。
* ACR での監視の詳細については、[こちら](../container-registry/monitor-service.md)を参照してください
* ACR の正常性の詳細については、[こちら](../container-registry/container-registry-check-health.md)を参照してください。

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

[summary-msi]: use-managed-identity.md#summary-of-managed-identities
