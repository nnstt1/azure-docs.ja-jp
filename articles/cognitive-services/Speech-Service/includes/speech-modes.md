---
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: eur
ms.openlocfilehash: 9481d0db9f21b90b38dd89cba028b5fd1059e03f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508169"
---
**対話**
- コマンドと制御のシナリオ向けです。
- セグメント化のタイムアウト値は X になっています。
- 認識された 1 回の発話の最後に、その要求 ID からの音声の処理が停止され、順番が終わります。 接続は終了しません。
- 認識の最大限度は 20s です。
- 一般的なカーボン呼び出しは `RecognizeOnceAsync` です。

**会話**
- 認識を長時間実行することを意図しています。
- セグメント化のタイムアウト値は Y になっています。(Y != X)
- 順番を終了せずに、複数の完全な発話を処理します。
- 無音状態が過剰になると、順番が終了します。
- カーボンは、新しい要求 ID で続行し、必要に応じて音声を再生します。
- このサービスは、音声認識の 10 分後、強制的に切断されます。
- カーボンは再接続して未確認の音声を再生します。
- `StartContinuousRecognition` でカーボンで呼び出されます。

**ディクテーション**
- ユーザーが発音して句読点を指定できるようにします。
- 認識を開始する API 呼び出しに関係なく、`SpeechConfig` オブジェクトで `EnableDictation` を指定することによって、カーボンで呼び出されます。
- 最初<sup></sup>のパーティー クラスターは、中間結果の `speech.fragment` メッセージを返し、3 番目<sup></sup>のパーティーは `speech.hypothesis` メッセージを返します。
