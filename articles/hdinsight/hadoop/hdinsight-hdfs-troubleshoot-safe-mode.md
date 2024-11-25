---
title: Azure HDInsight クラスター上のローカル HDFS がセーフ モードでスタックする
description: Azure HDInsight の Apache クラスター上でセーフ モードでスタックするローカル Apache HDFS のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 6764b2c1b8b2a06f2bd9264a7461f43986f557ad
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299971"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>シナリオ: Azure HDInsight クラスター上のローカル HDFS がセーフ モードでスタックする

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDInsight クラスター上でローカル Apache Hadoop 分散ファイル システム (HDFS) のセーフ モードが解除されない。 次のようなエラー メッセージが表示されます。

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>原因

HDFS レプリケーション係数未満か、それに近いごく少数のノードにまで、HDInsight クラスターがスケール ダウンされました。

## <a name="resolution"></a>解決方法

1. 次のコマンドを使用して、HDInsight クラスター上の HDFS の状態をレポートします。

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. 次のコマンドで、HDInsight クラスター上の HDFS の整合性をチェックします。

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. 欠落しているブロックや破損しているブロック、レプリケート数不足のブロックが存在しないことがわかっている場合や、それらのブロックを無視できる場合は、次のコマンドを実行して、ネーム ノードのセーフ モードを解除します。

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]