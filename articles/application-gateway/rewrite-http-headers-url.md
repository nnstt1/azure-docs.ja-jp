---
title: Azure Application Gateway で HTTP ヘッダーと URL を書き換える
description: この記事では、Azure Application Gateway での HTTP ヘッダーと URL の書き換えの概要を説明します
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: c4e4af8fb14c48988a593261365dcfde6c7a0657
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577281"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Application Gateway で HTTP ヘッダーと URL を書き換える

Application Gateway を使用すると、選択した要求と応答のコンテンツを書き換えることができます。 この機能を使用すると、URL の変換、クエリ文字列パラメーター、および要求ヘッダーと応答ヘッダーの変更を行うことができます。 また、条件を追加することで、特定の条件が満たされた場合にのみ、URL または指定したヘッダーが確実に書き換えられるようにできます。 これらの条件は、要求と応答の情報に基づいています。

>[!NOTE]
>HTTP ヘッダーおよび URL の書き換え機能は、[Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md) でのみ使用できます

## <a name="rewrite-types-supported"></a>サポートされている書き換えの種類

### <a name="request-and-response-headers"></a>要求ヘッダーと応答ヘッダー

クライアントとサーバーは、HTTP ヘッダーを使用して、要求または応答に追加の情報を渡すことができます。 これらのヘッダーの書き換えによって、HSTS/X-XSS-Protection などのセキュリティ関連ヘッダー フィールドの追加、機密情報が漏れる可能性のある応答ヘッダー フィールドの削除、X-Forwarded-For ヘッダーからのポート情報の削除など、重要なタスクを実現できます。

Application Gateway を使用することで、要求/応答パケットがクライアントとバックエンド プールの間を移動する間に、HTTP 要求および応答ヘッダーを追加、削除、または更新することができます。

Azure portal を使用して Application Gateway で要求ヘッダーと応答ヘッダーを書き換える方法については、[こちら](rewrite-url-portal.md)を参照してください。

