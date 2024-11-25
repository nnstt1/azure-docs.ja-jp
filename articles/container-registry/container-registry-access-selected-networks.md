---
title: パブリック レジストリ アクセスの構成
description: 選択したパブリック IP アドレスまたはアドレス範囲から Azure Container Registry へのアクセスを有効にする IP ルールを構成します。
ms.topic: article
ms.date: 07/30/2021
ms.openlocfilehash: 3a4a4a28dfbcd859cf97be6799e24a8110add436
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627009"
---
# <a name="configure-public-ip-network-rules"></a>パブリック IP ネットワーク ルールを構成する

既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからのインターネット経由の接続を受け入れます。 この記事では、特定のパブリック IP アドレスまたはアドレス範囲からのアクセスのみを許可するようにコンテナー レジストリを構成する方法について説明します。 Azure CLI と Azure portal を使用した同等の手順が提供されます。

IP ネットワーク ルールは、パブリック レジストリ エンドポイントで構成されます。 IP ネットワーク ルールは、[Private Link](container-registry-private-link.md) を使用して構成されたプライベート エンドポイントには適用されません

IP アクセス規則の構成は、**Premium** コンテナー レジストリ サービス レベルで利用できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のレベル](container-registry-skus.md)に関する記事を参照してください。

各レジストリでは、最大 100 個の IP アクセス規則がサポートされています。

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>選択したパブリック ネットワークからのアクセス - CLI

### <a name="change-default-network-access-to-registry"></a>レジストリへの既定のネットワーク アクセスを変更する

選択したパブリック ネットワークへのアクセスを制限するには、まず既定のアクションを変更してアクセスを拒否します。 次の [az acr update][az-acr-update] コマンドでは、自分のレジストリの名前に置き換えます。

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>レジストリにネットワーク規則を追加する

[az acr network-rule add][az-acr-network-rule-add] コマンドを使用して、パブリック IP アドレスまたは範囲からのアクセスを許可するネットワーク規則をレジストリに追加します。 たとえば、仮想ネットワーク内の VM のコンテナー レジストリの名前とパブリック IP アドレスを置き換えます。

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 規則を追加してから有効になるまで数分かかります。

## <a name="access-from-selected-public-network---portal"></a>選択したパブリック ネットワークからのアクセス - ポータル

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[設定]** で、 **[ネットワーク]** を選択します。
1. **[パブリック アクセス]** タブで、 **[選択されたネットワーク]** からのパブリック アクセスを許可するよう選択します。
1. **[ファイアウォール]** で、仮想ネットワーク内の VM のパブリック IP アドレスなどのパブリック IP アドレスを入力します。 または、VM の IP アドレスを含むアドレス範囲を CIDR 表記法で入力します。
1. **[保存]** を選択します。

![コンテナー レジストリのファイアウォール規則の構成][acr-access-selected-networks]

> [!NOTE]
> 規則を追加してから有効になるまで数分かかります。

> [!TIP]
> 必要に応じて、ローカル クライアント コンピューターまたは IP アドレス範囲からのレジストリ アクセスを有効にします。 このアクセスを許可するには、コンピューターのパブリック IPv4 アドレスが必要です。 このアドレスは、インターネット ブラウザーで "what is my IP address" と検索することで確認できます。 現在のクライアント IPv4 アドレスは、ポータルの **[ネットワーク]** ページでファイアウォール設定を構成したときにも自動的に表示されます。

## <a name="disable-public-network-access"></a>パブリック ネットワーク アクセスの無効化

必要に応じて、レジストリのパブリック エンドポイントを無効にします。 パブリック エンドポイントを無効にすると、すべてのファイアウォール構成がオーバーライドされます。 たとえば、[Private Link](container-registry-private-link.md) を使用して、仮想ネットワークでセキュリティ保護されたレジストリへのパブリック アクセスを無効にする場合などです。

> [!NOTE]
> [サービス エンドポイント](container-registry-vnet.md)のある仮想ネットワークでレジストリが設定される場合、レジストリのパブリック エンドポイントへのアクセスを無効にすると、仮想ネットワーク内のレジストリへのアクセスも無効になります。

### <a name="disable-public-access---cli"></a>パブリック アクセスを無効にする - CLI

Azure CLI を使用してパブリック アクセスを無効にするには、[az acr update][az-acr-update] を実行し、`--public-network-enabled` を `false` に設定します。 `public-network-enabled` 引数には、Azure CLI 2.6.0 以降が必要です。 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>パブリック アクセスを無効にする - ポータル

1. ポータルで、コンテナー レジストリに移動し、 **[設定] > [ネットワーク]** の順に選択します。
1. **[パブリック アクセス]** タブの **[Allow public network access]\(パブリック ネットワーク アクセスを許可する\)** で、 **[無効]** を選択します。 次に、 **[保存]** を選択します。

![パブリック アクセスを無効にする][acr-access-disabled]


