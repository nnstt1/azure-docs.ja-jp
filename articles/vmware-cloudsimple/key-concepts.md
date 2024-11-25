---
title: Azure VMware Solution by CloudSimple を管理するための重要な概念
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple を管理するための重要な概念について説明します
author: suzizuber
ms.author: v-szuber
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e48df490137357a376f0d546b7d3c16165205d09
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620110"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Azure VMware Solution by CloudSimple を管理するための重要な概念

Azure VMware Solution by CloudSimple を管理するには、次の概念を理解する必要があります。

* CloudSimple サービス、[Azure VMware Solution by CloudSimple - サービス] と表示されます
* CloudSimple ノード、[Azure VMware Solution by CloudSimple - ノード] と表示されます
* CloudSimple プライベート クラウド
* サービスのネットワーク
* CloudSimple 仮想マシン、[Azure VMware Solution by CloudSimple - 仮想マシン] と表示されます

## <a name="cloudsimple-service"></a>CloudSimple サービス

CloudSimple サービスでは、CloudSimple によって VMware ソリューションに関連付けられたすべてのリソースの作成と管理を Azure portal から実行できます。 このサービスを使用する予定のすべてのリージョン内にサービス リソースを作成してください。

[CloudSimple サービス](cloudsimple-service.md)の詳細を確認してください。

## <a name="cloudsimple-node"></a>CloudSimple ノード

CloudSimple ノードは、その中に VMware ESXi ハイパーバイザーがデプロイされる専用のベア メタルのハイパーコンバージド コンピューティングおよびストレージ ホストです。 このノードが VMware vSphere、vCenter、vSAN、および NSX プラットフォームに組み込まれます。 CloudSimple ネットワーク サービスとエッジ ネットワーク サービスも有効化されます。 各ノードは、[CloudSimple プライベート クラウド](cloudsimple-private-cloud.md)を作成するためにプロビジョニングできる、コンピューティングおよびストレージの容量の単位として機能します。 お客様は、CloudSimple サービスを利用できるリージョン内のノードをプロビジョニングまたは予約します。

[CloudSimple ノード](cloudsimple-node.md)の詳細を確認してください。

## <a name="cloudsimple-private-cloud"></a>CloudSimple プライベート クラウド

CloudSimple プライベート クラウドは、独自の管理ドメイン内の vCenter サーバーによって管理される、分離された VMware スタック環境です。 VMware スタックには、ESXi ホスト、vSphere、vCenter、vSAN、および NSX が含まれます。 スタックは専用ノード (専用の分離されたベア メタル ハードウェア) 上で実行され、vCenter や NSX Manager などのネイティブの VMware ツールを通してユーザーによって使用されます。 専用ノードが Azure の場所にデプロイされ、Azure によって管理されます。 VLAN およびサブネットやファイアウォール テーブルなどのネットワーク サービスを使用して、各プライベート クラウドのセグメント化とセキュリティ保護を実行できます。 オンプレミス環境と Azure ネットワークへの接続は、セキュリティ保護されたプライベート VPN と Azure ExpressRoute 接続を使用して作成されます。

[CloudSimple プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認してください。

## <a name="service-networking"></a>サービスのネットワーク

CloudSimple サービスでは、CloudSimple サービスがデプロイされているリージョンごとにネットワークが提供されます。 ネットワークは、ルーティングが既定で有効になっている単一の TCP レイヤー 3 アドレス空間です。 このリージョン内に作成されるすべてのプライベート クラウドとサブネットでは、追加構成なしで相互通信できます。 VLAN を使って vCenter 上に分散ポート グループを作成します。 次のネットワーク機能を使用して、プライベート クラウド上でワークロード リソースを構成し、セキュリティ保護できます。

* [VLAN とサブネット](cloudsimple-vlans-subnets.md)
* [ファイアウォール テーブル](cloudsimple-firewall-tables.md)
* [VPN ゲートウェイ](cloudsimple-vpn-gateways.md)
* [パブリック IP](cloudsimple-public-ip-address.md)
* [Azure ネットワーク接続](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple 仮想マシン

CloudSimple サービスでは、Azure portal から VMware 仮想マシンを管理できます。 vSphere 環境の 1 つまたは複数のクラスターまたはリソース プールを、サービスが作成されているサブスクリプションにマップできます。

各項目の詳細情報

* [CloudSimple 仮想マシン](cloudsimple-virtual-machines.md)
* [Azure サブスクリプションのマッピング](./azure-subscription-mapping.md)
