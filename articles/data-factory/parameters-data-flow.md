---
title: マッピング データ フローをパラメーター化する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory パイプラインおよび Azure Synapse Analytics パイプラインからのマッピング データ フローをパラメーター化する方法を説明します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d01e1ca8d3eb0ca2e345d42118ad5f1463c3139f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750458"
---
# <a name="parameterizing-mapping-data-flows"></a>マッピング データ フローをパラメーター化する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Azure Data Factory パイプラインおよび Azure Synapse Analytics パイプラインのマッピング データ フローでは、パラメーターの使用がサポートされます。 データ フロー定義内でパラメーターを定義し、式全体で使用します。 パラメーター値を設定するには、データ フローの実行アクティブティ経由でパイプラインを呼び出します。 データ フロー アクティビティの式で値を設定するには、次の 3 つのオプションがあります。

* パイプラインの制御フローの式を使用して動的な値を設定する
* データ フロー式の言語を使用して動的な値を設定する
* いずれかの式の言語を使用して静的なリテラル値を設定する

この機能を使用して、ご利用のデータ フローを汎用的かつ柔軟性があり、再利用可能なものにします。 3 つのパラメーターを使って、データ フローの設定と式をパラメーター化することができます。

## <a name="create-parameters-in-a-mapping-data-flow"></a>マッピング データ フローでのパラメーターの作成

ご利用のデータ フローにパラメーターを追加するには、データ フローのキャンバスの空白部分をクリックして、全般プロパティを表示します。 [設定] ウィンドウに、 **[パラメーター]** という名前のタブが表示されます。 新しいパラメーターを生成するには **[新規]** を選択します。 パラメーターごとに、名前を割り当て、型を選択し、必要に応じて既定値を設定する必要があります。

:::image type="content" source="media/data-flow/create-params.png" alt-text="データ フローのパラメーターを作成する":::

## <a name="use-parameters-in-a-mapping-data-flow"></a>マッピング データ フローでのパラメーターの使用 

パラメーターは任意のデータ フロー式で参照できます。 パラメーターは $ で始まり、変更することはできません。 式ビルダー内で利用可能なパラメーターの一覧が **[パラメーター]** タブに表示されます。

:::image type="content" source="media/data-flow/parameter-expression.png" alt-text="スクリーンショットは、[パラメーター] タブの使用可能なパラメータを示しています。":::

**[新しいパラメーター]** を選択して [名前] と [種類] を指定することで、パラメーターを簡単に追加できます。

:::image type="content" source="media/data-flow/new-parameter-expression.png" alt-text="スクリーンショットは、新しいパラメーターが追加された [パラメーター] タブのパラメーターを示しています。":::

## <a name="assign-parameter-values-from-a-pipeline"></a>パイプラインからパラメーター値を割り当てる

パラメーターを使ってデータ フローを作成したら、データ フローの実行アクティブティを使用してパイプラインからそれを実行できます。 パイプライン キャンバスにアクティビティを追加すると、アクティビティの **[パラメーター]** タブに使用可能なデータ フローのパラメーターが表示されます。

パラメーター値を割り当てる場合、spark の種類に基づいて、[パイプライン式言語](control-flow-expression-language-functions.md)または[データ フロー式言語](data-flow-expression-functions.md)のいずれかを使用できます。 各マッピング データ フローには、パイプラインとデータ フロー式のパラメーターの任意の組み合わせを含めることができます。

:::image type="content" source="media/data-flow/parameter-assign.png" alt-text="スクリーンショットは、myparam の値に [Data flow expression]\(データ フロー式\) が選択された [パラメーター] タブを示しています。":::

### <a name="pipeline-expression-parameters"></a>パイプライン式パラメーター

パイプライン式パラメーターを使用すると、システム変数、関数、パイプライン パラメーター、および他のパイプライン アクティビティと同様の変数を参照できます。 **[Pipeline expression]\(パイプライン式\)** をクリックすると、サイドナビゲーションが開き、式ビルダーを使用して式を入力できます。

:::image type="content" source="media/data-flow/parameter-pipeline.png" alt-text="スクリーンショットは、式ビルダーのペインを示しています。":::

参照すると、パイプライン パラメーターが評価され、その値がデータ フロー式言語で使用されます。 パイプライン式の型は、データ フロー パラメーターの型と一致する必要はありません。 

