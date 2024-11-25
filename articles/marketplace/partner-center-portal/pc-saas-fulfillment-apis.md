---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API
description: Microsoft AppSource および Azure Marketplace で SaaS オファーを統合できるようにする Fulfillment API について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: f5db31caffe420f7b53a554e858b8752e9c51223
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054355"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API

SaaS Fulfillment API を使用すると、発行元 (独立系ソフトウェア ベンダー (ISV) とも呼ばれます) は、SaaS アプリケーションを Microsoft AppSource、Azure Marketplace、Azure portal と統合することができます。 これらの API により、ISV アプリケーションは、すべての商取引が有効になっているチャネル (直接、パートナー主導 (再販業者)、フィールド主導) に参加できるようになります。 これらの API との統合は、パートナー センターで取引可能な SaaS オファーを作成して公開するための要件です。

SaaS Fulfillment API の詳細については、次の記事も参照することをお勧めします。
- [SaaS サブスクリプション ライフサイクルの管理](pc-saas-fulfillment-life-cycle.md)
- [SaaS Fulfillment サブスクリプション API v2](pc-saas-fulfillment-subscription-api.md)
- [SaaS Fulfillment 操作 API v2](pc-saas-fulfillment-operations-api.md)
- [SaaS サービスでの Webhook の実装](pc-saas-fulfillment-webhook.md)
- [SaaS Fulfillment API についてよく寄せられる質問](saas-fulfillment-apis-faq.yml)

## <a name="api-flows"></a>API フロー

ISV と Microsoft の両方で同じサブスクリプションの状態を維持するために、ISV は次の API フローを SaaS サービス コードに追加することで実装する必要があります。

* ランディング ページ フロー:Microsoft は、発行元の SaaS オファーがマーケットプレースの顧客によって購入されたことを発行元に通知します。
* アクティベーション フロー:発行元は、新しく購入した SaaS アカウントが発行元側で構成されていることを Microsoft に通知します。
* 更新フロー: 購入プランまたは購入シートの数、あるいはその両方の変更。
* 中断および再開のフロー:顧客の支払い方法が無効になった場合に、購入した SaaS オファーを中断します。 支払い方法に関する問題が解決されると、中断されたオファーを再開できます。
* Webhook フロー: Microsoft は、Microsoft 側の顧客によってトリガーされた SaaS サブスクリプションの変更と解約について、発行元に通知します。

購入した SaaS サブスクリプションを解約する場合、統合は、Microsoft 側の顧客で行うことができるため、省略可能です。

SaaS Fulfillment API との統合を正しく行うことは、次を保証するために非常に重要です。

* 発行元の SaaS オファーを購入したエンド カスタマーが、Microsoft から正しく請求されること。
* エンド カスタマーが、マーケットプレースで購入した SaaS サブスクリプションを購入、構成、使用、および管理する際に適切なユーザー エクスペリエンスが得られていること。

これらの API を使用すると、発行元のオファーをすべてのコマース対応チャネルに参加させることができます。

* 直接
* パートナー主導 (再販業者、CSP)
* フィールド主導

リセラー (CSP) シナリオでは、CSP がエンド ユーザーに代わって SaaS オファーを購入します。 顧客は SaaS オファーを使用することが見込まれますが、CSP は次のタスクを行うエンティティです。

* 顧客に請求する
* サブスクリプション プランまたは購入したシート数を変更する
* サブスクリプションを取り消す

発行元は、このシナリオのために API 呼び出しフローを異なる方法で実装する必要はありません。

CSP の詳細については、 https://partner.microsoft.com/licensing を参照してください。

>[!Warning]
>この API の最新バージョンはバージョン 2 であり、すべての新しい SaaS オファーではこれを使用する必要があります。 この API のバージョン 1 は非推奨であり、既存のオファーをサポートするために保持されています。

>[!Note]
>SaaS Fulfillment API は、発行元のバックエンド サービスから呼び出すことのみを目的としています。 発行元の Web ページから直接 API と統合することはサポートされていません。 サービス間認証フローのみを使用する必要があります。

## <a name="next-steps"></a>次のステップ

- SaaS アプリケーションをまだ登録していない場合は、「[Azure AD アプリケーションの登録](./pc-saas-registration.md)」で説明されているように、[Azure portal](https://ms.portal.azure.com) で登録を行います。  その後、開発に次のインターフェイスの最新バージョンを使用します。[SaaS Fulfillment サブスクリプション API v2](pc-saas-fulfillment-subscription-api.md) および [SaaS Fulfillment 操作 API v2](pc-saas-fulfillment-operations-api.md)。
