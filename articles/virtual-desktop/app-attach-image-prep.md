---
title: Azure Virtual Desktop の MSIX アプリのアタッチ イメージを準備する - Azure
description: Azure Virtual Desktop ホスト プールの MSIX アプリのアタッチ イメージを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: e804168d56092b6e5451aa26c7a2106b96672865
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587349"
---
# <a name="prepare-an-msix-image-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用の MSIX イメージを準備する

MSIX アプリのアタッチはアプリケーション レイヤー ソリューションです。これを使用すると、MSIX パッケージからユーザー セッションにアプリを動的にアタッチできます。 MSIX パッケージ システムは、アプリをオペレーティング システムから分離することで、仮想マシンのイメージを簡単に作成できるようにします。 また、MSIX パッケージを使用すると、ユーザーが自分の仮想マシンでアクセスできるアプリをより細かく制御できます。 アプリをマスター イメージから分離し、後でユーザーに提供することもできます。

デスクトップ インストーラー (MSI、EXE、ClickOnce、App-V、スクリプトなど) を MSIX に変換する手順については、「[任意のデスクトップ インストーラー (MSI、EXE、ClickOnce、または App-V) から MSIX パッケージを作成する](/windows/msix/packaging-tool/create-app-package)」を参照してください。

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>MSIX 用の VHD または VHDX パッケージを生成する

MSIX パッケージは、正常に動作するために VHD または VHDX 形式である必要があります。 つまり、まず、VHD または VHDX パッケージを作成する必要があります。

>[!NOTE]
>まだ行っていない場合は、「[Windows 10 上に Hyper-V をインストールする](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)」の指示に従って、確実に Hyper-V を有効にしてください。

MSIX 用の VHD または VHDX パッケージを生成するには:

1. 最初に PowerShell を開きます。
2. 続いて、次のコマンドレットを実行して VHD を作成します。

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > VHD が、展開された MSIX パッケージを保持するのに十分大きいことを確認します。

3. 次のコマンドレットを実行して、作成したばかりの VHD をマウントします。

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. 続いて、次のコマンドレットを実行して、マウントした VHD を初期化します。

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. その後、次のコマンドレットを実行して、初期化された VHD 用の新しいパーティションを作成します。

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. 次のコマンドレットを実行して、パーティションをフォーマットします。

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. 最後に、マウントされた VHD に親フォルダーを作成します。 この手順が必要なのは、MSIX パッケージが正常に機能するために親フォルダーが必要であるためです。 親フォルダーが存在する限り、親フォルダーにどのような名前を付けてもかまいません。

## <a name="expand-msix"></a>MSIX を展開する

その後、ファイルを VHD に "アンパック" して MSIX イメージを展開する必要があります。

MSIX イメージを展開するには:

1. [msixmgr ツール](https://aka.ms/msixmgr)をダウンロードし、.zip フォルダーをセッション ホスト VM 内のフォルダーに保存します。

2. msixmgr ツールの .zip フォルダーを解凍します。

3. msixmgr ツールを解凍したフォルダーに、ソース MSIX パッケージを配置します。

4. 管理者としてコマンドプロンプトを開き、msixmgr ツールをダウンロードして解凍したフォルダーに移動します。

5. 次のコマンドレットを実行して、前のセクションで作成した VHD に MSIX をアンパックします。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    アンパックが完了すると、次のメッセージが表示されます。

    > パッケージの ACL が正常にアンパックされ、適用されました: \<package name\>.msix

    >[!NOTE]
    > ネットワーク上、またはインターネットに接続されていないデバイス上のビジネス向けまたは教育機関向け Microsoft Store のパッケージを使用する場合、アプリを実行するには、Microsoft Store からパッケージ ライセンスをダウンロードしてインストールする必要があります。 ライセンスを取得するには、「[パッケージをオフラインで使用する](app-attach.md#use-packages-offline)」をご覧ください。

6. マウントされた VHD にアクセスし、アプリ フォルダーを開いて、パッケージの内容があることを確認します。

7. VHD のマウントを解除します。

## <a name="upload-msix-image-to-share"></a>共有する MSIX イメージをアップロードする

MSIX パッケージを作成した後、結果として得られた VHD、VHDX、または CIM ファイルを、ユーザーの仮想マシンがアクセスできる共有にアップロードする必要があります。

## <a name="next-steps"></a>次のステップ

[Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) で、コミュニティにこの機能に関する質問を行うことができます。

また、Azure Virtual Desktop についてのフィードバックは、[Azure Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます。

役に立つその他の記事を次に示します。

- [MSIX アプリのアタッチの用語集](app-attach-glossary.md)
- [MSIX アプリ アタッチに関するごよくある質問](app-attach-faq.yml)
