---
title: Azure API Management のアクセス制限ポリシー | Microsoft Docs
description: Azure API Management で使用できるアクセス制限ポリシーについて説明します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 08/20/2021
ms.author: danlep
ms.openlocfilehash: 32fa405a612026fc16257447cb2cc858101c729c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536507"
---
# <a name="api-management-access-restriction-policies"></a>API Management のアクセス制限ポリシー

このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](./api-management-policies.md)」をご覧ください。

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> アクセス制限ポリシー

-   [HTTP ヘッダーを確認する](#CheckHTTPHeader) - HTTP ヘッダーの存在と値の両方、またはそのどちらかを適用します。
-   [呼び出しレートをサブスクリプション別に制限する](#LimitCallRate) - 呼び出しレートをサブスクリプションに基づいて制限することで、API の使用量の急増を防ぎます。
-   [呼び出しレートをキー別に制限する](#LimitCallRateByKey) - 呼び出しレートをキーに基づいて制限することで、API の使用量の急増を防ぎます。
-   [呼び出し元 IP を制限する](#RestrictCallerIPs) - 特定の IP アドレスとアドレス範囲の両方、またはそのどちらかからの呼び出しをフィルター処理 (許可/拒否) します。
-   [使用量のクォータをサブスクリプション別に設定する](#SetUsageQuota) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをサブスクリプションに基づいて適用できます。
-   [使用量のクォータをキー別に設定する](#SetUsageQuotaByKey) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをキーに基づいて適用できます。
-   [JWT を検証する](#ValidateJWT) - 指定された HTTP ヘッダーまたは指定されたクエリ パラメーターから抽出した JWT の存在と有効性を適用します。
-  [クライアント証明書の検証](#validate-client-certificate) -クライアントから API Management インスタンスに提示された証明書が、指定された検証規則と要求に一致することを強制します。

> [!TIP]
> さまざまな目的に応じて異なるスコープでアクセス制限ポリシーを使用できます。 たとえば、API 全体を AAD 認証を使用して保護するには、`validate-jwt` ポリシーを API レベルに適用します。または、これを API 操作レベルに適用して、`claims` を使用してきめ細かい制御を行うこともできます。

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP ヘッダーを確認する

`check-header` ポリシーを使用して、HTTP ヘッダーの指定がもれなく要求に含まれるようにします。 必要に応じて、ヘッダーに特定の値が指定されているかどうか、または許可されている範囲内の値かどうかを確認できます。 チェックに失敗した場合、このポリシーに従って要求の処理は終了し、ポリシーで指定した HTTP 状態コードとエラー メッセージが返されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>例

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>要素

| 名前         | 説明                                                                                                                                   | 必須 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | ルート要素。                                                                                                                                 | はい      |
| value        | 許可される HTTP ヘッダーの値。 複数の要素を指定した場合、いずれかの値に一致すればチェックは成功とみなされます。 | いいえ       |

### <a name="attributes"></a>属性

| 名前                       | 説明                                                                                                                                                            | 必須 | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | ヘッダーが存在しないかヘッダーが無効な値である場合に HTTP 応答本文で返されるエラー メッセージ。 このメッセージ内では、特殊文字を適切にエスケープする必要があります。 | はい      | 該当なし     |
| failed-check-httpcode      | ヘッダーが存在しないかヘッダーが無効な値である場合に返される HTTP 状態コード。                                                                                        | はい      | 該当なし     |
| header-name                | チェックする HTTP ヘッダーの名前。                                                                                                                                  | はい      | 該当なし     |
| ignore-case                | True または False に設定できます。 True に設定した場合、ヘッダー値と許容される値セットとの比較時に大文字と小文字は区別されません。                                    | はい      | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> 呼び出しレートをサブスクリプション別に制限する

`rate-limit` ポリシーは、指定期間あたりの呼び出しレートを指定数に制限することで、サブスクリプションごとに API 使用量の急増を防ぎます。 この呼び出しレートを超えると、呼び出し元は `429 Too Many Requests` 応答状態コードを受信します。

> [!IMPORTANT]
> このポリシーは、ポリシー ドキュメントごとに 1 回のみ使用できます。
>
> このポリシー内のポリシー属性では、[ポリシー式](api-management-policy-expressions.md)は使用できません。

> [!CAUTION]
> スロットリングのアーキテクチャは分散型の性質のため、レートの制限は完全に正確ではありません。 許可される要求の構成された数と実際の数の差異は、要求のボリュームとレート、バックエンドの待ち時間、その他の要因によって異なります。

> [!NOTE]
> レート上限とクォータの違いについては、[レート上限とクォータ](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)に関するページを参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds">
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>例

次の例では、サブスクリプションあたりのレート上限が 90 秒ごとに 20 回です。 ポリシーを実行するたびに、その期間内に許可されている残りの呼び出しが変数 `remainingCallsPerSubscription` に格納されます。

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>要素

| 名前       | 説明                                                                                                                                                                                                                                                                                              | 必須 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | ルート要素。                                                                                                                                                                                                                                                                                            | はい      |
| api        | 製品内の API に対して呼び出しレート制限をかけるには、これらの要素を 1 つまたは複数追加します。 製品と API の呼び出しレート制限は別々に適用されます。 API は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。                    | いいえ       |
| 操作  | API 内の操作に対して呼び出しレート制限をかけるには、これらの要素を 1 つまたは複数追加します。 製品、API、および操作の呼び出しレート制限は別々に適用されます。 操作は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。 | いいえ       |

### <a name="attributes"></a>属性

| 名前           | 説明                                                                                           | 必須 | Default |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | レート制限の適用対象になる API の名前。                                                | はい      | 該当なし     |
| calls          | `renewal-period` で指定された期間中に許可される最大の呼び出し合計数。 | はい      | 該当なし     |
| renewal-period | 許可された要求の数が、`calls` で指定された値を超えてはならないスライディング ウィンドウの長さ (秒単位)。 許可される最大値: 300 秒。                                            | はい      | 該当なし     |
| retry-after-header-name    | 値が指定された呼び出しレートを超えた後の推奨される再試行間隔 (秒単位) である応答ヘッダーの名前。 |  いいえ | N/A  |
| retry-after-variable-name    | 指定した呼び出しレートを超えた後の推奨される再試行間隔 (秒単位) を格納するポリシー式変数の名前。 |  いいえ | N/A  |
| remaining-calls-header-name    | 各ポリシーの実行後の値が、`renewal-period` で指定された時間間隔に対して許可されている残りの呼び出しの数である応答ヘッダーの名前。 |  いいえ | 該当なし  |
| remaining-calls-variable-name    | 各ポリシーの実行後に、`renewal-period` で指定された時間間隔に対して許可されている残りの呼び出しの数を格納する、ポリシー式変数の名前。 |  いいえ | N/A  |
| total-calls-header-name    | 値が `calls` で指定された値である応答ヘッダーの名前。 |  いいえ | 該当なし  |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** 製品、API、操作

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> 呼び出しレートをキー別に制限する

> [!IMPORTANT]
> この機能は、API Management の **従量課金** レベルでは使用できません。

`rate-limit-by-key` ポリシーは、指定期間あたりの呼び出しレートを指定数に制限することで、キーごとに API 使用量の急増を防ぎます。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。 必要に応じて増分条件を追加し、制限に対してカウントする要求を指定することもできます。 この呼び出しレートを超えると、呼び出し元は `429 Too Many Requests` 応答状態コードを受信します。

このポリシーの詳細と例については、「[Azure API Management を使用した高度な要求スロットル](./api-management-sample-flexible-throttling.md)」を参照してください。

> [!CAUTION]
> スロットリングのアーキテクチャは分散型の性質のため、レートの制限は完全に正確ではありません。 許可される要求の構成された数と実際の数の差異は、要求のボリュームとレート、バックエンドの待ち時間、その他の要因によって異なります。

> [!NOTE]
> レート上限とクォータの違いについては、[レート上限とクォータ](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)に関するページを参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>例

次の例では、60 秒ごとに 10 回のレート制限のキーに呼び出し元 IP を設定しています。 ポリシーを実行するたびに、その期間内に許可されている残りの呼び出しが変数 `remainingCallsPerIP` に格納されます。

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>要素

| 名前              | 説明   | 必須 |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 名前                | 説明                                                                                           | 必須 | Default |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | `renewal-period` で指定した期間中に許容する最大呼び出し総数。 ポリシー式は許可されます。 | はい      | 該当なし     |
| counter-key         | レート制限ポリシーに使用するキー。                                                             | はい      | 該当なし     |
| increment-condition | レートに対して要求の件数をカウントするかどうかを指定するブール式 (`true`)。        | いいえ       | 該当なし     |
| renewal-period      | 許可された要求の数が、`calls` で指定された値を超えてはならないスライディング ウィンドウの長さ (秒単位)。 ポリシー式は許可されます。 許可される最大値: 300 秒。                 | はい      | 該当なし     |
| retry-after-header-name    | 値が指定された呼び出しレートを超えた後の推奨される再試行間隔 (秒単位) である応答ヘッダーの名前。 |  いいえ | N/A  |
| retry-after-variable-name    | 指定した呼び出しレートを超えた後の推奨される再試行間隔 (秒単位) を格納するポリシー式変数の名前。 |  いいえ | N/A  |
| remaining-calls-header-name    | 各ポリシーの実行後の値が、`renewal-period` で指定された時間間隔に対して許可されている残りの呼び出しの数である応答ヘッダーの名前。 |  いいえ | N/A  |
| remaining-calls-variable-name    | 各ポリシーの実行後に、`renewal-period` で指定された時間間隔に対して許可されている残りの呼び出しの数を格納する、ポリシー式変数の名前。 |  いいえ | N/A  |
| total-calls-header-name    | 値が `calls` で指定された値である応答ヘッダーの名前。 |  いいえ | 該当なし  |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> 呼び出し元 IP を制限する

`ip-filter` ポリシーは、特定の IP アドレスやアドレス範囲からの呼び出しをフィルター処理 (許可または拒否) します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>例

次の例のポリシーでは、指定された単一の IP アドレスまたは IP アドレス範囲のどちらかから受け取った要求しか許可されません。

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>要素

| 名前                                      | 説明                                         | 必須                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | ルート要素。                                       | はい                                                            |
| address                                   | フィルターを適用する単一の IP アドレスを指定します。   | `address` 要素または `address-range` 要素は少なくとも 1 つ必要です。 |
| address-range from="address" to="address" | フィルターを適用する IP アドレスの範囲を指定します。 | `address` 要素または `address-range` 要素は少なくとも 1 つ必要です。 |

### <a name="attributes"></a>属性

| 名前                                      | 説明                                                                                 | 必須                                           | Default |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | アクセスを許可または拒否する IP アドレスの範囲。                                        | `address-range` 要素を使用する場合は必須です。 | 該当なし     |
| ip-filter action="allow &#124; forbid"    | 指定した IP アドレスおよび IP アドレス範囲に対する呼び出しを許可するかどうかを指定します。 | はい                                                | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound
-   **ポリシー スコープ:** すべてのスコープ

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> 使用量のクォータをサブスクリプション別に設定する

`quota` ポリシーは、更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをサブスクリプションに基づいて適用します。

> [!IMPORTANT]
> このポリシーは、ポリシー ドキュメントごとに 1 回のみ使用できます。
>
> [ポリシー式](api-management-policy-expressions.md)は、このポリシーのポリシー属性に使用できません。

> [!NOTE]
> レート上限とクォータの違いについては、[レート上限とクォータ](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)に関するページを参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>要素

| 名前      | 説明                                                                                                                                                                                                                                                                                  | 必須 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | ルート要素。                                                                                                                                                                                                                                                                                | はい      |
| api       | 製品内の API に対して呼び出しクォータをかけるには、これらの要素を 1 つまたは複数追加します。 製品と API の呼び出しクォータは別々に適用されます。 API は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。                    | いいえ       |
| 操作 | API 内の操作に対して呼び出しクォータをかけるには、これらの要素を 1 つまたは複数追加します。 製品、API、および操作の呼び出しクォータは別々に適用されます。 操作は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。 | いいえ      |

### <a name="attributes"></a>属性

| 名前           | 説明                                                                                               | 必須                                                         | Default |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | クォータを適用する API または操作の名前。                                             | はい                                                              | 該当なし     |
| bandwidth      | `renewal-period` で指定した期間中に許可する最大合計キロバイト数。 | `calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。 | 該当なし     |
| calls          | `renewal-period` で指定した期間中に許容する最大呼び出し総数。     | `calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。 | 該当なし     |
| renewal-period | クォータのリセット間隔 (秒単位)。 `0` に設定すると、この期間は無限に設定されます。 | はい                                                              | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound
-   **ポリシー スコープ:** 製品

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> 使用量のクォータをキー別に設定する

> [!IMPORTANT]
> この機能は、API Management の **従量課金** レベルでは使用できません。

`quota-by-key` ポリシーは、更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをキーに基づいて適用します。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。 必要に応じて増分条件を追加し、クォータに対してカウントする要求を指定することもできます。 複数のポリシーによって同じキー値が増分される場合は、要求ごとに 1 回だけ増分されます。 この呼び出しレートを超えると、呼び出し元は `403 Forbidden` 応答状態コードを受信します。

このポリシーの詳細と例については、「[Azure API Management を使用した高度な要求スロットル](./api-management-sample-flexible-throttling.md)」を参照してください。

> [!NOTE]
> レート上限とクォータの違いについては、[レート上限とクォータ](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)に関するページを参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>例

次のサンプルでは、クォータのキーに呼び出し元 IP を設定しています。

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>要素

| 名前  | 説明   | 必須 |
| ----- | ------------- | -------- |
| quota | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 名前                | 説明                                                                                               | 必須                                                         | Default |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | `renewal-period` で指定した期間中に許可する最大合計キロバイト数。 | `calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。 | 該当なし     |
| calls               | `renewal-period` で指定した期間中に許容する最大呼び出し総数。     | `calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。 | 該当なし     |
| counter-key         | クォータ ポリシーに使用するキー。                                                                      | はい                                                              | 該当なし     |
| increment-condition | クォータに対して要求の件数をカウントするかどうかを指定するブール式 (`true`)             | いいえ                                                               | 該当なし     |
| renewal-period      | クォータのリセット間隔 (秒単位)。 `0` に設定すると、この期間は無限に設定されます。                                                   | はい                                                              | 該当なし     |

> [!NOTE]
> `counter-key` 属性値は、他の API 間で合計値を共有したくない場合は、API Management のすべての API で一意でなければなりません。

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound
-   **ポリシー スコープ:** すべてのスコープ

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT を検証する

`validate-jwt` ポリシーは、指定した HTTP ヘッダーまたは指定したクエリ パラメーターのどちらかから抽出した JSON Web トークン (JWT) が存在し、有効であることを必須にします。

> [!IMPORTANT]
> `validate-jwt` ポリシーは、`require-expiration-time` 属性を指定し `false` に設定した場合を除いて、`exp` 登録クレームが JWT トークンに含まれていることを必須にします。
> `validate-jwt` ポリシーでは HS256 署名アルゴリズムと RS256 署名アルゴリズムがサポートされています。 HS256 の場合、キーをポリシー内に base64 エンコード形式でインライン指定する必要があります。 RS256 の場合、キーは Open ID 構成エンドポイント経由で提供されるか、公開キーまたは公開キーのモジュラスとエクスポーネントのペアを含むアップロードされた証明書の ID を提示することによって提供されます。
> `validate-jwt` ポリシーでは、暗号化アルゴリズムA128CBC-HS256、A192CBC-HS384、A256CBC-HS512 を使用して対称キーで暗号化されたトークンがサポートされます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>例

#### <a name="simple-token-validation"></a>単純なトークン検証

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>RSA 証明書を使用したトークン検証

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory のトークン検証

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C のトークン検証

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>トークン クレームに基づいて操作へのアクセスを承認する

次の例では、[JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、トークン クレーム値に基づいて操作へのアクセスを承認する方法を示します。

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>要素

| 要素             | 説明                                                                                                                                                                                                                                                                                                                                           | 必須 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | ルート要素。                                                                                                                                                                                                                                                                                                                                         | はい      |
| audiences           | トークン上に存在する可能性がある、許容される対象ユーザー クレームの一覧を記載します。 対象ユーザー値が複数存在する場合は、すべての値が消費される (この場合検証は失敗します) かいずれかの値の検証が成功するまで、各値について検証が行われます。 少なくとも 1 つの対象ユーザーを指定する必要があります。                                                                     | いいえ       |
| issuer-signing-keys | 署名付きトークンの検証に使用する base64 でエンコードされたセキュリティ キーの一覧。 セキュリティ キーが複数存在する場合は、すべてのキーが処理される (この場合検証は失敗します) かいずれかのキーの検証が成功する (トークンのロールオーバーに使用されます) まで、各キーについて検証が行われます。 キー要素には、`kid` クレームとの照合に使用される `id` 属性を必要に応じて設定できます。 <br/><br/>または、次のものを使用して発行者の署名キーを指定します。<br/><br/> - `<key certificate-id="mycertificate" />` という形式の `certificate-id` を使用して、API Management に[アップロードされた](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add)証明書エンティティの識別子を指定します<br/>- `<key n="<modulus>" e="<exponent>" />` という形式の RSA のモジュラス `n` とエクスポーネント `e` のペアを使用して、base64url エンコード形式で RSA パラメーターを指定します               | いいえ       |
| decryption-keys     | トークンの暗号化を解除するために使用される Base64 でエンコードされたキーの一覧。 セキュリティ キーが複数存在する場合は、すべてのキーが処理される (この場合検証は失敗します) かいずれかのキーの検証が成功するまで、各キーについて検証が行われます。 キー要素には、`kid` クレームとの照合に使用される `id` 属性を必要に応じて設定できます。<br/><br/>または、次のものを使用して復号化キーを指定します。<br/><br/> - `<key certificate-id="mycertificate" />` という形式の `certificate-id` を使用して、API Management に[アップロードされた](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add)証明書エンティティの識別子を指定します                                                 | いいえ       |
| issuers             | トークンを発行した、許容されるプリンシパルの一覧。 発行者の値が複数存在する場合は、すべての値が消費される (この場合検証は失敗します) かいずれかの値の検証が成功するまで、各値について検証が行われます。                                                                                                                                         | いいえ       |
| openid-config       | 署名キーと発行者を取得可能な準拠している Open ID 構成エンドポイントを指定するために使用する要素。                                                                                                                                                                                                                        | いいえ       |
| required-claims     | トークン上に存在すると予測される、有効とみなすクレームの一覧を記載します。 `match` 属性を `all` に設定した場合、検証が成功するにはポリシー内のクレーム値がすべてトークン内に存在する必要があります。 `match` 属性を `any` に設定した場合、検証が成功するには少なくとも 1 つのクレームがトークン内に存在する必要があります。 | いいえ       |

### <a name="attributes"></a>属性

| 名前                            | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                            | 必須                                                                         | Default                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | 期間。 トークンの発行者と API Management インスタンスのシステム クロックの間に予想される最大時間差を指定するために使用します。                                                                                                                                                                                                                                                                                                               | いいえ                                                                               | 0 秒                                                                         |
| failed-validation-error-message | JWT が検証で不合格となった場合に HTTP 応答本文で返すエラー メッセージ。 このメッセージ内では、特殊文字を適切にエスケープする必要があります。                                                                                                                                                                                                                                                                                                 | いいえ                                                                               | 既定のエラー メッセージは検証の問題によって異なります ("JWT not present" (JWT が存在しません) など)。 |
| failed-validation-httpcode      | JWT が検証で不合格となった場合に返す HTTP 状態コード。                                                                                                                                                                                                                                                                                                                                                                                         | いいえ                                                                               | 401                                                                               |
| header-name                     | トークンを保持する HTTP ヘッダーの名前。                                                                                                                                                                                                                                                                                                                                                                                                         | `header-name`、`query-parameter-name`、`token-value` のいずれかを指定する必要があります。 | 該当なし                                                                               |
| query-parameter-name            | トークンを保持するクエリ パラメーターの名前。                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name`、`query-parameter-name`、`token-value` のいずれかを指定する必要があります。 | 該当なし                                                                               |
| token-value                     | JWT トークンを含む文字列を返す式。 トークン値の一部として `Bearer ` を返すことはできません。                                                                                                                                                                                                                                                                                                                                           | `header-name`、`query-parameter-name`、`token-value` のいずれかを指定する必要があります。 | 該当なし                                                                               |
| id                              | `key` 要素の `id` 属性を使用すると、署名検証用の適切なキーを確認するためにトークン内の `kid` クレーム (存在する場合) と照合する文字列を指定できます。                                                                                                                                                                                                                                           | いいえ                                                                               | 該当なし                                                                               |
| match                           | `claim` 要素の `match` 属性では、検証が成功するためにポリシー内のクレーム値がすべてトークン内に存在する必要があるかどうかを指定します。 次のいずれかの値になります。<br /><br /> - `all` - 検証が成功するには、ポリシー内のクレーム値がすべてトークン内に存在する必要があります。<br /><br /> - `any` - 検証が成功するには、ポリシー内のクレーム値が少なくとも 1 つトークン内に存在する必要があります。                                                       | No                                                                               | all                                                                               |
| require-expiration-time         | ブール型。 トークン内に有効期限クレームが存在する必要があるかどうかを指定します。                                                                                                                                                                                                                                                                                                                                                                               | いいえ                                                                               | true                                                                              |
| require-scheme                  | トークンの名前。例: "Bearer"。 この属性が設定されている場合、ポリシーは指定したスキームが承認ヘッダーの値に存在していることを確認します。                                                                                                                                                                                                                                                                                    | いいえ                                                                               | 該当なし                                                                               |
| require-signed-tokens           | ブール型。 トークンに署名が必要かどうかを指定します。                                                                                                                                                                                                                                                                                                                                                                                           | いいえ                                                                               | true                                                                              |
| separator                       | 文字列。 複数値を含む要求から一連の値を抽出するために使用する区切り記号を指定します (例: ",")。                                                                                                                                                                                                                                                                                                                                          | いいえ                                                                               | 該当なし                                                                               |
| url                             | Open ID 構成メタデータを取得可能な Open ID 構成エンドポイントの URL。 応答は、URL で定義されている仕様に従っている必要があります:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`。 Azure Active Directory の場合は、`https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` という URL をご使用のディレクトリ テナント名 (`contoso.onmicrosoft.com` など) に置き換えて使用します。 | はい                                                                              | 該当なし                                                                               |
| output-token-variable-name      | 文字列 をオンにします。 成功したトークンの検証において、[`Jwt`](api-management-policy-expressions.md) 型のオブジェクトとしてトークン値を受け取るコンテキスト変数の名前                                                                                                                                                                                                                                                                                     | いいえ                                                                               | 該当なし                                                                               |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound
-   **ポリシー スコープ:** すべてのスコープ


## <a name="validate-client-certificate"></a>クライアント証明書を検証する

`validate-client-certificate` ポリシーを使用して、クライアントから API Management インスタンスに提示された証明書が、1つまたは複数の証明書 ID のサブジェクトや発行者などの指定した検証規則と要求に一致することを強制します。

クライアント証明書は、有効と見なされるために、最上位要素の属性で定義されているすべての検証規則と一致し、定義済みの ID の少なくとも 1 つに対して定義されているすべての要求と一致している必要があります。 

このポリシーを使用して、受信証明書のプロパティと必要なプロパティを確認します。 また、このポリシーを使用して、次のような場合にクライアント証明書の既定の検証を上書きします。

* マネージド ゲートウェイへのクライアント要求を検証するためにカスタム CA 証明書をアップロードした場合
* セルフマネージド ゲートウェイへのクライアント要求を検証するようにカスタム証明機関を構成した場合

カスタム CA 証明書と証明機関の詳細については、[「Azure API Management でカスタム CA 証明書を追加する方法」](api-management-howto-ca-certificates.md)を参照してください。 

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-client-certificate 
    validate-revocation="true|false"
    validate-trust="true|false" 
    validate-not-before="true|false" 
    validate-not-after="true|false" 
    ignore-error="true|false">
    <identities>
        <identity 
            thumbprint="certificate thumbprint"
            serial-number="certificate serial number"
            common-name="certificate common name"
            subject="certificate subject string"
            dns-name="certificate DNS name"
            issuer-subject="certificate issuer"
            issuer-thumbprint="certificate issuer thumbprint"
            issuer-certificate-id="certificate identifier" />
    </identities>
</validate-client-certificate> 
```

### <a name="example"></a>例

次の例では、ポリシーの既定の検証規則に一致するようにクライアント証明書を検証し、サブジェクトと発行者の名前が指定された値に一致するかどうかを確認します。

```xml
<validate-client-certificate 
    validate-revocation="true" 
    validate-trust="true" 
    validate-not-before="true" 
    validate-not-after="true" 
    ignore-error="false">
    <identities>
        <identity
            subject="C=US, ST=Illinois, L=Chicago, O=Contoso Corp., CN=*.contoso.com"
            issuer-subject="C=BE, O=FabrikamSign nv-sa, OU=Root CA, CN=FabrikamSign Root CA" />
    </identities>
</validate-client-certificate> 
```

### <a name="elements"></a>要素

| 要素             | 説明                                  | 必須 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-client-certificate        | ルート要素。      | はい      |
|   ID      |  クライアント証明書に対して定義されたクレームのある ID の一覧が含まれます。       |    いいえ        |

### <a name="attributes"></a>属性

| 名前                            | 説明      | 必須 |  Default    |
| ------------------------------- | -----------------| -------- | ----------- |
| validate-revocation  | ブール型。 オンライン失効リストに対して証明書を検証するかどうかを指定します。  | Ｘ   | True  |
| validate-trust | ブール型。 信頼された証明機関にチェーンを正常に構築できない場合に検証が失敗するかどうかを指定します。 | Ｘ | True |
| validate-not-before | ブール型。 現在の時刻に対して値を検証します。 | Ｘ | True |
| validate-not-after  | ブール型。 現在の時刻に対して値を検証します。 | Ｘ | True|
| ignore-error  | Boolean です。 ポリシーを次のハンドラーに進めるか、検証に失敗した場合に ON-ERROR にジャンプするかを指定します。 | no | False |
| ID | 文字列 をオンにします。 証明書を有効にする証明書要求値の組み合わせ。 | はい | N/A |
| thumbprint | 証明書のサムプリント。 | Ｘ | N/A |
| serial-number | 証明書のシリアル番号。 | Ｘ | N/A |
| common-name | 証明書の共通名 (サブジェクト文字列の一部)。 | Ｘ | N/A |
| subject | サブジェクト文字列。 識別名の形式に従う必要があります。 | Ｘ | N/A |
| dns-name | サブジェクトの代替名要求内の dnsName エントリの値。 | Ｘ | 該当なし |
| issuer-subject | 発行者のサブジェクト。 識別名の形式に従う必要があります。 | Ｘ | N/A |
| issuer-thumbprint | 発行者の拇印。 | Ｘ | N/A |
| issuer-certificate-id | 発行者の公開キーを表す既存の証明書エンティティの識別子。 他の発行者属性と同時に指定できません。  | Ｘ | 該当なし |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound
-   **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

-   [API Management のポリシー](api-management-howto-policies.md)
-   [API を変換する](transform-api.md)
-   ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
-   [ポリシーのサンプル](./policy-reference.md)
