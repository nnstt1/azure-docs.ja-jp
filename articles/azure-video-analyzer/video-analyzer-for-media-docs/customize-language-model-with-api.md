---
title: Azure Video Analyzer for Media (旧称 Video Indexer) API を使用して言語モデルをカスタマイズする
titlesuffix: Azure Video Analyzer for Media
description: Azure Video Analyzer for Media (旧称 Video Indexer) API を使用して言語モデルをカスタマイズする方法について説明します。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: b3239e9af0a8ba3218d511f872dcbd117295add0
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123235"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API を使用して言語モデルをカスタマイズする

Azure Video Analyzer for Media (旧称 Video Indexer) では、カスタム言語モデルを作成し、適応テキストをアップロードすることで音声認識をカスタマイズすることができます。適応テキストとは、エンジンを適応させたいボキャブラリがあるドメインから取得したテキストのことです。 モデルをトレーニングすると、適応テキスト内に含まれる新しい単語が認識されるようになります。

カスタム言語モデルの詳細な概要とベスト プラクティスについては、「[Video Analyzer for Media を使用して言語モデルをカスタマイズする](customize-language-model-overview.md)」を参照してください。

このトピックで説明しているように、Video Analyzer for Media API を使用すると、アカウント内にカスタム言語モデルを作成し、編集することができます。 また、[Video Analyzer for Media Web サイトを使用した言語モデルのカスタマイズ](customize-language-model-with-api.md)に関するページで説明しているように、Web サイトを使用することもできます。

## <a name="create-a-language-model"></a>言語モデルの作成

[言語モデルの作成](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) API により、指定されたアカウントに新しいカスタム言語モデルが作成されます。 この呼び出しで、言語モデル用のファイルをアップロードすることができます。 なお、ここでは言語モデルの作成だけを行い、後で言語モデルを更新することで、モデル用のファイルをアップロードすることもできます。

> [!NOTE]
> いずれの場合も、モデル用の対応ファイルでモデルをトレーニングし、ファイルのコンテンツを学習させる必要があります。 言語のトレーニング方法については、次のセクションで説明します。

言語モデルに追加するファイルをアップロードするには、上記の必須パラメーターの値を指定するだけでなく、FormData を使用して本文内のファイルをアップロードする必要があります。 このタスクを行うには 2 つの方法があります。

* キーをファイル名にし、値を txt ファイルにします。
* キーをファイル名にし、値を txt ファイルの URL にします。

### <a name="response"></a>Response

応答では、新しく作成された言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>言語モデルのトレーニング

[言語モデルのトレーニング](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Language-Model) API により、言語モデルにアップロードされ、その言語モデル内で有効化されたファイルのコンテンツを使用して、指定されたアカウント内のカスタム言語モデルがトレーニングされます。

> [!NOTE]
> 最初に、言語モデルを作成し、そのファイルをアップロードする必要があります。 ファイルのアップロードは、言語モデルの作成時に行うか、言語モデルを更新して実行することができます。

### <a name="response"></a>Response

応答では、新しくトレーニングされた言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

返された `id` は、言語モデルを区別するために使用される一意の ID です。一方、`languageModelId` は、[インデックスを作成するビデオのアップロード](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API と、[ビデオのインデックスの再作成](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API の両方に使用されます (Video Analyzer for Media のアップロードまたはインデックスの再作成 API では `linguisticModelId` とも呼ばれます)。

## <a name="delete-a-language-model"></a>言語モデルの削除

[言語モデルの削除](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model) API により、指定されたアカウントからカスタム言語モデルが削除されます。 削除された言語モデルを使用していたすべてのビデオでは、ユーザーがビデオのインデックスを再作成するまで同じインデックスが保持されます。 ビデオのインデックスを再作成すると、ビデオに新しい言語モデルを割り当てることができます。 それ以外の場合、Video Analyzer for Media では既定のモデルを使用して、ビデオのインデックスが再作成されます。

### <a name="response"></a>Response

言語モデルが正常に削除されたときに返されるコンテンツはありません。

## <a name="update-a-language-model"></a>言語モデルの更新

[言語モデルの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API により、指定されたアカウントのカスタム言語モデルが更新されます。

> [!NOTE]
> あらかじめ言語モデルを作成しておく必要があります。 この呼び出しは、モデルの配下にあるすべてのファイルを有効または無効にしたり、言語モデルの名前を更新したり、言語モデルに追加するファイルをアップロードしたりするために使用できます。

言語モデルに追加するファイルをアップロードするには、上記の必須パラメーターの値を指定するだけでなく、FormData を使用して本文内のファイルをアップロードする必要があります。 このタスクを行うには 2 つの方法があります。

* キーをファイル名にし、値を txt ファイルにします。
* キーをファイル名にし、値を txt ファイルの URL にします。

### <a name="response"></a>Response

応答では、新しくトレーニングされた言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

応答で返されたファイルの `id` を使用して、ファイルのコンテンツをダウンロードします。

## <a name="update-a-file-from-a-language-model"></a>言語モデル内のファイルを更新する

[ファイルの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model-file)を使用すると、指定されたアカウント内のカスタム言語モデルに含まれるファイルの名前を更新したり、状態を `enable` にしたりすることができます。

### <a name="response"></a>Response

応答では、更新したファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

応答で返されたファイルの `id` を使用して、ファイルのコンテンツをダウンロードします。

## <a name="get-a-specific-language-model"></a>特定の言語モデルを取得する

[取得](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model) API を使用すると、指定されたアカウント内の指定された言語モデルに関する情報が返されます (言語や、言語モデルに含まれるファイルなど)。

### <a name="response"></a>Response

応答では、指定された言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

応答で返されたファイルの `id` を使用して、ファイルのコンテンツをダウンロードします。

## <a name="get-all-the-language-models"></a>すべての言語モデルを取得する

[すべて取得](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Models) API を使用すると、指定されたアカウント内のすべてのカスタム言語モデルが一覧で返されます。

### <a name="response"></a>Response

応答では、アカウント内のすべての言語モデルと、各言語モデルのメタデータとファイルが、次の JSON 出力サンプルの形式で一覧表示されます。

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>言語モデルからファイルを削除する

[削除](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model-File) API を使用すると、指定されたアカウント内の指定された言語モデルから、指定されたファイルが削除されます。

### <a name="response"></a>Response

言語モデルからファイルが正常に削除された場合、返されるコンテンツはありません。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>言語モデルからファイルのメタデータを取得する

[ファイルのメタデータを取得](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model-File-Data) API を使用すると、アカウント内の選択された言語モデルから、指定されたファイルのコンテンツとメタデータが返されます。

### <a name="response"></a>Response

応答では、ファイルのコンテンツとメタデータが、次の例のような JSON 形式で返されます。

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> このサンプル ファイルのコンテンツは、2 つの行に分けられた、"hello" と "world" という単語です。

## <a name="download-a-file-from-a-language-model"></a>言語モデルからファイルをダウンロードする

[ファイルのダウンロード](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Download-Language-Model-File-Content) API を使用すると、指定されたアカウント内の指定された言語モデルから、指定されたファイルのコンテンツを含んだテキスト ファイルがダウンロードされます。 このテキスト ファイルは、最初にアップロードされたテキスト ファイルのコンテンツと一致します。

### <a name="response"></a>Response

応答では、ファイルのコンテンツを含んだテキスト ファイルが JSON 形式でダウンロードされます。

## <a name="next-steps"></a>次のステップ

[Web サイトを使用して言語モデルをカスタマイズする](customize-language-model-with-website.md)
