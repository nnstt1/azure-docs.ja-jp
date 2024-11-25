---
title: VNet ピアリングを使用して仮想ネットワークを接続する - チュートリアル - Azure portal
description: このチュートリアルでは、Azure Portal を使って仮想ネットワーク ピアリングで仮想ネットワークを接続する方法を説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/06/2021
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ca8829a5f71dabfe33f013c354e083cae08e4566
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430765"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>チュートリアル: Azure Portal を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する

仮想ネットワーク ピアリングを使用して、仮想ネットワークを相互に接続できます。 これらの仮想ネットワークは、同じリージョン内にあっても異なるリージョン内にあってもかまいません (グローバル VNet ピアリングとも呼ばれます)。 仮想ネットワークをピアリングすると、それぞれの仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような待ち時間と帯域幅で相互に通信できます。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 2 つの仮想ネットワークを作成する
> * 仮想ネットワーク ピアリングを使用して 2 つの仮想ネットワークを接続する
> * 各仮想ネットワークに仮想マシン (VM) を展開する
> * VM 間の通信

好みに応じて、[Azure CLI](tutorial-connect-virtual-networks-cli.md) または [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) を使ってこのチュートリアルの手順を実行することもできます。

## <a name="prerequisites"></a>前提条件

開始する前に、アクティブなサブスクリプションを含む Azure アカウントが必要です。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ことができます。

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、 **[+ リソースの作成]** を選択します。

1. 「**仮想ネットワーク**」を検索してから、 **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vnet.png" alt-text="仮想ネットワーク用のリソースの作成のスクリーンショット。":::

1. **[基本]** タブで次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用します。

    |設定|値|
    |---|---|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group| **[新規作成]** を選択し、「*myResourceGroup* と入力します。|
    |リージョン| **[米国東部]** を選択します。|
    |名前|myVirtualNetwork1|

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-basic-tab.png" alt-text="[仮想ネットワークの作成] の [基本] タブのスクリーンショット":::

1. **[IP アドレス]** タブで、 **[アドレス空間]** フィールドに「*10.0.0.0/16*」と入力します。 下の **[サブネットの追加]** ボタンをクリックし、 **[サブネット名]** に「*Subnet1*」と入力し、 **[サブネット アドレス範囲]** に「*10.0.0.0/24*」と入力します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/ip-addresses-tab.png" alt-text="[仮想ネットワークの作成] の [IP アドレス] タブのスクリーンショット。":::

1. **[確認と作成]**、**[作成]** の順に選択します。
   
1. もう一度手順 1 から 5 を繰り返して、次の設定で 2 つ目の仮想ネットワークを作成します。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | myVirtualNetwork2 |
    | アドレス空間 | 10.1.0.0/16 |
    | Resource group | **[既存のものを使用]** 、 **[myResourceGroup]** の順に選択します。|
    | サブネット名 | Subnet2 |
    | サブネットのアドレス範囲 | 10.1.0.0/24 |

## <a name="peer-virtual-networks"></a>仮想ネットワークをピアリングする

1. Azure portal の上部にある検索ボックスで、「*myVirtualNetwork1*」を開始します。 検索結果に **[myVirtualNetwork1]** が表示されたら、それを選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vnet.png" alt-text="myVirtualNetwork1 を検索しているスクリーンショット。":::

1. 次の図に示すように、 **[設定]** で **[ピアリング]** を選択してから **[追加]** を選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-peering.png" alt-text="myVirtualNetwork1 のピアリングの作成のスクリーンショット。":::
    
1. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[追加]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | この仮想ネットワーク - ピアリング リンク名 | myVirtualNetwork1 からリモート仮想ネットワークへのピアリングの名前。  この接続には *myVirtualNetwork1-myVirtualNetwork2* が使用されます。|
    | リモート仮想ネットワーク - ピアリング リンク名 |  リモート仮想ネットワークから myVirtualNetwork1 へのピアリングの名前。 この接続には *myVirtualNetwork2-myVirtualNetwork1* が使用されます。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | 仮想ネットワーク  | 同じリージョンまたは異なるリージョンの仮想ネットワークを選択することができます。 ドロップダウンから *[myVirtualNetwork2] を選択します* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png" alt-text="仮想ネットワーク ピアリングの構成のスクリーンショット" lightbox="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional-expanded.png":::

    次の図に示すように、**[ピアリング状態]** は "*接続中*" です。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png" alt-text="仮想ネットワーク ピアリング接続状態のスクリーンショット":::

    *[接続済み]* 状態が 表示されない場合は、 **[更新]** ボタン を選択 します。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

