---
title: Azure Marketplace に SaaS オファーの技術的な詳細を追加する
description: Azure Marketplace でサービスとしてのソフトウェア (SaaS) オファーの技術的な詳細を提供します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 39e41febed0809486d2e6796284d86a0874c13f5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059678"
---
# <a name="add-technical-details-for-a-saas-offer"></a>SaaS オファーの技術的な詳細を追加する

この記事では、Microsoft コマーシャル マーケットプレースから貴社のソリューションに接続する場合の技術的な詳細を入力する方法について説明します。 貴社のオファーを顧客が取得および管理することを選択した場合、Microsoft ではこの接続を使用して、顧客にオファーをプロビジョニングします。 これらの設定の詳細については、[技術情報](plan-saas-offer.md#technical-information)に関するページを参照してください。

> [!NOTE]
> トランザクションを個別に処理することを選択した場合、このオプションは表示されません。 代わりに、「[SaaS オファーを販売する方法](create-new-saas-offer-marketing.md)」に進んでください。

## <a name="technical-configuration"></a>技術的な構成

コマーシャル マーケットプレースが貴社の SaaS アプリケーションまたはソリューションとの通信に使用する技術的な詳細は、 **[技術的な構成]** タブに定義します。

- **[ランディング ページの URL]** (必須) – 顧客が貴社のオファーをコマーシャル マーケットプレースから取得し、新しく作成された SaaS サブスクリプションから構成プロセスをトリガーした後に表示される、SaaS Web サイトの URL (例: `https://contoso.com/signup`) を定義します。

  > [!IMPORTANT]
  > 貴社のランディング ページは、24 時間 365 日稼働している必要があります。 これは、顧客のコマーシャル マーケットプレースからの貴社の SaaS オファーの新規購入、またはオファーのアクティブなサブスクリプションの構成要求の通知を受ける唯一の方法です。 ランディング ページの URL にはシャープ記号 (#) を含めないでください。 そうしないと、顧客はランディング ページにアクセスできません。

- **[接続 Webhook]** (必須) – Microsoft からお客様に送信する必要があるすべての非同期イベント (例: SaaS サブスクリプションが取り消された) のために、お客様は [接続 Webhook の URL を指定する](./partner-center-portal/pc-saas-fulfillment-webhook.md)必要があります。 イベントは、この URL を呼び出して通知します。

  > [!IMPORTANT]
  > これは、コマーシャル マーケットプレース経由で購入された顧客の SaaS サブスクリプションに関する更新の通知を受け取る唯一の方法なので、Webhook を 24 時間 365 日稼働させておく必要があります。

- **[Azure Active Directory テナント ID]** (必須) – 貴社の Azure Active Directory (Azure AD) アプリのテナント ID を見つけるには、Azure Active Directory の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ブレードに移動します。 **[表示名]** 列で、アプリを選択します。 次いで、一覧から **ディレクトリ (テナント) ID** 番号 (例: `50c464d3-4930-494c-963c-1e951d15360e`) を見つけます。

- **[Azure Active Directory アプリケーション ID]** (必須) – 貴社の [アプリケーション ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) を見つけるには、Azure Active Directory の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ブレードに移動します。 **[表示名]** 列で、アプリを選択します。 次いで、一覧からアプリケーション (クライアント) ID 番号 (例: `50c464d3-4930-494c-963c-1e951d15360e`) を見つけます。

**[下書きの保存]** を選択してから、次の[プランの概要] タブに移動します。

## <a name="next-steps"></a>次のステップ

- [SaaS オファーのプランを作成する](create-new-saas-offer-plans.md)。
