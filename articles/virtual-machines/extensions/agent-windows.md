---
title: Azure 仮想マシン エージェントの概要
description: Azure 仮想マシン エージェントの概要
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8e5118b25238a9483eba3e74a65fe97186d32168
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236848"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 仮想マシン エージェントの概要
Microsoft Azure 仮想マシン エージェント (VM エージェント) は、仮想マシン (VM) と Azure ファブリック コントローラーのやり取りを管理する、セキュリティで保護された簡易プロセスです。 VM エージェントは、Azure 仮想マシン拡張機能の有効化と実行において主要な役割を果たします。 VM 拡張機能は、VM のデプロイ後の構成 (ソフトウェアのインストールと構成など) を有効にします。 VM 拡張機能は、VM の管理者パスワードのリセットなどの回復機能も有効にします。 Azure VM エージェントがないと、VM 拡張機能を実行できません。

この記事では、Azure 仮想マシン エージェントのインストールと検出について説明します。

## <a name="install-the-vm-agent"></a>VM エージェントのインストール

### <a name="azure-marketplace-image"></a>Azure Marketplace イメージ

Azure VM エージェントは、Azure Marketplace イメージからデプロイされたすべての Windows VM に既定でインストールされます。 ポータル、PowerShell、コマンド ライン インターフェイス、Azure Resource Manager テンプレートから Azure Marketplace イメージをデプロイしても、Azure VM エージェントがインストールされます。

Windows Guest Agent Package は 2 つの部分に分かれています。

- プロビジョニング エージェント (PA)
- Windows ゲスト エージェント (WinGA)

VM を起動するには、VM に PA がインストールされている必要がありますが、WinGA はインストール不要です。 VM のデプロイ時に、WinGA をインストールしないことを選択できます。 次の例は、Azure Resource Manager テンプレートを使って *provisionVmAgent* オプションを選択する方法を示しています。

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

エージェントがインストールされていない場合、Azure Backup や Azure Security など、Azure の一部のサービスを使用できません。 これらのサービスでは、拡張機能をインストールする必要があります。 WinGA なしで VM をデプロイした場合は、最新バージョンのエージェントを後からインストールできます。

### <a name="manual-installation"></a>手動のインストール
Windows インストーラー パッケージを使用して、手動で Windows VM エージェントをインストールできます。 Azure にデプロイされるカスタム VM イメージを作成するときには、手動でのインストールが必要な場合があります。 手動で Windows VM エージェントをインストールするには、[VM エージェント インストーラーをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=394789)。 [GitHub Windows IaaS VM エージェントのリリース](https://github.com/Azure/WindowsVMAgent/releases)で、特定のバージョンを検索することもできます。 VM エージェントは、Windows Server 2008 (64 ビット) 以降でサポートされます。

> [!NOTE]
> ProvisionVMAgent を有効にせずにイメージからデプロイされた VM に VMAgent を手動でインストールした後は、AllowExtensionOperations オプションを更新することが重要です。

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>前提条件

- Windows VM エージェントでは、.NET Framework 4.0 を使用して、少なくとも Windows Server 2008 SP2 (64 ビット) を実行する必要があります。 「[Azure での仮想マシン エージェントの最小バージョン サポート](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)」を参照してください

- VM が IP アドレス168.63.129.16 にアクセスできることを確認します。 詳しくは、「[IP アドレス 168.63.129.16 とは](../../virtual-network/what-is-ip-address-168-63-129-16.md)」を参照してください。

- ゲスト VM 内で DHCP が有効になっていることを確認します。 これは、IaaS VM エージェントと拡張機能が機能するためのホストまたはファブリック アドレスを DHCP から取得するために必要です。 静的プライベート IP が必要な場合は、Azure portal または PowerShell を使用してそれを構成し、VM 内の DHCP オプションが有効になっていることを確認する必要があります。 PowerShell を使用した静的 IP アドレスの設定については、[こちら](../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md)をご覧ください。


## <a name="detect-the-vm-agent"></a>VM エージェントの検出

### <a name="powershell"></a>PowerShell

Azure Resource Manager の PowerShell モジュールを使用して、Azure VM に関する情報を取得できます。 VM に関する情報 (Azure VM エージェントのプロビジョニング状態など) を表示するには、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用します。

```powershell
Get-AzVM
```

次の圧縮された出力例は、`OSProfile` の内部で入れ子になった *ProvisionVMAgent* プロパティを示しています。 このプロパティを使用すると、VM に VM エージェントかデプロイされているかどうかを判定できます。

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

次のスクリプトを使用すると、VM 名と VM エージェントの状態の簡潔な一覧が返されます。

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手動での検出

Windows VM にログインすると、タスク マネージャーを使用して、実行中のプロセスを調べることができます。 Azure VM エージェントを確認するには、タスク マネージャーを開いて *[詳細]* タブをクリックし、**WindowsAzureGuestAgent.exe** というプロセス名を探します。 このプロセスが存在する場合は、VM エージェントがインストールされています。


## <a name="upgrade-the-vm-agent"></a>VM エージェントのアップグレード
Windows 用 Azure VM エージェントは、Azure Marketplace からデプロイされたイメージ上で自動的にアップグレードされます。 新しい VM が Azure にデプロイされると、VM のプロビジョニング時に最新の VM エージェントが提供されます。 エージェントを手動でインストールした場合、またはカスタム VM イメージをデプロイしている場合は、イメージの作成時に新しい VM エージェントを含めるように手動で更新する必要があります。

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows ゲスト エージェントの自動ログ収集
Windows ゲスト エージェントには、一部のログを自動的に収集する機能があります。 この機能は、CollectGuestLogs.exe プロセスによって制御されます。 それは PaaS Cloud Services と IaaS Virtual Machines の両方のために存在し、その目的は、VM から診断ログをすばやく自動的に収集し、オフライン分析に使用できるようにすることです。 収集されるログは、イベント ログ、OS ログ、Azure ログ、および一部のレジストリ キーです。 VM のホストに転送される ZIP ファイルが生成されます。 その後、エンジニアリング チームやサポート担当者がこの ZIP ファイルを調べて、VM を所有しているお客様の要求に関する問題を調査できます。

## <a name="guest-agent-and-osprofile-certificates"></a>ゲスト エージェントと OSProfile 証明書
Azure VM エージェントによって、VM または仮想マシン スケール セットの `OSProfile` で参照される証明書がインストールされます。 ゲスト VM 内の証明書 MMC コンソールからこれらの証明書を手動で削除しても、ゲスト エージェントによって再び追加されることが予想されます。
証明書を完全に削除するには、`OSProfile` から削除し、その後、ゲスト オペレーティング システム内から削除する必要があります。

仮想マシンについては、[Remove-AzVMSecret]() を使用し、`OSProfile` から証明書を削除します。

仮想マシン スケール セットの証明書に関する詳細については、仮想マシン スケール セットに関するページの「[非推奨の証明書を削除するにはどうすればよいですか?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-remove-deprecated-certificates-)」を参照してください。


## <a name="next-steps"></a>次のステップ
VM 拡張機能の詳細については、「[Azure 仮想マシンの拡張機能と機能の概要](overview.md)」をご覧ください。
