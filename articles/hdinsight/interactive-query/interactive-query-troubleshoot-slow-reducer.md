---
title: Azure HDInsight でレジューサーが遅い
description: レジューサーは Azure HDInsight のデータ スキューが原因で遅くなる可能性があります
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 951dd8b4bc9ee68c88e6bae481482e041ae89054
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290595"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>シナリオ: Azure HDInsight でレジューサーが遅い

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

`insert into table1 partition(a,b) select a,b,c from table2` などのクエリを実行すると、クエリ プランによって一連のレジューサーが開始されますが、各パーティションのデータは 1 つのレジューサーに送信されます。 その結果、最も大きなパーティションのレジューサーの場合と同じくらいにクエリが遅くなります。

## <a name="cause"></a>原因

[beeline](../hadoop/apache-hadoop-use-hive-beeline.md) を開き、set `hive.optimize.sort.dynamic.partition` の値を確認します。

この変数の値は、データの性質に基づいて true または false に設定されます。

入力テーブル内のパーティション数が少なく (たとえば 10 未満)、出力パーティション数も同様であり、変数が `true` に設定されている場合は、パーティションごとに 1 つのレジューサーを使用してデータの並べ替えと書き込みがグローバルに行われます。 使用できるレジューサー数が多い場合でも、データ スキューによっていくつかのレジューサーに遅延が発生し、並列処理の最大数が達成されない可能性があります。 `false` に変更すると、複数のレジューサーで 1 つのパーティションを処理できるようになり、複数の小さいファイルが書き出されるので、挿入が高速になります。 この場合、小さなファイルが存在するため、クエリへの影響がさらに大きくなる可能性があります。

`true` の値は、パーティション数が多く、データ スキューが発生していない場合に意味があります。 このような場合、1 つのレジューサーで各パーティションが処理され、後続のクエリのパフォーマンスが向上するように、マップ フェーズの結果が書き出されます。

## <a name="resolution"></a>解決方法

1. 複数のパーティションに正規化するようにデータのパーティションを再分割してみます。

1. 1 の手順を実行できない場合は、beeline セッションで config の値を false に設定してから、クエリを再試行します。 `set hive.optimize.sort.dynamic.partition=false`. クラスター レベルで値を false に設定することは推奨されません。 `true` の値が最適なので、データとクエリの性質に基づいて必要に応じてこのパラメーターを設定します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]