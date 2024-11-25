---
title: テンプレート関数 - 文字列
description: Azure Resource Manager テンプレート (ARM テンプレート) で文字列の操作に使用する関数について説明します。
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: 44661751e3bd1f32a9b06ed6b7b6993716162cb1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455038"
---
# <a name="string-functions-for-arm-templates"></a>ARM テンプレート用の文字列関数

Resource Manager では、Azure Resource Manager テンプレート (ARM テンプレート) で文字列を操作するために、次の関数が提供されています。

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [json](#json)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

入力文字列の base64 表現を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |Base 64 形式として返す値。 |

### <a name="return-value"></a>戻り値

base64 形式を含む文字列。

### <a name="examples"></a>例

次の例は、`base64` 関数の使用法を示しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/base64.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

base64 形式を JSON オブジェクトに変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |JSON オブジェクトに変換する base64 形式。 |

### <a name="return-value"></a>戻り値

JSON オブジェクト。

### <a name="examples"></a>例

次の例では、`base64ToJson` 関数を使用して base64 値を変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/base64.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

base64 形式を文字列に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |文字列に変換する base64 形式。 |

### <a name="return-value"></a>戻り値

変換された base64 値の文字列。

### <a name="examples"></a>例

次の例では、`base64ToString` 関数を使用して base64 値を変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/base64.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

複数の文字列値を結合して連結された文字列を返します。または複数の配列を結合して連結された配列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または配列 |連結の最初の文字列または配列。 |
| その他の引数 |いいえ |文字列または配列 |連結する順番でのその他の文字列または配列。 |

この関数は、任意の数の引数を取ることができ、パラメーターに文字列または配列を使用できます。 ただし、パラメーターに配列と文字列の両方を指定することはできません。 文字列は、他の文字列とのみ連結されます。

### <a name="return-value"></a>戻り値

連結された値の文字列または配列。

### <a name="examples"></a>例

次の例は、2 つの文字列値を結合して 1 つの連結文字列を返す方法を示しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

次の例では、2 つの配列を結合する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| 戻り値 | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。 文字列比較では大文字・小文字を区別します。 ただし、オブジェクトにキーが含まれているかどうかをテストする場合、比較で大文字・小文字を区別しません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |はい |文字列または整数 |検索対象の値。 |

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

### <a name="examples"></a>例

次の例では、contains をさまざまな型で使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

値をデータ URI に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToConvert |はい |string |データ URI に変換する値。 |

### <a name="return-value"></a>戻り値

データ URI として書式設定された文字列。

### <a name="examples"></a>例

次の例では、値をデータ URI に変換し、データ URI を文字列に変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/datauri.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

データ URI の形式で書式設定された値を文字列に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |はい |string |変換するデータ URI 値。 |

### <a name="return-value"></a>戻り値

変換後の値を含む文字列。

### <a name="examples"></a>例

次のテンプレート例では、値をデータ URI に変換し、データ URI を文字列に変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/datauri.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| itemToTest |はい |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

### <a name="examples"></a>例

次の例では、配列、オブジェクト、および文字列が空かどうかを確認します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

文字列が特定の値で終わるかどうかを判断します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の例では、`startsWith` 関数および `endsWith` 関数の使用方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/startsendswith.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

文字列の最初の文字、または配列の最初の要素を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字、または配列の最初の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の例では、first 関数を配列および文字列と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

入力値から書式設定された文字列を作成します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| formatString | はい | string | 複合の書式設定文字列。 |
| arg1 | はい | 文字列、整数、またはブール値 | 書式設定された文字列に含める値。 |
| その他の引数 | いいえ | 文字列、整数、またはブール値 | 書式設定された文字列に含めるその他の値。 |

### <a name="remarks"></a>解説

この関数を使用して、テンプレート内の文字列を書式設定します。 .NET 内の[System.String.Format](/dotnet/api/system.string.format) メソッドと同じ書式設定オプションを使用します。

### <a name="examples"></a>例

次の例に、書式設定の関数を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/format.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| formatTest | String | Hello, User. Formatted number:8,175,133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

パラメーターとして指定した値に基づき、グローバル一意識別子の形式で値を作成します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| baseString |はい |string |GUID を作成するためにハッシュ関数で使用される値。 |
| 必要に応じてその他のパラメーター |いいえ |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

### <a name="remarks"></a>解説

この関数は、グローバル一意識別子の形式で値を作成する必要がある場合に役立ちます。 結果の一意性のスコープを制限するパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイのレベルで名前が一意であるかどうかを指定できます。

返される値はランダムな文字列ではなく、パラメーターに対するハッシュ関数の結果になります。 返される値は、36 文字です。 グローバルに一意ではありません。 パラメーターのそのハッシュ値に基づかない新しい GUID を作成するには、[newGuid](#newguid) 関数を使用します。

次の例は、guid を使用して、よく使用されるレベルで一意の値を作成する方法を示しています。

サブスクリプションのスコープで一意

```json
"[guid(subscription().subscriptionId)]"
```

リソース グループのスコープで一意

```json
"[guid(resourceGroup().id)]"
```

リソース グループのデプロイのスコープで一意

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>戻り値

グローバル一意識別子の形式の 36 文字を含む文字列。

### <a name="examples"></a>例

次の例では、`guid` から結果を返します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/guid.json":::

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

文字列内の値の最初の位置を返します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の例では、`indexOf` 関数および `lastIndexOf` 関数の使用方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/indexof.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

有効な JSON 文字列を JSON データ型に変換します。 詳細については、[JSON 関数](template-functions-object.md#json)を参照してください。

## <a name="last"></a>last

`last (arg1)`

文字列の最後の文字、または配列の最後の要素を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字、または配列の最後の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の例では、`last` 関数を配列および文字列と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

文字列内の値の最後の位置を返します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の最後の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の例では、`indexOf` 関数および `lastIndexOf` 関数の使用方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/indexof.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="length"></a>length

`length(string)`

文字列内の文字、配列内の要素、またはオブジェクト内のルート レベル プロパティの数を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |array、string、または object |要素の数を取得するために使用する配列、文字の数を取得するために使用する文字列、またはルート レベル プロパティの数を取得するために使用するオブジェクト。 |

### <a name="return-value"></a>戻り値

整数。

### <a name="examples"></a>例

次の例では、`length` 関数を配列および文字列と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

グローバル一意識別子の形式の値を返します。 **この関数は、パラメーターの既定値でのみ使用できます。**

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数をテンプレートのその他の場所で使用すると、エラーが返されます。 テンプレートの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じテンプレートをデプロイしても、同じ結果が生成される保証はありません。

newGuid 関数は、パラメーターを受け取らない点が [guid](#guid) 関数と異なります。 guid では、同じパラメーターを使用して呼び出すと、毎回同じ識別子が返されます。 特定の環境に対して同じ GUID を確実に生成する必要がある場合は、guid を使用してください。 テスト環境へのリソースのデプロイなど、毎回異なる識別子が必要なときは、newGuid を使用してください。

newGuid 関数では、.NET Framework 内の [Guid 構造](/dotnet/api/system.guid)を使用して、グローバル一意識別子が生成されます。

[以前の正常なデプロイを再デプロイするオプション](rollback-on-error.md)を使用し、以前のデプロイに newGuid を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

テスト環境では、短時間だけ存在するリソースを繰り返しデプロイすることが必要な場合があります。 一意の名前を構築するのではなく、[uniqueString](#uniquestring) で newGuid を使用して一意の名前を作成できます。

既定値に対する newGuid 関数に依存するテンプレートを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

グローバル一意識別子の形式の 36 文字を含む文字列。

### <a name="examples"></a>例

次の例に、新しい識別子を使用するパラメーターを示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/newguid.json":::

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

次の例では、`newGuid` 関数を使用して、ストレージ アカウントの一意の名前を作成します。 このテンプレートは、ストレージ アカウントが短時間だけ存在し、再デプロイされないテスト環境に適しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/newguid-storageaccount.json":::

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| valueToPad |はい |文字列または整数 |右揃えにする値。 |
| totalLength |はい |INT |返される文字列の文字合計数。 |
| paddingCharacter |いいえ |1 文字 |左余白の長さに到達するまで使用する文字。 既定値は空白です。 |

元の文字列が、埋め込まれる文字数よりも長い場合は、文字が追加されません。

### <a name="return-value"></a>戻り値

少なくとも指定した文字数の文字列。

### <a name="examples"></a>例

次の例では、文字の合計数に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値を埋め込む方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/padleft.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

ある文字列のすべてのインスタンスを別の文字列で置き換えた、新しい文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalString |はい |string |別の文字列で置き換えられる文字列の全インスタンスを含む値。 |
| oldString |はい |string |元の文字列から削除する文字列。 |
| newString |はい |string |削除された文字列の代わりに追加する文字列。 |

### <a name="return-value"></a>戻り値

文字が置き換えられた文字列。

### <a name="examples"></a>例

次の例では、ユーザーが指定した文字列からすべてのダッシュを削除し、その文字列の部分を別の文字列に置き換える方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/replace.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

指定した文字数の後にあるすべての文字を含む文字列を返します。または、指定した数の要素の後にあるすべての要素を含む配列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |INT |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超過している場合は、空の配列または文字列が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の例では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

指定された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |分割する文字列。 |
| delimiter |はい |文字列または文字列の配列 |文字列の分割に使用する区切り記号。 |

### <a name="return-value"></a>戻り値

文字列の配列。

### <a name="examples"></a>例

次の例では、入力文字列をコンマで分割したり、コンマまたはセミコロンで分割したりします。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/split.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

文字列が特定の値で始まるかどうかを判断します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の例では、`startsWith` 関数および `endsWith` 関数の使用方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/startsendswith.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

指定した値を文字列に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい | Any |文字列に変換する値。 オブジェクトと配列を含む、あらゆる種類の値を変換できます。 |

### <a name="return-value"></a>戻り値

変換された値の文字列。

### <a name="examples"></a>例

次の例では、さまざまな型の値を文字列に変換する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/string.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

指定した文字位置から始まる指定された文字数分の部分文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToParse |はい |string |部分文字列の抽出元となる文字列。 |
| startIndex |いいえ |INT |部分文字列の 0 から始まる開始文字位置。 |
| length |いいえ |INT |部分文字列の文字数。 文字列内の場所を参照する必要があります。 0 以上である必要があります。 省略した場合、開始位置からの文字列の残りの部分が返されます。|

### <a name="return-value"></a>戻り値

部分文字列。 または、長さが 0 の場合は空の文字列。

### <a name="remarks"></a>解説

この関数は、部分文字列が文字列の最後を超えるか、または長さが 0 未満のときは失敗します。 次の例は、"インデックス パラメーターと長さパラメーターは文字列内の場所を参照している必要があります。 インデックス パラメーター:'0'、長さパラメーター:'11'、文字列パラメーターの長さ:'10'。" というエラーで失敗します。

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>例

次の例では、パラメーターから部分文字列を抽出します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/substring.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

配列または文字列を返します。 配列には配列の先頭から指定された数の要素が含まれます。 文字列には文字列の先頭から指定された数の文字が含まれます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |はい |INT |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超過している場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の例では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

指定された文字列を小文字に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToChange |はい |string |小文字に変換する値。 |

### <a name="return-value"></a>戻り値

小文字に変換された文字列。

### <a name="examples"></a>例

次の例では、パラメーター値を小文字と大文字に変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/tolower.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

指定した文字列を大文字に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToChange |はい |string |大文字に変換する値。 |

### <a name="return-value"></a>戻り値

大文字に変換された文字列。

### <a name="examples"></a>例

次の例では、パラメーター値を小文字と大文字に変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/tolower.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

指定された文字列から先頭と末尾の空白文字をすべて削除します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToTrim |はい |string |トリムする値。 |

### <a name="return-value"></a>戻り値

先頭および末尾の空白文字なし文字列です。

### <a name="examples"></a>例

次の例では、パラメーターから空白文字を削除します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/trim.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| 戻り値 | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| baseString |はい |string |一意の文字列を作成するためにハッシュ関数で使用される値。 |
| 必要に応じてその他のパラメーター |いいえ |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

### <a name="remarks"></a>解説

この関数は、リソースの一意の名前を作成する必要がある場合に便利です。 結果の一意性のスコープを制限するパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイのレベルで名前が一意であるかどうかを指定できます。

返される値はランダムな文字列ではなく、ハッシュ関数の結果になります。 返される値は、13 文字です。 グローバルに一意ではありません。 命名規則にあるプレフィックスをこの値と組み合わせて、わかりやすい名前を作成することもできます。 次の例では、戻り値の形式を示します。 実際の値は、指定されたパラメーターによって異なります。

`tcvhiyu5h2o5o`

次の例では、`uniqueString` を使用して、よく使用されるレベルで一意の値を作成する方法を示します。

サブスクリプションのスコープで一意

```json
"[uniqueString(subscription().subscriptionId)]"
```

リソース グループのスコープで一意

```json
"[uniqueString(resourceGroup().id)]"
```

リソース グループのデプロイのスコープで一意

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

次の例は、リソース グループに基づいてストレージ アカウントの一意の名前を作成する方法を示しています。 リソース グループ内では、名前は、同じ方法で作成されると一意ではなくなります。

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

テンプレートをデプロイするたびに一意の新しい名前を作成する必要があり、リソースを更新しない場合は、[utcNow](template-functions-date.md#utcnow) 関数と `uniqueString` を使用できます。 この方法は、テスト環境で使用できます。 例については、「[utcNow](template-functions-date.md#utcnow)」をご覧ください。

### <a name="return-value"></a>戻り値

13 文字が含まれる文字列。

### <a name="examples"></a>例

次の例では、`uniquestring` から結果を返します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uniquestring.json":::

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| baseUri |はい |string |ベース URI 文字列。 この表の後に記述されている、末尾のスラッシュ (`/`) の処理に関する動作を注意して確認してください。  |
| relativeUri |はい |string |ベース URI 文字列に追加する相対 URI 文字列。 |

* **baseUri** の末尾がスラッシュで終わる場合、結果は **baseUri** の後に **relativeUri** が続きます。

* **baseUri** の末尾がスラッシュで終わっていない場合は、次の 2 つのいずれかが発生します。

   * **baseUri** にスラッシュが (先頭付近の `//` を除いて) まったく含まれていない場合、結果は **baseUri** の後に **relativeUri** が続きます。

   * **baseUri** にスラッシュが含まれているが、末尾がスラッシュでない場合は、最後のスラッシュの後ろがすべて **baseUri** から削除され、結果は **baseUri** の後に **relativeUri** が続きます。

次に例をいくつか示します。

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```

詳細については、[RFC 3986 のセクション 5](https://tools.ietf.org/html/rfc3986#section-5) に記載されているように、**baseUri** パラメーターと **relativeUri** パラメーターが解決されます。

### <a name="return-value"></a>戻り値

ベース値および相対値の絶対 URI を表す文字列。

### <a name="examples"></a>例

次の例は、親テンプレートの値に基づいて、入れ子になったテンプレートへのリンクを作成する方法を示しています。

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

次のテンプレート例では、`uri`、`uriComponent`、および `uriComponentToString` を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uri.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

URI をエンコードします。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToEncode |はい |string |エンコード対象の値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値の文字列。

### <a name="examples"></a>例

次のテンプレート例では、`uri`、`uriComponent`、および `uriComponentToString` を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uri.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

URI エンコードされた値の文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| uriEncodedString |はい |string |文字列に変換する URI エンコードされた値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値のデコード済み文字列。

### <a name="examples"></a>例

次の例では、`uri`、`uriComponent`、および `uriComponentToString` を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uri.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。
* 複数のテンプレートをマージする方法については、「[Azure リソース デプロイ時のリンクされたテンプレートおよび入れ子になったテンプレートの使用](linked-templates.md)」を参照してください。
* ある種類のリソースを作成するときに、指定した回数だけ反復する方法については、「[ARM テンプレートでのリソースの反復処理](copy-resources.md)」を参照してください。
* 作成したテンプレートをデプロイする方法については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
