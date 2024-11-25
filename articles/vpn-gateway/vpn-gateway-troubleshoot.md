---
title: 構成と接続のトラブルシューティング
titleSuffix: Azure VPN Gateway
description: この記事では、お使いの VPN Gateway の構成、接続のトラブルシューティングを行い、スループットを検証する際に役立つ記事へのリンクを紹介します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: 19edbd41617a01e54cd14ff7f2991763b2da9bba
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108204497"
---
# <a name="troubleshoot-vpn-gateway"></a>VPN Gateway のトラブルシューティング

VPN Gateway の接続は、さまざまな原因で失敗する可能性があります。 この記事には、トラブルシューティングを開始するためのリンクを示します。 完全な一覧については、このページの左側の「**トラブルシューティング**」の下にある目次に示した記事を参照してください。

## <a name="troubleshooting-scenarios-and-solutions"></a>トラブルシューティングのシナリオと解決策

* [VNet への VPN スループットの確認](vpn-gateway-validate-throughput-to-vnet.md)<br>VPN ゲートウェイ接続を使用すると、Azure 内の Virtual Network とオンプレミスの IT インフラストラクチャ間の安全なクロスプレミス接続を確立できます。 この記事では、オンプレミスのリソースから Azure 仮想マシン (VM) へのネットワーク スループットを検証する方法を示します。 また、トラブルシューティングのガイドラインも示します。

* [VPN およびファイアウォールのデバイス設定](vpn-gateway-third-party-settings.md)<br>この記事では、Azure VPN Gateway で使うサード パーティ製の VPN デバイスまたはファイアウォール デバイスに関するさまざまな推奨ソリューションを紹介しています。 サード パーティの VPN デバイスまたはファイアウォール デバイスに関するテクニカル サポートは、デバイスのベンダーから提供されます。

* [ポイント対サイト接続](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>この記事では、発生する可能性があるポイント対サイト接続のよくある問題について説明します。 また、これらの問題の考えられる原因と解決策についても説明します。

* [サイト間接続](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>オンプレミスのネットワークと Azure 仮想ネットワークの間にサイト間 VPN 接続を構成した後、VPN 接続が突然動作を停止して再接続できません。 この記事では、この問題の解決に役立つトラブルシューティング手順について説明します。

* [診断ログを使用した Azure VPN Gateway のトラブルシューティング](troubleshoot-vpn-with-azure-diagnostics.md)<br>診断ログを使用すると、構成アクティビティ、VPN トンネル接続、IPsec ログ、BGP ルート交換、ポイント対サイト詳細ログを含む、複数の VPN ゲートウェイに関連するイベントのトラブルシューティングを行うことができます。 

## <a name="next-steps"></a>次のステップ

次の手順を使用して、[VNet および VPN 接続を検証する](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)こともできます。
