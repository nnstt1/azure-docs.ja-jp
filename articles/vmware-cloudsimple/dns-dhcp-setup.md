---
title: Azure VMware Solution by CloudSimple - プライベート クラウド用のワークロード DNS と DHCP の設定
description: CloudSimple プライベート クラウド環境で実行されるアプリケーションとワークロードのために DNS および DHCP を設定する方法について説明します
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dec9ea878b0403cec1c19f039a945a8145a64bd9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322362"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドでの DNS および DHCP アプリケーションとワークロードの設定

プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。  これらのサービスを提供する、適切な DHCP と DNS インフラストラクチャが必要です。  仮想マシンを構成して、プライベート クラウド環境でこれらのサービスを提供することができます。  

## <a name="prerequisites"></a>前提条件

* VLAN が構成された分散ポート グループ
* オンプレミスまたはインターネット ベースの DNS サーバーへのルート設定
* 仮想マシンを作成するための仮想マシン テンプレートまたは ISO

## <a name="linux-based-dns-server-setup"></a>Linux ベースの DNS サーバーの設定

Linux では、DNS サーバーを設定するためのさまざまなパッケージが提供されています。  ここでは、[DigitalOcean による設定例](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)と、オープンソースの BIND DNS サーバーを設定する手順を示します。

## <a name="windows-based-setup"></a>Windows ベースのセットアップ

これらの Microsoft のトピックでは、Windows サーバーを DNS サーバーおよび DHCP サーバーとして設定する方法について説明しています。

* [DNS サーバーとしての Windows Server](/windows-server/networking/dns/dns-top)
* [DHCP サーバーとしての Windows Server](/windows-server/networking/technologies/dhcp/dhcp-top)
