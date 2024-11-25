---
title: Azure Virtual Desktop 用 GPU を構成する - Azure
description: Azure Virtual Desktop 内で GPU アクセラレーションを使用したレンダリングとエンコードを有効にする方法。
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 0bd470650ca3a2d6a8fd2d672d2eac0e5790dc89
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241005"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用グラフィックス処理装置 (GPU) アクセラレーションを構成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md)を参照してください。

Azure Virtual Desktop は、アプリのパフォーマンスとスケーラビリティを向上させるために、GPU アクセラレーションを使用したレンダリングとエンコードをサポートしています。 GPU アクセラレーションは、グラフィックを多用するアプリケーションの場合に特に重要です。

この記事で説明するようにして、GPU で最適化された Azure 仮想マシンを作成し、それをホスト プールに追加して、レンダリングとエンコードに GPU アクセラレーションを使うように構成します。 この記事では、Azure Virtual Desktop テナントを既に構成してあるものとします。

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>GPU で最適化する Azure 仮想マシンの適切なサイズを選択する

Azure の [NV シリーズ](../virtual-machines/nv-series.md)、[NVv3 シリーズ](../virtual-machines/nvv3-series.md)、または [NVv4 シリーズ](../virtual-machines/nvv4-series.md)の各 VM サイズのいずれかを選択します。 これらは、アプリとデスクトップの仮想化に合わせて調整され、ほとんどのアプリと Windows ユーザー インターフェイスで GPU アクセラレーションを有効にすることができます。 実際のホスト プールに適した選択は、特定のアプリ ワークロード、ユーザー エクスペリエンスの望ましい品質、コストなど、さまざまなの要因に依存します。 一般に、GPU が大きくて高機能であるほど、特定のユーザー密度でのユーザー エクスペリエンスは向上しますが、GPU サイズが小さく、分割されている場合は、コストと品質をよりきめ細やかに制御することができます。 VM を選択するときは、NV シリーズの VM の提供終了について考慮してください。詳細については、[NV の提供終了](../virtual-machines/nv-series-retirement.md)に関する記事を参照してください

>[!NOTE]
>Azure の NC、NCv2、NCv3、ND、NDv2 シリーズの VM は通常、Azure Virtual Desktop のセッション ホストには適していません。 これらの VM は、NVIDIA CUDA を使用して構築されたものなど、特殊な高パフォーマンスのコンピューティング ツールまたは機械学習ツール向けに設計されています。 ほとんどのアプリまたは Windows ユーザー インターフェイスでは、GPU アクセラレーションはサポートされていません。


## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>ホスト プールを作成し、仮想マシンをプロビジョニングして、アプリ グループを構成する

選択したサイズの VM を使って、新しいホスト プールを作成します。 手順については、「[チュートリアル:Azure portal を使用してホスト プールを作成する](./create-host-pools-azure-marketplace.md)」を参照してください。

Azure Virtual Desktop では、次のオペレーティング システム内で GPU アクセラレーションを使用したレンダリングとエンコードがサポートされています。

* Windows 10 バージョン 1511 以降
* Windows Server 2016 以降

>[!NOTE]
>マルチセッション OS は具体的には記載されていませんが、NV インスタンスの GRID ライセンスでは、25 人の同時ユーザーがサポートされます。「[NV シリーズ](../virtual-machines/nv-series.md)」を参照してください

また、アプリ グループを構成するか、または新しいホスト プールを作成すると自動的に作成される ("Desktop Application Group" という名前の) 既定のデスクトップ アプリ グループを使う必要があります。 手順については、[Azure Virtual Desktop 用アプリ グループの管理チュートリアル](./manage-app-groups.md)のページをご覧ください。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>サポートされているグラフィック ドライバーを仮想マシンにインストールする

Azure Virtual Desktop 内で Azure N シリーズ VM の GPU 機能を利用するには、適切なグラフィック ドライバーをインストールする必要があります。 「[サポートされているオペレーティング システムとドライバー](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers)」の手順に従って、ドライバーをインストールします。 サポートされているのは、Azure によって配布されたドライバーのみです。

