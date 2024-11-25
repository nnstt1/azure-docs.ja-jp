---
title: Azure HDInsight クラスターを自動的にスケール調整する
description: 自動スケーリング機能を使用して、スケジュールまたはパフォーマンス メトリックに基づいて、Azure HDInsight クラスターを自動的にスケーリングします。
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 7a31fde34a65d69ca862a6dd8bd4fb638b15cb3a
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109751421"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight クラスターを自動的にスケール調整する

Azure HDInsight の無料自動スケーリング機能を使用すると、以前に設定した条件に基づいて、クラスター内のワーカー ノードの数を自動的に増減できます。 自動スケーリング機能は、パフォーマンス メトリックまたはスケールアップ操作やスケールダウン操作のスケジュールに基づいて、事前設定された制限内のノードの数をスケーリングすることで機能します。

## <a name="how-it-works"></a>しくみ

自動スケーリング機能では、スケーリング イベントをトリガーするために 2 種類の条件を使用します。さまざまなクラスター パフォーマンス メトリックのしきい値 ("*負荷ベースのスケーリング*" と呼ばれます) と、時間ベースのトリガー ("*スケジュール ベースのスケーリング*" と呼ばれます) です。 負荷ベースのスケーリングでは、ユーザーが設定した範囲内でクラスター内のノードの数が変更され、最適な CPU 使用率と最小のランニング コストが保証されます。 スケジュール ベースのスケーリングでは、スケールアップ操作やスケールダウン操作のスケジュールに基づいて、クラスター内のノードの数が変更されます。

次のビデオでは、自動スケーリングによって解決される課題の概要と、HDInsight を使用したコストの管理にそれがどのように役立つかについて説明します。

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>負荷ベースまたはスケジュール ベースのスケーリングを選択する

スケーリングの種類を選択する際は、次の要素を考慮してください。

* 負荷変動: クラスターの負荷は、特定の日の特定の時刻に一貫性のあるパターンに従っていますか。 そうでない場合、負荷ベースのスケジューリングが適切な選択肢になります。
* SLA 要件:自動スケーリングは、予測型ではなく反応型です。 負荷が増加し始めるタイミングと、クラスターがそのターゲット サイズに達している必要があるタイミングとの間に十分な遅延がありますか。 厳密な SLA 要件があり、負荷が、固定した既知のパターンである場合は、「スケジュール ベース」が適切な選択肢になります。

### <a name="cluster-metrics"></a>クラスターのメトリック

自動スケールはクラスターを継続的に監視し、次のメトリックを収集します。

|メトリック|説明|
|---|---|
|保留中の CPU の合計|すべての保留中のコンテナーの実行を開始するために必要なコアの総数。|
|保留中のメモリの合計|すべての保留中のコンテナーの実行を開始するために必要なメモリの合計 (MB 単位)。|
|空き CPU の合計|アクティブなワーカー ノード上のすべての未使用のコアの合計。|
|空きメモリの合計|アクティブなワーカー ノード上の未使用のメモリの合計 (MB 単位)。|
|ノードごとの使用済みメモリ|ワーカー ノード上の負荷。 10 GB のメモリが使用されているワーカー ノードは、使用済みメモリが 2 GB のワーカー ノードより多くの負荷がかかっていると見なされます。|
|ノードごとのアプリケーション マスターの数|ワーカー ノード上で実行されているアプリケーション マスター (AM) コンテナーの数。 2 つの AM コンテナーをホストしているワーカー ノードは、ホストしている AM コンテナーがないワーカー ノードより重要であると見なされます。|

上のメトリックは 60 秒ごとにチェックされます。 これらのメトリックのいずれかを使用して、クラスターのスケーリング操作を設定できます。

### <a name="load-based-scale-conditions"></a>負荷ベースのスケール条件

次の条件が検出されると、自動スケーリングによりスケーリング要求が発行されます。

