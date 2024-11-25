---
title: 統合ランタイム
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の統合ランタイムについて説明します。
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6081e63a9a2fbc4598bac0d368d43c31121fa448
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787928"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory の統合ランタイム 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) は、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory と Azure Synapse のパイプラインによって使用されるコンピューティング インフラストラクチャです。

- **Data Flow**: マネージド Azure コンピューティング環境で [データ フロー](concepts-data-flow-overview.md)を実行します。  
- **データ移動**:パブリック ネットワーク内のデータ ストアとプライベート ネットワーク (オンプレミスまたは仮想プライベート ネットワーク) 内のデータ ストアをまたいでデータをコピーします。 組み込みコネクタ、形式の変換、列のマッピング、パフォーマンスとスケーラビリティに優れたデータ転送に関するサポートを提供します。
- **アクティビティのディスパッチ**:  Azure Databricks、Azure HDInsight、ML スタジオ (クラシック)、Azure SQL Database、SQL Server などのさまざまなコンピューティング サービスで実行される変換アクティビティをディスパッチして監視します。
- **SSIS パッケージの実行**:マネージド Azure コンピューティング環境で SQL Server Integration Services (SSIS) パッケージをネイティブに実行します。

Data Factory と Synapse のパイプラインでは、実行されるアクションをアクティビティで定義します。 リンクされたサービスは、ターゲットのデータ ストアやコンピューティング サービスを定義します。 統合ランタイムは、アクティビティとリンクされたサービスとを橋渡しします。  リンクされたサービスまたはアクティビティによって参照され、アクティビティが実行されたりディスパッチされたりするコンピューティング環境を提供します。 これにより、できるだけターゲットのデータ ストアやコンピューティング サービスに近いリージョンでアクティビティを実行して効率を最大化できる一方、セキュリティとコンプライアンスの必要も満たせます。

統合ランタイムは、Azure Data Factory と Azure Synapse の UI で、[管理ハブ](author-management-hub.md)およびそれらを参照するすべてのアクティビティ、データセット、データ フローを使用して作成できます。

## <a name="integration-runtime-types"></a>統合ランタイムの種類

Data Factory には 3 種類の統合ランタイム (IR) が用意されているので、ご希望のデータ統合機能やネットワーク環境のニーズに最もかなっている種類を選択する必要があります。  次の 3 種類があります。

- Azure
- セルフホステッド
- Azure-SSIS

> [!NOTE]
> Synapse パイプラインは、現在 Azure またはセルフホステッド統合ランタイムのみに対応しています。

次の表で、各種の統合ランタイムの機能とネットワークのサポートについて説明します。

IR の種類 | パブリック ネットワーク | プライベート ネットワーク 
------- | -------------- | ---------------
Azure | Data Flow<br/>データの移動<br/>アクティビティのディスパッチ | Data Flow<br/>データの移動<br/>アクティビティのディスパッチ
セルフホステッド | データの移動<br/>アクティビティのディスパッチ | データの移動<br/>アクティビティのディスパッチ
Azure-SSIS | SSIS パッケージ実行 | SSIS パッケージ実行


## <a name="azure-integration-runtime"></a>Azure 統合ランタイム

Azure Integration Runtime では以下が可能です。

- Azure でデータ フローを実行する 
- クラウドのデータ ストア間でコピー アクティビティを実行する
- パブリック ネットワーク内で次の変換アクティビティをディスパッチする: Databricks Notebook/Jar/Python アクティビティ、HDInsight Hive アクティビティ、HDInsight Pig アクティビティ、HDInsight MapReduce アクティビティ、HDInsight Spark アクティビティ、HDInsight Streaming アクティビティ、ML スタジオ (クラシック) Batch Execution アクティビティ、ML スタジオ (クラシック) 更新リソース アクティビティ、ストアド プロシージャ アクティビティ、Data Lake Analytics U-SQL アクティビティ、.NET カスタム アクティビティ、Web アクティビティ、Lookup アクティビティ、GetMetadata アクティビティ。

### <a name="azure-ir-network-environment"></a>Azure IR のネットワーク環境

