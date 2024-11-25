---
title: Azure の予約ソフトウェアのコスト
description: Azure Reserved VM Instance のコストに含まれないソフトウェアのメーターについて説明します。
author: bandersmsft
ms.reviewer: primittal
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 4980c779c5b3301a0e15b77b21230d14af04271b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425749"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances に含まれないソフトウェアのコスト

予約仮想マシン インスタンスと SQL 予約容量の割引は、インフラストラクチャのコストにのみ適用され、ソフトウェアのコストには適用されません。 Windows VM をご使用で、かつご使用の予約仮想マシン インスタンスに Azure ハイブリッド特典がない場合、以下のセクションに記載されているソフトウェアのメーターに関して料金が発生します。 SQL PaaS デプロイで、Azure ハイブリッド特典が選択されていない場合、IP コストは個別のメーターを使用して引き続き課金されます。

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>予約のコストに含まれない Windows ソフトウェアの測定

| MeterId | 使用状況ファイルにおける MeterName | VM による使用 |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | 占有 - Windows Svr バースト (1 コア) | B シリーズ |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | 占有 - Windows Svr バースト (2 コア) | B シリーズ |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | 占有 - Windows Svr バースト (4 コア) | B シリーズ |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | 占有 - Windows Svr バースト (8 コア) | B シリーズ |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | 占有 - Windows Svr (1 コア) | B シリーズを除くすべて |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | 占有 - Windows Svr (2 コア) | B シリーズを除くすべて |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | 占有 - Windows Svr (4 コア) | B シリーズを除くすべて |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | 占有 - Windows Svr (6 コア) | B シリーズを除くすべて |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | 占有 - Windows Svr (8 コア) | B シリーズを除くすべて |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | 占有 - Windows Svr (12 コア) | B シリーズを除くすべて |
| 02968a6b-1654-4495-ada6-13f378ba7172 | 占有 - Windows Svr (16 コア) | B シリーズを除くすべて |
| 175434d8-75f9-474b-9906-5d151b6bed84 | 占有 - Windows Svr (20 コア) | B シリーズを除くすべて |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | 占有 - Windows Svr (24 コア) | B シリーズを除くすべて |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | 占有 - Windows Svr (32 コア) | B シリーズを除くすべて |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | 占有 - Windows Svr (40 コア) | B シリーズを除くすべて |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | 占有 - Windows Svr (64 コア) | B シリーズを除くすべて |
| da612742-e7cc-4ca3-9334-0fb7234059cd | 占有 - Windows Svr (72 コア) | B シリーズを除くすべて |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | 占有 - Windows Svr (128 コア) | B シリーズを除くすべて |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | 占有 - Windows Svr (256 コア) | B シリーズを除くすべて |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | 占有 - Windows Svr (96 コア) | B シリーズを除くすべて |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>予約のコストに含まれないクラウド サービス ソフトウェアのメーター

| MeterId | 使用状況ファイルにおける MeterName |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU ライセンス|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU ライセンス|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU ライセンス|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU ライセンス|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU ライセンス|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU ライセンス|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU ライセンス|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU ライセンス|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU ライセンス|

## <a name="get-rates-for-azure-meters"></a>Azure メーターの料金を取得する

Azure Retail Prices API を使用してメーターごとのコストを取得できます。 Azure メーターの料金を取得する方法については、「[Azure 小売価格の概要](/rest/api/cost-management/retail-prices/azure-retail-prices)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Azure の予約の管理](manage-reserved-vm-instance.md)
- [予約割引の適用方法について](../manage/understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。