---
title: Azure Migrate の新着情報
description: Azure Migrate サービスの最新の情報や最近行われた更新について説明します。
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 08/04/2021
ms.custom: mvc
ms.openlocfilehash: bf10074292dd123a7f732c2afc11a28edbedb3ed
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130068876"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate の新着情報

[Azure Migrate](migrate-services-overview.md) を使用すると、オンプレミスのサーバー、アプリ、データを検出して評価し、Microsoft Azure クラウドに移行するのに役立ちます。 この記事では、Azure Migrate の新しいリリースと機能について概要を説明します。

## <a name="update-october-2021"></a>更新 (2021 年 10 月)
- Azure Migrate では、パブリック クラウドの新しい地域とリージョンがサポートされるようになりました。 [詳細情報](migrate-support-matrix.md#supported-geographies-public-cloud)

## <a name="update-september-2021"></a>更新 (2021 年 9 月)
- [Azure Private Link](../private-link/private-endpoint-overview.md) を使用してプライベート ネットワーク上でサーバーを検出、評価、移行する機能が現在、  サポートされている[政府機関向けクラウド](migrate-support-matrix.md#supported-geographies-azure-government)でプレビュー段階です。 [詳細情報](how-to-use-azure-migrate-with-private-endpoints.md)
- PowerShell を利用し、エージェントレス VMware VM 移行のリソースにタグを付けたり、カスタム名を追加したりできるようになりました。
- Azure Migrate アプライアンス: 物理サーバーの検出一覧からサーバーを削除するオプション。

## <a name="update-august-2021"></a>更新 (2021 年 8 月)

- VMware 環境 の IIS サーバーで実行されている ASP.NET Web アプリの大規模な検出と評価は、現在プレビュー段階です。 [詳細については、こちらを参照してください](concepts-azure-webapps-assessment-calculation.md)。 始めるには、[検出](tutorial-discover-vmware.md)と[評価](tutorial-assess-webapps.md)に関するチュートリアルを参照してください。
- Azure VM 評価の推奨事項での Azure [Ultra Disks](../virtual-machines/disks-types.md#ultra-disks) のサポート。
- VMware 仮想マシンの大規模なソフトウェア インベントリおよびエージェントレスの依存関係の分析の一般提供。
- Azure Migrate アプライアンスの更新:
    - ユーザーがアプライアンスの問題を特定して自己評価できるようにするアプライアンスの "診断と解決"。
    - 統合インストーラー スクリプト - ユーザーがシナリオ、クラウド、および接続のオプションから選択して、必要な構成でアプライアンスをデプロイする必要がある共通スクリプト。
    - Linux サーバーの検出を実行するためにアプライアンス構成マネージャーに、(ルート アカウントを指定したり、setcap アクセス許可を有効にしたりする代わりに) "sudo" アクセス権を持つユーザー アカウントを追加するためのサポート。
    - アプライアンス構成マネージャーでの SQL Server 接続プロパティの編集のサポート。

## <a name="update-july-2021"></a>更新 (2021 年 7 月)

- Azure Migrate: アプリ コンテナ化ツールを使用して、サーバー上で実行されているアプリケーションをパッケージ化してコンテナー イメージを作成したり、コンテナ化されたアプリケーションを Azure Kubernetes Service に加えて Azure App Service コンテナーにデプロイしたりできるようになりました。 また、Java アプリのアプリケーション監視を Azure Application Insights と自動的に統合し、Azure Key Vault を使用して、証明書やパラメーター化された構成などのアプリケーション シークレットを管理することもできます。 詳細については、入門編チュートリアルの「[ASP.NET アプリのコンテナ化と Azure App Service への移行](tutorial-app-containerization-aspnet-app-service.md)」と「[Java Web アプリのコンテナ化と Azure App Service への移行](tutorial-app-containerization-java-app-service.md)」を参照してください。

## <a name="update-june-2021"></a>更新 (2021 年 6 月)

- Azure Migrate では、パブリック クラウドの新しい地域とリージョンがサポートされるようになりました。 [詳細情報](migrate-support-matrix.md#supported-geographies-public-cloud)
- Azure Migrate を使用すると、SQL Server を実行するサーバーをレプリケーション時に SQL VM RP に登録して、SQL IaaS Agent 拡張機能を自動的にインストールできます。 この機能は、エージェントレス VMware、エージェントレス Hyper-V、エージェントベースの移行で使用できます。
- CSV ファイルをインポートして評価するとき、最大 20 個のディスクがサポートされるようになりました。 以前は、サーバーあたり 8 個のディスクに制限されていました。

## <a name="update-may-2021"></a>更新 (2021 年 5 月)

- OS ディスクが 4 TB までの VM と物理サーバーの移行が、エージェントベースの移行方法を使用してサポートされるようになりました。

## <a name="update-march-2021"></a>更新 (2021 年 3 月)

- Azure Migrate アプライアンスでの複数のサーバー資格情報の指定によるインストールされているアプリケーション (ソフトウェア インベントリ) の検出、エージェントレスの依存関係分析、および VMware 環境の SQL Server インスタンスおよびデータベースの検出のサポート。 [詳細情報](tutorial-discover-vmware.md#provide-server-credentials)
- VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 詳細は[こちら](concepts-azure-sql-assessment-calculation.md)をご覧ください。始めるには、[検出](tutorial-discover-vmware.md)と[評価](tutorial-assess-sql.md)に関するチュートリアルを参照してください。
- エージェントレスの VMware 移行では、vCenter あたり 500 台の VM を同時にレプリケートできるようになりました。
- Azure Migrate: アプリのコンテナ化ツールを利用すると、サーバー上で実行されているアプリケーションをパッケージ化してコンテナー イメージを作成したり、コンテナ化されたアプリケーションを Azure Kubernetes Service にデプロイしたりできます。  
詳細については、入門編チュートリアルの「[ASP.NET アプリのコンテナ化と Azure Kubernetes Service への移行](tutorial-app-containerization-aspnet-kubernetes.md)」と「[Java Web アプリのコンテナ化と Azure Kubernetes Service への移行](tutorial-app-containerization-java-kubernetes.md)」を参照してください。

## <a name="update-january-2021"></a>更新 (2021 年 1 月)

- Azure Migrate: カスタマー マネージド キー (CMK) によるサーバー側暗号化でディスクが暗号化された Azure 仮想マシンに、Server Migration ツールを使用して、VMware 仮想マシンや物理サーバー、さらに他のクラウドの仮想マシンを移行できるようになりました。

## <a name="update-december-2020"></a>更新 (2020 年 12 月)

- Azure Migrate で、エージェントレスの移行手法を使用して VMware VM を Azure に移行する間、VMware VM に Azure VM エージェントが自動的にインストールされるようになりました。 (Windows Server 2008 R2 以降)
- サーバー側暗号化 (SSE) とカスタマー マネージド キー (CMK) によってディスクが暗号化された Azure 仮想マシンに対し、Azure Migrate Server Migration (エージェントレス レプリケーション) を使用して VMware VM を移行する方法が Azure portal から利用できるようになりました。

## <a name="update-september-2020"></a>更新 (2020 年 9 月)

- サーバーを Availability Zones に移行できるようになりました。
- UEFI ベースの VM と物理サーバーを Azure 第 2 世代 VM に移行できるようになりました。 今回のリリースでは、Azure Migrate: Server Migration ツールは、移行中に Gen 2 VM から Gen 1 VM への変換は実行しません。
- 新しい Azure Migrate Power BI 評価ダッシュボードを使用して、さまざまな評価設定の間でコストを比較することができます。 ダッシュボードには、評価を自動的に作成する PowerShell ユーティリティが付属し、評価は Power BI ダッシュボードにプラグインされます。 [詳細情報。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- 1000 台の VM で同時に依存関係の分析 (エージェントレス) を実行できるようになりました。
- PowerShell スクリプトを使用して、大規模な依存関係の分析 (エージェントレス) を有効または無効にすることができるようになりました。 [詳細情報。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- 依存関係の分析 (エージェントレス) によって収集されたデータを使用して、Power BI でネットワーク接続を視覚化できます。[詳細情報。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- データ ディスクのサイズが最大 32 TB の VMware VM の移行は、Azure Migrate:Server Migration のエージェントレス VMware 移行方式を使用してサポートされるようになりました。

## <a name="update-august-2020"></a>更新 (2020 年 8 月)

- Azure Migrate プロジェクト キーがポータルから生成され、アプライアンスの登録を完了するために使用される、オンボード エクスペリエンスが向上しました。
- VMware アプライアンスまたは Hyper-V アプライアンスを設定するために、ポータルからそれぞれ OVA か VHD ファイルまたはインストーラー スクリプトをダウンロードするオプション。
- ユーザー エクスペリエンスが向上した、更新されたアプライアンス構成マネージャー。
- Hyper-V VM の検出での、複数の資格情報のサポート。

## <a name="update-july-2020"></a>更新 (2020 年 7 月)

- エージェントレスの VMware 移行では、vCenter あたり 300 台の VM を同時にレプリケートできるようになりました

## <a name="update-june-2020"></a>更新 (2020 年 6 月)

- オンプレミス VMware VM を [Azure VMware Solution (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) に移行するための評価がサポートされるようになりました。 [詳細情報](how-to-create-azure-vmware-solution-assessment.md)
- 物理サーバーを検出するための、アプライアンスでの複数の資格情報のサポート。
- テナント制限が構成されているテナントのアプライアンスからの Azure ログインを許可するためのサポート。

## <a name="update-april-2020"></a>更新 (2020 年 4 月)

Azure Migrate では、Azure Government へのデプロイがサポートされます。

- VMware VM、Hyper-V VM、物理サーバーを検出して評価することができます。
- VMware VM、Hyper-V VM、物理サーバーを Azure に移行できます。
- VMware の移行では、エージェントレスまたはエージェントベースの移行を使用できます。 [詳細については、こちらを参照してください](server-migrate-overview.md)。
- Azure Government でサポートされている地域やリージョンを[確認](migrate-support-matrix.md#supported-geographies-azure-government)します。
- [エージェントベースの依存関係の分析](concepts-dependency-visualization.md#agent-based-analysis)は、Azure Government ではサポートされません。
- Azure Government でプレビュー段階の機能 ([エージェントレスの依存関係の分析](concepts-dependency-visualization.md#agentless-analysis)と[アプリケーション検出](how-to-discover-applications.md)) がサポートされます。

## <a name="update-march-2020"></a>更新 (2020 年 3 月)

スクリプト ベースのインストールを使用して、[Azure Migrate アプライアンス](migrate-appliance.md)をセットアップできるようになりました。

- スクリプト ベースのインストールは、アプライアンスの .OVA (VMware) および VHD (Hyper-V) インストールに対する代替手段です。
- Windows Server 2016 が実行されている既存のコンピューターに VMware および Hyper-V 用のアプライアンスを設定するために使用できる PowerShell インストーラー スクリプトが提供されます。

## <a name="update-november-2019"></a>更新 (2019 年 11 月)

Azure Migrate に新機能がたくさん追加されました。

- **物理サーバーの評価**。 既にサポートされている物理サーバーの移行に加えて、オンプレミスの物理サーバーの評価がサポートされるようになりました。
- **インポートベースの評価**。 CSV ファイルで提供されるメタデータとパフォーマンス データを使用したコンピューターの評価がサポートされるようになりました。
- **アプリケーションの検出**:Azure Migrate では、Azure Migrate アプライアンスを使用したアプリ、ロール、機能のアプリケーションレベルの検出がサポートされるようになりました。 この機能は現在、VMware VM でのみサポートされており、検出のみに制限されています (評価は現在サポートされていません)。 [詳細情報](how-to-discover-applications.md)
- **エージェントレスの依存関係の視覚化**:依存関係の視覚化のためにエージェントを明示的にインストールする必要がなくなりました。 エージェントレスとエージェントベースの両方がサポートされるようになりました。
- **仮想デスクトップ**:オンプレミスの仮想デスクトップ インフラストラクチャ (VDI) を評価して Azure の Windows Virtual Desktop に移行するには、ISV のツールを使用します。
- **Web アプリ**:Web アプリの評価と移行に使用される Azure App Service Migration Assistant が Azure Migrate に統合されました。

Azure Migrate に新しい評価ツールと移行ツールが追加されました。

- **RackWare**: クラウドへの移行を支援します。
- **Movere**: 評価を支援します。

Azure Migrate での評価と移行については、ツールと ISV 製品の[詳細](migrate-services-overview.md)を参照してください。

## <a name="azure-migrate-current-version"></a>Azure Migrate の現在のバージョン

Azure Migrate の現在のバージョン (2019 年 7 月リリース) には、新機能がたくさんあります。

- **統合された移行プラットフォーム**:Azure Migrate では、Azure への移行過程を一元化、管理、追跡するための、デプロイのフローとポータル エクスペリエンスが向上した、単一のポータルが提供されるようになりました。
- **評価と移行のツール**: Azure Migrate では、ネイティブ ツールが提供され、他の Azure サービスおよび独立系ソフトウェア ベンダー (ISV) のツールが統合されます。 ISV の統合について、[詳しくはこちらをご覧ください](migrate-services-overview.md#isv-integration)。
- **Azure Migrate の評価**: Azure Migrate Server Assessment ツールを使用すると、Azure への移行に関して VMware VM と Hyper-V VM を評価できます。 また、他の Azure サービスおよび ISV ツールを使用する移行についても評価できます。
- **Azure Migrate の移行**: Azure Migrate Server Migration ツールを使用すると、オンプレミスの VMware VM と Hyper-V VM を、Azure だけでなく、物理サーバー、他の仮想化サーバー、プライベート/パブリッククラウド VM にも移行できます。 さらに、ISV ツールを使用して Azure に移行することもできます。
- **Azure Migrate アプライアンス**: Azure Migrate では、オンプレミスの VMware VM と Hyper-V VM の検出と評価のために、軽量のアプライアンスがデプロイされます。
    - このアプライアンスは、Azure Migrate Server Assessment と、エージェントレスの移行のための Azure Migrate Server Migration によって使用されます。
    - アプライアンスでは、評価と移行のために、サーバーのメタデータとパフォーマンス データが継続的に検出されます。  
- **VMware VM の移行**: Azure Migrate Server Migration では、オンプレミスの VMware VM を Azure に移行するために、2 つの方法が提供されています。  Azure Migrate アプライアンスを使用するエージェントレスの移行と、レプリケーション アプライアンスを使用し、移行する各 VM にエージェントが展開されるエージェントベースの移行です。 [詳細情報](server-migrate-overview.md)
 - **データベースの評価と移行**: Azure Migrate では、Azure Database Migration Assistant を使用して、Azure への移行についてオンプレミスのデータベースを評価できます。 Azure Database Migration Service を使用してデータベースを移行できます。
- **Web アプリの移行**: Azure App Service のパブリック エンドポイント URL を使用して、Web アプリを評価できます。 内部 .NET アプリの移行の場合は、App Service Migration Assistant をダウンロードして実行できます。
- **Data Box**: Azure Migrate で Azure Data Box を使用して、大量のオフライン データを Azure にインポートします。

## <a name="azure-migrate-previous-version"></a>Azure Migrate の以前のバージョン

旧バージョンの Azure Migrate (オンプレミスの VMware VM の評価のみサポート) を使用している場合、今後は最新バージョンを使用してください。 前のバージョンでは、新しい Azure Migrate プロジェクトを作成したり、新しい検出を実行したりできなくなりました。 既存のプロジェクトには引き続きアクセスできます。 それを行うには、Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。 Azure Migrate の通知には、古い Azure Migrate プロジェクトにアクセスするためのリンクがあります。

## <a name="next-steps"></a>次のステップ

- Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
- [VMware VM](./tutorial-assess-vmware-azure-vm.md) と [Hyper-V VM](tutorial-assess-hyper-v.md) を評価するチュートリアルをお試しください。