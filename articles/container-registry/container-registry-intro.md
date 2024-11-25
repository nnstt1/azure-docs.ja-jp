---
title: マネージド コンテナー レジストリ
description: クラウド ベースの管理されたプライベート Docker レジストリを提供する Azure Container Registry サービスの紹介です。
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 6a05e0664bc7576f662d39e3ccd44d3258a84a9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337777"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure のプライベート Docker コンテナー レジストリの概要

Azure Container Registry は、オープンソースの Docker Registry 2.0 が基になっている管理されたプライベートな Docker レジストリ サービスです。 プライベート Docker コンテナー イメージおよび関連する成果物を保存、管理する Azure Container Registry を作成、管理します。

既存のコンテナーの開発およびデプロイ パイプラインで Azure Container Registry を使うか、Azure Container Registry タスクを使って Azure にコンテナー イメージをビルドします。 必要に応じてビルドするか､またはソースコードのコミットやベース イメージの更新などのトリガーでビルドを完全に自動化します｡

Docker とレジストリの概念について詳しくは、「[Docker overview (Docker の概要)](https://docs.docker.com/engine/docker-overview/)」および「[About registries, repositories, and images (レジストリ、リポジトリ、およびイメージについて)](container-registry-concepts.md)」をご覧ください。

## <a name="use-cases"></a>ユース ケース

Azure Container Registry からさまざまなデプロイ ターゲットにイメージをプルできます。

* [Kubernetes](https://kubernetes.io/docs/)、[DC/OS](https://docs.mesosphere.com/)、[Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/) など、ホストから成るクラスターにわたってコンテナー化されたアプリケーションを管理する **スケーラブルなオーケストレーション システム**。
* [Azure Kubernetes Service (AKS)](../aks/index.yml) や  [App Service](../app-service/index.yml)､[Batch](../batch/index.yml)､[Service Fabric](../service-fabric/index.yml) など､大規模なアプリケーション のビルドと実行をサポートしている **Azure サービス**｡

開発者は、コンテナー開発ワークフローの一環としてコンテナー レジストリにプッシュすることもできます。 たとえば、[Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) や [Jenkins](https://jenkins.io/) などの継続的インテグレーションと継続的配信ツールからコンテナー レジストリを対象にします。

ベース イメージが更新されたときに自動的にアプリケーション イメージをリビルドするように ACR タスクを構成するか、チームがコードを Git リポジトリにコミットしたときのイメージ ビルドを自動化します。 複数のコンテナー イメージのビルド、テスト、および修正プログラム適用をクラウドで並列で自動的に行うために、マルチステップ タスクを作成します。

Azure には、Azure Container Registry を管理するために、Azure コマンド ライン インターフェイス、Azure portal、API サポートなどのツールが用意されています。 Azure Container Registry を操作するには、必要に応じて [Visual Studio Code 用の Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)と [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能をインストールします。 Azure Container Registry との間でイメージをプルおよびプッシュしたり、ACR タスクを実行したりします。すべて Visual Studio Code 内で実行します。

## <a name="key-features"></a>主要な機能

* **レジストリのサービス レベル** - Azure サブスクリプションに 1 つまたは複数のコンテナー レジストリを作成します。 レジストリは次の 3 つのレベルで利用できます。[Basic、Standard、Premium](container-registry-skus.md) の 3 つの SKU があり、それぞれ Webhook 統合、Azure Active Directory によるレジストリ認証、および削除機能がサポートされます。 デプロイと同じ Azure の場所にレジストリを作成することで、ネットワーク上の近い場所にローカルで保存されたコンテナー イメージを活用します。 高度なレプリケーションとコンテナー イメージの配布に対応するには、Premium レジストリの [geo レプリケーション](container-registry-geo-replication.md)機能を使用してください。 

* **セキュリティとアクセス** - Azure CLI または標準の `docker login` コマンドを使用して、レジストリにログインします。 Azure Container Registry は、HTTPS でコンテナー イメージを転送し、TLS によるクライアント接続のセキュリティ保護をサポートします。 

  > [!IMPORTANT]
  > 2020 年 1 月 13 日以降、Azure Container Registry では、サーバーとアプリケーションからのセキュリティで保護されたすべての接続で TLS 1.2 を使用する必要があります。 最新の docker クライアント (バージョン 18.03.0 以降) を使用して TLS 1.2 を有効にします。 TLS 1.0 と 1.1 のサポートは、廃止される予定です。 

  コンテナー レジストリへの[アクセスを制御](container-registry-authentication.md)するには、Azure ID、Azure Active Directory でサポートされている[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)、または提供された管理者アカウントを使用します。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、レジストリに対するきめ細かなアクセス許可をユーザーまたはシステムに割り当てます。

  Premium サービス レベルのセキュリティ機能には、イメージ タグに署名するための[コンテンツの信頼](container-registry-content-trust.md)や、レジストリへのアクセスを制限する[ファイアウォールと仮想ネットワーク (プレビュー)](container-registry-vnet.md) などがあります。 Azure Container Registry に必要に応じて Microsoft Defender for Cloud を統合すれば、イメージがレジストリにプッシュされるたびに、[イメージがスキャン](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json)されます。

* **サポートされるイメージと成果物** - リポジトリにグループ化されます。各イメージは、Docker 互換コンテナーの読み取り専用のスナップショットです。 Azure Container Registry には、Windows と Linux の両方のイメージを含めることができます。 すべてのコンテナーのデプロイのイメージ名を制御できます。 イメージをリポジトリにプッシュしたり、イメージをリポジトリからプルしたりするには、標準の [Docker コマンド](https://docs.docker.com/engine/reference/commandline/)を使用します。 Azure Container Registry は、Docker コンテナー イメージに加えて、[Helm チャート](container-registry-helm-repos.md)のような[関連コンテンツの形式](container-registry-image-formats.md)および [Open Container Initiative (OCI) のイメージ形式の仕様](https://github.com/opencontainers/image-spec/blob/master/spec.md)に基づいて構築されたイメージを格納します。

* **イメージ ビルドの自動化** - Azure でのイメージのビルド、テスト、プッシュ、デプロイを効率化するには、[Azure Container Registry タスク](container-registry-tasks-overview.md) (ACR タスク) を使います。 たとえば、ACR タスクを使って､`docker build` 操作を Azure にオフロードすることで、開発の社内ループをクラウドに拡張します。 ビルド タスクの設定では､コンテナーの OS およびフレームワーク パッチ適用 パイプラインを自動化し､ソース コントロールにコードがコミットされたときに自動的にイメージがビルドされるよう指定できます｡

  [マルチステップ タスク](container-registry-tasks-overview.md#multi-step-tasks)では、クラウドでのコンテナー イメージのビルド、テスト、および修正プログラムの適用のために、ステップベースでタスクの定義と実行を行うことができます。 タスクのステップでは、コンテナー イメージのビルド操作とプッシュ操作を個々に定義します。 各ステップで実行環境としてコンテナーを使用するように、1 つまたは複数のコンテナーの実行を定義することもできます。

## <a name="next-steps"></a>次のステップ

* [Azure Portal を使用したコンテナー レジストリの作成](container-registry-get-started-portal.md)
* [Azure CLI を使用したコンテナー レジストリの作成](container-registry-get-started-azure-cli.md)
* [ACR タスクでコンテナーのビルドとメンテナンスを自動化する](container-registry-tasks-overview.md)