* Azure NV シリーズまたは NVv3 シリーズの VM の場合、NVIDIA CUDA ドライバーではなく NVIDIA GRID ドライバーでのみ、ほとんどのアプリと Windows ユーザー インターフェイスの GPU アクセラレータがサポートされます。 ドライバーを手動でインストールする場合は、必ず GRID ドライバーをインストールしてください。 Azure VM 拡張機能を使用してドライバーをインストールすることを選択した場合は、これらの VM サイズに応じて、GRID ドライバーが自動的にインストールされます。
* Azure NVv4 シリーズ VM の場合は、Azure によって提供される AMD ドライバーをインストールします。 Azure VM 拡張機能を使用して自動的にインストールすることも、手動でインストールすることもできます。

ドライバーをインストールした後は、VM を再起動する必要があります。 上記の説明の検証手順を使って、グラフィック ドライバーが正常にインストールされたことを確認します。

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU アクセラレーションを使用するアプリ レンダリングを構成する

既定では、マルチセッション構成で実行されるアプリとデスクトップは CPU でレンダリングされ、使用可能な GPU はレンダリングに利用されません。 セッション ホストのグループ ポリシーを構成して、GPU アクセラレーションを使用するレンダリングを有効にします。

1. ローカル管理者特権を持つアカウントを使って、VM のデスクトップに接続します。
2. [スタート] メニューを開き、「gpedit.msc」と入力して、グループ ポリシー エディターを開きます。
3. ツリーで、 **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[リモート セッション環境]** に移動します。
4. ポリシー **[Use hardware graphics adapters for all Remote Desktop Services sessions] (すべてのリモート デスクトップ サービス セッションにハードウェアのグラフィックス アダプターを使用する)** を選択し、このポリシーを **[有効]** に設定して、リモート セッションでの GPU レンダリングを有効にします。

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU アクセラレーションを使用するフレーム エンコードを構成する

リモート デスクトップでは、リモート デスクトップ クライアントへの送信用にアプリとデスクトップでレンダリングされるすべてのグラフィックスが (GPU または CPU のどちらでレンダリングされるかに関係なく) エンコードされます。 画面の一部が頻繁に更新される場合、画面のこの部分はビデオ コーデック (H.264/AVC) を使用してエンコードされます。 既定では、リモート デスクトップによるこのエンコードに、使用可能な GPU は利用されません。 セッション ホストのグループ ポリシーを構成して、GPU アクセラレーションを使用するフレーム エンコードを有効にします。 上記の手順に続けて次のようにします。

>[!NOTE]
>GPU アクセラレーションを使用するフレーム エンコードは、NVv4 シリーズの VM では使用できません。

1. ポリシー **[リモート デスクトップ接続用に H.264/AVC ハードウェア エンコードを構成する]** を選択し、このポリシーを **[有効]** に設定して、リモート セッションで AVC/H.264 に対するハードウェア エンコードを有効にします。

    >[!NOTE]
    >Windows Server 2016 では、 **[AVC ハードウェア エンコードを優先]** オプションを **[常に試行]** に設定します。

2. グループ ポリシーを編集したので、強制的にグループ ポリシーを更新します。 コマンド プロンプトを開き、次のように入力します。

    ```cmd
    gpupdate.exe /force
    ```

3. リモート デスクトップ セッションからサインアウトします。

## <a name="configure-fullscreen-video-encoding"></a>全画面表示のビデオ エンコードを構成する

>[!NOTE]
>GPU が存在しない場合でも、全画面ビデオ エンコードを有効にできます。