## <a name="restore-public-network-access"></a>パブリック ネットワーク アクセスの復元

パブリック エンドポイントを再度有効にするには、パブリック アクセスを許可するようにネットワーク設定を更新します。 パブリック エンドポイントを有効にすると、すべてのファイアウォール構成がオーバーライドされます。 

### <a name="restore-public-access---cli"></a>パブリック アクセスの復元 - CLI

[az acr update][az-acr-update] を実行し、`--public-network-enabled` を `true` に設定します。 

> [!NOTE]
> `public-network-enabled` 引数には、Azure CLI 2.6.0 以降が必要です。 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>パブリック アクセスの復元 - ポータル

1. ポータルで、コンテナー レジストリに移動し、 **[設定] > [ネットワーク]** の順に選択します。
1. **[パブリック アクセス]** タブの **[Allow public network access]\(パブリック ネットワーク アクセスを許可する\)** で、 **[すべてのネットワーク]** を選択します。 次に、 **[保存]** を選択します。

![すべてのネットワークからのパブリック アクセス][acr-access-all-networks]

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="access-behind-https-proxy"></a>HTTPS プロキシの内側のアクセス

パブリック ネットワーク ルールが設定されている場合、またはレジストリへのパブリック アクセスが拒否された場合は、許可されていないパブリック ネットワークからレジストリにログインしようとすると失敗します。 プロキシのアクセス ルールが設定されていない場合も、HTTPS プロキシの内側からクライアントにアクセスすることはできません。 `Error response from daemon: login attempt failed with status: 403 Forbidden` や `Looks like you don't have access to registry` のようなエラー メッセージが表示されます。

これらのエラーは、ネットワーク アクセス ルールで許可されている HTTPS プロキシを使用しているにも関わらず、プロキシがクライアント環境で適切に構成されていない場合にも発生する可能性があります。 Docker クライアントと Docker デーモンの両方がプロキシの動作用に構成されていることを確認します。 詳細については、Docker ドキュメントの [HTTP/HTTPS プロキシ](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)に関するページを参照してください。

### <a name="access-from-azure-pipelines"></a>Azure Pipelines からのアクセス

特定の IP アドレスへのアクセスが制限されている Azure コンテナー レジストリで Azure Pipelines を使用した場合、パイプラインからのアウトバウンド IP アドレスが固定されていないため、パイプラインでレジストリにアクセスできない可能性があります。 既定では、パイプラインにより、IP アドレスのセットが変化する仮想マシン プール上の Microsoft ホステッド [エージェント](/azure/devops/pipelines/agents/agents)を使用して、ジョブが実行されます。

回避策の 1 つは、パイプラインの実行に使用されるエージェントを、Microsoft ホステッドからセルフホステッドに変更することです。 お客様が管理する [Windows](/azure/devops/pipelines/agents/v2-windows) または [Linux](/azure/devops/pipelines/agents/v2-linux) マシン上で実行されるセルフホステッド エージェントを使用すると、パイプラインのアウトバウンド IP アドレスをお客様が制御するので、このアドレスをレジストリ IP アクセス ルールに追加することができます。

### <a name="access-from-aks"></a>AKS からのアクセス

特定の IP アドレスへのアクセスが制限されている Azure コンテナー レジストリで Azure Kubernetes Service (AKS) を使用する場合、既定では固定の AKS IP アドレスを構成できません。 AKS クラスターからのエグレス IP アドレスはランダムに割り当てられます。

AKS クラスターがレジストリにアクセスできるようにするには、次のオプションがあります。

* Azure Basic Load Balancer を使用している場合は、AKS クラスターに対して[静的 IP アドレス](../aks/egress.md)を設定します。 
* Azure Standard Load Balancer を使用している場合は、クラスターからの[エグレス トラフィックの制御](../aks/limit-egress-traffic.md)に関するガイダンスを参照してください。

## <a name="next-steps"></a>次のステップ

<<<<<<< HEAD
* 仮想ネットワーク内のプライベート エンドポイントを使用してレジストリへのアクセスを制限するには、「[Azure Container Registry 用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。
* クライアント ファイアウォールの内側からレジストリ アクセス規則を設定する必要がある場合は、「[ファイアウォールの内側から Azure Container Registry にアクセスする規則を構成する](container-registry-firewall-access-rules.md)」を参照してください。
=======
* 仮想ネットワーク内のプライベート エンドポイントを使用してレジストリへのアクセスを制限するには、「[Azure コンテナー レジストリ用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。
* クライアント ファイアウォールの内側からレジストリ アクセス規則を設定する必要がある場合は、「[ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する](container-registry-firewall-access-rules.md)」を参照してください。
* トラブルシューティングに関する他のガイダンスについては、「[レジストリに関するネットワークの問題のトラブルシューティング](container-registry-troubleshoot-access.md)」を参照してください。
>>>>>>> repo_sync_working_branch

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
