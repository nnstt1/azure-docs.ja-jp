---
title: 非同期での会話の文字起こし - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービスを使って非同期での会話の文字起こしを使用する方法について説明します。 Java と C# でのみ使用できます。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 9dbee96d6b277ac76b07e3fd8b2b72b1c84b32f9
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546407"
---
# <a name="asynchronous-conversation-transcription"></a>非同期での会話の文字起こし

この記事では、**RemoteConversationTranscriptionClient** API を使って、非同期での会話の文字起こしを行う方法を示します。 非同期で文字起こしを行うように会話の文字起こしを構成してあり、`conversationId` がある場合は、**RemoteConversationTranscriptionClient** API を使って、その `conversationId` に関連付けられている文字起こしを取得できます。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同期またはリアルタイムと非同期

非同期での文字起こしでは、会話のオーディオをストリーミングしますが、文字起こしをリアルタイムで受け取る必要はありません。 代わりに、オーディオを送信した後、`Conversation` の `conversationId` を使って、非同期での文字起こしの状態を照会します。 非同期での文字起こしの準備が完了していると、`RemoteConversationTranscriptionResult` を受け取ります。

リアルタイムと非同期では、リアルタイムで文字起こしを受け取りますが、`conversationId` で照会して文字起こしを取得することもできます (非同期のシナリオと同様)。

非同期での文字起こしを実行するには、2 つのステップが必要です。 最初のステップでは、非同期のみまたはリアルタイムと非同期のどちらかを選択して、オーディオをアップロードします。 2 番目のステップでは、文字起こしの結果を取得します。

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
