---
title: Azure Monitor ブックのテキスト パラメーター
description: 作成済みのブックやパラメーター化されたカスタム ブックを使用して複雑なレポート作成を簡素化します。 ブックのテキスト パラメーターの詳細について学習します。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: e3beedff4b9d65f5f0b69b5c60bd67d4b134d096
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287574"
---
# <a name="workbook-text-parameters"></a>ブックのテキスト パラメーター

テキスト ボックス パラメーターを使用すると、ブック ユーザーからテキスト入力を簡単に収集できます。 これらは、ドロップダウンを使用して入力を収集するのが実用的でない場合に使用されます (たとえば、任意のしきい値または汎用フィルター)。 ブックでは、作成者は、クエリからテキスト ボックスの既定値を取得できます。 これにより、メトリックの p95 に基づいて既定のしきい値を設定するなどの興味深いシナリオが可能になります。

テキスト ボックスは、一般的に、他のブック コントロールによって使用される内部変数として使用します。 これは、既定値を求めるクエリを使用し、入力コントロールを読み取りモードで非表示にすることによって実現されます。 たとえば、しきい値を (ユーザーが入力するのではなく) 数式から取得した後、後続のクエリでそのしきい値を使用することができます。

## <a name="creating-a-text-parameter"></a>テキスト パラメーターの作成
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンを選択します。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `SlowRequestThreshold` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Text`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [データの取得元]\: [クエリ]`None`
5. ツール バーの [保存] を選択して、パラメーターを作成します。

    :::image type="content" source="./media/workbooks-text/text-create.png" alt-text="テキスト パラメーターの作成を示すスクリーンショット。":::

ブックが読み取りモードでどのように表示されるかを次に示します。

:::image type="content" source="./media/workbooks-text/text-readmode.png" alt-text="読み取りモードでのテキスト パラメーターを示すスクリーンショット。" border="false":::

## <a name="parameter-field-style"></a>パラメーター フィールドのスタイル
テキスト パラメーターでは、次のフィールド スタイルがサポートされています。

- 標準: 1 行のテキスト フィールド。

     :::image type="content" source="./media/workbooks-text/standard-text.png" alt-text="標準テキスト フィールドを示すスクリーンショット。":::

- パスワード: 1 行のパスワード フィールド。 ユーザーが入力するとき、パスワード値は UI 上でのみ非表示になります。 この値は、参照時にパラメーター値として引き続き完全にアクセス可能であり、ブックの保存時には暗号化されていない状態で格納されます。

     :::image type="content" source="./media/workbooks-text/password-text.png" alt-text="パスワード フィールドを示すスクリーンショット。":::

- 複数行: 次の言語でリッチな IntelliSense と構文の色付けをサポートする複数行テキスト フィールド。
    - テキスト
    - Markdown
    - JSON
    - SQL
    - TypeScript
    - KQL
    - TOML

    ユーザーは、複数行エディターの高さを指定することもできます。

     :::image type="content" source="./media/workbooks-text/kql-text.png" alt-text="複数行テキスト フィールドを示すスクリーンショット。":::

## <a name="referencing-a-text-parameter"></a>テキスト パラメーターの参照
1. 青い [クエリの追加] リンクを選択してブックにクエリ コントロールを追加し、Application Insights リソースを選択します。
2. KQL ボックスに、このスニペットを追加します。
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 値 500 を指定したテキスト パラメーターをクエリ コントロールと組み合わせて使用することにより、次のクエリを効果的に実行できます。
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. クエリを実行して結果を確認します。

    :::image type="content" source="./media/workbooks-text/text-reference.png" alt-text="KQL で参照されるテキスト パラメーターを示すスクリーンショット。":::

> [!NOTE]
> 上の例の `{SlowRequestThreshold}` は整数値を表します。 文字列 (`{ComputerName}` など) を照会する場合、パラメーター フィールドに引用符なしで入力値を指定できるようにするには、Kusto クエリに変更を加え、`"{ComputerName}"` のように引用符を追加する必要があります。

## <a name="setting-default-values-using-queries"></a>クエリを使用した既定値の設定
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンを選択します。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `SlowRequestThreshold` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Text`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [データの取得元]\: [クエリ]`Query`
5. KQL ボックスに、このスニペットを追加します。
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    このクエリでは、テキスト ボックスの既定値を、アプリ内のすべての要求の 95 パーセンタイル期間に設定します。
6. クエリを実行し、結果を確認します
7. ツール バーの [保存] を選択して、パラメーターを作成します。

    :::image type="content" source="./media/workbooks-text/text-default-value.png" alt-text="KQL からの既定値を含むテキスト パラメーターを示すスクリーンショット。":::

> [!NOTE]
> この例では Application Insights データを照会していますが、このアプローチは、任意のログ ベースのデータ ソース (Log Analytics、Azure Resource Graph など) に使用できます。

## <a name="adding-validations"></a>検証の追加 

標準とパスワードのテキスト パラメーターの場合、ユーザーはテキスト フィールドに適用される検証ルールを追加できます。 有効な正規表現をエラー メッセージと一緒に追加します。 メッセージを設定した場合は、フィールドが無効な場合にエラーとして表示されます。

一致が選択されている場合は、値が正規表現と一致する場合にフィールドは有効です。一致が選択されていない場合は、正規表現と一致しない場合にフィールドは有効です。

:::image type="content" source="./media/workbooks-text/validation-settings.png" alt-text="テキスト検証設定のスクリーンショット。":::

## <a name="format-json-data"></a>JSON データの書式設定 

複数行テキスト フィールドの言語として JSON が選択されている場合、フィールドには、フィールドの JSON データを書式設定するボタンが表示されます。 また、ユーザーはショートカット `(ctrl + \)` を使用して JSON データを書式設定することもできます。

データがクエリから取得される場合、ユーザーは、クエリによって返される JSON データを事前に書式設定するオプションを選択できます。

:::image type="content" source="./media/workbooks-text/pre-format-json-data.png" alt-text="JSON データを事前に書式設定するスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](./workbooks-overview.md#visualizations)します。
* ブック リソースへのアクセスを[制御](./workbooks-access-control.md)し、共有します。