![画像](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**サポートされているヘッダー**

要求と応答では、接続とアップグレードのヘッダーを除くすべてのヘッダーを書き換えることができます。 また、アプリケーション ゲートウェイを使用してカスタム ヘッダーを作成し、ゲートウェイを経由してルーティングされる要求と応答にヘッダーを追加することもできます。

### <a name="url-path-and-query-string"></a>URL パスとクエリ文字列

Application Gateway の URL 書き換え機能を使用すると、次のことができます。

* 要求 URL のホスト名、パス、およびクエリ文字列の書き換え 

* リスナーに対するすべての要求の URL の書き換え、または設定した 1 つ以上の条件に一致する要求の URL のみの書き換え。 これらの条件は、要求と応答のプロパティ (要求、ヘッダー、応答ヘッダー、およびサーバー変数) に基づいています。

* 元の URL または書き換えられた URL に基づく要求のルーティング (バックエンド プールの選択)

Azure portal を使用して Application Gateway で URL を書き換える方法については、[こちら](rewrite-url-portal.md)を参照してください。

![Application Gateway を使用して URL を書き換えるプロセスを説明する図。](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>書き換えアクション

書き換えアクションを使用して、書き換える URL、要求ヘッダー、または応答ヘッダーと、書き換え後の新しい値を指定します。 URL、新しいヘッダーまたは既存のヘッダーの値は、次の種類の値に設定できます。

* Text
* 要求ヘッダー。 要求ヘッダーを指定するには、{http_req_<*ヘッダー名*>} という構文を使用する必要があります
* 応答ヘッダー。 応答ヘッダーを指定するには、{http_resp_<*ヘッダー名*>} という構文を使用する必要があります
* サーバー変数。 サーバー変数を指定するには、{var_<*サーバー変数*>} という構文を使用する必要があります。 サポートされているサーバー変数の一覧を参照してください
* テキスト、要求ヘッダー、応答ヘッダー、サーバー変数の組み合わせ。 

## <a name="rewrite-conditions"></a>書き換え条件

書き換え条件 (オプションの構成) を使用して、HTTP(S) の要求と応答のコンテンツを評価し、1 つ以上の条件が満たされたときにのみ書き換えを実行することができます。 アプリケーション ゲートウェイは、次の 3 種類の変数を使用して、要求と応答のコンテンツを評価します。

* 要求の HTTP ヘッダー
* 応答の HTTP ヘッダー
* Application Gateway のサーバー変数

条件を使用して、指定した変数が存在するかどうか、指定した変数が特定の値と一致するかどうか、または指定した変数が特定のパターンと一致するかどうかを、評価することができます。 


### <a name="pattern-matching"></a>パターン マッチング 

Application Gateway では、条件のパターン マッチングに正規表現が使用されます。 条件に正規表現のパターン マッチングを設定するには、[Perl Compatible Regular Expressions (PCRE) ライブラリ](https://www.pcre.org/)を使用できます。 正規表現の構文については、[Perl 正規表現の man ページ](https://perldoc.perl.org/perlre.html)をご覧ください。

### <a name="capturing"></a>キャプチャ

後で使用するために部分文字列をキャプチャするには、条件の正規表現の定義で、照合するサブパターンをかっこで囲みます。 かっこの最初のペアによってその部分文字列が 1 に、2 番目のペアによって 2 に格納されます (以下同様)。 かっこはいくつでも使用できます。Perl は、キャプチャされた文字列を表すために、より多くの番号付き変数を定義し続けます。 [参考書籍](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm)の例を次に示します。 

*  /(\d)(\d)/ # 2 桁を照合し、それらをグループ 1 と 2 にキャプチャします 

* /(\d +)/ # 1 桁以上の数字を照合し、すべてをグループ 1 にキャプチャします 

* /(\d)+/ # 1 桁を 1 回以上照合し、最後の数字をグループ 1 にキャプチャします

キャプチャが完了したら、次の形式を使用してアクション セットでそれらを参照できます。

* 要求ヘッダーのキャプチャの場合は、{http_req_headerName_groupNumber} を使用する必要があります。 たとえば、{http_req_User-Agent_1} または {http_req_User-Agent_2} です
* 応答ヘッダーのキャプチャの場合は、{http_resp_headerName_groupNumber} を使用する必要があります。 たとえば、{http_resp_Location_1} または {http_resp_Location_2} です
* サーバー変数の場合は、{var_serverVariableName_groupNumber} を使用する必要があります。 たとえば、{var_uri_path_1} または {var_uri_path_2} です

値全体を使用する場合は、数値を指定しないでください。 GroupNumber を指定せずに、{http_req_headerName} などの形式を使用します。

## <a name="server-variables"></a>サーバー変数

Application Gateway では、サーバー変数を使用して、サーバー、クライアントとの接続、およびその接続での現在の要求に関する情報が格納されます。 格納される情報には、クライアントの IP アドレスや Web ブラウザーの種類などがあります。 サーバー変数は、たとえば新しいページが読み込まるときや、フォームが投稿されるときに動的に変化します。 これらの変数を使用して、書き換え条件を評価し、ヘッダーを書き換えることができます。 サーバー変数の値を使用してヘッダーを書き換えるには、{var_ *serverVariableName*} という構文でこれらの変数を指定する必要があります

アプリケーション ゲートウェイでは、次のサーバー変数がサポートされています。

|   変数名    |                   説明                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | X-Forwarded-For クライアント要求ヘッダー フィールドと、IP1、IP2、IP3 などの形式でそれに追加される `client_ip` 変数 (この表で後ほど説明します) が含まれています。 X-Forwarded-For フィールドがクライアント要求ヘッダーにない場合、`add_x_forwarded_for_proxy` 変数は `$client_ip` 変数と等しくなります。   この変数は、Application Gateway によって設定された X-Forwarded-For ヘッダーを書き換えるときに特に有用です。この方法により、ヘッダーにはポート情報は含まれず、IP アドレスのみが含まれるようになります。 |
| ciphers_supported         | クライアントでサポートされている暗号の一覧。               |
| ciphers_used              | 確立された TLS 接続で使用される暗号の文字列。 |
| client_ip                 | アプリケーション ゲートウェイが要求を受信したクライアントの IP アドレス。 アプリケーション ゲートウェイと発信元のクライアントの前にリバース プロキシがある場合、`client_ip` にはリバース プロキシの IP アドレスが返されます。 |
| client_port               | クライアント ポート。                                             |
| client_tcp_rtt            | クライアント TCP 接続の情報。 TCP_INFO ソケット オプションをサポートするシステムで使用できます。 |
| client_user               | HTTP 認証の使用時に、認証のために提供されるユーザー名。 |
| host                      | 優先順位は次のとおりです: 要求行のホスト名、Host 要求ヘッダー フィールドのホスト名、要求に一致するサーバー名。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、host 値は `contoso.com` になります |
| cookie_ *name*             | *name* クッキー。                                           |
| http_method               | URL 要求を行うために使用されたメソッド。 たとえば、GET、POST などです。 |
| http_status               | セッションの状態。 たとえば、200、400、403 です。           |
| http_version              | 要求プロトコル。 通常、HTTP/1.0、HTTP/1.1、または HTTP/2.0 です。 |
| query_string              | 要求された URL 内で "?" の後にある、変数と値のペアから成る一覧。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、query_string 値は `id=123&title=fabrikam` になります |
| received_bytes            | 要求の長さ (要求行、ヘッダー、および要求本文を含む)。 |
| request_query             | 要求行内の引数。                           |
| request_scheme            | 要求スキーム。http または https です。                           |
| request_uri               | 完全な元の要求 URI (引数を含む)。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*`では、request_uri 値は `/article.aspx?id=123&title=fabrikam` になります |
| sent_bytes                | クライアントに送信されたバイト数。                        |
| server_port               | 要求を受け付けたサーバーのポート。              |
| ssl_connection_protocol   | 確立された TLS 接続のプロトコル。               |
| ssl_enabled               | 接続が TLS モードで動作する場合は "オン"。 それ以外の場合は、空の文字列です。 |
| uri_path                  | Web クライアントがアクセスする必要があるホスト内の特定のリソースを識別します。 これは、引数を含まない要求 URI の部分です。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、uri_path 値は `/article.aspx` になります |

### <a name="mutual-authentication-server-variables-preview"></a>相互認証サーバー変数 (プレビュー)

Application Gateway は、相互認証のシナリオに対して次のサーバー変数をサポートしています。 これらのサーバー変数は、上記の他のサーバー変数と同じように使用します。 

|   変数名    |                   説明                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | 確立された SSL 接続用のクライアント証明書 (PEM 形式)。 |
| client_certificate_end_date| クライアント証明書の終了日。 |
| client_certificate_fingerprint| 確立された SSL 接続用のクライアント証明書の SHA1 フィンガープリント。 |
| client_certificate_issuer | 確立された SSL 接続用のクライアント証明書の "発行者の DN" 文字列。 |
| client_certificate_serial | 確立された SSL 接続用のクライアント証明書のシリアル番号。  |
| client_certificate_start_date| クライアント証明書の開始日。 |
| client_certificate_subject| 確立された SSL 接続のクライアント証明書の "サブジェクトの DN" 文字列。 |
| client_certificate_verification| クライアント証明書検証の結果: *SUCCESS*、*FAILED:\<reason\>* 、証明書が存在しない場合は *NONE*。 | 

## <a name="rewrite-configuration"></a>書き換えの構成

書き換えルールを構成するには、書き換えルール セットを作成して、それに書き換えルールの構成を追加する必要があります。

書き換えルール セットには、次のものが含まれています。

* **要求ルーティング規則の関連付け:** 書き換え構成が、ルーティング規則によってソース リスナーに関連付けられます。 基本ルーティング規則を使用すると、書き換え構成はソース リスナーに関連付けられ、グローバルなヘッダーの書き換えになります。 パスベースのルーティング規則を使用すると、書き換え構成は URL パス マップで定義されます。 その場合、サイトの特定のパス領域にのみ適用されます。 書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 ただし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

* **書き換え条件**: これはオプション構成です。 書き換え条件では、HTTP(S) の要求と応答の内容が評価されます。 HTTP(S) の要求または応答が書き換え条件に一致する場合、書き換えアクションが発生します。 複数の条件を 1 つのアクションと関連付けた場合は、すべての条件が満たされた場合にのみアクションが発生します。 つまり、操作は論理 AND 操作です。

* **書き換えの種類**: 使用できる書き換えには、次の 3 種類があります。
   * 要求ヘッダーの書き換え 
   * 応答ヘッダーの書き換え
   * URL コンポーネントの書き換え
      * **URL パス**:パスの書き換え後の値。 
      * **URL クエリ文字列**:クエリ文字列の書き換え後の値。 
      * **パス マップの再評価**:URL パス マップを再評価するかどうかを決定するために使用します。 オフのままにすると、元の URL パスが使用され、URL パス マップのパス パターンと照合されます。 True に設定すると、URL パス マップが再評価され、書き換えられたパスと一致するかどうかがチェックされます。 このスイッチを有効にすると、書き換え後に要求を別のバックエンド プールにルーティングする際に役立ちます。

## <a name="rewrite-configuration-common-pitfalls"></a>書き込み構成に関する一般的な落とし穴

* 基本要求ルーティング規則では、[パス マップの再評価] を有効にすることはできません。 これは、基本的なルーティング規則の無限評価ループを防ぐためです。

* パスベースのルーティング規則の無限評価ループを防ぐために、パスベースのルーティング規則に対して [パス マップの再評価] が有効になっていない 1 つ以上の条件付き書き換え規則または書き換え規則が必要です。

* ループがクライアントの入力に基づいて動的に作成された場合、受信要求は 500 エラー コードで終了します。 Application Gateway は、このようなシナリオでパフォーマンスを低下させずに、引き続き他の要求を処理します。

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Web アプリケーション ファイアウォール (WAF_v2 SKU) で URL 書き換えまたはホスト ヘッダー書き換えを使用する

URL 書き換えまたはホスト ヘッダー書き換えを構成すると、要求ヘッダーまたは URL パラメーターの変更後 (書き換え後) に WAF 評価が行われます。 また、Application Gateway で URL 書き換えまたはホスト ヘッダー書き換え構成を削除すると、ヘッダー書き換えの前 (事前書き換え) に WAF 評価が行われます。 この順序により、バックエンド プールによって受信される最終的な要求に WAF ルールが適用されます。

たとえば、`"Accept" : "text/html"` ヘッダーに対して、次のヘッダー書き換えルールがあるとします。ヘッダーの値 `"Accept"` が `"text/html"` と等しい場合は、値を `"image/png"` に書き換えます。

ここでは、ヘッダー書き換えが構成されているだけで、WAF の評価が `"Accept" : "text/html"` に行われます。 ただし、URL 書き換えやホスト ヘッダー書き換えを構成する場合は、WAF の評価は `"Accept" : "image/png"` に行われます。

>[!NOTE]
> URL 書き換え操作では、WAF Application Gateway の CPU 使用率がわずかに増加することが予想されます。 WAF Application Gateway で URL 書き換えルールを有効にした後、[CPU 使用率メトリック](high-traffic-support.md)を短時間監視することをお勧めします。

### <a name="common-scenarios-for-header-rewrite"></a>ヘッダーの書き換えの一般的なシナリオ

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-Forwarded-For ヘッダーからポート情報を削除する

Application Gateway では、要求をバックエンドに転送する前に、すべての要求に X-Forwarded-For ヘッダーが挿入されます。 このヘッダーは、IP ポート のコンマ区切りリストです。 バック エンド サーバーでヘッダーに IP アドレスが含まれることだけが必要なシナリオがあるとします。 ヘッダーの書き換えを使用して X-Forwarded-For ヘッダーからポート情報を削除できます。 これを行う 1 つの方法は、ヘッダーを add_x_forwarded_for_proxy サーバー変数に設定することです。 または、client_ip 変数を使用することもできます。

![ポートを削除する](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>リダイレクト URL を変更する

バックエンド アプリケーションでリダイレクト応答が送信されるとき、バックエンド アプリケーションで指定されているものとは別の URL にクライアントをリダイレクトしたい場合があります。 たとえば、アプリ サービスがアプリケーション ゲートウェイの背後でホストされており、クライアントに相対パスへのリダイレクトを行わせる必要があるときに、これを行えます。 (たとえば、contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクトです)。

App Service はマルチテナント サービスであるため、要求のホスト ヘッダーを使用して適切なエンドポイントに要求をルーティングします。 アプリ サービスの既定のドメイン名 \*.azurewebsites.net (contoso.azurewebsites.net など) は、アプリケーション ゲートウェイのドメイン名 (contoso.com など) とは異なります。 クライアントからの元の要求には、ホスト名としてアプリケーション ゲートウェイのドメイン名 contoso.com が設定されているので、アプリケーション ゲートウェイでホスト名を contoso.azurewebsites.net に変更します。 アプリ サービスが適切なエンドポイントに要求をルーティングできるようにこの変更を行います。

アプリ サービスでは、リダイレクト応答を送信するとき、その応答の場所ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 そのため、クライアントでは、アプリケーション ゲートウェイ (`contoso.com/path2`) を経由するのではなく、`contoso.azurewebsites.net/path2` に直接要求を行います。 アプリケーション ゲートウェイをバイパスすることは望ましくありません。

この問題は、場所ヘッダーのホスト名をアプリケーション ゲートウェイのドメイン名に設定することで解決できます。

ホスト名を置換する手順を次に示します。

1. 応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件で書き換え規則を作成します。 パターン「`(https?):\/\/.*azurewebsites\.net(.*)$`」を入力します。
2. アプリケーション ゲートウェイのホスト名を含むように、場所ヘッダーを書き換えるにアクションを実行します。 これを行うには、ヘッダー値として「`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`」を入力します。 または、サーバー変数 `host` を使用して、元の要求と一致するようにホスト名を設定することもできます。

![場所ヘッダーを変更する](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>脆弱性を防ぐための HTTP セキュリティ ヘッダーを実装する

アプリケーションの応答で必要なヘッダーを実装することにより、いくつかのセキュリティの脆弱性を修正できます。 たとえば、X-XSS-Protection、Strict-Transport-Security、Content-Security-Policy などのセキュリティ ヘッダーです。 Application Gateway を使用して、すべての応答にこれらのヘッダーを設定できます。

![セキュリティ ヘッダー](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>不要なヘッダーを削除する

HTTP 応答から、機密情報を明らかにするヘッダーを削除することができます。 たとえば、バックエンド サーバー名、オペレーティング システム、ライブラリの詳細などの情報を削除できます。 アプリケーション ゲートウェイを使用して、これらのヘッダーを削除できます。

![ヘッダーを削除する](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>ヘッダーの有無を確認する

HTTP 要求または応答ヘッダーを評価し、ヘッダーまたはサーバー変数の有無を確認できます。 この評価は、特定のヘッダーが存在する場合にのみヘッダーの書き換えを実行する場合に役立ちます。

![ヘッダーの有無を確認する](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>URL の書き換えの一般的なシナリオ

#### <a name="parameter-based-path-selection"></a>パラメーター ベースのパスの選択

ヘッダーの値、URL の一部、または要求内のクエリ文字列の値に基づいてバックエンド プールを選択するシナリオを実現するには、URL 書き換え機能とパスベースのルーティングの組み合わせを使用できます。 たとえば、会社にショッピング Web サイトがあり、製品カテゴリがクエリ文字列として URL に渡され、そのクエリ文字列に基づいて要求をバックエンドにルーティングする場合は、次の手順を実行します。

**ステップ 1:** 次の図に示すように、パス マップを作成します

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL 書き換えシナリオ 1-1。":::

**ステップ 2 (a):** 次の 3 つの書き換えルールを含む書き換えセットを作成します。 

* 1 番目のルールには、*query_string* 変数が *category=shoes* であるかどうかをチェックする条件があり、URL パスを /*listing1* に書き換える、 **[パス マップの再評価]** が有効なアクションがあります。

* 2 番目のルールには、*query_string* 変数が *category=bags* であるかどうかをチェックする条件があり、URL パスを /*listing2* に書き換える、 **[パス マップの再評価]** が有効なアクションがあります。

* 3 番目のルールには、*query_string* 変数が *category=accessories* であるかどうかをチェックする条件があり、URL パスを /*listing3* に書き換える、 **[パス マップの再評価]** が有効なアクションがあります。

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL 書き換えシナリオ 1-2。":::

 

**ステップ 2 (b):** この書き換えセットを上記のパスベースのルールの既定のパスに関連付けます

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL 書き換えシナリオ 1-3。":::

ここで、ユーザーが *contoso.com/listing?category=any* を要求した場合、パスマップのパス パターン (/listing1、/listing2、/listing3) がいずれも一致しないため、既定のパスと一致します。 上記の書き換えセットをこのパスに関連付けたため、この書き換えセットが評価されます。 クエリ文字列はこの書き換えセットの 3 つの書き換えルールのいずれの条件とも一致しないので、書き換えアクションが実行されないため、要求は既定のパスに関連付けられているバックエンド (*GenericList*) に変更されずにルーティングされます。

ユーザーが *contoso.com/listing?category=shoes* を要求した場合は、既定のパスと再び一致します。 ただし、この場合、最初のルールの条件が一致するため、その条件に関連付けられているアクションが実行されます。これにより、URL パスが/*listing1* に書き換えられ、パスマップが再評価されます。 パスマップが再評価されると、要求は、パターン */listing1* に関連付けられているパスと一致するようになり、このパターンに関連付けられているバックエンド (ShoesListBackendPool) にルーティングされます。

>[!NOTE]
>このシナリオは、定義されている条件に基づいて、任意のヘッダーやクッキーの値、URL パス、クエリ文字列、またはサーバー変数に適用できます。また、基本的に、これらの条件に基づいて要求をルーティングできます。

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>URL に基づいてクエリ文字列パラメーターを書き換える

ユーザーに表示されるリンクを単純で読みやすくする必要があるショッピング Web サイトのシナリオについて考えてみましょう。ただし、バックエンド サーバーでは、適切なコンテンツを表示するためにクエリ文字列パラメーターが必要となります。

この場合、Application Gateway は URL からパラメーターを取得し、URL のクエリ文字列のキーと値のペアを追加できます。 たとえば、ユーザーが `https://www.contoso.com/fashion/shirts` を `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` に書き換える必要がある場合は、次の URL 書き換え構成を使用して実現できます。

**条件** - サーバー変数 `uri_path` がパターン `/(.+)/(.+)` に等しい場合

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL 書き換えシナリオ 2-1。":::

**アクション** - URL パスを `buy.aspx` に設定し、クエリ文字列を `category={var_uri_path_1}&product={var_uri_path_2}` に設定します

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL 書き換えシナリオ 2-2。":::

前述のシナリオを実現するための詳細な手順については、[Azure portal を使用した Application Gateway での URL の書き換え](rewrite-url-portal.md)に関する記事を参照してください

### <a name="url-rewrite-vs-url-redirect"></a>URL の書き換えと URL のリダイレクト

URL の書き換えの場合は、要求がバックエンドに送信される前に、Application Gateway によって URL が書き換えられます。 変更がユーザーに表示されないため、ブラウザーでユーザーに表示される内容は変更されません。

URL のリダイレクトの場合は、Application Gateway が、新しい URL を使用してリダイレクトの応答をクライアントに送信します。 そのため、クライアントは、リダイレクトで提供された新しい URL に要求を再送信する必要があります。 ブラウザーでユーザーに表示される URL は、新しい URL に更新されます。

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="書き換えとリダイレクト。":::

## <a name="limitations"></a>制限事項

- 応答内に同じ名前のヘッダーが複数含まれている場合、これらのヘッダーのいずれかの値を書き換えると、応答内の他のヘッダーが破棄されます。 応答に複数の Set-Cookie ヘッダーを設定できるので、これは通常、Set-Cookie ヘッダーで発生します。 このようなシナリオの 1 つが、アプリ サービスをアプリケーション ゲートウェイとともに使用していて、アプリケーション ゲートウェイで Cookie ベースのセッション アフィニティを構成している場合です。 この場合、応答には 2 つの Set-Cookie ヘッダーが含まれ、1 つはアプリ サービスで使用されるもの (`Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`) で、もう 1 つはアプリケーション ゲートウェイ アフィニティ用 (`Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`) です。 このシナリオでどちらかの Set-Cookie ヘッダーを書き換えると、もう一方の Set-Cookie ヘッダーが応答から削除されることがあります。
- アプリケーション ゲートウェイが要求をリダイレクトするように構成されている場合、またはカスタム エラー ページを表示するように構成されている場合は、書き換えはサポートされません。
- ヘッダー名には、任意の英数字と、[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) で定義されている特定の記号を含めることができます。 現在はヘッダー名内で特殊文字のアンダー スコア (\_) がサポートされていません。
- 接続ヘッダーとアップグレード ヘッダーを書き換えることはできません

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Application Gateway で HTTP ヘッダーを書き換える方法](rewrite-http-headers-portal.md)
- [Azure portal を使用して Application Gateway で URL を書き換える方法](rewrite-url-portal.md)
