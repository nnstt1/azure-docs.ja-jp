---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 68e088b088c461b29311a1289e1867c4df9e5b1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069153"
---
このクイックスタートでは、Speech SDK を使用した Speaker Recognition の基本的な設計パターンについて学習します。これには次のものが含まれます。

* テキスト依存およびテキスト非依存の認証
* 音声のグループから音声サンプルを識別する話者識別
* 音声プロファイルの削除

Speaker Recognition の概念の概要については、[概要](../../../speaker-recognition-overview.md)に関する記事を参照してください。 サポートされているプラットフォームの一覧については、左側のナビゲーションの参照ノードを参照してください。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [C++ クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> Microsoft では、Speaker Recognition へのアクセスを制限しています。 [Azure Cognitive Services Speaker Recognition 制限付きアクセス レビュー](https://aka.ms/azure-speaker-recognition)を通じて使用する場合に該当します。 承認後、Speaker Recognition API にアクセスできます。 

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、.cpp ファイルの先頭に次のステートメントを追加します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>テキスト依存の認証

Speaker Verification では、話者が既知の、または **登録されている** 音声と一致することを確認します。 最初の手順では、音声プロファイルを **登録** し、サービスで今後の音声サンプルと比較できるようにします。 この例では、**テキスト依存** の戦略を使用してプロファイルを登録します。その場合、登録と認証の両方に使用する特定のパスフレーズが必要になります。 サポートされるパスフレーズの一覧については、[リファレンス ドキュメント](/rest/api/speakerrecognition/)を参照してください。

### <a name="textdependentverification-function"></a>TextDependentVerification 関数

まず、`TextDependentVerification` 関数を作成します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

この関数では、[CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) メソッドを使用して [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) オブジェクトを作成します。 `VoiceProfile` には次の 3 つの[種類](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype)があることに注意してください。

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

この場合は、`VoiceProfileType::TextDependentVerification` を `CreateProfileAsync` に渡します。

その後、次に定義する 2 つのヘルパー関数 `AddEnrollmentsToTextDependentProfile` と `SpeakerVerify` を呼び出します。 最後に、[DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync) を呼び出してプロファイルをクリーンアップします。

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 関数

音声プロファイルを登録するには、次の関数を定義します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

この関数では、登録のために、必要となる残りのサンプルの数を追跡する `while` ループにオーディオ サンプルを登録します。 繰り返されるたびに、[EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync) によって、マイクに向かってパスフレーズを読み上げるように求められ、サンプルが音声プロファイルに追加されます。

### <a name="speakerverify-function"></a>SpeakerVerify 関数

`SpeakerVerify` を次のように定義します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

この関数では、[SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speaker-speakerverificationmodel#fromprofile) メソッドを使用して [SpeakerVerificationModel](/cpp/cognitive-services/speech/speaker-speakerverificationmodel) オブジェクトを作成し、先ほど作成した [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) オブジェクトを渡します。

次に、[SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) により、パスフレーズをもう一度読み上げるように求められますが、今度は音声プロファイルに対して認証が行われ、0.0 から 1.0 の範囲の類似性スコアが返されます。 また、[SpeakerRecognitionResult](/cpp/cognitive-services/speech/speaker-speakerrecognitionresult) オブジェクトでは、パスフレーズが一致するかどうかに基づいて、`Accept` または `Reject` も返されます。

## <a name="text-independent-verification"></a>テキストに依存しない認証

**テキスト依存** 検証、**テキスト非依存** 検証と異なり、3 つのオーディオ サンプルは必要ありませんが、合計 20 秒のオーディオが必要です。

### <a name="textindependentverification-function"></a>TextIndependentVerification 関数

まず、`TextIndependentVerification` 関数を作成します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

`TextDependentVerification` 関数と同様に、この関数では、[CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) メソッドを使用して [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) オブジェクトを作成します。

この場合は、`VoiceProfileType::TextIndependentVerification` を `CreateProfileAsync` に渡します。

その後、2 つのヘルパー関数を呼び出します。1 つは次に定義する `AddEnrollmentsToTextIndependentProfile`、もう 1 つは既に定義した `SpeakerVerify` です。 最後に、[DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync) を呼び出してプロファイルをクリーンアップします。

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

音声プロファイルを登録するには、次の関数を定義します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

この関数では、登録のために、必要となる残りのオーディオの秒数を追跡する `while` ループにオーディオ サンプルを登録します。 繰り返されるたびに、[EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync) によって、マイクに向かって話すように求められ、サンプルが音声プロファイルに追加されます。

## <a name="speaker-identification"></a>話者識別

話者識別を使用して、登録されている音声の特定のグループの **誰** が話しているのかを判別します。 このプロセスは **テキストに依存しない認証** と非常によく似ています。主な違いは、1 つのプロファイルに対して認証するのではなく、一度に複数の音声プロファイルに対して認証できることです。

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 関数

まず、`TextIndependentIdentification` 関数を作成します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

`TextDependentVerification` および `TextIndependentVerification` 関数と同様に、この関数では、[CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) メソッドを使用して [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) オブジェクトを作成します。

この場合は、`VoiceProfileType::TextIndependentIdentification` を `CreateProfileAsync` に渡します。

その後、2 つのヘルパー関数を呼び出します。1 つは既に定義した `AddEnrollmentsToTextIndependentProfile`、もう 1 つは次に定義する `SpeakerIdentify` です。 最後に、[DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync) を呼び出してプロファイルをクリーンアップします。

### <a name="speakeridentify-function"></a>SpeakerIdentify 関数

`SpeakerIdentify` 関数を次のように定義します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

この関数では、[SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel#fromprofiles) メソッドを使用して [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel) オブジェクトを作成します。 `SpeakerIdentificationModel::FromProfiles` は、[VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) オブジェクトのリストを受け取ります。 この場合は、先ほど作成した `VoiceProfile` オブジェクトを渡すだけです。 ただし、必要に応じて、それぞれが音声の異なるオーディオ サンプルに登録されている複数の `VoiceProfile` オブジェクトを渡すことができます。

次に、[SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) によって、もう一度話すよう求められます。 今回は、自分の声が登録済みの音声プロファイルと比較され、最も類似した音声プロファイルが返されます。

## <a name="main-function"></a>main 関数

最後に、`main` 関数を次のように定義します。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

この関数では、前に定義した関数を呼び出すだけです。 ただし、最初に、[VoiceProfileClient](/cpp/cognitive-services/speech/speaker-voiceprofileclient) オブジェクトと [SpeakerRecognizer](/cpp/cognitive-services/speech/speaker-speakerrecognizer) オブジェクトを作成します。

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient` は、音声プロファイルの作成、登録、および削除に使用されます。 `SpeakerRecognizer` は、1 つ以上の登録済み音声プロファイルに対して音声サンプルを検証するために使用されます。

## <a name="changing-audio-input-type"></a>オーディオ入力の種類の変更

この記事の例では、オーディオ サンプルの入力として既定のデバイス マイクを使用しています。 ただし、マイク入力の代わりにオーディオ ファイルを使用する必要があるシナリオでは、次の行を変更するだけです。

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

この行を次のように変更します。

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

または、使用しているすべての `audio_config` を [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput) に置き換えます。 入力を混在させることもできます。たとえば、登録用にはマイクを、認証用にはファイルを使用します。
