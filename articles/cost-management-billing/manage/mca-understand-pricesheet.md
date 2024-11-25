---
title: Microsoft 顧客契約の価格シートの用語 - Azure
description: Microsoft 顧客契約の使用量と請求書を確認して理解する方法について説明します。
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 997bf9e2b88269985c85220c4960179d148a4190
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637474"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Microsoft 顧客契約の価格シートの用語

この記事は、Microsoft 顧客契約の Azure 課金アカウントを対象としています。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者の場合、組織の価格シートを Azure portal からダウンロードすることができます。 [組織の価格の表示およびダウンロード](ea-pricing.md)に関する記事を参照してください。

## <a name="terms-and-descriptions-in-your-price-sheet"></a>価格シートの用語と説明

次のセクションでは、Microsoft の顧客契約の価格シートに表示されている重要な用語について説明します。

| **フィールド名**   | **説明**   |
| --- | --- |
| basePrice  | お客様がサインオンした時点での市場価格、またはサインオン後の場合はサービス メーターが開始した時点での市場価格。   |
| billingAccountId  | 請求先アカウントの一意の識別子。   |
| billingAccountName  | 請求先アカウントの名前。  |
| billingCurrency | 請求金額の計上に使用される通貨 |
| billingProfileId  | 課金プロファイルの一意の識別子。   |
| billingProfileName  | 請求書を受け取るよう設定されている課金プロファイルの名前。 価格シート内の価格は、この課金プロファイルに関連付けられています。 |
| currency | すべての価格が反映される通貨。 |
| discount | Graduation レベル、Free レベル、含まれる数量、交渉済み割引 (該当する場合) で提供される価格割引。 パーセンテージで表されます。 |
| effectiveEndDate  | 有効価格の終了日。 |
| effectiveStartDate  | 価格が有効になる開始日。 |
| includedQuantity | 増分料金なしで顧客に利用資格がある特定のサービスの数量。 |
| marketPrice | 特定のサービスの最新の実勢市場価格。 |
| meterId  | メーターの一意の識別子。 |
| meterCategory  | メーターの分類カテゴリの名前。 たとえば、_Cloud services_、_Networking_ などです。 |
| meterName  | メーターの名前。 メーターは、Azure サービスのデプロイ可能なリソースを表します。 |
| meterSubCategory  | メーターのサブ分類カテゴリの名前。  |
| meterType  |  メーターの種類の名前。 |
| meterRegion  | サービスのメーターが使用可能なリージョンの名前。 データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。    |
| 製品  | 料金が発生する製品の名前。例: Basic SQL DB か Standard SQL DB か  |
| productId  | メーターが消費される製品の一意の識別子。 |
| productOrderName  | 購入した製品プランの名前。 |
| serviceFamily  | Azure サービスの種類。例: Compute、Analytics、Security |
| tierMinimumUnits  | 価格が定義されているレベル範囲の下限を定義します。 たとえば、範囲が 0 から 100 の場合、tierMinimumUnits は 0 になります。  |
| unitOfMeasure  | サービス課金の測定単位の一意の識別子。 たとえば、コンピューティング サービスは時間単位で課金されます。 |
| unitPrice  | メーターと製品オーダー名に特有の、課金時のユニットあたりの価格 (有効なブレンド価格ではない)。  注: レベルごとに価格が異なるサービスの場合、単価は使用量の詳細のダウンロード内の有効価格と同じではありません。  複数のレベルの価格にわたるサービスの場合、有効価格はレベル全体のブレンド価格であり、レベル別の単価は示されません。 ブレンド価格または有効価格は、複数のレベル (各レベルには固有の単価が存在) にまたがって消費された数量に対する正味価格です。 |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [組織の価格の表示とダウンロード](ea-pricing.md)
