---
title: マッピング データ フロー内の列パターン
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics のマッピング データ フローにおける列パターンを使って一般化されたデータ変換パターンを作成します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 7b8343c06dd0815f8c0fb44fa00f85c2c0195b13
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058527"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>マッピング データ フロー内の列パターンを使用する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

複数のマッピング データ フローの変換によって、ハードコーディングされた列名ではなく、パターンに基づいてテンプレート列を参照できるようになります。 このマッチングは、"*列パターン*" と呼ばれます。 正確なフィールド名を必要とするのではなく、パターンを定義して名前、データ、型、ストリーム、発生元、位置に基づいて列を照合できます。 列パターンが役立つシナリオが 2 つあります。

* 受信ソース フィールドが頻繁に変更される場合。テキスト ファイルや NoSQL データベースの列が変更されるケースなどです。 このシナリオは、[スキーマの誤差](concepts-data-flow-schema-drift.md)と呼ばれます。
* 大規模な列グループに対して共通の操作を実行したい場合。 列名に ' total ' が含まれているすべての列を double 型にキャストするなどです。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>派生列および集計の列パターン

派生列、集計、またはウィンドウ変換における列パターンを追加するには、列リストの上にある **[追加]** か、既存の派生列の横にあるプラス記号をクリックします。 **[列パターンの追加]** を選択します。

:::image type="content" source="media/data-flow/add-column-pattern.png" alt-text="スクリーンショットには、列パターンを追加するプラス アイコンが示されています。":::

[式ビルダー](concepts-data-flow-expression-builder.md)を使用して、一致条件を入力します。 列の `name`、`type`、`stream`、`origin`、`position` を基に列と照合するブール式を作成します。 パターンは、条件から true が返される任意の列 (誤差または定義) に影響を及ぼします。


:::image type="content" source="media/data-flow/edit-column-pattern.png" alt-text="スクリーンショットには、[Derived column's settings]\(派生列の設定\) タブが示されています。":::

上の列パターンでは、double 型のすべての列と照合して、一致ごとに 1 つの派生列を作成します。 列名フィールドとして `$$` を記すことによって、一致した各列が同じ名前で更新されます。 各列の値は、小数点以下第 3 位を四捨五入した既存の値です。

一致条件が正しいことを確認するには、 **[検査]** タブ内にある定義列の出力スキーマを検証するか、または **[データのプレビュー]** タブ内にあるデータのスナップショットを取得できます。 

:::image type="content" source="media/data-flow/columnpattern3.png" alt-text="スクリーンショットには、[出力スキーマ] タブが示されています。":::

### <a name="hierarchical-pattern-matching"></a>階層型のパターン マッチング

複雑な階層構造内でも、パターン マッチングを作成できます。 セクション `Each MoviesStruct that matches` を展開すると、データ ストリーム内の各階層の内容が表示されます。 その後、選択した階層内のプロパティに関するマッチング パターンを作成できます。

:::image type="content" source="media/data-flow/patterns-hierarchy.png" alt-text="スクリーンショットは、階層型の列パターンを示しています。":::

## <a name="rule-based-mapping-in-select-and-sink"></a>選択とシンクにおけるルールベースのマッピング

ソース変換および選択変換の中で列をマッピングする場合、固定マッピングまたはルールベースのマッピングのどちらかを追加できます。 列の `name`、`type`、`stream`、`origin`、`position` に基づいて照合されます。 フィールドとルールベースの両方のマッピングを任意に組み合わせることもできます。 既定では、50 列を超えるすべてのプロジェクションは、既定ではルールベースのマッピングになり、すべての列に対して照合され、入力された名前が出力されます。 

ルールベースのマッピングを追加するには、 **[マッピングの追加]** をクリックし、 **[ルールベースのマッピング]** を選択します。

:::image type="content" source="media/data-flow/rule2.png" alt-text="スクリーンショットには、[マッピングの追加] から選択されたルールベースのマッピングが示されています。":::

