---
title: Windows Server ノード プールに関する FAQ
titleSuffix: Azure Kubernetes Service
description: Windows Server ノード プールとアプリケーション ワークロードを Azure Kubernetes Service (AKS) 内で実行する際の、よく寄せられる質問について説明します。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9839b9075c3747c6c803e95c5622416da85d34d4
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138483"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>AKS の Windows Server ノード プールに関してよく寄せられる質問

Azure Kubernetes Service (AKS) では、Windows Server をゲスト OS としてノード上で実行するノード プールを作成することができます。 これらのノードは、.NET Framework 上に構築されたものなど、ネイティブの Windows コンテナー アプリケーションを実行できます。 Linux と Windows OS では、コンテナーのサポート方法に違いがあります。 Windows ノード プールでは、一般的な Linux Kubernetes およびポッド関連の機能の一部は現在使用できません。

この記事では、AKS 内の Windows Server ノードのよく寄せられる質問および OS の概念について説明します。

## <a name="which-windows-operating-systems-are-supported"></a>どの Windows オペレーティング システムがサポートされていますか?

AKS では、ホスト OS のバージョンとして Windows Server 2019 が使用され、プロセス分離のみがサポートされます。 他のバージョンの Windows Server を使用してビルドされたコンテナー イメージはサポートされていません。 詳細については、「[Windows コンテナーのバージョンの互換性][windows-container-compat]」を参照してください。

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows と Linux では Kubernetes に違いはありますか?

Windows Server ノード プールのサポートには、Kubernetes プロジェクトのアップストリーム Windows Server の一部であるいくつかの制限が含まれます。 これらの制限は、AKS 固有ではありません。 Kubernetes での Windows Server のアップストリーム サポートの詳細については、Kubernetes プロジェクトの「[Kubernetes での Windows のサポートの概要][intro-windows]」ドキュメントの「[サポートされている機能と制限事項][upstream-limitations]」を参照してください。

Kubernetes は従来より Linux が中心となっています。 アップストリームの [Kubernetes.io][kubernetes] Web サイトで使用されている多くの例は、Linux ノードでの使用を意図したものです。 Windows Server コンテナーを使用したデプロイを作成する場合、OS レベルの次の考慮事項が該当します。

- **ID**: Linux では、整数のユーザー識別子 (UID) によってユーザーが識別されます。 ユーザーは、ログオンに使用する英数字のユーザー名も持っています。これは、Linux によって、ユーザーの UID に変換されます。 同様に、Linux では、整数のグループ識別子 (GID) によってユーザー グループが識別され、グループ名が対応する GID に変換されます。
    Windows Server では、Windows Security Access Manager (SAM) データベースに格納されている大きいバイナリ セキュリティ識別子 (SID) を使用します。 このデータベースは、ホストとコンテナー、またはコンテナー間で共有されません。
- **ファイルのアクセス許可**: Windows Server では、アクセス許可および UID と GID のビットマスクではなく、SID に基づくアクセス制御リストが使用されます。
- **ファイル パス**: Windows Server の規則では、/ の代わりに \ が使用されます。 
    ボリュームをマウントするポッド仕様では、Windows Server コンテナー用にパスを正しく指定してください。 たとえば、Linux コンテナーの */mnt/volume* というマウント ポイントではなく、*K:* ドライブとしてマウントするために、 */K/Volume* のようにドライブ文字と場所を指定してください。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows ではどのような種類のディスクがサポートされていますか?

サポートされているボリュームの種類は、Azure ディスクと Azure Files です。 これらは、Windows Server コンテナーで NTFS ボリュームとしてアクセスされます。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Windows のみのクラスターを AKS で実行できますか?

AKS クラスター内のマスター ノード (コントロール プレーン) は AKS サービスによってホストされています。 マスター コンポーネントをホストしているノードのオペレーティング システムは、見える状態になっていません。 すべての AKS クラスターは既定の最初のノード プール (Linux ベース) で作成されます。 このノード プールには、クラスターが機能するために必要なシステム サービスが含まれています。 クラスターの信頼性を確保し、クラスター操作を実行できるようにするために、最初のノード プールで少なくとも 2 つのノードを実行することをお勧めします。 最初の Linux ベースのノード プールは、AKS クラスター自体を削除しない限り削除できません。

