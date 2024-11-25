---
title: '場所と接続プロバイダー: Azure ExpressRoute | Microsoft Docs'
description: この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。 場所ごとにソートされています。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 0623726022e909c2bc1be405e2afb155142658be
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135036"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute パートナーとピアリングの場所

> [!div class="op_single_selector"]
> * [プロバイダー別の場所](expressroute-locations.md)
> * [場所別のプロバイダー](expressroute-locations-providers.md)


この記事の表では、ExpressRoute の地理的範囲と場所、ExpressRoute の接続プロバイダー、ExpressRoute のシステム インテグレーター (SI) に関する情報を提供します。

> [!Note]
> Azure リージョンと ExpressRoute の場所は 2 つの異なる概念であり、2 つの違いを理解することは、Azure ハイブリッド ネットワーク接続を理解するうえで非常に重要です。 
>
>

## <a name="azure-regions"></a>Azure Azure リージョン
Azure リージョンは、Azure のコンピューティング、ネットワーク、およびストレージのリソースが配置されているグローバル データセンターです。 Azure リソースを作成する場合、お客様はリソースの場所を選択する必要があります。 リソースの場所によって、リソースが作成される Azure データセンター (または可用性ゾーン) が決まります。

## <a name="expressroute-locations"></a>ExpressRoute の場所
ExpressRoute の場所 (ピアリングの場所や meet-me-locations と呼ばれることもあります) は、Microsoft エンタープライズ エッジ (MSEE) デバイスが配置されているコロケーション施設です。 ExpressRoute の場所は Microsoft のネットワークへの入り口であり、世界中に分散されているため、お客様は世界中の Microsoft のネットワークに接続できます。 これらの場所は、ExpressRoute パートナーと ExpressRoute Direct のお客様が Microsoft のネットワークへの交差接続を実行する場所です。 一般的に、ExpressRoute の場所と Azure リージョンを一致させる必要はありません。 たとえば、お客様は、リソースの場所が *米国東部* で、ピアリングの場所が *シアトル* である ExpressRoute 回線を作成できます。

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。 

[!INCLUDE [expressroute-azure-regions-geopolitical-region](../../includes/expressroute-azure-regions-geopolitical-region.md)]

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 接続プロバイダー

次の表に、接続する場所と、各場所のサービス プロバイダーを示します。 サービス プロバイダーと、そのサービス プロバイダーがサービスを提供できる場所を確認する場合は、[サービス プロバイダー別の場所](expressroute-locations.md)に関するページを参照してください。

* **ローカル Azure リージョン** は、それぞれのピアリングの場所で [ExpressRoute Local](expressroute-faqs.md) がアクセスできるリージョンです。 **該当なし** は、そのピアリングの場所で ExpressRoute Local が使用できないことを示します。

