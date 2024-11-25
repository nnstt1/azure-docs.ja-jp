---
title: Azure Virtual WAN とリモートでの作業
description: COVID-19 の世界的流行により、Azure Virtual WAN を利用してリモートで作業できるようにする方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: ad8a365550b25906c7fb4fabda725b21abe97417
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477764"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN とリモート作業のサポート

>[!NOTE]
>この記事では、Azure Virtual WAN、Azure、Microsoft ネットワーク、および Azure パートナー エコシステムを利用してリモートで作業し、COVID-19 の危機によって直面しているネットワークの問題を軽減する方法について説明します。
>

緊急にリモート ユーザーへの接続を提供する必要がありますか。
突然、予定していた数を超えるユーザーのサポートが必要になりましたか。
自宅から接続するユーザーが、クラウドだけでなくオンプレミスにもアクセスできる必要がありますか。
リモート ユーザーがプライベート WAN の内側にあるリソースに接続できるようにする必要がありますか。
リージョン間の接続を設定する必要なしに、ユーザーがクラウド内のリソースにアクセスする必要がありますか。
この前例のない世界的なパンデミックにあたり、Azure Virtual WAN チームはお客様の接続ニーズに対応します。

Azure Virtual WAN は、ネットワーク、セキュリティ、ルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供するネットワーク サービスです。 これらの機能には、ブランチ接続 (SD-WAN や VPN CPE などの Virtual WAN パートナー デバイスからの接続自動化経由)、サイト間 VPN 接続、リモート ユーザー VPN (ポイント対サイト) 接続、プライベート (ExpressRoute) 接続、クラウド内接続 (仮想ネットワークの推移的な接続)、VPN ExpressRoute 相互接続、ルーティング、Azure ファイアウォール、プライベート接続のための暗号化などが含まれます。Virtual WAN の利用を開始するために、これらのすべてのユース ケースを用意する必要はありません。 1 つのユース ケースだけで始めて、発展に応じてネットワークを調整することができます。

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan1.png)

リモート ユーザーのためのネットワークを稼働させるために必要な作業を見てみましょう。

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>リモート ユーザーの接続を設定する

IPsec/IKE (IKEv2) 接続または OpenVPN 接続を使用して、Azure 内のリソースに接続できます。 この種の接続を使用するには、リモート ユーザー用に VPN クライアントを構成する必要があります。 このクライアントには、[Azure VPN クライアント](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN クライアント、または IKEv2 をサポートする任意のクライアントを使用できます。 詳細については、[ポイント対サイト接続の作成](virtual-wan-point-to-site-portal.md)に関する記事をご覧ください。

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>リモート ユーザーからオンプレミスへの接続

2 つのオプションがあります。

* 任意の既存の VPN デバイスでサイト間接続をセットアップします。 IPsec VPN デバイスを Azure Virtual WAN ハブに接続すると、ポイント対サイト ユーザー VPN (リモート ユーザー) とサイト間 VPN の間の相互接続が自動的に確立されます。 オンプレミスの VPN デバイスから Azure Virtual WAN へのサイト間 VPN を設定する方法の詳細については、[Virtual WAN を使用したサイト間接続の作成](virtual-wan-site-to-site-portal.md)に関する記事をご覧ください。

* ExpressRoute 回線を Virtual WAN ハブに接続します。 ExpressRoute 回線を接続するには、Virtual WAN に ExpressRoute ゲートウェイをデプロイする必要があります。 それをデプロイするとすぐに、ポイント対サイト ユーザー VPN と ExpressRoute ユーザーの間の相互接続が自動的に確立されます。 ExpressRoute 接続を作成する方法については、[Virtual WAN を使用した ExpressRoute 接続の作成](virtual-wan-expressroute-portal.md)に関する記事をご覧ください。 既存の ExpressRoute 回線を使用して、Azure Virtual WAN に接続することができます。

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Basic Virtual WAN の既存のお客様

Basic Virtual WAN では、サイト間 VPN のみが提供されます。 リモート ユーザーが接続できるようにするには、Virtual WAN を Standard Virtual WAN にアップグレードする必要があります。 Virtual WAN をアップグレードする手順については、「[Virtual WAN を Basic から Standard にアップグレードする](upgrade-virtual-wan.md)」をご覧ください

## <a name="additional-information"></a><a name="other considerations"></a>追加情報

Virtual WAN では、リージョンまたは場所ごとに複数のハブがサポートされています。 場所の情報については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。 各ハブでは、最大で、100,000 のリモート ユーザー接続、1,000 のブランチ接続、4 つの ExpressRoute 回線、500 の Virtual Network 接続がサポートされています。 リモート ユーザーをスケールアップするとき、質問がある場合は、遠慮せず azurevirtualwan@microsoft.com にメールでお問い合わせください。 テクニカル サポートが必要な場合は、Azure portal からサポート チケットを開くと、ヘルプが提供されます。

## <a name="faq"></a><a name="faq"></a>FAQ

「[Virtual WAN の FAQ](virtual-wan-faq.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Virtual WAN について詳しくは、[Virtual WAN の概要](virtual-wan-about.md)に関する記事をご覧ください