Azure Integration Runtime では、だれでもアクセス可能なエンドポイントを使用して、データ ストアやコンピューティング サービスへの接続がサポートされます。 マネージド仮想ネットワークを有効にすると、Azure Integration Runtime では、プライベート ネットワーク環境でプライベート リンク サービスを使用したデータ ストアへの接続がサポートされます。

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR のコンピューティング リソースとスケーリング
Azure 統合ランタイムは、Azure 内のフル マネージドのサーバーレス コンピューティングを提供します。  インフラストラクチャのプロビジョニング、ソフトウェアのインストール、修正プログラムの適用、容量のスケーリングについて心配する必要はありません。  さらに、実際の使用時間分だけのお支払いになります。

Azure 統合ランタイムは、ネイティブのコンピューティングを備えており、セキュリティで保護された、信頼性とパフォーマンスの高い方法で、クラウドのデータ ストア間でデータを移動します。  コピー アクティビティで使用するデータの統合単位の数を設定できます。Azure IR のコンピューティング サイズはそれに応じて柔軟にスケール アップし、Azure 統合ランタイムのサイズを明示的に調整する必要はありません。 

アクティビティのディスパッチは、アクティビティをターゲット コンピューティング サービスにルーティングする負荷の低い操作であるため、このシナリオのためにコンピューティング サイズをスケールアップする必要はありません。

Azure IR の作成と構成に関する詳細については、「[Azure 統合ランタイムを作成して構成する方法](create-azure-integration-runtime.md)」を参照してください。 

> [!NOTE] 
> Azure Integration Runtime には、データ フローを実行するための基盤となるコンピューティング インフラストラクチャを定義する、Data Flow ランタイムに関連するプロパティがあります。 

## <a name="self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム

セルフホステッド IR により、次のことが可能になります。

- クラウドのデータ ストアとプライベート ネットワーク内のデータ ストアの間でコピー アクティビティを実行する。
- オンプレミスまたは Azure Virtual Network 内のコンピューティング リソースに対して次の変換アクティビティをディスパッチする: HDInsight Hive アクティビティ (BYOC-Bring Your Own Cluster)、HDInsight Pig アクティビティ (BYOC)、HDInsight MapReduce アクティビティ (BYOC)、HDInsight Spark アクティビティ (BYOC)、HDInsight Streaming アクティビティ (BYOC)、ML スタジオ (クラシック) Batch Execution アクティビティ、ML スタジオ (クラシック) 更新リソース アクティビティ、ストアド プロシージャ― アクティビティ、Data Lake Analytics U-SQL アクティビティ、カスタム アクティビティ (Azure Batch 上で実行)、Lookup アクティビティ、GetMetadata アクティビティ。