|スケールアップ|スケールダウン|
|---|---|
|保留中の CPU の合計が空き CPU の合計を超えて 3 分以上が経過した。|保留中の CPU の合計が空き CPU の合計を下回って 10 分以上が経過した。|
|保留中のメモリの合計が空きメモリの合計を超えて 3 分以上が経過した。|保留中のメモリの合計が空きメモリの合計を下回って 10 分以上が経過した。|

スケールアップの場合、自動スケーリングは、必要な数のノードを追加するスケールアップ要求を発行します。 スケールアップは、現在の CPU とメモリの要件を満たすために必要な新しいワーカー ノード数に基づいて行われます。

スケールダウンの場合、自動スケーリングは、特定の数のノードを削除する要求を発行します。 スケールダウンは、ノードあたりの AM コンテナーの数に基づいて行われます。 現在の CPU とメモリの要件にもです。 また、サービスでは、現在のジョブの実行に基づいて、削除候補のノードも検出されます。 スケールダウン操作では、最初にノードの使用が停止された後、クラスターから削除されます。

### <a name="cluster-compatibility"></a>クラスターの互換性

> [!Important]
> Azure HDInsight 自動スケーリング機能は、Spark および Hadoop クラスター向けに 2019 年 11 月 7 日に一般提供され、プレビュー バージョンの機能では利用できない機能強化が追加されました。 2019 年 11 月 7 日より前に Spark クラスターを作成済みで、クラスター上で自動スケーリング機能を使用する場合、推奨されるパスは、新しいクラスターを作成し、新しいクラスター上で自動スケーリングを有効にすることです。
>
> Interactive Query (LLAP) の自動スケーリングは、2020 年 8 月 27 日に HDI 4.0 用に一般公開されました。 HBase クラスターはまだプレビュー段階です。 自動スケーリングは、Spark、Hadoop、Interactive Query、および HBase クラスター上でのみ使用できます。

次の表では、自動スケーリング機能と互換性のあるクラスターの種類とバージョンについて説明します。

| Version | Spark | Hive | Interactive Query | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 (ESP なし) | はい | はい | はい* | はい* | いいえ | いいえ | いいえ |
| HDInsight 4.0 (ESP なし) | はい | はい | はい* | はい* | いいえ | いいえ | いいえ |
| HDInsight 3.6 (ESP あり) | はい | はい | はい* | はい* | いいえ | いいえ | いいえ |
| HDInsight 4.0 (ESP あり) | はい | はい | はい* | はい* | いいえ | いいえ | いいえ |

\* HBase および Interactive Query クラスターは、負荷ベースではなく、スケジュールベースのスケーリングの場合にのみ構成できます。

## <a name="get-started"></a>はじめに

### <a name="create-a-cluster-with-load-based-autoscaling"></a>負荷ベースの自動スケーリングでクラスターを作成する

負荷ベースのスケーリングで自動スケーリング機能を有効にするには、通常のクラスターの作成プロセスの一部として、次の手順を行います。

1. **[構成と価格]** タブで、 **[自動スケーリングの有効化]** チェック ボックスをオンにします。
1. **[Autoscale type]\(自動スケーリングの種類\)** で、 **[Load-based]\(負荷ベース\)** を選択します。
1. 次のプロパティに対して目的の値を入力します。

    * **[ワーカー ノード]** の初期の **[Number of nodes]\(ノードの数\)** 。
    * ワーカー ノードの **[最小]** の数。
    * ワーカー ノードの **[最大]** の数。

    :::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png" alt-text="ワーカー ノードの負荷ベースの自動スケーリングを有効にする":::

ワーカー ノードの初期の数には、最小数から最大数までの数を指定する必要があります。 この値によって、クラスターが作成されるときのその初期サイズが定義されます。 ワーカー ノードの最小数は、3 以上に設定する必要があります クラスターを 3 つ未満のノードにスケーリングすると、ファイル レプリケーションが不十分なためにセーフ モードでスタックする場合があります。  詳細については、「[セーフ モードでスタックする](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)」を参照してください。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>スケジュール ベースの自動スケーリングでクラスターを作成する

スケジュール ベースのスケーリングで自動スケーリング機能を有効にするには、通常のクラスターの作成プロセスの一部として、次の手順を行います。

