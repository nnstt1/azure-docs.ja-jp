---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.custom: devx-track-java
ms.author: eur
ms.openlocfilehash: 29518e68e3de51ae37812ba590b36ab37dc9b54c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510860"
---
このクイックスタートでは、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。 まずは基本的な構成と合成を行った後、次のようなより高度なカスタム アプリケーション開発の例に進みます。

* インメモリ ストリームとして応答を取得する
* 出力のサンプル レートとビット レートをカスタマイズする
* SSML (音声合成マークアップ言語) を使用して合成要求を送信する
* ニューラル音声を使用する

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [Java クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/text-to-speech)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java ランタイム</a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Android </a>

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、スクリプトの先頭に次の import ステートメントを含めます。

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) を作成する必要があります。 このクラスには、音声キーとそれに関連付けられた場所/リージョン、エンドポイント、ホスト、認証トークンなど、サブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: 音声キーと、それに関連付けられた場所/リージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 音声キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 音声キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられた場所またはリージョンを渡します。

この例では、音声キーと場所/リージョンを使用して [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) を作成します。 「[Speech Service を無料で試す](../../../overview.md#try-the-speech-service-for-free)」の手順に従って、これらの資格情報を取得します。 また、この記事の残りの部分で使用する、基本的な定型コードをいくつか作成します。これを変更して、さまざまなカスタマイズを行います。

```java
public class Program
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    }
}
```

## <a name="select-synthesis-language-and-voice"></a>合成言語と音声を選択する

Azure Text to Speech サービスでは、250 を超える音声と 70 を超える言語とバリアントがサポートされています。
[すべてのリスト](../../../language-support.md#neural-voices)を入手することも、[テキスト読み上げのデモ](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)でそれらを試すこともできます。
入力テキストに合わせて [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) の言語または音声を指定し、必要な音声を使用します。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    // Note: if only language is set, the default voice of that language is chosen.
    config.setSpeechSynthesisLanguage("<your-synthesis-language>"); // e.g. "de-DE"
    // The voice setting will overwrite language setting.
    // The voice setting will not overwrite the voice element in input SSML.
    config.setSpeechSynthesisVoiceName("<your-wanted-voice>");
}
```

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次に、[`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) オブジェクトを作成します。これにより、テキストから音声への変換と、スピーカー、ファイル、またはその他の出力ストリームへの出力が実行されます。 [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) は、前の手順で作成された [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) オブジェクト、および出力結果の処理方法を指定する [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) オブジェクトをパラメーターとして受け取ります。

まず、`fromWavFileOutput()` 静的関数を使用して `.wav` ファイルに出力を自動的に書き込む `AudioConfig` を作成します。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

次に、`speechConfig` オブジェクトと `audioConfig` オブジェクトをパラメーターとして渡す `SpeechSynthesizer` をインスタンス化します。 こうすることで、音声合成を実行してファイルに書き込むことが、テキスト文字列を使用して `SpeakText()` を実行するのと同じぐらい簡単になります。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

このプログラムを実行すると、合成された `.wav` ファイルが、指定した場所に書き込まれます。 以上は最も基本的な使用方法の好例ですが、この次は、カスタム シナリオに対応できるよう、出力をカスタマイズし、出力応答をインメモリ ストリームとして処理する方法について説明します。

## <a name="synthesize-to-speaker-output"></a>スピーカー出力に合成する

場合によっては、合成された音声をスピーカーに直接出力することが必要になる場合があります。 これを行うには、`fromDefaultSpeakerOutput()` 静的関数を使用して `AudioConfig` をインスタンス化します。 これにより、現在のアクティブな出力デバイスに対して出力が行われます。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>結果をインメモリ ストリームとして取得する

音声アプリケーション開発の多くのシナリオでは、結果として得られたオーディオ データは、ファイルに直接書き込むのではなく、インメモリ ストリームとして必要となるケースがよくあります。 その場合、次のようなカスタム動作を構築できます。

