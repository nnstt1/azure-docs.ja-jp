---
title: Azure Arc 対応 SQL Managed Instance を削除する
description: Azure Arc 対応 SQL Managed Instance を削除する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 19f5befde22ed7b16302b7da5df313c476b47194
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733562"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance を削除する
この記事では、Azure Arc 対応 SQL Managed Instance を削除する方法について説明します。


## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>既存の Azure Arc 対応 SQL Managed Instance を表示する
SQL Managed Instance を表示するには、次のコマンドを実行します。

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

出力は次のようになります。

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance を削除する
SQL Managed Instance を削除するには、次のコマンドを実行します。

```azurecli
az sql mi-arc delete -n <NAME_OF_INSTANCE> --k8s-namespace <namespace> --use-k8s
```

出力は次のようになります。

```azurecli
# az sql mi-arc delete -n demo-mi --k8s-namespace <namespace> --use-k8s
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes 永続ボリューム要求 (PVC) を再利用する

SQL Managed Instance を削除しても、関連付けられた [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) が削除されるわけではありません。 これは仕様です。 これは、インスタンスが誤って削除された場合に、ユーザーがデータベース ファイルにアクセスできるようにするためです。 PVC の削除は必須ではありません。 ただし、お勧めではあります。 これらの PVC を再利用しない場合、Kubernetes クラスターによりディスク領域が不足するため、最終的にエラーが発生します。 PVC を再利用するには、次の手順を実行します。

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1.削除したサーバー グループの PVC を一覧表示する
PVC を一覧表示するには、次のコマンドを実行します。
```console
kubectl get pvc
```

次の例では、削除した SQL Managed Instance の PVC に注意してください。
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2.各 PVC を削除する
削除した SQL Managed Instance ごとに、データを削除し、PVC をログに記録します。
このコマンドの一般的な形式は次のとおりです。 
```console
kubectl delete pvc <name of pvc>
```

次に例を示します。
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

これらの各 kubectl コマンドは、PVC が正常に削除されたことを確認します。 次に例を示します。
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> 示しているように、PVC を削除しないと、最終的に Kubernetes クラスターがエラーをスローする状況になる可能性があります。 このようなエラーには、Kubernetes API からリソースを作成、読み取り、更新、削除できなくなる場合や、このストレージの問題によって、コントローラー ポッドが Kubernetes ノードから削除される可能性がある (通常の Kubernetes 動作) `az arcdata dc export` などのコマンドを実行できる場合があります。
>
> たとえば、次のようなメッセージがログに表示されます。  
> - Annotations:    microsoft.com/ignore-pod-health: true  
> - 状態:       失敗  
> - 理由:       削除された  
> - メッセージ:      ノードのリソースが不足しています: 短期ストレージ。 コンテナー コントローラーは 16372Ki を使用していましたが、これは 0 の要求を超えています。

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 SQL Managed Instance の機能](managed-instance-features.md)の詳細を確認する

[データ コントローラーの作成から開始する](create-data-controller.md)

データ コントローラーが既に作成されている場合 [Azure Arc 対応 SQL Managed Instance を作成する](create-sql-managed-instance.md)