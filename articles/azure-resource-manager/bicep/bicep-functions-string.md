---
title: Bicep 関数 - 文字列
description: 文字列を操作する際に Bicep ファイルで使用する関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: a73df839ff7dcaad992b8930c8fb2545e854951c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028080"
---
# <a name="string-functions-for-bicep"></a>Bicep の string 関数

この記事では、文字列を操作するための Bicep 関数について説明します。

## <a name="base64"></a>base64

`base64(inputString)`

入力文字列の base64 表現を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |Base 64 形式として返す値。 |

### <a name="return-value"></a>戻り値

base64 形式を含む文字列。

### <a name="examples"></a>例

次の例では、base64 関数を使用する方法を示します。

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

base64 形式を JSON オブジェクトに変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |JSON オブジェクトに変換する base64 形式。 |

### <a name="return-value"></a>戻り値

JSON オブジェクト。

### <a name="examples"></a>例

次の例では、base64ToJson 関数を使用して base64 値を変換します。

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

base64 形式を文字列に変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |文字列に変換する base64 形式。 |

### <a name="return-value"></a>戻り値

変換された base64 値の文字列。

### <a name="examples"></a>例

次の例では、base64ToString 関数を使用して base64 値を変換します。

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

concat 関数を使用する代わりに、文字列補間を使用します。

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}And${uniqueString(resourceGroup().id)}'
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| concatOutput | String | prefixAnd5yj4yjf5mbg72 |

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

## <a name="contains"></a>contains

