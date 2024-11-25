---
title: 言語アナライザーを文字列フィールドに追加する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search での英語以外のクエリおよびインデックスの多言語字句解析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 082ece269fa0e07419ff44c736fdeb19aaf30bdc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735335"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Cognitive Search インデックスの文字列フィールドに言語アナライザーを追加する

"*言語アナライザー*" は、[テキスト アナライザー](search-analyzers.md)の固有の種類であり、対象言語の言語規則を使用して字句解析を実行します。 すべての検索可能文字列フィールドには、**analyzer** プロパティがあります。 翻訳された文字列でコンテンツが構成されている場合 (英語と中国語のテキストが別のフィールドの場合など)、各フィールドで言語アナライザーを指定して、これらのアナライザーの豊富な言語機能にアクセスできます。

## <a name="when-to-use-a-language-analyzer"></a>言語アナライザーを使用する場合

単語または文の構造を認識することでテキスト解析の価値が高まる場合は、言語アナライザーを検討してください。 一般的な例としては、不規則な動詞の形 ("bring" と "brought") や複数名詞 ("mice" と mouse") の関連付けなどがあります。 言語認識なしでは、これらの文字列は物理的な特性だけで解析され、つながりを見つけることができません。 テキストの大部分にはこのコンテンツが含まれる可能性が高いため、説明、レビュー、または要約で構成されるフィールドは、言語アナライザーの候補として適しています。

また、コンテンツが西洋以外の言語の文字列で構成されている場合も、言語アナライザーを検討してください。 [既定のアナライザー (Standard Lucene)](search-analyzers.md#default-analyzer) は言語に依存しませんが、スペースと特殊文字 (ハイフンとスラッシュ) を使用して文字列を区切るという概念は、西洋以外の言語よりも西洋言語に当てはまります。 

たとえば、中国語、日本語、韓国語 (CJK)、およびその他のアジア言語では、スペースは必ずしも単語の区切り記号ではありません。 次の日本語の文字列を考えてみます。 これにはスペースがないため、文字列は実際には句であっても、言語に依存しないアナライザーでは文字列全体が 1 つのトークンとして分析される可能性があります。

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

上記の例では、クエリを正常に実行するには、完全なトークン、またはサフィックス ワイルドカードを使用する部分トークンを含める必要があります。これにより、自然ではない制限的な検索エクスペリエンスが実現します。

エクスペリエンスを向上させるには、個々の単語を検索することです: 明るい (Bright)、私たちの (Our)、銀河系 (Galaxy)。 Cognitive Search で利用できる日本語アナライザーのいずれかを使用すると、この動作が解除される可能性が高くなります。これらのアナライザーの方が、テキストのチャンクをターゲット言語で意味のある単語に分割することにおいて、より適した機能を備えているためです。

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Lucene と Microsoft のアナライザーの比較

Azure Cognitive Search では、Lucene によって提供される 35 個の言語アナライザーと、Office および Bing で使用される Microsoft 独自の自然言語処理技術によって提供される 50 個の言語アナライザーがサポートされています。

開発者によっては、使い慣れた簡単なオープン ソース ソリューションである Lucene を好む場合があります。 Lucene の言語アナライザーは高速です。しかし、マイクロソフトのアナライザーには高度な機能があります。たとえば、レンマ化、単語複混合化 (ドイツ語、デンマーク語、オランダ語、スウェーデン語、ノルウェー語、エストニア語、フィンランド語、ハンガリー語、スロバキア語などの言語で)、エンティティ認識 (URL、電子メール、日付、数値) などです。 可能であれば、マイクロソフトと Lucene の両方のアナライザーを比較し、より適したものを選んでください。 [Analyze API](/rest/api/searchservice/test-analyzer) を使用すると、特定のアナライザーを使用して、指定のテキストから生成されたトークンを表示できます。

言語によっては、マイクロソフトのアナライザーでのインデックス作成には、平均して、Lucene の同等機能の 2 ～ 3 倍の時間がかかります。 平均的なサイズのクエリの場合、検索のパフォーマンスはさほど影響はありません。 

### <a name="english-analyzers"></a>英語のアナライザー

既定のアナライザーは標準 Lucene であり、英語でも問題なく動作しますが、おそらく Lucene の英語アナライザーや Microsoft の英語アナライザーほどではありません。

+ Lucene の英語アナライザーでは、標準アナライザーが拡張されています。 単語から所有格 (末尾の 's) が除去され、Porter Stemming アルゴリズムに従ってステミングが適用され、英語のストップワードが除去されます。  

+ Microsoft の英語アナライザーでは、ステミングではなくレンマ化が実行されます。 つまり、語形変化および変則的な語形がより適切に処理され、関連性の高い検索結果が得られます 

## <a name="how-to-specify-a-language-analyzer"></a>言語アナライザーを指定する方法

データを読み込む前に、インデックスの作成時にアナライザーを設定します。

1. フィールド定義で、フィールドが "検索可能" として属性付けされ、Edm.String 型になっていることを確認します。

1. "analyzer" プロパティを、[サポートされるアナライザーの一覧](#language-analyzer-list)にある言語アナライザーのいずれかに設定します。

   "analyzer" プロパティは、言語アナライザーを受け入れる唯一のプロパティであり、インデックス作成とクエリの両方に使用されます。 その他のアナライザー関連のプロパティ ("searchAnalyzer" と "indexAnalyzer") では、言語アナライザーは受け入れられません。

言語アナライザーはカスタマイズできません。 アナライザーが要件を満たしていない場合は、microsoft_language_tokenizer または microsoft_language_stemming_tokenizer を使用して[カスタム アナライザー](cognitive-search-working-with-skillsets.md)の作成を試みて、トークン化前と後の処理用のフィルターを追加することができます。

次の例は、インデックス内の言語アナライザーの仕様を示しています。

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

インデックスの作成とフィールド プロパティの設定の詳細については、[Create Index (REST)](/rest/api/searchservice/create-index) に関するページを参照してください。 テキストの解析について詳しくは、[Azure Cognitive Search でのアナライザー](search-analyzers.md)に関する記事をご覧ください。

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>サポートされる言語アナライザー

 サポートされている言語と、Lucene およびマイクロソフトのアナライザーの名前を以下に一覧します。  

| Language | Microsoft のアナライザーの名前 | Lucene のアナライザーの名前 |
|----------|-------------------------|----------------------|
| アラビア語   | ar.microsoft | ar.lucene |
| アルメニア語 |           | hy.lucene |
| ベンガル語   | bn.microsoft |  |
| バスク語   |  | eu.lucene |
| ブルガリア語 | bg.microsoft | bg.lucene |
| カタロニア語  | ca.microsoft | ca.lucene |
| 簡体中国語 | zh-Hans.microsoft | zh-Hans.lucene |
| 中国語 (繁体字) | zh-Hant.microsoft | zh-Hant.lucene |
| クロアチア語 | hr.microsoft |  |
| チェコ語 | cs.microsoft | cs.lucene |
| デンマーク語 | da.microsoft | da.lucene |
| オランダ語 | nl.microsoft | nl.lucene |
| 英語 | en.microsoft | en.lucene |
| エストニア語 | et.microsoft |  |
| フィンランド語 | fi.microsoft | fi.lucene |
| フランス語 | fr.microsoft | fr.lucene |
| ガリシア語 |  | gl.lucene |
| ドイツ語 | de.microsoft | de.lucene |
| ギリシャ語 | el.microsoft | el.lucene |
| グジャラート語 | gu.microsoft |  |
| ヘブライ語 | he.microsoft |  |
| ヒンディー語 | hi.microsoft | hi.lucene |
| ハンガリー語 | hu.microsoft | hu.lucene |
| アイスランド語 | is.microsoft |  |
| インドネシア語 | id.microsoft | id.lucene |
| アイルランド語 |  | ga.lucene |
| イタリア語 | it.microsoft | it.lucene |
| 日本語 | ja.microsoft | ja.lucene |
| カンナダ語 | kn.microsoft |  |
| 韓国語 | ko.microsoft | ko.lucene |
| ラトビア語 | lv.microsoft | lv.lucene |
| リトアニア語 | lt.microsoft |  |
| マラヤーラム語 | ml.microsoft |  |
| マレー語 (ラテン) | ms.microsoft |  |
| マラーティー語 | mr.microsoft |  |
| ノルウェー語 | nb.microsoft | no.lucene |
| ペルシャ語 |  | fa.lucene |
| ポーランド語 | pl.microsoft | pl.lucene |
| ポルトガル語 (ブラジル) | pt-Br.microsoft | pt-Br.lucene |
| ポルトガル語 (ポルトガル) | pt-Pt.microsoft | pt-Pt.lucene |
| パンジャーブ語 | pa.microsoft |  |
| ルーマニア語 | ro.microsoft | ro.lucene |
| ロシア語 | ru.microsoft | ru.lucene |
| セルビア語 (キリル) | sr-cyrillic.microsoft |  |
| セルビア語 (ラテン) | sr-latin.microsoft |  |
| スロバキア語 | sk.microsoft |  |
| スロベニア語 | sl.microsoft |  |
| スペイン語 | es.microsoft | es.lucene |
| スウェーデン語 | sv.microsoft | sv.lucene |
| タミル語 | ta.microsoft |  |
| テルグ語 | te.microsoft |  |
| タイ語 | th.microsoft | th.lucene |
| トルコ語 | tr.microsoft | tr.lucene |
| ウクライナ語 | uk.microsoft |  |
| ウルドゥ語 | ur.microsoft |  |
| ベトナム語 | vi.microsoft |  |

 名前に **Lucene** が含まれるすべてのアナライザーでは、[Apache Lucene の言語アナライザー](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )が利用されています。

## <a name="see-also"></a>関連項目  

+ [インデックスを作成する](search-what-is-an-index.md)
+ [複数言語インデックスの作成](search-language-support.md)
+ [Create Index (REST API)](/rest/api/searchservice/create-index)  
+ [LexicalAnalyzerName クラス (Azure SDK for .NET)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)
