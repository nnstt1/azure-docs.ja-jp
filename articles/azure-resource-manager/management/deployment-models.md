---
title: Resource Manager デプロイとクラシック デプロイ
description: Resource Manager デプロイ モデルとクラシック (あるいはサービス管理) デプロイ モデルの違いについて説明します。
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e9ea1e778db81cfaa69163d5e127d384f8c4b3f5
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298171"
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について

> [!NOTE]
> この記事に記載されている情報は、クラシック デプロイから Azure Resource Manager デプロイに移行する場合にのみ適用されます。

この記事では、Azure Resource Manager デプロイ モデルとクラシック デプロイ モデルについて学習します。 Resource Manager デプロイ モデルとクラシック デプロイ モデルは、Azure ソリューションのデプロイと管理における 2 種類の異なる方法です。 異なる 2 種類の API セットを使用することで、デプロイしたリソースには重要な相違点が存在する可能性があります。 これらの 2 つのモデルに互換性はありません。 この記事では、その相違点について説明します。

リソースのデプロイと管理を簡単にするために、すべての新しいリソースに Resource Manager を利用することが推奨されています。 可能であれば、Resource Manager を使用して既存のリソースを再度デプロイすることをお勧めします。 Cloud Services を使用した場合は、ソリューションを [Cloud Services (延長サポート)](../../cloud-services-extended-support/overview.md) に移行することができます。

これまでに Resource Manager を使用したことがない場合は、まず「[Azure Resource Manager の概要](overview.md)」で定義されている用語をご確認ください。

## <a name="history-of-the-deployment-models"></a>デプロイメント モデルの歴史

Azure では当初、クラシック デプロイ モデルのみ用意されていました。 このモデルでは、各リソースが独立して存在していたため、関連リソースをまとめてグループ化する方法がありませんでした。 それどころか、ソリューションまたはアプリケーションを構成するリソースを手動で追跡し、うまくバランスを取りながら管理する必要がありました。 ソリューションをデプロイするには、ポータル経由で各リソースを個別に作成するか、すべてのリソースを正しい順序でデプロイするスクリプトを作成する必要がありました。 ソリューションを削除するには、各リソースを個別に削除するほかありませんでした。 関連リソースのアクセス制御ポリシーの適用と更新は、簡単ではありませんでした。 そのうえ、タグをリソースに適用し、リソースの監視と請求の管理に役立つ単語を使ってこれらのリソースにラベル付けすることもできませんでした。

2014 年、Azure に Resource Manager が導入され、リソース グループの概念が追加されました。 リソース グループとは、共通のライフサイクルが共有されるリソースのコンテナーです。 Resource Manager デプロイ モデルにはいくつかの利点があります。

* ソリューションのすべてのサービスを、個別に処理するのではなく、グループとしてデプロイ、管理、監視できます。
* ソリューションをそのライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。
* リソース グループに属するすべてのリソースにアクセス制御を適用できます。これらのポリシーは、リソース グループに新しいリソースが追加されたときに自動的に適用されます。
* タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。
* JavaScript Object Notation (JSON) を使用してソリューションのインフラストラクチャを定義できます。 JSON ファイルは Resource Manager テンプレートと呼ばれます。
* 正しい順序でデプロイされるようにリソース間の依存性を定義できます。

Resource Manager が追加されたとき、すべてのリソースが遡及的に既定のリソース グループに追加されました。 今、従来のデプロイでリソースを作成すると、デプロイ時にリソース グループを指定していなくても、リソースはそのサービスの既定のリソース グループ内に自動的に作成されます。 ただし、リソース グループ内に存在するだけでは、リソースが Resource Manager モデルに変換されたことになりません。

## <a name="understand-support-for-the-models"></a>モデルのサポートについて

次の 3 種類のシナリオに注意してください。

1. [Cloud Services (クラシック)](../../cloud-services/cloud-services-choose-me.md) では、Resource Manager デプロイ モデルはサポートされていません。 [Cloud Services (延長サポート)](../../cloud-services-extended-support/overview.md) では、Resource Manager デプロイ モデルがサポートされています。
2. 仮想マシン、ストレージ アカウント、仮想ネットワークでは、Resource Manager デプロイ モデルとクラシック デプロイ モデルの両方がサポートされています。
3. その他のすべての Azure サービスでは、Resource Manager がサポートされています。

