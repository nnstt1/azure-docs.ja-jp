---
title: Azure の予約の自動化に関する API | Microsoft Docs
description: 予約の情報をプログラムで取得するために使用できる Azure API について説明します。
author: bandersmsft
ms.reviewer: primittal
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: a82c0ffa8f4396af409624083a945e8b53847a96
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430517"
---
# <a name="apis-for-azure-reservation-automation"></a>Azure の予約の自動化に関する API

Azure API を使用して、Azure サービスまたはソフトウェアの予約に関する組織の情報をプログラムで取得します。

## <a name="find-reservation-plans-to-buy"></a>購入する予約プランを検索する

組織の使用状況に基づいて購入する予約プランについての推奨事項を取得するには、Reservation Recommendation API を使用します。 詳細については、[予約についの推奨事項の取得](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)に関する記事をご覧ください。

Consumption API の使用状況の詳細を使って、リソースの使用状況を分析することもできます。 詳細については、「[Usage Details - List For Billing Period By Billing Account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy)」(使用状況の詳細 - 請求期間に対する課金アカウント別のリスト) をご覧ください。 通常、一貫して使用する Azure リソースは、予約に最適な候補です。

## <a name="buy-a-reservation"></a>予約の購入

REST API を使用して、Azure の予約やソフトウェア プランをプログラムから購入できるようになりました。 詳細は、「[Reservation Order - Purchase API](/rest/api/reserved-vm-instances/reservationorder/purchase)」 (予約注文 - 購入 API) を参照してください。

REST API を使用して購入する場合のサンプル要求を以下に示します。

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

要求本文:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

予約は Azure portal でも購入できます。 詳細については、次の記事を参照してください。

サービス プラン:
- [仮想マシン](../../virtual-machines/prepay-reserved-vm-instances.md?toc=%2fazure%2fbilling%2fTOC.json)
-  [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

ソフトウェア プラン:
- [SUSE Linux ソフトウェア](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>予約を取得する

Enterprise Agreement をご利用のお客様 (EA のお客様) の場合は、[予約インスタンス トランザクション料金取得 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) を使用することで、組織によって購入された予約を取得できます。 他のサブスクリプションの場合は、[予約注文の一覧表示](/rest/api/reserved-vm-instances/reservationorder/list) API を使用することにより、お客様自身が購入して表示アクセス許可を持っている予約の一覧を取得できます。 既定では、アカウントの所有者または予約を購入した人には、予約を表示するアクセス許可があります。

## <a name="see-reservation-usage"></a>予約の使用状況を表示する

EA のお客様の場合、組織の予約がどのように使用されているかを、プログラムで表示することができます。 詳細については、「[Get Reserved Instance usage for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)」(エンタープライズのお客様による予約インスタンスの使用状況を取得する) をご覧ください。 他のサブスクリプションの場合は、「[Reservations Summaries - List By Reservation Order And Reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)」(予約の概要 - 予約注文別および予約別の一覧) で説明されている API を使用します。

組織の予約の使用量が少ない場合は、次のようにします。

- 組織で作成されている仮想マシンが、予約での VM サイズと一致していることを確認します。
- インスタンス サイズの柔軟性が有効になっていることを確認します。 詳細については、予約の管理に関する記事の「[予約 VM インスタンスの最適化設定を変更する](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)」をご覧ください。
- より広範に適用されるように、予約範囲を共有に変更します。 詳細については、予約の管理に関する記事の「[予約のスコープを変更する](manage-reserved-vm-instance.md#change-the-reservation-scope)」をご覧ください。
- 未使用の数量を交換します。 詳しくは、[予約の管理](manage-reserved-vm-instance.md)に関する記事をご覧ください。

## <a name="give-access-to-reservations"></a>予約にアクセスできるようにする

ユーザーがアクセス権を持っているすべての予約の一覧を取得するには、[予約操作の一覧取得 API](/rest/api/reserved-vm-instances/reservationorder/list) を使用します。 プログラムで予約へのアクセス権を付与するには、次のいずれかの記事をご覧ください。

- [REST API を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell を使用して Azure でのロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>予約を分割または統合する

予約内で複数のリソース インスタンスを購入した後、その予約内のインスタンスを異なるサブスクリプションに割り当てることができます。 同じ課金コンテキスト内のすべてのサブスクリプションに適用されるように、予約範囲を変更できます。 ただし、コスト管理や予算作成が目的の場合は、範囲を "1 つのサブスクリプション" に維持し、予約インスタンスを特定のサブスクリプションに割り当てるのが望ましいことがあります。

予約を分割するには、[予約分割](/rest/api/reserved-vm-instances/reservation/split) API を使用します。 PowerShell を使用して予約を分割することもできます。 詳細については、予約の管理に関する記事の「[1 つの予約を 2 つの予約に分割する](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations)」をご覧ください。

2 つの予約を 1 つの予約にまとめるには、[予約統合](/rest/api/reserved-vm-instances/reservation/merge) API を使用します。

## <a name="change-scope-for-a-reservation"></a>予約の範囲を変更する

予約のスコープは、単一のサブスクリプションや単一のリソース グループになることもあれば、課金コンテキスト内のすべてのサブスクリプションになることもあります。 単一のサブスクリプションまたは単一のリソース グループにスコープを設定した場合、選択したサブスクリプション内の実行中のリソースに対して予約が照合されます。 サブスクリプションまたはリソース グループを削除または移動した場合、予約は利用できなくなります。  スコープを "共有" に設定した場合、課金コンテキスト内の全サブスクリプションで実行されるリソースに対して予約が照合されます。 課金コンテキストは、予約の購入に使用したサブスクリプションによって決まります。 スコープは購入時に選択できます。また、購入後いつでも変更することもできます。 詳細については、予約の管理に関する記事の「[予約のスコープを変更する](manage-reserved-vm-instance.md#change-the-reservation-scope)」をご覧ください。

プログラムで範囲を変更するには、[予約更新](/rest/api/reserved-vm-instances/reservation/update) API を使用します。

## <a name="learn-more"></a>詳細情報

- [Azure の予約とは](save-compute-costs-reservations.md)
- [VM 予約割引の適用方法](../manage/understand-vm-reservation-charges.md)
- [SUSE Linux Enterprise ソフトウェア プランの割引の適用方法](understand-suse-reservation-charges.md)
- [その他の予約割引の適用方法](understand-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)