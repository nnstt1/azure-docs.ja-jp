---
title: App Service インスタンスの正常性の監視
description: 正常性チェックを使用して App Service インスタンスの正常性を監視する方法について説明します。
keywords: Azure App Service, Web アプリ, 正常性チェック, トラフィックのルーティング, 正常なインスタンス, パス, 監視, 障害のあるインスタンスの削除, 異常なインスタンス, ワーカーの削除
author: msangapu-msft
ms.topic: article
ms.date: 07/19/2021
ms.author: msangapu
ms.custom: contperf-fy22q1
ms.openlocfilehash: 59e3d0f8e71de6333b3978dd9d40b51206a8a2a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462350"
---
# <a name="monitor-app-service-instances-using-health-check"></a>正常性チェックを使用して App Service インスタンスを監視する

この記事では、Azure portal の正常性チェックを使用して App Service インスタンスを監視します。 正常性チェックを行うと、異常なインスタンスを避けて要求を再ルーティングし、正常に戻らないインスタンスを置き換えることができるので、アプリケーションの可用性が向上します。 正常性チェックを完全に利用するには、[App Service プラン](./overview-hosting-plans.md)を 2 つ以上のインスタンスにスケーリングする必要があります。 正常性チェック パスによって、ご利用のアプリケーションの重要なコンポーネントが確認されます。 たとえば、ご利用のアプリケーションがデータベースとメッセージング システムに依存している場合、正常性チェックのエンドポイントはそれらのコンポーネントに接続する必要があります。 アプリケーションが重要なコンポーネントに接続できない場合は、アプリケーションが異常であることを示す 500 レベルの応答コードがパスから返されます。

![正常性チェックのエラー][1]

## <a name="what-app-service-does-with-health-checks"></a>App Service で正常性チェックを使用した処理

- アプリでパスを指定すると、正常性チェックによって、App Service アプリのすべてのインスタンスで 1 分間隔でこのパスに対する ping が実行されます。
- インスタンスが 2 つ以上の要求の後に 200 ～ 299 (200 と 299 を含む) の状態コードで応答しない場合、または ping への応答に失敗した場合は、システムによって異常があると判断され、削除されます。
- 削除後、正常性チェックによる異常なインスタンスへの ping が継続されます。 インスタンスが正常な状態コード (200 - 299) で応答し始める場合、インスタンスはロード バランサーに返されます。
- インスタンスが 1 時間、異常のままである場合、それは新しいインスタンスに置き換えられます。
- さらに、スケール アップまたはスケール アウトする場合は、新しいインスタンスの準備ができていることを保証するために、App Service によって正常性チェック パスに対して ping が実行されます。

> [!NOTE]
>- 正常性チェックは 302 リダイレクトには従いません。 App Service プランによれば、1 時間あたり最大で 1 つのインスタンス、1 日あたり最大で 3 つのインスタンスが置き換えられます。
>- 正常性チェックで状態 `Waiting for health check response` が表示されている場合は、HTTP 状態コード 307 が原因でチェックが失敗している可能性があります。これは、HTTPS リダイレクトが有効になっているが `HTTPS Only` が無効になっている場合に発生する可能性があります。

## <a name="enable-health-check"></a>正常性チェックを有効にする

![Azure portal での正常性チェックのナビゲーション][3]

- 正常性チェックを有効にするには、Azure portal にアクセスし、App Service アプリを選択します。
- **[監視]** で **[正常性チェック]** を選択します。
- **[有効]** を選択し、`/health` や `/api/health` など、ご利用のアプリケーションの有効な URL パスを指定します。
- **[保存]** をクリックします。

> [!CAUTION]
> 正常性チェックの構成変更によって、アプリが再起動します。 運用環境のアプリへの影響を最小限に抑えるには、[ステージング スロットを構成し](deploy-staging-slots.md)、運用環境にスワップすることをお勧めします。
>

### <a name="configuration"></a>構成

