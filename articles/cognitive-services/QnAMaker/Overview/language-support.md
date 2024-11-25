---
title: 言語サポート - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースでサポートされるカルチャと自然言語の一覧。 同じナレッジ ベースに複数の言語を混在させないでください。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.custom: ignite-fall-2021
ms.openlocfilehash: 36f7c08cec7375d8b8a78519752c572357e6525a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073531"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker のリソースとナレッジ ベースに対する言語のサポート

この記事では、QnA Maker リソースおよびナレッジ ベースに対する言語サポート オプションについて説明します。 

[!INCLUDE [Custom question answering](../includes/new-version.md)]

サービスの言語は、リソースに最初のナレッジ ベースを作成するときに選択します。 そのリソースに追加するすべてのナレッジ ベースは、同じ言語である必要があります。 

言語により、ユーザー クエリに対する応答で QnA Maker によって提供される結果の関連性が決まります。 QnA Maker リソース、およびそのリソース内のすべてのナレッジ ベースは、1 つの言語をサポートします。 クエリに対して最適な回答結果を提供するために、1 つの言語が必要です。

## <a name="single-language-per-resource"></a>リソースごとに 1 つの言語

以下、具体例に沿って説明します。

* QnA Maker サービスとそのすべてのナレッジ ベースは、1 つの言語のみをサポートします。
* 言語は、サービスの最初のナレッジ ベースが作成されるときに明示的に設定されます。
* 言語は、ナレッジ ベースの作成時に追加されたファイルと URL から決定されます
* サービス内の他のナレッジ ベースの言語は変更できません
* この言語は Cognitive Search Service (ランカー #1) と QnA Maker サービス (ランカー #2) に使用され、クエリに対して最適な回答が生成されます。

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>1 つの QnA Maker リソースで複数の言語をサポートする

この機能は、現在の一般提供 (GA) の安定版リリースではサポートされていません。 この機能をテストするには、QnA Maker マネージドをチェックアウトしてください。 

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>1 つのナレッジ ベースで複数の言語をサポートする

複数の言語を含むナレッジ ベース システムをサポートする必要がある場合は、次を行うことができます。

* ナレッジ ベースに質問を送信する前に、[Translator サービス](../../translator/translator-info-overview.md)を使用して、質問を 1 つの言語に翻訳します。 これで、1 つの言語の品質と、代替の質問と回答の品質に集中できるようになります。
* すべての言語について、QnA Maker リソースとそのリソース内のナレッジ ベースを作成します。 これで、言語ごとに、より微妙な個別の代替の質問と回答のテキストを管理できるようになります。 これにより、柔軟性が大幅に向上しますが、すべての言語で質問または回答が変わると、メンテナンス コストが大幅に増加します。


## <a name="languages-supported"></a>サポートされている言語

次の一覧では、QnA Maker のリソースに対してサポートされている言語を示します。 

| Language |
|--|
| アラビア語 |
| アルメニア語 |
| ベンガル語 |
| バスク語 |
| ブルガリア語 |
| カタロニア語 |
| 簡体中国語 |
| 繁体字中国語 |
| クロアチア語 |
| チェコ語 |
| デンマーク語 |
| オランダ語 |
| 英語 |
| エストニア語 |
| フィンランド語 |
| フランス語 |
| ガリシア語 |
| ドイツ語 |
| ギリシャ語 |
| グジャラート語 |
| ヘブライ語 |
| ヒンディー語 |
| ハンガリー語 |
| アイスランド語 |
| インドネシア語 |
| アイルランド語 |
| イタリア語 |
| 日本語 |
| カンナダ語 |
| 韓国語 |
| ラトビア語 |
| リトアニア語 |
| マラヤーラム語 |
| マレー語 |
| ノルウェー語 |
| ポーランド語 |
| Portuguese |
| パンジャーブ語 |
| ルーマニア語 |
| ロシア語 |
| セルビア語 (キリル) |
| セルビア語 (ラテン) |
| スロバキア語 |
| スロベニア語 |
| スペイン語 |
| スウェーデン語 |
| タミル語 |
| テルグ語 |
| タイ語 |
| トルコ語 |
| ウクライナ語 |
| ウルドゥ語 |
| ベトナム語 |

## <a name="query-matching-and-relevance"></a>クエリの一致と関連性
QnA Maker による結果の提供は、[Azure Cognitive Search の言語アナライザー](/rest/api/searchservice/language-support)に依存してします。

Azure Cognitive Search の機能はサポートされている言語に従いますが、QnA Maker には Azure Search の結果より上位に追加のランカーがあります。 このランカー モデルでは、以下の言語においていくつかの特別なセマンティックとワード ベースの機能が使用されます。

|追加のランカーがある言語|
|--|
|中国語|
|チェコ語|
|オランダ語|
|英語|
|フランス語|
|ドイツ語|
|ハンガリー語|
|イタリア語|
|日本語|
|韓国語|
|ポーランド語|
|Portuguese|
|スペイン語|
|スウェーデン語|

この追加のランキングは、QnA Maker のランカーの内部処理です。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [言語の選択](../index.yml)