* 結果として得られたバイト配列を、カスタム ダウンストリーム サービス向けのシーク可能なストリームとして抽象化する。
* 結果を他の API またはサービスと統合する。
* オーディオ データの変更やカスタム `.wav` ヘッダーの記述などを行う。

この変更は、前の例から簡単に行うことができます。 まず、`AudioConfig` ブロックを削除します。これは、この時点から出力動作を手動で管理して制御を強化するためです。 次に、`SpeechSynthesizer` コンストラクターの `AudioConfig` に `null` を渡します。

> [!NOTE]
> 前述のスピーカー出力の例のように省略するのではなく、`AudioConfig` に `null` を渡した場合、既定ではオーディオは現在のアクティブな出力デバイスで再生されません。

今回は、結果を [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) 変数に保存します。 `SpeechSynthesisResult.getAudioData()` 関数は、出力データの `byte []` を返します。 この `byte []` を手動で操作することも、[`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) クラスを使用してインメモリ ストリームを管理することもできます。 この例では、`AudioDataStream.fromResult()` 静的関数を使用して、結果からストリームを取得します。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

ここから、結果として得られた `stream` オブジェクトを使用して、任意のカスタム動作を実装できます。

## <a name="customize-audio-format"></a>オーディオ形式をカスタマイズする

次のセクションでは、次のようなオーディオ出力属性をカスタマイズする方法について説明します。

* オーディオ ファイルの種類
* サンプルレート
* ビット深度

オーディオ形式を変更するには、`SpeechConfig` オブジェクトで `setSpeechSynthesisOutputFormat()` 関数を使用します。 この関数には、[`SpeechSynthesisOutputFormat`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat) 型の `enum` が必要です。これは、出力形式を選択するために使用します。 使用できる[オーディオ形式の一覧](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat)については、リファレンス ドキュメントを参照してください。

要件に応じて、ファイルの種類ごとにさまざまなオプションがあります。 定義上、`Raw24Khz16BitMonoPcm` のような未加工の形式にはオーディオ ヘッダーが含まれないことに注意してください。 未加工の形式は、ダウンストリームの実装で未加工のビットストリームをデコードできることがわかっている場合か、ビット深度、サンプル レート、チャネル数などに基づいてヘッダーを手動で作成する場合にのみ使用してください。

この例では、`SpeechConfig` オブジェクトに `SpeechSynthesisOutputFormat` を設定することにより、高忠実度の RIFF 形式 `Riff24Khz16BitMonoPcm` を指定します。 前のセクションの例と同様に、[`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) を使用して結果のインメモリ ストリームを取得し、それをファイルに書き込みます。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

プログラムをもう一度実行すると、指定したパスに `.wav` ファイルが書き込まれます。

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

次に、XML ファイルを参照するように音声合成要求を変更する必要があります。 要求はほとんど同じですが、`SpeakText()` 関数を使用する代わりに、`SpeakSsml()` を使用します。 この関数には XML 文字列が必要なので、最初に、XML ファイルを読み込んでそれを文字列として返す関数を作成します。

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

ここからは、結果のオブジェクトは前の例とまったく同じです。

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

> [!NOTE]
> SSML を使用せずに音声を変更するには、`SpeechConfig.setSpeechSynthesisVoiceName("en-US-ChristopherNeural");` を使用して `SpeechConfig` のプロパティを設定します

## <a name="get-facial-pose-events"></a>表情イベントを取得する

スピーチは、表情のアニメーションを動かす有効な手段となる場合があります。
特定の音素を生成するときの唇、顎、舌の位置など、観察された発話における主要な姿勢を表すために、[口形素](../../../how-to-speech-synthesis-viseme.md)がよく使用されます。
Speech SDK で口形素イベントをサブスクライブすることにより、顔のアニメーション データを取得し、キャラクターに顔のアニメーションを付けるときにそのデータを適用することができます。
[口形素イベントを取得する方法](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)に関するセクションを参照してください。
