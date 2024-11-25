---
title: データ処理を最適化する
titleSuffix: Azure Machine Learning
description: データ処理速度を最適化するためのベスト プラクティスと、大規模なデータ処理のために Azure Machine Learning でサポートされる統合について説明します。
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: mldata
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 10/21/2021
ms.custom: data4ml
ms.openlocfilehash: b2c52022fdc3a3f71da5dd30853820ab480bbc1b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559886"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Azure Machine Learning を使用したデータ処理の最適化

この記事では、データ処理速度をローカルかつ大規模で最適化するためのベスト プラクティスについて説明します。

Azure Machine Learning は、データ処理のためのオープンソース パッケージとフレームワークに統合されています。 この記事では、これらの統合を使用してベスト プラクティスの推奨事項を適用することで、ローカルと大規模の両方でデータ処理速度を向上させることができます。

## <a name="parquet-and-csv-file-formats"></a>Parquet ファイル形式と CSV ファイル形式

コンマ区切り値 (CSV) ファイルは、データ処理に使用される一般的なファイル形式です。 ただし、機械学習タスクには Parquet ファイル形式を使用することをお勧めします。

[Parquet ファイル](https://parquet.apache.org/)は、バイナリ列形式でデータを格納します。 この形式は、データを複数のファイルに分割する必要がある場合に便利です。 またこの形式では、機械学習の実験に関連するフィールドをターゲットとすることができます。 20 GB のデータ ファイルを読み取るのではなく、ML モデルのトレーニングに必要な列を選択することで、そのデータの読み込み量を減らすことができます。 Parquet ファイルを圧縮することで、処理能力を最小化して占有する容量を少なくすることもできます。

CSV ファイルは、Excel での編集や読み取りが簡単であるため、データのインポートやエクスポートによく利用されます。 CSV のデータは行ベースの形式で文字列として格納され、データ転送の負荷を軽減するためにファイルを圧縮できます。 圧縮されていない CSV は約 2 - 10 倍に膨張し、圧縮された CSV では、さらに増大する可能性があります。 このため、メモリ内の 5 GB の CSV は、マシンに搭載された 8 GB の RAM を優に超えて膨張します。 この圧迫挙動により、データ転送の待機時間が増大する可能性があり、大量のデータを処理する場合には最適ではありません。 

## <a name="pandas-dataframe"></a>Pandas データフレーム

[Pandas データフレーム](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)は、データの操作と分析によく使用されます。 `Pandas` は 1 GB 未満のデータ サイズには適していますが、ファイル サイズが 1 GB 付近になると、`pandas` データフレーム処理時間が遅くなります。 これは、ストレージ内のデータのサイズが、データフレーム内のデータのサイズと同じではないためです。 たとえば、CSV ファイルのデータはデータフレームで最大 10 倍まで膨張する可能性があるため、1 GB の CSV ファイルがデータフレームで 10 GB になる可能性があります。

`Pandas` はシングル スレッドです。つまり、操作は 1 つの CPU で一度に 1 つずつ実行されます。 分散型バックエンドを利用して `Pandas` をラップする [Modin](https://modin.readthedocs.io/en/latest/) のようなパッケージを使用することで、1 つの Azure Machine Learning コンピューティング インスタンス上の複数の仮想 CPU にワークロードを簡単に並列化できます。

`Modin` および [Dask](https://dask.org) を使用してタスクを並列化するには、コードの `import pandas as pd` の行を `import modin.pandas as pd` に変更するだけです。

## <a name="dataframe-out-of-memory-error"></a>データフレーム: メモリ不足エラー 

通常、*メモリ不足* エラーは、お使いのマシンで利用可能な RAM を超えてデータフレームが膨張したときに発生します。 この概念は、`Modin` や `Dask` などの分散フレームワークにも適用されます。  つまり、操作ではクラスター内の各ノードのメモリにデータフレームを読み込もうとしますが、そのために必要な RAM が不足しています。

解決策の 1 つは、データフレームに合わせて RAM を増設することです。 コンピューティング サイズと処理能力を RAM のサイズの 2 倍にすることをお勧めします。 つまりデータフレームが 10 GB の場合、データフレームがメモリ内に収まり、処理されるようにするには、少なくとも 20 GB の RAM を搭載したコンピューティング先を使用します。 

複数の仮想 CPU (vCPU) では、各 vCPU がマシン上で持つことのできる RAM に収まるように、パーティションを 1 つずつ配置する必要があることに注意してください。 つまり、16 GB RAM で 4 つの仮想 CPU を使用している場合は、各 vCPU につき約 2 GB のデータフレームとする必要があります。

### <a name="local-vs-remote"></a>ローカルとリモートの比較

特定の Pandas データフレーム コマンドの実行が、ローカル PC で作業した場合に、Azure Machine Learning でプロビジョニングしたリモート VM と比べて速くなることがあります。 ローカル PC では通常、ページ ファイルが有効になっているため、物理メモリの容量を超えて読み込むことができます。つまり、ハードドライブが RAM の拡張として使用されます。 現時点では、Azure Machine Learning の VM はページ ファイルなしで実行されるため、使用可能な物理 RAM に収まるだけのデータを読み込むことができます。 

コンピューティング負荷の高いジョブの場合は、処理速度を向上させるために、より大きな VM を選択することをお勧めします。

Azure Machine Learning で[使用可能な VM シリーズとサイズ](concept-compute-target.md#supported-vm-series-and-sizes)の詳細に関する記事を参照してください。 

RAM 仕様については、[Dv2-Dsv2 シリーズ](../virtual-machines/dv2-dsv2-series-memory.md)や [NC シリーズ](../virtual-machines/nc-series.md)など、対応する VM シリーズのページを参照してください。

### <a name="minimize-cpu-workloads"></a>CPU の負荷を最小限に抑える

マシンに RAM を追加できない場合は、CPU の負荷を最小限に抑えて処理時間を最適化するために、次の手法を適用できます。 これらの推奨事項は、単一システムと分散システムの両方に適用されます。

手法 | 説明
----|----
圧縮 | 使用するメモリが少なく、計算結果に大きな影響を与えない方法で、データに対して異なる表現を使用します。<br><br>*例:* エントリを、1 エントリあたり約 10 バイト以上となる文字列として格納するのではなく、True または False のブール値として格納します。これは 1 バイトで格納できます。
チャンキング | データをサブセット (チャンク) としてメモリに読み込み、一度に 1 つのサブセットを処理するか、複数のサブセットを並行して処理します。 この方法は、すべてのデータを処理する必要があるが、すべてのデータを一度にメモリに読み込む必要はない場合に最適です。 <br><br>*例:* 1 年分のデータを一度に処理するのではなく、1 か月単位でデータを読み込んで処理します。
インデックス作成 | 関心のあるデータの場所を示す要約である、インデックスを適用して使用します。 インデックス作成は、データの完全なセットではなくてサブセットのみを使用する必要がある場合に便利です。<br><br>*例:* 1 年分の売上データが月別に並べ替えられている場合に、インデックスを使用して、処理する必要のある月をすばやく検索することができます。

## <a name="scale-data-processing"></a>データ処理のスケーリング

上記の推奨事項では不十分で、かつデータに適合する仮想マシンを用意できない場合は、次のことを実行できます。 

* `Spark` や `Dask` などのフレームワークを使用して、データの "メモリ不足" に対処します。 このオプションでは、データフレームはパーティション毎に RAM に読み込まれて処理され、最終的な結果は最後に集約されます。  

* 分散フレームワークを使用してクラスターにスケールアウトします。 このオプションでは、データ処理の負荷は、並列で動作する複数の CPU に分割されて処理され、最終的な結果は最後に集約されます。

### <a name="recommended-distributed-frameworks"></a>推奨される分散フレームワーク

次の表は、コードの選択またはデータ サイズに基づいて推奨される、Azure Machine Learning と統合された分散フレームワークを示しています。

経験またはデータ サイズ | 推奨
------|------
`Pandas` に慣れている場合| `Modin` または `Dask` データフレーム
`Spark` を希望する場合 | `PySpark`
1 GB 未満のデータ | ローカルの `Pandas` **または** リモートの Azure Machine Learning コンピューティング インスタンス
10 GB を超えるデータの場合| `Ray`、`Dask`、または `Spark` を使用してクラスターに移動

> [!TIP]
> 大規模なデータ処理のために、[to_dask_dataframe()](/python/api/azureml-core/azureml.data.tabulardataset#to-dask-dataframe-sample-size-10000--dtypes-none--on-error--null---out-of-range-datetime--null--) メソッドを使用して、データセットを Dask データフレームに読み込みます。 このメソッドは[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能であり、いつでも変更される可能性があります。

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning を使用したデータ インジェスト オプション](concept-data-ingestion.md)。
* [データセットを作成して登録する](how-to-create-register-datasets.md)。
