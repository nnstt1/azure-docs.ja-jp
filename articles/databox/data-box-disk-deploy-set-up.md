---
title: Azure Data Box Disk を開梱、接続、ロック解除するチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk の開梱、ディスクへの接続、パスキーの取得、Windows クライアントと Linux クライアントにおけるディスクのロック解除の方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: b58fd8ba86febba41715aba4340838a0f494e6ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591656"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>チュートリアル:Azure Data Box Disk の開梱、接続、ロック解除

このチュートリアルでは、Azure Data Box Disk の開梱、接続、ロック解除の方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box Disk を開梱する
> * ディスクに接続してパスキーを取得する
> * Windows クライアントでディスクのロックを解除する
> * Linux クライアントでディスクのロックを解除する

::: zone-end

::: zone target="chromeless"

## <a name="unpack-connect-and-unlock-azure-data-box-disk"></a>Azure Data Box Disk の開梱、接続、ロック解除

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [チュートリアル:Azure Data Box Disk を注文する](data-box-disk-deploy-ordered.md)」を参照してください。
2. ディスクの受け取りが済んでいて、ポータルでジョブの状態が **[配信済み]** に更新されていること。
3. Data Box Disk のロック解除ツールをインストールするクライアント コンピューターがあること。 クライアント コンピューターの要件は以下のとおりです。
    - [サポート対象のオペレーティング システム](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)が実行されていること。
    - Windows クライアントである場合は、その他の[必須ソフトウェア](data-box-disk-system-requirements.md#other-required-software-for-windows-clients)がインストールされていること。  

## <a name="unpack-your-disks"></a>ディスクを開梱する

 次の手順に従ってディスクを開梱します。

1. Data Box Disk は、小さな梱包箱に入って郵送されます。 箱を開けて内容物を取り出します。 1 台から 5 台の SSD (Solid State Disk) と各ディスクにつき 1 本の USB 接続ケーブルが同梱されていることを確認してください。 箱を調べて、改ざんの痕跡や、その他明らかな損傷がないか確認します。

    ![Data Box Disk の発送パッケージ](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 梱包箱が改ざんされていたり、ひどく破損している場合は、箱を開けないでください。 Microsoft サポートに問い合わせて、ディスクが正常に使用できる状態にあるかどうか、また交換ディスクを発送してもらう必要があるかどうかを評価してください。
3. 返送用の配送先住所ラベルが入った透明な袋が (現在のラベルの下に) 同梱されていることを確認します。 このラベルが紛失するか破損した場合は、いつでも Azure portal から新しいラベルをダウンロードして印刷することができます。

    ![Data Box Disk の配送先住所ラベル](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 箱と梱包材は、ディスクを返送するときのために保管しておいてください。

## <a name="connect-to-disks-and-get-the-passkey"></a>ディスクに接続してパスキーを取得する

1. 前提条件に記載したサポート対象 OS が実行されているクライアント コンピューターに、同梱されているケーブルを使用してディスクを接続します。

    ![Data Box Disk の接続](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)

2. Azure portal で [Data Box Disk の注文] に移動します。 **[全般] > [すべてのリソース]** の順に移動して Data Box Disk を検索し、[Data Box Disk の注文] を選択します。 コピー アイコンを使用してパスキーをコピーします。 このパスキーは、ディスクのロック解除に使用されます。

    ![Data Box Disk のロック解除パスキー](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png)

接続先が Windows クライアントまたは Linux クライアントのどちらであるかに応じて、ディスクのロックを解除する手順は異なります。

## <a name="unlock-disks-on-windows-client"></a>Windows クライアントでディスクのロックを解除する

ディスクを接続し、ロックを解除するには、次の手順を実行します。

1. Azure portal で [Data Box Disk の注文] に移動します。 **[全般] > [すべてのリソース]** の順に移動して Data Box Disk を検索し、[Data Box Disk の注文] を選択します。
2. Windows クライアントに対応する Data Box Disk ツールセットをダウンロードします。 このツールセットには、Data Box Disk ロック解除ツール、Data Box Disk 検証ツール、および Data Box Disk 分割コピー ツールの 3 つが含まれています。

    この手順では、Data Box Disk ロック解除ツールのみを使用します。 他の 2 つのツールは後で使用されます。

    > [!div class="nextstepaction"]
    > [Windows 用 Data Box Disk ツールセットをダウンロードする](https://aka.ms/databoxdisktoolswin)

3. データのコピーに使用するのと同じコンピューターでツールセットを抽出します。
4. 同じコンピューターで、管理者として Windows PowerShell を実行するかコマンド プロンプト ウィンドウを開きます。
5. (省略可) ディスクのロック解除に使用しているコンピューターがオペレーティング システムの要件を満たしていることを確認するために、システム チェック コマンドを実行します。 サンプル出力を次に示します。

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.

    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. `DataBoxDiskUnlock.exe` を実行し、「[ディスクに接続してパスキーを取得する](#connect-to-disks-and-get-the-passkey)」で取得したパスキーを指定します。 ディスクに割り当てられたドライブ文字が表示されます。 サンプル出力を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1

    Following volumes are unlocked and verified.
    Volume drive letters: D:

    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. 今後ディスクを再挿入するたびにロック解除の手順を繰り返します。 Data Box Disk ロック解除ツールでわからないことがある場合は、`help` コマンドを使用してください。

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>

    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help

    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.

    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  

8. ディスクのロックが解除されたら、ディスクの内容を表示することができます。

    ![Data Box Disk の内容](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

ディスクのロックを解除中に問題が発生した場合は、[ロック解除に関する問題のトラブルシューティングを行う](data-box-disk-troubleshoot-unlock.md)方法をご覧ください。

## <a name="unlock-disks-on-linux-client"></a>Linux クライアントでディスクのロックを解除する

1. Azure portal で **[全般] > [デバイスの詳細]** に移動します。
2. Linux クライアントに対応する Data Box Disk ツールセットをダウンロードします。  

    > [!div class="nextstepaction"]
    > [Linux 用 Data Box Disk ツールセットをダウンロードする](https://aka.ms/databoxdisktoolslinux)

3. Linux クライアントで、ターミナルを開きます。 ソフトウェアをダウンロードしたフォルダーに移動します。 これらのファイルを実行できるように、ファイルのアクセス許可を変更します。 次のコマンドを入力します。

    `chmod +x DataBoxDiskUnlock_x86_64` 

    `chmod +x DataBoxDiskUnlock_Prep.sh` 

    サンプル出力を次に示します。 chmod コマンドが実行されたら、`ls` コマンドを実行することで、ファイルのアクセス許可が変更されたことを確認できます。 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```

4. Data Box Disk ロック解除ソフトウェアに必要なすべてのバイナリがインストールされるように、スクリプトを実行します。 `sudo` を使用して、root としてコマンドを実行します。 バイナリが正常にインストールされると、ターミナルに、その効果に対するメモが表示されます。

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    スクリプトはまず、クライアント コンピューターが、サポートされているオペレーティング システムを実行しているかどうかを確認しています。 サンプル出力を次に示します。 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 

        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```

 
5. 「`y`」を入力してインストールを続行します。 スクリプトによってインストールされるパッケージは次のとおりです。 
   - **epel リリース** - 次の 3 つのパッケージを含むリポジトリ。 
   - **dislocker と fuse dislocker** - これらのユーティリティは、BitLocker で暗号化されたディスクを復号化するのに役立ちます。 
   - **ntfs-3g** - NTFS ボリュームのマウントに役立つパッケージ。 
 
     パッケージが正常にインストールされると、ターミナルには、その影響に対する通知が表示されます。     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Data Box Disk ロック解除ツールを実行します。 「[ディスクに接続してパスキーを取得する](#connect-to-disks-and-get-the-passkey)」で取得したパスキーを、Azure portal から指定します。 オプションで、ロックを解除する、BitLocker で暗号化されたボリュームの一覧を指定します。 パスキーとボリュームの一覧は、単一引用符で囲んで指定する必要があります。 

   次のコマンドを入力します。
 
   ```bash
   sudo ./DataBoxDiskUnlock_x86_64 /PassKey:'<Your passkey from Azure portal>'
   ```

   サンプル出力を次に示します。 
 
   ```output
   [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:'qwerqwerqwer'  
   
   START: Mon Aug 13 14:25:49 2018 
   Volumes: /dev/sdbl 
   Passkey: qwerqwerqwer 
   
   Volumes for data copy : 
   /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
   END: Mon Aug 13 14:26:02 2018
   ```
   データをコピーできるボリュームのマウント ポイントが表示されます。

7. 今後ディスクを再挿入するたびにロック解除の手順を繰り返します。 Data Box Disk ロック解除ツールでわからないことがある場合は、`help` コマンドを使用してください。 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    サンプル出力を次に示します。 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:'<passkey from Azure_portal>' 
    
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' 
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' /Volumes:'/dev/sdbl' 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. ディスクのロックが解除されたら、マウント ポイントに移動して、ディスクの内容を表示することができます。 これで、データを *BlockBlob* フォルダーまたは *PageBlob* フォルダーにコピーする準備が整いました。 

    ![Data Box Disk の内容 2](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


ディスクのロックを解除中に問題が発生した場合は、[ロック解除に関する問題のトラブルシューティングを行う](data-box-disk-troubleshoot-unlock.md)方法をご覧ください。 

::: zone-end

::: zone target="chromeless"

1. ディスクを開梱し、同梱されているケーブルを使用してディスクをクライアント コンピューターに接続します。
2. データのコピーに使用するのと同じコンピューター上で、Data Box Disk ツールセットをダウンロードし、展開します。

    > [!div class="nextstepaction"]
    > [Windows 用 Data Box Disk ツールセットをダウンロードする](https://aka.ms/databoxdisktoolswin)

    or
    > [!div class="nextstepaction"]
    > [Linux 用 Data Box Disk ツールセットをダウンロードする](https://aka.ms/databoxdisktoolslinux) 

3. Windows クライアント上でディスクのロックを解除するには、同じコンピューター上で管理者としてコマンド プロンプト ウィンドウを開くか、Windows PowerShell を実行します。

    - Data Box Disk Unlock ツールがインストールされているフォルダーで、次のコマンドを入力します。

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Azure portal の **[全般] > [デバイスの詳細]** でパスキーを取得し、ここで指定します。 ディスクに割り当てられたドライブ文字が表示されます。 
4. Linux クライアント上でディスクのロックを解除するには、ターミナルを開きます。 ソフトウェアをダウンロードしたフォルダーに移動します。 以下のコマンドを入力し、これらのファイルを実行できるように、ファイルのアクセス許可を変更します。 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    スクリプトを実行し、必要なすべてのバイナリをインストールします。

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Data Box Disk ロック解除ツールを実行します。 Azure portal の **[全般] > [デバイスの詳細]** でパスキーを取得し、ここで指定します。 オプションで、ロックを解除する、BitLocker で暗号化されたボリュームの一覧を単一引用符で囲んで指定します。

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:'<Your passkey from Azure portal>'
    ```      
5. 今後ディスクを再挿入するたびにロック解除の手順を繰り返します。 Data Box Disk ロック解除ツールでわからないことがある場合は、help コマンドを使用してください。

ディスクのロックが解除されたら、ディスクの内容を表示することができます。

ディスクの設定とロック解除の方法の詳細については、[Data Box Disk の設定](data-box-disk-deploy-set-up.md)に関するページに移動してください。

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box Disk を開梱する
> * ディスクに接続してパスキーを取得する
> * Windows クライアントでディスクのロックを解除する
> * Linux クライアントでディスクのロックを解除する


次のチュートリアルに進み、Data Box Disk にデータをコピーする方法を学習してください。

> [!div class="nextstepaction"]
> [データを Data Box Disk にコピーする](./data-box-disk-deploy-copy-data.md)

::: zone-end