#### <a name="string-literals-vs-expressions"></a>文字列リテラルと式

文字列型のパイプライン式パラメーターを割り当てると、既定で引用符が追加され、値はリテラルとして評価されます。 パラメーター値をデータ フロー式として読み取るには、パラメーターの横にある [式] ボックスをオンにします。

:::image type="content" source="media/data-flow/string-parameter.png" alt-text="スクリーンショットは、パラメーターに対して [式] が選択された [Data flow parameters]\(データ フロー パラメーター\) ペインを示しています。":::

データ フロー パラメーター `stringParam` から値 `upper(column1)` のパイプライン パラメーターを参照する場合。 

- [式] がオンの場合、`$stringParam` は column1 の値に評価され、すべて大文字になります。
- [式] がオフの場合 (既定の動作)、`$stringParam` は `'upper(column1)'` に評価されます

#### <a name="passing-in-timestamps"></a>タイムスタンプを渡す

パイプライン式言語では、`pipeline().TriggerTime` などのシステム変数や `utcNow()` などの関数からは、タイムスタンプが 'yyyy-MM-dd\'T\'HH:mm:ss.SSSSSSZ' という形式の文字列として返されます。 これらをタイムスタンプ型のデータ フロー パラメーターに変換するには、文字列補間を使用して、`toTimestamp()` 関数に目的のタイムスタンプを含めます。 たとえば、パイプラインのトリガー時間をデータ フロー パラメーターに変換するには、`toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` を使用できます。 

:::image type="content" source="media/data-flow/parameter-timestamp.png" alt-text="スクリーンショットは、トリガー時間を入力できる [パラメーター] タブを示しています。":::

> [!NOTE]
> データ フローは、最大 3 ミリ秒の数字のみをサポートしています。 `left()` 関数は、追加の数字を切り捨てるために使用されます。

#### <a name="pipeline-parameter-example"></a>パイプライン パラメーターの例

型が String、`@pipeline.parameters.pipelineParam` のパイプライン パラメーターを参照する整数パラメーター `intParam` があるとします。 

:::image type="content" source="media/data-flow/parameter-pipeline-2.png" alt-text="スクリーンショットは、stringParam と intParam という名前のパラメーターが表示された [パラメーター] タブを示しています。":::

`@pipeline.parameters.pipelineParam` には実行時に `abs(1)` の値が割り当てられます。

:::image type="content" source="media/data-flow/parameter-pipeline-4.png" alt-text="スクリーンショットは、a b s (1) の値が選択された [パラメーター] タブを示しています。":::

派生列などの式で `$intParam` が参照されると、`abs(1)` が評価されて `1` が返されます。 

:::image type="content" source="media/data-flow/parameter-pipeline-3.png" alt-text="スクリーンショットは、列の値を示しています。":::

### <a name="data-flow-expression-parameters"></a>データ フロー式のパラメーター

**[Data flow expression]\(データ フロー式\)** を選択すると、データ フロー式ビルダーが開きます。 データ フロー全体で、関数、その他のパラメーター、および定義されたスキーマ列を参照できます。 この式は、参照時にそのまま評価されます。

> [!NOTE]
> 無効な式を渡すか、その変換に存在しないスキーマ列を参照すると、パラメーターは null と評価されます。


### <a name="passing-in-a-column-name-as-a-parameter"></a>パラメーターとして列名を渡す

一般的なパターンは、列名をパラメーター値として渡すことです。 列がデータ フロー スキーマで定義されている場合は、文字列式として直接参照できます。 列がスキーマで定義されていない場合は、`byName()` 関数を使用します。 `toString()` などのキャスト関数を使用して、列を適切な型にキャストすることを忘れないでください。

たとえば、パラメーター `columnName` に基づいて文字列型の列をマップする場合は、`toString(byName($columnName))` に等しい派生列変換を追加できます。

:::image type="content" source="media/data-flow/parameterize-column-name.png" alt-text="パラメーターとして列名を渡す":::

## <a name="next-steps"></a>次のステップ
* [データ フローの実行アクティビティ](control-flow-execute-data-flow-activity.md)
* [制御フローの式](control-flow-expression-language-functions.md)
