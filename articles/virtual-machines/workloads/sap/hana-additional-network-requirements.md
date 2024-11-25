---
title: SAP HANA on Azure (Large Instances) 向けのその他のネットワーク要件 | Microsoft Docs
description: 必要になる可能性がある、SAP HANA on Azure Large Instances の追加のネットワーク要件について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/3/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a91f2116eb7eb2e6d7908e5c8c4e5171fff8c294
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361074"
---
# <a name="other-network-requirements-for-large-instances"></a>Large Instances 向けのその他のネットワーク要件

この記事では、SAP HANA on Azure Large Instances をデプロイするときに必要になる可能性のある他のネットワーク要件について説明します。

## <a name="prerequisites"></a>前提条件

この記事は、次の手順を完了していることを前提としています。
- [HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)
- [HANA Large Instances に仮想ネットワークを接続する](hana-connect-vnet-express-route.md)

## <a name="add-more-ip-addresses-or-subnets"></a>IP アドレスまたはサブネットの追加

IP アドレスまたはサブネットを追加することが必要な場合があります。 IP アドレスまたはサブネットを追加するには、Azure portal、PowerShell、Azure CLI のいずれかを使用します。

新しい IP アドレス範囲を新しい範囲として仮想ネットワーク アドレス空間に追加します。 集約した範囲を新たに作成しないでください。 この変更を Microsoft に送信します。 これにより、ご利用のクライアントでその新しい IP アドレス範囲から、HANA Large Instances に接続できます。 追加する新しい仮想ネットワーク アドレス空間を取得するために、Azure サポート要求を開くことができます。 確認を受け取ったら、「[HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)」で説明されている手順を実行します。 

Azure portal から別のサブネットを作成するには、[Azure portal を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)に関する記事を参照してください。 PowerShell からサブネットを作成するには、[PowerShell を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)に関する記事をご覧ください。

## <a name="add-virtual-networks"></a>仮想ネットワークの追加

最初に 1 つ以上の Azure 仮想ネットワークを接続した後、SAP HANA on Azure Large Instances にアクセスする追加の仮想ネットワークを接続できます。 まず、Azure サポート要求を送信します。 その要求に、特定の Azure デプロイを識別する特定の情報を含めます。 さらに、IP アドレス空間範囲または Azure 仮想ネットワーク アドレス空間範囲も含めます。 その後、SAP HANA on Microsoft サービス管理から、追加した仮想ネットワークおよび Azure ExpressRoute を接続するために必要な情報が提供されます。 HANA Large Instances への ExpressRoute 回線に接続するには、仮想ネットワークごとに一意の承認キーが必要です。

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅の増加

SAP HANA on Microsoft サービス管理にお問い合わせください。 SAP HANA on Azure Large Instances の ExpressRoute 回線の帯域幅を増やすように勧められた場合は、Azure サポート リクエストを作成します (1 つの回線につき、帯域幅を最大 10 Gbps 増やすよう要求することができます)。作業が完了すると通知が届きます。その他の作業は不要で、これで Azure が高速化されます。

## <a name="add-another-expressroute-circuit"></a>別の ExpressRoute 回線の追加

SAP HANA on Microsoft サービス管理にお問い合わせください。 別の ExpressRoute 回線を追加するように勧められた場合は、Azure サポート リクエスト (新しい回線に接続するための承認情報を取得する要求を含む) を作成します。 要求を行う前に、仮想ネットワークで使用するアドレス空間を定義する必要があります。 それにより、SAP HANA on Microsoft サービス管理から承認を提供できます。

新しい回線が作成され、SAP HANA on Microsoft サービス管理の構成が完了すると、作業の続行に必要な情報が記載された通知が届きます。 同じ Azure リージョン内の、SAP HANA on Azure Large Instances の別の ExpressRoute 回線に既に接続されている Azure 仮想ネットワークは、この追加の回線には接続できません。

## <a name="delete-a-subnet"></a>サブネットの削除

仮想ネットワーク サブネットを削除するには、Azure portal、PowerShell、または Azure CLI を使用できます。 Azure 仮想ネットワークの IP アドレス範囲またはアドレス空間の範囲が集約されている場合、Microsoft でアクションを実行する必要はありません (削除したサブネットを含む BGP ルート アドレス空間は依然として仮想ネットワークから伝播されています)。 

Azure 仮想ネットワークのアドレス範囲またはアドレス空間を複数の IP アドレス範囲として定義した可能性があります。 これらの範囲のいずれかが、削除したサブネットに割り当てられていることがあります。 それを仮想ネットワーク アドレス空間から削除してください。 次に、SAP HANA on Microsoft サービス管理に、SAP HANA on Azure Large Instances が通信を許可されている範囲からそれを削除するように通知します。

詳細については、「[サブネットの削除](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet)」を参照してください。

## <a name="delete-a-virtual-network"></a>仮想ネットワークの削除

詳細については、「[仮想ネットワークの削除](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network)」を参照してください。

SAP HANA on Microsoft サービス管理により、SAP HANA on Azure Large Instances の ExpressRoute 回線に対する既存の承認が削除されます。 また、HANA Large Instances との通信用の Azure 仮想ネットワークの IP アドレス範囲またはアドレス空間も削除されます。

仮想ネットワークの削除後、Azure サポート要求を開き、削除する IP アドレス空間範囲を指定します。

必ずすべてのものを削除してください。 次を削除します。
- ExpressRoute 接続
- 仮想ネットワーク ゲートウェイ
- 仮想ネットワーク ゲートウェイのパブリック IP
- 仮想ネットワーク

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute 回線の削除

SAP HANA on Azure Large Instances の追加の ExpressRoute 回線を削除するには、SAP HANA on Microsoft サービス管理で Azure サポート リクエストを開きます。 回線の削除を要求します。 Azure サブスクリプション内では、必要に応じて仮想ネットワークを削除することも保持することもできます。 ただし、HANA Large Instances の ExpressRoute 回線と、リンク先の仮想ネットワーク ゲートウェイの間の接続は削除する必要があります。

## <a name="next-steps"></a>次のステップ

SAP HANA on Azure Large Instances をインストールして構成する方法を確認します。

> [!div class="nextstepaction"]
> [SAP HANA Large Instances のインストールと構成](hana-installation.md)
