---
title: 仮想ネットワーク用の Azure CLI サンプル
description: 多層アプリケーション用の仮想ネットワークの作成など、Azure CLI のタスクを完了するために使用できるさまざまなサンプル スクリプトについて説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 97565e30171a529dd4d8a30916333407f5bd185c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435706"
---
# <a name="azure-cli-samples-for-virtual-network"></a>仮想ネットワーク用の Azure CLI サンプル

次の表には、Azure CLI コマンドを使用する Bash スクリプトへのリンクが含まれています。

| スクリプト | 説明 |
|----|----|
| [多層アプリケーション用の仮想ネットワークの作成](./scripts/virtual-network-cli-sample-multi-tier-application.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP と SSH に制限され、バックエンド サブネットへのトラフィックは MySQL、ポート 3306 に制限されます。 |
| [2 つの仮想ネットワークのピアリング](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | 同じリージョンに 2 つの仮想ネットワークを作成し、接続します。 |
| [ネットワーク仮想アプライアンス経由のトラフィックのルーティング](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークと、2 つのサブネット間でトラフィックをルーティングできる VM を作成します。 |
| [VM ネットワークの着信トラフィックおよび送信トラフィックのフィルター処理](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへの着信ネットワーク トラフィックは、HTTP、HTTPS、SSH に制限されます。 バックエンド サブネットからインターネットへの送信トラフィックは許可されません。 |
|[Basic Load Balancer を使用した IPv4 と IPv6 のデュアル スタック仮想ネットワークの構成](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|2 つの VM と IPv4 および IPv6 のパブリック IP アドレスを使用した Azure Basic Load Balancer によるデュアル スタック (IPv4+IPv6) の仮想ネットワークを展開します。 |
|[Standard Load Balancer を使用した IPv4 と IPv6 のデュアル スタック仮想ネットワークの構成](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|2 つの VM と IPv4 および IPv6 のパブリック IP アドレスを使用した Azure Standard Load Balancer によるデュアル スタック (IPv4+IPv6) の仮想ネットワークを展開します。 |
|[チュートリアル:NAT ゲートウェイの作成とテスト - Azure CLI](../virtual-network/nat-gateway/tutorial-create-nat-gateway-cli.md)|仮想マシンを使用して NAT ゲートウェイを作成し、有効性を検証します。 |
