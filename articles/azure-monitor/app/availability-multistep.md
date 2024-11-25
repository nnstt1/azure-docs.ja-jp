---
title: 複数ステップ Web テストを使用した監視 - Azure Application Insights
description: 複数手順の Web テストを設定して、Azure Application Insights で Web アプリケーションを監視します
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 6e0cbbc772b9eb2f3ad245fcb9728b047d2d7126
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993228"
---
# <a name="multi-step-web-tests"></a>複数手順の Web テスト

複数ステップ Web テストを使用して、記録された一連の URL と Web サイトとのインタラクションを監視することができます。 この記事では、Visual Studio Enterprise を使用した複数ステップ Web テストを作成するプロセスについて説明します。

> [!IMPORTANT]
> [複数ステップ Web テストは非推奨となっています](https://azure.microsoft.com/updates/retirement-notice-transition-to-custom-availability-tests-in-application-insights/)。 複数ステップ Web テストの代わりに、[TrackAvailability](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)  を使用して [カスタム可用性テスト](availability-azure-functions.md) を送信することをお勧めします。 TrackAvailability() とカスタム可用性テストを使用すると、任意のコンピューティングでテストを実行でき、また、C# を使用して容易に新しいテストを作成することができます。

> [!NOTE]
> [Azure Government](../../azure-government/index.yml) クラウドでは、複数ステップ Web テストは **サポートされていません**。


複数ステップ Web テストはクラシック テストとして分類され、[Availability]\(可用性\) ペインの **[Add Classic Test]\(クラシック テストの追加\)** に表示されます。

## <a name="multi-step-webtest-alternative"></a>複数ステップ Web テストに代わる機能

複数ステップ Web テストは、Visual Studio の Web テスト ファイルに依存します。 Visual Studio 2019 が Web テスト機能を備えた最後のバージョンとなることが[発表](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)されました。 新しい機能は追加されませんが、Visual Studio 2019 の Web テスト機能は現在もサポートされており、製品のサポート ライフサイクルの間は引き続きサポートされることを理解しておくことが重要です。 

[カスタム可用性テスト](./availability-azure-functions.md)は、複数ステップ Web テストではなく、[TrackAvailability](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) を使用して送信することをお勧めします。 これは複数の要求または認証テストのシナリオで長期的にサポートされるソリューションです。 TrackAvailability() とカスタム可用性テストを使用すると、任意のコンピューティングでテストを実行でき、また、C# を使用して容易に新しいテストを作成することができます。

## <a name="pre-requisites"></a>前提条件

* Visual Studio 2017 Enterprise 以降。
* Visual Studio の Web パフォーマンスとロード テスト ツール。

前提条件であるテスト ツールを見つけるために、 **[Visual Studio インストーラー]**  >  **[個別のコンポーネント]**  >  **[デバッグとテスト]**  >  **[Web パフォーマンスとロード テスト ツール]** を起動します。

![Web パフォーマンスとロード テスト ツール用の項目の横のチェックボックスを使用して個別のコンポーネントが選択されている Visual Studio インストーラー UI のスクリーンショット](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 複数ステップ Web テストには、それらに関連付けられている追加のコストがあります。 詳細については、[価格の公式ガイド](https://azure.microsoft.com/pricing/details/application-insights/)をご覧ください。

## <a name="record-a-multi-step-web-test"></a>複数ステップ Web テストを記録する 

> [!WARNING]
> マルチステップ レコーダーの使用は推奨されなくなりました。 このレコーダーは、基本的な対話機能を備えた静的な HTML ページ用に開発されており、最新の web ページでは機能しません。

Visual Studio Web テストの作成に関するガイダンスについては、[公式の Visual Studio 2019 ドキュメント](/visualstudio/test/how-to-create-a-web-service-test)を参照してください。

## <a name="upload-the-web-test"></a>Web テストをアップロードする

1. Application Insights ポータルの [可用性] ペインで、 **[Add Classic test]\(クラシック テストの追加\)** を選択し、 *[SKU]* として **[Multi-step]\(複数ステップ\)** を選択します。
2. 複数ステップ Web テストをアップロードします。
3. テストの場所、頻度、およびアラートのパラメーターを設定します。
4. **［作成］** を選択します

### <a name="frequency--location"></a>頻度と場所

|設定| 説明
|----|----|----|
|**テスト間隔**| 各テストの場所からテストを実行する頻度を設定します。 既定の間隔が 5 分で、テストの場所が 5 か所の場合、サイトは平均して毎分テストされます。|
|**テストの場所**| 指定の URL にサーバーによって Web 要求が送信される送信元の場所です。 Web サイトの問題とネットワークの問題とを確実に区別するために、**最低でもテストの場所を 5 か所にすることが推奨されます**。 最大 16 個の場所を選択できます。

### <a name="success-criteria"></a>成功の基準

|設定| 説明
|----|----|----|
| **テストのタイムアウト** |この値を引き下げると、応答が遅い場合に警告されます。 テストは、この期間内にサイトから応答が返されない場合に、エラーとしてカウントされます。 **[従属要求の解析]** をオンにした場合、すべての画像、スタイル ファイル、スクリプト、その他依存するリソースがこの期間内に受信される必要があります。|
| **HTTP 応答** | 成功としてカウントされる、返される状態コード。 200 は、通常の Web ページが返されたことを示すコードです。|
| **コンテンツの一致** | "Welcome!" などの文字列。 それぞれの応答に大文字小文字を区別した完全一致があるかどうかをテストします。 文字列は、(ワイルドカードを含まない) プレーン文字列である必要があります。 ページ コンテンツが変更された場合は、この文字列も更新する必要がある可能性があることに注意してください。 **コンテンツの一致では、英語のみがサポートされています。** |

### <a name="alerts"></a>警告

|設定| 説明
|----|----|----|
|**準リアルタイム (プレビュー)** | 準リアルタイムのアラートを使用することが推奨されます。 この種類のアラートの構成は、可用性テストの作成後に実行されます。  |
|**アラートの場所のしきい値**|少なくとも 3/5 の場所にすることをお勧めします。 アラートの場所のしきい値とテストの場所の数の最適な関係は、**アラートの場所のしきい値** = **テストの場所の数** - 2 です。テストの場所は、少なくとも 5 か所にします。|

## <a name="configuration"></a>構成

### <a name="plugging-time-and-random-numbers-into-your-test"></a>テストに対する時間とランダムな数の組み込み

外部からフィードされる株価など、時間に依存するデータを取得するツールをテストするとします。 Web テストを記録するときに、特定の時刻を使用する必要があります。ただし、その時刻は、StartTime と EndTime というテストのパラメーターとして設定しました。

![myawesomestockapp のスクリーンショット](./media/availability-multistep/app-insights-72webtest-parameters.png)

テストを実行するときに、EndTime を現在の時刻に、StartTime を 15 分前に常に設定する必要があるとします。

Web Test Date Time Plugin によって、パラメーター化された時間を処理する方法が提供されます。

1. 目的の各変数のパラメーター値に対して Web テスト プラグインを追加します。 Web テスト ツールバーで、 **[Web テスト プラグインを追加]** を選択します。
    
    ![Web テスト プラグインを追加する](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    この例では、日時プラグインというインスタンスを 2 つ使用します。 1 つのインスタンスが "15 分前" 用、もう 1 つは "現在" 用です。

2. 各プラグインのプロパティを開きます。 名前を付け、現在の時刻を使用するように設定します。 いずれかのプロパティに、「Add Minutes = -15」を設定します。

    ![コンテキスト パラメーター](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Web テスト パラメーターで、{{プラグイン名}} を使用して、プラグイン名を参照します。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

ここでテストをポータルにアップロードします。 テストを実行するたびに、動的な値が使用されます。

### <a name="dealing-with-sign-in"></a>サインインの処理

ユーザーがアプリにサインインする場合、サインインの背後にあるページをテストできるように、サインインをシミュレートするためのさまざまなオプションがあります。 使用する方法は、アプリで提供されるセキュリティの種類によって異なります。

どの場合も、アプリケーションでテスト目的のみのアカウントを作成する必要があります。 可能であれば、実際のユーザーが Web テストの影響を受けないように、このテスト アカウントのアクセス許可を制限します。

**単純なユーザー名とパスワード** 通常の方法で Web テストを記録します。 まず Cookie を削除します。

**SAML 認証**

|プロパティ名| 説明|
|----|-----|
| 対象ユーザー URI | SAML トークンの対象ユーザー URI です。  これは Access Control Service (ACS) の URI で、ACS 名前空間とホスト名を含みます。 |
| 証明書のパスワード | 埋め込まれた秘密キーへのアクセスを許可するクライアント証明書のパスワード。 |
| クライアント証明書  | Base64 でエンコードされた形式の秘密キーを持つクライアント証明書の値。 |
| 名前識別子 | トークンの名前識別子 |
| 有効期間の終了時刻 | トークンが有効な期間。  既定値は 5 分です。 |
| 期間の開始時刻 | 過去に作成されたトークンの有効期間 (時間のずれを解決するため)。  既定値は (マイナス) 5 分です。 |
| ターゲット コンテキスト パラメーター名 | 生成されたアサーションを受け取るコンテキスト パラメーター。 |


**クライアント シークレット** アプリにクライアント シークレットを含むサインイン ルートがある場合は、そのルートを使用します。 クライアント シークレット サインインを提供するサービスの例として、Azure Active Directory (AAD) が挙げられます。 AAD の場合、クライアント シークレットはアプリ キーです。

アプリ キーを使用した Azure Web アプリのサンプル Web テストを次に示します。

![サンプルのスクリーン ショット](./media/availability-multistep/client-secret.png)

クライアント シークレット (AppKey) を使用して AAD からトークンを取得します。
応答からベアラー トークンを抽出します。
承認ヘッダーにベアラー トークンを使用して API を呼び出します。
Web テストが実際のクライアントであること、つまり、専用のアプリが AAD に登録されていることを確認し、その clientId と appkey を使用します。 テスト対象のサービスの独自のアプリも AAD に登録されている場合: Web テストのリソース フィールドにはこのアプリの appID URI が反映されます。

### <a name="open-authentication"></a>オープン認証
オープン認証の例としては、Microsoft または Google アカウントを使用したサインインが挙げられます。 OAuth を使用する多くのアプリがクライアント シークレットに代わる機能を提供しているため、その可能性を調査することが最初の方法です。

テストで OAuth を使用してサインインする必要がある場合に、一般的な方法を次に示します。

Fiddler などのツールを使用して、Web ブラウザー、認証サイト、アプリケーション間のトラフィックを調べます。
異なるコンピューターやブラウザーを使用するか、または長い間隔 (トークンを期限切れにさせる) で複数のサインインを実行します。
異なるセッションを比較して、認証サイトから返され、次にサインイン後にアプリケーション サーバーに渡されるトークンを識別します。
Visual Studio を使用して Web テストを記録します。
トークンをパラメーター化し、トークンが認証システムから返されたときに、パラメーターを設定し、サイトへのクエリでそれを使用します (Visual Studio は、テストのパラメーター化を試みますが、トークンを正しくパラメーター化しません)。

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティングに関する記事](troubleshoot-availability.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [可用性のアラート](availability-alerts.md)
* [URL ping Web テスト](monitor-web-app-availability.md)
