---
title: マッピング データ フローの存在変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Synapse Analytics マッピング データ フローでの存在変換を使用して、既存の行を確認します
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 747a873e64bd143d988173f236f27d7246f6deeb
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058729"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>マッピング データ フローの存在変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

存在変換は、ご利用のデータが別のソースまたはストリームに存在するかどうかを確認する行のフィルター変換です。 出力ストリームには、左側ストリームに存在するすべての行が含まれます。これらの行には、右側ストリームに存在する行も存在しない行もあります。 存在変換は ```SQL WHERE EXISTS``` および ```SQL WHERE NOT EXISTS``` と同様です。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>構成

1. **[右側ストリーム]** ドロップダウンで、存在しているかどうかを確認するデータ ストリームを選択します。
1. **[Exist type]\(存在の種類\)** 設定では存在するデータまたは存在しないデータを探すかどうかを指定します。
1. **カスタム式** を使用するかどうかを選択します。
1. 存在条件として比較するキー列を選択します。 既定では、データフローは、1 つの列について各ストリームでの同等性を検索します。 計算値で比較するには、列のドロップダウン上にマウス ポインターを移動し、 **[計算列]** を選択します。

:::image type="content" source="media/data-flow/exists.png" alt-text="[存在] 設定":::

### <a name="multiple-exists-conditions"></a>複数存在条件

各ストリームからの複数の列を比較するには、既存の行の横にある正符号アイコンをクリックして、新しい存在条件を追加します。 それぞれの追加の条件は、"and" ステートメントによって結合されます。 2 つの列を比較することは、次の式と同じです。

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>カスタム式

"and" および "equals to" 以外の演算子を含む自由形式の式を作成するには、**[カスタム式]** フィールドを選択します。 青いボックスをクリックすると、データ フロー式ビルダーを介してカスタム式を入力できます。

:::image type="content" source="media/data-flow/exists1.png" alt-text="[存在] のカスタム設定":::

## <a name="broadcast-optimization"></a>ブロードキャストの最適化

:::image type="content" source="media/data-flow/broadcast.png" alt-text="ブロードキャスト結合":::

結合変換、参照変換、および存在変換では、一方または両方のデータ ストリームがワーカー ノードのメモリに収まる場合、**ブロードキャスト** を有効にすることでパフォーマンスを最適化できます。 既定では、ある一方をブロードキャストするかどうかは、Spark エンジンによって自動的に決定されます。 ブロードキャストする側を手動で選択するには **[Fixed]\(固定\)** を選択します。

**[オフ]** オプションを使用してブロードキャストを無効にすることは、結合でタイムアウト エラーが発生する場合を除いて推奨されません。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>例

以下の例は、左側ストリーム `NameNorm2` と右側ストリーム `TypeConversions` を取る `checkForChanges` という名前の存在変換です。  存在条件の式は `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` です。この式では、各ストリームで `EMPID` 列と `Region` 列の両方が一致する場合に true が返されます。 存在を確認している間、`negate` は false になります。 最適化タブでブロードキャストを有効にしていないので、`broadcast` の値は `'none'` になります。

UI エクスペリエンスでは、この変換は次の図のようになります。

:::image type="content" source="media/data-flow/exists-script.png" alt-text="存在の例":::

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>次のステップ

同様の変換として、[参照](data-flow-lookup.md)と[結合](data-flow-join.md)があります。
