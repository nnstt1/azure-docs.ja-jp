---
title: サポートされているデータ プラットフォーム
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine のサポートされているデータ プラットフォームおよびツールについて説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 04/29/2021
ms.openlocfilehash: 3e071ad5f86d13270144ec8eb7839f31601b5c39
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432308"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine でサポートされているデータ プラットフォーム

Data Science Virtual Machine (DSVM) を使用すると、さまざまなデータ プラットフォームに対して分析を作成することができます。 リモート データ プラットフォームへのインターフェイスに加えて、DSVM は、迅速な開発およびプロトタイプ作成のためのローカル インスタンスを提供します。

DSVM でサポートされているデータ プラットフォーム ツールは次のとおりです。

## <a name="sql-server-developer-edition"></a>SQL Server Developer エディション

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | ローカルのリレーショナル データベース インスタンス      |
| サポートされている DSVM エディション      | Windows 2019、Ubuntu 18.04 (SQL Server 2019)   |
| 標準的な使用      | <ul><li>比較的小さなデータセットを使用したローカルでの迅速開発</li><li>In-Database R の実行</li></ul> |
| サンプルへのリンク      | <ul><li>New York City データセットの小さなサンプルが、次の SQL データベースに読み込まれます。<br/>  `nyctaxi`</li><li>Microsoft Machine Learning Server およびデータベース内の分析を示す Jupyter サンプルは次の場所にあります。<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| DSVM 上の関連ツール       | <ul><li>SQL Server Management Studio</li><li>ODBC および JDBC ドライバー</li><li>pyodbc、RODBC</li></ul> |

> [!NOTE]
> SQL Server Developer エディションは、開発およびテスト目的でのみ使用できます。 実稼働環境で実行するには、ライセンスまたはいずれかの SQL Server VM が必要です。

> [!NOTE]
> Machine Learning Server スタンドアロンのサポートは 2021 年 7 月 1 日に終了します。 これは 6 月 30 日以降、DSVM イメージから削除されます。 既存のデプロイは引き続きソフトウェアにアクセスできますが、サポート終了日に達したため、2021 年 7 月 1 日以降、サポートはなくなります。

> [!NOTE]
> SQL Server Developer エディションは、2021 年 11 月末をもって DSVM イメージから削除されます。 既存のデプロイには、今後も SQL Server Developer エディションがインストールされます。 新規デプロイで SQL Server Developer エディションにアクセスできるようにするには、Docker サポート経由でインストールして使用することができます。詳細については、「[クイック スタート:Docker を使用して SQL Server コンテナー イメージを実行する](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash&preserve-view=true)」を参照してください

### <a name="windows"></a>Windows

#### <a name="setup"></a>セットアップ

データベース サーバーは既に事前構成されており、SQL Server に関連する Windows サービス (`SQL Server (MSSQLSERVER)` など) は自動的に実行されるように設定されています。 手動で行う唯一の手順は、Microsoft Machine Learning Server を使用してデータベース内分析を有効にすることです。 SQL Server Management Studio (SSMS) で 1 回限りの操作として次のコマンドを実行することで、分析を行うことができます。 マシン管理者としてログインし、SSMS で新しいクエリを開き、選択したデータベースが `master` であることを確認した後、このコマンドを実行します。

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(%COMPUTERNAME% を自分の VM 名に置き換えます。)

SQL Server Management Studio を実行するには、プログラムの一覧から "SQL Server Management Studio" を探すか、または Windows Search を使用してこれを探して実行します。 資格情報の入力を求められたら、 **[Windows 認証]** を選択し、 **[SQL Server 名]** フィールドにはマシン名または ```localhost``` を使用します。

#### <a name="how-to-use-and-run-it"></a>使用と実行方法

既定では、既定のデータベース インスタンスがあるデータベース サーバーは自動的に実行されます。 VM 上の SQL Server Management Studio などのツールを使用して、SQL Server データベースにローカルでアクセスできます。 ローカル管理者アカウントには、データベースへの管理者アクセス権があります。

また DSVM には、Python、Machine Learning Server を含む複数の言語で記述されたアプリケーションから SQL Server、Azure SQL データベース、および Azure Synapse Analytics と通信するための ODBC ドライバーおよび JDBC ドライバーが付属しています。

#### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM での構成とインストール方法 

 SQL Server は、標準の方法でインストールされます。 その場所は `C:\Program Files\Microsoft SQL Server` です。 データベース内 Machine Learning Server インスタンスは、`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` にあります。 DSVM には、`C:\Program Files\Microsoft\R Server\R_SERVER` にインストールされる別のスタンドアロン Machine Learning Server インスタンスもあります。 これらの 2 つの Machine Learning Server インスタンスは、ライブラリを共有しません。


### <a name="ubuntu"></a>Ubuntu

Ubuntu DSVM で SQL Server Developer エディションを使用するには、まずそれをインストールする必要があります。 方法については、「[クイック スタート:Ubuntu に SQL Server をインストールし、データベースを作成する](/sql/linux/quickstart-install-connect-ubuntu)」で説明しています。



## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (スタンドアロン)

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | 広く普及した Apache Spark プラットフォームのスタンドアロン (シングル ノード インプロセス) インスタンス、高速で大規模なデータ処理および機械学習のためのシステム     |
| サポートされている DSVM エディション      | Linux     |
| 標準的な使用      | <ul><li>小さなデータセットを使用してローカルで Spark/PySpark アプリケーションを迅速に開発し、後で Azure HDInsight などの大規模 Spark クラスターにデプロイする</li><li>Microsoft Machine Learning Server Spark コンテキストをテストする</li><li>SparkML または Microsoft のオープン ソース [MMLSpark](https://github.com/Azure/mmlspark) ライブラリを使用して ML アプリケーションを構築する</li></ul> |
| サンプルへのリンク      |    Jupyter サンプル:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark コンテキスト): /dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| DSVM 上の関連ツール       | <ul><li>PySpark、Scala</li><li>Jupyter (Spark/PySpark カーネル)</li><li>Microsoft Machine Learning Server、SparkR、Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>使用方法
コマンド ラインで Spark ジョブを送信するには、 `spark-submit` または `pyspark` コマンドを実行します。 Spark カーネルで新しいノートブックを作成することによって、Jupyter ノートブックを作成することもできます。

DSVM で使用可能な SparkR、Sparklyr、Microsoft Machine Learning Server などのライブラリを使用して、R から Spark を使用できます。 前出の表のサンプルへのリンクを参照してください。

### <a name="setup"></a>セットアップ
Ubuntu Linux DSVM エディション上の Microsoft Machine Learning Server で Spark コンテキストで実行する前に、1 回限りのセットアップ手順を実行して、単一ノードのローカル Hadoop (HDFS と Yarn) インスタンスを有効にする必要があります。 Hadoop サービスはインストールされていますが、既定では DSVM で無効になっています。 これらを有効にするには、最初に次のコマンドを root 権限で実行します。

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Hadoop 関連サービスが不要になった場合は、```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` を実行してそれらを停止することができます。

MRS をリモート Spark コンテキスト (つまり DSVM 上のスタンドアロン Spark インスタンス) で開発およびテストする方法を示したサンプルは、`/dsvm/samples/MRS` ディレクトリで入手して使用することができます。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM での構成とインストール方法 
|プラットフォーム|インストール場所 ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Azure Blob Storage または Azure Data Lake Storage から、Microsoft MMLSpark 機械学習ライブラリを使用してデータにアクセスするためのライブラリは、$SPARK_HOME/jars にプレインストールされています。 これらの JAR は Spark の起動時に自動的に読み込まれます。 既定では、Spark はローカル ディスク上のデータを使用します。 

DSVM 上の Spark インスタンスから Blob ストレージまたは Azure Data Lake Storage に格納されているデータにアクセスするには、$SPARK_HOME/conf/core-site.xml.template にあるテンプレートに基づいて `core-site.xml` ファイルを作成して構成する必要があります。 Blob ストレージと Azure Data Lake Storage にアクセスするための適切な資格情報も必要です。 (テンプレート ファイルでは、Blob ストレージおよび Azure Data Lake Storage の構成にプレースホルダーが使用されることに注意してください。)

Azure Data Lake Storage サービス資格情報の作成方法の詳細情報については、[Azure Data Lake Storage Gen1 に対する認証](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)に関する記事を参照してください。 Blob ストレージまたは Azure Data Lake Storage の資格情報が core-site.xml ファイルに入力されると、URI プレフィックス wasb:// または adl:// を使用して、それらのソースに格納されたデータを参照できます。