1. **[Configuration + pricing]\(構成と価格\)** タブで、 **[自動スケーリングの有効化]** チェックボックスをオンにします。
1. **[ワーカー ノード]** の **[Number of nodes]\(ノードの数\)** を入力します。これは、クラスターのスケールアップの制限を制御します。
1. **[Autoscale type]\(自動スケーリングの種類\)** で、 **[Schedule-based]\(スケジュール ベース\)** オプションを選択ます。
1. **[構成]** を選択して **[Autoscale configuration]\(自動スケーリングの構成\)** ウィンドウを開きます。
1. タイムゾーンを選択し、 **[+ Add condition]\(+ 条件の追加\)** をクリックします
1. 新しい条件を適用する曜日を選択します。
1. 条件を有効にする時刻と、クラスターのスケーリング後のノード数を編集します。
1. 必要に応じて、さらに条件を追加します。

    :::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png" alt-text="ワーカー ノードのスケジュールベースの作成を有効にする":::

ノードの数は、3 から条件追加前に入力したワーカー ノードの最大数の間にする必要があります。

### <a name="final-creation-steps"></a>最後の作成手順

**[ノード サイズ]** の下のドロップダウン リストから VM を選択して、ワーカー ノードの VM の種類を選択します。 ノードの種類ごとに VM の種類を選択すると、クラスター全体の概算のコスト範囲を表示できるようになります。 予算に合わせて VM の種類を調整します。

:::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png" alt-text="ワーカー ノードのノード サイズに対するスケジュールベースの自動スケーリングを有効にする":::

サブスクリプションには、リージョンごとに容量のクォータがあります。 ヘッド ノードのコアの総数とワーカー ノードの最大数の合計が容量のクォータを超えることはできません。 ただし、このクォータはソフト制限です。それを簡単に増やすためのサポート チケットをいつでも作成できます。

> [!Note]
> 合計のコア クォータ制限を超えた場合は、"最大ノードがこのリージョン内の使用可能なコア数を超えました。別のリージョンを選択するか、またはサポートに連絡してクォータを増やしてください" というエラー メッセージが表示されます。

Azure portal を使用した HDInsight クラスターの作成に関する詳細については、「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager テンプレートでクラスターを作成する

#### <a name="load-based-autoscaling"></a>負荷ベースの自動スケーリング

以下の JSON スニペットに示すように、プロパティ `minInstanceCount` と `maxInstanceCount` で `computeProfile` > `workernode` セクションに `autoscale` ノードを追加することで、Azure Resource Manager テンプレートで負荷ベースの自動スケーリングを使用する HDInsight クラスターを作成できます。 完全な Resource Manager テンプレートについては、「[Deploy Spark Cluster with Loadbased Autoscale Enabled](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-autoscale-loadbased)」 (クイックスタート テンプレート: 負荷ベースの自動スケーリングを有効にして Spark クラスターをデプロイする) というクイックスタート テンプレートを参照してください。

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>スケジュール ベースの自動スケーリング

`computeProfile` > `workernode` セクションに `autoscale` ノードを追加することで、Azure Resource Manager テンプレートでスケジュール ベースの自動スケーリングを使用する HDInsight クラスターを作成できます。 `autoscale` ノードには、`timezone` および変更を実行するタイミングが記述されている `schedule` を含む `recurrence` が含まれます。 完全な Resource Manager テンプレートについては、「[Deploy Spark Cluster with schedule-based Autoscale Enabled](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-autoscale-schedulebased)」 (スケジュールベースの自動スケーリングを有効にして Spark クラスターをデプロイする) を参照してください。

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>実行中のクラスターの自動スケーリングの有効化および無効化

#### <a name="using-the-azure-portal"></a>Azure ポータルの使用

実行中のクラスターで自動スケーリングを有効にするには、 **[設定]** の **[クラスター サイズ]** を選択します。 次に、 **[自動スケーリングの有効化]** を選択します。 使用する自動スケーリングの種類を選択し、負荷ベースまたはスケジュール ベースのスケーリングのオプションを入力します。 最後に、 **[保存]** を選択します。

