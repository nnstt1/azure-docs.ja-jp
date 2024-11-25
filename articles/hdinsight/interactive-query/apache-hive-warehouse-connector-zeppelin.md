---
title: Hive Warehouse Connector - Livy を使用した Apache Zeppelin - Azure HDInsight
description: Azure HDInsight 上で Hive Warehouse Connector と Apache Zeppelin を統合する方法について説明します。
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 6a7d58a1646e77e99ad7282af5be0dc1f8d2976d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750808"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight で Apache Zeppelin と Hive Warehouse Connector を統合する

HDInsight Spark クラスターには、異なるインタープリターを持つ Apache Zeppelin Notebook が含まれています。 この記事では、Hive Warehouse Connector を使用して Spark から Hive テーブルにアクセスする Livy インタープリターに絞って説明します。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*ホワイトリスト*" という用語への言及があります。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

## <a name="prerequisite"></a>前提条件

[Hive Warehouse Connector の設定](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)ステップを完了します。

## <a name="getting-started"></a>作業の開始

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して Apache Spark クラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから次のコマンドを実行して、`hive-warehouse-connector-assembly` と `pyspark_hwc` のバージョンを確認します。

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    後で Apache Zeppelin を構成するときに使用するために、出力を保存します。

## <a name="configure-livy"></a>Livy を構成する

Livy インタープリターを使用して Zeppelin から Hive テーブルにアクセスするには、次の構成が必要です。

### <a name="interactive-query-cluster"></a>対話型クエリ クラスター

1. Web ブラウザーから、`https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` に移動します。ここで、LLAPCLUSTERNAME は対話型クエリ クラスターの名前です。

1. **[詳細]**  >  **[カスタム core-site]** の順に移動します。 **[プロパティの追加...]** を選択して、次の構成を追加します。

    | 構成                 | 値 |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. 変更を保存し、影響を受けるすべてのコンポーネントを再起動します。

### <a name="spark-cluster"></a>Spark クラスター

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` に移動します。ここで、CLUSTERNAME は Apache Spark クラスターの名前です。

1. **カスタム livy2-conf** を展開します。 **[プロパティの追加...]** を選択して、次の構成を追加します。

    | 構成                 | 値                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. 変更を保存し、影響を受けるすべてのコンポーネントを再起動します。

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Zeppelin UI で Livy インタープリターを構成する (Spark クラスター)

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` に移動します。ここで、`CLUSTERNAME` は実際の Apache Spark クラスターの名前です。

1. **[livy2]** に移動します。

1. 次の構成を追加します。

    | 構成                 | 値                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy、zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>VERSION を、前述の「[作業の開始](#getting-started)」で取得した値に置き換えます。 |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>VERSION を、前述の「[作業の開始](#getting-started)」で取得した値に置き換えます。 |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | 対話型クエリ クラスターの HiveServer2 対話型 JDBC URL に設定します。 |
    | spark.security.credentials.hiveserver2.enabled | true |

1. ESP クラスターの場合のみ、次の構成を追加します。

    | 構成| 値|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/_HOST@<AAD-Domain>` |

    * [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して Interactive Query クラスターに接続します。 `/etc/krb5.conf` ファイルで `default_realm` パラメーターを探します。 `<AAD-DOMAIN>` は、その値に置き換えます。値の文字列は大文字にしてください。そうしないと、資格情報が見つからなくなります。

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Hive Warehouse Connector の AAD ドメイン" border="true":::

1. 変更を保存し、Livy インタープリターを再起動します。

Livy インタープリターがアクセスできない場合、Ambari の Zeppelin コンポーネント内に存在する `shiro.ini` ファイルを変更します。 詳細については、[Apache Zeppelin Security の構成](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html)に関するページをご覧ください。  

## <a name="running-queries-in-zeppelin"></a>Zeppelin でのクエリの実行

Livy インタープリターを使用して Zeppelin Notebook を起動し、次のように実行します

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>次のステップ

* [HWC と Apache Spark の操作](./apache-hive-warehouse-connector-operations.md)
* [Apache Spark および Apache Hive との HWC の統合](./apache-hive-warehouse-connector.md)
* [HDInsight での対話型クエリの使用](./apache-interactive-query-get-started.md)