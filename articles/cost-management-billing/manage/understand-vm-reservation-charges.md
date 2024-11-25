---
title: Azure Reserved VM Instances の割引を理解する
description: Azure Reserved VM Instance の割引が、実行中の仮想マシンにどのように適用されるのかを説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 03b9237756c34f7f227e529d0397a85d2411032e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423111"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Azure の予約割引が仮想マシンに適用されるしくみ

Azure 予約仮想マシン インスタンスを購入すると、予約の属性や数量に合致する仮想マシンに対して予約割引が自動的に適用されます。 予約購入分は、ご利用の仮想マシンの計算コストに充当されます。

予約割引は、Azure Marketplace から購入したベース VM に適用されます。

SQL Database の予約容量については、「[Understand Azure Reserved Instances discount](../reservations/understand-reservation-charges.md)」(Azure 予約インスタンスの割引について) を参照してください。

以下の表は、予約 VM インスタンスを購入した後の仮想マシンのコストを示しています。 ストレージとネットワークについては、すべての場合において通常料金が適用されます。

| 仮想マシンのタイプ  | 予約 VM インスタンスの料金 |
|-----------------------|--------------------------------------------|
|Linux VM (追加ソフトウェアなし) | 予約購入分は、ご利用の VM インフラストラクチャ コストに充当されます。|
|Linux VM + ソフトウェア料金 (例: Red Hat) | 予約購入分は、インフラストラクチャ コストに充当されます。 追加ソフトウェアについては課金の対象となります。|
|Windows VM (追加ソフトウェアなし) |予約購入分は、インフラストラクチャ コストに充当されます。 Windows ソフトウェアについては課金の対象となります。|
|Windows VM + 追加ソフトウェア (例: SQL Server) | 予約購入分は、インフラストラクチャ コストに充当されます。 Windows ソフトウェアや追加ソフトウェアについては課金の対象となります。|
|Windows VM + [Azure ハイブリッド特典](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | 予約購入分は、インフラストラクチャ コストに充当されます。 Windows ソフトウェアの料金には、Azure ハイブリッド特典が適用されます。 その他のソフトウェアについては、別途課金の対象となります。|

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、"*使用しないと失われます*"。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンするか、VM の数をスケーリングすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

## <a name="reservation-discount-for-non-windows-vms"></a>非 Windows VM に対する予約割引

 Azure 予約割引は、実行中の VM インスタンスに 1 時間単位で適用されます。 購入済みの予約は、実行中の VM によって生成された使用量と照合され、予約割引が適用されます。 実行時間が 1 時間に満たない可能性のある VM の場合、予約は、同時に実行されている VM を含め、予約を使用していない他の VM から満たされます。 1 時間の最後には、その時間の VM の予約の適用がロックされます。 VM の実行が 1 時間に満たない場合、またはその時間内の同時実行 VM が予約の時間を満たさない場合、その時間の予約は十分に活用されていません。 以下のグラフは、課金対象の VM 使用量に予約を適用した例を示しています。 この例は、単一の予約購入で、合致する VM インスタンスが 2 つあることを前提としています。

![適用済みの 1 つの予約と合致する 2 つの VM インスタンスのスクリーンショット](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. 予約のラインを超える使用量には、通常の従量課金制の料金が適用されます。 予約のラインを下回る使用量については、予約購入分として前払い済みであるため課金されません。
2. Hour 1 では、インスタンス 1 の実行時間が 0.75 時間、インスタンス 2 の実行時間が 0.5 時間です。 Hour 1 の合計使用量は 1.25 時間となります。 残りの 0.25 時間については従量課金制の料金が発生します。
3. Hour 2 と Hour 3 では、どちらのインスタンスも実行時間はそれぞれ 1 時間です。 一方のインスタンスには予約購入分が充当されますが、もう一方には従量課金制の料金が発生します。
4. Hour 4 では、インスタンス 1 の実行時間が 0.5 時間、インスタンス 2 の実行時間が 1 時間です。 インスタンス 1 は予約購入分で全額充当されます。またインスタンス 2 の 0.5 時間も充当されます。 残りの 0.5 時間については従量課金制の料金が発生します。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[予約の使用状況](../reservations/understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="reservation-discount-for-windows-vms"></a>Windows VM に対する予約割引

Windows VM インスタンスの実行中は、インフラストラクチャ コストに予約が適用されて充当されます。 VM インフラストラクチャ コストに対する予約の適用は、Windows VM の場合も 非 Windows VM の場合も変わりません。 Windows ソフトウェアには別途、vCPU 単位の料金が発生します。 [予約に伴う Windows ソフトウェアのコスト](../reservations/reserved-instance-windows-software-costs.md)に関するページを参照してください。 Windows のライセンス コストは [Windows Server 向け Azure ハイブリッド特典](../../virtual-machines/windows/hybrid-use-benefit-licensing.md)で充当できます。

## <a name="discount-can-apply-to-different-sizes"></a>さまざまなサイズに割引を適用できる

予約 VM インスタンスを購入するときに、 **[最適化の対象: インスタンス サイズの柔軟性]** を選択した場合、割引範囲は、選択した VM のサイズに適用されます。 また、同じシリーズのインスタンス サイズの柔軟性グループに存在する他の VM サイズにも適用できます。 詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../../virtual-machines/reserved-vm-instance-size-flexibility.md)」を参照してください。

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>Premium Storage VM には Premium 以外の割引は適用されない

次に例を示します。 5 つの Standard_D1 VM に対する予約を購入した場合、予約割引が適用されるのは、Standard_D1 の VM のほか、同じインスタンス ファミリに属する VM だけです。 Standard_DS1 VM や DS1 インスタンス サイズの柔軟性グループに属している他のサイズの VM には割引が適用されません。

予約割引の適用では、VM に対して使用される測定は無視され、ServiceType のみが評価されます。 ご利用の VM のインスタンス柔軟性グループ (またはシリーズ) 情報は、`AdditionalInfo` の `ServiceType` の値を見て判断してください。 それらの値は、使用状況の CSV ファイルにあります。

予約のインスタンス柔軟性グループ (またはシリーズ) を購入後に直接変更することはできません。 ただし、インスタンス柔軟性グループ (シリーズ) 間で VM の予約を "*交換*" することはできます。

## <a name="services-that-get-vm-reservation-discounts"></a>VM の予約割引が適用されるサービス

VM の予約は、VM のデプロイだけでなく、複数のサービスから発生する VM の使用量にも適用できます。 予約割引が適用されるリソースは、インスタンス サイズの柔軟性の設定によって変わります。

### <a name="instance-size-flexibility-setting"></a>インスタンス サイズの柔軟性の設定

インスタンス サイズの柔軟性の設定によって、予約インスタンスの割引が適用されるサービスが決まります。

設定がオンかオフかにかかわらず、*ConsumedService* が `Microsoft.Compute` の場合は、条件に一致する VM の使用量に予約割引が自動適用されます。 そのため、*ConsumedService* の値に対応した使用量データを確認してください。 次に例をいくつか示します。

- 仮想マシン
- 仮想マシン スケール セット
- コンテナー サービス
- Azure Batch のデプロイ (ユーザー サブスクリプション モード)
- Azure Kubernetes Service (AKS)
- Service Fabric

設定がオンの場合、*ConsumedService* が次の項目のいずれかであれば、条件に一致する VM の使用量に予約割引が自動適用されます。

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

使用量データで *ConsumedService* の値を確認して、その使用量が予約割引の対象であるかどうかを判断してください。

インスタンス サイズの柔軟性の詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../../virtual-machines/reserved-vm-instance-size-flexibility.md)」を参照してください。


## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../reservations/save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure の予約の管理](../reservations/manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](../reservations/understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../reservations/understand-reserved-instance-usage-ea.md)
- [CSP サブスクリプションの予約の使用状況について](/partner-center/azure-reservations)
- [予約に含まれない Windows ソフトウェアのコスト](../reservations/reserved-instance-windows-software-costs.md)
