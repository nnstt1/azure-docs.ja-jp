---
title: 仮想ネットワークを使用して推論環境をセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning 推論環境をセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/05/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: df790ee9480333b806ff1903d5e81d81a83177cd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723006"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>仮想ネットワークを使用して Azure Machine Learning 推論環境をセキュリティで保護する

この記事では、Azure Machine Learning で仮想ネットワークを使用して、推論環境をセキュリティで保護する方法について説明します。

> [!TIP]
> この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。
>
> * [Virtual Network の概要](how-to-network-security-overview.md)
> * [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
> * [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
> * [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
> * [カスタム DNS を使用する](how-to-custom-dns.md)
> * [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)

この記事では、仮想ネットワークで次の推論リソースをセキュリティで保護する方法について説明します。
> [!div class="checklist"]
> - 既定の Azure Kubernetes Service (AKS) クラスター
> - プライベート AKS クラスター
> - プライベート リンクを使用する AKS クラスター
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>前提条件

+ 一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについては、[ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照してください。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure ロールベースのアクセス制御 (Azure RBAC) で次のアクションへのアクセス許可を保持している必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください

## <a name="limitations"></a>制限事項

### <a name="azure-container-instances"></a>Azure Container Instances

* 仮想ネットワークで Azure Container Instances を使用する場合、仮想ネットワークは、Azure Machine Learning ワークスペースと同じリソース グループに含まれている必要があります。 それ以外の場合、仮想ネットワークは異なるリソース グループ内に配置することができます。
* ワークスペースに __プライベート エンドポイント__ がある場合、Azure Container Instances に使用される仮想ネットワークは、ワークスペースのプライベート エンドポイントで使用されているものと同じである必要があります。
* 仮想ネットワーク内で Azure Container Instances を使用する場合、ご使用のワークスペースの Azure Container Registry (ACR) をその仮想ネットワーク内に配置することはできません。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

* ワークスペースに __プライベート エンドポイント__ がある場合、Azure Kubernetes Service クラスターは、ワークスペースと同じ Azure リージョンに存在する必要があります。
* [パブリックの完全修飾ドメイン名 (FQDN) とプライベート AKS クラスター](../aks/private-clusters.md#create-a-private-aks-cluster-with-a-public-fqdn)を使用することは Azure Machine Learning では __サポートされていません__。

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

> [!IMPORTANT]
> 仮想ネットワークで AKS クラスターを使用するには、最初に「[Azure Kubernetes サービス (AKS) における高度なネットワークの構成](../aks/configure-azure-cni.md#prerequisites)」の前提条件に従います。


仮想ネットワーク内の AKS をワークスペースに追加するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインし、お使いのサブスクリプションとワークスペースを選択します。
1. 左側の __[Compute]\(コンピューティング\)__ を選択し、中央の __[Inference clusters]\(推論クラスター\)__ を選択して、 __[+ 新規]__ を選択します。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference.png" alt-text="[推論クラスターの作成] ダイアログのスクリーンショット":::

1. __[推論クラスターの作成]__ ダイアログから __[新規作成]__ を選択し、クラスターに使用する VM サイズを選択します。 最後に、 __[次へ]__ を選択します。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference-vm.png" alt-text="VM 設定のスクリーンショット":::

1. __[Configure Settings]\(構成の設定\)__ セクションで __[Compute name]\(コンピューティング名\)__ を入力し、 __[Cluster Purpose]\(クラスターの目的\)__ 、 __[ノード数]__ 、 __[Advanced]\(詳細\)__ の順に選択してネットワークの設定を表示します。 __[仮想ネットワークの構成]__ 領域で、次の値を設定します。

    * 使用する __仮想ネットワーク__ を設定します。

        > [!TIP]
        > ワークスペースがプライベート エンドポイントを使用して仮想ネットワークに接続する場合、 __[仮想ネットワーク]__ 選択フィールドが灰色表示されます。

    * クラスターの作成先となる __[サブネット]__ を設定します。
    * __[Kubernetes サービスのアドレス範囲]__ フィールドに、Kubernetes サービスのアドレス範囲を入力します。 このアドレス範囲は、Classless Inter-Domain Routing (CIDR) 表記の IP 範囲を使用して、クラスターで使用できる IP アドレスを定義します。 どのサブネットの IP 範囲とも重複していてはなりません (例: 10.0.0.0/16)。
    * __[Kubernetes DNS サービスの IP アドレス]__ フィールドに、Kubernetes DNS サービスの IP アドレスを入力します。 この IP アドレスは Kubernetes DNS サービスに割り当てられます。 Kubernetes サービスのアドレス範囲内に含まれるアドレスを指定する必要があります (例: 10.0.0.10)。
    * __[Docker ブリッジ アドレス]__ フィールドに、Docker ブリッジ アドレスを入力します。 この IP アドレスは Docker ブリッジに割り当てられます。 サブネットの IP 範囲または Kubernetes サービスのアドレス範囲内にすることはできません (例: 172.18.0.1/16)。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference-settings.png" alt-text="ネットワーク設定の構成のスクリーンショット":::

1. Web サービスとしてのモデルを AKS にデプロイすると、推論要求を処理するスコアリング エンドポイントが作成されます。 仮想ネットワークを制御するネットワーク セキュリティ グループ (NSG) に、スコアリング エンドポイントの IP アドレスに対して有効になっているインバウンド セキュリティ規則があることを確認します (仮想ネットワークの外部から呼び出す場合)。

    スコアリング エンドポイントの IP アドレスを確認するには、デプロイされたサービスのスコアリング URI を確認します。 スコアリング URI の表示の詳細については、[Web サービスとしてデプロイされたモデルを使用する](how-to-consume-web-service.md#connection-information)ことに関する記事をご覧ください。

   > [!IMPORTANT]
   > NSG に対しては既定のアウトバウンド規則のままにします。 詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md#default-security-rules)」の既定のセキュリティ規則をご覧ください。

   [![インバウンド セキュリティ規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > スコアリング エンドポイントのイメージに表示される IP アドレスは、デプロイによって異なります。 1 つの AKS クラスターに対して同じ IP がすべてのデプロイで共有されますが、各 AKS クラスターには異なる IP アドレスが割り当てられます。

また、Azure Machine Learning SDK を使用して仮想ネットワークに Azure Kubernetes Service を追加することもできます。 仮想ネットワークに既に AKS クラスターがある場合は、[AKS にデプロイする方法](how-to-deploy-and-where.md)に関するページで説明されているように、ワークスペースにアタッチすることができます。 次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい AKS インスタンスが作成されます。

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

作成プロセスが完了すると、仮想ネットワークの背後にある AKS クラスターで推論 (モデルのスコアリング) を実行できるようになります。 詳細については、[AKS へのデプロイ方法](how-to-deploy-and-where.md)に関するページをご覧ください。

Kubernetes でロールベースのアクセス制御を使用する方法の詳細については、「[Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)」を参照してください。

## <a name="network-contributor-role"></a>ネットワーク共同作成者ロール

> [!IMPORTANT]
> 前に作成した仮想ネットワークを提供して AKS クラスターを作成またはアタッチする場合は、AKS クラスターのサービス プリンシパル (SP) またはマネージド ID に、仮想ネットワークを含むリソース グループに対する _ネットワーク共同作成者_ ロールを付与する必要があります。
>
> ネットワーク共同作成者として ID を追加するには、次の手順に従います。

1. AKS のサービス プリンシパルまたはマネージド ID を検索するには、次の Azure CLI コマンドを使用します。 `<aks-cluster-name>` をクラスターの名前に置き換えます。 `<resource-group-name>` を、_AKS クラスターが含まれている_ リソース グループの名前に置き換えます。

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    このコマンドによって `msi` の値が返された場合は、次のコマンドを使用して、マネージド ID のプリンシパル ID を識別します。

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 仮想ネットワークが含まれているリソース グループの ID を検索するには、次のコマンドを使用します。 `<resource-group-name>` を、_仮想ネットワークが含まれている_ リソース グループの名前に置き換えます。

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. ネットワーク共同作成者としてサービス プリンシパルまたはマネージド ID を追加するには、次のコマンドを使用します。 `<SP-or-managed-identity>` を、サービス プリンシパルまたはマネージド ID 用に返された ID に置き換えます。 `<resource-group-id>` を、仮想ネットワークが含まれているリソース グループ用に返された ID に置き換えます。

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS での内部ロードバランサーの使用の詳細については、「[Azure Kubernetes Service (AKS) で内部ロード バランサーを使用する](../aks/internal-lb.md)」を参照してください。

## <a name="secure-vnet-traffic"></a>VNet トラフィックをセキュリティ保護する

AKS クラスターと仮想ネットワークの間のトラフィックを分離するには、次の 2 つの方法があります。

* __プライベート AKS クラスター__: この方法では、Azure Private Link を使用して、デプロイまたは管理操作用のクラスターとの通信をセキュリティで保護します。
* __内部 AKS ロード バランサー__: この方法では、AKS へのデプロイのエンドポイントが、仮想ネットワーク内でプライベート IP を使用するように構成します。

### <a name="private-aks-cluster"></a>プライベート AKS クラスター

既定では AKS クラスターには、パブリック IP アドレスを持つコントロール プレーンまたは API サーバーがあります。 プライベート AKS クラスターを作成することによって、プライベート コントロール プレーンを使用するように AKS を構成できます。 詳細については、「[プライベート Azure Kubernetes Service クラスターを作成する](../aks/private-clusters.md)」を参照してください。

プライベート AKS クラスターを作成したら、Azure Machine Learning で使用する[仮想ネットワークにクラスターをアタッチします](how-to-create-attach-kubernetes.md)。

### <a name="internal-aks-load-balancer"></a>内部 AKS ロード バランサー

既定では、AKS のデプロイでは[パブリック ロード バランサー](../aks/load-balancer-standard.md)が使用されます。 このセクションでは、内部ロード バランサーを使用するように AKS を構成する方法について説明します。 内部 (プライベート) ロード バランサーは、プライベート IP のみがフロントエンドとして許可される場合に使用されます。 内部ロード バランサーは、仮想ネットワーク内でトラフィックを負荷分散させるために使用されます

プライベート ロード バランサーを有効にするには、"_内部ロード バランサー_" を使用するように AKS を構成します。 

#### <a name="enable-private-load-balancer"></a>プライベート ロード バランサーを有効にする

> [!IMPORTANT]
> Azure Machine Learning スタジオで Azure Kubernetes Service クラスターを作成しているときに、プライベート IP を有効にすることはできません。 Python SDK を使用する場合、または機械学習用の Azure CLI 拡張機能を使用する場合は、内部ロード バランサーを使用して作成できます。

次の例では、SDK と CLI を使用して、__プライベート IP または内部ロード バランサーで新しい AKS クラスターを作成する__ 方法を示します。

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

内部ロード バランサーを使用するように既存の AKS クラスターをアップグレードするには、次のコマンドを使用します。

```azurecli
az ml computetarget update aks \
                           -n myaks \
                           --load-balancer-subnet mysubnet \
                           --load-balancer-type InternalLoadBalancer \
                           --workspace-name myworkspace \
                           -g myresourcegroup
```


詳細については、[az ml computetarget create aks](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_aks) と [az ml computetarget update aks](/cli/azure/ml(v1)/computetarget/update#az_ml_computetarget_update_aks) のリファレンスを参照してください。

---

ワークスペースに __既存のクラスターを接続する__ 場合は、アタッチ操作が完了するまで待ってから、ロード バランサーを構成する必要があります。 クラスターのアタッチの詳細については、「[既存の AKS クラスターをアタッチする](how-to-create-attach-kubernetes.md)」を参照してください。

既存のクラスターをアタッチした後、内部ロード バランサーまたはプライベート IP を使用するようにクラスターを更新できます。

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Azure Container Instances (ACI) を有効にする

Azure Container Instances は、モデルのデプロイ時に動的に作成されます。 Azure Machine Learning で仮想ネットワーク内に ACI を作成できるようにするには、デプロイで使用されるサブネットに対して __サブネットの委任__ を有効にする必要があります。 ワークスペースに対する仮想ネットワークで ACI を使用するには、次の手順のようにします。

1. 仮想ネットワークでサブネットの委任を有効にするには、「[サブネットの委任を追加または削除する](../virtual-network/manage-subnet-delegation.md)」の記事に書かれている情報を使用します。 仮想ネットワークを作成するときに委任を有効にすることも、既存のネットワークに委任を追加することもできます。

    > [!IMPORTANT]
    > 委任を有効にする場合は、 __[サブネットをサービスに委任]__ の値として `Microsoft.ContainerInstance/containerGroups` を使用します。

2. [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) を使用してモデルをデプロイします。`vnet_name` パラメーターと `subnet_name` パラメーターを使用します。 これらのパラメーターには、委任を有効にした仮想ネットワークの名前とサブネットを設定します。

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a> 仮想ネットワークからのアウトバウンド接続を制限する

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限する場合は、Azure Container Registry へのアクセスを許可する必要があります。 たとえば、ネットワーク セキュリティ グループ (NSG) に __AzureContainerRegistry.RegionName__ サービス タグへのアクセスを許可する規則が含まれていることを確認してください。ここで、`{RegionName} は Azure リージョンの名前です。

## <a name="next-steps"></a>次のステップ

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [Virtual Network の概要](how-to-network-security-overview.md)
* [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)