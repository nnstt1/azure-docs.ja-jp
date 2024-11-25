---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 01/12/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 4648693bc0dd7cebd8f8aec1cf878a8c3abfc49e
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122635490"
---
| リソース | 制限 |
| --- | --- |
| サブスクリプションあたりの ExpressRoute 回線数 |50 |
| サブスクリプションあたりのリージョンごとの ExpressRoute 回線数 (Azure Resource Manager) |10 |
| ExpressRoute Standard の Azure プライベート ピアリングにアドバタイズされる IPv4 ルートの最大数 |4,000 |
| ExpressRoute Premium アドオンの Azure プライベート ピアリングにアドバタイズされる IPv4 ルートの最大数 |10,000 |
| ExpressRoute Standard の Azure プライベート ピアリングにアドバタイズされる IPv6 ルートの最大数 |100 |
| ExpressRoute Premium アドオンの Azure プライベート ピアリングにアドバタイズされる IPv6 ルートの最大数 |100 |
| ExpressRoute 接続に使用する VNet アドレス空間からの Azure プライベート ピアリングからアドバタイズされる最大 IPv4 ルート数 | 1,000 |
| ExpressRoute 接続に使用する VNet アドレス空間からの Azure プライベート ピアリングからアドバタイズされる最大 IPv6 ルート数 | 1,000 |
| ExpressRoute Standard の Azure Microsoft ピアリングにアドバタイズされる最大ルート数 |200 |
| ExpressRoute Premium アドオンの Azure Microsoft ピアリングにアドバタイズされる最大ルート数 |200 |
| 同じピアリング場所で同じ仮想ネットワークにリンクされる ExpressRoute 回線の最大数 |4 |
| さまざまなピアリング場所で同じ仮想ネットワークにリンクされる ExpressRoute の最大数 |16 (詳細については、「[ゲートウェイの SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md#aggthroughput)」を参照してください。) |
| ExpressRoute 回線あたりで許可される仮想ネットワークのリンク数 |「[ExpressRoute 回線あたりの仮想ネットワーク数](#vnetpercircuit)」の表を参照してください。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>ExpressRoute 回線あたりの仮想ネットワーク数
| **回線のサイズ** | **Standard での仮想ネットワークのリンク数** | **Premium アドオンでの仮想ネットワークのリンク数** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps ExpressRoute Direct のみ*

> [!NOTE]
> Global Reach の接続数は、ExpressRoute 回線あたりの仮想ネットワーク接続数の上限にカウントされます。 たとえば、10 Gbps Premium 回線であれば、Global Reach 接続が 5 本と ExpressRoute ゲートウェイへの接続が 95 本、または Global Reach 接続が 95 本と ExpressRoute ゲートウェイへの接続が 5 本許容されます。回線に対して 100 接続という上限を超えなければ、他のどのような組み合わせでもかまいません。
