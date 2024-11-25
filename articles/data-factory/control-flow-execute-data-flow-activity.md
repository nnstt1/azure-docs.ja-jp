---
title: Data Flow アクティビティ
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory パイプラインまたは Azure Synapse Analytics パイプライン内からデータ フローを実行する方法。
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.author: makromer
ms.date: 09/09/2021
ms.openlocfilehash: c42ba6008f80f3fe625d9716c6a6d62f3fb60d2a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260286"
---
# <a name="data-flow-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory および Azure Synapse Analytics 内の Data Flow アクティビティ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ フロー アクティビティを使用して、Mapping Data Flow を介してデータを変換および移動します。 データ フローを初めて扱う場合は、[Mapping Data Flow の概要](concepts-data-flow-overview.md)に関するページを参照してください。

## <a name="syntax"></a>構文

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
dataflow | 実行されているデータ フローへの参照 | DataFlowReference | はい
integrationRuntime | データ フローが実行されているコンピューティング環境です。 指定されていない場合は、自動解決 Azure 統合ランタイムが使用されます。 | IntegrationRuntimeReference | いいえ
compute.coreCount | Spark クラスター内で使用されるコアの数です。 自動解決 Azure 統合ランタイムが使用されている場合にのみ指定できます。 | 8、16、32、48、80、144、272 | いいえ
compute.computeType | Spark クラスター内で使用されるコンピューティングの種類です。 自動解決 Azure 統合ランタイムが使用されている場合にのみ指定できます。 | "General"、"ComputeOptimized"、"MemoryOptimized" | いいえ
staging.linkedService | Azure Synapse Analytics ソースまたはシンクを使用している場合は、PolyBase ステージングに使用するストレージ アカウントを指定します。<br/><br/>Azure Storage が VNet サービス エンドポイントを使用して構成されている場合は、ストレージ アカウントで [信頼された Microsoft サービスを許可する] を有効にしたマネージド ID 認証を使用する必要があります。「[Azure Storage で VNet サービス エンドポイントを使用した場合の影響](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)」を参照してください。 また、[Azure Blob](connector-azure-blob-storage.md#managed-identity) と [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) に必要な構成についても説明します。<br/> | LinkedServiceReference | データ フローが Azure Synapse Analytics に対して読み取りまたは書き込みを行う場合のみ
staging.folderPath | Azure Synapse Analytics ソースまたはシンクを使用している場合は、PolyBase ステージングに使用する BLOB ストレージ アカウント内のフォルダー パス | String | データ フローが Azure Synapse Analytics に対して読み取りまたは書き込みを行う場合のみ
traceLevel | データ フロー アクティビティの実行のログ レベルを設定します | Fine、Coarse、None | No

:::image type="content" source="media/data-flow/activity-data-flow.png" alt-text="データ フローの実行":::

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>実行時、データ フロー コンピューティングのサイズを動的に設定する

Core Count プロパティと Compute Type プロパティは、実行時に入ってくるソース データのサイズに合わせて調整されるよう、動的に設定できます。 ソース データセット データのサイズを見つける目的で、Lookup や Get Metadata など、パイプライン アクティビティを使用します。 次に、Data Flow アクティビティ プロパティで Add Dynamic Content を使用します。

> [!NOTE]
> Azure Synapse Analytics データ フローでドライバーとワーカー ノードのコアを選択する場合は、常に少なくとも 3 つのノードが使用されます。

:::image type="content" source="media/data-flow/dyna1.png" alt-text="動的データ フロー":::

[この短い動画チュートリアルでこの手法について説明しています](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>データ フロー統合ランタイム

データ フロー アクティビティの実行に使用する統合ランタイムを選択します。 既定では、このサービスは 4 つのワーカー コアを持つ自動解決 Azure 統合ランタイムを使用します。 この IR は汎用目的のコンピューティングの種類で、ご使用のサービス インスタンスと同じリージョンで実行します。 運用可能なパイプラインの場合、データ フロー アクティビティの実行用に特定のリージョン、コンピューティングの種類、コア数、および TTL を定義する独自の Azure 統合ランタイムを作成することをお勧めします。

General Purpose の最小コンピューティング タイプ (コンピューティング最適化は大規模なワークロードには推奨されません) が 8 + 8 (合計 16 個の v コア) の構成で、ほとんどの運用ワークロードの最小推奨は 10 分です。 TTL を小さい値に設定することにより、Azure IR は、コールド クラスターに数分の開始時間を発生させないウォーム クラスターを維持できます。 Azure IR データ フローの構成で [クイック再利用] を選択すると、データ フローの実行時間を短縮できます。 詳細については、[Azure 統合ランタイム](concepts-integration-runtime.md)に関するページを参照してください。

:::image type="content" source="media/data-flow/ir-new.png" alt-text="Azure Integration Runtime":::

> [!IMPORTANT]
> データ フロー アクティビティでの Integration Runtime の選択は、お使いのパイプラインの *トリガー済みの実行* のみに適用されます。 データ フローを使用したパイプラインのデバッグは、デバッグ セッションで指定されたクラスターで実行されます。

### <a name="polybase"></a>PolyBase

Azure Synapse Analytics をシンクまたはソースとして使用する場合は、PolyBase バッチ読み込み用のステージングの場所を選択する必要があります。 PolyBase を使用すると、データを行ごとに読み込む代わりに一括してバッチ読み込みを行うことができます。 PolyBase を実行すると、Azure Synapse Analytics への読み込み時間が大幅に短縮されます。

## <a name="logging-level"></a>ログ記録レベル

データ フロー アクティビティのすべてのパイプラインを実行してすべての詳細なテレメトリ ログを完全にログする必要がない場合は、必要に応じてログ レベルを "基本" または "なし" に設定できます。 データ フローを [詳細] モード (既定値) で実行する場合、データ変換中に各パーティション レベルでアクティビティを完全にログするように、サービスに要求します。 これは負荷の高い操作であるため、トラブルシューティングを行うときにのみ詳細を有効にすることで、データ フローとパイプラインのパフォーマンス全体を向上させることができます。 "基本" モードでは、その変換の期間だけがログされるのに対し、"なし" を指定した場合は、期間の概要のみが提供されます。

:::image type="content" source="media/data-flow/logging.png" alt-text="ログ記録レベル":::

## <a name="sink-properties"></a>シンクのプロパティ

データ フローのグループ化機能を使用すると、シンクの実行順序を設定できるほか、同じグループ番号を使用してシンクをグループ化できます。 グループを管理しやすくするため、シンクを同じグループ内で並列で実行するように、サービスに要求できます。 また、いずれかのシンクでエラーが発生しても続行するようにシンク グループを設定することもできます。

データ フロー シンクの既定の動作では、各シンクが逐次実行され、シンクでエラーが発生した場合はデータ フローが失敗します。 さらに、データ フロー プロパティでシンクに異なる優先順位を設定しない限り、すべてのシンクは既定で同じグループに設定されます。

:::image type="content" source="media/data-flow/sink-properties.png" alt-text="シンクのプロパティ":::

### <a name="first-row-only"></a>First row only (先頭行のみ)

このオプションは、"アクティビティへの出力" でキャッシュ シンクが有効になっているデータ フローでのみ使用できます。 パイプラインに直接挿入されるデータ フローからの出力は、2 MB に制限されます。 "最初の行のみ" を設定すると、データ フロー アクティビティの出力をパイプラインに直接挿入する際に、データ フローからのデータ出力を制限することができます。

## <a name="parameterizing-data-flows"></a>データ フローをパラメーター化する

### <a name="parameterized-datasets"></a>パラメーター化されたデータセット

データ フローでパラメーター化されたデータセットを使用する場合は、 **[設定]** タブでパラメーター値を設定します。

:::image type="content" source="media/data-flow/params.png" alt-text="データ フローの実行パラメーター":::

### <a name="parameterized-data-flows"></a>パラメーター化されたデータ フロー

データ フローがパラメーター化されている場合は、 **[パラメーター]** タブでデータ フロー パラメーターの動的な値を設定します。パイプライン式言語またはデータ フロー式言語のいずれかを使用して、動的パラメーター値またはリテラル パラメーター値を割り当てることができます。 詳しくは、[データ フロー パラメーター](parameters-data-flow.md)に関するページを参照してください。

### <a name="parameterized-compute-properties"></a>パラメーター化されたコンピューティングのプロパティ

コア カウントまたはコンピューティングの種類は、自動解決 Azure 統合ランタイムを使用し、かつ compute.coreCount と compute.computeType の値を指定する場合にパラメーター化が可能です。

:::image type="content" source="media/data-flow/parameterize-compute.png" alt-text="データ フローの実行パラメーターの例":::

## <a name="pipeline-debug-of-data-flow-activity"></a>データ フロー アクティビティのパイプライン デバッグ

データ フロー アクティビティを使用してデバッグ パイプラインを実行するには、上部バーにある **[Data Flow Debug]\(データ フロー デバッグ\)** スライダーを使用して、データ フロー デバッグ モードをオンに切り替える必要があります。 デバッグ モードでは、アクティブな Spark クラスターに対してデータ フローを実行できます。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

:::image type="content" source="media/data-flow/debug-button-3.png" alt-text="[デバッグ] ボタンの場所を示すスクリーンショット":::

デバッグ パイプラインは、データ フロー アクティビティ設定で指定された統合ランタイム環境ではなく、アクティブなデバッグ クラスターに対して実行されます。 デバッグ モードを開始するときに、デバッグ コンピューティング環境を選択できます。

## <a name="monitoring-the-data-flow-activity"></a>データ フロー アクティビティを監視する

データ フロー アクティビティには、パーティション分割、ステージ時間、およびデータ系列の情報を表示できる特別な監視エクスペリエンスがあります。 **[アクション]** の下にある眼鏡アイコンを使用して、[監視] ウィンドウを開きます。 詳しくは、[データ フローの監視](concepts-data-flow-monitoring.md)に関するページを参照してください。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>後続のアクティビティでデータ フロー アクティビティの結果を使用する

データ フロー アクティビティは、各シンクに書き込まれた行の数と各ソースから読み取られた行に関するメトリックを出力します。 これらの結果は、アクティビティの実行結果の `output` セクションに返されます。 返されるメトリックは、以下の JSON の形式です。

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

たとえば、'dataflowActivity' という名前のアクティビティで、'sink1' という名前のシンクに書き込まれた行の数を取得するには、`@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` を使用します。

このシンクで使用されていた、'source1' という名前のソースから読み取られた行の数を取得するには、`@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` を使用します。

> [!NOTE]
> シンクに書き込まれた行が 0 の場合は、メトリックに表示されません。 存在を確認するには、`contains` 関数を使用します。 たとえば、`contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` は、sink1 に行が書き込まれたかどうかを確認します。

## <a name="next-steps"></a>次のステップ

サポートされている制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