> [!NOTE] 
> SAP Hana や MySQL などの独自ドライバーを必要とするデータ ストアをサポートするには、セルフホステッド統合ランタイムを使用します。詳細については、「[supported data stores (サポートされるデータ ストア)](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

> [!NOTE] 
> Java Runtime Environment (JRE) は、セルフホステッド IR の依存関係です。 JRE が同じホストにインストールされていることを確認してください。

### <a name="self-hosted-ir-network-environment"></a>セルフホステッド IR のネットワーク環境

パブリック クラウド環境からの直接の通信経路がないプライベート ネットワーク環境で、安全にデータ統合を実行しようとしている場合は、社内のファイアウォール内のオンプレミス環境か仮想プライベート ネットワーク内にセルフホステッド IR をインストールできます。  セルフホステッド統合ランタイムは、開かれたインターネットへの送信 HTTP ベースの接続のみを行います。

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>セルフホステッド IR のコンピューティング リソースとスケーリング

セルフホステッド IR は、オンプレミスのマシンか、プライベート ネットワーク内の仮想マシンにインストールします。 現時点では、セルフホステッド IR の実行対象として Windows オペレーティング システムだけがサポートされています。  

高可用性とスケーラビリティを実現するには、アクティブ/アクティブ モードで論理インスタンスをオンプレミスの複数のマシンに関連付けて、セルフホステッド IR をスケールアウトできます。  詳細については、ハウツー ガイドで[セルフホステッド IR の作成と構成](create-self-hosted-integration-runtime.md)の方法に関する記事をご覧ください。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイム

> [!NOTE]
> Azure-SSIS 統合ランタイムは、現在 Synapse パイプラインではサポートされていません。

既存の SSIS ワークロードをリフトアンドシフトするには、Azure-SSIS IR を作成して SSIS パッケージをネイティブに実行できます。  

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR のネットワーク環境

Azure-SSIS IR は、パブリック ネットワークかプライベート ネットワーク内でプロビジョニングできます。  オンプレミスのデータ アクセスは、オンプレミスのネットワークに接続している仮想ネットワークと Azure-SSIS IR を結合することでサポートされます。  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR のコンピューティング リソースとスケーリング

Azure-SSIS IR は、SSIS パッケージ実行専用の、Azure VM のフル マネージドのクラスターです。 SSIS プロジェクトまたはパッケージのカタログ (SSISDB) 用に独自の Azure SQL Database または SQL Managed Instance を持ち込むことができます。 ノードのサイズを指定してコンピューティング能力をスケールアップしたり、クラスター内のノードの数を指定してスケール アウトしたりできます。 必要に応じて Azure-SSIS 統合ランタイムを停止したり開始したりして、その実行のコストを管理できます。

詳細については、ハウツー ガイドで Azure-SSIS IR の作成と構成の方法に関する記事をご覧ください。  作成し終えたら、オンプレミスで SSIS を使用する場合と同様に、SQL Server Data Tools (SSDT) や SQL Server Management Studio (SSMS) などの使い慣れたツールを使用して、まったく、またはほとんど変更を加えずに既存の SSIS パッケージをデプロイして管理することができます。

Azure-SSIS ランタイムの詳細については、次の記事をご覧ください。 

- [チュートリアル: SSIS パッケージを Azure にデプロイする](./tutorial-deploy-ssis-packages-azure.md): この記事は、Azure-SSIS IR を作成し、Azure SQL Database を使用して SSIS カタログをホストするための詳細な手順を示しています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)。 この記事では、チュートリアルを基に、SQL Managed Instance の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、Azure 仮想ネットワークへの Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が仮想ネットワークに参加できるように Azure Portal を使用して仮想ネットワークを構成する手順も説明されています。 

## <a name="integration-runtime-location"></a>統合ランタイムの場所

### <a name="relationship-between-factory-location-and-ir-location"></a>ファクトリの場所と IR の場所の関係

顧客が Data Factory インスタンスを作成する際は、Data Factory または Synapse ワークスペースの場所を指定する必要があります。 Data Factory または Synapse ワークスペースのメタデータはここに格納されており、パイプラインのトリガーはここから開始されます。 メタデータは、顧客が選択したリージョンにのみ格納され、その他のリージョンには格納されません。

