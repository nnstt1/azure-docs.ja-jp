---
title: Azure 仮想ネットワーク ピアリングの作成、変更、削除 | Microsoft Docs
description: 仮想ネットワーク ピアリングを作成、変更、削除します。 仮想ネットワーク ピアリングを使用して、同じリージョン内およびリージョン間で仮想ネットワークを接続します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/01/2021
ms.author: altambaw
ms.openlocfilehash: 2a72cebc1857b32499ee0132e8535cf9571cb2c5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436653"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>仮想ネットワーク ピアリングの作成、変更、削除

仮想ネットワーク ピアリングを作成、変更、削除する方法について説明します。 仮想ネットワーク ピアリングにより、同じリージョン内と異なるリージョン間 (グローバル VNet ピアリングとも呼ばれます) で仮想ネットワークを Azure バックボーン ネットワークを介して接続できます。 ピアリング後も、仮想ネットワークは個別のリソースとして管理されます。 仮想ネットワーク ピアリングが初めての方は、「[virtual network peering overview](virtual-network-peering-overview.md)」、または[チュートリアル](tutorial-connect-virtual-networks-portal.md)を行うことで詳しく学習できます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、[Azure portal](https://portal.azure.com) を開き、ピアリングの作業に[必要なアクセス許可](#permissions)を持つアカウントでサインインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、Azure との接続を作成するには、ピアリングの作業に[必要なアクセス許可](#permissions)を持つアカウントで `Connect-AzAccount` を実行する必要もあります。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI のバージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、Azure との接続を作成するには、ピアリングの作業に[必要なアクセス許可](#permissions)を持つアカウントで `az login` を実行する必要もあります。

Azure へのログインまたは接続に使用するアカウントは、[ネットワーク共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧に記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てられている必要があります。

## <a name="create-a-peering"></a>ピアリングの作成

ピアリングを作成する前に、要件と制約、[必要なアクセス許可](#permissions)を十分に理解しておいてください。

1. Azure portal の上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。 **[仮想ネットワーク (クラシック)]** は選択しないでください。クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークからピアリングを作成することはできません。

    :::image type="content" source="./media/virtual-network-manage-peering/search-vnet.png" alt-text="仮想ネットワークを検索しているスクリーンショット。":::

1. ピアリングを作成する仮想ネットワークを一覧から選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/select-vnet.png" alt-text="仮想ネットワーク ページから VNetA を選択しているスクリーンショット。":::

1. *[設定]* の **[ピアリング]** を選択してから、 **[+ 追加]** を選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/vneta-peerings.png" alt-text="VNetA のピアリング ページのスクリーンショット。":::

1. <a name="add-peering"></a>次の設定の値を入力または選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/add-peering.png" alt-text="ピアリング構成ページのスクリーンショット。" lightbox="./media/virtual-network-manage-peering/add-peering-expanded.png":::

    | 設定 | Description |
    | -------- | ----------- |
    | ピアリング リンク名 (この仮想ネットワーク) | ピアリングの名前は、仮想ネットワーク内で一意である必要があります。 |
    | [Traffic to remote virtual network]\(リモート仮想ネットワークへのトラフィック\) | 既定の `VirtualNetwork` フローを通過する 2 つの仮想ネットワーク間の通信を有効にする場合は、 **[許可] (既定値)** を選択します。 仮想ネットワーク間の通信を有効にすると、各仮想ネットワークに接続されているリソースは、同じ仮想ネットワークに接続されている場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 2 つの仮想ネットワーク内のリソース間のすべての通信は、Azure プライベート ネットワーク経由で行われます。 この設定が **[許可]** になっている場合、ネットワーク セキュリティ グループの **VirtualNetwork** サービス タグには、仮想ネットワークと、ピアリングされた仮想ネットワークが含まれます。 ネットワーク セキュリティ グループのサービス タグについて詳しくは、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md#service-tags)に関する記事をご覧ください。 ピアリングされた仮想ネットワークにトラフィックが既定で流れないようにする場合は、 **[リモート仮想ネットワークへのトラフィックをすべてブロックする]** を選択します。 この設定は、2 つの仮想ネットワーク間にピアリングがあるが、それらの間の既定のトラフィック フローを時折無効にする必要がある場合に選択できます。 ピアリングを削除し、再作成するよりも、有効化/無効化する方が便利な場合があります。 この設定が無効のとき、ピアリングされた仮想ネットワーク間では既定でトラフィックが流れません。ただし、適切な IP アドレスまたはアプリケーション セキュリティ グループが含まれる[ネットワーク セキュリティ グループ](./network-security-groups-overview.md) ルール経由で明示的に許可されている場合、トラフィックが流れることがあります。 </br></br> **注:** ***[リモート仮想ネットワークへのトラフィックをすべてブロックする]** 設定を無効にしても、**VirtualNetwork** サービス タグの定義が変更されるだけです。この設定の説明に記載されているように、ピア接続でのトラフィック フローが完全に停止するわけでは *ありません*。* |
    | [Traffic forwarded from remote virtual network]\(リモート仮想ネットワークから転送されるトラフィック\) | 仮想ネットワーク内のネットワーク仮想アプライアンスによって "*転送*" されたトラフィック (仮想ネットワークから発生したものではないもの) を、ピアリングを通じてこの仮想ネットワークに流す場合には、 **[許可] (既定値)** を選択します。 たとえば、Spoke1、Spoke2、およびハブという 3 つの仮想ネットワークがあるとします。 各スポーク仮想ネットワークとハブ仮想ネットワークの間にはピアリングが存在しますが、スポーク仮想ネットワーク間にはピアリングが存在しません。 ハブ仮想ネットワークにはネットワーク仮想アプライアンスがデプロイされており、ユーザー定義ルートは、ネットワーク仮想アプライアンスを通じてサブネット間のトラフィックをルーティングする、各スポーク仮想ネットワークに適用されます。 各スポーク仮想ネットワークとハブ仮想ネットワーク間のピアリングに対してこれが設定されていない場合、仮想ネットワーク間のトラフィックがハブによって転送されないため、スポーク仮想ネットワーク間ではトラフィックは流れません。 この機能を有効にすると、ピアリングを介して転送されたトラフィックが許可されますが、ユーザー定義ルートやネットワーク仮想アプライアンスが作成されるわけではありません。 ユーザー定義ルートとネットワーク仮想アプライアンスは個別に作成されます。 ユーザー定義ルートについては、[こちら](virtual-networks-udr-overview.md#user-defined)をご覧ください。 Azure VPN Gateway を通じて仮想ネットワーク間でトラフィックが転送される場合は、この設定をオンにする必要はありません。 |
    | 仮想ネットワーク ゲートウェイまたは Route Server | 以下の場合には、 **[この仮想ネットワークのゲートウェイまたはルート サーバーを使用する]** を選択します。 </br> - この仮想ネットワークに仮想ネットワーク ゲートウェイがデプロイされており、ピアリングされた仮想ネットワークからのトラフィックがこのゲートウェイ経由で流れることを許可する場合。 たとえば、仮想ネットワーク ゲートウェイを介して、この仮想ネットワークをオンプレミス ネットワークに接続できます。 ゲートウェイは、ExpressRoute または VPN ゲートウェイを指定できます。 このボックスをオンにすると、ピアリングされた仮想ネットワークからのトラフィックを、この仮想ネットワークに接続されたゲートウェイ経由でオンプレミス ネットワークに流すことができます。 このボックスをオンにしても、ピアリングされた仮想ネットワークでゲートウェイを構成することはできません。 </br> - この仮想ネットワークにルート サーバーがデプロイされており、ピアリングされた仮想ネットワークがルート サーバーと通信してルートを交換する場合。 詳細については、[Azure Route Server](../route-server/overview.md) に関するページを参照してください。 </br></br> もう一方の仮想ネットワークからこの仮想ネットワークへのピアリングを設定する場合は、ピアリングされた仮想ネットワークで **[リモート仮想ネットワークのゲートウェイまたはルート サーバーを使用する]** を選択する必要があります。 この設定を **[なし] (既定値)** のままにしてもピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできず、ルート サーバーからルートを学習することもできません。 ピアリングが仮想ネットワーク (Resource Manager) と仮想ネットワーク (クラシック) の間で行われる場合、ゲートウェイは仮想ネットワーク (Resource Manager) 内にある必要があります。 |
    | リモート仮想ネットワーク ピアリング リンク名 | リモート仮想ネットワーク ピアの名前。 |
    | 仮想ネットワークのデプロイ モデル | ピアリングする仮想ネットワークのデプロイに使用されたデプロイ モデルを選択します。 |
    | リソース ID を知っている | ピアリングする仮想ネットワークへの読み取りアクセス権がある場合は、このチェック ボックスをオフのままにしておきます。 ピアリングする仮想ネットワークまたはサブスクリプションへの読み取りアクセス権がない場合は、このボックスをオンにします。 チェック ボックスをオンにしたときに表示される **[リソース ID]** ボックスに、ピアリングする仮想ネットワークの完全なリソース ID を入力します。 この仮想ネットワークと同じ Azure [リージョン](https://azure.microsoft.com/regions)、または[サポートされている異なる](#requirements-and-constraints) Azure リージョンに存在する、仮想ネットワークのリソース ID を入力する必要があります。 完全なリソース ID は `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` のようになります。 仮想ネットワークのリソース ID を取得するには、仮想ネットワークのプロパティを表示します。 仮想ネットワークのプロパティを表示する方法については、「[仮想ネットワークと設定の表示](manage-virtual-network.md#view-virtual-networks-and-settings)」を参照してください。 サブスクリプションが、ピアリングを作成している仮想ネットワークを含むサブスクリプションと異なる Azure Active Directory テナントに関連付けられている場合、まず各テナントのユーザーを[ゲスト ユーザー](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)として他方のテナントに追加します。 |
    | Resource ID | このフィールドは、ボックスをオンにした場合に表示されます。 この仮想ネットワークと同じ Azure [リージョン](https://azure.microsoft.com/regions)、または[サポートされている異なる](#requirements-and-constraints) Azure リージョンに存在する、仮想ネットワークのリソース ID を入力する必要があります。 完全なリソース ID は `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` のようになります。 仮想ネットワークのリソース ID を取得するには、仮想ネットワークのプロパティを表示します。 仮想ネットワークのプロパティを表示する方法については、「[仮想ネットワークと設定の表示](manage-virtual-network.md#view-virtual-networks-and-settings)」を参照してください。 サブスクリプションが、ピアリングを作成している仮想ネットワークを含むサブスクリプションと異なる Azure Active Directory テナントに関連付けられている場合、まず各テナントのユーザーを[ゲスト ユーザー](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)として他方のテナントに追加します。
    | サブスクリプション | ピアリングする仮想ネットワークの[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を選択します。 アカウントに読み取りアクセス権があるサブスクリプションの数に応じて、1 つ以上のサブスクリプションが表示されます。 **[リソース ID]** チェック ボックスをオンにした場合、この設定は使用できません。 |
    | 仮想ネットワーク | ピアリングする仮想ネットワークを選択します。 いずれかの Azure デプロイ モデルで作成された仮想ネットワークを選択できます。 異なるリージョンの仮想ネットワークを選ぶ場合は、[サポートされているリージョン](#cross-region)の仮想ネットワークを選ぶ必要があります。 仮想ネットワークを一覧に表示するには、その仮想ネットワークへの読み取りアクセス権が必要です。 仮想ネットワークが一覧に表示されていても、淡色表示されている場合、仮想ネットワークのアドレス空間がこの仮想ネットワークのアドレス空間と重複している可能性があります。 仮想ネットワークのアドレス空間が重複している場合、それらの仮想ネットワークをピアリングをすることはできません。 **[リソース ID]** チェック ボックスをオンにした場合、この設定は使用できません。 |
    | [Traffic to remote virtual network]\(リモート仮想ネットワークへのトラフィック\) | 既定の `VirtualNetwork` フローを通過する 2 つの仮想ネットワーク間の通信を有効にする場合は、 **[許可] (既定値)** を選択します。 仮想ネットワーク間の通信を有効にすると、各仮想ネットワークに接続されているリソースは、同じ仮想ネットワークに接続されている場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 2 つの仮想ネットワーク内のリソース間のすべての通信は、Azure プライベート ネットワーク経由で行われます。 この設定が **[許可]** になっている場合、ネットワーク セキュリティ グループの **VirtualNetwork** サービス タグには、仮想ネットワークと、ピアリングされた仮想ネットワークが含まれます。 (ネットワーク セキュリティ グループのサービス タグの詳細については、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md#service-tags)に関するページを参照してください。) ピアリングされた仮想ネットワークに既定でトラフィックを流さない場合は、 **[リモート仮想ネットワークへのトラフィックをすべてブロックする]** を選択します。 仮想ネットワークを別の仮想ネットワークとピアリングしていても、これらの 2 つの仮想ネットワーク間の既定のトラフィック フローを時折無効にする必要がある場合は、 **[リモート仮想ネットワークへのトラフィックをすべてブロックする]** を選択できます。 ピアリングを削除し、再作成するよりも、有効化/無効化する方が便利な場合があります。 この設定が無効のとき、ピアリングされた仮想ネットワーク間では既定でトラフィックが流れません。ただし、適切な IP アドレスまたはアプリケーション セキュリティ グループが含まれる[ネットワーク セキュリティ グループ](./network-security-groups-overview.md) ルール経由で明示的に許可されている場合、トラフィックが流れることがあります。 </br></br> **注:** ***[リモート仮想ネットワークへのトラフィックをすべてブロックする]** 設定を無効にしても、**VirtualNetwork** サービス タグの定義が変更されるだけです。この設定の説明に記載されているように、ピア接続でのトラフィック フローが完全に停止するわけでは *ありません*。* |
    | [Traffic forwarded from remote virtual network]\(リモート仮想ネットワークから転送されるトラフィック\) | 仮想ネットワーク内のネットワーク仮想アプライアンスによって "*転送*" されたトラフィック (仮想ネットワークから発生したものではないもの) を、ピアリングを通じてこの仮想ネットワークに流せるようにする場合には、 **[許可] (既定値)** のままにします。 たとえば、Spoke1、Spoke2、およびハブという 3 つの仮想ネットワークがあるとします。 各スポーク仮想ネットワークとハブ仮想ネットワークの間には仮想ネットワーク ピアリングが存在しますが、スポーク仮想ネットワーク間には仮想ネットワーク ピアリングが存在しません。 ハブ仮想ネットワークにはネットワーク仮想アプライアンスがデプロイされており、ユーザー定義ルートは、ネットワーク仮想アプライアンスを通じてサブネット間のトラフィックをルーティングする、各スポーク仮想ネットワークに適用されます。 各スポーク仮想ネットワークとハブ仮想ネットワーク間のピアリングに対してこの設定が選択されていない場合、仮想ネットワーク間のトラフィックがハブによって転送されないため、スポーク仮想ネットワーク間ではトラフィックは流れません。 この機能を有効にすると、ピアリングを介してトラフィック転送が許可されますが、ユーザー定義ルートやネットワーク仮想アプライアンスが作成されるわけではありません。 ユーザー定義ルートとネットワーク仮想アプライアンスは個別に作成されます。 ユーザー定義ルートについては、[こちら](virtual-networks-udr-overview.md#user-defined)をご覧ください。 Azure VPN Gateway を通じて仮想ネットワーク間でトラフィックが転送される場合は、この設定をオンにする必要はありません。 |
    | 仮想ネットワーク ゲートウェイまたは Route Server | 以下の場合には、 **[この仮想ネットワークのゲートウェイまたはルート サーバーを使用する]** を選択します。 </br>- この仮想ネットワークに仮想ネットワーク ゲートウェイが接続されており、ピアリングされた仮想ネットワークからのトラフィックがゲートウェイ経由で流れることを許可する場合。 たとえば、仮想ネットワーク ゲートウェイを介して、この仮想ネットワークをオンプレミス ネットワークに接続できます。 ゲートウェイは、ExpressRoute または VPN ゲートウェイを指定できます。 この設定を選択すると、ピアリングされた仮想ネットワークからのトラフィックを、この仮想ネットワークに接続されたゲートウェイ経由でオンプレミス ネットワークに流すことができます。 </br>- この仮想ネットワークにルート サーバーがデプロイされており、ピアリングされた仮想ネットワークがルート サーバーと通信してルートを交換する場合。 詳細については、[Azure Route Server](../route-server/overview.md) に関するページを参照してください。 </br></br> *[**この** 仮想ネットワークのゲートウェイまたはルート サーバーを使用する]* を選択した場合、ピアリングされた仮想ネットワークにゲートウェイを構成することはできません。 もう一方の仮想ネットワークからこの仮想ネットワークへのピアリングを設定する場合は、ピアリングされた仮想ネットワークで *[**リモート** 仮想ネットワークのゲートウェイまたはルート サーバーを使用する]* を選択する必要があります。 この設定を **[なし] (既定値)** のままにしても、ピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできません。 ピアリングが仮想ネットワーク (Resource Manager) と仮想ネットワーク (クラシック) の間で行われる場合、ゲートウェイは仮想ネットワーク (Resource Manager) 内にある必要があります。</br></br> VPN ゲートウェイでは、トラフィックをオンプレミス ネットワークに転送するだけでなく、自身が置かれている仮想ネットワークとピアリングされた仮想ネットワーク間のネットワーク トラフィックを、それらの仮想ネットワークを相互にピアリングすることなく転送できます。 VPN ゲートウェイを使用したトラフィックの転送は、ハブ内の VPN ゲートウェイを使用して、相互にピアリングされていないスポーク仮想ネットワーク間のトラフィックをルーティングする場合に便利です ( **[転送されたトラフィックを許可する]** について説明したハブとスポークの例をご覧ください)。 転送へのゲートウェイの使用許可について詳しくは、「[仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。 このシナリオを実行するには、次ホップの種類として仮想ネットワーク ゲートウェイを指定したユーザー定義ルートを実装する必要があります。 ユーザー定義ルートについては、[こちら](virtual-networks-udr-overview.md#user-defined)をご覧ください。 ユーザー定義ルートでネクスト ホップの種類として指定できるのは、VPN ゲートウェイだけです。ユーザー定義ルートで、ネクスト ホップの種類として ExpressRoute ゲートウェイを指定することはできません。 </br></br> 以下の場合には、 **[リモート仮想ネットワークのゲートウェイまたはルート サーバーを使用する]** を選択します。 </br>- この仮想ネットワークからのトラフィックが、ピアリングされた仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることを許可する場合。 たとえば、ピアリングされた仮想ネットワークに、オンプレミス ネットワークとの通信を可能にする VPN Gateway が接続されているとします。 この設定を選択すると、この仮想ネットワークからのトラフィックを、ピアリングされた仮想ネットワークに接続された VPN ゲートウェイ経由で流すことができます。 </br>- この仮想ネットワークで、リモートのルート サーバーを使用してルートを交換する場合。 詳細については、[Azure Route Server](../route-server/overview.md) に関するページを参照してください。 </br></br> この設定を選択するには、ピアリングされた仮想ネットワークに仮想ネットワーク ゲートウェイが接続されている必要があります。また、 **[この仮想ネットワークのゲートウェイまたはルート サーバーを使用する]** オプションが選択されている必要があります。 この設定を **[なし] (既定値)** のままにしても、ピアリングされた仮想ネットワークからのトラフィックはこの仮想ネットワークに流れることができますが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で流れることはできません。 この設定は、この仮想ネットワークの 1 つのピアリングに対してのみ有効にすることができます。 </br></br> **注:** *仮想ネットワークにゲートウェイが既に構成されている場合は、リモート ゲートウェイを使用することはできません。転送にゲートウェイを使用する方法の詳細については、[仮想ネットワーク ピアリングで転送するために VPN ゲートウェイを構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことに関するページを参照してください。* |
    
    
    > [!NOTE]
    > Virtual Network ゲートウェイを使用してオンプレミスのトラフィックをピアリングされた VNet に推移的に送信する場合は、オンプレミスの VPN デバイスのピアリングされた VNet IP の範囲を "対象" トラフィックに設定する必要があります。 そうしないと、オンプレミスのリソースがピアリングされた VNet 内のリソースと通信できなくなります。

1. **[追加]** を選んで、選択した仮想ネットワークへのピアリングを構成します。 数秒後、 **[更新]** ボタンを選択すると、ピアリングの状態が *[更新中]* から *[接続済み]* に変わります。

    :::image type="content" source="./media/virtual-network-manage-peering/vnet-peering-connected.png" alt-text="ピアリング ページの仮想ネットワーク ピアリング状態のスクリーンショット。":::

異なるサブスクリプションおよびデプロイ モデルの仮想ネットワーク間にピアリングを実装する手順については、[次の手順](#next-steps)をご覧ください。

### <a name="commands"></a>コマンド

- **Azure CLI**: [az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**:[Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>ピアリング設定の表示または変更

ピアリングを変更する前に、要件と制約、[必要なアクセス許可](#permissions)を十分に理解しておいてください。

1. 仮想ネットワーク ピアリング設定を表示または変更する仮想ネットワークを選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/vnet-list.png" alt-text="サブスクリプション内の仮想ネットワークの一覧のスクリーンショット。":::

1. *[設定]* で **[ピアリング]** を選択してから、設定を表示または変更するピアリングを選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/select-peering.png" alt-text="仮想ネットワークから設定を変更するピアリングの選択のスクリーンショット。":::

1. 該当する設定を変更します。 各設定のオプションについては、「ピアリングの作成」の[手順 4](#add-peering) を参照してください。 その後、 **[保存]** を選択して構成の変更を完了します。

    :::image type="content" source="./media/virtual-network-manage-peering/change-peering-settings.png" alt-text="仮想ネットワーク ピアリング設定の変更のスクリーンショット。":::

**コマンド**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering) (仮想ネットワークのピアリングを一覧表示する)、[az network vnet peering show](/cli/azure/network/vnet/peering) (特定のピアリングの設定を表示する)、[az network vnet peering update](/cli/azure/network/vnet/peering) (ピアリング設定を変更する)|
- **PowerShell**:[Get-AzureRmVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) (ピアリング設定を取得する)、[Set-AzureRmVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) (設定を変更する)

## <a name="delete-a-peering"></a>ピアリングの削除

ピアリングを削除する前に、[必要なアクセス許可](#permissions)がアカウントにあることを確認してください。

ピアリングが削除されると、2 つの仮想ネットワーク間でトラフィックが流れなくなります。 仮想ネットワーク ピアリングを削除すると、対応するピアリングも削除されます。 仮想ネットワーク間で通信することはあっても、常に通信するわけではない場合は、ピアリングを削除するのではなく、 **[リモート仮想ネットワークへのトラフィック]** 設定を、 **[リモート仮想ネットワークへのトラフィックをすべてブロックする]** に設定します。 ピアリングを削除し、再作成するよりも、ネットワーク アクセスを無効化/有効化する方が簡単な場合があります。

1. ピアリングを削除する仮想ネットワークを一覧から選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/vnet-list.png" alt-text="サブスクリプションで仮想ネットワークを選択しているスクリーンショット。":::

1. *[設定]* で **[ピアリング]** を選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/select-peering.png" alt-text="仮想ネットワークから削除するピアリングの選択のスクリーンショット。":::

1. 削除するピアリングの右側にある **[...]** を選択し、 **[削除]** を選択します。

    :::image type="content" source="./media/virtual-network-manage-peering/delete-peering.png" alt-text="仮想ネットワークからピアリングを削除するスクリーンショット。":::

1.  **[はい]** を選択して、ピアリングと対応するピアを削除することを確認します。

    :::image type="content" source="./media/virtual-network-manage-peering/confirm-deletion.png" alt-text="ピアリング削除の確認のスクリーンショット。":::

1. 上記の手順を繰り返して、ピアリングのもう一方の仮想ネットワークからピアリングを削除します。

**コマンド**

- **Azure CLI**: [az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**:[Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>要件と制約

- <a name="cross-region"></a>同じリージョンまたは異なるリージョンの仮想ネットワークをピアリングできます。 異なるリージョン内の仮想ネットワークのピアリングは "*グローバル VNet ピアリング*" とも呼ばれます。 
- グローバル ピアリングの作成では、ピアリングされた仮想ネットワークは Azure パブリック クラウドの任意のリージョン、または China クラウド リージョン、あるいは Government クラウド リージョンに存在できます。 クラウド間ではピアリングできません。 たとえば、Azure パブリック クラウドの VNet は Azure 中国クラウドの VNet にピアリングすることはできません。
- 仮想ネットワーク内のリソースは、グローバルにピアリングされた仮想ネットワークの Basic 内部ロード バランサーのフロントエンド IP アドレスと通信することはできません。 Basic Load Balancer のサポートは、同じリージョン内でのみ存在します。 Standard Load Balancer のサポートは VNet Peering と Global VNet Peering の両方に存在します。 Basic ロード バランサーを使用するサービスは、グローバル VNet ピアリング経由では動作しません。このようなサービスについては、[こちら](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)を参照してください。
- グローバルにピアリングされた仮想ネットワークおよびローカルにピアリングされた仮想ネットワークでは、リモート ゲートウェイを使用でき、ゲートウェイ転送を許可できます。
- 仮想ネットワークが属しているサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションに属する仮想ネットワークをピアリングする場合、両方のサブスクリプションを同じまたは異なる Azure Active Directory テナントに関連付けることができます。 AD テナントをまだ持っていない場合は、[作成](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant)できます。
- ピアリングする仮想ネットワークの IP アドレス空間が重複していてはいけません。
- 仮想ネットワークを別の仮想ネットワークとピアリングした後に、仮想ネットワークのアドレス空間に対してアドレス範囲の追加または削除を実行することはできません。 アドレス範囲を追加または削除するには、ピアリングを削除し、アドレス範囲を追加または削除してからピアリングを再作成します。 仮想ネットワークに対してアドレス範囲を追加または削除するには、[仮想ネットワークの管理](manage-virtual-network.md)に関するページを参照してください。
- Resource Manager を使用してデプロイされた 2 つの仮想ネットワーク、または Resource Manager を使用してデプロイされた仮想ネットワークとクラシック デプロイ モデルを使用してデプロイされた仮想ネットワークをピアリングできます。 クラシック デプロイ モデルを使用して作成された 2 つの仮想ネットワークをピアリングすることはできません。 Azure デプロイ モデルの知識がない場合は、[Azure デプロイ モデルの概要](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 クラシック デプロイ モデルを使って作成された 2 つの仮想ネットワークは、[VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) を使用して接続できます。
- Resource Manager を使用して作成された 2 つの仮想ネットワークをピアリングするときは、ピアリングする仮想ネットワークごとにピアリングを構成する必要があります。 ピアリングの状態の種類として次のいずれかが表示されます。 
  - *開始済み:* 1 つ目の仮想ネットワークから 2 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "*開始済み*" になります。 
  - *接続済み:* 2 つ目の仮想ネットワークから 1 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "*接続済み*" になります。 1 つ目の仮想ネットワークのピアリングの状態を確認すると、状態が "*開始済み*" から "*接続済み*" に変わっていることがわかります。 両方の仮想ネットワーク ピアリングの状態が "*接続済み*" になるまで、ピアリングは正常に確立されません。
- Resource Manager を使用して作成された仮想ネットワークを、クラシック デプロイ モデルを使用して作成された仮想ネットワークとピアリングするときは、Resource Manager を使用してデプロイされた仮想ネットワークのピアリングだけを構成します。 仮想ネットワーク (クラシック) のピアリングや、クラシック デプロイ モデルを使用してデプロイされた 2 つの仮想ネットワークのピアリングを構成することはできません。 仮想ネットワーク (Resource Manager) から仮想ネットワーク (クラシック) へのピアリングを作成すると、ピアリングの状態が "*更新中*" になった後、すぐに "*接続済み*" に変わります。
- ピアリングは 2 つの仮想ネットワーク間で確立されます。 ピアリング単独では推移的ではありません。 次の仮想ネットワーク間のピアリングを作成したとします。
  - VirtualNetwork1 と VirtualNetwork2   - VirtualNetwork1 と VirtualNetwork2
  - VirtualNetwork2 と VirtualNetwork3   - VirtualNetwork2 と VirtualNetwork3


  この場合、VirtualNetwork2 を介して、VirtualNetwork1 と VirtualNetwork3 のピアリングが確立されるわけではありません。 VirtualNetwork1 と VirtualNetwork3 の間に仮想ネットワーク ピアリングを作成する場合は、VirtualNetwork1 と VirtualNetwork3 の間にピアリングを作成する必要があります。 この場合、VirtualNetwork2 を介して、VirtualNetwork1 と VirtualNetwork3 のピアリングが確立されるわけではありません。 VirtualNetwork1 と VirtualNetwork3 が直接通信するようにしたい場合は、VirtualNetwork1 と VirtualNetwork3 の間に明示的なピアリングを作成するか、またはハブ ネットワークで NVA を実行する必要があります。  
- 既定の Azure 名前解決を使用して、ピアリングされた仮想ネットワークで名前を解決することはできません。 他の仮想ネットワークで名前を解決するには、[プライベート ドメインの Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) またはカスタム DNS サーバーを使う必要があります。 独自の DNS サーバーを設定する方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。
- 同じリージョン内のピアリングされた仮想ネットワークのリソースは、同じ仮想ネットワークに存在する場合と同様に、同じ帯域幅と待機時間で相互に通信できます。 ただし、仮想マシンのサイズごとに独自の最大ネットワーク帯域幅が設定されています。 さまざまなサイズの仮想マシンの最大ネットワーク帯域幅の詳細については、[Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM サイズに関する記事を参照してください。
- 仮想ネットワークは、別の仮想ネットワークとピアリングすることができ、Azure 仮想ネットワーク ゲートウェイを使用して別の仮想ネットワークに接続することもできます。 ピアリングとゲートウェイの両方を使用して仮想ネットワークが接続されている場合、仮想ネットワーク間のトラフィックは、ゲートウェイではなく、ピアリング構成を介して流れます。
- 新しいルートをクライアントに確実にダウンロードするには、仮想ネットワーク ピアリングが正常に構成された後で、もう一度ポイント対サイト VPN クライアントをダウンロードする必要があります。
- 仮想ネットワーク ピアリングを利用するイグレス トラフィックとエグレス トラフィックには少額の料金が発生します。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-network)を参照してください。

## <a name="permissions"></a>アクセス許可

仮想ネットワークのピアリングを扱うために使用するアカウントは、次のロールに割り当てる必要があります。

- [ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor):Resource Manager を通じてデプロイされる仮想ネットワークの場合。
- [従来のネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor):従来のデプロイ モデルを通じてデプロイされる仮想ネットワークの場合。

上記のどのロールにもアカウントが割り当てられていない場合、次の表から必要なアクションが割り当てられる[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる必要があります。

| アクション                                                          | 名前 |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | 仮想ネットワーク A から仮想ネットワーク B へのピアリングを作成するために必要です。仮想ネットワーク A は仮想ネットワーク (Resource Manager) である必要があります          |
| Microsoft.Network/virtualNetworks/peer/action                   | 仮想ネットワーク B (Resource Manager) から仮想ネットワーク A へのピアリングを作成するために必要です                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | 仮想ネットワーク B (クラシック) から仮想ネットワーク A へのピアリングを作成するために必要です                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | 仮想ネットワーク ピアリングの読み取り   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 仮想ネットワーク ピアリングの削除 |

## <a name="next-steps"></a>次のステップ

- 仮想ネットワーク ピアリングは、同じまたは異なるサブスクリプションに存在する同じまたは異なるデプロイメント モデルを使って作成された仮想ネットワーク間に作成されます。 次のいずれかのシナリオのチュートリアルを完了します。

  |Azure デプロイメント モデル             | サブスクリプション  |
  |---------                          |---------|
  |両方が Resource Manager              |[同じ](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[異なる](create-peering-different-subscriptions.md)|
  |一方が Resource Manager、もう一方がクラシック  |[同じ](create-peering-different-deployment-models.md)|
  |                                   |[異なる](create-peering-different-deployment-models-subscriptions.md)|

- [ハブおよびスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成する方法を学習します
- [PowerShell](powershell-samples.md) または [Azure CLI](cli-samples.md) のサンプル スクリプトを使って、または Azure [Resource Manager テンプレート](template-samples.md)を使って、仮想ネットワーク ピアリングを作成します
- 仮想ネットワーク用に [Azure Policy 定義](./policy-reference.md)を作成して割り当てる