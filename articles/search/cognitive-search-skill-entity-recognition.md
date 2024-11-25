---
title: エンティティ認識のコグニティブ スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のエンリッチメント パイプラインのテキストからさまざまな種類のエンティティを抽出します。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: 0ff55bf38e88bf62b483cf70df89a17b9c5afb8d
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210385"
---
<<<<<<< HEAD
#    <a name="entity-recognition-cognitive-skill"></a>エンティティ認識のコグニティブ スキル
=======
# <a name="entity-recognition-cognitive-skill"></a>エンティティ認識の認知スキル
>>>>>>> repo_sync_working_branch

**エンティティ認識** スキルによってテキストからさまざまな種類のエンティティが抽出されます。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

> [!IMPORTANT]
> エンティティの認識スキルは廃止となり、[Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) に置き換えられました。 「[非推奨の Cognitive Search スキル](cognitive-search-skill-deprecated.md)」に記載されている推奨事項に従い、サポートされているスキルに移行してください。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Cognitive Search のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格](https://azure.microsoft.com/pricing/details/search/)に関するページで説明されています。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>データ制限
レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データをキー フレーズ エクストラクターに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターでは大文字と小文字が区別されます。パラメーターはすべて任意です。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `categories`    | 抽出する必要があるカテゴリの配列。  可能なカテゴリの型は、`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"Datetime"`、`"URL"`、`"Email"` です。 カテゴリが指定されていない場合、すべての型が返されます。|
| `defaultLanguageCode` |    入力テキストの言語コード。 次の言語がサポートされます。`ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` すべての言語ですべてのエンティティ カテゴリがサポートされているわけではありません。以下の注意事項を参照してください。|
| `minimumPrecision` | 0 から 1 の値。 (`namedEntities` 出力の) 信頼度スコアがこの値よりも小さい場合は、エンティティは返されません。 既定値は 0 です。 |
| `includeTypelessEntities` | 現在のカテゴリに当てはまらない既知のエンティティを認識するには、`true` に設定します。 認識されたエンティティは、`entities` 複合出力フィールドに返されます。 たとえば、"Windows 10" は既知のエンティティ (製品) ですが、"製品" はサポートされているカテゴリではないため、このエンティティはエンティティ出力フィールドに含まれます。 既定値は `false` です |


## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| `languageCode`    | 省略可能。 既定値は `"en"` です。  |
| `text`          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

> [!NOTE]
> 言語によっては、一部のエンティティ カテゴリがサポートされていません。 `"Person"`、`"Location"`、および `"Organization"` エンティティ カテゴリの種類は、上記の言語のすべてでサポートされています。 `"Quantity"`、`"Datetime"`、`"URL"`、および `"Email"` の種類の抽出をサポートするのは、_de_、_en_、_es_、_fr_、および _zh-hans_ のみです。 詳細については、「[Text Analytics API の言語と地域のサポート](../cognitive-services/text-analytics/language-support.md)」を参照してください。  

| 出力名      | 説明                   |
|---------------|-------------------------------|
| `persons`       | 各文字列が人物の名前を表す文字列の配列。 |
| `locations`  | 各文字列が場所を表す文字列の配列。 |
| `organizations`  | 各文字列が組織を表す文字列の配列。 |
| `quantities`  | 各文字列が数量を表す文字列の配列。 |
| `dateTimes`  | 各文字列が DateTime (テキストに表示される) 値を表す文字列の配列。 |
| `urls` | 各文字列が URL を表す文字列の配列。 |
| `emails` | 各文字列が電子メールを表す文字列の配列。 |
| `namedEntities` | 次のフィールドが含まれる複合型の配列。 <ul><li>category</li> <li>value (実際のエンティティ名)</li><li>offset (テキスト内で見つかった場所)</li><li>confidence (値が高いほど、実際のエンティティに近づきます)</li></ul> |
| `entities` | 複合型の配列。テキストから抽出されたエンティティに関する豊富な情報と次のフィールドが含まれます。 <ul><li> name (実際のエンティティ名。 これは "正規化" フォームです)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (エンティティの Wikipedia ページのリンク)</li><li>bingId</li><li>type (認識されたエンティティのカテゴリ)</li><li>subType (特定のカテゴリのみで利用可能。エンティティ型がより詳しく表示されます)</li><li> matches (次を含む複合コレクション)<ul><li>text (エンティティの未加工テキスト)</li><li>offset (それが見つかった場所)</li><li>length (未加工エンティティ テキストの長さ)</li></ul></li></ul> |

##    <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

このスキルの出力のエンティティに対して返されるオフセットは、[Text Analytics API](../cognitive-services/text-analytics/overview.md) から直接返されることに注意してください。つまり、これらを使用して元の文字列にインデックスを作成する場合は、正しい内容を抽出するために .NET の [StringInfo](/dotnet/api/system.globalization.stringinfo) クラスを使用する必要があります。  [詳細については、こちらで確認できます。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>警告のケース
ドキュメントの言語コードがサポートされていない場合、警告が返され、エンティティは抽出されません。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
<<<<<<< HEAD
=======
+ [エンティティ認識スキル (V3)](cognitive-search-skill-entity-recognition-v3.md)
>>>>>>> repo_sync_working_branch
