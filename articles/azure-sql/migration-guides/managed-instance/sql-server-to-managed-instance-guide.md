---
title: 'SQL Server から Azure SQL Managed Instance へ: 移行ガイド'
description: このガイドでは SQL Server データベースの Azure SQL Managed Instance への移行を説明します。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 06/25/2021
ms.openlocfilehash: d00eb47a1e366d5ae9f3ba559a65e57f5c9c9baa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044004"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>移行ガイド: SQL Server から Azure SQL Managed Instance へ
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

このガイドに従って、SQL Server データベースを Azure SQL Managed Instance に移行します。 

オンプレミスまたは次で実行されている SQL Server を移行できます。 

- SQL Server on Virtual Machines  
- アマゾン ウェブ サービス (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform – GCP) 

移行の詳細については、[移行の概要](sql-server-to-managed-instance-overview.md)に関するページを参照してください。 その他の移行ガイドについては、[データベースの移行](/data-migration)に関するページを参照してください。 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="移行プロセス フロー":::

## <a name="prerequisites"></a>前提条件 

SQL Server を Azure SQL Managed Instance に移行するには、以下を完了していることを確認します。 

- [移行方法](sql-server-to-managed-instance-overview.md#compare-migration-options)と、それに対応するツールの選択。
- ソース SQL Server に接続できるコンピューターへの [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) のインストール。
- 移行先となる [Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md) の作成。
- ソースとターゲットの両方にアクセスするための接続と、適切なアクセス許可の構成。 
- [Azure SQL Managed Instance で使用できる](../../database/features-comparison.md) SQL Server データベース エンジンの機能の確認。 


## <a name="pre-migration"></a>移行前

ソース環境がサポートされていることを確認した後、移行前ステージから開始します。 既存のデータ ソースをすべて検出し、移行が可能かどうかを評価し、移行の阻害要素となる可能性のある問題を特定します。  

### <a name="discover"></a>発見

検出フェーズでは、ネットワークをスキャンして、組織で使用されているすべての SQL Server インスタンスと機能を特定します。 

[Azure Migrate](../../../migrate/migrate-services-overview.md) を使用して、オンプレミス サーバーの移行の適合性を評価し、パフォーマンスに基づくサイズ変更を行い、Azure でそれらのサーバーを実行するためのコストを見積もります。 

または、 [Microsoft Assessment and Planning Toolkit ("MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) を使用して、現在の IT インフラストラクチャを評価します。 ツールキットには、移行計画のプロセスを簡略化するための強力なインベントリ、評価、およびレポート ツールが用意されています。 

検出フェーズで使用できるツールの詳細については、「[データ移行のシナリオで利用できるサービスとツール](../../../dms/dms-tools-matrix.md)」を参照してください。 

データ ソースが検出された後、Azure SQL Managed Instance に移行できるオンプレミスの SQL Server インスタンスを評価して、移行の阻害要素や互換性の問題を特定します。
次の手順に進んで、データベースを評価し、Azure SQL Managed Instance に移行します。

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-sql-managed-instance-steps.png" alt-text="Azure SQL Managed Instance への移行手順":::

- [Assess SQL Managed Instance の互換性を評価する](#assess)。ここでは、移行を妨げる可能性のある、障害となっている問題がないことを確認する必要があります。
  この手順には、ソース SQL Server インスタンスのリソース使用率を判断するための[パフォーマンス ベースライン](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline)の作成も含まれます。 この手順は、適切なサイズに設定された Managed Instance をデプロイし、移行後のパフォーマンスに影響がないことを確認する場合に必要です。
- [アプリの接続性オプションを選択する](../../managed-instance/connect-application-instance.md)。
- [最適なサイズに設定されたマネージド インスタンスにデプロイする](#deploy-to-an-optimally-sized-managed-instance)。ここでは、マネージド インスタンスの技術的特性 (仮想コア数、メモリ容量) とパフォーマンス レベル (Business Critical、General Purpose) を選択します。
- [移行方法の選択と移行](sql-server-to-managed-instance-overview.md#compare-migration-options)。ここでは、オフライン移行またはオンライン移行のオプションを使用してデータベースを移行します。
- [アプリケーションの監視と修復](#monitor-and-remediate-applications)を行い、期待されるパフォーマンスが得られることを確認します。


### <a name="assess"></a>評価 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

SQL Managed Instance がアプリケーションのデータベース要件に適合しているかどうかを確認します。 SQL Managed Instance は、SQL Server を使用する既存のアプリケーションの大部分について簡単なリフト アンド シフト移行を提供するように設計されています。 ただし、まだサポートされていない機能が必要で、回避策を実装するコストが高すぎる場合があります。 

Data Migration Assistant (バージョン 4.1 以降) を使用してデータベースを評価し、以下を得ることができます。 

- [Azure のターゲットに関する推奨事項](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure の SKU に関する推奨事項](/sql/dma/dma-sku-recommend-sql-db)

Database Migration Assessment を使用して環境を評価するには、次の手順に従います。 

1. [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) を開きます。 
1. **[ファイル]** を選択し、 **[新しい評価]** を選択します。 
1. プロジェクト名を指定し、[ソース サーバーの種類] として [SQL Server] を選択し、[ターゲット サーバーの種類] として [Azure SQL Managed Instance] を選択します。 
1. 生成する評価レポートの種類を選択します。 たとえば、データベースの互換性、機能パリティなどがあります。 評価の種類によっては、ソース SQL Server で必要となるアクセス許可が異なる場合があります。  この評価を実行する前に、選択したアドバイザーに必要なアクセス許可が DMA によって強調表示されます。
    - **[機能パリティ]** カテゴリで、包括的な一連の推奨事項、Azure で使用可能な代替手段、および移行プロジェクトの計画に役立つ軽減手順を確認できます。 (sysadmin 権限が必要)
    - **[互換性の問題]** カテゴリでは、移行を阻害する可能性のある、部分的にサポートされている機能またはサポートされていない機能の互換性の問題を確認でき、さらにそれらに対処するための推奨事項を確認できます (`CONNECT SQL`、`VIEW SERVER STATE`、および `VIEW ANY DEFINITION` 権限が必要)。
1. SQL Server のソース接続の詳細を指定し、ソース データベースに接続します。
1. **[評価の開始]** を選択します。 
1. プロセスが完了したら、移行の阻害要素と機能パリティの問題に関する評価レポートを選択し、確認します。 評価レポートはファイルにエクスポートすることもできます。このファイルを、組織内の他のチームや担当者と共有できます。 
1. 移行後の作業が最小限になるようなデータベース互換レベルを特定します。  
1. オンプレミスのワークロードに最適な Azure SQL Managed Instance SKU を特定します。 

詳細については、「[Data Migration Assistant を使用した SQL Server 移行評価の実行](/sql/dma/dma-assesssqlonprem)」を参照してください。

SQL Managed Instance がお客様のワークロードに適したターゲットではない場合、Azure VM 上の SQL Server が、お客様のビジネスに適した代替ターゲットである可能性があります。

#### <a name="scaled-assessments-and-analysis"></a>スケーリングされた評価と分析

Data Migration Assistant は、スケーリングされた評価の実行と、分析用の評価レポートの統合をサポートしています。 複数のサーバーとデータベースがあり、それらを大規模に評価および分析して、より広い視点からデータ資産を確認する必要がある場合は、次のリンクをクリックして詳細を確認してください。

- [PowerShell を使用してスケーリングされた評価を実行する](/sql/dma/dma-consolidatereports)
- [Power BI を使用して評価レポートを分析する](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>複数のデータベースの大規模な評価は、[DMA のコマンド ライン ユーティリティ](/sql/dma/dma-commandline)を使用して自動化することもできます。また、[Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) に結果をアップロードして、さらに分析を行うことや、ターゲットの準備を行うこともできます。

### <a name="deploy-to-an-optimally-sized-managed-instance"></a>最適なサイズに設定されたマネージド インスタンスにデプロイする

検出および評価フェーズの情報に基づいて、適切なサイズのターゲット SQL Managed Instance を作成します。 これを行うには、[Azure portal](../../managed-instance/instance-create-quickstart.md)、[PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)、または [Azure Resource Manager (ARM) テンプレート](../../managed-instance/create-template-quickstart.md)を使用します。

SQL Managed Instance は、クラウドへの移行を予定しているオンプレミスのワークロード向けに調整されます。 ワークロードのリソースの適切なレベルの選択において高い柔軟性を発揮する[購入モデル](../../database/service-tiers-vcore.md)が導入されます。 オンプレミスの世界では、物理コアと IO 帯域幅を使用して、これらのワークロードのサイズを設定することがおそらく一般的です。 マネージド インスタンスの購入モデルは仮想コア ("vCore") に基づいており、追加のストレージと IO を個別に使用できます。 仮想コア モデルは、クラウドのコンピューティング要件と現在オンプレミスで使用しているものを把握するためのシンプルな方法です。 この購入モデルにより、クラウド内の移行先環境を適切にサイズ設定することができます。 適切なサービス レベルと特性を選択するために役立ついくつかの一般的なガイドラインを次に示します。

- ベースラインの CPU 使用率に基づいて、SQL Server で使っているコアの数と一致するマネージド インスタンスをプロビジョニングできます。そのとき、[マネージド インスタンスがインストールされている VM の特性](../../managed-instance/resource-limits.md#hardware-generation-characteristics)と一致するように CPU の特性をスケーリングすることが必要になる場合があることに留意します。
- ベースラインのメモリ使用量に基づいて、[対応するメモリを備えたサービス レベル](../../managed-instance/resource-limits.md#hardware-generation-characteristics)を選択します。 メモリの量は直接選択できないため、一致するメモリ (Gen5 の 5.1 GB/仮想コアなど) を備えた仮想コアの容量を持つマネージド インスタンスを選択する必要があります。
- ファイル サブシステムのベースライン IO 待機時間に基づいて、General Purpose (5 ミリ秒を超える待機時間) と Business Critical (3 ミリ秒未満の待機時間) のサービス レベルのいずれかを選択します。
- 予期される IO パフォーマンスを得るために、ベースラインのスループットに基づいて、データ ファイルまたはログ ファイルのサイズを事前に割り当てます。

コンピューティング リソースとストレージ リソースをデプロイ時に選択し、後で [Azure portal](../../database/scale-resources.md) を使用してアプリケーションのダウンタイムなしに変更できます。

:::image type="content" source="media/sql-server-to-managed-instance-overview/managed-instance-sizing.png" alt-text="マネージド インスタンスのサイズ設定":::

VNet インフラストラクチャとマネージド インスタンスを作成する方法については、[マネージド インスタンスの作成](../../managed-instance/instance-create-quickstart.md)に関するページを参照してください。

> [!IMPORTANT]
> 移行先の VNet とサブネットが[マネージド インスタンスの VNet 要件](../../managed-instance/connectivity-architecture-overview.md#network-requirements)に従っているようにすることが重要です。 非互換性があると、新しいインスタンスの作成や、既に作成したインスタンスの使用ができなくなることがあります。 詳しくは、[ネットワークの新規作成](../../managed-instance/virtual-network-subnet-create-arm-template.md)と[既存のネットワークの構成](../../managed-instance/vnet-existing-add-subnet.md)に関する記事をご覧ください。

## <a name="migrate"></a>移行

移行前ステージの関連タスクを完了したら、スキーマとデータの移行を実行する準備は完了です。 

選択した[移行方法](sql-server-to-managed-instance-overview.md#compare-migration-options)を使用してデータを移行します。

SQL Managed Instance のターゲットは、オンプレミスまたは Azure VM データベース実装からのデータベースの一括移行を必要とするユーザー シナリオです。 これらは、インスタンス レベルの機能や複数データベース間の機能を定期的に使用するアプリケーションのバックエンドのリフト アンド シフトが必要な場合に最適な選択肢です。 このシナリオに該当する場合は、アプリケーションを再設計する必要なしに Azure 内の対応する環境にインスタンスを移行できます。

SQL インスタンスを移行するには、以下を慎重に計画する必要があります。

- 併置する必要のある (同じインスタンスで実行されている) すべてのデータベースの移行。
- ログイン、資格情報、SQL エージェント ジョブと演算子、サーバー レベルのトリガーなど、アプリケーションが依存するインスタンス レベルのオブジェクトの移行。

SQL Managed Instance は、通常の DBA アクティビティの一部を組み込み時にユーザーがプラットフォームに委任できるマネージド サービスです。 したがって、[高可用性](../../database/high-availability-sla.md)が組み込まれているため、定期的なバックアップや Always On 構成のメンテナンス ジョブなど、いくつかのインスタンス レベルのデータは移行する必要がありません。

SQL Managed Instance では、次のデータベース移行オプションがサポートされます (現在は、これらの移行方法のみがサポートされます)。

- Azure Database Migration Service - ほぼダウンタイムがない移行。
- ネイティブな `RESTORE DATABASE FROM URL` - SQL Server のネイティブ バックアップを使用し、ある程度のダウンタイムが必要。

このガイドでは、2 つの最も一般的な方法、つまり Azure Database Migration Service (DMS) およびネイティブのバックアップと復元について説明します。

### <a name="database-migration-service"></a>Database Migration Service

DMS を使用して移行を実行するには、次の手順に従います。

1. これを初めて実行する場合は、 [**microsoft.datamigration** リソースプロバイダー](../../../dms/quickstart-create-data-migration-service-portal.md#register-the-resource-provider)をサブスクリプションに登録します。
1. 任意の場所 (できればターゲット Azure SQL Managed Instance と同じリージョン) に Azure Database Migration Service インスタンスを作成し、この DMS インスタンスをホストするために、既存の仮想ネットワークを選択するか、新しい仮想ネットワークを作成します。
1. DMS インスタンスを作成した後、新しい移行プロジェクトを作成し、ソース サーバーの種類として **[SQL Server]** を指定し、ターゲット サーバーの種類として **[Azure SQL Database Managed Instance]** を指定します。 プロジェクトの作成ブレードで、アクティビティの種類 (オンラインまたはオフラインのデータ移行) を選択します。 
1.  **[移行ソースの詳細]** ページでソース SQL Server の詳細を指定し、 **[移行のターゲットの詳細]** ページでターゲット Azure SQL Managed Instance の詳細を指定します。 **[次へ]** を選択します。
1. 移行するデータベースを選択します。 
1. 構成設定を指定して、データベースのバックアップ ファイルを格納する **SMB ネットワーク共有** を指定します。 DMS では、ネットワーク共有にアクセスできる Windows ユーザー資格情報を使用します。 **Azure Storage アカウントの詳細** を指定します。 
1. 移行の概要を確認し、 **[移行の実行]** を選択します。 その後、移行アクティビティを監視し、データベース移行の進行状況を確認できます。
1. データベースが復元されたら、 **[一括で開始]** を選択します。 SMB ネットワーク共有でログ末尾のバックアップを使用可能にすると、移行プロセスでログ末尾のバックアップがコピーされ、ターゲットで復元されます。 
1. ソース データベースへのすべての受信トラフィックを停止し、新しい Azure SQL Managed Instance データベースへの接続文字列を更新します。 

この移行オプションの詳細なステップバイステップのチュートリアルについては、「[DMS を使用してオンラインで SQL Server を Azure SQL Managed Instance に移行する](../../../dms/tutorial-sql-server-managed-instance-online.md)」を参照してください。 
   

### <a name="backup-and-restore"></a>バックアップと復元 

短時間で簡単にデータベースを移行できるようにするための Azure SQL Managed Instance の主な機能の 1 つは、[Azure Storage](https://azure.microsoft.com/services/storage/) に格納されているデータベース バックアップ (`.bak`) ファイルのネイティブ復元です。 バックアップと復元は、データベースのサイズに基づく非同期の操作です。 

次の図は、プロセスの概要を示しています。

![SQL Server、Azure Storage に向かう "BACKUP / Upload to URL" というラベルの矢印、Azure Storage から SQL の Managed Instance に向かう "RESTORE from URL" というラベルの 2 つ目の矢印を示す図。](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> バックアップを作成し、それを Azure Storage にアップロードする時間、および Azure SQL Managed Instance に対してネイティブの復元操作を実行する時間は、データベースのサイズによって異なります。 大規模なデータベースの操作に対応するために、十分なダウンタイムを考慮してください。 

次の表は、実行しているソース SQL Server のバージョンに応じて使用できる方法に関する詳細情報を示しています。

|手順|SQL エンジンとバージョン|バックアップ/復元方法|
|---|---|---|
|Azure Storage へのバックアップの格納|2012 SP1 CU2 より前|Azure Storage に .bak ファイルを直接アップロードする|
| |2012 SP1 CU2 - 2016|非推奨の [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) 構文を使用して直接バックアップする|
| |2016 以上|[WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) を使用して直接バックアップする|
|Azure Storage からマネージド インスタンスに復元する| |[SAS 資格情報での URL からの復元](../../managed-instance/restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - ネイティブ復元オプションを使用して、[Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md) によって保護されたデータベースをマネージド インスタンスに移行する場合は、データベースの復元の前に、オンプレミスまたは Azure VM SQL Server の対応する証明書を移行する必要があります。 詳細な手順については、[TDE 証明書のマネージド インスタンスへの移行](../../managed-instance/tde-certificate-migrate.md)に関するページを参照してください。
> - システム データベースの復元はサポートされていません。 (マスターまたは msdb データベースに格納されている) インスタンス レベルのオブジェクトを移行するには、それらのスクリプトを作成し、移行先のインスタンスで T-SQL スクリプトを実行することをお勧めします。

バックアップと復元を使用して移行するには、次の手順に従います。 

1. データベースを Azure Blob Storage にバックアップします。 たとえば、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) で [[backup to url]\(URL へのバックアップ\)](/sql/relational-databases/backup-restore/sql-server-backup-to-url) を使用します。 [Microsoft Azure Tool](https://go.microsoft.com/fwlink/?LinkID=324399) を使用して、SQL Server 2012 SP1 CU2 より前のデータベースをサポートします。 
1. SQL Server Management Studio を使用して Azure SQL Managed Instance に接続します。 
1. データベース バックアップがある Azure Blob Storage のアカウントにアクセスするために、Shared Access Signature を使用して資格情報を作成します。 次に例を示します。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Azure Storage Blob コンテナー内のバックアップを復元します。 次に例を示します。 

   ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. 復元が完了したら、SQL Server Management Studio 内の **オブジェクト エクスプローラー** でデータベースを確認します。 

この移行オプションの詳細については、「[SSMS を使用して Azure SQL Managed Instance にデータベースを復元する](../../managed-instance/restore-sample-database-quickstart.md)」を参照してください。

> [!NOTE]
> データベースの復元操作は非同期であり、再試行可能です。 接続が切断されるか、タイムアウトが発生した場合に、SQL Server Management Studio にエラーが生じる可能性があります。 Azure SQL Database では、バックグラウンドでのデータベースの復元を試行し続けます。[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) および [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ビューを使用して、復元の進行状況を追跡できます。


## <a name="data-sync-and-cutover"></a>データの同期と切り替え

データの変更をソースからターゲットに継続的にレプリケートおよび同期する移行オプションを使用している場合、ソースのデータとスキーマが変更され、ターゲットと食い違う可能性があります。 データの同期中にソースのすべての変更がキャプチャされ、移行プロセス中にターゲットに適用されるようにしてください。 

ソースとターゲットの両方でデータが同じであることを確認した後、ソース環境からターゲット環境に切り替えることができます。 切り替え中の最小限の中断によってビジネス継続性に影響が出ないようにするために、ビジネス チーム/アプリケーション チームと共に切り替えプロセスを計画することが重要です。 

> [!IMPORTANT]
> DMS を使用した移行の一環として切り替えを実行する場合、それに関連する特定の手順について詳しくは、[移行カットオーバーの実行](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover)に関するページを参照してください。


## <a name="post-migration"></a>移行後

移行ステージが正常に完了した後、移行後の一連のタスクを実行し、すべてが円滑かつ効率的に機能することを確認します。 

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。 

### <a name="monitor-and-remediate-applications"></a>アプリケーションの監視と修復 
マネージド インスタンスへの移行を完了した後は、アプリケーションの動作とワークロードのパフォーマンスを追跡する必要があります。 このプロセスには、次のアクティビティが含まれます。

- [ソース SQL Server インスタンス上で作成したパフォーマンス ベースライン](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline)と、[マネージド インスタンスで実行されているワークロードのパフォーマンスを比較](sql-server-to-managed-instance-performance-baseline.md#compare-performance)します。
- 継続的に[ワークロードのパフォーマンスを監視](sql-server-to-managed-instance-performance-baseline.md#monitor-performance)し、問題と改善の可能性を明らかにします。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテストア プローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


## <a name="leverage-advanced-features"></a>高度な機能を活用する 

SQL Managed Instance によって提供されるクラウドベースの高度な機能を活用してください。たとえば、[組み込みの高可用性](../../database/high-availability-sla.md)、[脅威検出](../../database/azure-defender-for-sql.md)、[ワークロードの監視と調整](../../database/monitor-tune-overview.md)などです。 

[Azure SQL Analytics](../../../azure-sql/database/monitor-tune-overview.md) を使用すると、多数のマネージド インスタンスを一元的な方法で監視できます。

SQL Server の一部の機能は、[データベース互換レベル](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)を最新の互換性レベル (150) に変更した場合にのみ使用できます。 


## <a name="next-steps"></a>次のステップ

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールの一覧については、[データ移行のサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Managed Instance の詳細については、次を参照してください。
   - [Azure SQL Managed Instance のサービス レベル](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server と Azure SQL Managed Instance の相違点](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。
