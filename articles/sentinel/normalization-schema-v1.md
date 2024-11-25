---
title: Microsoft Azure Sentinel のネットワーク正規化スキーマ (レガシ バージョン - パブリック プレビュー) | Microsoft Docs
description: この記事では、Microsoft Azure Sentinel のデータ正規化スキーマを示します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b263a0d7be7d0bd42494dc5c5a931f9c97b00bfb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712853"
---
# <a name="microsoft-sentinel-network-normalization-schema-legacy-version---public-preview"></a>Microsoft Azure Sentinel のネットワーク正規化スキーマ (レガシ バージョン - パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

ネットワーク正規化スキーマは、報告されたネットワーク イベントを説明するために使用されます。Microsoft Azure Sentinel では、ソースに依存しない分析を可能にするために使用されます。

詳細については、「[正規化と Microsoft Sentinel 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> この記事は、ASIM が使用可能になる前にプレビューとしてリリースされた、ネットワーク正規化スキーマのバージョン 0.1 に関連しています。 [バージョン 0.2](network-normalization-schema.md) のネットワーク正規化スキーマは ASIM と適合し、その他の拡張機能が提供されます。
>
> 詳細については、[ネットワーク正規化スキーマのバージョン間の相違点](#changes)に関するページを参照してください
>

## <a name="terminology"></a>用語

次の用語が、Microsoft Azure Sentinel のスキーマで使用されます。

| 項目 | 定義 |
| ---- | ---------- |
| **レポート デバイス** | レコードを Microsoft Azure Sentinel に送信するシステム。 レコードのサブジェクト システムではない場合があります。 |
| **レコード** | レポート デバイスから送信されるデータの単位。 このデータの単位は、`log`、`event`、または `alert` と呼ばれることがよくありますが、他の種類が含まれる場合もあります。|
|

## <a name="data-types-and-formats"></a>データ型と形式

次の表は、データ値を正規化するためのガイダンスを示しています。これは、正規化されたフィールドには必須であり、他のフィールドには推奨されます。

| データ型 | 物理型 | 形式と値 |
| --------- | ------------- | ---------------- |
| **日付/時刻** | 使用する取り込み方法の機能に応じて、次のいずれか (降順の優先順位)<ul><li>Log Analytics 組み込みの datetime 型</li><li>Log Analytics の datetime の数値表現を使用した整数フィールド</li><li>Log Analytics の datetime の数値表現を使用した文字列フィールド</li></ul> | Log Analytics の datetime の表現。 <br></br>Log Analytics の日付と時刻の表現は、Unix の時刻表現と性質は似ていますが異なります。 以下の変換ガイドラインを参照してください。 <br><br>タイムゾーンに合わせて日付と時刻を調整する必要があります。 |
| **MAC アドレス** | String | コロン 16 進表記 |
| **IP アドレス** | IP アドレス | スキーマには、IPv4 アドレスと IPv6 アドレスが別々に含まれているわけではありません。 どの IP アドレス フィールドにも、IPv4 アドレスか IPv6 アドレスのどちらかを含めることができます。<ul><li>ドット 10 進表記の IPv4</li><li>IPv6 は 8 ヘクテット表記で、ここで説明する短い形式を使用できます。</li></ul> |
| **User** | String | 次の 3 つのユーザー フィールドを使用できます。<ul><li>ユーザー名</li><li>ユーザー UPN</li><li>ユーザー ドメイン</li></ul> |
| **[ユーザー ID]** | String | 次の 2 つのユーザー ID が現在サポートされています。<ul><li>ユーザー SID</li><li>Azure Active Directory ID</li></ul> |
| **[デバイス]** | String | 次の 3 つのデバイス/ホスト列がサポートされています。<ul><li>id</li><li>名前</li><li>完全修飾ドメイン名 (FQDN)</li></ul> |
| **Country (国)** | String | 次の優先順位に従って ISO 3166-1 を使用した文字列:<ul><li>アルファ 2 コード (例: 北米は `US`)</li><li>アルファ 3 コード (例: 北米は `USA`)</li><li>短い名前</li></ul> |
| **リージョン** | String | ISO 3166-2 を使用した都道府県名 |
| **City (市)** | String | |
| **Longitude (経度)** | Double | ISO 6709 座標表現 (符号付き 10 進数) |
| **Latitude (緯度)** | Double | ISO 6709 座標表現 (符号付き 10 進数) |
| **ハッシュ アルゴリズム** | String | 次の 4 つのハッシュ列がサポートされています。<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **ファイルの種類** | String | このファイル タイプの種類:<ul><li>拡張子</li><li>クラス</li><li>NamedType</li></ul> |
|

## <a name="network-sessions-table-schema"></a>ネットワーク セッション テーブル スキーマ

次に、バージョン 1.0.0 のネットワーク セッション テーブルのスキーマを示します

| フィールド名 | 値の種類 | 例 | 説明 | 関連付けられた OSSEM エンティティ |
|-|-|-|-|-|
| **EventType** | String | トラフィック | 収集されているイベントの種類 | event |
| **EventSubType** | String | 認証 | 種類の追加説明 (該当する場合) | Event |
| **EventCount** | Integer  | 10 | 集計されたイベントの数 (該当する場合)。 | event |
| **EventEndTime** | 日付/時刻 | 「データ型」をご覧ください | イベントが終了した時刻 | event |
| EventMessage | string |  アクセス拒否 | レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明 | event |
| **DvcIpAddr** | IP アドレス |  23.21.23.34 | レコードを生成しているデバイスの IP アドレス | デバイス<br>IP |
| **DvcMacAddr** | String | 06:10:9f:eb:8f:14 | イベントの送信元のレポート デバイスのネットワーク インターフェイスの MAC アドレス。 | デバイス<br>Mac |
| **DvcHostname** | デバイス名 (文字列) | syslogserver1.contoso.com | メッセージを生成しているデバイスのデバイス名。 | Device |
| **EventProduct** | String | OfficeSharepoint | イベントを生成している製品。 | event |
| **EventProductVersion** | string | 9.0 |  イベントを生成している製品のバージョン。 | event |
| **EventResourceId** | デバイス ID (文字列) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | メッセージを生成しているデバイスのリソース ID。 | event |
| **EventReportUrl** | String | https://192.168.1.1/repoerts/ae3-56.htm | レポート デバイスによって作成された完全なレポートへのリンク | event |
| **EventVendor** | String | Microsoft | イベントを生成している製品のベンダー。 | event |
| **EventResult** | 複数値:Success、Partial、Failure、[空] (文字列) | Success | アクティビティについて報告された結果。 該当しない場合は空の値。 | event |
| **EventResultDetails** | String | パスワードが違います | EventResult で報告された結果の理由または詳細 | event |
| **EventSchemaVersion** | Real | 0.1 | Microsoft Azure Sentinel スキーマのバージョン。 現時点では 0.1。 | event |
| **EventSeverity** | String | 低 | 報告されたアクティビティがセキュリティに影響を与える場合、影響の重大度を示します。 | event |
| **EventOriginalUid** | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | レポート デバイスからのレコード ID。 | event |
| **EventStartTime** | 日付/時刻 | 「データ型」をご覧ください | イベントが開始した時刻 | event |
| **TimeGenerated** | 日付/時刻 | 「データ型」をご覧ください | レポート ソースによって報告された、イベントが発生した時刻。 | カスタム フィールド |
| **EventTimeIngested** | 日付/時刻 | 「データ型」をご覧ください | イベントが Microsoft Azure Sentinel に取り込まれた時刻。 Microsoft Azure Sentinel によって追加されます。 | event |
| **EventUid** | Guid (文字列) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Microsoft Azure Sentinel が行をマークするために使用される一意の識別子。 | event |
| **NetworkApplicationProtocol** | String | HTTPS | 接続またはセッションで使用されるアプリケーション レイヤー プロトコル。 | ネットワーク |
| **DstBytes** | INT | 32455 | 接続またはセッションで送信先から送信元に送信されたバイト数。 | 宛先 |
| **SrcBytes** | INT | 46536 | 接続またはセッションで送信元から送信先に送信されたバイト数。 | source |
| **NetworkBytes** | INT | 78991 | 両方向に送信されたバイト数。 BytesReceived と BytesSent が両方とも存在する場合、BytesTotal はその合計と同じである必要があります。 | ネットワーク |
| **NetworkDirection** | 複数値:受信、送信 (文字列) | 着信 | 組織に対する接続またはセッションの方向。 | ネットワーク |
| **DstGeoCity** | String | Burlington | 送信先 IP アドレスに関連付けられた都市 | 送信先、<br>ジオ (主要地域) |
| **DstGeoCountry** | 国 (文字列) | 米国 | 送信元 IP アドレスに関連付けられた国 | 送信先、<br>ジオ (主要地域) |
| **DstDvcHostname** | デバイス名 (文字列) |  victim_pc | 送信先デバイスのデバイス名 | 宛先<br>Device |
| **DstDvcFqdn** | String | victim_pc.contoso.local | ログが作成されたホストの完全修飾ドメイン名 | 送信先、<br>Device |
| **DstDomainHostname** | string | CONTOSO | たとえば、DNS 参照や NS 参照用の送信先のドメイン、送信先ホストのドメイン (Web サイト、ドメイン名など) | 宛先 |
| **DstInterfaceName** | string | Microsoft Hyper-V ネットワーク アダプター | 送信先デバイスによる接続またはセッションに使用されるネットワーク インターフェイス。 | 宛先 |
| **DstInterfaceGuid** | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 認証要求に使用されたネットワーク インターフェイスの GUID  | 宛先 |
| **DstIpAddr** | IP アドレス | 2001:db8::ff00:42:8329 | 接続またはセッションの送信先の IP アドレス。通常、ネットワーク パケットで送信先 IP と呼ばれます | 送信先、<br>IP |
| **DstDvcIpAddr** | IP アドレス | 75.22.12.2 | ネットワークパケットに直接関連付けられていないデバイスの送信先 IP アドレス | 送信先、<br>デバイス<br>IP
| **DstGeoLatitude** | 緯度 (倍精度) | 44.475833 | 送信先 IP アドレスに関連付けられている地理的座標の緯度 | 送信先、<br>ジオ (主要地域) |
| **DstMacAddr** | String | 06:10:9f:eb:8f:14 | 接続またはセッションが終了するネットワーク インターフェイスの MAC アドレス。通常は、ネットワーク パケットでの送信先 MAC と呼ばれます | 送信先、<br>MAC |
| **DstDvcMacAddr** | String | 06:10:9f:eb:8f:14 | ネットワーク パケットに直接関連付けられていないデバイスの送信先 MAC アドレス。 | 送信先、<br>デバイス<br>MAC |
| **DstDvcDomain** | String | CONTOSO | 送信先デバイスのドメイン。 | 送信先、<br>Device |
| **DstPortNumber** | Integer | 443 | 送信先 IP ポート。 | 送信先、<br>ポート |
| **DstGeoRegion** | リージョン (文字列) | バーモント | 送信先 IP アドレスに関連付けられたリージョン | 送信先、<br>ジオ (主要地域) |
| **DstResourceId** | デバイス ID (文字列) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | 送信先デバイスのリソース ID。 | 宛先 |
| **DstNatIpAddr** | IP アドレス | 2::1 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信元との通信に使用する IP アドレス。 | 送信先 NAT、<br>IP |
| **DstNatPortNumber** | INT | 443 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信元との通信に使用するポート。 | 送信先 NAT、<br>ポート |
| **DstUserSid** | ユーザー SID |  S-12-1445 | セッションの送信先に関連付けられている ID のユーザー ID。 通常、サーバーの認証に使用される ID。 詳細については、「[データ型と形式](#data-types-and-formats)」を参照してください。 | 送信先、<br>User |
| **DstUserAadId** | 文字列 (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | セッションの送信先側にいるユーザーの Azure AD アカウント オブジェクト ID | 送信先、<br>User |
| **DstUserName** | ユーザー名 (文字列) | johnd | セッションの送信先に関連付けられている ID のユーザー名。  | 送信先、<br>User |
| **DstUserUpn** | string | johnd@anon.com | セッションの送信先に関連付けられている ID の UPN。 | 送信先、<br>User |
| **DstUserDomain** | string | WORKGROUP | セッションの送信先側にあるアカウントのドメインまたはコンピューター名 | 送信先、<br>User |
| **DstZone** | String | Dmz | レポート デバイスにより定義された、送信先のネットワーク ゾーン。 | 宛先 |
| **DstGeoLongitude** | 経度 (倍精度) | -73.211944 | 送信先 IP アドレスに関連付けられている地理的座標の経度 | 送信先、<br>ジオ (主要地域) |
| **DvcAction** | 複数値:Allow、Deny、Drop (文字列) | Allow | ファイアウォールなどの中間デバイスによって報告された場合に、デバイスによって実行されるアクション。 | Device |
| **DvcInboundInterface** | String | eth0 | ファイアウォールなどの中間デバイスによって報告された場合に、送信元デバイスへの接続に使用されるネットワーク インターフェイス。 | Device |
| **DvcOutboundInterface** | String  | ギガビット アダプター イーサネット 4 | ファイアウォールなどの中間デバイスによって報告された場合に、送信先デバイスへの接続に使用されるネットワーク インターフェイス。 | Device |
| **NetworkDuration** | Integer | 1500 | ネットワーク セッションまたは接続の完了にかかる時間 (ミリ秒単位) | ネットワーク |
| **NetworkIcmpCode** | Integer | 34 | ICMP メッセージの場合、ICMP メッセージの種類の数値 (RFC 2780 または RFC 4443)。 | ネットワーク |
| **NetworkIcmpType** | String | 宛先は到達不能です | ICMP メッセージの場合は、ICMP メッセージの種類のテキスト表現 (RFC 2780 または RFC 4443)。 | ネットワーク |
| **DstPackets** | INT  | 446 | 接続またはセッションで送信先から送信元に送信されたパケット数。 パケットの意味はレポート デバイスで定義されます。 | 宛先 |
| **SrcPackets** | INT  | 6478 | 接続またはセッションで送信元から送信先に送信されたパケット数。 パケットの意味はレポート デバイスで定義されます。 | source |
| **NetworkPackets** | INT  | 0 | 両方向に送信されたパケット数。 PacketsReceived と PacketsSent が両方とも存在する場合、BytesTotal はその合計と同じである必要があります。 | ネットワーク |
| **HttpRequestTime** | Integer | 700 | 要求がサーバーに送信されるまでにかかった時間 (該当する場合)。 | Http |
| **HttpResponseTime** | Integer | 800 | サーバーで応答を受け取るまでにかかった時間 (該当する場合)。 | Http |
| **NetworkRuleName** | String | AnyAnyDrop | DeviceAction を決定したときに使用されたルールの名前または ID | ネットワーク |
| **NetworkRuleNumber** | INT |  23 | 一致したルール番号  | ネットワーク |
| **NetworkSessionId** | string | 172_12_53_32_4322__123_64_207_1_80 | レポート デバイスによって報告されたセッション識別子。 たとえば、認証後の特定のアプリケーションの L7 セッション識別子 | ネットワーク |
| **SrcGeoCity** | String | Burlington | 送信元 IP アドレスに関連付けられた都市 | 送信元、<br>ジオ (主要地域) |
| **SrcGeoCountry** | 国 (文字列) | 米国 | 送信元 IP アドレスに関連付けられた国 | 送信元、<br>ジオ (主要地域) |
| **SrcDvcHostname** | デバイス名 (文字列) |  villain | 送信元デバイスのデバイス名 | 送信元、<br>Device |
| **SrcDvcFqdn** | string | Villain.malicious.com | ログが作成されたホストの完全修飾ドメイン名 | 送信元、<br>Device |
| **SrcDvcDomain** | string | EVILORG | セッションを開始したデバイスのドメイン | 送信元、<br>Device |
| **SrcDvcOs** | String | iOS | 送信元デバイスの OS | 送信元、<br>Device |
| **SrcDvcModelName** | String | Samsung Galaxy Note | 送信元デバイスのモデル名 | 送信元、<br>Device |
| **SrcDvcModelNumber** | String | 10.0 | 送信元デバイスのモデル番号 | 送信元、<br>Device |
| **SrcDvcType** | String | モバイル | 送信元デバイスの種類 | 送信元、<br> Device |
| **SrcIntefaceName** | String | eth01 | 送信元デバイスで接続またはセッションに使用されるネットワーク インターフェイス。 | source |
| **SrcInterfaceGuid** | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | 使用されるネットワーク インターフェイスの GUID | source |
| **SrcIpAddr** | IP アドレス | 77.138.103.108 | 接続またはセッションの開始元の IP アドレス。 | 送信元、<br>IP |
| **SrcDvcIpAddr** | IP アドレス | 77.138.103.108 | ネットワーク パケットに直接関連付けられていないデバイスの送信元 IP アドレス (プロバイダーによって収集されるか、明示的に計算されます)。 | 送信元、<br>デバイス<br>IP |
| **SrcGeoLatitude** | 緯度 (倍精度) | 44.475833 | 送信元 IP アドレスに関連付けられている地理的座標の緯度 | 送信元、<br>ジオ (主要地域) |
| **SrcGeoLongitude** | 経度 (倍精度) | -73.211944 | 送信元 IP アドレスに関連付けられている地理的座標の経度 | 送信元、<br>ジオ (主要地域) |
| **SrcMacAddr** | String | 06:10:9f:eb:8f:14 | 接続またはセッションの開始元のネットワーク インターフェイスの MAC アドレス。 | 送信元、<br>Mac |
| **SrcDvcMacAddr** | String | 06:10:9f:eb:8f:14 | ネットワーク パケットに直接関連付けられていないデバイスの送信元 MAC アドレス。 | 送信元、<br>デバイス<br>Mac |
| **SrcPortNumber** | Integer | 2335 | 接続元の IP ポート。 複数の接続を構成するセッションに関連しないことがあります。 | 送信元、<br>ポート |
| **SrcGeoRegion** | リージョン (文字列) | バーモント | 送信元 IP アドレスに関連付けられた国内のリージョン | 送信元、<br>ジオ (主要地域) |
| **SrcResourceId** | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | メッセージを生成しているデバイスのリソース ID。 | source |
| **SrcNatIpAddr** | IP アドレス | 4.3.2.1 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信先との通信に使用する IP アドレス。 | 送信元 NAT、<br>IP |
| **SrcNatPortNumber** | Integer | 345 | ファイアウォールなどの中間 NAT デバイスによって報告された場合は、その NAT デバイスが送信先との通信に使用するポート。 | 送信元 NAT、<br>ポート |
| **SrcUserSid** | ユーザー ID (文字列) | S-15-1445 | セッションの送信元に関連付けられている識別子のユーザー ID。 通常、クライアントに対してアクションを実行するユーザー。 詳細については、「[データ型と形式](#data-types-and-formats)」を参照してください。 | 送信元、<br>User |
| **SrcUserAadId** | 文字列 (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | セッションの送信元側にいるユーザーの Azure AD アカウント オブジェクト ID | 送信元、<br>User |
| **SrcUserName** | ユーザー名 (文字列) | bob | セッションの送信元に関連付けられている識別子のユーザー名。 通常、クライアントに対してアクションを実行するユーザー。 詳細については、「[データ型と形式](#data-types-and-formats)」を参照してください。 | source<br>User |
| **SrcUserUpn** | string | bob@alice.com | セッションを開始するアカウントの UPN | 送信元、<br>User |
| **SrcUserDomain** | string | デスクトップ | セッションを開始するアカウントのドメイン | 送信元、<br>User |
| **SrcZone** | String | タップ | レポート デバイスにより定義された、送信元のネットワーク ゾーン。 | source |
| **NetworkProtocol** | String | TCP | 接続またはセッションで使用される IP プロトコル。 通常は、TCP、UDP、または ICMP | ネットワーク |
| **CloudAppName** | String | Facebook | プロキシによって識別される HTTP アプリケーションの送信先アプリケーションの名前。 | クラウド |
| **CloudAppId** | String | 124 | プロキシによって識別される HTTP アプリケーションの送信先アプリケーションの ID。 これは、通常、使用されるプロキシに固有の値です。 | クラウド |
| **CloudAppOperation** | String | DeleteFile | プロキシによって識別される HTTP アプリケーションの送信先アプリケーションのコンテキストでユーザーが実行した操作。 これは、通常、使用されるプロキシに固有の値です。 | クラウド |
| **CloudAppRiskLevel** | String | 3 | プロキシによって識別される HTTP アプリケーションに関連付けられているリスク レベル。 これは、通常、使用されるプロキシに固有の値です。 | クラウド |
| **FileName** | 文字列型 | ImNotMalicious.exe | ファイル名情報を提供する FTP や HTTP などのプロトコルのネットワーク接続で送信されるファイル名。 | File |
| **FilePath** | String | C:\Malicious\ImNotMalicious.exe | ファイル名を含んだ、ファイルの完全パス | ファイル |
| **FileHashMd5** | String | 51BC68715FC7C109DCEA406B42D9D78F | プロトコルのネットワーク接続を介して送信されるファイルの MD5 ハッシュ値。 | ファイル |
| **FileHashSha1** | String | 491AE3…C299821476F4 | プロトコルのネットワーク接続を介して送信されるファイルの SHA1 ハッシュ値。 | ファイル |
| **FileHashSha256** | String | 9B8F8EDB…C129976F03 | プロトコルのネットワーク接続を介して送信されるファイルの SHA256 ハッシュ値。 | ファイル |
| **FileHashSha512** | String | 5E127D…F69F73F01F361 | プロトコルのネットワーク接続を介して送信されるファイルの SHA512 ハッシュ値。 | ファイル |
| **FileExtension** |  String | exe | FTP や HTTP などのプロトコルのネットワーク接続を介して送信されるファイルの SHA256 ハッシュ値。 | ファイル
| **FileMimeType** | String | application/msword | FTP や HTTP などのプロトコルのネットワーク接続を介して送信されるファイルの MIME の種類 | ファイル |
| **FileSize** | Integer | 23500 | プロトコルのネットワーク接続を介して送信されるファイルのファイルサイズ (バイト単位)。 | ファイル |
| **HttpVersion** | String | 2.0 | HTTP/HTTPS ネットワーク接続の HTTP 要求バージョン。 | Http |
| **HttpRequestMethod** | String | GET | HTTP/HTTPS ネットワーク セッションの HTTP メソッド。 | Http |
| **HttpStatusCode** | String | 404 | HTTP/HTTPS ネットワーク セッションの HTTP 状態コード。 | Http |
| **HttpContentType** | String | multipart/form-data; boundary=something | HTTP/HTTPS ネットワーク セッションの HTTP 応答のコンテンツ タイプのヘッダー。 | Http |
| **HttpReferrerOriginal** | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS ネットワーク セッションの HTTP 参照元ヘッダー。 | Http |
| **HttpUserAgentOriginal** | String | Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML、like Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS ネットワーク セッションの HTTP ユーザー エージェント ヘッダー。 | Http |
| **HttpRequestXff** | String | 120.12.41.1 | HTTP/HTTPS ネットワーク セッションの HTTP X-Forwarded-For ヘッダー。 | Http |
| **UrlCategory** | String | 検索エンジン | コンテンツの内容に関連する、URL 内のドメインに基づいていて定義される可能性がある URL のグループ化。 たとえば、成人向け、ニュース、広告、パーク ドメインなど。 | url |
| **UrlOriginal** | String | https:// contoso.com/fo/?k=v&q=u#f | HTTP/HTTPS ネットワーク セッションの HTTP 要求 URL。 | url |
| **UrlHostname** | String | contoso.com | HTTP/HTTPS ネットワーク セッションの HTTP 要求 URL のドメイン部分。 | url |
| **ThreatCategory** | String | Trojan | IPS の Web Security Gateway などのセキュリティ システムによって識別され、このネットワーク セッションに関連付けられている脅威のカテゴリ。 | 脅威 |
| **ThreatId** | String | Tr.124 | IP の Web セキュリティゲートウェイなどのセキュリティシステムによって識別され、このネットワークセッションに関連付けられている脅威の ID。 | 脅威 |
| **ThreatName** | String | EICAR テスト ファイル | 識別された脅威またはマルウェアの名前 | 脅威 |
| **AdditionalFields** | 動的 (JSON バッグ) | {<br>Property1: "val1"、<br>Property2: "val2"<br>} | スキーマ内のそれぞれの列が一致しない場合、他のフィールドを JSON バッグに格納できます。<br>クエリ時の解析では、JSON コードにデータをパックするとクエリのパフォーマンスが低下するので、JSON バッグを使用するのでなく、追加の列を昇格させることをお勧めします。 | カスタム フィールド |
|


## <a name="differences-between-the-version-01-and-version-02"></a><a name="changes"></a>バージョン 0.1 とバージョン 0.2 の相違点

Microsoft Azure Sentinel のネットワーク セッション正規化スキーマ バージョン 0.1 の元のバージョンは、ASIM が使用可能になる前にプレビューとしてリリースされました。

この記事で説明されているバージョン 0.1 と[バージョン 0.2](network-normalization-schema.md) の相違点を次に示します。

- バージョン 0.2 では、標準の ASIM 名前付け規則に準拠するように、ソースに依存しないソース固有のパーサー名が変更されています。
- バージョン 0.2 では、特定のデバイスの種類に対応するための特定のガイドラインとソースに依存しないパーサーが追加されています。

以下のセクションでは、特定のフィールドについて[バージョン 0.2](network-normalization-schema.md) がどのように異なるかについて説明します。

### <a name="added-fields-in-version-02"></a>バージョン 0.2 で追加されたフィールド

次のフィールドは[バージョン 0.2](network-normalization-schema.md) で追加され、バージョン 0.1 には存在しません。

:::row:::
   :::column span="":::
      - DstAppType
      - DstDeviceType
      - DstDomainType
      - DstDvcId
      - DstDvcIdType
      - DstOriginalUserType
      - DstUserIdType
      - DstUsernameType
      - DstUserType
      - DvcActionOriginal
      - DvcDomain
   :::column-end:::
   :::column span="":::
      - DvcDomainType
      - DvcFQDN
      - DvcId
      - DvcIdType
      - DvcIdType
      - EventOriginalSeverity
      - EventOriginalType
      - SrcAppId
      - SrcAppName
      - SrcAppType
   :::column-end:::
   :::column span="":::
      - SrcDeviceType
      - SrcDomainType
      - SrcDvcId
      - SrcDvcIdType
      - SrcOriginalUserType
      - SrcUserIdType
      - SrcUsernameType
      - SrcUserType
      - ThreatRiskLevelOriginal
      - url
   :::column-end:::
:::row-end:::


### <a name="newly-aliased-fields-in-version-02"></a>バージョン 0.2 で新しく別名が付けられたフィールド

[バージョン 0.2](network-normalization-schema.md) では、ASIM が導入され、次のフィールドに別名が付けられました。

|バージョン 0.1 のフィールド  |バージョン 0.2 の別名  |
|---------|---------|
|SessionId     |     NetworkSessionId    |
|期間     |     NetworkDuration    |
|IpAddr     | SrcIpAddr        |
|User     |     DstUsername    |
|Hostname (ホスト名)     |   DstHostname      |
|UserAgent     |     HttpUserAgent    |
|     |         |

### <a name="modified-fields-in-version-02"></a>バージョン 0.2 で変更されたフィールド

次のフィールドは[バージョン 0.2](network-normalization-schema.md) で列挙され、指定されたリストの特定の値が必要です。

- EventType
- EventResultDetails
- EventSeverity

### <a name="renamed-fields-in-version-02"></a>バージョン 0.2 で名前が変更されたフィールド

次のフィールドの名前は[バージョン 0.2](network-normalization-schema.md) で変更されました。

- **バージョン 0.2 では、組み込みの Log Analytics フィールドを使用します。**

    `ingestion_time()` は KQL 関数であり、フィールド名ではないことに注意してください。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  EventResourceId  |   _ResourceId      |
    | EventUid   |     _ItemId    |
    | EventTimeIngested   |  ingestion_time()       |
    |    |         |

- **ASIM と OSSEM の機能強化と整合させるために名前が変更されました**。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  HttpReferrerOriginal  |   HttpReferrer      |
    | HttpUserAgentOriginal   |     HttpUserAgent    |
    |    |         |

- **ネットワーク セッションの宛先がクラウド サービスである必要がないことを反映するために名前変更されました**。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  CloudAppId  |   DstAppId      |
    | CloudAppName   |     DstAppName    |
    | CloudAppRiskLevel   |  ThreatRiskLevel       |
    |    |         |

- **大文字と小文字を変更し、ユーザー エンティティ の ASIM 処理と整合させるために名前変更されました**。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  DstUserName  |   DstUsername      |
    | SrcUserName   |     SrcUsername    |
    |    |         |

- **ASIM デバイス エンティティとの整合性を向上させて、Azure 以外のリソース ID を許可するために名前変更されました**。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  DstResourceId  |   SrcDvcAzureRerouceId      |
    | SrcResourceId   |     SrcDvcAzureRerouceId    |
    |    |         |

- **バージョン 0.1 での処理が不整合だったので、フィールド名から `Dvc` 文字列を削除するために名前変更されました**。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  DstDvcDomain  |   DstDomain      |
    | DstDvcFqdn   |     DstFqdn    |
    |  DstDvcHostname  |   DstHostname      |
    | SrcDvcDomain   |     SrcDomain    |
    |  SrcDvcFqdn  |   SrcFqdn      |
    | SrcDvcHostname   |     SrcHostname    |
    |    |         |

- **ASIM ファイル表現ガイダンスと整合させるために名前変更されました**。

    |バージョン 0.1 のフィールド  |バージョン 0.2 で名前変更済み  |
    |---------|---------|
    |  FileHashMd5  |   FileMD5      |
    | FileHashSha1   |     FileSHA1    |
    |  FileHashSha256  |   FileSHA256      |
    | FileHashSha512   |     FileSHA512    |
    |  FileMimeType  |   FileContentType      |
    |    |         |

### <a name="removed-fields-in-version-02"></a>バージョン 0.2 で削除されたフィールド

次のフィールドはバージョン 0.1 にのみ存在し、[バージョン 0.2](network-normalization-schema.md) では削除されています。

|理由  |削除されたフィールド  |
|---------|---------|
|**フィールド名に `Dvc` 文字列が含まれておらず重複が存在するため、削除されました**     |  - DstDvcIpAddr<br> - DstDvcMacAddr<br>- SrcDvcIpAddr<br>- SrcDvcMacAddr       |
|**URL の ASIM 処理と整合させるために削除されました**     |  - UrlHostname       |
|**これらのフィールドは通常、ネットワーク セッション イベントの一部として提供されないため、削除されました。**<br><br>イベントにこれらのフィールドが含まれている場合は、[プロセス イベント スキーマ](process-events-normalization-schema.md)を使用して、デバイスのプロパティを記述する方法を理解します。 |     - SrcDvcOs<br>-&nbsp;SrcDvcModelName<br>-&nbsp;SrcDvcModelNumber<br>- DvcMacAddr<br>- DvcOs         |
|**ASIM ファイル表現ガイダンスと整合させるために名前変更されました**     |   - FilePath<br>- FileExtension      |
|**このフィールドは、[認証スキーマ](authentication-normalization-schema.md)などの別のスキーマを使用する必要があることを示しているため、削除されました。**     |  - CloudAppOperation       |
|**重複しているため削除されました`DstHostname`**     |  - DstDomainHostname         |
|     |         |


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Sentinel での正規化](normalization.md)
- [Microsoft Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Microsoft Azure Sentinel DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Microsoft Sentinel プロセス イベント正規化スキーマ リファレンス](process-events-normalization-schema.md)
- [Microsoft Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)
