---
title: Translator の Translate メソッド
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services のテキストを翻訳するための Translator の Translate メソッド用のパラメーター、ヘッダー、および本文のメッセージを理解します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: 8e3c343aede5959b30e4732e74eb6a56a683e365
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848536"
---
# <a name="translator-30-translate"></a>Translator 3.0:Translate

テキストを翻訳します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

### <a name="required-parameters"></a>必須のパラメーター

| Query parameter (クエリ パラメーター) | 説明 |
| --- | --- |
| api-version | "_必須のパラメーター_"。  <br>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。 |
| to  | "_必須のパラメーター_"。  <br>出力テキストの言語を指定します。 ターゲット言語は、`translation` スコープに含まれている[サポートされている言語](v3-0-languages.md)のいずれかとする必要があります。 たとえば、ドイツ語に翻訳するには `to=de` を使用します。  <br>クエリ文字列内でパラメーターを繰り返すことにより、同時に複数の言語に翻訳することができます。 たとえば、ドイツ語とイタリア語に翻訳するには、`to=de&to=it` を使用します。 |

### <a name="optional-parameters"></a>省略可能なパラメーター



| Query parameter (クエリ パラメーター) | 説明 |
| --- | --- |


| Query parameter (クエリ パラメーター) | 説明 |
| --- | --- |
| from | "_省略可能なパラメーター_"。  <br>入力テキストの言語を指定します。 `translation` スコープを使用して[サポートされている言語](../reference/v3-0-languages.md)を検索することにより、翻訳することができるソース言語を確認します。 `from` パラメーターが指定されていない場合は、自動言語検出が適用されてソース言語が特定されます。  <br>  <br>[動的ディクショナリ](../dynamic-dictionary.md)機能を使用する場合は、自動検出ではなく、`from` パラメーターを使用する必要があります。 |
| textType | "_省略可能なパラメーター_"。  <br>翻訳するテキストがプレーン テキストか、それとも HTML テキストかを定義します。 HTML の場合は、適切な形式の完全な要素である必要があります。 指定できる値は `plain` (既定値) または `html` です。 |
| category | "_省略可能なパラメーター_"。  <br>翻訳のカテゴリ (ドメイン) を指定する文字列。 このパラメーターは、[Custom Translator](../customization.md) でビルドしたカスタマイズされたシステムから翻訳を取得するために使用します。 デプロイ済みのカスタマイズされたシステムを使用するには、カスタム翻訳ツール [プロジェクトの詳細](../custom-translator/how-to-create-project.md#view-project-details)からこのパラメーターにカテゴリ ID を追加します。 既定値は `general` です。 |
| profanityAction | "_省略可能なパラメーター_"。  <br>翻訳での不適切な表現の処理方法を指定します。 指定できる値は `NoAction` (既定値)、`Marked`、または `Deleted` です。 不適切な表現の処理方法を理解するには、[不適切な表現の処理](#handle-profanity)に関するセクションを参照してください。 |
| profanityMarker | "_省略可能なパラメーター_"。  <br>翻訳での不適切な表現のマーキング方法を指定します。 指定できる値は `Asterisk` (既定値) または `Tag` です。 不適切な表現の処理方法を理解するには、[不適切な表現の処理](#handle-profanity)に関するセクションを参照してください。 |
| includeAlignment | "_省略可能なパラメーター_"。  <br>ソース テキストから翻訳済みテキストへのアライメント プロジェクションを含めるかどうかを指定します。 指定できる値は `true` または `false` (既定値) です。 |
| includeSentenceLength | "_省略可能なパラメーター_"。  <br>入力テキストと翻訳済みテキストに対して文の境界を含めるかどうかを指定します。 指定できる値は `true` または `false` (既定値) です。 |
| suggestedFrom | "_省略可能なパラメーター_"。  <br>入力テキストの言語を識別できない場合のフォールバック言語を指定します。 `from` パラメーターが省略されている場合は、言語自動検出が適用されます。 検出に失敗した場合は、`suggestedFrom` 言語と見なされます。 |
| fromScript | "_省略可能なパラメーター_"。  <br>入力テキストのスクリプトを指定します。 |
| toScript | "_省略可能なパラメーター_"。  <br>翻訳済みテキストのスクリプトを指定します。 |
| allowFallback | "_省略可能なパラメーター_"。  <br>カスタム システムが存在しない場合に、サービスが一般的なシステムにフォールバックできることを指定します。 指定できる値は `true` (既定値) または `false` です。  <br>  <br>`allowFallback=false` は、要求によって指定された `category` 向けにトレーニングされているシステムのみを翻訳で使用することを指定します。 言語 X から言語 Y への翻訳で、中心言語 E を経由するチェーン処理が必要な場合、チェーン内のすべてのシステム (X->E と E->Y) はカスタムであり、同じカテゴリを持っている必要があります。 特定のカテゴリを持つシステムが見つからない場合、要求は 400 状態コードを返します。 `allowFallback=true` は、カスタム システムが存在しない場合に、サービスが一般的なシステムにフォールバックできることを指定します。 |

要求ヘッダーには次のものがあります。

| ヘッダー | 説明 |
| --- | --- |
| 認証ヘッダー | "_必須の要求ヘッダー_" です。  <br>[認証に使用できるオプション](./v3-0-reference.md#authentication)に関するページをご覧ください。 |
| Content-Type | "_必須の要求ヘッダー_" です。  <br>ペイロードのコンテンツ タイプを指定します。  <br>指定できる値は `application/json; charset=UTF-8` です。 |
| Content-Length | "_必須の要求ヘッダー_" です。  <br>要求本文の長さです。 |
| X-ClientTraceId | _オプション_。  <br>要求を一意に識別する、クライアントで生成された GUID。 `ClientTraceId` という名前のクエリ パラメーターを使用してクエリ文字列内にトレース ID を含める場合、このヘッダーは省略できます。 |

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティ (翻訳するテキストを表す) を持つ JSON オブジェクトです。

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 100 個です。
* 要求に含めるテキスト全体では、スペースも含めて 10,000 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `detectedLanguage`:次のプロパティによって、検出された言語を説明するオブジェクトです。

      * `language`:検出された言語のコードを表す文字列です。

      * `score`:結果内の信頼度を示す浮動小数点値です。 スコアは 0 から 1 の範囲であり、低いスコアは低い信頼度を示します。

    `detectedLanguage` プロパティは、言語自動検出が要求された場合に限り、結果オブジェクト内に存在します。

  * `translations`:翻訳結果の配列です。 配列のサイズは、`to` クエリ パラメーターを通して指定されたターゲット言語の数に一致します。 配列内の各要素は次のとおりです。

    * `to`:ターゲット言語の言語コードを表す文字列です。

    * `text`:翻訳済みテキストを提供する文字列です。

    * `transliteration`:`toScript` パラメーターによって指定されたスクリプトで、翻訳済みテキストを提供するオブジェクトです。

      * `script`:ターゲット スクリプトを指定する文字列です。

      * `text`:ターゲット スクリプトで翻訳済みテキストを提供する文字列です。

    翻訳が実行されない場合、`transliteration` オブジェクトは含められません。

    * `alignment`:`proj` という名前の 1 つの文字列プロパティを持つオブジェクトです。これにより、入力テキストが翻訳済みテキストにマッピングされます。 アライメント情報は、要求パラメーター `includeAlignment` が `true` である場合に提供されるだけとなります。 アライメントは、次の形式の文字列値として返されます: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`。  コロンによって開始および終了インデックスが区切られ、ダッシュによって言語が区切られ、スペースによって単語が区切られます。 1 つの単語が他の言語では 0 個、1 個、または複数個の単語にアライメントされる場合があり、アライメントされた単語が連続していない場合もあります。 アライメント情報が使用できない場合、アライメント要素は空になります。 例と制限事項については、「[アライメント情報を取得する](#obtain-alignment-information)」を参照してください。

    * `sentLen`:入力テキストと出力テキスト内で文の境界を返すオブジェクトです。

      * `srcSentLen`:入力テキスト内の文の長さを表す整数配列です。 配列の長さは文の数であり、値は各文の長さです。

      * `transSentLen`:翻訳済みテキスト内の文の長さを表す整数配列です。 配列の長さは文の数であり、値は各文の長さです。

    文の境界は、要求パラメーター `includeSentenceLength` が `true` の場合にのみ含められます。

  * `sourceText`:`text` という名前の 1 つの文字列プロパティを持つオブジェクトです。これにより、ソース言語の既定のスクリプトで入力テキストが提供されます。 `sourceText` プロパティは、言語の通常のスクリプトではないスクリプトで入力が表されている場合にのみ存在します。 たとえば、入力がラテン文字で記述されたアラビア語であるならば、`sourceText.text` は同じアラビア語のテキストをアラビア文字に変換したものとなります。

JSON 応答の例については、「[例](#examples)」セクションを参照してください。

## <a name="response-headers"></a>応答ヘッダー

| ヘッダー | 説明 |
| --- | --- |
| X-RequestId | 要求を識別するためにサービスによって生成される値。 トラブルシューティングの目的で使用されます。 |
| X-MT-System | 翻訳するように要求された 'to' 言語への翻訳を実行するために使用されたシステムの種類を示します。 値は、文字列のコンマ区切りの一覧です。 各文字列は種類を示します。  <br><br>* Custom - 要求にはカスタム システムが含まれ、翻訳中に少なくとも 1 つのカスタム システムが使用されています。<br>* Team - それ以外のすべての要求 |

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

| ProfanityAction | アクション |
| --- | --- |
| `NoAction` |NoAction は既定の動作です。 不適切な表現はソースからターゲットに渡されます。  <br>  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)**: He is a jackass. |
| `Deleted` | 不適切な単語は、置換されずに出力から削除されます。  <br>  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)** : He is  |
| `Marked` | 不適切な単語は、出力ではマーカーに置き換えられます。 マーカーは `ProfanityMarker` パラメーターによって異なります。  <br>  <br>`ProfanityMarker=Asterisk` の場合、不適切な単語は `***` に置き換えられます。  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)** : He is a \\ *\\* \\*.  <br>  <br>`ProfanityMarker=Tag` の場合、不適切な単語は XML タグ &lt;profanity&gt; および &lt;/profanity&gt; によって囲まれます。  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)**: He is a &lt;profanity&gt;jackass&lt;/profanity&gt;. |

エラーが発生した場合は、要求の結果として JSON エラー応答も返されます。 このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コードは、[v3 Translator のリファレンス ページ](./v3-0-reference.md#errors)で確認できます。

## <a name="examples"></a>例

### <a name="translate-a-single-input"></a>単一の入力を翻訳する

この例では、1 つの文を英語から簡体中国語に翻訳する方法を示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

応答本文を次に示します。

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 配列には 1 つの要素が含まれており、これによって入力内の単一のテキストの翻訳が提供されます。

### <a name="translate-a-single-input-with-language-autodetection"></a>言語自動検出を使用して単一の入力を翻訳する

この例では、1 つの文を英語から簡体中国語に翻訳する方法を示します。 要求では入力言語が指定されていません。 代わりに、ソース言語の自動検出が使用されます。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

応答本文を次に示します。

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
応答は、前の例での応答に似ています。 言語自動検出が要求されたので、応答には入力テキストで検出された言語に関する情報も含まれています。 言語自動検出は、入力テキストが長いほど、うまく機能します。

### <a name="translate-with-transliteration"></a>音訳を使用して翻訳する

音訳を追加して、前の例を拡張してみましょう。 次の要求では、ラテン文字で記述する、中国語の翻訳が求められています。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

応答本文を次に示します。

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

翻訳結果には `transliteration` プロパティが含まれるようになります。これにより、ラテン文字を使用して翻訳されたテキストが提供されます。

### <a name="translate-multiple-pieces-of-text"></a>複数のテキストを変換する

一度に複数の文字列を翻訳するには、要求本文に文字列の配列を指定するだけです。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

応答には、テキストの全箇所の翻訳が要求とまったく同じ順序で含まれます。
応答本文を次に示します。

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>複数の言語に変換する

この例では、1 つの要求で同じ入力を複数の言語に翻訳する方法を示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

応答本文を次に示します。

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>不適切な表現を処理する

通常、Translator サービスでは、ソースに存在する不適切な表現は翻訳でも保たれます。 不適切な表現の程度や、言葉に不敬な意味を込めるコンテキストは文化によって異なります。結果として、ターゲット言語での不適切な表現の程度は強められることもあれば、弱められることもあります。

ソース テキスト内での不適切な表現の有無に関係なく、翻訳で不適切な表現が生じるのを防ぐには、不適切な表現のフィルター処理オプションを使用できます。 このオプションを使用すると、不適切な表現を削除するかどうか、適切なタグで不適切な表現をマークするかどうか (独自の後処理を追加するためのオプションが用意されています)、または何も操作を行わないかどうかを選択することができます。 `ProfanityAction` に指定できる値は、`Deleted`、`Marked`、および `NoAction` (既定値) です。


| ProfanityAction | アクション |
| --- | --- |
| `NoAction` | NoAction は既定の動作です。 不適切な表現はソースからターゲットに渡されます。  <br>  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)**: He is a jackass. |
| `Deleted` | 不適切な単語は、置換されずに出力から削除されます。  <br>  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)**: He is a. |
| `Marked` | 不適切な単語は、出力ではマーカーに置き換えられます。 マーカーは `ProfanityMarker` パラメーターによって異なります。  <br>  <br>`ProfanityMarker=Asterisk` の場合、不適切な単語は `***` に置き換えられます。  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)** : He is a \\ *\\* \\*.  <br>  <br>`ProfanityMarker=Tag` の場合、不適切な単語は XML タグ &lt;profanity&gt; および &lt;/profanity&gt; によって囲まれます。  <br>**ソースの例 (日本語)**: 彼はジャッカスです。  <br>**翻訳の例 (英語)**: He is a &lt;profanity&gt;jackass&lt;/profanity&gt;. |

次に例を示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is an <expletive> good idea.'}]"
```

この要求では次が返されます。

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

次の場合と比較します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is an <expletive> good idea.'}]"
```

その最後の要求では次が返されます。

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>マークアップを含むコンテンツの翻訳および翻訳対象の決定

HTML ページからのコンテンツや XML ドキュメントからのコンテンツなど、マークアップを含むコンテンツを翻訳することは一般的です。 タグ付きのコンテンツを翻訳する場合は、クエリ パラメーター `textType=html` を含めます。 さらに、特定のコンテンツを翻訳から除外すると便利な場合があります。 属性 `class=notranslate` を使用すると、元の言語のまま残す必要があるコンテンツを指定できます。 次の例では、1 番目の `div` 要素内のコンテンツは翻訳されませんが、2 番目の `div` 要素内のコンテンツは翻訳されます。

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

要求の例を次に示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

応答は次のとおりです。

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>アラインメント情報を取得する

アライメントは、ソースのすべての単語について、次の形式の文字列値として返されます。 各単語の情報は、中国語のようなスペースで区切られない言語 (書記法) の場合を含め、スペースで区切られます。

[[SourceTextStartIndex]\:[SourceTextEndIndex]–[TgtTextStartIndex]\:[TgtTextEndIndex]] *

アラインメント文字列は、たとえば、"0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21" のようになります。

言い換えると、コロンによって開始および終了インデックスが区切られ、ダッシュによって言語が区切られ、スペースによって単語が区切られます。 1 つの単語が他の言語では 0 個、1 個、または複数個の単語にアライメントされる場合があり、アライメントされた単語が連続していない場合もあります。 アライメント情報を使用できない場合は、Alignment 要素が空になります。 このメソッドは、その場合にエラーを返しません。

アライメント情報を受信するには、クエリ文字列上で `includeAlignment=true` を指定します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

応答は次のとおりです。

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

アライメント情報は `0:2-0:1` で始まっています。これは、ソース テキスト内の最初の 3 文字 (`The`) が翻訳済みテキスト内の最初の 2 文字 (`La`) にマッピングされていることを意味します。

#### <a name="limitations"></a>制限事項
アラインメント情報の取得は、フレーズ マッピングの可能性があるプロトタイプの研究とエクスペリエンスのために有効にされている試験的な機能です。 今後、このサポートは停止される可能性があります。 アラインメントがサポートされない場合の重要な制限事項を次に示します。

* HTML 形式のテキスト、つまり textType=html の場合、配置は使用できません。
* アライメントは言語ペアのサブセットに対してのみ返されます。
  - 英語から他の言語へ (繁体字中国語、広東語 (繁体字)、またはセルビア語 (キリル) 以外)、またはその逆。
  - 日本語から韓国語へ、または韓国語から日本語へ
  - 日本語から簡体字中国語へ、または簡体字中国語から日本語へ。
  - 簡体字中国語から繁体字中国語へ、または繁体字中国語から簡体字中国語へ。
* 文があらかじめ用意された翻訳である場合、アラインメントは返されません。 あらかじめ用意された翻訳には、"This is a test" や "I love you" など高い頻度で出現する文があります。
* [こちら](../prevent-translation.md)で説明されているように、翻訳を禁止するためのいずれかのアプローチを適用する場合、アラインメントは使用できません

### <a name="obtain-sentence-boundaries"></a>文の境界を取得する

ソース テキストと翻訳済みテキストの文の長さに関する情報を受信するには、クエリ文字列上で `includeSentenceLength=true` を指定します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

応答は次のとおりです。

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>動的ディクショナリを使用して翻訳する

単語や語句に適用する翻訳があらかじめわかっている場合は、それを要求内でマークアップとして指定することができます。 動的ディクショナリは、人名や製品名などの固有名詞に対してのみ安全に使用できます。

指定するマークアップでは、次の構文を使用します。

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

たとえば、"The word wordomatic is a dictionary entry." という英文を考えてみます。 単語 _wordomatic_ を翻訳内でそのまま使用するには、次の要求を送信します。

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

結果は次のとおりです。

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

この機能は、`textType=text` または `textType=html` の場合も同じように動作します。 この機能は慎重に使用する必要があります。 翻訳をカスタマイズするには、Custom Translator を使用するのが適切で望ましい方法です。 Custom Translator では、コンテキストおよび統計的確率を最大限に活用します。 コンテキスト内の単語または語句を表示するトレーニング データを作成する余裕がある場合、非常に良い結果が得られます。 [Custom Translator の詳細については、こちらを参照してください](../customization.md)。