:::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png" alt-text="ワーカー ノードの実行中のクラスターに対するスケジュールベースの自動スケーリングを有効にする":::

#### <a name="using-the-rest-api"></a>REST API の使用

REST API を使用して、実行中のクラスターで自動スケーリングを有効または無効にするには、自動スケーリング エンドポイントに POST 要求を行います。

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

要求ペイロードでは適切なパラメーターを使用します。 自動スケーリングを有効にするには、下の json ペイロードを使用できます。 自動スケーリングを無効にするには、ペイロード `{autoscale: null}` を使用します。

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

すべてのペイロード パラメーターの完全な説明については、前のセクションの[負荷ベースの自動スケーリングの有効化](#load-based-autoscaling)を参照してください。

## <a name="monitoring-autoscale-activities"></a>自動スケーリング アクティビティの監視

### <a name="cluster-status"></a>クラスターの状態

Azure portal に表示されるクラスターの状態は、自動スケーリング アクティビティの監視に役立ちます。

:::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png" alt-text="ワーカー ノードのクラスター状態に対する負荷ベースの自動スケーリングを有効にする":::

表示される可能性があるすべてのクラスター状態メッセージを、以下の一覧で説明します。

| クラスターの状態 | 説明 |
|---|---|
| 実行中 | クラスターは正常に動作しています。 以前のすべての自動スケーリング アクティビティは、正常に完了しました。 |
| 更新中  | クラスターの自動スケーリング構成は更新されています。  |
| HDInsight 構成  | クラスターのスケールアップまたはスケールダウン操作が進行中です。  |
| 更新エラー  | 自動スケーリング構成の更新中に HDInsight で問題が発生しました。 お客様は、更新の再試行または自動スケーリングの無効化を選択できます。  |
| エラー  | クラスターに何らかの問題があり、使用できません。 このクラスターを削除し、新しいクラスターを作成してください。  |

クラスター内の現在のノード数を表示するには、クラスターの **[概要]** ページにある **[クラスター サイズ]** グラフに移動します。 または、 **[設定]** で **[クラスター サイズ]** を選択します。

### <a name="operation-history"></a>操作の履歴

クラスター メトリックの一部として、クラスターのスケールアップおよびスケールダウンの履歴を表示できます。 また、過去の 1 日、1 週間、またはその他の期間中のすべてのスケーリング アクションを一覧表示することもできます。

**[監視]** で **[メトリック]** を選択します。 その後、 **[メトリックの追加]** を選択し、 **[メトリック]** ドロップダウン ボックスから **[Number of Active Workers]\(アクティブなワーカーの数\)** を選択します。 右上にあるボタンを選択して、時間の範囲を変更します。

:::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png" alt-text="ワーカー ノードのスケジュールベースの自動スケーリング メトリックを有効にする":::

## <a name="best-practices"></a>ベスト プラクティス

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>スケールアップ操作およびスケールダウン操作の待ち時間について検討する

スケーリング操作が完了するには 10 分から 20 分かかる可能性があります。 カスタマイズしたスケジュールを設定する場合は、この遅延を考慮してください。 たとえば、午前 9時にクラスター サイズを 20 にする必要がある場合は、スケーリング操作が午前 9時までに完了するように、スケジュールのトリガーを午前 8時 30分などの早い時刻に設定する必要があります。

### <a name="prepare-for-scaling-down"></a>スケール ダウンを準備する

クラスターのスケール ダウン プロセス中、自動スケーリングはターゲット サイズを満たすためにノードを使用停止にします。 これらのノードでタスクが実行中の場合、自動スケーリングは、Spark および Hadoop クラスターに対してタスクが完了するまで待機します。 各ワーカー ノードは HDFS でのロールも果たすため、一時データは残りのノードに移動されます。 残りのノードに、すべての一時データをホストするのに十分なスペースがあることを確認します。

実行中のジョブは続行されます。 保留中のジョブは、少ない数の使用可能ワーカー ノードでスケジューリングされるのを待ちます。

### <a name="configure-schedule-based-autoscale-based-on-usage-pattern"></a>使用パターンに基づいてスケジュールベースの自動スケーリングを構成する

スケジュールベースの自動スケーリングを構成する場合、クラスターの使用パターンを把握しておく必要があります。 [Grafana ダッシュボード](./interactive-query/hdinsight-grafana.md)は、クエリの負荷と実行スロットを理解するのに役立ちます。 このダッシュボードから、利用可能な実行プログラム スロットと実行プログラム スロットの合計数を取得できます。

ここでは、必要となるワーカー ノードの数を見積もる方法を示します。 変動するワークロードを処理するために、10% のバッファーを追加することをお勧めします。

実際に使用される実行プログラム スロットの数 = 実行プログラム スロットの合計数 - 使用可能な実行プログラム スロットの合計数。

必要なワーカー ノードの数 = 実際に使用される実行プログラム スロットの数 / (hive.llap.daemon.num.executors + hive.llap.daemon.task.scheduler.wait.queue.size)

*hive.llap.daemon.num.executors は構成可能であり、既定値は 4 です。

*hive.llap.daemon.task.scheduler.wait.queue.size は構成可能であり、既定値は 10 です。


### <a name="be-aware-of-the-minimum-cluster-size"></a>クラスターの最小サイズに注意する

クラスターを 3 つ未満のノードにスケールダウンしないでください。 クラスターを 3 つ未満のノードにスケーリングすると、ファイル レプリケーションが不十分なためにセーフ モードでスタックする場合があります。 詳細については、「[セーフ モードでスタックする](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)」を参照してください。

### <a name="increase-the-number-of-mappers-and-reducers"></a>マッパーとレジューサーの数を増やす

Hadoop クラスターの自動スケーリングでは、HDFS の使用状況も監視します。 HDFS がビジーの場合は、クラスターがまだ現在のリソースを必要としていることを前提とします。 クエリに大量のデータが含まれている場合は、マッパーとレジューサーの数を増やして、並列処理を増やし、HDFS 操作を高速化することができます。 このように、追加リソースがある場合に適切なスケールダウンがトリガーされます。

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>ピーク時の使用状況シナリオに対する Hive 構成の同時実行クエリの最大合計数を設定する

Ambari では、Hive 構成の *同時実行クエリの最大合計数* は、自動スケーリング イベントによって変更されません。 つまり Hive Server 2 Interactive Service は、Interactive Query デーモンの数が負荷やスケジュールに基づいてスケールアップまたはスケールダウンされた場合でも、任意の時点で指定された数の同時実行クエリのみを処理できます。 一般的には、手動による介入を避けられるように、ピーク時の使用状況シナリオに合わせてこの構成を設定することをお勧めします。

ただし、ワーカー ノードが少ししかなく、同時実行クエリの最大合計数の値が非常に高く構成されている場合に、Hive サーバー 2 の再起動エラーが発生することがあります。 少なくとも、指定された数の Tez AM に対応できるワーカー ノードの最小数が必要です (同時実行クエリの最大合計数の構成と同じ)。

## <a name="limitations"></a>制限事項


### <a name="interactive-query-daemons-count"></a>Interactive Query デーモン数

自動スケーリングが有効な Interactive Query クラスターでは、自動スケーリングの上下イベントによって、アクティブなワーカー ノードの数に合わせて Interactive Query デーモンの数もスケールアップまたはスケールダウンされます。 デーモンの数の変化は、Ambari の `num_llap_nodes` 構成には保持されません。 Hive サービスが手動で再起動された場合は、Ambari の構成に従って Interactive Query デーモンの数がリセットされます。

Interactive Query サービスが手動で再起動された場合、*Advanced hive-interactive-env* の下の `num_llap_node` 構成 (Hive Interactive Query デーモンを実行するために必要なノードの数) を手動で変更し、現在アクティブなワーカー ノードの数と一致させる必要があります。

## <a name="next-steps"></a>次のステップ

クラスターを手動でスケーリングするためのガイドラインについては、[スケーリングのガイドライン](hdinsight-scaling-best-practices.md)に関するページをお読みください。
