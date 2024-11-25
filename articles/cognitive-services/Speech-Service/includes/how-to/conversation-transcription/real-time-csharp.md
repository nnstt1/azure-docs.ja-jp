---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: eur
ms.openlocfilehash: 4f396aa1a3ac1b5b02039f5ae525c4a8581780bc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508935"
---
## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-voice-signatures"></a>声紋を作成する

(事前登録されたユーザー プロファイルを使用して特定の参加者を識別しない場合は、この手順を省略できます。)

ユーザー プロファイルを登録する場合は、最初の手順で会話の参加者の声紋を作成して、一意の話者として識別できるようにします。 声紋を作成するための入力 `.wav` 音声ファイルは、16 ビット、16 kHz のサンプル レート、およびシングル チャンネル (モノラル) 形式である必要があります。 各オーディオ サンプルの推奨される長さは、30 秒間から 2 分間です。 オーディオ サンプルが短すぎると、話者を認識するときの精度が低下します。 一意の音声プロファイルを作成するため、`.wav` ファイルは **1 人の** 音声のサンプルである必要があります。

次の例は、C# で [REST API を使用](https://aka.ms/cts/signaturegenservice)して声紋を作成する方法を示しています。 `subscriptionKey`、`region`、およびサンプル `.wav` ファイルのパスを実際の情報に置き換える必要があることに注意してください。

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

関数 `GetVoiceSignatureString()` を実行すると、声紋文字列が正しい形式で返されます。 この関数を 2 回実行して、下記の `voiceSignatureStringUser1` および `voiceSignatureStringUser2` 変数への入力として使用する 2 つの文字列を作成します。

> [!NOTE]
> 声紋は、REST API を使用して **のみ** 作成できます。

## <a name="transcribe-conversations"></a>会話の文字起こし

次のサンプル コードは、2 人の話者の会話をリアルタイムで文字起こしする方法を示しています。 上の手順に従って各話者の声紋文字列が既に作成されていることを前提としています。 `subscriptionKey`、`region`、および文字起こしする音声のパス `filepath` を、実際の情報に置き換えます。

事前登録されたユーザー プロファイルを使用しない場合、speaker1、speaker2 などとして不明なユーザーの最初の認識を完了するまでに数秒かかります。

> [!NOTE]
> 署名の作成にアプリケーション全体で同じ `subscriptionKey` が使用されていることを確認してください。そうでないと、エラーが発生します。 

このサンプル コードは、次の処理を実行します。

* 文字起こしするサンプル `.wav` ファイルから `AudioConfig` を作成します。
* `CreateConversationAsync()` を使用して `Conversation` を作成します。
* コンストラクターを使用して `ConversationTranscriber` を作成し、必要なイベントにサブスクライブします。
* 参加者を会話に追加します。 上記の手順の関数 `GetVoiceSignatureString()` の出力として、文字列 `voiceSignatureStringUser1` と `voiceSignatureStringUser2` が得られます。
* 会話に参加し、文字起こしを開始します。
* 音声サンプルを提供せずに話者を区別したい場合は、[会話の文字起こしの概要](../../../conversation-transcription.md)に関するページにあるように、`DifferentiateGuestSpeakers` 機能を有効にしてください。 

> [!NOTE]
> `AudioStreamReader` は [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/csharp/dotnet/conversation-transcription/helloworld/AudioStreamReader.cs) で取得できるヘルパー クラスです。

関数 `TranscribeConversationsAsync()` を呼び出して、会話の文字起こしを開始します。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

class transcribe_conversation
{
// all your other code

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // en-us by default. Adding this code to specify other languages, like zh-cn.
    // config.SpeechRecognitionLanguage = "zh-cn";
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioConfig.FromWavFileInput(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                conversationTranscriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
         }
      }
   }
}
```

