---
title: 通話 (音声またはビデオ) およびチャットの価格シナリオ
titleSuffix: An Azure Communication Services concept document
description: Communication Services の価格モデルについて説明します。
author: nmurav
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5d08f964899faf9fe438a0df68c6fe4401fd01c7
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859382"
---
# <a name="pricing-scenarios"></a>価格シナリオ

Azure Communication Services の価格は、一般に従量課金制モデルに基づきます。 以降の例に出現する価格はあくまで例であり、最新の Azure の価格は反映されていない可能性があります。

## <a name="voicevideo-calling-and-screen-sharing"></a>音声またはビデオによる通話と画面共有

Azure Communication Services を使用すると、音声またはビデオによる通話と画面共有をアプリケーションに追加できます。 JavaScript、Objective-C (Apple)、Java (Android)、または .NET SDK を使用して、アプリケーションにエクスペリエンスを埋め込むことができます。 [使用可能な SDK の完全な一覧](./sdk-options.md)を参照してください。

### <a name="pricing"></a>価格

通話および画面共有のサービスは、グループ通話に対して参加者あたり $0.004/分で、参加者ごとに分単位で課金されます。 Azure Communication Services では、データ エグレスに対して課金されません。 可能な各種の呼び出しフローを理解するには、[こちらのページ](./call-flows.md)を参照してください。

通話の各参加者は、通話に接続されている 1 分ごとに課金されます。 これは、ユーザーがビデオ通話、音声通話、画面共有のいずれを行っているかに関係なく当てはまります。

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>価格の例: JS SDK および iOS SDK を使用して音声またはビデオによるグループ通話を行う

Alice が、仕事仲間の Bob と Charlie とグループ通話を行いました。 Alice と Bob は JS SDK を、Charlie は iOS SDK を使用しました。

- この通話は合計 60 分間続きました。
- Alice と Bob は通話全体に参加しました。 Alice は、自分のビデオを 5 分間オンにし、自分の画面を 23 分間共有しました。 Bob は、通話全体 (60 分間) にわたってビデオを表示し、自分の画面を 12 分間共有しました。
- Charlie は 43 分後に通話を終了しました。 Charlie は、参加している間中 (43 分間)、オーディオとビデオを使用しました。

**コストの計算**

- 2 人の参加者 x 60 分 x 参加者あたり $0.004/分 = $0.48 [ビデオとオーディオの両方が同じ料金で課金されます]
- 1 人の参加者 x 43 分 x 参加者あたり $0.004/分 = $0.172 [ビデオとオーディオの両方が同じ料金で課金されます]

**グループ通話の総コスト**: $0.48 + $0.172 = $0.652


### <a name="pricing-example-outbound-call-from-app-using-js-sdk-to-a-pstn-number"></a>価格の例: JS SDK を使用してアプリから PSTN 番号への発信通話を行う

Alice は、Bob の `+1-425` で始まる米国の電話番号に対し、アプリから PSTN 通話を行います。

- Alice は JS SDK を使用してアプリを作成しました。
- この通話は合計 10 分間続きます。

**コストの計算**

- アプリから Communication Services サーバーへの VoIP レッグの参加者 1 名 (Alice) x 10 分 x $0.004 (1 参加者レッグ、1 分あたり) = $0.04
- Communication Services サーバーから米国の電話番号への PSTN 発信レッグの参加者 1 名 (Bob) x 10 分 x $0.013 (1 参加者レッグ、1 分あたり) = $0.13

> [!Note]
> `+1-425` に対する米国の混合料金は $0.013 です。 詳細については、 https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) のリンクを参照してください。


**通話の総コスト**: $0.04 + $0.13 = $0.17

### <a name="pricing-example-outbound-call-from-app-using-js-sdk-via-azure-communication-services-direct-routing"></a>価格の例: Azure Communication Services のダイレクト ルーティングを使用したアプリからの送信呼び出し (JS SDK)

Alice は、Azure Communication Services のダイレクト ルーティングを使用して、Azure Communication Services アプリから電話番号 (Bob) への送信呼び出しを行います。
- Alice は JS SDK を使用してアプリを作成しました。
- 呼び出しは、Communication Services のダイレクト ルーティングを介して接続されたセッション境界コントローラー (SBC) に移動します。
- この通話は合計 10 分間続きます。 

**コストの計算**

- アプリから Communication Services サーバーへの VoIP レッグの参加者 1 名 (Alice) x 10 分 x $0.004 (1 参加者レッグ、1 分あたり) = $0.04
- Communication Services サーバーから SBC への Communication Services ダイレクト ルーティング発信レッグの参加者 1 名 (Bob) x 10 分 x $0.004 (1 参加者レッグ、1 分あたり) = $0.04。

**通話の総コスト**: $0.04 + $0.04 = $0.08

