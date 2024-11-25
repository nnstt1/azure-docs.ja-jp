---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: eur
ms.openlocfilehash: 7234ef607f29b1bf65abad5a697e91e035ecad75
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510772"
---
このクイックスタートでは、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。 まずは基本的な構成と合成を行った後、次のようなより高度なカスタム アプリケーション開発の例に進みます。

* インメモリ ストリームとして応答を取得する
* 出力のサンプル レートとビット レートをカスタマイズする
* SSML (音声合成マークアップ言語) を使用して合成要求を送信する
* ニューラル音声を使用する

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [Python クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。

```Python
pip install azure-cognitiveservices-speech
```

macOS を使用していて、インストールの問題が発生した場合は、まず次のコマンドを実行することが必要な場合があります。

```Python
python3 -m pip install --upgrade pip
```

Speech SDK がインストールされたら、スクリプトの先頭に次の import ステートメントを含めます。

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) を作成する必要があります。 このクラスには、音声キーとそれに関連付けられた場所/リージョン、エンドポイント、ホスト、認証トークンなど、サブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: 音声キーと、それに関連付けられた場所/リージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 音声キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 音声キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられた場所またはリージョンを渡します。

この例では、音声キーと場所/リージョンを使用して [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) を作成します。 「[Speech Service を無料で試す](../../../overview.md#try-the-speech-service-for-free)」の手順に従って、これらの資格情報を取得します。

```python
speech_config = SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
```

## <a name="select-synthesis-language-and-voice"></a>合成言語と音声を選択する

Azure Text to Speech サービスでは、250 を超える音声と 70 を超える言語とバリアントがサポートされています。
[すべてのリスト](../../../language-support.md#neural-voices)を入手することも、[テキスト読み上げのデモ](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)でそれらを試すこともできます。
入力テキストに合わせて [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) の言語または音声を指定し、必要な音声を使用します。

```python
# Note: if only language is set, the default voice of that language is chosen.
speech_config.speech_synthesis_language = "<your-synthesis-language>" # e.g. "de-DE"
# The voice setting will overwrite language setting.
# The voice setting will not overwrite the voice element in input SSML.
speech_config.speech_synthesis_voice_name ="<your-wanted-voice>"
```

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次に、[`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) オブジェクトを作成します。これにより、テキストから音声への変換と、スピーカー、ファイル、またはその他の出力ストリームへの出力が実行されます。 [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) は、前の手順で作成された [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) オブジェクト、および出力結果の処理方法を指定する [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig) オブジェクトをパラメーターとして受け取ります。

まず、`filename` コンストラクター パラメーターを使用して `.wav` ファイルに出力を自動的に書き込む `AudioOutputConfig` を作成します。

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

次に、`speech_config` オブジェクトと `audio_config` オブジェクトをパラメーターとして渡して `SpeechSynthesizer` をインスタンス化します。 このようにすれば、音声合成を実行してファイルに書き込むことが、テキスト文字列を使用して `speak_text_async()` を実行するのと同じぐらい簡単になります。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

このプログラムを実行すると、合成された `.wav` ファイルが、指定した場所に書き込まれます。 以上は最も基本的な使用方法の好例ですが、この次は、カスタム シナリオに対応できるよう、出力をカスタマイズし、出力応答をインメモリ ストリームとして処理する方法について説明します。

## <a name="synthesize-to-speaker-output"></a>スピーカー出力に合成する

場合によっては、合成された音声をスピーカーに直接出力することが必要になる場合があります。 これを行うには、前のセクションの例を使用しますが、`filename` パラメーターを削除して `AudioOutputConfig` を変更し、`use_default_speaker=True` を設定します。 これにより、現在のアクティブな出力デバイスに対して出力が行われます。

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>結果をインメモリ ストリームとして取得する

音声アプリケーション開発の多くのシナリオでは、結果として得られたオーディオ データは、ファイルに直接書き込むのではなく、インメモリ ストリームとして必要となるケースがよくあります。 その場合、次のようなカスタム動作を構築できます。

* 結果として得られたバイト配列を、カスタム ダウンストリーム サービス向けのシーク可能なストリームとして抽象化する。
* 結果を他の API またはサービスと統合する。
* オーディオ データの変更やカスタム `.wav` ヘッダーの記述などを行う。

この変更は、前の例から簡単に行うことができます。 まず、`AudioConfig` を削除します。これは、制御を高めるために、この時点から出力動作を手動で管理するからです。 次に、`SpeechSynthesizer` コンストラクターの `AudioConfig` に `None` を渡します。

> [!NOTE]
> 前述のスピーカー出力の例のように省略するのではなく、`AudioConfig` に `None` を渡した場合、既定ではオーディオは現在のアクティブな出力デバイスで再生されません。

今回は、結果を [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) 変数に保存します。 `audio_data` プロパティには、出力データの `bytes` オブジェクトが含まれます。 このオブジェクトを手動で操作することも、[`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) クラスを使用してインメモリ ストリームを管理することもできます。 この例では、`AudioDataStream` コンストラクターを使用して、結果からストリームを取得します。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

ここから、結果として得られた `stream` オブジェクトを使用して、任意のカスタム動作を実装できます。

## <a name="customize-audio-format"></a>オーディオ形式をカスタマイズする

次のセクションでは、次のようなオーディオ出力属性をカスタマイズする方法について説明します。

* オーディオ ファイルの種類
* サンプルレート
* ビット深度

オーディオ形式を変更するには、`SpeechConfig` オブジェクトで `set_speech_synthesis_output_format()` 関数を使用します。 この関数には、[`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) 型の `enum` が必要です。これは、出力形式を選択するために使用します。 使用できる[オーディオ形式の一覧](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat)については、リファレンス ドキュメントを参照してください。

要件に応じて、ファイルの種類ごとにさまざまなオプションがあります。 定義上、`Raw24Khz16BitMonoPcm` のような未加工の形式にはオーディオ ヘッダーが含まれないことに注意してください。 未加工の形式は、ダウンストリームの実装で未加工のビットストリームをデコードできることがわかっている場合か、ビット深度、サンプル レート、チャネル数などに基づいてヘッダーを手動で作成する場合にのみ使用してください。

この例では、`SpeechConfig` オブジェクトに `SpeechSynthesisOutputFormat` を設定することにより、高忠実度の RIFF 形式 `Riff24Khz16BitMonoPcm` を指定します。 前のセクションの例と同様に、[`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) を使用して結果のインメモリ ストリームを取得し、それをファイルに書き込みます。


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

プログラムをもう一度実行すると、指定したパスに、カスタマイズされた `.wav` ファイルが書き込まれます。

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML を使用して音声の特徴をカスタマイズする

音声合成マークアップ言語 (SSML) を使用すると、XML スキーマから要求を送信して、テキスト読み上げ出力のピッチ、発音、読み上げ速度、ボリュームなどを微調整することができます。 このセクションでは音声を変更する例を紹介しますが、より詳細なガイドについては、[SSML の操作方法に関する記事](../../../speech-synthesis-markup.md)を参照してください。

SSML を使用したカスタマイズを開始するには、音声を切り替える単純な変更を加えます。
まず、ルート プロジェクト ディレクトリに SSML 構成用の新しい XML ファイルを作成します (この例では `ssml.xml`)。 ルート要素は常に `<speak>` であり、テキストを `<voice>` 要素でラップすることで、`name` パラメーターを使用して音声を変更できます。 サポートされている **ニューラル** 音声の [全一覧](../../../language-support.md#neural-voices)を参照してください。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-ChristopherNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

次に、XML ファイルを参照するように音声合成要求を変更する必要があります。 要求はほとんど同じですが、`speak_text_async()` 関数を使用する代わりに、`speak_ssml_async()` を使用します。 この関数には XML 文字列が必要なので、最初に SSML 構成を文字列として読み取ります。 ここからは、結果のオブジェクトは前の例とまったく同じです。

> [!NOTE]
> `ssml_string` が文字列の先頭に `ï»¿` を含んでいる場合は、BOM 形式を削除する必要があります。そうしないと、サービスからエラーが返されます。 これを行うには、`encoding` パラメーターを次のように設定します: `open("ssml.xml", "r", encoding="utf-8-sig")`。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

> [!NOTE]
> SSML を使用せずに音声を変更するには、`SpeechConfig.speech_synthesis_voice_name = "en-US-ChristopherNeural"` を使用して `SpeechConfig` のプロパティを設定します

## <a name="get-facial-pose-events"></a>表情イベントを取得する

スピーチは、表情のアニメーションを動かす有効な手段となる場合があります。
特定の音素を生成するときの唇、顎、舌の位置など、観察された発話における主要な姿勢を表すために、[口形素](../../../how-to-speech-synthesis-viseme.md)がよく使用されます。
口形素イベントは、Speech SDK でサブスクライブできます。
その後、口形素イベントを適用すれば、スピーチ音声の再生に伴って、キャラクターに顔のアニメーションを付けることができます。
[口形素イベントを取得する方法](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)に関するセクションを参照してください。
