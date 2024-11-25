---
title: Azure Video Analyzer for Media (旧称 Video Indexer) API でブランド モデルをカスタマイズする
titleSuffix: Azure Media Services
description: Azure Video Analyzer for Media (旧称 Video Indexer) API を使用してブランド モデルをカスタマイズする方法について説明します。
services: media-services
author: anikaz
manager: johndeu
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 3f3e2356a9c9101763f9b944993b9a96b22b8a0e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386693"
---
# <a name="customize-a-brands-model-with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API を使用してブランド モデルをカスタマイズする

Azure Video Analyzer for Media (旧 Video Indexer) では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば、Microsoft がビデオまたはオーディオ コンテンツで言及されている場合や、ビデオ内の視覚テキストに表示される場合、Video Analyzer for Media では Microsoft がそのコンテンツ内のブランドとして検出されます。 カスタム ブランド モデルでは、特定のブランドを検出対象から除外し、Bing のブランド データベースにはない可能性があるモデルの一部である必要があるブランドを含めることができます。 詳細については、[概要](customize-brands-model-overview.md)に関するページを参照してください。

> [!NOTE]
> ブランド追加に先立ってビデオにインデックスを付けた場合、インデックスを再作成する必要があります。

このトピックで説明されているように、Video Analyzer for Media API を使用して、ビデオで検出されるカスタム ブランド モデルを作成、使用、および編集することができます。 また、[Video Analyzer for Media Web サイトを使用したブランド モデルのカスタマイズ](customize-brands-model-with-api.md)に関するページで説明しているように、Video Analyzer for Media Web サイトを使用することもできます。

## <a name="create-a-brand"></a>ブランドを作成する

[ブランドの作成](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Brand) API では、新しいカスタム ブランドを作成し、指定されたアカウント用のカスタム ブランド モデルに追加します。

> [!NOTE]
> `enabled` (本文内) を true に設定すると、Video Analyzer for Media での検出対象として "*対象*" リストにブランドが配置されます。 `enabled` を false に設定すると、"*除外*" リストにブランドが配置されます。したがって、Video Analyzer for Media では検出されません。

本文で設定できるその他のパラメーターは次のとおりです。

* `referenceUrl` 値は、Wikipedia ページへのリンクなど、ブランドの参照 Web サイトにすることができます。
* `tags` 値はブランドのタグ リストです。 このタグは、Video Analyzer for Media Web サイトのブランドの *[カテゴリ]* フィールドに表示されます。 たとえば、ブランドが "Azure" の場合、"クラウド" としてタグを付けたり、分類したりすることができます。

### <a name="response"></a>Response

応答では、次の例の形式に従って、先ほど作成したブランドに関する情報が提供されます。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>ブランドを削除する

[ブランドの削除](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Brand) API では、指定されたアカウントのカスタム ブランド モデルからブランドを削除します。 アカウントは `accountId` パラメーターで指定されます。 呼び出しが正常に行われると、ブランドは、*[含める]* や *[除外]* のブランド リストに配置されなくなります。

### <a name="response"></a>Response

ブランドが正常に削除されたときに返されるコンテンツはありません。

## <a name="get-a-specific-brand"></a>特定のブランドを取得する

[ブランドの取得](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brand) API では、ブランド ID を使用して、指定されたアカウント用のカスタム ブランド モデルで、ブランドの詳細を検索できます。

### <a name="response"></a>Response

応答では、次の例の形式に従って、(ブランド ID を使用して) 検索したブランドに関する情報が提供されます。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled` が `true` に設定されている場合、これはブランドが Video Analyzer for Media での検出対象として "*含める*" リストにあることを意味します。`enabled` が false の場合、ブランドが "*除外*" リストにあることを意味するため、Video Analyzer for Media では検出されません。

## <a name="update-a-specific-brand"></a>特定のブランドを更新する

[ブランドの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brand) API では、ブランド ID を使用して、指定されたアカウント用のカスタム ブランド モデルで、ブランドの詳細を検索できます。

### <a name="response"></a>Response

応答では、次の例の形式に従って、更新したブランドに関する更新された情報が提供されます。

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>すべてのブランドを取得する

[すべてのブランドの取得](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API では、ブランドが "*対象*" と "*除外*" のどちらのブランド リストに配置されるようになっているかに関係なく、指定されたアカウント用のカスタム ブランド モデルのブランドがすべて返されます。

### <a name="response"></a>Response

応答では、以下の例の形式に従って、アカウント内のすべてのブランドとそれらの詳細がそれぞれリストされます。

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> *Example* という名前のブランドは、Video Analyzer for Media での検出対象として "*含める*" リストにあります。*Example2* という名前のブランドは、"*除外*" リストにあるため、Video Analyzer for Media では検出されません。

## <a name="get-brands-model-settings"></a>ブランド モデルの設定を取得する

[ブランドの設定の取得](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API では、指定されたアカウントのブランド モデル設定が返されます。 モデル ブランド設定は、Bing ブランド データベースからの検出が有効であるかどうかを表します。 Bing ブランドが有効でない場合、Video Analyzer for Media では指定されたアカウントのカスタム ブランド モデルからのみブランドが検出されます。

### <a name="response"></a>Response

応答では、次の例の形式に従って、Bing ブランドが有効かどうかが示されます。

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` が true に設定されている場合、Bing ブランドが有効であることを表します。 `useBuiltin` が false の場合、Bing ブランドは無効です。 `state` 値は、推奨されていないため、無視してかまいません。

## <a name="update-brands-model-settings"></a>ブランド モデルの設定を更新する

[ブランドの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brands-Model-Settings) API では、指定されたアカウントのブランド モデル設定が更新されます。 モデル ブランド設定は、Bing ブランド データベースからの検出が有効であるかどうかを表します。 Bing ブランドが有効でない場合、Video Analyzer for Media では指定されたアカウントのカスタム ブランド モデルからのみブランドが検出されます。

`useBuiltIn` が true に設定されている場合、これは Bing ブランドが有効であることを意味します。 `useBuiltin` が false の場合、Bing ブランドは無効です。

### <a name="response"></a>Response

ブランド モデル設定が正常に更新されたときに返されるコンテンツはありません。

## <a name="next-steps"></a>次のステップ

[Web サイトを使用してブランド モデルをカスタマイズする](customize-brands-model-with-website.md)