3D モデリング、CAD/CAM、ビデオ アプリケーションなどの高フレーム レートのコンテンツを生成するアプリケーションを頻繁に使用する場合は、リモート セッションに対して全画面表示のビデオ エンコードを有効にすることを選択できます。 全画面表示のビデオ プロファイルでは、ネットワーク帯域幅と、セッション ホストとクライアント リソースの両方を犠牲にして、このようなアプリケーション用の高フレーム レートとより良いユーザー エクスペリエンスが提供されます。 全画面表示のビデオ エンコードには、GPU アクセラレーションを使用するフレーム エンコードの使用をお勧めします。 全画面表示のビデオ エンコードを有効にするセッション ホストのグループ ポリシーを構成します。 上記の手順に続けて次のようにします。

1. ポリシー **[リモート デスクトップ接続で H.264/AVC 444 グラフィック モードを優先する]** を選択し、このポリシーを **[有効]** に設定して、リモート セッションで H.264/AVC 444 コーデックを強制的に行います。
2. グループ ポリシーを編集したので、強制的にグループ ポリシーを更新します。 コマンド プロンプトを開き、次のように入力します。

    ```cmd
    gpupdate.exe /force
    ```

3. リモート デスクトップ セッションからサインアウトします。
## <a name="verify-gpu-accelerated-app-rendering"></a>GPU アクセラレーションを使用するアプリ レンダリングを検証する

アプリでレンダリングに GPU が使われていることを確認するには、次のいずれかを試します。

* NVIDIA GPU を搭載した Azure VM の場合は、「[ドライバーのインストールの確認](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)」の説明に従って `nvidia-smi` ユーティリティを使用して、アプリ実行時の GPU 使用率を確認します。
* サポートされているオペレーティング システムのバージョンでは、タスク マネージャーを使って GPU の利用を確認できます。 [パフォーマンス] タブで GPU を選択し、アプリで GPU が利用されているかどうかを確認します。

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU アクセラレーションを使用するフレーム エンコードを検証する

リモート デスクトップで GPU アクセラレーションを使用するエンコードが使われていることを検証するには:

1. Azure Virtual Desktop クライアントを使って VM のデスクトップに接続します。
2. イベント ビューアーを起動し、次のノードに移動します。 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[RemoteDesktopServices RdpCoreCDV]**  >  **[Operational]**
3. GPU アクセラレーションを使用するエンコードが使われているかどうかを確認するには、イベント ID 170 を探します。 [AVC ハードウェア エンコーダーが有効になりました: 1] と表示される場合は、GPU エンコードが使われています。

## <a name="verify-fullscreen-video-encoding"></a>全画面表示のビデオ エンコードを確認する

リモート デスクトップで全画面表示のビデオ エンコードが使用されていることを検証するには:

1. Azure Virtual Desktop クライアントを使って VM のデスクトップに接続します。
2. イベント ビューアーを起動し、次のノードに移動します。 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[RemoteDesktopServices RdpCoreCDV]**  >  **[Operational]**
3. 全画面表示のビデオ エンコードが使用されているかどうかを確認するには、イベント ID 162 を探します。 [AVC 対応: 1、初期プロファイル: 2048] と表示される場合は、AVC 444 が使われています。

## <a name="next-steps"></a>次のステップ

これらの手順により、1 つのセッション ホスト (1 つの VM) で GPU アクセラレーションが稼働状態になるはずです。 さらに大きなホスト プールで GPU アクセラレーションを有効にするには、いくつか追加の考慮事項があります。

* 多数の VM でのドライバーのインストールと更新を簡素化するには、[VM 拡張機能](../virtual-machines/extensions/overview.md)を使用することを検討してください。 NVIDIA GPU が搭載された VM には [NVIDIA GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-windows.md)を使用し、AMD GPU が搭載された VM には [AMD GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)を使用します。
* 多数の VM でのグループ ポリシーの構成を簡素化するには、Active Directory グループ ポリシーを使うことを検討します。 Active Directory ドメインでのグループ ポリシーのデプロイについては、「[Working with Group Policy Objects (グループ ポリシー オブジェクトの操作)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11))」をご覧ください。
