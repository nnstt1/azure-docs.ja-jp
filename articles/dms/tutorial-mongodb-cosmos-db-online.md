---
title: チュートリアル:MongoDB を Azure Cosmos DB の MongoDB 用 API にオンラインで移行する
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、オンプレミスの MongoDB から Azure Cosmos DB の MongoDB 用 API にオンラインで移行する方法について学習します。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-nov-2020
ms.topic: tutorial
ms.date: 09/21/2021
ms.openlocfilehash: 1d5198365465e07e393aef1c155416a444071385
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578699"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>チュートリアル:DMS を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオンラインで移行する
[!INCLUDE[appliesto-mongodb-api](../cosmos-db/includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 移行手順を実行する前に、このガイド全体をお読みください。
>

この MongoDB 移行ガイドは、MongoDB の移行に関するシリーズの一部です。 MongoDB 移行の重要な手順は、次に示すように、[移行前](../cosmos-db/mongodb-pre-migration.md)、移行、そして[移行後](../cosmos-db/mongodb-post-migration.md)です。

![移行手順の図。](../cosmos-db/mongodb/media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-online-data-migration-from-mongodb-to-azure-cosmos-db-using-dms"></a>DMS を使用した MongoDB から Azure Cosmos DB へのオンライン データ移行の概要

Azure Database Migration Service を使用して、MongoDB のオンプレミスまたはクラウドのインスタンスから Azure Cosmos DB の MongoDB 用 API に、データベースのオンライン (最小限のダウンタイム) の移行を実行できます。

このチュートリアルでは、Azure Database Migration Service を使用して MongoDB のデータを Azure Cosmos DB に移行する方法に関して、次の手順をデモンストレーションします。
> [!div class="checklist"]
> 
> * Azure Database Migration Service のインスタンスを作成する。 
> * 移行プロジェクトを作成します。 
> * ソースを指定する。 
> * ターゲットを指定する。 
> * ターゲット データベースにマップする。 
> * 移行を実行する。 
> * 移行を監視する。 
> * Azure Cosmos DB 内のデータを検証する。 
> * 準備が整ったら、移行を完了する。 

このチュートリアルでは、Azure Database Migration Service を使用して、Azure 仮想マシンでホストされている MongoDB 内のデータセットを、Azure Cosmos DB の MongoDB 用 API に最小限のダウンタイムで移行します。 MongoDB ソースをまだセットアップしていない場合は、記事「[Azure の Windows VM に MongoDB をインストールして構成する](/previous-versions/azure/virtual-machines/windows/install-mongodb)」をご覧ください。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなる可能性があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、MongoDB から Azure Cosmos DB の MongoDB 用 API へのオンライン移行について説明します。 オフライン移行については、「[DMS を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する](tutorial-mongodb-cosmos-db.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* スループットの見積もり、パーティション キーの選択、インデックス作成ポリシーなど、[移行前の手順を完了](../cosmos-db/mongodb-pre-migration.md)します。
* [Azure Cosmos DB の MongoDB 用 API アカウントを作成](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)し、[SSR (サーバー側の再試行)](../cosmos-db/mongodb/prevent-rate-limiting-errors.md) を有効にします。

  > [!NOTE]
  > サーバーレス モードでプロビジョニングされた MongoDB 用 API アカウントに移行している場合、DMS は現在サポートされていません。

* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これにより、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続が確立されます。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループ (NSG) の規則によって、次の各通信ポートがブロックされていないことを確認します。53、443、445、9354、および 10000 から 20000。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* Azure Database Migration Service がソース MongoDB サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 27017 が使用されています。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Azure Cosmos DB のサーバー側の再試行を構成して効率的に移行する

MongoDB から Azure Cosmos DB に移行するお客様は、リソース ガバナンス機能を活用できます。これにより、プロビジョニング済み RU/秒のスループットを完全に利用できるようになります。 Azure Cosmos DB では、データ移行サービスの特定の要求がコンテナーにプロビジョニングされている RU/秒を超え、その要求を再試行する必要がある場合に、移行の過程でその要求を調整することができます。 データ移行サービスは再試行を実行できますが、データ移行サービスと Azure Cosmos DB と間のネットワーク ホップに関係するラウンドトリップ時間が、その要求の全体的な応答時間に影響します。 調整された要求の応答時間を改善することにより、移行に必要な合計時間を短縮できます。 Azure Cosmos DB の "*サーバー側の再試行*" 機能を使用すると、サービスで調整エラー コードをインターセプトし、はるかに短いラウンドトリップ時間で再試行することができるため、要求の応答時間が大幅に短縮されます。

サーバー側の再試行機能は、Azure Cosmos DB ポータルの *[機能]* ブレードで確認できます

![MongoDB のサーバー側の再試行機能のスクリーンショット。](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-feature.png)

*[無効]* になっている場合は、次のように有効にすることをお勧めします

![MongoDB のサーバー側の再試行機能を有効にする画面のスクリーンショット。](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-enable.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)] 

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。

5. 既存の仮想ネットワークを選択するか、新しく作成します。

   この仮想ネットワークによって、Azure Database Migration Service に、ソース MongoDB インスタンスとターゲット Azure Cosmos DB アカウントへのアクセスが提供されます。

   Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。

6. Premium 価格レベルの SKU を選択します。

    > [!NOTE]
    > オンライン移行は、Premium レベルを使用している場合にのみサポートされます。 コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

    または、Azure portal の [検索] ウィンドウから Azure Database Migration Service インスタンスを見つけることができます。

    ![Azure portal の [検索] ウィンドウを使用する](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. **[+ 新しい移行プロジェクト]** を選択します。

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、 **[ソース サーバーの種類]** ボックスでは **[MongoDB]** を、 **[ターゲット サーバーの種類]** ボックスでは **[CosmosDB (MongoDB API)]** を選択して、 **[アクティビティの種類を選択します]** で **[オンライン データの移行 [プレビュー]]** を選択します。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. **[保存]** を選択し、 **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの詳細]** 画面で、ソース MongoDB サーバーの接続の詳細を指定します。

   > [!IMPORTANT]
   > Azure Database Migration Service では、Azure Cosmos DB はソースとしてサポートされていません。

    ソースへの接続には、3 つのモードがあります。
   * **標準モード**: 完全修飾ドメイン名または IP アドレス、ポート番号、および接続の資格情報を受け取ります。
   * **接続文字列のモード**: MongoDB 接続文字列 (記事「[Connection String URI Format (接続文字列の URI 形式)](https://docs.mongodb.com/manual/reference/connection-string/)」で説明しています) を受け取ります。
   * **Azure Storage からのデータ**: BLOB コンテナーの SAS URL を受け取ります。 MongoDB [bsondump ツール](https://docs.mongodb.com/manual/reference/program/bsondump/)によって生成された BSON ダンプが BLOB コンテナーに含まれている場合は、 **[Blob には BSON ダンプが含まれています]** を選択します。JSON ファイルがコンテナーに含まれている場合は、その選択を解除します。

     このオプションを選択する場合は、ストレージ アカウントの接続文字列が次の形式で表示されていることを確認してください。

     ```
     https://blobnameurl/container?SASKEY
     ```

     また、Azure Storage のダンプの種類情報に基づいて、次の点に注意してください。

     * BSON ダンプの場合、データ ファイルが collection.bson 形式の包含データベースの名前が付けられたフォルダーに配置されるように、BLOB コンテナー内のデータは bsondump 形式にする必要があります。 メタデータ ファイルがある場合は、*collection*.metadata.json の形式で名前を付けます。

     * JSON ダンプの場合、BLOB コンテナー内のファイルは、包含データベースの名前が付けられたフォルダーに配置する必要があります。 各データベース フォルダー内で、データ ファイルは "data" というサブフォルダーに配置し、*collection*.json の形式で名前を付ける必要があります。 メタデータ ファイルがある場合は、"metadata" というサブフォルダーに配置し、同じ形式の *collection*.json を使用して名前を付ける必要があります。 メタデータ ファイルは、MongoDB bsondump ツールによって生成されたものと同じ形式にする必要があります。

    > [!IMPORTANT]
    > Mongo サーバー上で自己署名入りの証明書を使用することはお勧めできません。 ただし、使用する場合は、**接続文字列のモード** を使用してサーバーに接続し、確実に接続文字列に “” を付けるようにしてください。
    >
    >```
    >&sslVerifyCertificate=false
    >```

    DNS の名前解決ができない場合は、IP アドレスを使用できます。

   ![ソース詳細を指定する](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. **[保存]** を選択します。

   > [!NOTE]
   > ソース サーバーのアドレスは、ソースがレプリカ セットの場合はプライマリのアドレス、シャード化された MongoDB クラスターの場合はルーターのアドレスである必要があります。 シャード化された MongoDB クラスターの場合、Azure Database Migration Service がクラスター内の個々のシャードに接続できる必要があります。それには、他のマシンでもファイアウォールを開くことが必要になる場合があります。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[移行のターゲットの詳細]** 画面で、ターゲットの Azure Cosmos DB アカウントの接続の詳細を指定します。これは、MongoDB データの移行先である、事前プロビジョニング済みの Azure Cosmos DB の MongoDB 用 API アカウントです。

    ![ターゲット詳細を指定する](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. **[保存]** を選択します。

## <a name="map-to-target-databases"></a>ターゲット データベースにマップする

1. **[Map to target databases]\(ターゲット データベースにマップ\)** 画面で、移行用のソース データベースとターゲット データベースをマップします。

   ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

   データベース名の横に **[作成]** と表示される場合は、Azure Database Migration Service でターゲット データベースが見つからず、サービスによってデータベースが自動的に作成されることを示します。

   移行のこの時点で、データベースに対する共有スループットが必要な場合は、スループットの RU を指定します。 Cosmos DB では、データベース レベルで、またはコレクションごとに個別に、スループットをプロビジョニングできます。 スループットは、[要求ユニット](../cosmos-db/request-units.md) (RU) で測定されます。 [Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)の詳細を確認してください。

   ![ターゲット データベースにマップする](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. **[保存]** を選択します。

3. **[コレクション設定]** 画面で、コレクションの一覧表示を展開し、移行されるコレクションの一覧を確認します。

   Azure Database Migration Service では、ソースの MongoDB インスタンスには存在していて、ターゲットの Azure Cosmos DB アカウントには存在していない、すべてのコレクションが自動的に選択されます。 データが既に含まれているコレクションを再度移行する場合は、この画面でコレクションを明示的に選択する必要があります。

   コレクションで使用する RU の数を指定することができます。 ほとんどの場合、500 (シャード化されたコレクションの場合、最小は 1000) から 4000 までの値で十分です。 Azure Database Migration Service では、コレクションのサイズに基づいて適切な既定値が提案されます。

    > [!NOTE]
    > 実行を高速化するために、必要に応じて Azure Database Migration Service の複数のインスタンスを使用して、データベースの移行とコレクションを並列して実行します。

   また、最適なスケーラビリティのために、シャード キーを指定して [Azure Cosmos DB のパーティション分割](../cosmos-db/partitioning-overview.md)を利用することもできます。 [シャード/パーティション キーの選択に関するベスト プラクティス](../cosmos-db/partitioning-overview.md#choose-partitionkey)を確認してください。 パーティション キーを持っていない場合は、スループット向上のためにシャード キーとして **_id** を常に使用できます。

   ![コレクション テーブルを選択する](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. **[保存]** を選択します。

5. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

    ![移行の概要](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

   移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** が表示されます。

   ![アクティビティの状態](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>移行を監視する

* 移行アクティビティ画面で、移行の **[状態]** が **[再生中]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

   > [!NOTE]
   > アクティビティを選択して、データベース レベルおよびコレクション レベルの移行メトリックの詳細を取得できます。

   ![アクティビティの状態 - 再生中](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB でデータを確認する

1. ソース MongoDB データベースに変更を加えます。
2. COSMOS DB に接続して、データがソース MongoDB サーバーからレプリケートされるかどうかを確認します。

    ![データがレプリケートされたことを確認できる画面のスクリーンショット。](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>移行を完了する

* ソースのすべてのドキュメントが COSMOS DB ターゲットで使用可能になったら、移行アクティビティのコンテキスト メニューから **[完了]** を選択して、移行を完了します。

    このアクションにより、保留中の変更がすべて再生し終わり、移行が完了します。

    ![[完了] メニュー オプションが表示されている画面のスクリーンショット。](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>移行後の最適化

MongoDB データベースに格納されているデータを Azure Cosmos DB の MongoDB 用 API に移行した後、Azure Cosmos DB に接続してデータを管理できます。 また、インデックス作成ポリシーの最適化、既定の整合性レベルの更新、Azure Cosmos DB アカウントのグローバル分散の構成など、移行後の他の最適化手順を実行することもできます。 詳細については、「[移行後の最適化](../cosmos-db/mongodb-post-migration.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [Cosmos DB サービスの情報](https://azure.microsoft.com/services/cosmos-db/)
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか?
    * 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../cosmos-db/convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](../cosmos-db/mongodb/estimate-ru-capacity-planner.md)に関するページを参照してください

## <a name="next-steps"></a>次のステップ

* Microsoft の「[Database Migration Guide](https://datamigration.microsoft.com/)」(データベース移行ガイド) で、他のシナリオの移行ガイダンスを確認する。