## <a name="how-do-i-patch-my-windows-nodes"></a>Windows ノードに修正プログラムを適用するにはどうすればいいですか?

Windows ノードの最新の修正プログラムを入手するには、[ノード プールをアップグレードする][nodepool-upgrade]か、[ノード イメージをアップグレード][upgrade-node-image]します。 AKS のノードでは Windows Update は有効になっていません。 AKS では、修正プログラムが利用可能になるとすぐに新しいノード プール イメージがリリースされます。更新プログラムや修正プログラムを最新の状態に維持するために、ノード プールをアップグレードするのは、お客様の責任です。 これは、使用されている Kubernetes のバージョンにも当てはまります。 新しいバージョンが利用可能になると、[AKS のリリース ノート][aks-release-notes]に掲載されます。 Windows Server ノード プール全体のアップグレードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。 ノード イメージの更新にのみ関心がある場合は、[AKS ノード イメージのアップグレード][upgrade-node-image]に関する記事を参照してください。

> [!NOTE]
> 更新された Windows Server イメージは、ノード プールをアップグレードする前にクラスターのアップグレード (コントロール プレーンのアップグレード) が実行された場合にのみ使用されます。
>

## <a name="what-network-plug-ins-are-supported"></a>どのネットワーク プラグインがサポートされていますか?

Windows ノード プールを使用する AKS クラスターでは、Azure Container Networking Interface (Azure CNI) (高度) ネットワーク モデルを使用する必要があります。 Kubenet (基本) ネットワークはサポートされていません。 ネットワーク モデルの違いの詳細については、[AKS のアプリケーションにおけるネットワークの概念][azure-network-models]に関する記事を参照してください。 Azure CNI ネットワーク モデルでは、IP アドレス管理に関する追加の計画と考慮事項が必要です。 Azure CNI を計画して実装する方法の詳細については、[AKS での Azure CNI ネットワークの構成][configure-azure-cni]に関するページを参照してください。

AKS クラスター上の Windows ノードでは、Calico が有効になっている場合、[Direct Server Return (DSR)][dsr] も既定で有効になっています。

## <a name="is-preserving-the-client-source-ip-supported"></a>クライアント ソース IP の保持はサポートされていますか?

現時点で、[クライアント ソース IP の保持][client-source-ip]は Windows ノードではサポートされていません。

## <a name="can-i-change-the-maximum-number-of-pods-per-node"></a>ノードあたりのポッドの最大数を変更できますか?

はい。 変更に伴う影響と使用可能なオプションについては、[ポッドの最大数][maximum-number-of-pods]に関するページを参照してください。

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>新しい Windows エージェント プールを作成しようとしたときにエラーが表示されるのはなぜですか。

2020 年 2 月より前にクラスターを作成し、クラスターのアップグレード操作を行っていない場合、クラスターは引き続き古い Windows イメージを使用します。 次のようなエラーが表示されている可能性があります。

"デプロイ テンプレートから参照されている次のイメージの一覧が見つかりません:発行元: MicrosoftWindowsServer、オファー:WindowsServer、Sku:2019-datacenter-core-smalldisk-2004、バージョン: 最新。 使用可能なイメージを検索する方法については、「[Azure PowerShell を使用して Azure Marketplace VM イメージを検索して使用する](../virtual-machines/windows/cli-ps-findimage.md)」を参照してください。

このエラーを修復するには:

1. [クラスター コントロール プレーン][upgrade-cluster-cp]をアップグレードして、イメージ オファーと発行元を更新します。
1. 新しい Windows エージェント プールを作成します。
1. Windows ポッドを既存の Windows エージェント プールから新しい Windows エージェント プールに移動します。
1. 古い Windows エージェント プールを削除します。

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Windows ノード プールのサービス プリンシパルはどのようにローテーションするのですか?

