---
title: Azure API Management のキャッシュ ポリシー | Microsoft Docs
description: Azure API Management で使用できるキャッシュ ポリシーについて説明します。 例を参照し、使用可能なその他のリソースを確認します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/08/2021
ms.author: danlep
ms.openlocfilehash: 014891a9ed16e075b8f21c0a177a38d416f2f3a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629713"
---
# <a name="api-management-caching-policies"></a>API Management のキャッシュ ポリシー

この記事では、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](./api-management-policies.md)」をご覧ください。

> [!IMPORTANT]
> 組み込みのキャッシュは揮発性であり、同じ API Management サービスの同じリージョン内のすべてのユニットによって共有されます。

## <a name="caching-policies"></a><a name="CachingPolicies"></a> キャッシュ ポリシー

- 応答キャッシュ ポリシー
    - [キャッシュから取得](#GetFromCache) - キャッシュを検索して、キャッシュに格納された有効な応答があればそれを返します。
    - [キャッシュに格納](#StoreToCache) - 指定されたキャッシュ制御の構成に従って応答をキャッシュに格納します。
- 値キャッシュ ポリシー
    - [キャッシュから値を取得](#GetFromCacheByKey) - キャッシュされたキー別の項目を取得します。
    - [値をキャッシュに格納](#StoreToCacheByKey) - 項目をキー別にキャッシュに格納します。
    - [キャッシュから値を削除](#RemoveCacheByKey) - キー別にキャッシュ内の項目を削除します。

## <a name="get-from-cache"></a><a name="GetFromCache"></a> キャッシュから取得
`cache-lookup` ポリシーを使用し、キャッシュを検索して、キャッシュに格納された有効な応答ががあればそれを返します。 このポリシーを適用できるのは、応答の内容が一定期間にわたって静的である場合です。 応答のキャッシュを使用すると、バックエンド Web サーバーの帯域幅および処理の要件が低減され、API コンシューマーによって認識される遅延が小さくなります。

> [!NOTE]
> このポリシーには、対応する[キャッシュに格納](#StoreToCache)ポリシーが必要です。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

> [!NOTE]
> `vary-by-query-parameter` の使用時は、rewrite-uri テンプレートでパラメーターを宣言するか、または `copy-unmatched-params` 属性を `false` に設定することをお勧めします。 このフラグを非アクティブ化すると、宣言されていないパラメーターはバックエンドに送信されます。

### <a name="examples"></a>例

#### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>ポリシー式の使用例
この例は、API Management 応答のキャッシュ時間を、バックエンド サービスの `Cache-Control` ディレクティブによって指定されたバックエンド サービスの応答キャッシュ時間と一致するように構成する方法を示します。 このポリシーの構成と使用についてのデモは、「[Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」(クラウド カバー エピソード 177: Vlad Vinogradsky によるその他の API Management 機能の紹介) を 25:25 まで早送りしてご覧ください。

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

詳細については、[ポリシー式](api-management-policy-expressions.md)および[コンテキスト変数](api-management-policy-expressions.md#ContextVariables)に関する各ページを参照してください。

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|cache-lookup|ルート要素。|はい|
|vary-by-header|指定されたヘッダーの値 (Accept、Accept-Charset、Accept-Encoding、Accept-Language、Authorization、Expect、From、Host、If-Match など) ごとに応答をキャッシュに格納します。|いいえ|
|vary-by-query-parameter|指定されたクエリ パラメーターの値ごとに応答をキャッシュに格納します。 1 つまたは複数のパラメーターを入力します。 セミコロンを区切り文字として使用します。 パラメーターを指定しない場合、すべてのクエリ パラメーターが使用されます。|いいえ|

### <a name="attributes"></a>属性

| 名前                           | 説明                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | `true` に設定すると、承認ヘッダーを含む要求をキャッシュできます。                                                                                                                                                                                                                                                                        | いいえ       | false             |
| caching-type               | 属性の次の値のいずれかを選択します。<br />- `internal` (組み込みの API Management キャッシュを使用する場合)、<br />- `external` (「[Azure API Management で外部の Azure Cache for Redis を使用する](api-management-howto-cache-external.md)」の説明に従って、外部キャッシュを使用する場合)、<br />- `prefer-external` (構成されている場合は外部キャッシュ、そうでない場合は内部キャッシュを使用する場合)。 | いいえ       | `prefer-external` |
| downstream-caching-type        | この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   none - ダウンストリーム キャッシュは許可されません。<br />-   private - ダウンストリーム プライベート キャッシュが許可されます。<br />-   public - プライベートおよび共有ダウンストリーム キャッシュが許可されます。                                                                                                          | いいえ       | なし              |
| must-revalidate                | ダウンストリーム キャッシュが有効になっているとき、この属性によって、ゲートウェイ応答での `must-revalidate` キャッシュ制御ディレクティブのオンとオフを切り替えます。                                                                                                                                                                                                                      | いいえ       | true              |
| vary-by-developer              | 要求に含まれている[サブスクリプション キー](./api-management-subscriptions.md)を所有している開発者アカウントごとに応答をキャッシュする場合は、`true` に設定します。                                                                                                                                                                                                                                                                                                  | はい      |         False          |
| vary-by-developer-groups       | `true` に設定すると、[ユーザー グループ](./api-management-howto-create-groups.md)ごとに応答をキャッシュします。                                                                                                                                                                                                                                                                                                             | はい      |       False            |

### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound
- **ポリシー スコープ:** すべてのスコープ

## <a name="store-to-cache"></a><a name="StoreToCache"></a> キャッシュに格納
`cache-store`ポリシーは、指定されたキャッシュ設定に従って応答をキャッシュに格納します。 このポリシーを適用できるのは、応答の内容が一定期間にわたって静的である場合です。 応答のキャッシュを使用すると、バックエンド Web サーバーの帯域幅および処理の要件が低減され、API コンシューマーによって認識される遅延が小さくなります。

> [!NOTE]
> このポリシーには、対応する[キャッシュから取得](api-management-caching-policies.md#GetFromCache)ポリシーが必要です。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<cache-store duration="seconds" cache-response="true | false" />
```

### <a name="examples"></a>例

#### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>ポリシー式の使用例
この例は、API Management 応答のキャッシュ時間を、バックエンド サービスの `Cache-Control` ディレクティブによって指定されたバックエンド サービスの応答キャッシュ時間と一致するように構成する方法を示します。 このポリシーの構成と使用についてのデモは、「[Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」(クラウド カバー エピソード 177: Vlad Vinogradsky によるその他の API Management 機能の紹介) を 25:25 まで早送りしてご覧ください。

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

詳細については、[ポリシー式](api-management-policy-expressions.md)および[コンテキスト変数](api-management-policy-expressions.md#ContextVariables)に関する各ページを参照してください。

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|cache-store|ルート要素。|はい|

### <a name="attributes"></a>属性

| 名前             | 説明                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | キャッシュに格納されたエントリの有効期間 (秒単位)。     | はい      | 該当なし               |
| cache-response         | 現在の HTTP 応答をキャッシュする場合は true に設定します。 属性が省略されている場合、または false に設定されている場合は、状態コード `200 OK` を含む HTTP 応答のみがキャッシュされます。                           | いいえ      | false               |

### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** outbound
- **ポリシー スコープ:** すべてのスコープ

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> キャッシュから値を取得
`cache-lookup-value` ポリシーを使用して、キーごとにキャッシュの検索を行い、キャッシュされている値を返します。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。

> [!NOTE]
> このポリシーには、対応する[値をキャッシュに格納](#StoreToCacheByKey)ポリシーが必要です。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>例
このポリシーの詳細と例については、「[Custom caching in Azure API Management](./api-management-sample-cache-by-key.md)」(Azure API Management でのカスタム キャッシュ) を参照してください。

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|cache-lookup-value|ルート要素。|はい|

### <a name="attributes"></a>属性

| 名前             | 説明                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 属性の次の値のいずれかを選択します。<br />- `internal` (組み込みの API Management キャッシュを使用する場合)、<br />- `external` (「[Azure API Management で外部の Azure Cache for Redis を使用する](api-management-howto-cache-external.md)」の説明に従って、外部キャッシュを使用する場合)、<br />- `prefer-external` (構成されている場合は外部キャッシュ、そうでない場合は内部キャッシュを使用する場合)。 | いいえ       | `prefer-external` |
| default-value    | キーによるキャッシュ検索で何も見つからなかった場合に、変数に割り当てられる値。 この属性が指定されない場合は `null` が割り当てられます。                                                                                                                                                                                                           | いいえ       | `null`            |
| key              | 検索で使用するキャッシュのキー値。                                                                                                                                                                                                                                                                                                                       | はい      | 該当なし               |
| variable-name    | 検索が成功した場合に、検索された値が割り当てられる[コンテキスト変数](api-management-policy-expressions.md#ContextVariables)の名前。 検索結果が見つからない場合、変数は設定されません。                                       | はい      | 該当なし               |

### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound、outbound、backend、on-error
- **ポリシー スコープ:** すべてのスコープ

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> 値をキャッシュに格納
`cache-store-value` は、キーごとに記憶域のキャッシュを実行します。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。

> [!NOTE]
> このポリシーによって実行される、キャッシュに値を格納する操作は非同期です。 格納された値は、[キャッシュから値を取得](#GetFromCacheByKey)ポリシーを使用して取得できます。 ただし、キャッシュに値を格納する非同期操作がまだ進行中である可能性もあるため、格納されている値をすぐに使用できない場合があります。 

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>例
このポリシーの詳細と例については、「[Custom caching in Azure API Management](./api-management-sample-cache-by-key.md)」(Azure API Management でのカスタム キャッシュ) を参照してください。

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|cache-store-value|ルート要素。|はい|

### <a name="attributes"></a>属性

| 名前             | 説明                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 属性の次の値のいずれかを選択します。<br />- `internal` (組み込みの API Management キャッシュを使用する場合)、<br />- `external` (「[Azure API Management で外部の Azure Cache for Redis を使用する](api-management-howto-cache-external.md)」の説明に従って、外部キャッシュを使用する場合)、<br />- `prefer-external` (構成されている場合は外部キャッシュ、そうでない場合は内部キャッシュを使用する場合)。 | いいえ       | `prefer-external` |
| duration         | 指定された期間 (秒単位)、値がキャッシュされます。                                                                                                                                                                                                                                                                                 | はい      | 該当なし               |
| key              | 値が格納されるキャッシュのキー。                                                                                                                                                                                                                                                                                                                   | はい      | 該当なし               |
| value            | キャッシュされる値。                                                                                                                                                                                                                                                                                                                                     | はい      | 該当なし               |
### <a name="usage"></a>使用法
このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound、outbound、backend、on-error
- **ポリシー スコープ:** すべてのスコープ

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> キャッシュから値を削除
`cache-remove-value` は、キーで指定された、キャッシュされている項目を削除します。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。

#### <a name="policy-statement"></a>ポリシー ステートメント

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>例

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>要素

|名前|説明|必須|
|----------|-----------------|--------------|
|cache-remove-value|ルート要素。|はい|

#### <a name="attributes"></a>属性

| 名前             | 説明                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 属性の次の値のいずれかを選択します。<br />- `internal` (組み込みの API Management キャッシュを使用する場合)、<br />- `external` (「[Azure API Management で外部の Azure Cache for Redis を使用する](api-management-howto-cache-external.md)」の説明に従って、外部キャッシュを使用する場合)、<br />- `prefer-external` (構成されている場合は外部キャッシュ、そうでない場合は内部キャッシュを使用する場合)。 | いいえ       | `prefer-external` |
| key              | キャッシュから削除される、前にキャッシュされた値のキー。                                                                                                                                                                                                                                                                                        | はい      | 該当なし               |

#### <a name="usage"></a>使用法
このポリシーは、ポリシーの以下の[セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound、outbound、backend、on-error
- **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
+ [ポリシーのサンプル](./policy-reference.md)
