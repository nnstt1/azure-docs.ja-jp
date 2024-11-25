---
title: Azure Kubernetes Service (AKS) で Ultra Disk のサポートを有効にする
description: Azure Kubernetes Service (AKS) クラスターで Ultra Disks を有効化し、構成する方法について説明します
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 07d43fc72d10f1739fb67124feadacc9632206be
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984860"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service"></a>Azure Kubernetes Service での Azure Ultra Disks の使用

[Azure Ultra Disks](../virtual-machines/disks-enable-ultra-ssd.md) は、ステートフル アプリケーションに高スループット、高 IOPS、および一貫性のある低待機時間のディスク ストレージを提供します。 Ultra Disks の主なメリットの 1 つが、エージェント ノードを再起動することなく、SSD のパフォーマンスをワークロードと共に動的に変更する機能です。 Ultra Disks は、データ量の多いワークロードに適しています。

## <a name="before-you-begin"></a>開始する前に

この機能は、クラスターの作成時またはノード プールの作成時にのみ設定できます。

> [!IMPORTANT]
> Azure Ultra Disks では、特定の VM シリーズだけでなく、これらのディスクをサポートする可用性ゾーンとリージョンにデプロイされたノードプールが必要です。 [**Ultra Disks の GA の範囲と制限事項**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)に関する説明を参照してください。

### <a name="limitations"></a>制限事項
- [**Ultra Disks の GA の範囲と制限事項**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)に関する説明を参照してください
- Ultra Disks でサポートされているサイズの範囲は 100 から 1500 です

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Ultra Disks を使用できる新しいクラスターを作成する

次の CLI コマンドを使用して、Ultra Disks を活用できる AKS クラスターを作成します。 `--enable-ultra-ssd` フラグを使用して `EnableUltraSSD` 機能を設定します。

Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ultra Disks をサポートする AKS クラスターを作成します。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_D2s_v3 --zones 1 2 --node-count 2 --enable-ultra-ssd
```

Ultra Disks のサポートを使用せずにクラスターを作成するには、`--enable-ultra-ssd` パラメーターを省略します。

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>既存のクラスターでの Ultra Disks の有効化

新しいノード プールを Ultra Disks がサポートされるクラスターに追加することによって、既存のクラスターで Ultra Disks を有効にできます。 `--enable-ultra-ssd` フラグを使用して、Ultra ディスクを使用するように新しいノード プールを構成します。

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_D2s_v3 --zones 1 2 --node-count 2 --enable-ultra-ssd
```

Ultra Disks のサポートを使用せずに新しいノード プールを作成するには、`--enable-ultra-ssd` パラメーターを省略します。

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>ストレージ クラスで Ultra Disks を動的に使用する

デプロイまたはステートフル セットで Ultra Disks を使用するには、[動的プロビジョニングにストレージ クラス](azure-disks-dynamic-pv.md)を使用できます。

### <a name="create-the-storage-class"></a>ストレージ クラスの作成

ストレージ クラスは、保存の単位を永続ボリュームを使用して動的に作成する方法を定義します。 Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

この例では、Ultra Disks を参照するストレージ クラスを作成します。 `azure-ultra-disk-sc.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成し、*azure-ultra-disk-sc.yaml* ファイルを指定します。

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) を使用して、ストレージ クラスに基づいてストレージを自動的にプロビジョニングします。 この場合、PVC は以前に作成したストレージ クラスを使用して、Ultra Disks を作成できます。

`azure-ultra-disk-pvc.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。 クレームでは、サイズが *1000 GB* で *ReadWriteOnce* アクセスがある `ultra-disk` という名前のディスクを要求します。 ストレージ クラスとして、*ultra-disk-sc* ストレージ クラスが指定されます。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

[kubectl apply][kubectl-apply] コマンドを使用して、*azure-ultra-disk-pvc.yaml* ファイルを指定することで、永続ボリューム クレームを作成します。

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>永続ボリュームの使用

永続ボリューム要求が作成され、ディスクが正常にプロビジョニングされると、ディスクへのアクセスを使ってポッドを作成できます。 次のマニフェストは、*ultra-disk* という名前の永続ボリュームクレームを使って `/mnt/azure` パスに Azure ディスクをマウントする基本的な NGINX ポッドを作成します。

`nginx-ultra.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

次の例に示すように [kubectl apply][kubectl-apply] コマンドを使用してポッドを作成します。

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

これで Azure ディスクが `/mnt/azure` ディレクトリにマウントされ、ポッドが稼働状態となりました。 次の例に示すように、`kubectl describe pod nginx-ultra` でポッドを検査するときに、この構成を確認できます。

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>次のステップ

- Ultra Disks の詳細については、「[Azure Ultra ディスクの使用](../virtual-machines/disks-enable-ultra-ssd.md)」を参照してください。
- ストレージのベスト プラクティスの詳細については、「[Azure Kubernetes Services (AKS) のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]」を参照してください

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