Windows ノード プールは、サービス プリンシパルのローテーションをサポートしていません。 サービス プリンシパルを更新するには、新しい Windows ノード プールを作成し、ポッドを古いプールから新しいものに移行します。 ポッドを新しいプールに移行した後、古いノード プールを削除します。

サービス プリンシパルの代わりにマネージド ID を使用します。これは基本的に、サービス プリンシパルをラップするラッパーです。 詳細については、「[Azure Kubernetes Service でマネージド ID を使用する][managed-identity]」を参照してください。

## <a name="how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster"></a>クラスター上の Windows Server ノードの管理者パスワードはどのように変更しますか?

AKS クラスターを作成するとき、`--windows-admin-password` と `--windows-admin-username` の各パラメーターを指定して、クラスター上の Windows Server ノードの管理者資格情報を設定します。 Azure portal を使用してクラスターを作成するときや、Azure CLI を使用して `--vm-set-type VirtualMachineScaleSets` と `--network-plugin azure` を設定するときなどに、管理者の資格情報を指定しなかった場合、ユーザー名は既定の *azureuser* になり、パスワードはランダムに生成されます。

管理者パスワードを変更するには、`az aks update` コマンドを使用します。

```azurecli
az aks update \
    --resource-group $RESOURCE_GROUP \
    --name $CLUSTER_NAME \
    --windows-admin-password $NEW_PW
```

> [!IMPORTANT]
> `az aks update` 操作を実行すると、Windows Server ノード プールのみがアップグレードされます。 Linux ノード プールは影響を受けません。
> 
> `--windows-admin-password` を変更する場合、新しいパスワードは 14 文字以上かつ [Windows Server のパスワード要件][windows-server-password]を満たしている必要があります。

## <a name="how-many-node-pools-can-i-create"></a>ノード プールはいくつ作成できますか?

AKS クラスターは最大 100 個のノード プールを持つことができます。 それらのノード プール全体で最大 1,000 個のノードを使用できます。 詳細については、[ノード プール制限][nodepool-limitations]に関するページを参照してください。

## <a name="what-can-i-name-my-windows-node-pools"></a>Windows ノード プールにはどのような名前を指定できますか?

名前は最大 6 文字までにしてください。 これは AKS の現在の制限です。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows ノードではすべての機能がサポートされていますか?

Kubernet は現在、Windows ノードではサポートされていません。

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Windows ノードでイングレス コントローラーを実行できますか?

はい。Windows Server コンテナーがサポートされているイングレス コントローラーは、AKS の Windows ノードで実行できます。

## <a name="can-my-windows-server-containers-use-gmsa"></a>Windows Server コンテナーで gMSA を使用できますか?

グループ管理サービス アカウント (gMSA) のサポートは、現在 AKS では使用できません。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Windows ノードとコンテナーを含むコンテナーには Azure Monitor を使用できますか?

はい、できます。 ただし、Windows コンテナーからログ (stdout、stderr) とメトリックを収集するための Azure Monitor はパブリック プレビュー段階です。 また、Windows コンテナーから stdout ログのライブ ストリームにアタッチできます。

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Windows ノードを含むクラスター上のサービス数に制限はありますか?

Windows ノードを含むクラスターでは、ポート不足が発生するまで、約 500 個のサービスを設定できます。

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Windows ノードで Azure ハイブリッド特典を使用できますか?

正解です。 Windows Server 向け Azure ハイブリッド特典では、オンプレミスの Windows Server ライセンスを AKS Windows ノードに持ち込めるため、運用コストを削減できます。

Azure ハイブリッド特典は、AKS クラスター全体または個々のノードで使用できます。 個々のノードの場合は、[ノード リソース グループ][resource-groups]を参照し、ノードに Azure ハイブリッド特典を直接適用する必要があります。 個々のノードに Azure ハイブリッド特典を適用する方法の詳細については、「[Windows Server 向け Azure Hybrid Benefit][hybrid-vms]」を参照してください。 

