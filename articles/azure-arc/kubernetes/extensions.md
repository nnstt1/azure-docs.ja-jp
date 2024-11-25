---
title: Azure Arc 対応 Kubernetes クラスター拡張機能
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 対応 Kubernetes に拡張機能をデプロイし、そのライフサイクルを管理する
ms.openlocfilehash: 811bced5b0855ffdc44d851459b69a7b6aad6b19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312823"
---
# <a name="deploy-and-manage-azure-arc-enabled-kubernetes-cluster-extensions"></a>Azure Arc 対応 Kubernetes クラスター拡張機能をデプロイして管理する

Kubernetes 拡張機能を使用すると、Azure Arc 対応 Kubernetes クラスターで次のことを実行できます。

* クラスター拡張機能の Azure Resource Manager ベースのデプロイ。
* 拡張機能 Helm チャートのライフサイクル管理。

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * 現在利用可能な Azure Arc 対応 Kubernetes クラスター拡張機能。
> * 拡張機能インスタンスを作成する方法。
> * 必須の、および省略可能なパラメーター。
> * 拡張機能インスタンスを表示、一覧表示、更新、削除する方法。 

この機能の概要については、「[クラスター拡張機能 - Azure Arc 対応 Kubernetes](conceptual-extensions.md)」の記事を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](/cli/azure/install-azure-cli)。
- `connectedk8s` (バージョン 1.1.0 以降) および `k8s-extension` (バージョン 0.2.0 以降) の Azure CLI 拡張機能。 次のコマンドを実行して、これらの Azure CLI 拡張機能をインストールします。
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    `connectedk8s` および `k8s-extension` 拡張機能が既にインストールされている場合、次のコマンドを使用して最新バージョンに更新できます。

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Azure Arc 対応 Kubernetes に接続された既存のクラスター。
    - クラスターをまだ接続していない場合は[クイックスタート](quickstart-connect-cluster.md)を使用します。
    - バージョン 1.1.0 以降に[エージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

## <a name="currently-available-extensions"></a>現在使用可能な拡張機能

| 拡張機能 | 説明 |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Kubernetes クラスターにデプロイされているワークロードのパフォーマンスを可視化します。 コントローラー、ノード、コンテナーからメモリと CPU の使用率メトリックを収集します。 |
| [Microsoft Defender for Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Kubernetes クラスターから監査ログ データなどのセキュリティに関連する情報を収集します。 収集したデータに基づいて、推奨事項と脅威のアラートを提供します。 |
| [Azure Arc 対応 Open Service Mesh](tutorial-arc-enabled-open-service-mesh.md) | クラスターに Open Service Mesh をデプロイし、mTLS セキュリティ、きめ細かなアクセス制御、トラフィック移行、Azure Monitor または Prometheus および Grafana のオープンソース アドオンによる監視、Jaeger によるトレース、外部認定管理ソリューションとの統合などの機能を有効にします。 |
| [Azure Arc 対応 Data Services](../../azure-arc/kubernetes/custom-locations.md#create-custom-location) | Kubernetes と任意のインフラストラクチャを使用して、Azure データ サービスをオンプレミス、エッジ、パブリック クラウドで実行できるようになります。 |
| [Azure Arc 上の Azure App Service](../../app-service/overview-arc-integration.md) | Azure Arc 対応 Kubernetes クラスター上に App Service Kubernetes 環境をプロビジョニングできるようになります。 |
| [Kubernetes 上の Event Grid](../../event-grid/kubernetes/overview.md) | Azure Arc 対応 Kubernetes クラスター上で、トピックやイベント サブスクリプションなどのイベント グリッド リソースを作成および管理します。 |
| [Azure Arc 上の Azure API Management](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md) | Azure Arc 対応 Kubernetes クラスターに API Management ゲートウェイをデプロイして管理します。 |
| [Azure Arc 対応機械学習](../../machine-learning/how-to-attach-arc-kubernetes.md) | Azure Arc 対応 Kubernetes クラスターで Azure Machine Learning をデプロイして実行します。 |

## <a name="usage-of-cluster-extensions"></a>クラスター拡張機能の使用

### <a name="create-extensions-instance"></a>拡張機能インスタンスを作成する

`k8s-extension create` を使用して、必須パラメーターの値を渡して、新しい拡張機能インスタンスを作成します。 下のコマンドによって、Azure Arc 対応 Kubernetes クラスターに Azure Monitor for containers 拡張機能インスタンスが作成されます。

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**出力:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * サービスでは、48 時間よりも長く機密情報を保持できません。 Azure Arc 対応 Kubernetes エージェントに 48 時間よりも長くネットワーク接続がなく、クラスターに拡張機能を作成するかどうかを判断できない場合、拡張機能は `Failed` 状態に遷移します。 `Failed` 状態になったら、新しい拡張機能 Azure リソースを作成するために、`k8s-extension create` を再び実行する必要があります。
> * Azure Monitor for containers は、シングルトン拡張機能です (クラスターごとに 1 つのみ必要)。 Azure Monitor for containers (拡張機能なし) の以前の Helm チャート インストールをクリーンアップしてから、拡張機能を介して同じものをインストールする必要があります。 指示に従い、[`az k8s-extension create` を実行する前に Helm チャートを削除](../../azure-monitor/containers/container-insights-optout-hybrid.md)してください。

**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--name` | 拡張機能インスタンスの名前 |
| `--extension-type` | クラスターにインストールする拡張機能の種類。 例: Microsoft.AzureMonitor.Containers、microsoft.azuredefender.kubernetes | 
| `--scope` | 拡張機能のインストールのスコープ - `cluster` または `namespace` |
| `--cluster-name` | 拡張機能インスタンスを作成する必要がある Azure Arc 対応 Kubernetes リソースの名前 |
| `--resource-group` | Azure Arc 対応 Kubernetes リソースを含むリソース グループ |
| `--cluster-type` | 拡張機能インスタンスを作成する必要があるクラスターの種類。 現時点で指定できる値は、Azure Arc 対応 Kubernetes に対応する、`connectedClusters` のみです |

**省略可能なパラメーター**

| パラメーター名 | 説明 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 拡張機能のマイナー バージョンを自動的にアップグレードするかどうかを指定するブール型プロパティ。 既定値:`true`。  このパラメーターが true に設定されている場合、バージョンは動的に更新されるため、`version` パラメーターは設定できません。 `false` に設定されている場合、パッチ バージョンでも、拡張機能は自動アップグレードされません。 |
| `--version` | インストールする拡張機能のバージョン (拡張機能インスタンスの固定先の特定バージョン)。 auto-upgrade-minor-version が `true` に設定されている場合、指定しないでください。 |
| `--configuration-settings` | 機能を制御するために拡張機能に渡すことができる設定。 これらは、パラメーター名の後で、`key=value` のペアをスペースで区切って渡します。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-settings-file` は使用できません。 |
| `--configuration-settings-file` | 拡張機能に構成設定を渡すために使用されるキーと値のペアを含む JSON ファイルへのパス。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-settings` は使用できません。 |
| `--configuration-protected-settings` | これらの設定は、`GET` API 呼び出しまたは `az k8s-extension show` コマンドを使用して取得できないため、機密設定を渡すために使用されます。 これらは、パラメーター名の後で、`key=value` のペアをスペースで区切って渡します。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-protected-settings-file` は使用できません。 |
| `--configuration-protected-settings-file` | 拡張機能に機密設定を渡すために使用されるキーと値のペアを含む JSON ファイルへのパス。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-protected-settings` は使用できません。 |
| `--release-namespace` | このパラメーターは、リリースを作成する名前空間を示します。 このパラメーターは、`scope` パラメーターが `cluster` に設定されている場合にのみ関係します。 |
| `--release-train` |  拡張機能の作成者は、`Stable`、`Preview` など、さまざまなリリース トレインでバージョンを公開できます。このパラメーターが明示的に設定されていない場合、既定値として `Stable` が使用されます。 `autoUpgradeMinorVersion` パラメーターが `false` に設定されている場合、このパラメーターは使用できません。 |
| `--target-namespace` | このパラメーターは、リリースを作成する名前空間を示します。 この拡張機能インスタンスに対して作成されたシステム アカウントのアクセス許可は、この名前空間に制限されます。 このパラメーターは、`scope` パラメーターが `namespace` に設定されている場合にのみ関係します。 |

### <a name="show-details-of-an-extension-instance"></a>拡張機能インスタンスの詳細を表示する

`k8s-extension show` を使用し、必須パラメーターの値を渡すことで、現在インストールされている拡張機能インスタンスの詳細を表示します。

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**出力:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>クラスターにインストールされているすべての拡張機能を一覧表示する

`k8s-extension list` を使用し、必須パラメーターの値を渡すことで、クラスターにインストールされているすべての拡張機能を一覧表示します。

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**出力:**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>拡張機能インスタンスを削除する

`k8s-extension delete` を使用し、必須パラメーターの値を渡すことで、クラスター上の拡張機能インスタンスを削除します。

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> この拡張機能を表す Azure リソースはただちに削除されます。 Kubernetes クラスターで実行されているエージェントがネットワークに接続していて、目的の状態を取得するために Azure サービスに再びアクセスできる場合にのみ、この拡張機能に関連するクラスターの Helm リリースが削除されます。

## <a name="next-steps"></a>次のステップ

Azure Arc 対応 Kubernetes で現在使用できるクラスター拡張機能について詳しく学習してださい。

> [!div class="nextstepaction"]
> [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Microsoft Defender for Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Azure Arc 対応 Open Service Mesh](tutorial-arc-enabled-open-service-mesh.md)
> 
> [!div class="nextstepaction"]
> [Microsoft Defender for Cloud](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> 
> [!div class="nextstepaction"]
> [Azure Arc 上の Azure App Service](../../app-service/overview-arc-integration.md)
> 
> [!div class="nextstepaction"]
> [Kubernetes 上の Event Grid](../../event-grid/kubernetes/overview.md)
> 
> [!div class="nextstepaction"]
> [Azure Arc 上の Azure API Management](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md)
