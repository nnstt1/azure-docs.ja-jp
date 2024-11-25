---
title: BindException - アドレスが Azure HDInsight で既に使用されている
description: BindException - アドレスが Azure HDInsight で既に使用されている
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 720cdf4ddcb14351076d209011a087c3b30123e6
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369467"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>シナリオ: BindException - アドレスが Azure HDInsight で既に使用されている

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache HBase Region Server で再起動操作を完了できない。 リージョン サーバーの起動に失敗したワーカー ノードの `/var/log/hbase` ディレクトリにある `region-server.log` から、次のようなエラー メッセージが表示される場合があります。

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

負荷の高いワークロード アクティビティ中に Apache HBase リージョン サーバーを再起動しています。 ユーザーが Apache Ambari UI から HBase リージョン サーバーで再起動処理を開始したときにバックグラウンドで何が行われているかを以下に示します。

1. Ambari エージェントがリージョン サーバーに停止要求を送信します。

1. Ambari エージェントは、リージョン サーバーが正常にシャットダウンするのを 30 秒間待ちます。

1. アプリケーションが引き続きリージョン サーバーに接続していると、サーバーはすぐにはシャットダウンしません。 シャットダウンが発生する前に 30 秒が経過してタイムアウトになります。

1. 30 秒が経過すると、Ambari エージェントは強制終了 (`kill -9`) コマンドをリージョン サーバーに送信します。

1. このように突然シャットダウンされるために、リージョン サーバーのプロセスが強制終了されても、プロセスに関連付けられているポートが解放されない場合があり、最終的に `AddressBindException` が発生します。

## <a name="resolution"></a>解決方法

再起動を開始する前に HBase リージョン サーバーの負荷を軽減します。 また、最初にすべてのテーブルをフラッシュしておくことをお勧めします。 テーブルをフラッシュする方法については、[HDInsight HBase: テーブルをフラッシュして Apache HBase クラスターの再起動時間を短縮する方法](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables)に関するページをご覧ください。

または、ワーカー ノードで次のコマンドを使用してリージョン サーバーを手動で再起動します。

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
