---
title: Azure Virtual Desktop での MSIX アプリのアタッチ PowerShell - Azure
description: PowerShell を使用して MSIX アプリのアタッチを Azure Virtual Desktop 用に設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: d77dfb8883aac4e960274ce86de243f25df2f196
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707035"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>PowerShell を使用して MSIX アプリのアタッチを設定する

Azure portal に加え、PowerShell を使用して手動で MSIX アプリのアタッチを設定することもできます。 この記事では、PowerShell を使用して MSIX アプリのアタッチを設定する方法について説明します。

## <a name="requirements"></a>必要条件

MSIX アプリ アタッチを構成するために必要な項目を次に示します。

- 機能する Azure Virtual Desktop のデプロイ。 Azure Virtual Desktop (クラシック) のデプロイ方法については、「[Azure Virtual Desktop でテナントを作成する](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)」を参照してください。 Azure Resource Manager の統合を使用して Azure Virtual Desktop をデプロイする方法については、「[Azure portal を使用してホスト プールを作成する](./create-host-pools-azure-marketplace.md)」を参照してください。
- 少なくとも 1 つのアクティブなセッション ホストが含まれている Azure Virtual Desktop ホスト プール。
- デスクトップ リモート アプリ グループ。
- MSIX パッケージ化ツール。
- MSIX のパッケージされたアプリケーションは、ファイル共有にアップロードされる MSIX イメージに展開されます。
- MSIX パッケージが格納される Azure Virtual Desktop デプロイ内のファイル共有。
- MSIX イメージをアップロードしたファイル共有には、ホスト プール内のすべての仮想マシン (VM) からアクセスできる必要もあります。 ユーザーには、イメージにアクセスするための読み取り専用アクセス許可が必要になります。
- PowerShell Core をダウンロードしてインストールします。
- パブリック プレビュー Azure PowerShell モジュールをダウンロードし、ローカル フォルダーに展開します。
- 次のコマンドレットを実行して、Azure モジュールをインストールします。

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Azure にサインインしてモジュールをインポートする

すべての要件を準備したら、管理者特権のコマンド プロンプトで PowerShell Core を開き、次のコマンドレットを実行します。

```powershell
Connect-AzAccount
```

実行後、資格情報を使用してアカウントを認証します。 この場合、デバイスの URL またはトークンを求められることがあります。

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Az.WindowsVirtualDesktop モジュールをインポートする

この記事の手順を実行するには、Az.DesktopVirtualization モジュールが必要です。

>[!NOTE]
>パブリック プレビューでは、モジュールを別の ZIP ファイルとして提供し、手動でインポートする必要があります。

開始する前に、次のコマンドレットを実行して、Az.DesktopVirtualization モジュールが既にセッションまたは VM にインストールされているかどうかを確認できます。

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

既存のモジュールのコピーをアンインストールしてからやり直す場合は、次のコマンドレットを実行します。

```powershell
Uninstall-Module Az.DesktopVirtualization
```

モジュールが VM でブロックされている場合は、このコマンドレットを実行してブロックを解除します。

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

クリーンアップが終了したら、モジュールをインポートします。

1. 次のコマンドレットを実行し、カスタム コードの実行に同意するかどうかを確認するメッセージが表示されたら **R** キーを押します。

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. インポート コマンドレットを実行した後、次のコマンドレットを実行して、MSIX 用のコマンドレットがあるかどうかを確認します。

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   コマンドレットがある場合、出力は次のようになります。

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   この出力が表示されない場合は、PowerShell と PowerShell Core のすべてのセッションを閉じて、もう一度やり直してください。

## <a name="set-up-helper-variables"></a>ヘルパー変数を設定する

モジュールをインポートしたら、ヘルパー変数を設定する必要があります。 次の例では、それぞれの変数を設定する方法を示します。

サブスクリプション ID を取得するには:

```powershell
Get-AzContext -ListAvailable | fl
```

名前で Azure テナントとサブスクリプションのコンテキストを選択するには:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

サブスクリプション変数を設定するには:

```powershell
$subId = $obj.Subscription.Id
```

ワークスペース名を設定するには:

```powershell
$ws = "<WorksSpaceName>"
```

ホストプール名を設定するには:

```powershell
$hp = "<HostPoolName>"
```

セッション ホスト VM が構成されているリソース グループを設定するには:

```powershell
$rg = "<ResourceGroupName>"
```

最後に、すべての変数が正しく設定されていることを確認するには:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>MSIX パッケージをホスト プールに追加する

すべてを設定したら、MSIX パッケージをホスト プールに追加します。 そのためには、まず、MSIX イメージへの UNC パスを取得する必要があります。

UNC パスを使用して、次のコマンドレットを実行して MSIX イメージを展開します。

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

次のコマンドレットを実行して、目的のホスト プールに MSIX パッケージを追加します。

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

完了したら、次のコマンドレットを使用してパッケージが作成されたことを確認します。

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>MSIX パッケージをホスト プールから削除する

パッケージをホスト プールから削除するには:

次のコマンドレットを使用して、ホスト プールと関連付けられたすべてのパッケージの一覧を取得し、削除するパッケージの名前を出力から検索します。

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

または、次のコマンドレットを使用して、表示名に基づいて特定のパッケージを取得することもできます。

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

パッケージを削除するには、次のコマンドレットを実行します。

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX アプリをアプリ グループに発行する

前のセクションの手順を完了している場合にのみ、このセクションの手順に従うことができます。 ホスト プールにアクティブなセッション ホストがあり、少なくとも 1 つのデスクトップ アプリ グループがあり、ホスト プールに MSIX パッケージを追加している場合は進めることができます。

MSIX パッケージからアプリ グループにアプリを発行するには、その名前を検索してから、発行コマンドレットでその名前を使用する必要があります。

アプリを発行するには:

次のコマンドレットを実行して、使用可能なすべてのアプリ グループを一覧表示します。

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

アプリの発行先のアプリ グループの名前が見つかったら、次のコマンドレットでその名前を使用します。

```powershell
$grName = "<AppGroupName>"
```

最後に、アプリを発行する必要があります。

- デスクトップ アプリ グループに MSIX アプリケーションを発行するには、次のコマンドレットを実行します。

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- アプリをリモート アプリ グループに発行するには、代わりに次のコマンドレットを実行します。

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>ユーザーが同じホスト プール内のリモート アプリ グループとデスクトップ アプリ グループの両方に割り当てられている場合、ユーザーがリモート デスクトップに接続すると、両方のグループの MSIX アプリが表示されます。

## <a name="next-steps"></a>次のステップ

[Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) で、コミュニティにこの機能に関する質問を行うことができます。

また、Azure Virtual Desktop についてのフィードバックは、[Azure Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます。

役に立つその他の記事を次に示します。

- [MSIX アプリのアタッチの用語集](app-attach-glossary.md)
- [MSIX アプリ アタッチに関するごよくある質問](app-attach-faq.yml)
