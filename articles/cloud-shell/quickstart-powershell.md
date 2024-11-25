---
title: Azure Cloud Shell のクイックスタート - PowerShell
description: Azure Cloud Shell のブラウザーで PowerShell を使用する方法について説明します。
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.custom: devx-track-azurepowershell, ignite-fall-2021
ms.openlocfilehash: 8bc87fd6d7c760438ca35b968c767efef6539f0c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705097"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell の PowerShell のクイックスタート

このドキュメントでは、[Azure Portal](https://portal.azure.com/) で Cloud Shell の PowerShell を使う方法について詳しく説明します。

> [!NOTE]
> [Azure Cloud Shell の Bash](quickstart.md) のクイックスタートもあります。

## <a name="start-cloud-shell"></a>Cloud Shell の起動

1. Azure Portal 上部のナビゲーションの **[Cloud Shell]** ボタンをクリックします

   ![Azure portal から Azure Cloud Shell を起動する方法を示すスクリーンショット。](media/quickstart-powershell/shell-icon.png)

2. ドロップダウンで PowerShell 環境を選ぶと、Azure ドライブ `(Azure:)` になります

   ![Azure Cloud Shell 用の PowerShell 環境を選択する方法を示すスクリーンショット。](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell コマンドを実行する

次のように、通常の PowerShell コマンドを Cloud Shell で実行します。

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>仮想マシンを操作する

`VirtualMachines` ディレクトリを使うと、現在のサブスクリプションの下にあるすべての仮想マシンを確認できます。

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>リモート VM 間で PowerShell スクリプトを呼び出す

 > [!WARNING]
 > [Azure VM のリモート管理のトラブルシューティング](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)に関するページを参照してください。

  MyVM1 という VM があるとすると、`Invoke-AzVMCommand` を使ってリモート マシンの PowerShell スクリプト ブロックを呼び出すことができます。

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  また、最初に VirtualMachines ディレクトリに移動して、次のように `Invoke-AzVMCommand` を実行することもできます。

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>リモート VM に対話形式でログオンする

`Enter-AzVM` を使うと、Azure で実行されている VM に対話形式でログインできます。

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

`VirtualMachines`最初にディレクトリに移動して、次のように実行する`Enter-AzVM`こともでき ます：

```azurepowershell-interactive
PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
```

### <a name="discover-webapps"></a>WebApps を検出する

`WebApps` ディレクトリに入ることで、Web アプリ リソース間を簡単に移動することができます。

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

SSH を使ってサーバーまたは VM に対する認証を行うには、Cloud Shell で公開/秘密キー ペアを生成して、リモート マシン上の `authorized_keys` (`/home/user/.ssh/authorized_keys` など) に公開キーを発行します。

> [!NOTE]
> `ssh-keygen` を使って SSH の公開/秘密キーを作成し、Cloud Shell でそれらを `$env:USERPROFILE\.ssh` に発行できます。

### <a name="using-ssh"></a>SSH の使用

[こちら](../virtual-machines/linux/quick-create-powershell.md)の説明に従い、Azure PowerShell コマンドレットを使って新しい VM 構成を作成します。
`New-AzVM` を呼び出してデプロイを始める前に、SSH 公開キーを VM の構成に追加します。
新しく作成される VM には `~\.ssh\authorized_keys` にある公開キーが含まれ、それにより VM への資格情報不要の SSH セッションが有効になります。

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>使用可能なコマンドを一覧表示する

`Azure` ドライブで、「`Get-AzCommand`」と入力してコンテキスト固有の Azure コマンドを取得します。

または、`Get-Command *az* -Module Az.*` を使うと、使用できる Azure コマンドをいつでも検索できます。

## <a name="install-custom-modules"></a>カスタム モジュールをインストールする

`Install-Module` を実行して、[PowerShell ギャラリー][gallery]からモジュールをインストールできます。

## <a name="get-help"></a>Get-Help

Azure Cloud Shell の PowerShell についての情報を取得するには、「`Get-Help`」と入力します。

```azurepowershell-interactive
Get-Help
```

特定のコマンドの場合は、`Get-Help` の後にコマンドレットを指定します。

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure Files を使ってデータを保存する

たとえば `helloworld.ps1` といったスクリプトを作成して `clouddrive` に保存し、それを異なるシェル セッションで使うことができます。

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Cloud Shell で PowerShell を次に使用するときは、`helloworld.ps1` ファイルが Azure Files 共有をマウントした `$HOME\clouddrive` ディレクトリにあります。

## <a name="use-custom-profile"></a>カスタム プロファイルを使う

PowerShell プロファイル (`profile.ps1` または `Microsoft.PowerShell_profile.ps1`) を作成することで、PowerShell 環境をカスタマイズできます。
それを `$profile.CurrentUserAllHosts` (または `$profile.CurrentUserAllHosts`) に保存して、Cloud Shell セッションのすべての PowerShell で読み込めるようにします。

プロファイルの作成方法については、「[プロファイルについて][profile]」を参照してください。

## <a name="use-git"></a>Git を使う

Cloud Shell で Git リポジトリを複製するには、[個人用アクセス トークン][githubtoken]を作成し、それをユーザー名として使う必要があります。 トークンを作成した後は、次のようにしてリポジトリを複製します。

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>シェルを終了します。

「`exit`」と入力してセッションを終了します。

[bashqs]: quickstart.md
[gallery]: https://www.powershellgallery.com/
[customex]: ../virtual-machines/extensions/custom-script-windows.md
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/