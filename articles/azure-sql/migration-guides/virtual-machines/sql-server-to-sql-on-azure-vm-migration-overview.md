---
title: SQL Server から Azure VM 上の SQL Server (移行の概要)
description: SQL Server を Azure VM 上の SQL Server に移行したい場合のさまざまな移行戦略について説明します。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 09/07/2021
ms.openlocfilehash: 163fb5cba55248fcc478e219a6b7c014fedbb689
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397796"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>移行の概要: SQL Server から Azure VM 上の SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

SQL Server を Azure Virtual Machines (VM) 上の SQL Server に移行するためのさまざまな移行戦略について説明します。 

オンプレミスまたは次で動作している SQL Server を移行できます。

- SQL Server on Virtual Machines  
- アマゾン ウェブ サービス (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)

その他の移行ガイドについては、[データベースの移行](/data-migration)に関するページを参照してください。 

## <a name="overview"></a>概要

使い慣れた SQL Server 環境を OS 制御付きで使用したい場合や、組み込みの VM 高可用性、[自動バックアップ](../../virtual-machines/windows/automated-backup.md)、[自動修正](../../virtual-machines/windows/automated-patching.md)など、クラウドで提供される機能を活用したい場合は、[Azure Virtual Machines (VM) 上の SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) に移行しましょう。 

[Azure ハイブリッド特典のライセンス モデル](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)を使用して独自のライセンスを持ち込み、コストを削減します。または、[無料のセキュリティ更新プログラム](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)を取得して、SQL Server 2008 と SQL Server 2008 R2 のサポートを延長します。 


## <a name="choose-appropriate-target"></a>適切なターゲットを選択する