ただし、Azure Data Factory や Azure Synapse のパイプラインでは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。 この動作は[グローバルに使用できる IR](https://azure.microsoft.com/global-infrastructure/services/) によって実現し、データのコンプライアンス、効率性、ネットワークのエグレスのコストの削減が保証されます。

IR の場所は、そのバックエンドのコンピューティングの場所を定義するほか、実質的にデータの移動、アクティビティのディスパッチ、SSIS パッケージの実行が行われる場所を定義します。 IR の場所は、それが属している Data Factory の場所とは別にすることができます。 

### <a name="azure-ir-location"></a>Azure IR の場所

Azure IR の特定の場所を設定することができます。その場合は、その特定のリージョンでアクティビティの実行やディスパッチが行われます。

パブリック ネットワークで、既定の設定である自動解決の Azure IR を使用することを選択した場合、

- コピー アクティビティの場合、可能な限りシンク データ ストアの場所が自動的に検出され、同じリージョン (使用可能な場合) または同じ地理的な場所の最も近いリージョンのどちらかにある IR が使用されるようにします。シンク データ ストアのリージョンを検出できない場合は、代わりに Data Factory のリージョン内の IR が使用されます。

  たとえば、Data Factory または Synapse ワークスペースが米国東部で作成されているとします。 
  
  - 米国西部にある Azure BLOB にデータをコピーするときに、その BLOB が米国西部で検出された場合、コピー アクティビティは米国西部にある IR で実行されます。リージョンの検出に失敗した場合、コピー アクティビティは米国東部にある IR で実行されます。
  - リージョンを検出できない Salesforce にデータをコピーする場合、コピー アクティビティは米国東部にある IR で実行されます。

  >[!TIP] 
  >データ コンプライアンスの要件が厳しく、データが地理的な特定の場所を離れないようにする必要がある場合は、Azure IR を明示的に特定のリージョンに作成し、リンクされたサービスが ConnectVia プロパティを使用してこの IR を指すようにすることができます。 たとえば、データを英国内に留めたまま、英国南部の BLOB から英国南部の Azure Synapse Analytics にデータをコピーしたい場合は、英国南部に Azure IR を作成して、両方のリンクされたサービスをこの IR にリンクします。

- Lookup/GetMetadata/Delete アクティビティの実行 (パイプライン アクティビティとも呼ばれます)、変換アクティビティのディスパッチ (外部アクティビティとも呼ばれます)、およびオーサリング操作 (接続のテスト、フォルダー一覧とテーブル一覧の参照、データのプレビュー) の場合、Data Factory または Synapse ワークスペースと同じリージョンにある IR が使用されます。

- Data Flow の場合、Data Factory または Synapse ワークスペースのリージョンの IR が使用されます。 

  > [!TIP] 
  > 可能な場合は、Data Flow を対応するデータ ストアと同じリージョンで実行することをお勧めします。 これを実現するには、Azure IR を自動解決するか (データ ストアの場所が Data Factory または Synapse ワークスペースの場所と同じ場合)、データ ストアと同じリージョンに新しい Azure IR インスタンスを作成し、そこでデータ フローを実行します。 

自動解決 Azure IR に対してマネージド仮想ネットワークを有効にすると、Data Factory または Synapse ワークスペースのリージョンにある IR が使用されます。 

アクティビティの実行時に有効になっている IR の場所は、UI 上のパイプラインのアクティビティの監視ビューまたはアクティビティの監視のペイロードで監視できます。

### <a name="self-hosted-ir-location"></a>セルフホステッド IR の場所

セルフホステッド IR は Data Factory または Synapse ワークスペースに論理的に登録され、その機能のサポートのために使用するコンピューティングは自分で指定します。 したがって、セルフホステッド IR の明示的な場所のプロパティはありません。 

セルフホステッド IR を使用してデータの移動を実行する場合、この IR はデータをソースから抽出して移動先に書き込みます。

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR の場所

> [!NOTE]
> Azure-SSIS 統合ランタイムは、現在 Synapse パイプラインではサポートされていません。

抽出、変換、読み込み (ETL) ワークフローで高いパフォーマンスを実現するには、Azure-SSIS IR の正しい場所を選択することが重要です。

- Azure-SSIS IR の場所を Data Factory の場所と同じにする必要はありませんが、SSISDB のホストとなる独自の Azure SQL Database または SQL Managed Instance の場所と同じにする必要があります。 こうすると、Azure-SSIS 統合ランタイムから SSISDB に簡単にアクセスでき、複数の場所の間で過剰なトラフィックが生じません。
- 既存の SQL Database または SQL Managed Instance がなく、オンプレミスのデータ ソースまたは移動先がある場合、オンプレミスのネットワークに接続している仮想ネットワークの同じ場所に新しい Azure SQL Database または SQL Managed Instance を作成する必要があります。  この場合、新しい Azure SQL Database または SQL Managed Instance を使用し、この仮想ネットワークを参加させ、すべて同一の場所で Azure-SSIS IR を作成することができるため、異なる場所との間でデータの移動を効果的に最小限にすることができます。
- 既存の Azure SQL Database または SQL Managed Instance の場所と、オンプレミスのネットワークに接続している仮想ネットワークの場所が違う場合は、まず、既存の Azure SQL Database または SQL Managed Instance を使用して Azure-SSIS IR を作成し、同じ場所の別の仮想ネットワークを参加させます。次に、異なる場所の間の仮想ネットワーク間接続を構成します。

次の図は、Data Factory とその統合ランタイムの場所の設定を示しています。

:::image type="content" source="media/concepts-integration-runtime/integration-runtime-location.png" alt-text="統合ランタイムの場所":::

## <a name="determining-which-ir-to-use"></a>使用する IR の判別
1 つのアクティビティが複数の種類の統合ランタイムに関連付けられる場合は、そのどちらかに解決されます。 セルフホステッド統合ランタイムは、マネージド仮想ネットワークが使用されている Azure Data Factory や Synapse ワークスペース内の Azure 統合ランタイムよりも優先されます。 そして、後者はグローバル Azure 統合ランタイムよりも優先されます。

たとえば、ソースからシンクへのデータのコピーには、1 つのコピー アクティビティが使用されます。 グローバル Azure 統合ランタイムは、ソースにリンクされたサービスに関連付けられています。また、Azure Data Factory マネージド仮想ネットワーク内の Azure 統合ランタイムは、シンクのリンクされたサービスに関連付けられています。このため、ソースとシンクの両方のリンクされたサービスによって、マネージド仮想ネットワークが使用されている Azure Data Factory や Synapse ワークスペース内の Azure 統合ランタイムが使用されます。 ただし、セルフホステッド統合ランタイムが、ソースのリンクされたサービスに関連付けられている場合は、ソースとシンクの両方のリンクされたサービスによって、セルフホステッド統合ランタイムが使用されます。

### <a name="copy-activity"></a>コピー アクティビティ

コピー アクティビティの場合、データ フローの方向を定義するのに、ソースとシンクのリンクされたサービスが必要です。 どの統合ランタイム インスタンスを使用してコピーを実行するかを決めるために、次のロジックが使用されます。 

- **2 つのクラウド データ ソース間でのコピー**: ソースとシンクの両方のリンクされたサービスで Azure IR が使用されているとき、指定されている場合はそのリージョンの Azure IR が使用され、自動解決 IR (既定) が選択されている場合は、「[統合ランタイムの場所](#integration-runtime-location)」セクションで説明したとおり、Azure IR の場所が自動的に決定されます。
- **クラウド データ ソースとプライベート ネットワーク内のデータ ソースの間でのコピー**: ソースかシンクのいずれかのリンクされたサービスがセルフホステッド IR を指している場合、そのセルフホステッド統合ランタイム上でコピー アクティビティが実行されます。
- **プライベート ネットワーク内の 2 つのデータ ソース間でのコピー**: ソースとシンクの両方のリンクされたサービスが同じ統合ランタイム インスタンスを指す必要があり、その統合ランタイムを使用してコピー アクティビティが実行されます。

### <a name="lookup-and-getmetadata-activity"></a>Lookup および GetMetadata アクティビティ

Lookup および GetMetadata アクティビティは、データ ストアのリンクされたサービスに関連付けられている統合ランタイム上で実行されます。

### <a name="external-transformation-activity"></a>外部変換アクティビティ

外部のコンピューティング エンジンを活用する外部変換アクティビティにはそれぞれ、ターゲット コンピューティングのリンクされたサービスがあり、これは統合ランタイムに向けられています。 この統合ランタイム インスタンスによって、手動コーディングされたその外部変換アクティビティのディスパッチ元が判断されます。

### <a name="data-flow-activity"></a>Data Flow アクティビティ

Data Flow アクティビティは、それに関連付けられている Azure 統合ランタイムで実行されます。 Data Flow で活用される Spark コンピューティングは Azure 統合ランタイムのデータ フロー プロパティによって決定され、ADF によって完全管理されます。

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

- [Azure Integration Runtime の作成](create-azure-integration-runtime.md)
- [Create self-hosted integration runtime (セルフホステッド統合ランタイムの作成)](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 統合ランタイムを作成](create-azure-ssis-integration-runtime.md)します。 この記事では、チュートリアルを基に、SQL Managed Instance の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。