### <a name="pricing-example-group-audio-call-using-js-sdk-and-one-pstn-leg"></a>価格の例: JS SDK と 1 PSTN レッグを使用してグループ音声通話を行う

Alice と Bob は VOIP 通話を行っています。 Bob は、Charlie の PSTN 番号 (`+1-425` で始まる米国の電話番号) で、通話を Charlie にエスカレートしました。

- Alice は JS SDK を使用してアプリを作成しました。 PSTN 番号で Charlie を呼び出すまでの通話時間は 10 分です。
- Bob が Charlie の PSTN 番号に通話をエスカレートした後、3 人はさらに 10 分間通話しました。

**コストの計算**

- アプリから Communication Services サーバーへの VoIP レッグの参加者 2 名 (Alice と Bob) x 20 分 x $0.004 (1 参加者レッグ、1 分あたり) = $0.16
- Communication Services サーバーから米国電話番号への PSTN 発信レッグの参加者 1 名 (Charlie) x 10 分 x $0.013 (1 参加者レッグ、1 分あたり) = $0.13

注: `+1-425` に対する米国の混合料金は $0.013 です。 詳細については、 https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) のリンクを参照してください。

**VoIP + エスカレーション通話の総コスト**: $0.16 + $0.13 = $.29


### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>価格の例: スケジュールされている Microsoft Teams 会議に Communication Services JavaScript SDK のユーザーが参加する

Alice は医師として、患者である Bob を診察する予定です。 Alice は Teams デスクトップ アプリケーションから診察に参加します。 Bob には、医療機関の Web サイトを使用して参加するためのリンクが送信されます。このリンクを通じて、Communication Services JavaScript SDK を使用した診察に接続することができます。 Bob は携帯電話から Web ブラウザー (iPhone と Safari) を使用して診察に参加します。 仮想診察の間はチャットが利用できます。

- この通話は合計 30 分間続きます。
- Bob は会議に参加すると、Teams のポリシーに従って Teams ミーティング ロビーに配置されます。 1 分後、彼が会議に参加することを Alice が認めます。
- Bob が会議への参加を認められた後、Alice と Bob は通話全体に参加します。 Alice は、通話が開始されてから 5 分後にビデオをオンにし、13 分間画面を共有します。 Bob は通話が終わるまでビデオをオンにします。
- Alice は 5 件のメッセージを送信し、うち 3 件に Bob が返信します。


**コストの計算**

- Teams ロビーに接続された 1 人の参加者 (Bob) x 1 分 x 参加者 1 名につき毎分 $0.004 (ロビーは会議の通常料金で課金されます) = $0.004
- 1 人の参加者 (Bob) x 29 分 x 参加者あたり $0.004/分 = $0.116 [ビデオとオーディオの両方が同じ料金で課金されます]
- 1 人の参加者 (Alice) x 30 分 x 参加者あたり $0.000/分 = $0.0*
- 1 人の参加者 (Bob) x 3 件のチャット メッセージ x $0.0008 = $0.0024
- 1 人の参加者 (Alice) x 5 件のチャット メッセージ x $0.000  = $0.0*

\* Alice の参加は、Teams のライセンスによってカバーされています。 Azure の請求書には、Teams ユーザーが Communication Services ユーザーと交わしたチャット メッセージと時間 (分) が表示されますが、これはあくまで参考のためです。Teams クライアント側から送信されたメッセージと時間 (分) は課金されません。

**診察の総コスト**:
- Communication Services JavaScript SDK を使用して参加するユーザー: $0.004 + $0.116 + $0.0024 = $0.1224
- Teams デスクトップ アプリケーションで参加するユーザー: $0 (Teams ライセンスに含まれています)


## <a name="call-recording"></a>通話レコーディング

Azure Communication Services を使用すると、PSTN、WebRTC、会議、SIP インターフェイスの通話をレコーディングできます。 現在、通話レコーディングでは、混合オーディオ + ビデオ MP4 と混合オーディオ専用 MP3/WAV 出力形式がサポートされています。 通話レコーディング SDK は、Java と C# で使用できます。 詳細については、[こちらのページ](../quickstarts/voice-video-calling/call-recording-sample.md)を参照してください。

### <a name="price"></a>Price

混合オーディオ + ビデオ形式の場合は $0.01/分、混合オーディオのみの場合は $0.002/分が課金されます。

### <a name="pricing-example-record-a-call-in-a-mixed-audiovideo-format"></a>価格の例: 混合オーディオ + ビデオ形式で通話をレコーディングする

Alice が、仕事仲間の Bob と Charlie とグループ通話を行いました。 

- この通話は合計 60 分間続きました。 そして、レコーディングは 60 分間アクティブでした。
- Bob は 30 分間、Alice と Charlie は 60 分間通話に参加していました。

**コストの計算**
- 会議の長さに対して課金されます。 (会議の長さは、ユーザーがレコーディングを開始してから、明示的に停止するまで、または誰も会議に残っていない時点までのタイムラインです)。
- 60 分 x $0.01/分 (1 レコーディングあたり) = $0.6