Azure Virtual Machines は、Azure の多くの異なるリージョンで動作します。また、さまざまな[マシン サイズ](../../../virtual-machines/sizes.md)と[ストレージ オプション](../../../virtual-machines/disks-types.md)も備えています。 ご自分の SQL Server ワークロードについて適切なサイズの VM とストレージを特定する際は、「[Azure Virtual Machines 上の SQL Server のパフォーマンスに関するガイドライン](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#vm-size)」を参照してください。 ワークロードの VM サイズとストレージの要件を特定するには、 パフォーマンスベースの [Azure Migrate 評価](../../../migrate/concepts-assessment-calculation.md#types-of-assessments)を通じてそれらのサイズを設定することをお勧めします。 このオプションを使用できない場合は、[パフォーマンスのベースライン](https://azure.microsoft.com/services/virtual-machines/sql-server/)を独自に作成する方法について次の記事を参照してください。

VM 上の SQL Server の適切なインストールと構成にも考慮する必要があります。 [Azure SQL 仮想マシン イメージ ギャラリー](../../virtual-machines/windows/create-sql-vm-portal.md)では、適切なバージョン、エディション、オペレーティング システムで SQL Server VM を作成できるので、それを使用することをお勧めします。 さらに、そうすることで、Azure VM が SQL Server [リソース プロバイダー](../../virtual-machines/windows/create-sql-vm-portal.md)に自動的に登録され、自動バックアップや自動修正などの機能が有効になります。

## <a name="migration-strategies"></a>移行方法

ユーザー データベースを Azure VM 上の SQL Server のインスタンスに移行するには、**移行** と **リフト アンド シフト** の 2 つの移行戦略があります。 

ご自分のビジネスに適したアプローチは通常、次の要因によって決まります。 

- 移行のサイズとスケール
- 移行の速度
- アプリケーションのコード変更のサポート
- SQL Server バージョン、オペレーティング システムまたはその両方の変更の必要性。
- 既存の製品のサポート ライフ サイクル
- 移行中のアプリケーションのダウンタイム期間

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="仮想マシンの移行に伴うダウンタイム":::

次の表では、2 つの移行戦略の違いについて説明します。
<br />

| **移行戦略** | **説明** | **いつ使用するか** |
| --- | --- | --- |
| **リフト アンド シフト** | リフト アンド シフト戦略を使用して、物理または仮想の SQL Server 全体を、現在の場所から Azure VM 上の SQL Server のインスタンスに移動します。その際、オペレーティング システムや SQL Server バージョンに対する変更は行いません。 リフト アンド シフト移行を遂行するには、[Azure Migrate](../../../migrate/migrate-services-overview.md) に関するページを参照してください。 <br /><br /> ソースと宛先のサーバーでデータを同期している間、ソース サーバーはオンラインのままで要求に対応します。そのため、ほぼシームレスな移行が可能です。 | 単一から非常に大規模な移行まで使用でき、データ センターの終了などのシナリオにも適用可能です。 <br /><br /> ユーザーの SQL データベースまたはアプリケーションへのコード変更は最小限または不要で、移行全体を速やかに進められます。 <br /><br />[SSIS](/sql/integration-services/sql-server-integration-services)、[SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)、[SSAS](/analysis-services/analysis-services-overview) など、ビジネス インテリジェンス サービスを移行するために追加の手順が必要となることはありません。 |
|**移行** | ターゲット SQL Server やオペレーティング システムのバージョンをアップグレードしたい場合は、移行戦略を使用します。 <br /> <br /> Azure Marketplace の Azure VM、またはソース SQL Server のバージョンに一致する準備済み SQL Server イメージを選択します。 <br/> <br/> [Azure Data Studio 向け Azure SQL Migration 拡張機能](../../../dms/migration-using-azure-data-studio.md)を使用し、Azure 仮想マシンの SQL Server に SQL Server データベースを最小のダウンタイムで移行します。 | より新しいバージョンの SQL Server で利用可能な機能の使用が必要になったり望まれたりする場合、または、サポートされなくなったレガシ バージョンの SQL Server や OS をアップグレードする必要がある場合に使用します。  <br /> <br /> SQL Server のアップグレードをサポートするために、アプリケーションまたはユーザー データベースへの変更が必要な場合があります。 <br /><br />[ビジネス インテリジェンス](#business-intelligence) サービスが移行の対象になっている場合、その移行に関する追加の考慮事項がある可能性があります。 |


## <a name="lift-and-shift"></a>リフト アンド シフト  

次の表では、SQL Server データベースを Azure VM 上の SQL Server に移行する **リフト アンド シフト** 移行戦略で使用できる方法について詳しく説明します。
<br />

|**方法** | **最小ソース バージョン** | **最小ターゲット バージョン** | **ソースのバックアップ サイズ制限** |  **ノート** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM ストレージの制限](../../../index.yml) |  既存の SQL Server がそのまま、Azure VM 上の SQL Server のインスタンスに移動されます。 移行のワークロードは最大 35,000 VM にスケーリングできます。 <br /><br /> サーバー データの同期中、ソース サーバーがオンラインのままで要求に対応するので、ダウンタイムが最小限に抑えられます。 <br /><br /> **自動化とスクリプト**:[Azure Site Recovery のスクリプト](../../../migrate/how-to-migrate-at-scale.md)と [ Azure のスケーリングされた移行と計画の例](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

> [!NOTE]
> これで Azure Migrate を使用して、[フェールオーバー クラスター インスタンス](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)と[可用性グループ](sql-server-availability-group-to-sql-on-azure-vm.md)のソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 

## <a name="migrate"></a>Migrate  

設定が容易なため、ネイティブな SQL Server の[バックアップ](/sql/t-sql/statements/backup-transact-sql)をローカルで行ってからそのファイルを Azure にコピーすることが、移行アプローチとして推奨されます。 この方法でサポートされるのは、SQL Server のバージョンが 2008 以降のいずれかである大規模なデータベース (1 TB 超) と、大規模なデータベース バックアップ (1 TB 超) です。 ただし、1 TB 未満で Azure との接続性が高い SQL Server 2014 以降のデータベースでは、[URL への SQL Server バックアップ](/sql/relational-databases/backup-restore/sql-server-backup-to-url)の方が、より優れたアプローチになります。 

SQL Server データベースを Azure VM 上の SQL Server のインスタンスに移行する場合、ターゲット サーバーへのカットオーバーがアプリケーションのダウンタイム期間に影響するため、その必要性に応じてアプローチを選択することが重要です。

次の表では、SQL Server データベースを Azure VM 上の SQL Server に移行するために使用できるすべての方法について詳しく説明します。
<br />

|**方法** | **最小ソース バージョン** | **最小ターゲット バージョン** | **ソースのバックアップ サイズ制限** | **メモ** |
| --- | --- | --- | --- | --- |
| **[Azure Data Studio 用の Azure SQL Migration 拡張機能](../../../dms/migration-using-azure-data-studio.md)** | SQL Server 2005 | SQL Server 2008 | [Azure VM ストレージの制限](../../../index.yml) |  Azure 仮想マシンの SQL Server に SQL Server データベースを移行するには、Azure Data Studio でウィザード ベースの拡張機能を使用すると簡単です。 圧縮を使用して、転送するバックアップのサイズを最小限に抑えます。 <br /><br /> Azure Data Studio 向け Azure SQL Migration 拡張機能からは、簡単なユーザー インターフェイスで評価機能と移行機能の両方が提供されます。  |
| **[ファイルへのバックアップ](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM ストレージの制限](../../../index.yml) |  これは、マシン間でデータベースを移動する際の、十分にテストされたシンプルな手法です。 圧縮を使用して、転送するバックアップのサイズを最小限に抑えます。 <br /><br /> **自動化とスクリプト**:[Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) と [BLOB ストレージへの AzCopy](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[URL へのバックアップ](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| SQL Server 2016 の場合は 12.8 TB、それ以外の場合は 1 TB | Azure Storage を使用してバックアップ ファイルを VM に移動するもう 1 つの方法です。 圧縮を使用して、転送するバックアップのサイズを最小限に抑えます。 <br /><br /> **自動化とスクリプト**:[T-SQL またはメンテナンス プラン](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Database Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM ストレージの制限](../../../index.yml) |  [DMA](/sql/dma/dma-overview) では、オンプレミスの SQL Server を評価してから、SQL Server の最新バージョンにシームレスにアップグレードするか、Azure VM 上の SQL Server、Azure SQL Database、または Azure SQL Managed Instance に移行します。 <br /><br /> Filestream が有効なユーザー データベースでは使用しないようにしてください。<br /><br /> DMA は、[SQL と Windows ログイン](/sql/dma/dma-migrateserverlogins)を移行したり、[SSIS パッケージ](/sql/dma/dma-assess-ssis)を評価したりする機能も備えています。 <br /><br /> **自動化とスクリプト**:[コマンド ライン インターフェイス](/sql/dma/dma-commandline) |
| **[デタッチとアタッチ](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM ストレージの制限](../../../index.yml) | [Azure Blob Storage サービスを使用してこれらのファイルを格納](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)し、それらを Azure VM 上の SQL Server のインスタンスにアタッチする計画の場合 (特にデータベースが非常に大きいときに便利)、またはバックアップと復元にかかる時間が長すぎる場合に、この方法を使用します。 <br /><br /> **自動化とスクリプト**:[T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) と [BLOB ストレージへの AzCopy](../../../storage/common/storage-use-azcopy-v10.md)|
|**[ログ配布](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (Windows のみ) | SQL Server 2008 SP4 (Windows のみ) | [Azure VM ストレージの制限](../../../index.yml) | ログ配布では、オンプレミスから Azure VM 上の SQL Server のインスタンスにトランザクション ログ ファイルをレプリケートします。 <br /><br /> そうすることで、フェイルオーバー時のダウンタイムを最小限に抑えられます。また、Always On 可用性グループを設定するよりも構成のオーバーヘッドが少なくなります。 <br /><br /> **自動化とスクリプト**:[T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[分散型可用性グループ](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM ストレージの制限](../../../index.yml) |  [分散型可用性グループ](/sql/database-engine/availability-groups/windows/distributed-availability-groups)は、2 つの異なる可用性グループにまたがる、特殊な種類の可用性グループです。 分散型可用性グループに参加する可用性グループは、同じ場所にある必要がなく、クロスドメインに対応しています。 <br /><br /> この方法は使用するとダウンタイムを最小限に抑えられます。オンプレミスで構成された可用性グループがある場合に使用してください。 <br /><br /> **自動化とスクリプト**:[T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> - ネットワークのオプションが制限されていたり、なかったりする場合の大規模なデータ転送については、[接続に制限がある場合の大規模なデータ転送](../../../storage/common/storage-solution-large-dataset-low-network.md)に関するページを参照してください。
> - これで Azure Migrate を使用して、[フェールオーバー クラスター インスタンス](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)と[可用性グループ](sql-server-availability-group-to-sql-on-azure-vm.md)のソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 

### <a name="considerations"></a>考慮事項

次に示すのは、移行方法の確認時に考慮すべき重要ポイントの一覧です。

- 最適なデータ転送パフォーマンスを実現するには、圧縮されたバックアップ ファイルを使用して、データベースとファイルを Azure VM 上の SQL Server のインスタンスに移行します。 大規模なデータベースの場合、圧縮を行うほかに、[バックアップ ファイルを小さいファイルに分割](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server)して、バックアップおよび転送時のパフォーマンスを高めます。 
- SQL Server 2014 以上からの移行の場合、ネットワーク転送時にデータを保護するために[バックアップを暗号化](/sql/relational-databases/backup-restore/backup-encryption)することを検討してください。
- データベース移行時のダウンタイムを最小限に抑えるには、Always On 可用性グループのオプションを使用します。 
- 可用性グループを構成するオーバーヘッドを発生させずにダウンタイムを最小限に抑えるには、ログ配布のオプションを使用します。 
- ネットワーク オプションが制限されていたりなかったりする場合は、オフラインの移行方法を使用します (バックアップと復元や、Azure で利用可能な[ディスク転送サービス](../../../storage/common/storage-solution-large-dataset-low-network.md)など)。
- Azure VM 上の SQL Server で SQL Server のバージョンをさらに変更するには、[SQL Server エディションの変更](../../virtual-machines/windows/change-sql-server-edition.md)に関するページを参照してください。

## <a name="business-intelligence"></a>ビジネス インテリジェンス 

データベースの移行の範囲外の SQL Server Business Intelligence サービスを移行する場合は、付加的に考慮すべきことが生じる可能性があります。 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

以下の 2 つの方法のいずれかによって、SSISDB の SQL Server Integration Services (SSIS) パッケージとプロジェクトを Azure VM 上の SQL Server に移行できます。 

- ソース SQL Server インスタンスから SSISDB をバックアップして SQL Server Azure VM 上の SQL Server に復元します。 これにより、SSISDB 内のパッケージが、[Azure VM 上のターゲット SQL Server の Integration Services カタログ](/sql/integration-services/catalog/ssis-catalog)に復元されます。
- [デプロイ オプション](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)のいずれか 1 つを使って SSIS パッケージを Azure VM 上のターゲット SQL Server に再デプロイします。

SSIS パッケージをパッケージ配置モデルとしてデプロイしている場合は、移行する前に変換できます。 詳細については、[プロジェクト変換のチュートリアル](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)に関する記事を参照してください。 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Azure VM 上の SQL Server Reporting Services (SSRS) レポートをターゲット SQL Server に移行するには、「[Reporting Services インストールを移行する](/sql/reporting-services/install-windows/migrate-a-reporting-services-installation-native-mode)」をご覧ください。

代替方法として、SSRS レポートを Power BI 内のページ付けられたレポートに移行することもできます。  [RDL 移行ツール](https://github.com/microsoft/RdlMigration)を使用すると、レポートの準備と移行に役立ちます。 Microsoft では、ユーザーがレポート定義言語 (RDL) レポートを SSRS サーバーから Power BI に移行できるようにするために、このツールを開発しました。 GitHub から入手でき、移行シナリオのエンドツーエンドのチュートリアルが付属しています。 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services
SQL Server Analysis Services データベース (多次元モデルまたは表形式モデル) は、次のいずれかのオプションを使って、ソース SQL Server から Azure VM 上の SQL Server に移行できます:

-   SSMS の対話的使用
-   分析管理オブジェクト (AMO) のプログラム的使用
-   スクリプトによる XMLA (XML for Analysis) の使用

詳細については「[Analysis Services データベースを移行する](/analysis-services/multidimensional-models/move-an-analysis-services-database?view=asallproducts-allversions)」をご覧ください。

代替方法として、新しい XMLA 読み取り/書き込みエンドポイントを使ってオンプレミスの Analysis Services テーブル モデルを [Azure Analysis Services](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/) または [Power BI Premium](/power-bi/admin/service-premium-connect-tools) に移行することも検討できます。 

## <a name="server-objects"></a>サーバー オブジェクト

ソース SQL Server での設定によっては、SQL Server Management Studio を使って Transact-SQL (T-SQL) でスクリプトを生成し、さらにそのスクリプトを Azure VM 上のターゲット SQL Server 上で実行するという、Azure VM 上の SQL Server への移行を行うための手動による介入を必要とする、付加的な SQL Server の機能が存在する場合があります。 一般的に使用される機能の一部を次に示します:

- ログインとロール
- リンク サーバー
- 外部データ ソース
- エージェント ジョブ
- 警告
- データベース メール
- レプリケーション

## <a name="supported-versions"></a>サポートされているバージョン

SQL Server データベースを Azure VM 上の SQL Server に移行する準備を行う際は、SQL Server のバージョンがサポートされていることを必ず検討してください。 Azure VM 上で現在サポートされている SQL Server バージョンの一覧については、[Azure VM 上の SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms) に関するページを参照してください。

## <a name="migration-assets"></a>移行資産 

さらに支援が必要な場合は、実際の移行プロジェクトのために開発された次のリソースを参照してください。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://www.microsoft.com/download/details.aspx?id=103130)| このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。|
|[Logman を使用した Perfmon データ収集の自動化](https://www.microsoft.com/download/details.aspx?id=103114)|ベースライン パフォーマンスを把握するための Perfmon データを収集して、移行ターゲットの推奨に役立てるツール。 このツールは、リモート SQL Server で設定されたパフォーマンス カウンターを作成、開始、停止、削除するコマンドを作成するために、logman.exe を使用します。|
|[Multiple-SQL-VM-VNet-ILB](https://www.microsoft.com/download/details.aspx?id=103104)|このホワイトペーパーでは、SQL Server Always On 可用性グループの構成で複数の Azure 仮想マシンをセットアップする手順について説明しています。|
|[リージョンごとの Ultra SSD 対応 Azure 仮想マシン](https://www.microsoft.com/download/details.aspx?id=103105)|これらの PowerShell スクリプトは、Ultra SSD に対応した Azure 仮想マシンをサポートしているリージョンの一覧を取得するための、プログラムによるオプションを提供するものです。|

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ

SQL Server データベースを Azure VM 上の SQL Server に移行する作業を開始するには、[個別のデータベース移行ガイド](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)を参照してください。 

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関する記事を参照してください。

- Azure SQL の詳細については、以下を参照してください。
   - [デプロイ オプション](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM での SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- ライセンスの詳細については、以下を参照してください
   - [Azure ハイブリッド特典を使用するライセンス持ち込み](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 および SQL Server 2008 R2 の延長サポートの無料利用](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。