後で仮想ネットワーク間の通信をテストできるように、各仮想ネットワークに VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

1. Azure portal で、 **[+ リソースの作成]** を選択します。

1. **[コンピューティング]** を選択してから、 *[仮想マシン]* の **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm.png" alt-text="仮想マシン用のリソースの作成のスクリーンショット。":::

1. **[基本]** タブで次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[作成]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | Resource group| **[既存のものを使用]** 、 **[myResourceGroup]** の順に選択します。 |
    | 名前 | myVm1 |
    | 場所 | **[米国東部]** を選択します。 |
    | Image | OS イメージを選択します。 この VM では、 *[Windows Server 2019 Datacenter - Gen1]* を選択します。 |
    | サイズ | VM サイズを選択してください。 この VM では *[Standard_D2s_v3]*  を選択します。 |
    | ユーザー名 | ユーザー名を入力します。 この例では、ユーザー 名 *azure* が選択されました。 |
    | Password | 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。 |
   
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab.png" alt-text="仮想マシンの [基本] タブの構成のスクリーンショット。" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab-expanded.png":::

1. **[ネットワーク]** タブで、以下の値を選択します。

    | 設定 | 値 |
    | --- | --- |
    | 仮想ネットワーク | myVirtualNetwork1 - まだ選択されていない場合は、**[仮想ネットワーク]** を選択し、**[myVirtualNetwork1]** を選択します。 |
    | Subnet | Subnet1 - まだ選択されていない場合は、**[サブネット]** を選択し、**[Subnet1]** を選択します。 |
    | パブリック受信ポート | *[選択したポートを許可する]* |
    | 受信ポートの選択 | *RDP (3389)* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab.png" alt-text="仮想マシンの [ネットワーク] タブの構成のスクリーンショット。" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab-expanded.png":::

1. **[確認および作成]** 、 **[作成]** の順に選択して、VM のデプロイを開始します。

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

もう一度手順 1 から 6 を繰り返し、次のように変更して 2 つ目の仮想ネットワークを作成します。

| 設定 | 値 |
| --- | --- |
| 名前 | myVm2 |
| 仮想ネットワーク | myVirtualNetwork2 |

VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="communicate-between-vms"></a>VM 間の通信

1. ポータルの上部にある検索ボックスで、「*myVm1*」と検索します。 検索結果に **myVm1** が表示されたら、それを選択します。
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vm.png" alt-text="myVm1 を検索しているスクリーンショット。":::

1. 仮想マシンに接続するには、 **[接続]** を選択し、ドロップダウンから **[RDP]** を選択します。 **[RDP ファイルのダウンロード]** を選択して、リモート デスクトップ ファイルをダウンロードします。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png" alt-text="[仮想マシンへの接続] ボタンのスクリーンショット。"::: 

1. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-connect.png" alt-text="リモートデスクトップへの接続画面のスクリーンショット。":::

1. VM の作成時に指定したユーザー名とパスワードを入力し (VM の作成時に入力した資格情報を指定するために、 **[その他]** 、 **[別のアカウントを使う]** の選択が必要になる場合があります)、 **[OK]** を選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-credentials.png" alt-text="RDP 資格情報画面のスクリーンショット。":::

1. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続を続行します。

1. 後の手順では、*myVm2* から *myVm1* への通信に ping を使用します。 ping は Internet Control Message Protocol (ICMP) を使用していますが、Windows ファイアウォール経由の既定では拒否されます。 *myVm1* VM で、Windows ファイアウォールを介して ICMP を有効にし、後で PowerShell を使って *myVm2* からこの VM に ping を実行できるようにします。

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    このチュートリアルでは VM 間の通信に ping を使用していますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

1. *myVm2* VM に接続するには、*myVm1* VM 上でコマンド プロンプトから以下のコマンドを入力します。

    ```
    mstsc /v:10.1.0.4
    ```
    
1. *myVm1* で ping を有効にしたため、そのマシンに対して IP アドレスで ping を実行できます。

    ```
    ping 10.0.0.4
    ```
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/myvm2-ping-myvm1.png" alt-text="MyVM2 から myVM1 を ping しているスクリーンショット。":::

1. *myVm1* と *myVm2* の両方への RDP セッションを切断します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。 

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、 **[削除]** を選択します。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/delete-resource-group.png" alt-text="リソース グループ削除ページのスクリーンショット":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [仮想ネットワーク ピアリングの詳細を確認する](virtual-network-peering-overview.md)
