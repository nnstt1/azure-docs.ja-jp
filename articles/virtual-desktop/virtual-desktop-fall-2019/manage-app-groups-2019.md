---
title: Azure Virtual Desktop (クラシック) のアプリ グループを管理する - Azure
description: Azure Virtual Desktop (クラシック) のテナントを Azure Active Directory (AD) に設定する方法を学習します。
author: Heidilohr
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6fe87fb7fc0cbe9e727fe1539d8424d9ee1fe1b1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253264"
---
# <a name="tutorial-manage-app-groups-for-azure-virtual-desktop-classic"></a>チュートリアル: Azure Virtual Desktop (クラシック) のアプリ グループを管理する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../manage-app-groups.md)を参照してください。

Azure Virtual Desktop の新しいホスト プール向けに作成される既定のアプリ グループには、完全なデスクトップも公開されています。 加えて、ホスト プールには RemoteApp アプリケーション グループ (複数可) を作成することができます。 このチュートリアルに沿って作業すれば、RemoteApp アプリ グループを作成して、独自の **[スタート]** メニュー アプリを公開することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * RemoteApp グループを作成する。
> * RemoteApp プログラムへのアクセスを許可する。

作業を開始する前に、PowerShell セッションで使用する [Azure Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します (まだ行っていない場合)。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>RemoteApp グループを作成する

1. 次の PowerShell コマンドレットを実行して、新しい空の RemoteApp アプリ グループを作成します。

   ```powershell
   New-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <appgroupname> -ResourceType "RemoteApp"
   ```

2. (省略可) アプリ グループが作成されたことを確認したければ、次のコマンドレットを実行すると、ホスト プールのすべてのアプリ グループが一覧表示されます。

   ```powershell
   Get-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname>
   ```

3. 次のコマンドレットを実行して、ホスト プールの仮想マシン イメージにある **[スタート]** メニュー アプリの一覧を取得します。 **FilePath**、**IconPath**、**IconIndex** など、公開したいアプリケーションの重要な情報の値を書き留めます。

   ```powershell
   Get-RdsStartMenuApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

4. 次のコマンドレットを実行して、`AppAlias` に基づくアプリケーションをインストールします。 `AppAlias` は、手順 3. の出力を実行すると利用できるようになります。

   ```powershell
   New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (省略可) 次のコマンドレットを実行して、手順 1. で作成したアプリケーション グループに新しい RemoteApp プログラムを発行します。

   ```powershell
    New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. アプリが公開されたことを確認するために、次のコマンドレットを実行します。

   ```powershell
    Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

7. このアプリ グループに発行するアプリケーションごとに手順 1. から手順 5. を繰り返します。
8. 次のコマンドレットを実行して、アプリ グループ内の RemoteApp プログラムへのアクセスをユーザーに許可します。

   ```powershell
   Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリ グループを作成して、RemoteApp プログラムによりそれに値を設定し、アプリ グループにユーザーを割り当てる方法について説明しました。 検証ホスト プールを作成する方法については、次のチュートリアルを参照してください。 運用環境に展開する前に、検証ホスト プールを使用してサービスの更新プログラムを監視できます。

> [!div class="nextstepaction"]
> [サービスの更新プログラムを検証するためのホスト プールを作成する](create-validation-host-pool-2019.md)
