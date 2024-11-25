---
title: Azure API Management レベルの機能に基づく比較 | Microsoft Docs
description: 提供される機能に基づいて API Management レベルを比較します。 各価格レベルで使用できる主な機能をまとめた表を参照してください。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/15/2021
ms.author: danlep
ms.openlocfilehash: 6159b5c5d677dd66dda12573feeb3aad9faa9764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556536"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API Management レベルの機能に基づく比較

それぞれの API Management [価格レベル](https://aka.ms/apimpricing)には、機能セットとユニットごとの[容量](api-management-capacity.md)に違いがあります。 次の表は、各レベルで使用できる主な機能をまとめたものです。 一部の機能は、動作が異なります。レベルによって機能が異なる場合もあります。 このような場合、個々の機能を説明するドキュメント記事に相違点が記載されています。

> [!IMPORTANT]
> Developer レベルは、非運用環境のユースケースと評価のためのものであることに注意してください。 SLA は提供されません。

| 機能                                                                                      | 従量課金 | 開発者 | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD 統合<sup>1</sup>                                                             | いいえ          | はい       | いいえ    | はい      | はい     |
| Virtual Network (VNet) のサポート                                                               | いいえ          | はい       | いいえ    | いいえ       | はい     |
| 複数リージョンのデプロイ                                                                      | いいえ          | いいえ        | いいえ    | いいえ       | はい     |
| 可用性ゾーン                                                                           | いいえ          | いいえ        | いいえ    | いいえ       | はい     |
| 複数のカスタム ドメイン名                                                                 | いいえ          | はい        | いいえ    | いいえ       | はい     |
| 開発者ポータル<sup>2</sup>                                                                 | いいえ          | はい       | はい   | はい      | はい     |
| ビルトイン キャッシュ                                                                               | いいえ          | はい       | はい   | はい      | はい     |
| ビルトイン分析                                                                           | いいえ          | はい       | はい   | はい      | はい     |
| [セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)<sup>3</sup>                           | いいえ          | はい       | いいえ    | いいえ       | はい     |
| [TLS の設定](api-management-howto-manage-protocols-ciphers.md)                             | はい         | はい       | はい   | はい      | はい     |
| [外部キャッシュ](./api-management-howto-cache-external.md)                                                    | はい         | はい       | はい   | はい      | はい     |
| [クライアント証明書認証](api-management-howto-mutual-certificates-for-clients.md) | はい         | はい       | はい   | はい      | はい     |
| [バックアップと復元](api-management-howto-disaster-recovery-backup-restore.md)               | いいえ          | はい       | はい   | はい      | はい     |
| [Git による管理](api-management-configuration-repository-git.md)                        | いいえ          | はい       | はい   | はい      | はい     |
| ダイレクト管理 API                                                                        | いいえ          | はい       | はい   | はい      | はい     |
| Azure Monitor のログとメトリック                                                               | いいえ          | はい       | はい   | はい      | はい     |
| 静的 IP                                                                                    | いいえ          | はい       | はい   | はい      | はい     |
| [WebSocket API](websocket-api.md)                                                                                    | いいえ          | はい       | はい   | はい      | はい     |
| [GraphQL API (プレビュー)](graphql-api.md)                                                                               | はい          | はい       | はい   | はい      | はい     |

<sup>1</sup> ユーザーが開発者ポータルにサインインする際に、ID プロバイダーとして Azure AD (および Azure AD B2C) を使用できます。<br/>
<sup>2</sup> 関連機能 (例: ユーザー、グループ、問題、アプリケーション、電子メール テンプレートと通知) を含みます。<br/>
<sup>3</sup> Developer レベルのセルフホステッド ゲートウェイは、1 つのゲートウェイ ノードに制限されます。<br/>
