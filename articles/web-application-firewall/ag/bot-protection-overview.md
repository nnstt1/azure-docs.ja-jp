---
title: Azure Application Gateway 上の WAF でのボット保護の概要
titleSuffix: Azure Web Application Firewall
description: この記事では、Application Gateway 上の Web アプリケーション ファイアウォール (WAF) でのボット保護の概要について説明します
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7cc82630b2f65bdd94e02e71b3c2521fc5734a9e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315045"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure Application Gateway 上の Azure Web Application Firewall でのボット保護の概要

すべてのインターネット トラフィックの約 20 % は、問題のあるボットからのものです。 それらは、スクレーピング、スキャン、Web アプリケーションでの脆弱性の検索などを行います。 これらのボットは、Web アプリケーション ファイアウォール (WAF) で停止されると攻撃できません。 また、バックエンドやその他の基盤となるインフラストラクチャなどのリソースやサービスを使い尽くすこともできません。

マネージド ボット保護規則セットを WAF に対して有効にし、既知の悪意のある IP アドレスからの要求をブロックしたりログに記録したりすることができます。 この IP アドレスのソースは、Microsoft の脅威インテリジェンス フィードです。 インテリジェント セキュリティ グラフは Microsoft の脅威インテリジェンスの動力となるものであり、Microsoft Defender for Cloud を含む複数のサービスによって使用されます。

## <a name="use-with-owasp-rulesets"></a>OWASP ルールセットで使用する

ボット保護ルールセットは、任意の OWASP ルールセット (2.2.9、3.0、および 3.1) と共に使用できます。 どの特定の時点でも使用できるのは、1 つの OWASP ルールセットのみです。 ボット保護ルールセットには、独自のルールセットに表示される追加のルールが含まれています。 タイトルは **Microsoft_BotManagerRuleSet_0.1** で、他の OWASP ルールと同様に有効または無効にすることができます。

![ボット ルールセット](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>ルールセットの更新

既知の問題のある IP アドレスのボット軽減ルールセット一覧は、ボットとの同期を維持するために、Microsoft の脅威インテリジェンス フィードから 1 日に複数回更新されます。 Web アプリケーションは、ボットの攻撃ベクトルが変更された場合でも継続的に保護されます。

## <a name="log-example"></a>ログの例

ボット保護のためのログ エントリの例を次に示します。

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>次のステップ

- [Azure Application Gateway での Web アプリケーション ファイアウォール用にボット保護を構成する](bot-protection.md)
