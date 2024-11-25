---
title: Azure portal - SSE でカスタマー マネージド キーを有効にする - マネージド ディスク
description: Azure portal を使用して、マネージド ディスクでカスタマー マネージド キーを有効にします。
author: roygara
ms.date: 09/27/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: c5941c8307e5aacc99f6fce627dafc154b6bc786
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091180"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Azure portal を使用して、マネージド ディスクでカスタマー マネージド キーを使用し、サーバー側の暗号化を有効にする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 

Azure Disk Storage を使用すると、選択した場合は、マネージド ディスクにサーバー側の暗号化 (SSE) を使用しているときに独自のキーを管理できます。 カスタマー マネージド キーを使用する SSE とその他のマネージド ディスクの暗号化の概念については、ディスクの暗号化の記事の「**カスタマー マネージド キー**」セクションを参照してください ([カスタマー マネージド キー](disk-encryption.md#customer-managed-keys))

## <a name="restrictions"></a>制限

現在、カスタマー マネージド キーには次の制限があります。

- この機能がディスクで有効になっている場合、無効にすることはできません。
    これを回避する必要がある場合は、カスタマー マネージド キーを使用していないまったく別のマネージド ディスクにすべてのデータをコピーする必要があります。

    - Linux の場合:[マネージド ディスクをコピーする](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows の場合:[マネージド ディスクをコピーする](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

以下のセクションには、マネージド ディスクに対してカスタマー マネージド キーを有効にして使用する方法が含まれています。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM をデプロイする

キー コンテナーとディスク暗号化セットの作成と設定が完了したので、暗号化を使用して VM をデプロイできます。
VM のデプロイ プロセスは標準的なデプロイ プロセスと似ています。唯一の違いは、VM を他のリソースと同じリージョンにデプロイしたうえで、カスタマー マネージド キーを使用する必要があることです。

1. 「**Virtual Machines**」で検索し、 **[+ 追加]** を選択して、VM を作成します。
1. **[基本]** ブレードで、ディスク暗号化セットおよび Azure Key Vault と同じリージョンを選択します。
1. **[基本]** ブレードで、必要に応じてその他の値を入力します。

    ![リージョンの値が強調表示されている、VM 作成エクスペリエンスのスクリーンショット。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. **[ディスク]** ブレードで、 **[顧客が管理するキーを使用した保存時の暗号化]** を選択します。
1. **[ディスク暗号化セット]** ボックスの一覧で、使用するディスク暗号化セットを選択します。
1. 必要に応じて、残りの選択を行います。

    ![VM 作成エクスペリエンスの [ディスク] ブレードのスクリーンショット。 [ディスク暗号化セット] ドロップダウンが強調表示された状態です。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>既存のディスクで有効にする

> [!CAUTION]
> VM にアタッチされているすべてのディスクでディスク暗号化を有効にするには、VM を停止する必要があります。
    
1. 使用しているディスク暗号化セットのいずれかと同じリージョンにある VM に移動します。
1. VM を開き、 **[停止]** を選択します。

    ![[停止] ボタンが強調表示された、サンプル VM のメイン オーバーレイのスクリーンショット。](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM の停止が完了した後に、 **[ディスク]** を選択し、暗号化するディスクを選択します。

    ![[ディスク] ブレードが開いた状態のサンプル VM のスクリーンショット。 選択するディスクの例として、OS ディスクが強調表示されています。](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. **[暗号化]** を選択し、 **[顧客が管理するキーを使用した保存時の暗号化]** を選択してから、ボックスの一覧で、ディスク暗号化セットを選択します。
1. **[保存]** を選択します。

    ![サンプル OS ディスクのスクリーンショット。 [暗号化] ブレードが開いており、[顧客が管理するキーを使用した保存時の暗号化] が選択され、Azure Key Vault の例が表示されている。 これらの選択を行った後、[保存] ボタンを選択します。](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 暗号化する VM にアタッチされている他のすべてのディスクに対して、このプロセスを繰り返します。
1. 使用するディスクでカスタマー マネージド キーへの切り替えが完了し、暗号化するアタッチ済みディスクが他になくなったら、VM を起動することができます。

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

### <a name="enable-automatic-key-rotation-on-an-existing-disk-encryption-set"></a>既存のディスク暗号化セットでキーの自動キー交換を有効にする

1. [自動キー交換](disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)を有効にしたいディスク暗号化セットに移動します。
1. **[設定]** で **[キー]** を選択します。
1. **[自動キー交換]** を選択し、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [カスタマー マネージド キーで暗号化されたディスクを作成するための Azure Resource Manager テンプレートを探索する](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault とは](../key-vault/general/overview.md)
- [カスタマー マネージド キー対応ディスクを含むコンピューターをレプリケートする](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell を使用して Azure への VMware VM のディザスター リカバリーを設定する](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell と Azure Resource Manager を使用して Azure への Hyper-V VM のディザスター リカバリーを設定する](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)