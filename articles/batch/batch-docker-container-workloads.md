---
title: コンテナー ワークロード
description: Azure Batch でコンテナー イメージからアプリを実行し、スケーリングする方法について説明します。 コンテナー タスクの実行をサポートするコンピューティング ノードのプールを作成します。
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c6922c48aedc3394d164367806bece43d5fb8a49
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744224"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch で コンテナー アプリケーションを実行する

Azure Batch を使用すると、Azure で大量のバッチ コンピューティング ジョブを実行し、また、その実行量を調整できます。 Batch タスクは Batch プール内の仮想マシン (ノード) で直接実行できますが、タスクがノード上の Docker と互換性のあるコンテナーで実行されるように Batch プールを設定することもできます。 この記事では、実行中のコンテナー タスクをサポートするコンピューティング ノードのプールを作成して、プールでコンテナー タスクを実行する方法について説明します。

このコード例では、Batch .NET と Python SDK を使用します。 また、Batch SDK、および Azure Portal などのツールを使用して、コンテナー対応の Batch プールを作成したり、コンテナー タスクを実行したりすることもできます。

## <a name="why-use-containers"></a>コンテナーを使用する理由

コンテナーを使用すると、Batch タスクを簡単に実行できます。アプリケーションを実行する目的で環境や依存関係を管理する必要はありません。 コンテナーにより、アプリケーションが、複数の異なる環境で実行できる、軽量で移植可能かつ自律的なユニットとしてデプロイされます。 たとえば、コンテナーをローカルで構築し、テストしてから、コンテナー イメージを Azure のレジストリなどにアップロードします。 コンテナー デプロイメント モデルにより、アプリケーションのランタイム環境が、そのアプリケーションをホストする場所がどこであっても、常に適切にインストールおよび構成されます。 Batch のコンテナー ベース タスクでは、アプリケーション パッケージ、リソース ファイルの管理、出力ファイルの管理など、コンテナー タスク以外の機能も活用されます。

## <a name="prerequisites"></a>前提条件

コンテナーの概念および Batch プールとジョブを作成する方法に精通しておく必要があります。

- **SDK バージョン**: 次のバージョンの時点で、Batch SDK ではコンテナー イメージをサポートしています。
  - Batch REST API バージョン 2017-09-01.6.0
  - Batch .NET SDK バージョン 8.0.0
  - Batch Python SDK バージョン 4.0
  - Batch Java SDK バージョン 3.0
  - Batch Node.js SDK バージョン 3.0

- **アカウント**: ご使用の Azure サブスクリプションで、[Batch アカウント](accounts.md)を作成する必要があります。また、必要に応じて、Azure Storage アカウントを作成します。