### <a name="pricing-example-record-a-call-in-a-mixed-audioonly-format"></a>価格の例: 混合オーディオ専用形式で通話をレコーディングする

Alice は Jane との通話を開始します。 

- この通話は合計 60 分間続きました。 レコーディングは 45 分間続きました。

**コストの計算**
- レコーディングの長さに対して課金されます。 
- 45 分 x $0.002/分 (1 レコーディングあたり) = $0.09

## <a name="chat"></a>チャット

Communication Services を使用すると、2 人以上のユーザー間でチャット メッセージを送受信する機能でアプリケーションを強化できます。 Chat SDK は、JavaScript、.NET、Python、および Java で使用できます。 SDK の詳細については、[こちらのページ](./sdk-options.md)を参照してください

### <a name="price"></a>Price

送信されたチャット メッセージごとに $0.0008 が課金されます。

### <a name="pricing-example-chat-between-two-users"></a>価格の例:2 人のユーザー間のチャット

Geeta が、最新情報を共有するために Emily とチャット スレッドを開始し、5 件のメッセージを送信します。 チャットは 10 分間継続します。 その間に Geeta と Emily はさらに 15 件のメッセージをそれぞれ送信します。

**コストの計算**
- 送信されたメッセージの数 (5 + 15 + 15) x $0.0008 = $0.028

### <a name="pricing-example-group-chat-with-multiple-users"></a>価格の例:複数のユーザーとのグループ チャット

Charlie が、旅行を計画するために、友人の Casey と Jasmine とチャット スレッドを開始します。 彼らはしばらくチャットを行い、その間に Charlie、Casey、Jasmine はそれぞれ 20 件、30 件、18 件のメッセージを送信します。 友人の Rose も旅行への参加に興味があるかもしれないと気付いたので、彼女をチャット スレッドに追加し、すべてのメッセージ履歴を彼女と共有します。

Rose はメッセージを表示し、チャットを開始します。 その間に、Casey は電話を受け、後で会話に追いつくことにします。 Charlie、Jasmine、Rose は旅行日を決定し、それぞれ 30 件、25 件、35 件のメッセージを送信します。

**コストの計算**

- 送信されたメッセージの数 (20 + 30 + 18 + 30 + 25 + 35) x $0.0008 = $0.1264


## <a name="telephony-and-sms"></a>テレフォニーと SMS

## <a name="price"></a>Price

テレフォニー サービスは分単位の価格となるのに対し、SMS はメッセージ単位の価格となります。 価格は、ご使用の番号の種類と場所、さらに通話と SMS メッセージの宛先によって決まります。

### <a name="telephone-number-leasing"></a>電話番号のリース

電話番号のリース料金は、前払いで毎月課金されます。

|数値型   |月額料金   |
|--------------|-----------|
|ローカル (米国)     |1 ドル/月        |
|無料電話番号 (米国) |2 ドル/月 |


### <a name="telephone-calling"></a>電話による通話

従来の電話による通話 (公衆交換電話網での通話) は、米国内の電話番号については従量課金制価格で提供されます。 価格は、使用されている番号の種類と通話の宛先に基づく分単位の料金となっています。 以下の表は、最も一般的な通話の宛先について価格の詳細をまとめたものです。 全宛先の一覧については、[詳細な価格リスト](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)を参照してください。


#### <a name="united-states-calling-prices"></a>米国の通話価格

次の価格には、通信に必要な税と料金が含まれています。

|数値型   |電話をかける   |電話を受ける|
|--------------|-----------|------------|
|ローカル     |$0.013/分から       |$0.0085/分        |
|無料電話番号 |$0.013/分   |$0.0220/分 |

#### <a name="other-calling-destinations"></a>その他の通話の宛先

次の価格には、通信に必要な税と料金が含まれています。

|発信先   |1 分あたりの価格|
|-----------|------------|
|カナダ     |$0.013/分から   |
|イギリス     |$0.015/分から   |
|ドイツ     |$0.015/分から   |
|フランス     |$0.016/分から   |


### <a name="sms"></a>SMS

SMS の価格は従量課金制です。 価格は、メッセージの宛先に基づく、メッセージ セグメントごとの料金となっています。 メッセージ セグメントの詳細については、[こちら](./telephony-sms/sms-faq.md#what-is-the-sms-character-limit)を参照してください。 メッセージは、無料電話番号から米国内の電話番号に送信することができます。 SMS メッセージの送信にローカル (固定) 電話番号は使用できないので注意してください。

次の価格には、通信に必要な税と料金が含まれています。

|国   |メッセージを送信する|メッセージを受信する|
|-----------|------------|------------|
|USA (無料電話番号)    |$0.0075/メッセージ セグメント  | $0.0075/メッセージ セグメント |
