---
title: 第 2 世代 VM に対する Azure のサポート
description: 第 2 世代 VM に対する Azure のサポートの概要
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/26/2021
ms.author: jushiman
ms.openlocfilehash: f0a78f60f8610c4b6430cf33928ec389c0ed85ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471575"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure での第 2 世代 VM のサポート

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

第 2 世代仮想マシン (VM) が Azure でサポートされるようになりました。 仮想マシンを作成した後にその世代を変更することはできないため、世代を選択する前に、このページの考慮事項を確認してください。

第 2 世代 VM では、第 1 世代 VM ではサポートされていない重要な機能がサポートされています。 これらの機能には、メモリの増加、Intel ソフトウェア ガード エクステンションズ (Intel SGX)、および仮想化された永続メモリ (vPMEM) が含まれます。 オンプレミスで実行される第 2 世代 VM には、Azure ではまだサポートされていない機能がいくつかあります。 詳細については、「[特徴と機能](#features-and-capabilities)」セクションを参照してください。

第 2 世代 VM では、第 1 世代 VM で使用されている BIOS ベースのアーキテクチャではなく、新しい UEFI ベースのブート アーキテクチャが使用されます。 第 1 世代 VM と比較して、第 2 世代 VM ではブート時間やインストール時間が改善されている可能性があります。 第 2 世代 VM の概要、および第 1 世代と第 2 世代のいくつかの違いについては、「[Should I create a generation 1 or 2 virtual machine in Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」(Hyper-V で第 1 世代または第 2 世代のどちらの仮想マシンを作成すべきか) をご覧ください。

## <a name="generation-2-vm-sizes"></a>第 2 世代 VM のサイズ

Azure では、次の選択された VM シリーズに対して第 2 世代のサポートが提供されるようになりました。


* [B シリーズ](sizes-b-series-burstable.md)
* [DCsv2 シリーズ](dcv2-series.md)
* [DSv2 シリーズ](dv2-dsv2-series.md)
* [Dsv3 シリーズ](dv3-dsv3-series.md)
* [Dsv4 シリーズ](dv4-dsv4-series.md)
* [Dasv4 シリーズ](dav4-dasv4-series.md)
* [Ddsv4 シリーズ](ddv4-ddsv4-series.md)
* [Dasv5 シリーズ](dasv5-dadsv5-series.md)
* [Dadsv5 シリーズ](dasv5-dadsv5-series.md)
* [Dv5 シリーズ](dv5-dsv5-series.md)
* [Dsv5 シリーズ](dv5-dsv5-series.md)
* [Esv3 シリーズ](ev3-esv3-series.md)
* [Esv4 シリーズ](ev4-esv4-series.md)
* [Easv4 シリーズ](eav4-easv4-series.md)
* [Edsv4 シリーズ](edv4-edsv4-series.md)
* [Easv5 シリーズ](easv5-eadsv5-series.md)
* [Eadsv5 シリーズ](easv5-eadsv5-series.md)
* [Edv5 シリーズ](edv5-edsv5-series.md)
* [Edsv5 シリーズ](edv5-edsv5-series.md)
* [Ev5 シリーズ](ev5-esv5-series.md)
* [Esv5 シリーズ](ev5-esv5-series.md)
* [Fsv2 シリーズ](fsv2-series.md)
* [GS シリーズ](sizes-previous-gen.md#gs-series)
* [HB シリーズ](hb-series.md)
* [HC シリーズ](hc-series.md)
* [Ls シリーズ](sizes-previous-gen.md#ls-series) 
* [Lsv2 シリーズ](lsv2-series.md)
* [M シリーズ](m-series.md)
* [Mv2 シリーズ](mv2-series.md)<sup>1</sup>
* [Msv2 および Mdsv2 ミディアム メモリ シリーズ](msv2-mdsv2-series.md)<sup>1</sup>
* [NCv2 シリーズ](ncv2-series.md) 
* [NCv3 シリーズ](ncv3-series.md)
* [ND シリーズ](nd-series.md)
* [ND A100 v4-シリーズ](nda100-v4-series.md)
* [NDv2 シリーズ](ndv2-series.md)
* [NVv3 シリーズ](nvv3-series.md)
* [NVv4 シリーズ](nvv4-series.md)
* [NCasT4_v3 シリーズ](nct4-v3-series.md)
* [NDm A100 v4 シリーズ](ndm-a100-v4-series.md)

<sup>1</sup> Mv2 シリーズ、DC シリーズ、NDv2 シリーズ、Msv2 および Mdsv2 シリーズ ミディアム メモリは、第 1 世代 VM のイメージをサポートしておらず、第 2 世代のイメージのサブセットのみをサポートしています。 詳細については、[Mv2 シリーズのドキュメント](mv2-series.md)、[DSv2 シリーズ](dv2-dsv2-series.md)、[ND A100 v4 シリーズ](nda100-v4-series.md)、[NDv2 シリーズ](ndv2-series.md)、および [Msv2 および Mdsv2 ミディアム メモリ シリーズ](msv2-mdsv2-series.md)に関するページを参照してください。


## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace の第 2 世代 VM のイメージ

第 2 世代 VM では、次の Marketplace イメージがサポートされています。

* Windows Server 2019、2016、2012 R2、2012
* Windows 10 Pro、Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10, 20.04 
* RHEL 8.2, 8.1, 8.0, 7.9, 7.7, 7.6, 7.5, 7.4, 7.0, 8.3
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 8.2, 8.3
* Oracle Linux 7.7, 7.7-CI, 7.8

> [!NOTE]
> Mv2 シリーズ、DC シリーズ、ND A100 v4 シリーズ、NDv2 シリーズ、Msv2 および Mdsv2 シリーズのような特定の仮想マシンのサイズでは、これらのイメージのサブセットのみがサポートされている場合があります。詳細については、関連する仮想マシンのサイズに関するドキュメントを参照してください。

## <a name="on-premises-vs-azure-generation-2-vms"></a>オンプレミスと Azure 第 2 世代 VM の比較

Azure では現在、オンプレミスの Hyper-V が第 2 世代 VM に対してサポートする機能の一部がサポートされていません。

| 第 2 世代の機能                | オンプレミス Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| セキュア ブート                         | :heavy_check_mark:  | トラステッド起動 (プレビュー) を使用   |
| シールドされた VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | トラステッド起動 (プレビュー) を使用  |
| 仮想化ベースのセキュリティ (VBS) | :heavy_check_mark:  | :heavy_check_mark:   |
| VHDX 形式                         | :heavy_check_mark:  | :x:   |

詳細については、[トラステッド起動 (プレビュー)](trusted-launch.md) に関するページを参照してください。

## <a name="features-and-capabilities"></a>特徴と機能

### <a name="generation-1-vs-generation-2-features"></a>第 1 世代と第 2 世代の特徴の比較

| 機能 | 第 1 世代 | 第 2 世代 |
|---------|--------------|--------------|
| ブート             | PCAT                      | UEFI                               |
| ディスク コントローラー | IDE                       | SCSI                               |
| VM サイズ         | すべての VM サイズ | [使用可能なサイズを確認する](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 世代と第 2 世代の機能の比較

| 機能 | 第 1 世代 | 第 2 世代 |
|------------|--------------|--------------|
| 2 TB より大きい OS ディスク                    | :x:                | :heavy_check_mark: |
| カスタム ディスク/イメージ/スワップ OS         | :heavy_check_mark: | :heavy_check_mark: |
| 仮想マシン スケール セットのサポート | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| バックアップ/復元                    | :heavy_check_mark: | :heavy_check_mark: |
| Azure Compute Gallery             | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :heavy_check_mark:                |
| [サーバー側暗号化](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |


## <a name="creating-a-generation-2-vm"></a>第 2 世代 VM の作成

### <a name="marketplace-image"></a>Marketplace イメージ

Azure portal または Azure CLI では、UEFI ブートをサポートする Marketplace イメージから第 2 世代 VM を作成できます。

#### <a name="azure-portal"></a>Azure portal

Azure portal で第 2 世代 (Gen2) VM を作成する手順を以下に示します。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. **[リソースの作成]** を選択します。
1. 左側の Azure Marketplace から **[すべて表示]** をクリックします。
1. Gen2 をサポートするイメージを選択します。
1. **Create** をクリックしてください。
1. **[詳細設定]** タブで、 **[VM generation]\(VM の世代\)** セクションで、 **[Gen 2]** オプションを選択します。
1. **[基本]** タブの **[インスタンスの詳細]** で、 **[サイズ]** に移動し、 **[VM サイズの選択]** ブレードを開きます。
1. [サポートされている第 2 世代 VM](#generation-2-vm-sizes) を選択します。
1. 残りのページを参照して、VM の作成を完了します。

![Gen 1 または Gen 2 の VM を選択する](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

PowerShell を使用し、第 1 世代または第 2 世代の SKU を直接参照することによって、VM を作成することもできます。

たとえば、次の PowerShell コマンドレットを使用して、`WindowsServer` プランの SKU の一覧を取得します。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

OS として Windows Server 2012 を使用して VM を作成している場合は、次のように、第 1 世代 (BIOS) または第 2 世代 (UEFI) の VM SKU を選択します。

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

サポートされている Marketplace イメージの現在の一覧については、「[特徴と機能](#features-and-capabilities)」のセクションを参照してください。

#### <a name="azure-cli"></a>Azure CLI

または、Azure CLI を使用して、**Publisher** によって一覧表示される使用可能な第 2 世代のイメージを確認することもできます。

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>マネージド イメージまたはマネージド ディスク

第 2 世代 VM は、第 1 世代 VM を作成する場合と同じ方法でマネージド イメージまたはマネージド ディスクから作成できます。

### <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット

第 2 世代 VM はまた、仮想マシン スケール セットを使用して作成することもできます。 Azure CLI では、Azure スケール セットを使用して第 2 世代 VM を作成します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

* **第 2 世代 VM はすべての Azure リージョンで利用できますか?**  
    はい。 ただし、どのリージョンでも[第 2 世代 VM のサイズ](#generation-2-vm-sizes)のすべてを使用できるわけではありません。 第 2 世代 VM の可用性は、VM サイズの可用性によって異なります。

* **第 1 世代 VM と第 2 世代 VM の価格に違いはありますか?**  
   いいえ。

* **オンプレミスに第 2 世代の VM の .vhd ファイルがあります。この .vhd ファイルを使用して、Azure に第 2 世代の VM を作成できますか?**
  はい。ご自身の第 2 世代の .vhd ファイルを Azure に取り込み、それを使用して第 2 世代の VM を作成できます。 それには、次の手順を実行してください。
    1. ご自身の VM を作成するのと同じリージョンのストレージ アカウントに .vhd をアップロードします。
    1. .vhd ファイルからマネージド ディスクを作成します。 Hyper-V Generation プロパティを V2 に設定します。 Hyper-V Generation プロパティは、マネージド ディスクの作成時に、次の PowerShell コマンドによって設定されます。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. ディスクが使用可能になったら、このディスクを接続して VM を作成します。 作成される VM は第 2 世代 VM です。
    第 2 世代の VM の作成時には、必要に応じてこの VM のイメージを汎用化できます。 汎用化したイメージは、作成する複数の VM で使用できます。

* **OS ディスク サイズを増やすにはどうすればよいですか?**  

  第 2 世代 VM の新規のものとして、2 TiB を超える OS ディスクがあります。 既定では、第 2 世代 VM の OS ディスクは 2 TiB 未満です。 ディスク サイズは、4 TiB の推奨される最大サイズまで増やすことができます。 OS ディスク サイズを増やすには、Azure CLI または Azure portal を使用します。 プログラムでディスクを拡張する方法については、[Windows](./windows/expand-os-disk.md) または [Linux](./linux/resize-os-disk-gpt-partition.md) の **ディスクのサイズ変更** に関するページを参照してください。

  Azure portal から OS ディスク サイズを増やすには:

  1. Azure portal で、VM プロパティ ページに移動します。
  1. VM をシャットダウンして割り当てを解除するには、 **[停止]** ボタンを選択します。
  1. **[ディスク]** セクションで、サイズを増やす OS ディスクを選択します。
  1. **[ディスク]** セクションで、 **[構成]** を選択し、 **[サイズ]** を目的の値に更新します。
  1. VM プロパティ ページに戻り、VM を **[開始]** します。
  
  2 TiB を超える OS ディスクに対して警告が表示されることがあります。 この警告は、第 2 世代 VM には適用されません。 ただし、4 TiB を超える OS ディスク サイズはサポートされていません。

* **第 2 世代 VM では高速ネットワークはサポートされていますか?**  
    はい。 詳細については、[高速ネットワークを使用した VM の作成](../virtual-network/create-vm-accelerated-networking-cli.md)に関するページを参照してください。

* **Azure の第 2 世代 VM では、セキュア ブートまたは vTPM がサポートされますか?**
    vTPM とセキュア ブートはどちらも、第 2 世代 VM のトラステッド起動 (プレビュー) の機能です。 詳細については、[トラステッド起動](trusted-launch.md)に関するページを参照してください。
    
* **第 2 世代では VHDX はサポートされていますか?**  
    いいえ。第 2 世代 VM では VHD のみがサポートされています。

* **第 2 世代 VM では Azure Ultra Disk Storage はサポートされていますか?**  
    はい。

* **VM を第 1 世代から第 2 世代に移行できますか?**  
    いいえ。VM を作成した後にその世代を変更することはできません。 VM の世代を切り替える必要がある場合は、異なる世代の新しい VM を作成してください。

* **Gen2 VM を作成しようとすると、サイズ セレクターで VM サイズが有効になっていないのはなぜですか?**

    これを解決するには、次の手順を実行します。

    1. **[詳細]** タブで、 **[VM generation]\(VM の世代\)** プロパティが **[Gen 2]** に設定されていることを確認します。
    1. [Gen2 VM をサポートする VM サイズ](#generation-2-vm-sizes)を検索していることを確認します。

## <a name="next-steps"></a>次のステップ

第 2 世代 VM での[トラステッド起動 (プレビュー)](trusted-launch-portal.md) の詳細を確認します。

[Hyper-V の第 2 世代仮想マシン](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)について学習します。
