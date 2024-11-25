---
title: ソフトウェア プラン割引 - Azure
description: ソフトウェア プラン割引が仮想マシン上のソフトウェアにどのように適用されるかについて説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: fec1ecd33c47379d5c9f599aa97f47c8c543de78
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425673"
---
# <a name="azure-software-plan-discount"></a>Azure ソフトウェア プラン割引

SUSE と RedHat の Azure ソフトウェア プランは、デプロイされている VM に適用される予約です。 ソフトウェア プラン割引は、予約に一致するデプロイ済み VM のソフトウェア使用に適用されます。

VM をシャット ダウンするときに、割引は別の一致する VM (ある場合) に自動的に適用されます。 ソフトウェア プランでは、VM 上でソフトウェアを実行するコストが対象になります。 コンピューティング、ストレージ、およびネットワーキングなどの他の料金は別途課金されます。

適切なプランを購入するには、VM の使用状況と、それらの VM 上の vCPU の数を把握している必要があります。 以降のセクションを使用して、使用状況データに基づいて購入するプランを特定するのに役立ててください。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、"*使用しないと失われます*"。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

## <a name="review-redhat-vm-usage-before-you-buy"></a>購入する前に RedHat VM の使用状況を確認する

使用状況データから製品名を取得して、同じ種類とサイズの RedHat プランを購入します。

たとえば、お客様の使用状況に製品 **Red Hat Enterprise Linux - 1-4 vCPU VM License** がある場合は、**1-4 vCPU VM** 用の **Red Hat Enterprise Linux** を購入してください。

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>購入する前に SUSE VM の使用状況を確認する

使用状況データから製品名を取得して、同じ種類とサイズの SUSE プランを購入します。

たとえば、お客様の使用状況が製品 **SUSE Linux Enterprise Server Priority - 2-4 vCPU VM Support** を対象としている場合は、**2-4 vCPU** 用の **SUSE Linux Enterprise Server Priority** を購入してください。

![購入する製品の選択例](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>割引は SUSE プランのさまざまな VM サイズに適用される

予約 VM インスタンスと同様に、SUSE プランの購入は、インスタンス サイズの柔軟性を提供します。 つまり、異なる vCPU 数の VM をデプロイする場合でも、割引が適用されます。 割引は、ソフトウェア プラン内の異なる VM サイズに適用されます。

割引額は、以降の表に記載されている比率によって異なります。 比率は、そのグループ内の各測定の相対的フットプリントを比較します。 比率は、VM の vCPU に依存します。 比率の値を使用すると、SUSE Linux プランの割引が適用される VM インスタンスの数を計算できます。

たとえば、3 つまたは 4 つの vCPU を搭載する VM に対して SUSE Linux Enterprise Server for HPC Priority のプランを購入する場合、予約の比率は 2 です。 この場合、次の SUSE ソフトウェア コストが割引の対象になります。

- 1 つまたは 2 つの vCPU を使用する、2 つのデプロイされた VM
- 3 つまたは 4 つの vCPU を使用する、1 つのデプロイされた VM
- または、5 つ以上の vCPU を使用する VM の 0.77 または約 77%。

5 つ以上の vCPU の場合、比率は 2.6 です。 したがって、5 つ以上の vCPU を使用する VM に対して SUSE を予約した場合、ソフトウェア コストの一部のみ (約 77%) が対象になります。

次の表は、予約を購入できるソフトウェア プラン、関連する使用量メーター、およびそれぞれの比率を示しています。

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

|SUSE VM | MeterId| 比率| VM サイズの例|
| -------| ------------------------| --- |--- |
|SUSE Linux Enterprise Server for HPC Priority (vCPU 数: 1 から 2)|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SUSE Linux Enterprise Server for HPC Priority (vCPU 数: 3 から 4)|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SUSE Linux Enterprise Server for HPC Priority (vCPU 数: 5 以上)|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- | --- | ------------------------| --- |
|SUSE Linux Enterprise Server for HPC Standard (vCPU 数: 1 から 2) |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SUSE Linux Enterprise Server for HPC Standard (vCPU 数: 3 から 4)|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SUSE Linux Enterprise Server for HPC Standard (vCPU 数: 5 以上) |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-standard"></a>SUSE Linux Enterprise Server for SAP Standard

以前、SUSE Linux Enterprise Server for SAP Standard は、SUSE Linux Enterprise Server for SAP Priority と呼ばれていました。

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server for SAP Standard (vCPU 数: 1 から 2)|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server for SAP Standard (vCPU 数: 3 から 4) |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server for SAP Standard (vCPU 数: 5 以上) |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- |--- |
|SUSE Linux Enterprise Server Standard (vCPU コア数: 1 から 2) |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SUSE Linux Enterprise Server Standard (vCPU コア数: 3 から 4) |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SUSE Linux Enterprise Server Standard (vCPU 数: 5 以上) |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

予約について詳しくは、次の記事を参照してください。

- [Azure の予約とは](save-compute-costs-reservations.md)
- [Azure の予約による SUSE ソフトウェア プランの前払い](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Azure の予約の管理](manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)