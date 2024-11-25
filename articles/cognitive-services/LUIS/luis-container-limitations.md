---
title: コンテナーの制限 - LUIS
titleSuffix: Azure Cognitive Services
description: サポートされている LUIS コンテナー言語。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: cd29ef48f2d1042091a1379570e8ea710c718ff9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426224"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding (LUIS) コンテナーの制限

LUIS コンテナーには、いくつかの注目すべき制限があります。 この記事では、サポートされていない依存関係からサポートされている言語のサブセットまで、あらゆる制限について詳しく説明します。

## <a name="supported-dependencies-for-latest-container"></a>`latest` コンテナーでサポートされる依存関係

最新の LUIS コンテナーは次をサポートします。

* [新しい事前構築済みドメイン](luis-reference-prebuilt-domains.md): 企業向けのこれらのドメインには、エンティティ、発話の例、およびパターンが含まれます。 これらのドメインを独自の用途で拡張します。

## <a name="unsupported-dependencies-for-latest-container"></a>`latest` コンテナーでサポートされない依存関係

[[Export for container]\(コンテナー用にエクスポート\)](luis-container-howto.md#export-packaged-app-from-luis) するには、LUIS アプリからサポートされていない依存関係を削除する必要があります。 コンテナー用にエクスポートしようとすると、LUIS ポータルで、削除する必要があるサポートされていない機能が報告されます。

LUIS アプリケーションは、次の依存関係を一切 **含んでいない** 場合に使用できます。

サポートされていないアプリ構成|詳細|
|--|--|
|サポートされていないコンテナー カルチャ| オランダ語 (`nl-NL`)、日本語 (`ja-JP`)、ドイツ語 (`de-DE`) は [1.0.2 トークナイザー](luis-language-support.md#custom-tokenizer-versions)でのみサポートされています。|
|サポートされていないエンティティ (全カルチャ)|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) 事前構築済みエンティティ (全カルチャ)|
|サポートされていないエンティティ (英語 (`en-US`) カルチャ)|[GeographyV2](luis-reference-prebuilt-geographyV2.md) 事前構築済みエンティティ|
|音声認識の準備|コンテナーでは、外部依存関係がサポートされません。|
|センチメント分析|コンテナーでは、外部依存関係がサポートされません。|
|Bing Spell Check|コンテナーでは、外部依存関係がサポートされません。|

## <a name="languages-supported"></a>サポートされている言語

LUIS コンテナーは、LUIS によって適切に[サポートされる言語](luis-language-support.md#languages-supported)のサブセットをサポートしています。 LUIS コンテナーは、次の言語の発話を理解することができます。

| Language | Locale | 事前構築済みのドメイン | 事前構築済みのエンティティ | フレーズ リストのレコメンデーション | **[感情分析](../language-service/sentiment-opinion-mining/language-support.md)と[キー フレーズ抽出](../language-service/key-phrase-extraction/language-support.md)|
|--|--|:--:|:--:|:--:|:--:|
| 英語 (米国) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| アラビア語 (プレビュー - 現代標準アラビア語) |`ar-AR`|❌|❌|❌|❌|
| *[中国語](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| フランス語 (フランス) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| フランス語 (カナダ) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| ドイツ語 |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| ヒンディー語 | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| イタリア語 |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| 韓国語 |`ko-KR` | ✔️ | ❌ | ❌ | *キー フレーズ* のみ |
| マラーティー語 | `mr-IN`|❌|❌|❌|❌|
| ポルトガル語 (ブラジル) |`pt-BR` | ✔️ | ✔️ | ✔️ | 一部のサブカルチャのみ |
| スペイン語 (スペイン) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| スペイン語 (メキシコ)|`es-MX` | ❌ | ❌ |✔️|✔️|
| タミル語 | `ta-IN`|❌|❌|❌|❌|
| テルグ語 | `te-IN`|❌|❌|❌|❌|
| トルコ語 | `tr-TR` |✔️| ❌ | ❌ | *センチメント* のみ |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Language service support notes](includes/text-analytics-support-notes.md)]
