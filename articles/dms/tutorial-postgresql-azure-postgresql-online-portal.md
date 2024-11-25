---
title: チュートリアル:Azure portal を介してオンラインで PostgreSQL を Azure DB for PostgreSQL に移行する
titleSuffix: Azure Database Migration Service
description: Azure portal を介して Azure Database Migration Service を使用し、オンプレミスの PostgreSQL から Azure Database for PostgreSQL へのオンライン移行を実行する方法について学習します。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 4f73ced1dba5482c3c230c96163464c064523bd4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218243"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>チュートリアル:Azure portal を介して DMS を使用してオンラインで PostgreSQL を Azure DB for PostgreSQL に移行する

Azure Database Migration Service を使用して、アプリケーションへのダウンタイムを最小限に抑えながら、データベースをオンプレミスの PostgreSQL インスタンスから [Azure Database for PostgreSQL](../postgresql/index.yml) に移行することができます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**DVD Rental** サンプル データベースを PostgreSQL 9.6 のオンプレミス インスタンスから Azure Database for PostgreSQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * pg_dump ユーティリティを使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service で移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。
> * 一括移行を実行する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 移行プロセス中のデータの盗難を防ぐために、ディスクは暗号化します。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [PostgreSQL コミュニティ エディション](https://www.postgresql.org/download/) 9.4、9.5、9.6、または 10 をダウンロードしてインストールします。 ソースの PostgreSQL サーバーのバージョンは 9.4、9.5、9.6、10、11、12 または 13 である必要があります。 詳細については、[サポートされる PostgreSQL データベース バージョン](../postgresql/concepts-supported-versions.md)に関するページをご覧ください。

    また、ターゲットの Azure Database for PostgreSQL のバージョンは、オンプレミスの PostgreSQL バージョンと同じかそれ以降である必要があることに注意してください。 たとえば、PostgreSQL 9.6 は Azure Database for PostgreSQL 9.6、10、または 11 に移行でき、Azure Database for PostgreSQL 9.5 には移行できません。 

* [Azure Database for PostgreSQL サーバーを作成](../postgresql/quickstart-create-server-database-portal.md)するか、[Azure Database for PostgreSQL - Hyperscale (Citus) サーバーを作成](../postgresql/quickstart-create-hyperscale-portal.md)します。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループ (NSG) の規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソース PostgreSQL サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 5432 です。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for PostgreSQL のサーバーレベルの[ファイアウォール規則](../postgresql/concepts-firewall-rules.md)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
* postgresql.config ファイルで論理レプリケーションを有効にし、次のパラメーターを設定します。

  * wal_level = **logical**
  * max_replication_slots = [スロットの数]、**5 スロット** に設定することをお勧めします
  * max_wal_senders = [同時実行タスク数] - max_wal_senders パラメーターでは同時に実行できるタスクの数を設定します、**10 タスク** に設定することをお勧めします

> [!IMPORTANT]
> 変更をターゲット データベースに確実に同期できるようにするには、既存のデータベース内のすべてのテーブルに主キーが必要です。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを完了するには、ソース データベースからスキーマを抽出し、データベースに適用する必要があります。

1. pg_dump -s コマンドを使用して、データベースのスキーマ ダンプ ファイルを作成します。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    たとえば、**dvdrental** データベース用のスキーマ ダンプ ファイルを作成するには、次のようにします。

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    pg_dump ユーティリティの使用方法の詳細については、 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) チュートリアルの例を参照してください。

2. ターゲット環境に空のデータベース (Azure Database for PostgreSQL) を作成します。

    データベースの接続および作成方法の詳細については、「[Azure portal で Azure Database for PostgreSQL サーバーを作成する](../postgresql/quickstart-create-server-database-portal.md)」または「[Azure portal で Azure Database for PostgreSQL - Hyperscale (Citus) を作成する](../postgresql/quickstart-create-hyperscale-portal.md)」を参照してください。

    > [!NOTE]
    > Azure Database for PostgreSQL - Hyperscale (Citus) のインスタンスには、**citus** という単一のデータベースのみがあります。

3. スキーマ ダンプ ファイルを復元して作成したターゲット データベースに、スキーマをインポートします。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    次に例を示します。

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

   > [!NOTE]
   > 移行サービスにより、外部キーの有効化/無効化が内部的に処理され、信頼性のある堅牢なデータ移行が確保されるようトリガーされます。 そのため、ターゲット データベース スキーマの変更は検討する必要はありません。

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-a-dms-instance"></a>DMS インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループ、および場所を指定します。

4. 既存の仮想ネットワークを選択するか、新しく作成します。

    仮想ネットワークによって、Azure Database Migration Service に、ソースの PostgreSQL サーバーとターゲットの Azure Database for PostgreSQL インスタンスへのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。

5. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. **[確認および作成]** を選択してサービスを作成します。

   サービスの作成は約 10 分から 15 分以内に完了します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索し、インスタンスを選択してから、 **[+ 新しい移行プロジェクト]** を選びます。

3. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[PostgreSQL]** を選択して、 **[ターゲット サーバーの種類]** テキスト ボックスで **[Azure Database for PostgreSQL]** を選択します。

4. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    ![Azure Database Migration Service プロジェクトを作成する](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

5. **[保存]** を選択します。Azure Database Migration Service を使用して正常にデータを移行するための要件を確認してから、 **[アクティビティの作成と実行]** を選択します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの追加に関する詳細]** 画面で、ソース PostgreSQL インスタンスの接続の詳細を指定します。

    ![[Add Source Details]\(ソースの詳細の追加\) 画面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[ターゲットの詳細]** 画面で、事前プロビジョニングされており、pg_dump を使用して **DVD Rentals** スキーマがデプロイされている、ターゲットの Hyperscale (Citus) サーバーの接続の詳細を指定します。

    ![[ターゲットの詳細] 画面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![[ターゲット データベースへマッピング] 画面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **[保存]** を選択してから、 **[移行の設定]** 画面で既定値をそのまま使用します。

    ![[移行の設定] 画面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![[移行の概要] 画面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** が更新されて、 **[バックアップが進行中です]** と示されるはずです。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

     ![移行プロセスを監視する](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 移行が完了したら、 **[データベース名]** で、特定のデータベースを選択して、**データ全体の読み込み** および **増分データ同期** 操作の移行状態を取得します。

   > [!NOTE]
   > **データ全体の読み込み** には初回の読み込みの移行状態が表示され、**増分データ同期** には変更データ キャプチャ (CDC) の状態が表示されます。

     ![データ全体の読み込みの詳細](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![増分データ同期の詳細](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了すると、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

2. **[保留中の変更]** カウンターに **0** と表示されるまで待ってソース データベースへのすべての受信トランザクションが停止していることを確認し、 **[確認]** チェックボックスをオンにしてから **[適用]** を選択します。

    ![[一括を完了する] 画面](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. データベースの移行状態に **[完了]** と表示されたら、[順序を再作成](https://wiki.postgresql.org/wiki/Fixing_Sequences) (該当する場合) して、アプリケーションを Azure Database for PostgreSQL の新しいターゲット インスタンスに接続します。

## <a name="next-steps"></a>次のステップ

* Azure Database for PostgreSQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for PostgreSQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-postgresql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](../postgresql/overview.md)」を参照してください。