正常性チェックのオプションを構成するだけでなく、次の[アプリ設定](configure-common.md)を構成することもできます。

| アプリ設定の名前 | 使用できる値 | 説明 |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 ～ 10 | インスタンスを異常と見なし、ロード バランサーから削除するために必要な、失敗した要求の数。 たとえば、`2` に設定すると、ping に `2` 回失敗した後にインスタンスが削除されます。 (既定値は `10`) |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 ～ 100 | 既定では、残りの正常なインスタンスに負荷がかかりすぎるのを避けるため、一度にロード バランサーから除外されるインスタンスの数は半分以下です。 たとえば、App Service プランが 4 つのインスタンスにスケーリングされ、3 つに異常が発生した場合、2 つが除外されます。 他の 2 つのインスタンス (1 つは正常、1 つは異常) は、引き続き要求を受信することになります。 すべてのインスタンスが異常であるという最悪のシナリオでは、何も除外されません。 <br /> この動作をオーバーライドするには、アプリ設定を `0` ～ `100` の値に設定します。 値を大きくするほど、削除される異常なインスタンスが増えます (既定値は `50`)。 |

#### <a name="authentication-and-security"></a>認証とセキュリティ

正常性チェックは、App Service の[認証および認可機能](overview-authentication-authorization.md)と統合されます。 これらのセキュリティ機能が有効になっている場合、追加の設定は必要ありません。