`contains (container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。 文字列比較では大文字・小文字を区別します。 ただし、オブジェクトにキーが含まれているかどうかをテストする場合、比較で大文字・小文字を区別しません。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |はい |文字列または整数 |検索対象の値。 |

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

### <a name="examples"></a>例

次の例では、contains をさまざまな型で使用する方法を示します。

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
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

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToConvert |はい |string |データ URI に変換する値。 |

### <a name="return-value"></a>戻り値

データ URI として書式設定された文字列。

### <a name="examples"></a>例

次の例では、値をデータ URI に変換し、データ URI を文字列に変換します。

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

データ URI の形式で書式設定された値を文字列に変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |はい |string |変換するデータ URI 値。 |

### <a name="return-value"></a>戻り値

変換後の値を含む文字列。

### <a name="examples"></a>例

次の例では、値をデータ URI に変換し、データ URI を文字列に変換します。

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| itemToTest |はい |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

### <a name="examples"></a>例

次の例では、配列、オブジェクト、および文字列が空かどうかを確認します。

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

文字列が特定の値で終わるかどうかを判断します。 比較では大文字と小文字は区別されません。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の例は、startsWith 関数と endsWith 関数の使用方法を示しています。

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
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

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字、または配列の最初の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の例では、first 関数を配列および文字列と共に使用する方法を示します。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

入力値から書式設定された文字列を作成します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| formatString | はい | string | 複合の書式設定文字列。 |
| arg1 | はい | 文字列、整数、またはブール値 | 書式設定された文字列に含める値。 |
| 残りの引数 | いいえ | 文字列、整数、またはブール値 | 書式設定された文字列に含める追加の値。 |

### <a name="remarks"></a>解説

この関数を使用して、Bicep ファイル内の文字列を書式設定します。 .NET 内の[System.String.Format](/dotnet/api/system.string.format) メソッドと同じ書式設定オプションを使用します。

### <a name="examples"></a>例

次の例に、書式設定の関数を使用する方法を示します。

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| formatTest | String | Hello, User. Formatted number:8,175,133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

パラメーターとして指定した値に基づき、グローバル一意識別子の形式で値を作成します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| baseString |はい |string |GUID を作成するためにハッシュ関数で使用される値。 |
| 必要に応じて追加のパラメーター |いいえ |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

### <a name="remarks"></a>解説

この関数は、グローバル一意識別子の形式で値を作成する必要がある場合に役立ちます。 結果の一意性のスコープを制限するパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイのレベルで名前が一意であるかどうかを指定できます。

返される値はランダムな文字列ではなく、パラメーターに対するハッシュ関数の結果になります。 返される値は、36 文字です。 グローバルに一意ではありません。 パラメーターのそのハッシュ値に基づかない新しい GUID を作成するには、[newGuid](#newguid) 関数を使用します。

次の例は、guid を使用して、よく使用されるレベルで一意の値を作成する方法を示しています。

サブスクリプションのスコープで一意

```bicep
guid(subscription().subscriptionId)
```

リソース グループのスコープで一意

```bicep
guid(resourceGroup().id)
```

リソース グループのデプロイのスコープで一意

```bicep
guid(resourceGroup().id, deployment().name)
```

### <a name="return-value"></a>戻り値

グローバル一意識別子の形式の 36 文字を含む文字列。

> [!NOTE]
> 順序の重要性: 同じパラメーターであるだけでなく、同じ順序である必要があります。 例: `guid('hello', 'world') != guid('world', 'hello')`

### <a name="examples"></a>例

次の例では、guid からの結果を返します。

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

文字列内の値の最初の位置を返します。 比較では大文字と小文字は区別されません。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の例は、indexOf 関数と lastIndexOf 関数の使用方法を示しています。

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

有効な JSON 文字列を JSON データ型に変換します。 詳細については、[JSON 関数](./bicep-functions-object.md#json)を参照してください。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

## <a name="last"></a>last

`last (arg1)`

文字列の最後の文字、または配列の最後の要素を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字、または配列の最後の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の例では、last 関数を配列および文字列と共に使用する方法を示します。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

文字列内の値の最後の位置を返します。 比較では大文字と小文字は区別されません。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の最後の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の例は、indexOf 関数と lastIndexOf 関数の使用方法を示しています。

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="length"></a>length

`length(string)`

文字列内の文字、配列内の要素、またはオブジェクト内のルート レベル プロパティの数を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |array、string、または object |要素の数を取得するために使用する配列、文字の数を取得するために使用する文字列、またはルート レベル プロパティの数を取得するために使用するオブジェクト。 |

### <a name="return-value"></a>戻り値

整数。

### <a name="examples"></a>例

次の例では、length を配列および文字列と共に使用する方法を示します。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

グローバル一意識別子の形式の値を返します。 **この関数は、パラメーターの既定値でのみ使用できます。**

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数を Bicep ファイルのその他の場所で使用すると、エラーが返されます。 Bicep ファイルの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じ Bicep ファイルをデプロイしても、同じ結果が生成される保証はありません。

newGuid 関数は、パラメーターを受け取らない点が [guid](#guid) 関数と異なります。 guid では、同じパラメーターを使用して呼び出すと、毎回同じ識別子が返されます。 特定の環境に対して同じ GUID を確実に生成する必要がある場合は、guid を使用してください。 テスト環境へのリソースのデプロイなど、毎回異なる識別子が必要なときは、newGuid を使用してください。

newGuid 関数では、.NET Framework 内の [Guid 構造](/dotnet/api/system.guid)を使用して、グローバル一意識別子が生成されます。

[以前の正常なデプロイを再デプロイするオプション](../templates/rollback-on-error.md)を使用し、以前のデプロイに newGuid を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

テスト環境では、短時間だけ存在するリソースを繰り返しデプロイすることが必要な場合があります。 一意の名前を構築するのではなく、[uniqueString](#uniquestring) で newGuid を使用して一意の名前を作成できます。

既定値に対する newGuid 関数に依存する Bicep ファイルを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

グローバル一意識別子の形式の 36 文字を含む文字列。

### <a name="examples"></a>例

次の例に、新しい識別子を使用するパラメーターを示します。

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

次の例では、newGuid 関数を使用して、ストレージ アカウントの一意の名前を作成します。 この Bicep ファイルは、ストレージ アカウントが短時間だけ存在し、再デプロイされないテスト環境に適しています。

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| valueToPad |はい |文字列または整数 |右揃えにする値。 |
| totalLength |はい |INT |返される文字列の文字合計数。 |
| paddingCharacter |いいえ |1 文字 |左余白の長さに到達するまで使用する文字。 既定値は空白です。 |

元の文字列が、埋め込まれる文字数よりも長い場合は、文字が追加されません。

### <a name="return-value"></a>戻り値

少なくとも指定した文字数の文字列。

### <a name="examples"></a>例

次の例では、文字の合計数に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値を埋め込む方法を示します。

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

ある文字列のすべてのインスタンスを別の文字列で置き換えた、新しい文字列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| originalString |はい |string |別の文字列で置き換えられる文字列の全インスタンスを含む値。 |
| oldString |はい |string |元の文字列から削除する文字列。 |
| newString |はい |string |削除された文字列の代わりに追加する文字列。 |

### <a name="return-value"></a>戻り値

文字が置き換えられた文字列。

### <a name="examples"></a>例

次の例では、ユーザーが指定した文字列からすべてのダッシュを削除し、その文字列の部分を別の文字列に置き換える方法を示します。

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

指定した文字数の後にあるすべての文字を含む文字列を返します。または、指定した数の要素の後にあるすべての要素を含む配列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |INT |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超過している場合は、空の配列または文字列が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の例では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

指定された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |分割する文字列。 |
| delimiter |はい |文字列または文字列の配列 |文字列の分割に使用する区切り記号。 |

### <a name="return-value"></a>戻り値

文字列の配列。

### <a name="examples"></a>例

次の例では、入力文字列をコンマで分割したり、コンマまたはセミコロンで分割したりします。

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

文字列が特定の値で始まるかどうかを判断します。 比較では大文字と小文字は区別されません。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の例は、startsWith 関数と endsWith 関数の使用方法を示しています。

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
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

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい | Any |文字列に変換する値。 オブジェクトと配列を含む、あらゆる種類の値を変換できます。 |

### <a name="return-value"></a>戻り値

変換された値の文字列。

### <a name="examples"></a>例

次の例では、さまざまな型の値を文字列に変換する方法を示します。

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

指定した文字位置から始まる指定された文字数分の部分文字列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToParse |はい |string |部分文字列の抽出元となる文字列。 |
| startIndex |いいえ |INT |部分文字列の 0 から始まる開始文字位置。 |
| length |いいえ |INT |部分文字列の文字数。 文字列内の場所を参照する必要があります。 0 以上である必要があります。 省略した場合、開始位置からの文字列の残りの部分が返されます。|

### <a name="return-value"></a>戻り値

部分文字列。 または、長さが 0 の場合は空の文字列。

### <a name="remarks"></a>解説

この関数は、部分文字列が文字列の最後を超えるか、または長さが 0 未満のときは失敗します。 次の例は、"インデックス パラメーターと長さパラメーターは文字列内の場所を参照している必要があります。 インデックス パラメーター:'0'、長さパラメーター:'11'、文字列パラメーターの長さ:'10'。" というエラーで失敗します。

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

### <a name="examples"></a>例

次の例では、パラメーターから部分文字列を抽出します。

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

文字列の先頭から指定した数の文字を含む文字列を、または配列の先頭から指定した数の要素を含む配列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |はい |INT |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超過している場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の例では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

指定された文字列を小文字に変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToChange |はい |string |小文字に変換する値。 |

### <a name="return-value"></a>戻り値

小文字に変換された文字列。

### <a name="examples"></a>例

次の例では、パラメーター値を小文字と大文字に変換します。

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

指定した文字列を大文字に変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToChange |はい |string |大文字に変換する値。 |

### <a name="return-value"></a>戻り値

大文字に変換された文字列。

### <a name="examples"></a>例

次の例では、パラメーター値を小文字と大文字に変換します。

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

指定された文字列から先頭と末尾の空白文字をすべて削除します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToTrim |はい |string |トリムする値。 |

### <a name="return-value"></a>戻り値

先頭および末尾の空白文字なし文字列です。

### <a name="examples"></a>例

次の例では、パラメーターから空白文字を削除します。

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| 戻り値 | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| baseString |はい |string |一意の文字列を作成するためにハッシュ関数で使用される値。 |
| 必要に応じて追加のパラメーター |いいえ |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

### <a name="remarks"></a>解説

この関数は、リソースの一意の名前を作成する必要がある場合に便利です。 結果の一意性のスコープを制限するパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイのレベルで名前が一意であるかどうかを指定できます。

返される値はランダムな文字列ではなく、ハッシュ関数の結果になります。 返される値は、13 文字です。 グローバルに一意ではありません。 命名規則にあるプレフィックスをこの値と組み合わせて、わかりやすい名前を作成することもできます。 次の例では、戻り値の形式を示します。 実際の値は、指定されたパラメーターによって異なります。

`tcvhiyu5h2o5o`

次の例は、uniqueString を使用して、よく使用されるレベルで一意の値を作成する方法を示しています。

サブスクリプションのスコープで一意

```bicep
uniqueString(subscription().subscriptionId)
```

リソース グループのスコープで一意

```bicep
uniqueString(resourceGroup().id)
```

リソース グループのデプロイのスコープで一意

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

次の例は、リソース グループに基づいてストレージ アカウントの一意の名前を作成する方法を示しています。 リソース グループ内では、名前は、同じ方法で作成されると一意ではなくなります。

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

Bicep ファイルをデプロイするたびに一意の新しい名前を作成する必要があり、リソースを更新しない場合は、[utcNow](./bicep-functions-date.md#utcnow) 関数と共に uniqueString を使用できます。 この方法は、テスト環境で使用できます。 例については、「[utcNow](./bicep-functions-date.md#utcnow)」をご覧ください。 utcNow 関数は、パラメーターの既定値の式内でのみ使用できることにご留意ください。

### <a name="return-value"></a>戻り値

13 文字が含まれる文字列。

### <a name="examples"></a>例

次の例は、uniquestring から結果を返します。

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| baseUri |はい |string |ベース URI 文字列。 この表の後に記述されている、末尾のスラッシュ ("/") の処理に関する動作を注意して確認してください。  |
| relativeUri |はい |string |ベース URI 文字列に追加する相対 URI 文字列。 |

* **baseUri** の末尾がスラッシュで終わる場合、結果は単純に **baseUri** の後に **relativeUri** が続きます。

* **baseUri** の末尾がスラッシュで終わっていない場合は、次の 2 つのいずれかが発生します。

   * **baseUri** にスラッシュが (先頭付近の "//" を除いて) まったく含まれていない場合、結果は単純に **baseUri** の後に **relativeUri** が続きます。

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

次の例では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

URI をエンコードします。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToEncode |はい |string |エンコード対象の値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値の文字列。

### <a name="examples"></a>例

次の例では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

URI エンコードされた値の文字列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| uriEncodedString |はい |string |文字列に変換する URI エンコードされた値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値のデコード済み文字列。

### <a name="examples"></a>例

次の例では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>次のステップ

* Bicep ファイルのセクションの説明は、[Bicep ファイルの構造と構文](./file.md)に関する記事をご覧ください。
* リソースの種類を作成するときに指定した回数反復処理を行う場合は [Bicep での反復ループ](loops.md)を参照してください。
* 作成した Bicep ファイルをデプロイする方法については、「[Bicep ファイルと Azure PowerShell を使用してリソースをデプロイする](./deploy-powershell.md)」を参照してください。
