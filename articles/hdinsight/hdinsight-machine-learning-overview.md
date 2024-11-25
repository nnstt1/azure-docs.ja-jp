---
title: 機械学習の概要 - Azure HDInsight
description: Azure HDInsight 内のクラスターのためのビッグ データの機械学習オプションの概要。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: a911e468443fe49bb1edc18af3d3412edc8eb8cc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678218"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight での機械学習

HDInsight ではビッグ データでの機械学習が可能であるため、大量 (数ペタバイト、場合によっては数エクサバイト) の構造化、非構造化、および高速移動データから貴重な洞察を得る機能が提供されます。 HDInsight には、SparkML と Apache Spark MLlib、Apache Hive、Microsoft Cognitive Toolkit などのいくつかの機械学習オプションがあります。

## <a name="sparkml-and-mllib"></a>SparkML と MLlib

[HDInsight Spark](spark/apache-spark-overview.md) は、Azure でホストされる [Apache Spark](https://spark.apache.org/) のサービスであり、ビッグ データ分析を向上させるためのメモリ内処理をサポートする、統合されたオープンソースの並列データ処理フレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 この分散環境にアルゴリズム モデリング機能を提供するスケーラブルな機械学習ライブラリとして、MLlib と SparkML の 2 つがあります。 MLlib には、RDD 上に構築されたオリジナルの API が含まれています。 SparkML は、ML パイプラインを構成するために DataFrames 上に構築されたより高レベルの API を提供する新しいパッケージです。 SparkML は、まだ MLlib のすべての機能をサポートしているわけではありませんが、MLlib は Spark の標準の機械学習ライブラリと置き換えられています。

Apache Spark 用の Microsoft Machine Learning ライブラリは [MMLSpark](https://github.com/Azure/mmlspark) です。 このライブラリは、Spark 上でのデータ サイエンティストの生産性を高め、実験の速度を向上させ、さらに非常に大規模なデータセットに対してディープ ラーニングを含む最先端の機械学習手法を活用するように設計されています。 文字列のインデックス作成、機械学習アルゴリズムによって予測されるレイアウトへのデータの強制的な移行、特徴ベクトルのアセンブルなどのスケーラブルな ML モデルを構築する場合、MMLSpark は SparkML の低レベルの API の上に 1 つのレイヤーを提供します。 MMLSpark ライブラリはこれらのタスクや、PySpark でモデルを構築するためのその他の一般的なタスクを簡略化します。

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning と Apache Hive

Azure Machine Learning には、予測分析をモデル化するためのツールと、予測モデルをすぐに使用できる web サービスとして、デプロイするための完全管理型のサービスが用意されています。 Azure Machine Learning は、予測モデルの作成、テスト、操作可能化、および管理のために使用できる、クラウド内の完全な予測分析ソリューションです。 大規模なアルゴリズム ライブラリの中から選択し、モデルを構築するための Web ベースのスタジオを使用して、ご利用のモデルを簡単に Web サービスとしてデプロイできます。

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Microsoft Azure 機械学習の概要" border="false":::

[Hive クエリ](/azure/architecture/data-science-process/create-features-hive)を使用して、HDInsight Hadoop クラスター内のデータの特徴を作成します。 *特徴エンジニアリング* は、学習プロセスを容易にする生データの特徴を作成することによって、学習アルゴリズムの予測能力を向上させようとします。 Azure Machine Learning Studio (クラシック) から HiveQL クエリを実行し、[データのインポート モジュール](../machine-learning/classic/import-data.md)を使用して、Hive で処理され、Blob Storage に格納されているデータにアクセスできます。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[ディープ ラーニング](https://www.microsoft.com/en-us/research/group/dltc/)は、人間の脳の生物学的プロセスから着想を得た、ニューラル ネットワークを使用する機械学習の 1 分野です。 多くの研究者がディープ ラーニングを、人工知能を拡張するための将来性のあるアプローチとして見ています。 ディープ ラーニングの例には、音声言語翻訳機、画像認識システム、および機械推論があります。

ディープ ラーニングにおける独自の機能の進歩に役立つように、Microsoft は無料で、使いやすい、オープンソースの [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) を開発しました。 このツールキットは、ディープ ラーニングを大規模に展開する必要がある世界中の企業や、最新のアルゴリズムと手法に関心がある学生によって、さまざまな Microsoft 製品で使用されています。

## <a name="see-also"></a>関連項目

### <a name="scenarios"></a>シナリオ

* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Apache Mahout で映画のレコメンデーションを生成する](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive と Azure Machine Learning](/azure/architecture/data-science-process/create-features-hive)
* [Apache Hive と Azure Machine Learning の詳細](/azure/architecture/data-science-process/hive-walkthrough)
* [HDInsight での Apache Spark を使用した機械学習](/azure/architecture/data-science-process/spark-overview)

### <a name="deep-learning-resources"></a>ディープ ラーニングのリソース

* [Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit ディープ ラーニング モデルを使用する](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Data Science Virtual Machine (DSVM) でのディープ ラーニングおよび AI フレームワーク](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