独自の認証システムを使用する場合は、正常性チェック パスで匿名アクセスを許可する必要があります。 正常性チェック エンドポイントをセキュリティで保護するには、まず [IP 制限](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、[クライアント証明書](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、Virtual Network などの機能を使用して、アプリケーション アクセスを制限する必要があります。 着信要求の `User-Agent` が `HealthCheck/1.0` と一致するように要求することで、正常性チェック エンドポイントをセキュリティで保護できます。 以前のセキュリティ機能によって要求が既にセキュリティで保護されているため、User-Agent になりすますことはできません。

## <a name="monitoring"></a>監視

アプリケーションの正常性チェック パスを指定したら、Azure Monitor を使用してご利用のサイトの正常性を監視できます。 ポータルの **[正常性チェック]** ブレードで、上部のツールバーにある **[メトリック]** をクリックします。 これにより新しいブレードが開き、サイトの過去の正常性状態を確認したり、新しいアラート ルールを作成したりできるようになります。 サイトの監視方法の詳細については、[Azure Monitor に関するガイドを参照してください](web-sites-monitor.md)。

## <a name="limitations"></a>制限事項

- Premium Functions サイトでは、正常性チェックを有効にしないでください。 Premium Functions の迅速なスケーリングのため、正常性チェック要求によって HTTP トラフィックが不必要に変動する可能性があります。 Premium Functions には、スケーリングの決定を通知するために使用される、独自の内部正常性プローブが用意されています。
- 正常性チェックは **Free** と **Shared** の App Service プランに対して有効にできるので、サイトの正常性に関するメトリックを取得し、アラートを設定できますが、**Free** と **Shared** のサイトはスケールアウトできないので、異常なインスタンスを置き換えることはできません。 2 つ以上のインスタンスにスケールアウトし、正常性チェックのベネフィットを完全に利用できるようにするには、**Basic** レベル以上にスケールアップする必要があります。 これは、アプリの可用性とパフォーマンスが向上するので、運用環境向けアプリケーションに推奨されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-happens-if-my-app-is-running-on-a-single-instance"></a>アプリが 1 つのインスタンスで実行されている場合は、どうなるでしょうか?

アプリが 1 つのインスタンスだけにスケーリングされていて、異常になった場合、ロード バランサーから削除するとアプリケーションが完全にダウンするため、削除されません。 正常性チェックの再ルーティングのベネフィットを実現するには、2 つ以上のインスタンスにスケールアウトします。 アプリが 1 つのインスタンスで実行されている場合でも、正常性チェックの[監視](#monitoring)機能を使用して、アプリケーションの正常性を追跡できます。
 
### <a name="why-are-the-health-check-request-not-showing-in-my-frontend-logs"></a>正常性チェック要求がフロントエンドのログに表示されないのはなぜですか?

正常性チェック要求はサイトに内部的に送信されるため、要求は[フロントエンドのログ](troubleshoot-diagnostic-logs.md#enable-web-server-logging)には表示されません。 これは、要求は内部的に送信されるので、要求の送信元が `127.0.0.1` であることも意味します。 正常性チェックのコードにログ ステートメントを追加して、正常性チェックのパスで ping が行われたときのログを保持できます。

### <a name="are-the-health-check-requests-sent-over-http-or-https"></a>正常性チェック要求は、HTTP または HTTPS のどちらで送信されますか?

Windows App Service では、サイトで [HTTPS のみ](configure-ssl-bindings.md#enforce-https)が有効になっている場合、正常性チェック要求は HTTPS で送信されます。 それ以外の場合は、HTTP で送信されます。 Linux App Service では、正常性チェック要求は HTTP を使用してのみ送信され、現時点では HTTP **S** を使用して送信することはできません。

### <a name="what-if-i-have-multiple-apps-on-the-same-app-service-plan"></a>同じ App Service プランで複数のアプリがある場合はどうなりますか?

App Service プランに他のアプリがあるかどうかに関係なく、異常なインスタンスはロード バランサーのローテーションから常に削除されます ([`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`](#configuration) で指定されている割合まで)。 インスタンス上の 1 つのアプリが 1 時間より長く異常状態のままになっているときは、正常性チェックが有効になっている他のすべてのアプリも異常である場合にのみ、インスタンスが置き換えられます。 正常性チェックが有効になっていないアプリは考慮されません。 

#### <a name="example"></a>例 

正常性チェックが有効になっている、アプリ A およびアプリ B という名前の 2 つのアプリケーション (またはスロットがある 1 つのアプリ) があるとします。それらは同じ App Service プラン上にあり、プランは 4 つのインスタンスにスケールアウトされます。 2 つのインスタンスでアプリ A が異常になると、ロード バランサーによって、それら 2 つのインスタンス上のアプリ A への要求の送信は停止されます。 その場合でも、アプリ B が正常であるとすると、要求はそれらのインスタンス上のアプリ B にルーティングされます。 それら 2 つのインスタンスでアプリ A の異常状態が 1 時間を超えると、それらのインスタンスでアプリ B **もやはり** 異常である場合にのみ、それらのインスタンスは置き換えられます。 アプリ B が正常な場合は、インスタンスは置き換えられません。

![上記のシナリオ例を説明する図。][2]

> [!NOTE]
> プランに正常性チェックが有効になっていない別のサイトまたはスロットがある場合 (サイト C)、それはインスタンスの置換には考慮されません。

### <a name="what-if-all-my-instances-are-unhealthy"></a>すべてのインスタンスが異常になった場合はどうなりますか?

アプリケーションのすべてのインスタンスが異常なシナリオでは、`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` で指定されている割合になるまで、App Service によってロード バランサーからインスタンスが削除されます。 このシナリオでは、すべての異常なアプリ インスタンスがロード バランサーのローテーションから外されると、アプリケーションは実質的に停止します。

### <a name="does-health-check-work-on-app-service-environments"></a>正常性チェックは App Service Environment で機能しますか?

はい。App Service Environment (ASE) では、プラットフォームから指定されたパスのインスタンスに ping が実行され、ロード バランサーから異常なインスタンスが削除されるので、要求はそれらにルーティングされません。 ただし、現在、これらの異常なインスタンスは、1 時間異常なままになっている場合、新しいインスタンスに置き換えられません。

## <a name="next-steps"></a>次のステップ
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)
- [環境変数とアプリ設定のリファレンス](reference-app-settings.md)

[1]: ./media/app-service-monitor-instances-health-check/health-check-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-multi-app-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