各ルールベースのマッピングには、2 つの入力が必要となります。照合の基準となる条件、およびマップされた各列に付ける名前です。 どちらの値も[式ビルダー](concepts-data-flow-expression-builder.md)を使用して入力ます。 左側の式ボックスに、ブール値の一致条件を入力します。 右側の式ボックスに、一致した列のマップ先を指定します。

:::image type="content" source="media/data-flow/rule-based-mapping.png" alt-text="スクリーンショットには、マッピングが示されています。":::

一致した列の入力名を参照するには、`$$` 構文を使用します。 上の図を例として使用します。ユーザーは、名前が 6 文字より短いすべての文字列型の列に対して照合したいとします。 ある入力列に `test` という名前が付けられていた場合、式 `$$ + '_short'` によりその列の名前が `test_short` に変更されます。 これが唯一のマッピングの場合、条件を満たしていないすべての列が、出力されるデータから削除されます。

パターンにより、誤差の列と定義された列の両方が照合されます。 どの定義された列がルールによってマップされたかを確認するには、ルールの横にある眼鏡アイコンをクリックします。 データ プレビューを使用して出力を確認します。

### <a name="regex-mapping"></a>正規表現マッピング

下向きのシェブロン アイコンをクリックすると、正規表現のマッピング条件を指定できます。 正規表現のマッピング条件により、指定された正規表現の条件に一致するすべての列名が照合されます。 これは、標準のルールベースのマッピングと組み合わせて使用できます。

:::image type="content" source="media/data-flow/regex-matching.png" alt-text="スクリーンショットには、regex マッピング条件が、階層レベルと名前の照合とともに示されています。":::

上記の例では、正規表現パターン `(r)`、つまり小文字の r を含むすべての列名と照合されます。 標準のルールベースのマッピングと同様に、一致したすべての列は、`$$` 構文を使用した右側の条件によって変更されます。

### <a name="rule-based-hierarchies"></a>ルールベースの階層

定義されたプロジェクションに階層がある場合は、ルールベースのマッピングを使用して、階層のサブ列をマップできます。 一致条件と、マップ対象のサブ列が含まれる複合列を指定します。 一致したすべてのサブ列は、右側に指定された [Name as]\(付ける名前\) ルールを使用して出力されます。

:::image type="content" source="media/data-flow/rule-based-hierarchy.png" alt-text="スクリーンショットには、階層を使用したルールベースのマッピングが示されています。":::

上記の例では、複合列 `a` のすべてのサブ列に対して照合されます。 `a` には 2 つのサブ列 `b` と `c` が含まれています。 [Name as]\(付ける名前\) 条件が `$$` であるため、出力スキーマには 2 つの列 `b` と `c` が含まれす。

## <a name="pattern-matching-expression-values"></a>パターン マッチングの式の値

* `$$` は、実行時に各一致の名前または値に変換されます。 `$$` は `this` と同等と見なされます
* `$0` は、スカラー型の実行時に現在の列名と一致するものに変換されます。 階層型の場合、`$0` は現在一致している列階層パスを表します。
* `name` は、受信した各列の名前を表します
* `type` は、受信した各列のデータ型を表します。 データ フロー型システムのデータ型の一覧については、[こちら](concepts-data-flow-overview.md#data-flow-data-types)を参照してください。
* `stream` は、フロー内の各ストリームまたは変換に関連付けられた名前を表します
* `position` は、データ フロー内の列の序数位置です
* `origin` は、列の発生元であるか、最後に更新された場所となる変換です。

## <a name="next-steps"></a>次のステップ
* データ変換におけるマッピング データ フローの[式言語](data-flow-expression-functions.md)に関する詳細を確認する
* [シンク変換](data-flow-sink.md)と[選択変換](data-flow-select.md)の中でルールベースのマッピングと共に列パターンを使用する