* **ゾーン** は [価格](https://azure.microsoft.com/pricing/details/expressroute/)を参照します。

### <a name="global-commercial-azure"></a>グローバルな商用 Azure
| **場所** | **アドレス** | **ゾーン** | **ローカル Azure リージョン** | **ER Direct** | **サービス プロバイダー** |
| --- | --- | --- | --- | --- | --- |
| **アムステルダム** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 西ヨーロッパ | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **アムステルダム 2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 西ヨーロッパ | 10G、100G | BICS、British Telecom、CenturyLink Cloud Connect、Colt、DE-CIX、Equinix、euNetworks、GÉANT、Interxion、NOS、NTT Global DataCenters EMEA、Orange、Vodafone |
| **アトランタ** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 該当なし | 10G、100G | Equinix、Megaport |
| **オークランド** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 該当なし | 10G | Devoli、Kordia、Megaport、REANNZ、Spark NZ、Vocus Group NZ |
| **バンコク** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | 該当なし | 10G | AIS、National Telecom UIH |
| **ベルリン** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | ドイツ北部 | 10G | Colt、Equinix、NTT Global DataCenters EMEA|
| **ボゴタ** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | 該当なし | 10G | Equinix |
| **釜山** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | 韓国南部 | 該当なし | LG CNS |
| **カンピナス** | [Ascenty](https://www.ascenty.com/en/data-centers-en/campinas/) | 3 | ブラジル南部 | 10G、100G | Ascenty |
| **キャンベラ** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | オーストラリア中部 | 10G、100G | CDC |
| **キャンベラ2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | オーストラリア中部 2| 10G、100G | CDC、Equinix |
| **ケープタウン** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 南アフリカ西部 | 10G | BCX、Internet Solutions - Cloud Connect、Liquid Telecom、MTN Global Connect、Teraco、Vodacom |
| **チェンナイ** | Tata Communications | 2 | インド南部 | 10G | BSNL、Global CloudXchange (GCX)、SIFY、Tata Communications、VodafoneIdea |
| **チェンナイ 2** | Airtel | 2 | インド南部 | 10G | Airtel |
| **シカゴ** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 米国中北部 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Colt、Comcast、Coresite、Equinix、InterCloud、Internet2、Level 3 Communications、Megaport、PacketFabric、PCCW Global Limited、Sprint、Telia Carrier、Verizon、Zayo |
| **シカゴ 2** | [CoreSite CH1](https://www.coresite.com/data-center/ch1-chicago-il) | 1 | 米国中北部 | 10G、100G | CoreSite |
| **コペンハーゲン** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 該当なし | 10G | Interxion |
| **ダラス** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 該当なし | 10G、100G | Aryaka Networks、AT&T NetBond、Cologix、Equinix、Internet2、Level 3 Communications、Megaport、Neutrona Networks、PacketFabric、Telmex Uninet、Telia Carrier、Transtelco、Verizon、Zayo|
| **デンバー** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 米国中西部 | 10G、100G | CoreSite、Megaport、PacketFabric、Zayo |
| **ドバイ** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | アラブ首長国連邦北部 | 該当なし | Etisalat UAE |
| **ドバイ 2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | アラブ首長国連邦北部 | 該当なし | DE-CIX、du datamena、Equinix、GBI、Megaport、Orange、Orixcom |
| **ダブリン** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 北ヨーロッパ | 10G、100G | CenturyLink Cloud Connect、Colt、eir、Equinix、GEANT、euNetworks、Interxion、Megaport |
| **ダブリン 2** | [Interxion DUB2](https://www.interxion.com/locations/europe/dublin) | 1 | 北ヨーロッパ | 10G、100G | Interxion |
| **フランクフルト** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | ドイツ中西部 | 10G、100G | AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、DE-CIX、Equinix、euNetworks、GBI、GEANT、InterCloud、Interxion、Megaport、NTT Global DataCenters EMEA、Orange、Telia Carrier、T-Systems |
| **Frankfurt2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | ドイツ中西部 | 10G、100G | Deutsche Telekom AG、Equinix |
| **ジュネーブ** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | スイス西部 | 10G、100G | Colt、Equinix、Megaport、Swisscom |
| **香港特別行政区** | [Equinix HK1](https://www.equinix.com/data-centers/asia-pacific-colocation/hong-kong-colocation/hong-kong-data-centers/hk1) | 2 | 東アジア | 10G | Aryaka Networks、British Telecom、CenturyLink Cloud Connect、Chief Telecom、China Telecom Global、China Unicom、Colt、Equinix、InterCloud、Megaport、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telia Carrier、Verizon |
| **香港特別行政区 2** | [iAdvantage MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | 東アジア | 10G | China Mobile International、China Telecom Global、iAdvantage、Megaport、PCCW Global Limited、SingTel |
| **ジャカルタ** | [Telin](https://www.telin.net/) | 4 | 該当なし | 10G | NTT Communications、Telin、XL Axiata |
| **ヨハネスブルグ** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 南アフリカ北部 | 10G | BCX、British Telecom、Internet Solutions - Cloud Connect、Liquid Telecom、MTN Global Connect、Orange、Teraco、Vodacom |
| **クアラルンプール** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | 該当なし | 該当なし | TIME dotCom |
| **ラスベガス** | [Switch LV](https://www.switch.com/las-vegas) | 1 | 該当なし | 10G、100G | CenturyLink Cloud Connect、Megaport、PacketFabric |
| **ロンドン** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 英国南部 | 10G、100G | AT&T NetBond、British Telecom、CenturyLink、Colt、Equinix、euNetworks、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 英国南部 | 10G、100G | BICS、British Telecom、CenturyLink Cloud Connect、Colt、GTT、IX Reach、Equinix、JISC、Megaport、NTT Global DataCenters EMEA、SES、Sohonet、Telehouse - KDDI |
| **ロサンゼルス** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 該当なし | 10G、100G | CoreSite、Equinix*、Megaport、Neutrona Networks、NTT、Zayo</br></br> **新しい ExpressRoute 回線は、ロサンゼルスの Equinix ではサポートされなくなりました。ロサンゼルス 2 で新しい回線を作成してください。* |
| **ロサンゼルス 2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | 該当なし | 10G、100G | Equinix |
| **マドリッド** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | 西ヨーロッパ | 10G、100G | Interxion、Megaport |
| **マルセイユ** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | フランス南部 | 該当なし | Colt、DE-CIX、GEANT、Interxion、Jaguar Network、Ooredoo Cloud Connect |
| **メルボルン** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | オーストラリア南東部 | 10G、100G | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG Telecom |
| **マイアミ** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 該当なし | 10G、100G | Claro、C3ntro、Equinix、Megaport、Neutrona Networks |
| **ミラノ** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | 該当なし | 10G | Colt、Equinix、Fastweb、IRIDEOS、Retelit |
| **ミネアポリス** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | 該当なし | 10G、100G | Cologix、Megaport |
| **モントリオール** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 該当なし | 10G、100G | Bell Canada、Cologix、Fibrenoire、Megaport、Telus、Zayo |
| **ムンバイ** | Tata Communications | 2 | インド西部 | 10G | BSNL、DE-CIX、Global CloudXchange (GCX)、Reliance Jio、Sify、Tata Communications、Verizon |
| **ムンバイ 2** | Airtel | 2 | インド西部 | 10G | Airtel、Sify、Vodafone Idea |
| **ミュンヘン** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | 該当なし | 10G | Colt、DE-CIX、Megaport |
| **ニューヨーク** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 1 | 該当なし | 10G、100G | CenturyLink Cloud Connect、Colt、Coresite、DE-CIX、Equinix、InterCloud、Megaport、Packet、Zayo |
| **ニューポート (ウェールズ)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 英国西部 | 10G、100G | British Telecom、Colt、Jisc、Level 3 Communications、Next Generation Data |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 西日本 | 10G、100G | AT TOKYO、BBIX、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、Megaport、NTT Communications、NTT SmartConnect、Softbank、Tokai Communications |
| **オスロ** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | ノルウェー東部 | 10G、100G | GlobalConnect、Megaport、Telenor、Telia Carrier |
| **パリ** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | フランス中部 | 10G、100G | British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、Jaguar Network、Megaport、Orange、Telia Carrier、Zayo |
| **パース** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 該当なし | 10G | Megaport、NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | 米国西部 3 | 10G、100G | Megaport、Zayo |
| **プネー** | [STT GDC Pune DC1](https://www.sttelemediagdc.in/our-data-centres-in-india) | 2 | インド中部| 10G | |
| **ケベック シティ** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | カナダ東部 | 10G、100G | Bell Canada、Equinix、Megaport、Telus |
| **ケレタロ (メキシコ)** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | 該当なし | 10G | Transtelco|
| **クインシー** | [Sabey Datacenter - Building A](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | 米国西部 2 | 10G、100G | |
| **Rio de Janeiro** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | ブラジル南東部 | 10G | Equinix |
| **サンアントニオ** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 米国中南部 | 10G、100G | CenturyLink Cloud Connect、Megaport |
| **サンパウロ** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | ブラジル南部 | 10G、100G | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEO |
| **サンパウロ 2** | [TIVIT TSM](https://www.tivit.com/en/tivit/) | 3 | ブラジル南部 | 10G、100G | |
| **シアトル** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 米国西部 2 | 10G、100G | Aryaka Networks、Equinix、Level 3 Communications、Megaport、Telus、Zayo |
| **ソウル** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | 韓国中部 | 10G、100G | KINX、KT、LG CNS、LGUplus、Equinix、Sejong Telecom、SK Telecom |
| **Seoul2** | [KT IDC](https://www.kt-idc.com/eng/introduce/sub1_4_10.jsp#tab) | 2 | 韓国中部 | 該当なし | |
| **シリコン バレー** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 米国西部 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Comcast、Coresite、Equinix、InterCloud、Internet2、IX Reach、Packet、PacketFabric、Level 3 Communications、Megaport、Orange、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **シリコン バレー2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 米国西部 | 10G、100G | Colt、Coresite | 
| **シンガポール** | [Equinix SG1](https://www.equinix.com/data-centers/asia-pacific-colocation/singapore-colocation/singapore-data-center/sg1) | 2 | 東南アジア | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、China Mobile International、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **シンガポール 2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 東南アジア | 10G、100G | CenturyLink Cloud Connect、China Unicom Global、Colt、Epsilon Global Communications、Equinix、Megaport、PCCW Global Limited、SingTel、Telehouse - KDDI |
| **スタバンゲル** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | ノルウェー西部 | 10G、100G |GlobalConnect、Megaport |
| **ストックホルム** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 該当なし | 10G | Equinix、Megaport、Telia Carrier |
| **シドニー** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | オーストラリア東部 | 10G、100G | AARNet、AT&T NetBond、British Telecom、Devoli、Equinix、Kordia、Megaport、NEXTDC、NTT Communications、Optus、Orange、Spark NZ、Telstra Corporation、TPG Telecom、Verizon、Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | オーストラリア東部 | 10G、100G | Megaport、NextDC |
| **台北** | Chief Telecom | 2 | 該当なし | 10G | Chief Telecom、Chunghwa Telecom、FarEasTone |
| **東京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 東日本 | 10G、100G | Aryaka Networks、AT&T NetBond、BBIX、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Internet Initiative Japan Inc. - IIJ、Megaport、NTT Communications、NTT EAST、Orange、Softbank、Telehouse - KDDI、Verizon |
| **東京 2** | [アット東京](https://www.attokyo.com/) | 2 | 東日本 | 10G、100G | AT TOKYO、China Unicom Global、Megaport、Tokai Communications |
| **トロント** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | カナダ中部 | 10G、100G | AT&T NetBond、Bell Canada、CenturyLink Cloud Connect、Cologix、Equinix、IX Reach Megaport、Telus、Verizon、Zayo |
| **トロント 2** | [Allied REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | カナダ中部 | 10G、100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | 該当なし | 10G | Bell Canada、Cologix、Megaport、Telus |
| **ワシントン DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/)、[Equinix DC6](https://www.equinix.com/data-centers/americas-colocation/united-states-colocation/washington-dc-data-centers/dc6) | 1 | 米国東部、米国東部 2 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Colt、Comcast、Coresite、Equinix、Internet2、InterCloud、Iron Mountain、IX Reach、Level 3 Communications、Megaport、Neutrona Networks、NTT Communications、Orange、PacketFabric、SES、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **ワシントン DC2** | [Coresite VA2](https://www.coresite.com/data-center/va2-reston-va) | 1 | 米国東部、米国東部 2 | 10G、100G | CenturyLink Cloud Connect、Coresite、Intelsat、Megaport、Viasat、Zayo | 
| **チューリッヒ** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | スイス北部 | 10G、100G | Colt、Equinix、InterCloud、Interxion、Megaport、Swisscom |

 **+** は近日対応予定を表します

### <a name="national-cloud-environments"></a>国内のクラウド環境

Azure の各国のクラウドは互いに分離され、またグローバルな商用 Azure からも分離されています。 ある Azure クラウドの ExpressRoute は、他のクラウド内の Azure リージョンには接続できません。

### <a name="us-government-cloud"></a>米国政府のクラウド
| **場所** | **アドレス** | **ローカル Azure リージョン**| **ER Direct** | **サービス プロバイダー** |
| --- | --- | --- | --- | --- |
| **アトランタ** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | 該当なし | 10G、100G | Equinix |
| **シカゴ** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 該当なし | 10G、100G | AT&T NetBond、British Telecom、Equinix、Level 3 Communications、Verizon |
| **ダラス** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 該当なし | 10G、100G | Equinix、Internet2、Megaport、Verizon |
| **ニューヨーク** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 該当なし | 10G、100G | Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov アリゾナ | 10G、100G | AT&T NetBond、CenturyLink Cloud Connect、Megaport |
| **サンアントニオ** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov テキサス | 10G、100G | CenturyLink Cloud Connect、Megaport |
| **シリコン バレー** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 該当なし | 10G、100G | AT&T、Equinix、Level 3 Communications、Verizon |
| **シアトル** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 該当なし | 10G、100G | Equinix、Megaport |
| **ワシントン DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 東、US Gov バージニア州 | 10G、100G | AT&T NetBond、CenturyLink Cloud Connect、Equinix、Level 3 Communications、Megaport、Verizon |

### <a name="china"></a>中国
| **場所** | **アドレス** | **ローカル Azure リージョン** | **ER Direct** | **サービス プロバイダー** |
| --- | --- | --- | --- | --- |
| **北京** | China Telecom | 該当なし | 10G | China Telecom |
| **北京 2** | GDS | 該当なし | 10G | China Telecom、China Unicom、GDS |
| **上海** | China Telecom | 該当なし | 10G | China Telecom |
| **上海 2** | GDS | 該当なし | 10G | China Telecom、China Unicom、GDS |

詳細については、 [中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>ドイツ
| **場所** | **サービス プロバイダー** |
| --- | --- |
| **ベルリン** |e-shelter、Megaport+、T-Systems |
| **フランクフルト** |Colt、Equinix、Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Exchange プロバイダー経由の接続
接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

* 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/resources/videos/cloud-exchange-overview)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
  * 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
* Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
  * 「 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) 」の手順に従い、接続を構築します。

## <a name="connectivity-through-satellite-operators"></a>衛星通信業者経由の接続
遠隔地でファイバー接続がない場合、あるいはその他の接続方法を探してみる場合、次の衛星通信業者をお調べください。 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>その他のサービス プロバイダー経由の接続
| **場所** | **Exchange** | **接続プロバイダー** |
| --- | --- | --- |
| **アムステルダム** | Equinix、Interxion、Level 3 Communications | BICS、CloudXpress、Eurofiber、Fastweb S.p.A、Gulf Bridge International、Kalaam Telecom Bahrain B.S.C、MainOne、Nianet、POST Telecom Luxembourg、Proximus、RETN、TDC Erhverv、Telecom Italia Sparkle、Telekom Deutschland GmbH、Telia |
| **アトランタ** | Equinix| Crown Castle
| **ケープタウン** | Teraco | MTN |
| **チェンナイ** | Tata Communications | Tata Teleservices |
| **シカゴ** | Equinix| Crown Castle、Spectrum Enterprise、Windstream |
| **ダラス** | Equinix、Megaport | Axtel、C3ntro Telecom、Cox Business、Crown Castle、Data Foundry、Spectrum Enterprise、Transtelco |
| **フランクフルト** | Interxion | BICS、Cinia、Equinix、Nianet、QSC AG、Telekom Deutschland GmbH |
| **ハンブルク** | Equinix | Cinia |
| **香港特別行政区** | Equinix | Chief、Macroview Telecom |
| **ヨハネスブルグ** | Teraco | MTN |
| **ロンドン** | BICS、Equinix、euNetworks| Bezeq International Ltd.、CoreAzure、Epsilon Telecommunications Limited、Exponential E、HSO、NexGen Networks、Proximus、Tamares Telecom、Zain |
| **ロサンゼルス** | Equinix |Crown Castle、Spectrum Enterprise、Transtelco |
| **マドリッド** | Level3 | Zertia |
| **モントリオール** | Cologix| Airgate Technologies, Inc.Aptum Technologies、Rogers、Zirro |
| **ムンバイ** | Tata Communications | Tata Teleservices |
| **ニューヨーク** |Equinix、Megaport | Altice Business、Crown Castle、Spectrum Enterprise、Webair |
| **パリ** | Equinix | Proximus |
| **ケベック シティ** | Megaport | Fibrenoire |
| **サンパウロ** | Equinix | Venha Pra Nuvem |
| **シアトル** |Equinix | Alaska Communications |
| **シリコン バレー** |Coresite、Equinix | Cox Business、Spectrum Enterprise、Windstream、X2nsat Inc. |
| **シンガポール** |Equinix |1CLOUDSTAR、BICS、CMC Telecom、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **スラウ** | Equinix | HSO|
| **シドニー** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | アルテリア・ネットワークス株式会社、株式会社ブロードバンドタワー |
| **トロント** | Equinix、Megaport | Airgate Technologies Inc.、Beanfield Metroconnect、Aptum Technologies、IVedha Inc、Oncore Cloud Services Inc.、Rogers、Thinktel、Zirro|
| **ワシントン DC** |Equinix | Altice Business、BICS、Cox Business、Crown Castle、Gtt Communications Inc、Epsilon Telecommunications Limited、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute システム インテグレーター
ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

| **Continent** | **システム インテグレーター** |
| --- | --- |
| **アジア** |Avanade Inc.、OneAs1a |
| **オーストラリア** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **ヨーロッパ** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北米** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南アメリカ** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>次のステップ
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "場所のマップ"