仮想マシン、ストレージ アカウント、仮想ネットワークについては、クラシック デプロイメントでリソースが作成された場合、クラシックの操作でそのリソースを操作し続ける必要があります。 仮想マシン、ストレージ アカウント、または仮想ネットワークが Resource Manager デプロイメントで作成された場合は、Resource Manager の操作を使用し続ける必要があります。 このような区別があるため、Resource Manager デプロイメントで作成されたリソースとクラシック デプロイメントで作成されたリソースがサブスクリプション内に混在していると、混乱を招くおそれがあります。 リソースが同じ操作に対応しないため、そのような混在が予想外の結果を生むことがあります。

場合によっては、Resource Manager コマンドを使用することで、クラシック デプロイメントで作成したリソースに関する情報を取得したり、クラシック リソースを別のリソース グループに移動するなどの管理タスクを実行したりできます。 しかしこれらのケースから、その種類が Resource Manager の操作に対応しているという印象を持たないようにしてください。 たとえば、クラシック デプロイメントで作成された仮想マシンを含むリソース グループがあるとします。 例として、次の Resource Manager PowerShell コマンドを実行したとします。

```powershell
Get-AzResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

仮想マシンが返されます。

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

ただし、Resource Manager コマンドレット **Get-AzVM** を実行した場合は、Resource Manager でデプロイされた仮想マシンのみが返されます。 次のコマンドでは、クラシック デプロイで作成された仮想マシンは返されません。

```powershell
Get-AzVM -ResourceGroupName ExampleGroup
```

Resource Manager で作成したリソースだけがタグに対応しています。 従来のリソースにタグを適用することはできません。

## <a name="changes-for-compute-network-and-storage"></a>コンピューティング、ネットワーク、ストレージの変更

次の図は、Resource Manager でデプロイされたコンピューティング、ネットワーク、ストレージのリソースを示しています。

![Resource Manager architecture](./media/deployment-models/arm_arch3.png)

SRP: ストレージ リソース プロバイダー、CRP: コンピューティング リソース プロバイダー、NRP: ネットワーク リソース プロバイダー

次に挙げるリソース間の関係を確認してください。

* すべてのリソースがリソース グループ内に存在します。
* 仮想マシンは、ディスクを BLOB ストレージ (必須) に保存するために、Storage リソース プロバイダーで定義されている特定のストレージ アカウントに依存します。
* 仮想マシンは、Network リソース プロバイダー (必須) で定義されている特定のネットワーク インターフェイス カードと、Compute リソース プロバイダー (オプション) で定義されている可用性セットを参照します。
* ネットワーク インターフェイス カードは、仮想マシンに割り当てられた IP アドレス (必須)、仮想マシンの仮想ネットワークのサブネット (必須)、ネットワークのセキュリティ グループ (オプション) を参照します。
* 仮想ネットワーク内のサブネットは、ネットワーク セキュリティ グループ (オプション) を参照します。
* Load Balancer のインスタンスでは、仮想マシンのネットワーク インターフェイス カード (オプション) を含む IP アドレスのバックエンド プールと、Load Balancer パブリックまたはプライベート IP アドレス (オプション) を参照します。

次にコンポーネントと、クラシック デプロイメントにおけるコンポーネントの関係を示します。

![classic architecture](./media/deployment-models/arm_arch1.png)

仮想マシンをホストする従来のソリューションは次のとおりです。

* Cloud Services (クラシック) は、仮想マシン (コンピューティング) をホストするためのコンテナーとして機能します。 仮想マシンにはネットワーク インターフェイス カードが自動的に提供され、IP アドレスは Azure によって割り当てられます。 さらに、クラウド サービスには、外部のロード バランサーのインスタンス、パブリック IP アドレス、および Windows ベースの仮想マシンのリモート デスクトップとリモート PowerShell トラフィックと Linux ベースの仮想マシン用の Secure Shell (SSH) トラフィックを許可する既定のエンドポイントが含まれています。
* オペレーティング システム、一時データ ディスク、追加データ ディスク (ストレージ) を含む、仮想マシンの仮想ハード ディスクを格納するのに必要なストレージ アカウント。
* 追加のコンテナーとして機能するオプションの仮想ネットワーク。ここで、サブネット化構造を作成し、仮想マシンが配置されているサブネット (ネットワーク) を選択することができます。

次の表では、Compute、Network、Storage のリソース プロバイダーの相互作用の変化について説明します。

| Item | クラシック | リソース マネージャー |
| --- | --- | --- |
| 仮想マシン用クラウド サービス |クラウド サービスは、プラットフォームに基づく可用性と負荷分散を必要とする仮想マシンを保持するためのコンテナーです。 |新しいモデルを使用して仮想マシンを作成するためのオブジェクトとしてのクラウド サービスは不要となりました。 |
| 仮想ネットワーク |仮想ネットワークは仮想マシンでは省略可能です。 使用すると、仮想ネットワークは Resource Manager でデプロイできなくなります。 |仮想マシンには、Resource Manager でデプロイされた仮想ネットワークが必要です。 |
| ストレージ アカウント |仮想マシンには、オペレーティング システム、一時データ ディスク、追加データ ディスク用の仮想ハード ディスクを格納するためのストレージ アカウントが必要です。 |仮想マシンには、BLOB ストレージにディスクを保存するためのストレージ アカウントが必要です。 |
| 可用性セット |プラットフォームに対する可用性は、仮想マシンに同じ "AvailabilitySetName" を構成することによって示されます。 障害ドメインの最大数は 2 です。 |可用性セットは、Microsoft.Compute プロバイダーによって公開されるリソースです。 可用性セットには、高可用性を必要とする仮想マシンを含める必要があります。 障害ドメインの最大数は 3 です。 |
| アフィニティ グループ |仮想ネットワークを作成するにはアフィニティ グループが必要です。 ただし、リージョン仮想ネットワークの導入により、それは不要となりました。 |単純化するために、Azure Resource Manager によって公開される API には、アフィニティ グループの概念が存在しません。 |
| 負荷分散 |デプロイされている仮想マシンには、クラウド サービスの作成によって暗黙的なロード バランサーが提供されます |ロード バランサーは、Microsoft.Network プロバイダーによって公開されるリソースです。 負荷分散を必要とする仮想マシンのプライマリ ネットワーク インターフェイスは、ロード バランサーを参照している必要があります。 ロード バランサーには、内部ロード バランサーと外部ロード バランサーとがあります。 ロード バランサーのインスタンスは仮想マシンの NIC を含め(オプション)、ロード バランサーのパブリックまたはプライベート IP アドレス (オプション) を参照している IP アドレスのバックエンドプールを参照します。 |
| 仮想 IP アドレス |Cloud Services では、クラウド サービスに VM を追加したときに既定の VIP (仮想 IP アドレス) が付与されます。 仮想 IP アドレスは、暗黙的なロード バランサーに関連付けられるアドレスです。 |パブリック IP アドレスは、Microsoft.Network プロバイダーによって公開されるリソースです。 パブリック IP アドレスには、静的 (予約済み) アドレスと動的アドレスとがあります。 ロード バランサーには、動的パブリック IP を割り当てることができます。 パブリック IP のセキュリティは、セキュリティ グループを使用して保護できます。 |
| 予約済み IP アドレス |IP アドレスを Azure で予約し、クラウド サービスに関連付けることで、その IP アドレスを固定アドレスとすることができます。 |パブリック IP アドレスは静的モードで作成でき、予約済み IP アドレスと同じ機能を持ちます。 |
| VM ごとのパブリック IP アドレス (PIP) |パブリック IP アドレスを直接 VM に関連付けることもできます。 |パブリック IP アドレスは、Microsoft.Network プロバイダーによって公開されるリソースです。 パブリック IP アドレスには、静的 (予約済み) アドレスと動的アドレスとがあります。 |
| エンドポイント |特定のポートの接続を確立するためには、仮想マシンに入力エンドポイントを構成する必要があります。 入力エンドポイントを設定することによって仮想マシンに接続する一般的なモードの 1 つ。 |VM への接続用に特定のポートのエンドポイントを有効にする機能は、ロード バランサーに受信 NAT ルールを構成することで実現できます。 |
| DNS 名 |クラウド サービスには、グローバルに一意となる暗黙的な DNS 名が与えられます (例: `mycoffeeshop.cloudapp.net`)。 |DNS 名は、パブリック IP アドレス リソースに指定できる省略可能なパラメーターです。 FQDN は、`<domainlabel>.<region>.cloudapp.azure.com` という形式です。 |
| ネットワーク インターフェイス |プライマリとセカンダリのネットワーク インターフェイスおよびそのプロパティは、仮想マシンのネットワーク構成として定義されます。 |ネットワーク インターフェイスは、Microsoft.Network プロバイダーによって公開されるリソースです。 ネットワーク インターフェイスのライフサイクルは、仮想マシンに関連付けられません。 ネットワーク インターフェイスは、仮想マシンに割り当てられた IP アドレス (必須)、仮想マシンの仮想ネットワークのサブネット (必須)、ネットワークのセキュリティ グループ (オプション) を参照します。 |

さまざまなデプロイメント モデルから仮想ネットワークを接続する方法の詳細については、「[異なるデプロイ モデルの仮想ネットワークをポータルで接続する](../../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)」を参照してください。

## <a name="migrate-from-classic-to-resource-manager"></a>クラシックから Resource Manager への移行

クラシック デプロイから Resource Manager デプロイメントにリソースを移行する準備ができたら、次のページを参照してください。

1. [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../../virtual-machines/migration-classic-resource-manager-deep-dive.md)
2. [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../../virtual-machines/migration-classic-resource-manager-overview.md)
3. [Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../../virtual-machines/migration-classic-resource-manager-ps.md)
4. [Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../../virtual-machines/migration-classic-resource-manager-cli.md)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**クラシック デプロイで作成された仮想ネットワークにデプロイする仮想マシンを、Resource Manager で作成することはできますか。**

この構成はサポートされていません。 Resource Manager を使用して、クラシック デプロイで作成された仮想ネットワークに仮想マシンをデプロイすることはできません。

**クラシック デプロイ モデルを使用して作成されたユーザー イメージから、Resource Manager を使用して仮想マシンを作成することはできますか。**

この構成はサポートされていません。 ただし、クラシック デプロイ モデルで作成されたストレージ アカウントから仮想ハード ディスク ファイルをコピーし、Resource Manager で作成された新しいアカウントに追加することはできます。

**サブスクリプションのクォータにはどのような影響が生じますか。**

Azure Resource Manager を使用して作成された仮想マシン、仮想ネットワーク、ストレージ アカウントのクォータは、他のクォータとは区別されます。 各サブスクリプションには新しい API を使用してリソースを作成するためのクォータが付与されます。 追加クォータの詳細については、 [こちら](../../azure-resource-manager/management/azure-subscription-service-limits.md)を参照してください。

**仮想マシン、仮想ネットワーク、ストレージ アカウントをプロビジョニングするための独自の自動スクリプトは、Resource Manager API で引き続き使用できますか。**

これまでに作成した自動化やスクリプトはすべて、Azure Service Management モードで作成された既存の仮想マシンや仮想ネットワークに使用できます。 ただし、Resource Manager モードで同じリソースを作成するためには、新しいスキーマを使用できるようにスクリプトを更新する必要があります。

**Azure Resource Manager のテンプレートの例はどこで入手できますか。**

[Azure Resource Manager のクイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)に関するページで、広範囲にわたるスターター テンプレートが提供されています。

## <a name="next-steps"></a>次のステップ

<<<<<<< HEAD
* テンプレートをデプロイするためのコマンドについては、「[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイに関するページ](../templates/deploy-powershell.md)」を参照してください。
=======
* テンプレートをデプロイするためのコマンドについては、「 [Azure Resource Manager テンプレートを使用したアプリケーションのデプロイに関するページ](../templates/deploy-powershell.md)」を参照してください。
>>>>>>> repo_sync_working_branch
