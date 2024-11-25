---
title: サブネットのトラフィック ルーティングを構成する - Azure Traffic Manager
description: この記事では、特定のサブネットからのトラフィックをルーティングするように Traffic Manager を構成する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: c87278d19a2e035de6abedc8cb5c46bea4e5ca13
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112081661"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Traffic Manager を使用してユーザーのサブネットに基づいて特定のエンドポイントにトラフィックを転送する

この記事では、サブネット トラフィックのルーティング方法を構成する方法について説明します。 **サブネット** トラフィックのルーティング方法を使用すると、一連の IP アドレス範囲を特定のエンドポイントにマップできます。Traffic Manager で要求が受信されると、要求の送信元 IP アドレスが検査されて、それに関連付けられているエンドポイントが返されます。

この記事で説明されているシナリオでは、サブネットのルーティングを使用し、ユーザーのクエリの IP アドレスに応じて、トラフィックを内部 Web サイトまたは運用 Web サイトにルーティングします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Traffic Manager の動作を確認するために、以下をデプロイする必要があります。
- 異なる Azure リージョン (**米国東部** (内部 Web サイトとして機能) と **西ヨーロッパ** (運用 Web サイトとして機能)) で実行している 2 つの基本的な Web サイト。
- Traffic Manager をテストするための 2 台の VM (1 台は **米国東部** 内、2 台目は **西ヨーロッパ** 内)。

テスト VM を使用して、ユーザー クエリの送信元サブネットに基づいて、Traffic Manager でユーザー トラフィックが内部 Web サイトまたは運用 Web サイトにルーティングされる方法を示します。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

### <a name="create-websites"></a>Web サイトを作成する

このセクションでは、2 つの Azure リージョン内に Traffic Manager プロファイル用の 2 つのサービス エンドポイントを提供する 2 つの Web サイト インスタンスを作成します。 2 つの Web サイトの作成には、次の手順が含まれます。
1. 基本的な Web サイトを実行する 2 台の VM を作成します (1 台は **米国東部** 内、他方は **西ヨーロッパ** 内)。
2. 各 VM に IIS サーバーをインストールし、ユーザーが この Web サイトにアクセスした場合に接続される VM の名前を記述する既定の Web サイト ページを更新します。

#### <a name="create-vms-for-running-websites"></a>Web サイトを実行するための VM を作成する
このセクションでは、2 台の VM (*myEndpointVMEastUS* と *myEndpointVMWEurope*) を、Azure リージョンの **米国東部** と **西ヨーロッパ** に作成します。

1. Azure Portal の左上隅にある **[リソースの作成]**  >  を選択し、 **[コンピューティング]**  >  **[Windows Server 2016 VM]** を選択します。
2. **[基本]** について次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。

    |設定|値|
    |---|---|
    |名前|myIISVMEastUS|
    |ユーザー名| 任意のユーザー名を入力します。|
    |Password| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。|
    |Resource group| **[新規]** を選択し、「*myResourceGroupTM1*」と入力します。|
    |場所| **[米国東部]** を選択します。|
    |||

4. **[サイズの選択]** で、VM サイズを選択します。
5. **[設定]** に次の値を選択し、**[OK]** を選択します。
    
    |設定|値|
    |---|---|
    |仮想ネットワーク| **[仮想ネットワーク]** を選択し、**[仮想ネットワークの作成]** で **[名前]** に「*myVNet1*」と入力し、サブネットとして「*mySubnet*」と入力します。|
    |ネットワーク セキュリティ グループ|**[基本]** を選択し、**[パブリック受信ポートを選択]** ドロップダウンから **[HTTP]** と **[RDP]** を選択します。 |
    |ブート診断|**[無効]** をクリックします。|
    |||

6. **[概要]** の **[作成]** で **[作成]** を選択して、VM のデプロイを開始します。

7. 次のように値を変更して、手順 1 から 6 を繰り返します。

    |設定|値|
    |---|---|
    |Resource group | **[新規]** を選択し、「*myResourceGroupTM2*」と入力します。|
    |場所|西ヨーロッパ|
    |VM 名 | myIISVMWEurope|
    |仮想ネットワーク | **[仮想ネットワーク]** を選択し、**[仮想ネットワークの作成]** で **[名前]** に「*myVNet2*」と入力し、サブネットとして「*mySubnet*」と入力します。|
    |||

8. VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

   ![VM の作成](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS をインストールして既定の Web ページをカスタマイズする

このセクションでは、2 台の VM (*myIISVMEastUS*  & *myIISVMWEurope*) に IIS サーバーをインストールした後、既定の Web サイト ページを更新します。 カスタマイズする Web サイト ページには、Web ブラウザーからその Web サイトにアクセスするときに接続する VM の名前が表示されます。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *myResourceGroupTM1* リソース グループにある *myIISVMEastUS* をクリックします。
2. **[概要]** ページで **[接続]** **[仮想マシンへの接続]** の順にクリックし、 **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
6. サーバーのデスクトップで、 **[Windows 管理ツール]** > **[サーバー マネージャー]** の順に移動します。
7. *myIISVMEastUS* で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. *myIISVMEastUS* との RDP 接続を閉じます。
9. *myResourceGroupTM2* リソース グループ内の VM (*myIISVMWEurope*) への RDP 接続を作成し、手順 1. から 6. を繰り返して、IIS のインストールと既定の Web ページのカスタマイズを実行します。
10. *myIISVMWEurope* で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS を実行する VM の DNS 名を構成する

Traffic Manager は、サービス エンドポイントの DNS 名に基づいてユーザー トラフィックをルーティングします。 このセクションでは、IIS サーバー *myIISVMEastUS* と *myIISVMWEurope* の DNS 名を構成します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *myResourceGroupTM1* リソース グループにある *myIISVMEastUS* を選択します。
2. **[概要]** ページで、 **[DNS 名]** の下の **[構成]** を選択します。
3. **[構成]** ページで、[DNS 名ラベル] の下に一意の名前を追加した後、 **[保存]** を選択します。
4. *myResourceGroupTM1* リソース グループにある *myIISVMWEurope* という名前の VM に対して、手順 1 から 3 を繰り返します。

### <a name="create-test-vms"></a>テスト VM を作成する

このセクションでは、VM (*mVMEastUS* と *myVMWestEurope*) を、該当する Azure リージョン (**米国東部** と **西ヨーロッパ**) 内に作成します。 これらの VM は、Web サイトが参照されるときに、Traffic Manager が最も近い IIS サーバーにトラフィックをルーティングすることをテストするために使用します。

1. Azure Portal の左上隅にある **[リソースの作成]**  >  を選択し、 **[コンピューティング]**  >  **[Windows Server 2016 VM]** を選択します。
2. **[基本]** について次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。

    |設定|値|
    |---|---|
    |名前|myVMEastUS|
    |ユーザー名| 任意のユーザー名を入力します。|
    |Password| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。|
    |Resource group| **[既存]** を選択し、*[myResourceGroupTM1]* を選択します。|
    |||

4. **[サイズの選択]** で、VM サイズを選択します。
5. **[設定]** に次の値を選択し、**[OK]** を選択します。

    |設定|値|
    |---|---|
    |仮想ネットワーク| **[仮想ネットワーク]** を選択し、**[仮想ネットワークの作成]** で **[名前]** に「*myVNet3*」と入力し、サブネットとして「*mySubnet3*」と入力します。|
    |ネットワーク セキュリティ グループ|**[基本]** を選択し、**[パブリック受信ポートを選択]** ドロップダウンから **[HTTP]** と **[RDP]** を選択します。 |
    |ブート診断|**[無効]** をクリックします。|
    |||

6. **[概要]** の **[作成]** で **[作成]** を選択して、VM のデプロイを開始します。

7. 次のように値を変更して、手順 1 から 5 を繰り返します。

    |設定|値|
    |---|---|
    |VM 名 | *myVMWEurope*|
    |Resource group | **[既存]** を選択し、「*myResourceGroupTM2*」と入力します。|
    |仮想ネットワーク | **[仮想ネットワーク]** を選択し、**[仮想ネットワークの作成]** で **[名前]** に「*myVNet4*」と入力し、サブネットとして「*mySubnet4*」と入力します。|
    |||

8. VM の作成には数分かかります。 両方の VM の作成が完了するまで、以降の手順に進まないでください。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成
要求の送信元 IP アドレスに基づいて特定のエンドポイントを返すことができる Traffic Manager プロファイルを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]**  >  **[作成]** の順に選択します。
2. **[Traffic Manager プロファイルの作成]** で、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[作成]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | 名前                   | この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される、DNS 名.trafficmanager.net になるためです。                                   |
    | ルーティング方法          | **[サブネット]** ルーティング方式を選択します。                                       |
    | サブスクリプション            | サブスクリプションを選択します。                          |
    | Resource group          | **[既存]** を選択し、「*myResourceGroupTM1*」と入力します。 |
    | |                              |
    |

    ![Traffic Manager プロファイルの作成](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

ユーザーのクエリのサブネットに基づいてユーザー トラフィックをルーティングするために、IIS サーバーを実行する 2 つの VM *myIISVMEastUS* & *myIISVMWEurope* を追加します。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を検索し、表示された結果からそのプロファイルを選択します。
2. **[Traffic Manager プロファイル]** の **[設定]** セクションで **[エンドポイント]** をクリックし、 **[追加]** をクリックします。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[OK]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | Type                    | Azure エンドポイント                                   |
    | 名前           | myTestWebSiteEndpoint                                        |
    | ターゲット リソースの種類           | パブリック IP アドレス                          |
    | ターゲット リソース          | **パブリック IP アドレスを選択** して、同じサブスクリプションでパブリック IP アドレスを持つリソースの一覧を表示します。 **[リソース]** で、*myIISVMEastUS-ip* という名前のパブリック IP アドレスを選択します。 これは、米国東部内の IIS サーバー VM のパブリック IP アドレスです。|
    |  サブネット ルーティングの設定    |   *myVMEastUS* テスト VM の IP アドレスを追加します。 この VM から送信されたすべてのユーザー クエリは、*myTestWebSiteEndpoint* に転送されます。    |

4. 手順 2. と 3. を繰り返して、*myIISVMWEurope* という名前の IIS サーバー VM に関連付けられたパブリック IP アドレス *myIISVMWEurope-ip* 用の *myProductionEndpoint* という名前の別のエンドポイントを追加します。 **[Subnet routing settings]\(サブネット ルーティングの設定\)** で、テスト VM *myVMWestEurope* の IP アドレスを追加します。 このテスト VM からのすべてのユーザー クエリは、エンドポイント *myProductionWebsiteEndpoint* にルーティングされます。
5. 両方のエンドポイントは、追加が完了すると、 **[Traffic Manager プロファイル]** に、監視ステータスが **[オンライン]** の状態で表示されます。

    ![Traffic Manager エンドポイントの追加](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager プロファイルのテスト
このセクションでは、Traffic Manager によって特定のサブネットからのユーザー トラフィックが特定のエンドポイントにどのようにルーティングされるかをテストします。 動作中の Traffic Manager を表示するには、次の手順を完了します。
1. Traffic Manager プロファイルの DNS 名を判別します。
2. 次のように、動作中の Traffic Manager を表示します。
    - **米国東部** リージョン内のテスト VM (*myVMEastUS*) から、Web ブラウザーで、Traffic Manager プロファイルの DNS 名を参照します。
    - **西ヨーロッパ** リージョン内のテスト VM (*myVMEastUS*) から、Web ブラウザーで、Traffic Manager プロファイルの DNS 名を参照します。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager プロファイルの DNS 名を判別する
このチュートリアルでは、わかりやすくするために、Traffic Manager プロファイルの DNS 名を使用して Web サイトにアクセスします。

次のように、Traffic Manager プロファイルの DNS 名を判別できます。

1. ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索します。 表示された結果で、Traffic Manager プロファイルをクリックします。
1. **[Overview]** をクリックします。
2. **[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 運用環境のデプロイでは、DNS CNAME レコードを使用して、Traffic Manager のドメイン名をポイントするバニティ ドメイン名を構成します。

   ![Traffic Manager の DNS 名](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認
このセクションでは、Traffic Manager が動作していることを確認します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で *[myResourceGroupTM1]* リソース グループにある *[myVMEastUS]* をクリックします。
2. **[概要]** ページで **[接続]** **[仮想マシンへの接続]** の順にクリックし、 **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。
1. VM *myVMEastUS* の Web ブラウザーで、Traffic Manager プロファイルの DNS 名を入力して、Web サイトを表示します。 VM *myVMEastUS* の IP アドレスはエンドポイント *myIISVMEastUS* と関連付けられているので、Web ブラウザーではテスト Web サイト サーバー *myIISVMEastUS* が起動されます。

   ![Traffic Manager プロファイルのテスト](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. 次に、手順 1 から 5 を使用して **西ヨーロッパ** にある VM *myVMWestEurope* に接続し、その VM から Traffic Manager プロファイルのドメイン名を参照します。 VM *myVMWestEurope* の IP アドレスはエンドポイント *myIISVMEastUS* と関連付けられているので、Web ブラウザーではテスト Web サイト サーバー *myIISVMWEurope* が起動されます。

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager プロファイルの削除
不要になったら、リソース グループ (**ResourceGroupTM1** と **ResourceGroupTM2**) を削除します。 これを行うには、リソース グループ (**ResourceGroupTM1** または **ResourceGroupTM2**) を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [重み付けによるトラフィック ルーティング方法](traffic-manager-configure-weighted-routing-method.md)について学習します。
- [優先順位によるルーティング方法](traffic-manager-configure-priority-routing-method.md)について学習します。
- [地理的なルーティング方法](traffic-manager-configure-geographic-routing-method.md)について学習します。