- **サポートされている VM イメージ**: コンテナーは、サポートされているイメージ (次のセクションに示す) から仮想マシン構成を使用して作成されたプールでのみサポートされます。 カスタム イメージを提供する場合は、次のセクションの注意点と「[マネージ カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」の要件を参照してください。

次の制限事項にご注意ください。

- Batch では、Linux プールで実行されているコンテナーに対してのみ、RDMA サポートが提供されます。
- Windows コンテナー ワークロードの場合は、プールにマルチコア VM サイズを選択することをお勧めします。

## <a name="supported-virtual-machine-images"></a>サポートされている仮想マシン イメージ

コンテナー ワークロードのために VM コンピューティング ノードのプールを作成するには、次のいずれかのサポートされている Windows または Linux イメージを使用します。 Batch と互換性がある Marketplace イメージの詳細については、「[仮想マシン イメージの一覧](batch-linux-nodes.md#list-of-virtual-machine-images)」を参照してください。

### <a name="windows-support"></a>Windows のサポート

Batch は、コンテナー サポートの指定がある Windows サーバー イメージをサポートします。 通常、これらのイメージ SKU 名には、`-with-containers` または `-with-containers-smalldisk` というサフィックスが付きます。 また、[Batch でサポートされているすべてのイメージを一覧表示する API](batch-linux-nodes.md#list-of-virtual-machine-images) を使うと、イメージが Docker コンテナーをサポートしている場合は、`DockerCompatible` 機能が示されます。

Windows で Docker を実行している VM からカスタム イメージを作成することもできます。

### <a name="linux-support"></a>Linux Support

Linux コンテナー ワークロードの場合、現在、Batch は、Azure Marketplace で Microsoft Azure Batch によって発行された次の Linux イメージをサポートしており、カスタム イメージは必要ありません。

#### <a name="vm-sizes-without-rdma"></a>RDMA を使用しない VM サイズ

- 発行元: `microsoft-azure-batch`
  - オファー: `centos-container`
  - オファー: `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>RDMA を使用した VM サイズ

- 発行元: `microsoft-azure-batch`
  - オファー: `centos-container-rdma`
  - オファー: `ubuntu-server-container-rdma`

これらのイメージは、Azure Batch プールでの使用のみがサポートされており、Docker コンテナーの実行に適しています。 これらには以下が装備されています。

- プレインストールされた Docker 互換の [Moby](https://github.com/moby/moby) コンテナー ランタイム
- Azure N シリーズ VM へのデプロイを効率化するためにプレインストールされた NVIDIA GPU ドライバーと NVIDIA コンテナー ランタイム
- '-rdma' というサフィックスが付いた VM イメージは、InfiniBand RDMA VM サイズをサポートするように事前構成されています。 これらの VM イメージは、InfiniBand をサポートしていない VM サイズでは使用しないでください。

Docker を実行している VM から、Batch と互換性のある Linux ディストリビューションのいずれかでカスタム イメージを作成することもできます。 独自のカスタム Linux イメージを提供する場合は、「[マネージド カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)」の手順を参照してください。

カスタム イメージ上の Docker サポートの場合、[Docker Community Edition (CE)](https://www.docker.com/community-edition) または [Docker Enterprise Edition (EE)](https://www.docker.com/blog/docker-enterprise-edition/) をインストールします。

カスタム Linux イメージを使用するためのその他の注意点:

- カスタム イメージを使用する場合に Azure N シリーズ サイズの GPU パフォーマンスを活用するには、事前に NVIDIA ドライバーをインストールします。 また、NVIDIA GPU の Docker エンジン ユーティリティ、[NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker) をインストールする必要もあります。
- Azure RDMA ネットワークにアクセスするには、RDMA 対応の VM サイズを使用します。 必要な RDMA ドライバーは、Batch でサポートされている CentOS HPC イメージおよび Ubuntu イメージにインストールされます。 MPI ワークロードを実行するには、追加構成が必要になる可能性があります。 「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。

## <a name="container-configuration-for-batch-pool"></a>Batch プール用のコンテナー構成

Batch プールでコンテナー ワークロードを実行するには、プールの [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) オブジェクトに [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 設定を指定する必要があります。 (この記事では、Batch .NET API 参照へのリンクを提供しています。 対応する設定は、[Batch Python](/python/api/overview/azure/batch) API にあります。)

次の例のように、プリフェッチされたコンテナー イメージを使用して、または使用しないでコンテナー対応プールを作成できます。 プル (プリフェッチ) プロセスにより、インターネット上の別のコンテナー レジストリまたは Docker Hub のいずれかから、コンテナー イメージを事前に読み込むことができます。 最も高いパフォーマンスを得るには、Batch アカウントと同じリージョンにある [Azure コンテナー レジストリ](../container-registry/container-registry-intro.md)を使用します。

コンテナー イメージをプリフェッチするメリットは、初めてタスクを実行するとき、コンテナー イメージがダウンロードされるのを、そのタスクが待たなくてもよい点です。 プールの作成時に、コンテナー構成によって、コンテナー イメージが VM にプルされます。 これにより、プール上で実行されるタスクが、コンテナー イメージとコンテナー実行オプションの一覧を参照できます。

### <a name="pool-without-prefetched-container-images"></a>プリフェッチされたコンテナー イメージを使用しないプール

プリフェッチされたコンテナー イメージを使用せずにコンテナー対応プールを構成するには、次の例に示すように `ContainerConfiguration` オブジェクトと `VirtualMachineConfiguration` オブジェクトを定義します。 これらの例では、Marketplace から Azure Batch コンテナー プール イメージ用の Ubuntu Server を使用します。

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>コンテナー構成用にイメージをプリフェッチする

プールでコンテナー イメージをプリフェッチするには、コンテナー イメージの一覧 (Python の `container_image_names`) を `ContainerConfiguration` に追加します。

次の基本的な Python の例では、[Docker Hub](https://hub.docker.com) から標準 Ubuntu コンテナー イメージをプリフェッチする方法を示します。

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

次の C# の例では、TensorFlow イメージを [Docker Hub](https://hub.docker.com) からプリフェッチすると仮定しています。 この例には、プール ノード上の VM ホストで実行される開始タスクを含めています。 たとえば、コンテナーからアクセスできるファイル サーバーをマウントする目的で、ホストで開始タスクを実行することがあります。

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>プライベート コンテナー レジストリからイメージをプリフェッチする

プライベート コンテナー レジストリ サーバーの認証により、コンテナー イメージをプリフェッチすることもできます。 次の例では、`ContainerConfiguration` および `VirtualMachineConfiguration` オブジェクトは、プライベート TensorFlow イメージをプライベート Azure コンテナー レジストリからプリフェッチします。 イメージ参照は前の例と同じです。

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

### <a name="managed-identity-support-for-acr"></a>ACR のマネージド ID のサポート

[Azure Container Registry](https://azure.microsoft.com/services/container-registry) に格納されているコンテナーにアクセスする場合は、ユーザー名とパスワード、またはマネージド ID のいずれかを使用して、サービスの認証を行います。 マネージド ID を使用するには、まず、ID が[プールに割り当てられて](managed-identity-pools.md)おり、アクセスしたいコンテナー レジストリに割り当てられた `AcrPull` ロールがその ID に付与されている必要があります。 その後、ACR で認証するときに、使用する ID を Batch に指定します。

```csharp
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name" }
);

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>タスク用のコンテナー設定

コンテナーが有効なプール上でコンテナー タスクを実行するには、コンテナー固有の設定を指定します。 設定には、使用するイメージ、レジストリ、コンテナー実行オプションが含まれます。

- コンテナー固有の設定を構成するには、タスク クラスの `ContainerSettings` プロパティを使用します。 これらの設定は、[TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) クラスによって定義されます。 `--rm` コンテナー オプションは Batch によって処理されるため、追加の `--runtime` オプションは必要ありません。

- コンテナー イメージでタスクを実行する場合は、[クラウド タスク](/dotnet/api/microsoft.azure.batch.cloudtask)と[ジョブ マネージャー タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)にコンテナー設定が必要です。 ただし、[開始タスク](/dotnet/api/microsoft.azure.batch.starttask)、[ジョブの準備タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)、および[ジョブの解放タスク](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)にはコンテナー設定は不要です (つまり、コンテナーのコンテキスト内で、またはノード上で直接実行できます)。

- Windows の場合、タスクは [ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) を `admin` に設定して実行する必要があります。

- Linux の場合、Batch ではユーザーおよびグループの権限がコンテナーにマップされます。 コンテナー内の任意のフォルダーへのアクセスに管理者権限が必要な場合は、管理者の昇格レベルを使用して、プール スコープとしてタスクを実行する必要がある場合があります。 これにより、Batch がコンテナー コンテキストでルートとしてタスクを実行するようになります。 そうしないと、管理者以外のユーザーがこれらのフォルダーにアクセスできない可能性があります。

- GPU 対応ハードウェアを備えたコンテナー プールの場合、Batch ではコンテナー タスクに対して自動的に GPU が有効になります。そのため、`–gpus` 引数は含めないでください。

### <a name="container-task-command-line"></a>コンテナー タスクのコマンド ライン

コンテナー タスクを実行すると、Batch は自動的に [docker create](https://docs.docker.com/engine/reference/commandline/create/) コマンドを使用して、タスク内の指定されたイメージを使ってコンテナーを作成します。 次に、Batch はコンテナー内のタスク実行を制御します。

コンテナー以外の Batch タスクの場合と同様に、コンテナー タスクに対してコマンド ラインを設定します。 Batch は自動的にコンテナーを作成するため、コマンド ラインでは、コンテナー内で実行される 1 つまたは複数のコマンドを指定するだけでかまいません。

Batch タスクのコンテナー イメージが [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) スクリプトを使って構成されている場合は、コマンド ラインを設定して既定の ENTRYPOINT を使用するか、または上書きできます。

- コンテナー イメージの既定の ENTRYPOINT を使用するには、空の文字列 `""` にタスク コマンド ラインを設定します。

- 既定の ENTRYPOINT を上書きするため、またはイメージに ENTRYPOINT がない場合は、たとえば `/app/myapp` や `/bin/sh -c python myscript.py` のように、コンテナーに適したコマンド ラインを設定します。

オプションの [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) は、コンテナーを作成して実行するために Batch が使用する `docker create` コマンドに指定される追加の引数です。 たとえば、コンテナーの作業ディレクトリを設定するには、`--workdir <directory>` オプションを設定します。 追加のオプションについては、[docker create](https://docs.docker.com/engine/reference/commandline/create/) のリファレンスをご覧ください。

### <a name="container-task-working-directory"></a>コンテナー タスクの作業ディレクトリ

Batch コンテナー タスクは、コンテナー内の作業ディレクトリで実行されます。これは、通常の (コンテナーでない) タスク用に Batch が設定するディレクトリによく似ています。 この作業ディレクトリは、イメージ内に構成されている場合、または既定のコンテナーの作業ディレクトリ (Windows コンテナー上の `C:\`、または Linux コンテナー上の `/`) 内に構成されている場合、[WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) とは異なることに注意してください。

Batch コンテナー タスクの場合:

- ホスト ノード (Azure Batch ディレクトリのルート) 上の `AZ_BATCH_NODE_ROOT_DIR` 下にあるすべてのディレクトリが、コンテナー内に再帰的にマップされます。
- タスクの環境変数がすべて、コンテナー内にマップされます。
- ノード上にあるタスク作業ディレクトリ `AZ_BATCH_TASK_WORKING_DIR` が、通常タスクと同様に設定され、コンテナー内にマップされます。

これらのマッピングによって、コンテナー以外のタスクとほぼ同じように、コンテナー タスクを操作することが可能になります。 たとえば、アプリケーション パッケージを使用してアプリケーションをインストールし、Azure Storage からリソース ファイルにアクセスし、タスクの環境設定を使用し、コンテナーが停止した後はタスクの出力ファイルを永続化します。

### <a name="troubleshoot-container-tasks"></a>コンテナー タスクのトラブルシューティング

コンテナー タスクが正常に実行されない場合、状況に応じて、コンテナー イメージの WORKDIR または ENTRYPOINT 構成に関する情報を取得する必要があります。 構成を確認するには、[docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) コマンドを実行します。

必要な場合は、イメージに基づいてコンテナー タスクの設定を調整します。

- タスク コマンド ラインで絶対パスを指定します。 タスク コマンド ラインにイメージの既定の ENTRYPOINT が使用されている場合は、絶対パスが設定されていることを確認します。
- タスクのコンテナー実行オプションで、イメージの WORKDIR と一致するように作業ディレクトリを変更します。 たとえば、`--workdir /app` を設定します。

## <a name="container-task-examples"></a>コンテナー タスクの例

次の Python スニペットは、Docker Hub からプルされた架空のイメージを元に作成したコンテナーで実行される、基本のコマンド ラインを示しています。 ここで、`--rm` コンテナー オプションでは、タスクの終了後にコンテナーを削除し、`--workdir` オプションでは、作業ディレクトリを設定しています。 コマンド ラインは、ホスト上のタスク作業ディレクトリに小規模ファイルを書き込むシンプルなシェル コマンドを使って、コンテナーの ENTRYPOINT を上書きしています。

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

次の C# の例は、クラウド タスクの基本的なコンテナー設定を示しています。

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>次のステップ

- [Shipyard レシピ](https://github.com/Azure/batch-shipyard)を使用して Azure Batch でコンテナー ワークロードを簡単にデプロイする方法については、[Batch Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) ツールキットを参照してください。
- Linux での Docker CE のインストールおよび使用の詳細については、[Docker](https://docs.docker.com/engine/installation/) ドキュメントをご覧ください。
- [マネージド カスタム イメージを使用して仮想マシンのプールを作成する](batch-custom-images.md)方法について学習します。
- コンテナー ベースのシステムを作成するためのフレームワークである、[Moby プロジェクト](https://mobyproject.org/)について詳細をご確認ください。
