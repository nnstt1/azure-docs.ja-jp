---
title: マッピング データ フローのフラット化変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Synapse Analytics パイプラインのフラット化変換を使用して階層データを非正規化します。
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 4fb8d5ea1bfaa9534f7db27296d3cb7f61d0c4fb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274957"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>マッピング データ フローのフラット化変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

フラット化変換を使用して、JSON などの階層構造内の配列値を取得し、それらを個々の行にアンロールします。 このプロセスは非正規化と呼ばれるものです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWLX9j]

## <a name="configuration"></a>構成

フラット化変換には、次の構成設定が含まれます

:::image type="content" source="media/data-flow/flatten1.png" alt-text="フラット化の設定":::

### <a name="unroll-by"></a>アンロール

アンロールする配列を選択します。 出力データでは、配列の各項目が 1 つの行になります。 入力行のアンロール配列が null または空の場合は、アンロールされた値が null の出力行が 1 つ存在します。

### <a name="unroll-root"></a>ルートのアンロール

既定では、フラット化変換によって、配列はそれが存在する階層の最上位にアンロールされます。 必要に応じて、アンロール ルートとして配列を選択できます。 アンロール ルートは、アンロール配列である、またはアンロール配列を含む、複合オブジェクトの配列である必要があります。 アンロール ルートを選択した場合、出力データには、アンロール ルート内の項目ごとに少なくとも 1 行が含まれます。 入力行の項目がアンロール ルートにない場合、その入力行は出力データから削除されます。 アンロール ルートを選択したときの出力の行数は、常に、既定の動作での行数以下になります。

### <a name="flatten-mapping"></a>マッピングのフラット化

選択変換と同様に、入力フィールドと正規化されていない配列から新しい構造のプロジェクションを選択します。 正規化されていない配列がマップされている場合、出力列は配列と同じデータ型になります。 アンロール配列が、サブ配列を含む複合オブジェクトの配列である場合、そのサブ配列の項目をマッピングすると、配列が出力されます。

マッピングの出力を確認するには、検査タブとデータのプレビューを参照してください。

## <a name="rule-based-mapping"></a>ルールベースのマッピング

フラット化変換では、ルールベースのマッピングがサポートされており、ルールに基づいて配列をフラット化し、階層レベルに基づいて構造体をフラット化する、動的で柔軟な変換を作成できます。

:::image type="content" source="media/data-flow/flatten-pattern.png" alt-text="パターンのフラット化":::

### <a name="matching-condition"></a>Matching condition (一致条件)

完全一致またはパターンを使用してフラット化する列に対するパターン マッチング条件を入力します。 例: ```like(name,'cust%')```

### <a name="deep-column-traversal"></a>Deep column traversal (ディープ列トラバーサル)

複合オブジェクト全体を列として処理するのではなく、複合オブジェクトのすべてのサブ列を個別に処理するようサービスに指示するオプションの設定。

### <a name="hierarchy-level"></a>階層レベル

展開する階層のレベルを選択します。

### <a name="name-matches-regex"></a>Name matches (名前一致) (正規表現)

必要に応じて、上の一致条件を使用するのではなく、正規表現として名前の一致を表すには、このボックスを使用します。

## <a name="examples"></a>例

フラット化変換の以下の例については、次の JSON オブジェクトを参照してください

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>文字列配列を含むアンロール ルートがない

| アンロール | ルートのアンロール | Projection |
| --------- | ----------- | ---------- |
| goods.customers | なし | name <br> customer = goods.customer |

#### <a name="output"></a>出力

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>複合配列を含むアンロール ルートがない

| アンロール | ルートのアンロール | Projection |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | なし | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>出力

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>アンロール配列と同じルート

| アンロール | ルートのアンロール | Projection |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>出力

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>複合配列を含むアンロール ルート

| アンロール | ルートのアンロール | Projection |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>出力

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>例

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>次のステップ

* [ピボット解除変換](data-flow-pivot.md)を使用して、行を列にピボットします。
* [ピボット解除変換](data-flow-unpivot.md)を使用して、列を行にピボットします。
