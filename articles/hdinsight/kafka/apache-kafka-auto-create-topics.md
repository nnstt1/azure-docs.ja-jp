---
title: Apache Kafka での自動トピック作成を有効にする - Azure HDInsight
description: トピックを自動的に作成するように HDInsight 上の Apache Kafka を構成する方法を説明します。 Ambari を使用して `auto.create.topics.enable` を true に設定することにより、Kafka を構成できます。 または、クラスターの作成中に PowerShell または Resource Manager テンプレートを使用します。
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 946655910f17bb3a12237490f0570cbcff2e76b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620504"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>トピックを自動的に作成するように HDInsight 上の Apache Kafka を構成する方法

既定では、HDInsight 上の Apache Kafka では、自動的にトピックは作成されません。 Apache Ambari を使って、既存のクラスターでの自動トピック作成を有効にすることができます。 Azure Resource Manager テンプレートを使って新しい Kafka クラスターを作成するときに、自動トピック作成を有効にすることもできます。

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Ambari Web UI を使って既存のクラスターでトピックの自動作成を有効にするには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) 上で、ご自身の Kafka クラスターを選択します。

1. **クラスター ダッシュボード** 上で **[Ambari ホーム]** を選択します。

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="クラスター ダッシュボードが選ばれたポータルの画像" border="true":::

    認証を求められたら、クラスターのログイン (管理者) 資格情報を使用して認証を行います。 代わりに、`https://CLUSTERNAME.azurehdinsight.net/` から直接 Amabri に接続することもできます。その際、`CLUSTERNAME` はご自身の Kafka クラスターの名前になります。

1. ページの左側にある一覧で Kafka サービスを選びます。

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Apache Ambari のサービス一覧タブ" border="true":::

1. ページ中央の [Configs]\(構成\) を選択します。

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Apache Ambari のサービス構成タブ" border="true":::

1. [フィルター] フィールドに値「`auto.create`」を入力します。

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Apache Ambari の検索フィルター フィールド" border="true":::

    プロパティの一覧にこの設定フィルターが適用されて、`auto.create.topics.enable` 設定が表示されます。

1. `auto.create.topics.enable` の値を `true` に変更して、**[保存]** を選択します。 ノートを追加して、もう一度 **[保存]** を選択します。

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="auto.create.topics.enable エントリの画像" border="true":::

1. Kafka サービスを選択し、 __[Restart]\(再起動\)__ を選択して、 __[Restart all affected]\(影響を受けるものをすべて再起動\)__ を選択します。 メッセージが表示されたら、 __[Confirm restart all]\(すべて再起動\)__ を選択します。

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="Apache Ambari の影響を受けるものをすべて再起動" border="true":::

> [!NOTE]  
> Ambari REST API を使って Ambari の値を設定することもできます。 複数の REST を呼び出して現在の構成の取得や変更などを行う必要があるため、これは一般的により困難になります。詳細については、「[Apache Ambari REST API を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari-rest-api.md)」を参照してください。

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

Azure Resource Manager テンプレートを使って Kafka クラスターを作成するときに、`kafka-broker` に追加することによって、`auto.create.topics.enable` を直接設定できます。 次の JSON スニペットでは、この値を `true` に設定する方法を示します。

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
        "gateway": {
            "restAuthCredential.isEnabled": true,
            "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
            "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
        },
        "kafka-broker": {
            "auto.create.topics.enable": "true"
        }
    }
}
```

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight 上の Apache Kafka でトピックの自動作成を有効にする方法を説明しました。 Kafka の使用について詳しくは、次のリンクをご覧ください。

* [Apache Kafka ログを分析する](apache-kafka-log-analytics-operations-management.md)
* [Apache Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
