---
title: Windows の音声アシスタント - はじめに
titleSuffix: Azure Cognitive Services
description: サンプル コード クイックスタートの参照など、Windows 音声エージェントの開発を開始する手順。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 72484bad390f642dae1c5ce5f94c303c55da616f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503963"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows の音声アシスタントの概要

このガイドでは、Windows 上で音声アシスタントの開発を開始する手順について説明します。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

Windows 用の音声アシスタントの開発を開始するには、適切な開発環境があることを確認する必要があります。

- **Visual Studio:** [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/) Community Edition 以上をインストールする必要があります
- **Windows のバージョン**: Windows の Windows Insider ファスト リング ビルドと Windows SDK の Windows Insider バージョンが搭載された PC。  このサンプル コードは、Windows SDK 19018 を使用する Windows Insider リリース ビルド 19025.vb_release_analog.191112-1600 で動作することが確認されています。  この特定のバージョンより後のビルドまたは SDK と互換性があります。
- **UWP 開発ツール**: Visual Studio のユニバーサル Windows プラットフォーム開発ワークロード。  UWP アプリケーションを開発するためのマシンの準備については、UWP の「[準備](/windows/uwp/get-started/get-set-up)」ページを参照してください。
- **動作するマイクとオーディオ出力**

## <a name="obtain-resources-from-microsoft"></a>Microsoft からリソースを入手する

Windows 上で完全にカスタマイズされた音声エージェントに必要なリソースには、Microsoft のリソースが必要なものがあります。 [UWP 音声アシスタント サンプル](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)には、初期の開発とテストのためにこうしたリソースのサンプル バージョンが用意されているため、初期の開発の場合はこのセクションは不要です。

- **キーワード モデル:** 音声によるアクティブ化には、.bin ファイル形式の Microsoft のキーワード モデルが必要です。 UWP 音声アシスタント サンプルで用意されている .bin ファイルは、キーワード *Contoso* でトレーニングされています。
- **制限付きアクセス機能トークン:** ConversationalAgent API はマイク オーディオへのアクセスを提供するため、制限付きアクセス機能の制限の下で保護されています。  制限付きアクセス機能を使用するには、アプリケーションのパッケージ ID に接続された制限付きアクセス機能トークンを Microsoft から入手する必要があります。

## <a name="establish-a-dialog-service"></a>ダイアログ サービスを確立する

アプリケーションで完全な音声アシスタント エクスペリエンスを実現するには、次のようなダイアログ サービスが必要です。

- 特定のオーディオ ファイル内のキーワードを検出する
- ユーザー入力をリッスンしてテキストに変換する
- ボットにテキストを提供する
- ボットのテキスト応答をオーディオ出力に変換する

これらは、Direct Line Speech を使用して基本的なダイアログ サービスを作成するための要件です。

- **Speech Services のサブスクリプション:** 音声からテキストへの変換とテキストから音声への変換のための Cognitive Speech Services のサブスクリプション。 Speech Services は、[こちら](./overview.md#try-the-speech-service-for-free)から無料で試すことができます。
- **Bot Framework ボット:** Bot Framework バージョン 4.2 以降を使用して作成されたボットで、[Direct Line Speech](./direct-line-speech.md) にサブスクライブして音声の入出力を有効にします。 [このガイド](./tutorial-voice-enable-your-bot-speech-sdk.md)には、"エコー ボット" を作成し、Direct Line Speech にサブスクライブするための段階的な手順が説明されています。 また、カスタマイズされたボットを作成する手順については、[こちら](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/)を参照してください。そうすると、[こちら](./tutorial-voice-enable-your-bot-speech-sdk.md)と同じ手順に従って、"エコー ボット" ではなく新しいボットを使用して Direct Line Speech にサブスクライブすることができます。

## <a name="try-out-the-sample-app"></a>サンプル アプリを試す

Speech Services サブスクリプション キーとエコー ボットのボット ID を使用して、[UWP 音声アシスタント サンプル](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)を試す準備ができました。 readme の指示に従ってアプリを実行し、資格情報を入力します。

## <a name="create-your-own-voice-assistant-for-windows"></a>Windows 用の独自の音声アシスタントを作成する

Microsoft から制限付きアクセス機能トークンと bin ファイルを受け取ったら、Windows 上で独自の音声アシスタントに着手できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [音声アシスタントの実装ガイドを読む](windows-voice-assistants-implementation-guide.md)
