---
title: OData 比較演算子のリファレンス
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のクエリの OData 比較演算子 (eq、ne、gt、lt、ge、および le) を使用する構文およびリファレンス ドキュメント。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 804e974196b62b6dfed9d4bd64b8dfe0cc107faf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591522"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure Cognitive Search の OData 比較演算子 - `eq`、`ne`、`gt`、`lt`、`ge`、`le`

Azure Cognitive Search の [OData フィルター式](query-odata-filter-orderby-syntax.md)で最も基本的な操作は、フィールドと特定の値との比較です。 等価比較と範囲比較の 2 種類の比較を行うことができます。 次の演算子を使用して、フィールドを定数値と比較できます。

等価演算子:

- `eq`: フィールドが定数値と **等しい** かどうかをテストします
- `ne`: フィールドが定数値 **と等しくない** かどうかをテストします

範囲演算子:

- `gt`: フィールドが定数値 **より大きい** かどうかをテストします
- `lt`: フィールドが定数値 **より小さい** かどうかをテストします
- `ge`: フィールドが定数値 **以上である** かどうかをテストします
- `le`: フィールドが定数値 **以下である** かどうかをテストします

範囲演算子を[論理演算子](search-query-odata-logical-operators.md)と組み合わせて使用すると、フィールドが特定の値の範囲内にあるかどうかをテストできます。 この記事の後半の[例](#examples)を参照してください。

> [!NOTE]
> 必要に応じて、演算子の左側に定数値を、右側にフィールド名を入力することができます。 範囲演算子では、比較の意味が逆になります。 たとえば、定数値が左側にある場合、`gt` は定数値がフィールドより大きいかどうかをテストします。 比較演算子を使用して、`geo.distance` などの関数の結果を値と比較することもできます。 `search.ismatch` などのブール関数の場合、結果と `true` または `false` との比較は省略可能です。

## <a name="syntax"></a>構文

次の EBNF ([拡張バッカスナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) は、比較演算子を使用する OData 式の文法を定義します。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 完全な EBNF については、[Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)に関するページをご覧ください。

比較式には 2 つの形式があります。 この 2 つの形式の唯一の違いは、定数が演算子の左側に示されるか右側に示されるかです。 演算子の反対側の式は、**変数** または関数呼び出しでなければなりません。 変数は、フィールド名、または範囲変数 ([ラムダ式](search-query-odata-collection-operators.md)の場合) のいずれかです。

## <a name="data-types-for-comparisons"></a>比較用のデータ型

比較演算子の両側のデータ型には互換性がなければなりません。 たとえば、左側が `Edm.DateTimeOffset` 型のフィールドの場合、右側は日時定数でなければなりません。 数値データ型は、より柔軟です。 次の表に示すように、いくつかの制限はありますが、任意の数値型の変数と関数を他の数値型の定数と比較できます。

| 変数または関数の型 | 定数値の型 | 制限事項 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比較は、[`NaN` の特殊なルール](#special-case-nan)に従います |
| `Edm.Double` | `Edm.Int64` | 定数は `Edm.Double` に変換され、大きな値の場合は精度が失われる結果となります |
| `Edm.Double` | `Edm.Int32` | 該当なし |
| `Edm.Int64` | `Edm.Double` | `NaN`、`-INF`、または `INF` との比較はできません |
| `Edm.Int64` | `Edm.Int64` | 該当なし |
| `Edm.Int64` | `Edm.Int32` | 定数は比較の前に `Edm.Int64` に変換されます |
| `Edm.Int32` | `Edm.Double` | `NaN`、`-INF`、または `INF` との比較はできません |
| `Edm.Int32` | `Edm.Int64` | 該当なし |
| `Edm.Int32` | `Edm.Int32` | 該当なし |

許可されていない比較 (たとえば、型 `Edm.Int64` のフィールドと `NaN` の比較) が行われた場合、Azure Cognitive Search REST API は "HTTP 400: 無効な要求" のエラーを返します。

> [!IMPORTANT]
> 数値型の比較は柔軟ですが、定数値が比較対象の変数または関数と同じデータ型になるように、フィルターで比較を記述することを強くお勧めします。 このことは、浮動小数点値と整数値が混在し、精度の低下が発生する暗黙的な変換が行われる可能性がある場合に特に重要です。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>`null` と `NaN` の特殊なケース

比較演算子を使用する場合、Azure Cognitive Search のすべての非コレクション フィールドは `null` になる可能性があることを覚えておくことが重要です。 次の表に、いずれかの側が `null` になる可能性がある比較式の考えられるすべての結果を示します。

| 演算子 | フィールドまたは変数のみが `null` の場合の結果 | 定数のみが `null` の場合の結果 | フィールドまたは変数と定数の両方が `null` の場合の結果 |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: 無効な要求エラー | HTTP 400: 無効な要求エラー |
| `lt` | `false` | HTTP 400: 無効な要求エラー | HTTP 400: 無効な要求エラー |
| `ge` | `false` | HTTP 400: 無効な要求エラー | HTTP 400: 無効な要求エラー |
| `le` | `false` | HTTP 400: 無効な要求エラー | HTTP 400: 無効な要求エラー |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

要約すると、`null` はそれ自体にのみ等しく、他のどの値より小さくも大きくもありません。

インデックスに型 `Edm.Double` のフィールドがあり、`NaN` の値をそれらのフィールドにアップロードする場合は、フィルターを作成するときにそのことを考慮する必要があります。 Azure Cognitive Search では、`NaN` 値を処理するために IEEE 754 標準を実装しています。このような値との比較により、次の表に示すように、明らかではない結果が生じます。

| 演算子 | 少なくとも 1 つのオペランドが `NaN` の場合の結果 |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

つまり、`NaN` は、それ自体を含む、どの値とも等しくありません。

### <a name="comparing-geo-spatial-data"></a>地理空間データの比較

型 `Edm.GeographyPoint` のフィールドを定数値と直接比較することはできませんが、`geo.distance` 関数を使用できます。 この関数は、型 `Edm.Double` の値を返すため、それを数値定数と比較して、定数の地理空間座標からの距離に基づいてフィルター処理できます。 以下の[例](#examples)を参照してください。

### <a name="comparing-string-data"></a>文字列データの比較

文字列は、`eq` 演算子と `ne` 演算子を使用してフィルターで比較して、完全一致を見つけることができます。 これらの比較では大文字と小文字が区別されます。

## <a name="examples"></a>例

`Rating` フィールドが 3 から 5 (両端を含む) の範囲のドキュメントを照合します。

```text
Rating ge 3 and Rating le 5
```

`Location` フィールドが指定した緯度および経度から 2 キロメートル未満のドキュメントを一致させる場合:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

`LastRenovationDate` フィールドが、2015 年 1 月 1 日の午前 0 時 (UTC) 以降のドキュメントを一致させる場合:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

`Details/Sku` フィールドが `null` ではないドキュメントを一致させる場合:

```text
Details/Sku ne null
```

少なくとも 1 つの部屋のタイプが "Deluxe Room" であり、`Rooms/Type` フィールドの文字列がフィルターと正確に一致するホテルのドキュメントを一致させる場合:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>次のステップ  

- [Azure Cognitive Search のフィルター](search-filters.md)
- [Azure Cognitive Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)