新しい AKS クラスターで Azure ハイブリッド特典を使用するには、`--enable-ahub` 引数を使用します。

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

既存の AKS クラスターで Azure ハイブリッド特典を使用するには、`--enable-ahub` 引数を使用してクラスターを更新します。

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

クラスターに Azure ハイブリッド特典が設定されているかどうかを確認するには、次のコマンドを使用します。

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

クラスターで Azure ハイブリッド特典が有効になっている場合、`az vmss show` の出力は次のようになります。

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Windows コンテナーで Kubernetes Web ダッシュボードを使用できますか?

はい。[Kubernetes Web ダッシュボード][kubernetes-dashboard]を使用して、Windows コンテナーに関する情報にアクセスできます。 ただし、このとき Kubernetes Web ダッシュボードから、実行中の Windows コンテナーに対して *kubectl exec* を直接実行することはできません。 実行中の Windows コンテナーへの接続の詳細については、「[メンテナンスまたはトラブルシューティングのために RDP を使用して Azure Kubernetes Service (AKS) クラスターの Windows Server ノードに接続する][windows-rdp]」を参照してください。

## <a name="how-do-i-change-the-time-zone-of-a-running-container"></a>実行中のコンテナーのタイム ゾーンの変更方法を教えてください

実行中の Windows Server コンテナーのタイム ゾーンを変更するには、PowerShell セッションを使用して実行中のコンテナーに接続します。 次に例を示します。
    
```azurecli-interactive
kubectl exec -it CONTAINER-NAME -- powershell
```

実行中のコンテナーで、[Set-TimeZone](/powershell/module/microsoft.powershell.management/set-timezone) を使用して、実行中のコンテナーのタイム ゾーンを設定します。 次に例を示します。

```powershell
Set-TimeZone -Id "Russian Standard Time"
```

実行中のコンテナーの現在のタイム ゾーンまたは使用可能なタイム ゾーンの一覧を表示するには、[Get-TimeZone](/powershell/module/microsoft.powershell.management/get-timezone) を使用します。

## <a name="can-i-maintain-session-affinity-from-client-connections-to-pods-with-windows-containers"></a>Windows コンテナーを使用してクライアント接続からポッドへのセッション アフィニティを管理できますか?

Windows Server 2022 OS バージョンでは、Windows コンテナーを使用してクライアント接続からポッドへのセッション アフィニティを管理することがサポートされますが、現時点でクライアント IP によってセッション アフィニティを実現するには、ノードあたり 1 つのインスタンスを実行するように必要なポッドを制限し、ローカル ノードのポッドにトラフィックを転送するように Kubernetes サービスを構成します。 

次の構成を使用します。 

1. 1.20 以上のバージョンを実行している AKS クラスターを使用します。
1. Windows ノードあたり 1 つのインスタンスのみを許可するようにポッドを制限します。 これは、デプロイ構成でアンチアフィニティを使用して実現できます。
1. Kubernetes サービス構成で、**externalTrafficPolicy=Local** を設定します。 これにより、Kubernetes サービスでローカル ノード内のポッドにのみトラフィックが送信されます。
1. Kubernetes サービス構成で、**sessionAffinity: ClientIP** を設定します。 これにより、Azure Load Balancer がセッション アフィニティを使用して構成されます。

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>サポートされていない機能が必要な場合はどうすればよいですか?

機能のギャップがある場合、オープンソースのアップストリーム [aks-engine][aks-engine] プロジェクトで、Azure で Kubernetes を実行する簡単で完全にカスタマイズ可能な方法が提供されており、Windows のサポートが含まれます。 詳細については、[AKS ロードマップ][aks-roadmap]を参照してください。

## <a name="next-steps"></a>次のステップ

AKS で Windows Server コンテナーの使用を開始するには、[AKS で Windows Server を実行するノード プールを作成する][windows-node-cli]ことに関する記事を参照してください。

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
