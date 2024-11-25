---
title: 機械学習ツールとデータ サイエンス ツール
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine にあらかじめインストールされている機械学習ツールとフレームワークについて学習します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 86cbac686c2f994dff4042ea2a227156d9e45472
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742047"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure Data Science Virtual Machine 上の機械学習ツールとデータ サイエンス ツール
Azure Data Science Virtual Machine (DSVM) には、Python、R、Julia などの一般的な言語で使用できる、機械学習のための豊富な一連のツールやライブラリが備わっています。

ここでは、DSVM の機械学習ツールおよびライブラリの一部を示します。

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK for Python

[Azure Machine Learning SDK for Python の詳細についてはこちら ](../overview-what-is-azure-machine-learning.md) を参照してください。

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   |   Azure Machine Learning は、機械学習モデルの開発とデプロイに使用できるクラウド サービスです。 Python SDK を使用して、モデルの構築、トレーニング、スケール調整、および管理を行うときにそれらのモデルを追跡できます。 モデルをコンテナーとしてデプロイし、それをクラウド、オンプレミス、または Azure IoT Edge で実行します。   |
| サポートされているエディション     | Windows (Conda 環境:AzureML)、Linux (Conda 環境: py36)    |
| 標準的な使用      | 一般的な機械学習プラットフォーム      |
| 構成またはインストール方法      |  GPU サポートと共にインストールされます   |
| 使用または実行方法      | Python SDK および Azure CLI として。 Windows エディションでは conda 環境 `AzureML` に、*または* Linux エディションでは `py36` にアクティブ化します。      |
| サンプルへのリンク      | サンプルの Jupyter ノートブックは、`AzureML` ディレクトリの notebooks の下に含まれています。  |

## <a name="h2o"></a>H2O

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | インメモリ、分散型、高速で、かつスケーラブルな機械学習をサポートするオープンソースの AI プラットフォームです。  |
| サポートされているバージョン      | Linux   |
| 標準的な使用      | 汎用で分散型のスケーラブル機械学習   |
| 構成またはインストール方法      | H2O は `/dsvm/tools/h2o` にインストールされます。      |
| 使用または実行方法      | X2Go を使用して VM に接続します。 新しいターミナルを起動し、`java -jar /dsvm/tools/h2o/current/h2o.jar` を実行します。 次に、Web ブラウザーを起動して `http://localhost:54321` に接続します。      |
| サンプルへのリンク      | サンプルは、VM 上の Jupyter の `h2o` ディレクトリの下にあります。      |

DSVM には、DSVM 用の Anaconda Python ディストリビューションの一部である一般的な `scikit-learn` パッケージなど、他にもいくつかの機械学習ライブラリがあります。 Python、R、および Julia で使用可能なパッケージの一覧を確認するには、それぞれのパッケージ マネージャーを実行します。

## <a name="lightgbm"></a>LightGBM

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | ディシジョン ツリー アルゴリズムに基づく、高速な、分散型、高パフォーマンスの勾配ブースティング (GBDT、GBRT、GBM、または MART) フレームワークです。 これは、ランク付け、分類、その他の多くの機械学習タスクに使用されます。    |
| サポートされているバージョン      | Windows、Linux    |
| 標準的な使用      | 汎用の勾配ブースティング フレームワーク      |
| 構成またはインストール方法      | Windows では、LightGBM は Python パッケージとしてインストールされます。 Linux では、コマンド ラインの実行可能ファイルは `/opt/LightGBM/lightgbm` にあり、R パッケージがインストールされ、Python パッケージがインストールされます。     |
| サンプルへのリンク      | [LightGBM ガイド](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |

## <a name="rattle"></a>Rattle
| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   |   R を使用したデータ マイニングのためのグラフィカル ユーザー インターフェイス。   |
| サポートされているエディション     | Windows、Linux     |
| 標準的な使用      | R 用の一般的な UI データ マイニング ツール    |
| 使用または実行方法      | UI ツールとして。 Windows では、コマンド プロンプトを起動し、R を実行してから、R 内で `rattle()` を実行します。 Linux では、X2Go で接続し、ターミナルを起動し、R を実行してから、R 内で `rattle()` を実行します。 |
| サンプルへのリンク      | [Rattle](https://togaware.com/onepager/) |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   |   高速でオープンソースの、コア以外の学習システム ライブラリ    |
| サポートされているエディション     | Windows、Linux     |
| 標準的な使用      | 一般的な機械学習ライブラリ      |
| 構成またはインストール方法      |  Windows: MSI インストーラー<br/>Linux: apt-get |
| 使用または実行方法      | パスの通ったコマンド ライン ツール (Windows では `C:\Program Files\VowpalWabbit\vw.exe`、Linux では `/usr/bin/vw`) として    |
| サンプルへのリンク      | [VowPal Wabbit サンプル](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |

## <a name="weka"></a>Weka
| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   |  データ マイニング タスクのための機械学習アルゴリズムのコレクション。 これらのアルゴリズムは、データ セットに直接適用するか、または独自の Java コードから呼び出すことができます。 Weka には、データの前処理、分類、回帰、クラスタリング、アソシエーション ルール、および視覚化のためのツールが含まれています。 |
| サポートされているエディション     | Windows、Linux     |
| 標準的な使用      | 一般的な機械学習ツール     |
| 使用または実行方法      | Windows では、 **[スタート]** メニューで Weka を探します。 Linux では、X2Go でサインインし、 **[アプリケーション]**  >  **[開発]**  >  **[Weka]** に移動します。 |
| サンプルへのリンク      | [Weka サンプル](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |

## <a name="xgboost"></a>XGBoost 
| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   |   Python、R、Java、Scala、C++ など用の、高速で、可搬性があり、分散型の勾配ブースティング (GBDT、GBRT、または GBM) ライブラリ。 これは 1 台のコンピューター、Apache Hadoop、Spark 上で実行されます。    |
| サポートされているエディション     | Windows、Linux     |
| 標準的な使用      | 一般的な機械学習ライブラリ      |
| 構成またはインストール方法      |  GPU サポートと共にインストールされます   |
| 使用または実行方法      | Python ライブラリ (2.7 および 3.6 以降)、R パッケージ、パスの通ったコマンド ライン ツール (Windows では `C:\dsvm\tools\xgboost\bin\xgboost.exe`、Linux では `/dsvm/tools/xgboost/xgboost`) として    |
| サンプルへのリンク      | サンプルは、VM 上の Linux では `/dsvm/tools/xgboost/demo`、Windows では `C:\dsvm\tools\xgboost\demo` に含まれています。   |
