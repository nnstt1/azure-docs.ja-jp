---
title: 接続用の VPN デバイスについて
titleSuffix: Azure VPN Gateway
description: サイト間クロスプレミス接続用の VPN デバイスと IPsec パラメーターについて説明します。 構成の手順およびサンプルへのリンクが提供されています。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/28/2021
ms.author: yushwang
ms.openlocfilehash: 1a86e4dc6703d9a3c25c4c38a1a3f50bbbe80a6b
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202391"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>サイト間 VPN ゲートウェイ接続用の VPN デバイスと IPsec/IKE パラメーターについて

VPN ゲートウェイを使用する Site-to-Site (S2S) クロスプレミス VPN 接続を構成するには、VPN デバイスが必要です。 サイト間接続は、ハイブリッド ソリューションを作成するときに使用できるほか、オンプレミスのネットワークと仮想ネットワークとの間にセキュリティで保護された接続が必要な場合に使用できます。 この記事には、VPN ゲートウェイ用の検証済みの VPN デバイスの一覧と IPsec/IKE パラメーターの一覧が掲載されています。

> [!IMPORTANT]
> オンプレミスの VPN デバイスと VPN ゲートウェイの間で接続の問題が発生している場合は、「[デバイスの互換性に関する既知の問題](#known)」を参照してください。
>

### <a name="items-to-note-when-viewing-the-tables"></a>表を確認するときの注意事項:

* Azure VPN ゲートウェイ関連の用語が変更されています。 名前のみが変更されています。 機能の変更はありません。
  * 静的ルーティング = PolicyBased
  * 動的ルーティング = RouteBased
* HighPerformance VPN ゲートウェイと RouteBased VPN ゲートウェイの仕様は、特に記載がない限り同じです。 たとえば、RouteBased VPN ゲートウェイと互換性がある検証済みの VPN デバイスは、HighPerformance VPN ゲートウェイとも互換性があります。

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>検証済みの VPN デバイスとデバイス構成ガイド

Microsoft では、デバイス ベンダーと協力して一連の標準的な VPN デバイスを検証しました。 以下の一覧に含まれているデバイス ファミリ内のすべてのデバイスは、VPN ゲートウェイで動作します。 構成する VPN Gateway ソリューションの VPN の種類 (PolicyBased または RouteBased) については、[VPN ゲートウェイの設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)に関するページを参照してください。

VPN デバイスを構成するには、適切なデバイス ファミリに対応するリンクを参照してください。 構成方法へのリンクは、入手できる範囲で記載しています。 VPN デバイスのサポートについては、デバイスの製造元に問い合わせてください。

|**ベンダー名**          |**デバイス ファミリ**     |**OS の最小バージョン** |**PolicyBased の構成手順** |**RouteBased の構成手順** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |互換性なし  |[構成ガイド](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |AR シリーズ VPN ルーター |AR-Series 5.4.7 以降               | [構成ガイド](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[構成ガイド](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | CloudEOS Router | vEOS 4.24.0FX | (未テスト) | [構成ガイド](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen Firewall |PolicyBased:5.4.3<br>RouteBased:6.2.0 |[構成ガイド](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[構成ガイド](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |セキュリティ ゲートウェイ |R80.10 |[構成ガイド](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[構成ガイド](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4 以降 (IKEv2*) |サポートされています |[構成ガイド*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased:IOS 15.1<br>RouteBased:IOS 15.2 |サポートされています |サポートされています |
| Cisco | CSR | RouteBased:IOS-XE 16.10 | (未テスト) | [構成スクリプト](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased:IOS 15.0<br>RouteBased*:IOS 15.1 |サポートされています |サポートされています |
| Cisco |Meraki (MX) | MX v15.12 |互換性なし | [構成ガイド](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (Viptela OS) | 18.4.0 (アクティブ/パッシブ モード)<br><br>19.2 (アクティブ/アクティブ モード) | 互換性なし |  [手動構成 (アクティブ/パッシブ)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Cloud Onramp 構成 (アクティブ/アクティブ)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX、SDX、VPX |10.1 以上 |[構成ガイド](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |互換性なし |
| F5 |BIG-IP シリーズ |12.0 |[構成ガイド](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[構成ガイド](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (未テスト) |[構成ガイド](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone Networks | Next-Gen Firewalls (NGFW) | 5.5R7  | (未テスト) | [構成ガイド](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |SEIL シリーズ |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[構成ガイド](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |互換性なし |
| Juniper |SRX |PolicyBased:JunOS 10.2<br>Routebased:JunOS 11.4 |サポートされています |[構成スクリプト](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J シリーズ |PolicyBased:JunOS 10.4r9<br>RouteBased:JunOS 11.4 |サポートされています |[構成スクリプト](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |サポートされています |[構成スクリプト](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |サポートされています |[構成スクリプト](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|サポートされています |[構成スクリプト](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |ルーティングとリモート アクセス サービス |Windows Server 2012 |互換性なし |サポートされています |
| Open Systems AG |Mission Control Security Gateway |該当なし |[構成ガイド](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |互換性なし |
| Palo Alto Networks |PAN-OS を実行しているすべてのデバイス |PAN-OS<br>PolicyBased:6.1.5 以降<br>RouteBased:7.1.4 |サポートされています |[構成ガイド](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (Developer) | VyOS | VyOS 1.2.2 | (未テスト) | [構成ガイド](https://docs.vyos.io/en/latest/configexamples/azure-vpn-bgp.html)|
| ShareTech | Next Generation UTM (NU シリーズ) | 9.0.1.3 | 互換性なし | [構成ガイド](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ シリーズ、NSA シリーズ<br>SuperMassive シリーズ<br>E-class NSA シリーズ |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |互換性なし |[構成ガイド](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG Next Gen Firewall | XG v17 | (未テスト) | [構成ガイド](https://community.sophos.com/kb/127546)<br><br>[構成ガイド - 複数 SA](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM1.1.5/VpnPlusServer-1.2.0 | (未テスト) | [構成ガイド](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 | (未テスト) | [BGP over IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI over IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E-636L3 | 5.2.0.T3 Build-13  | (未テスト) | 構成ガイド |
| WatchGuard |All |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[構成ガイド](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[構成ガイド](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL USG シリーズ<br>ZyWALL ATP シリーズ<br>ZyWALL VPN シリーズ | ZLD v4.32 以降 | (未テスト) | [VTI over IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP over IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) Cisco ASA バージョン 8.4 以降では IKEv2 のサポートが追加されており、"UsePolicyBasedTrafficSelectors" オプションでカスタム IPsec/IKE ポリシーを使用して、Azure VPN ゲートウェイに接続できます。 こちらの[ハウツー記事](vpn-gateway-connect-multiple-policybased-rm-ps.md)を参照してください。
>
> (\*\*) ISR 7200 シリーズのルーターでは、PolicyBased の VPN のみがサポートされています。

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>Azure からの VPN デバイス構成スクリプトのダウンロード

特定のデバイスについて、Azure から直接構成スクリプトをダウンロードできます。 詳細およびダウンロードの方法については、[VPN デバイス構成スクリプトのダウンロード](vpn-gateway-download-vpndevicescript.md)に関するページをご覧ください。

### <a name="devices-with-available-configuration-scripts"></a>構成スクリプトを利用できるデバイス

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>未検証の VPN デバイス

検証済み VPN デバイスの表にお使いのデバイスが見つからない場合でも、そのデバイスをサイト間接続に利用できる可能性があります。 詳細なサポートと構成手順については、デバイスの製造元にお問い合わせください。

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>デバイス構成のサンプルの編集

提供されている VPN デバイス構成のサンプルをダウンロードしたら、一部の値を使用している環境の設定を反映した値に置換する必要があります。

### <a name="to-edit-a-sample"></a>サンプルを編集するには:

1. メモ帳を使用してサンプルを開きます。
2. お使いの環境に関連する値を含む <*テキスト*> 文字列をすべて検索して置き換えます。 < と > を必ず含めてください。 名前を指定する場合、選択する名前は一意である必要があります。 コマンドが機能しない場合は、デバイスの製造元のドキュメントを参照してください。

| **サンプル テキスト** | **次に変更** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |このオブジェクトに選択した名前。 例: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |このオブジェクトに選択した名前。 例: myAzureNetwork |
| &lt;RP_AccessList&gt; |このオブジェクトに選択した名前。 例: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |このオブジェクトに選択した名前。 例: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |このオブジェクトに選択した名前。 例: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |範囲を指定します。 例:192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |サブネット マスクを指定します。 例:255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |オンプレミスの範囲を指定します。 例:10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |オンプレミスのサブネット マスクを指定します。 例:255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |この情報は仮想ネットワークに固有であり、 **ゲートウェイの IP アドレス** として管理ポータルに存在しています。 |
| &lt;SP_PresharedKey&gt; |この情報は仮想ネットワークに固有であり、[キーの管理] として管理ポータルに存在しています。 |

## <a name="default-ipsecike-parameters"></a><a name="ipsec"></a>デフォルトの IPsec/IKE パラメーター

下の表には、Azure VPN ゲートウェイが既定の構成で使用するアルゴリズムとパラメーターの組み合わせが示されています (**既定のポリシー**)。 Azure Resource Manager デプロイメント モデルで作成されたルートベースの VPN ゲートウェイでは、個別の接続ごとにカスタム ポリシーを指定できます。 詳細な手順については、[IPsec/IKE ポリシーの構成](vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページを参照してください。

また、TCP **MSS** は **1350** で固定する必要があります。 お使いの VPN デバイスで MSS クランプがサポートされていない場合、別の方法として、トンネル インターフェイスの **MTU** を **1400** バイトに設定することができます。

以下の表では、次のようになっています。

* SA = セキュリティ アソシエーション (Security Association)
* IKE フェーズ 1 は "メイン モード" と呼ばれることもあります。
* IKE フェーズ 2 は "クイック モード" と呼ばれることもあります。

### <a name="ike-phase-1-main-mode-parameters"></a>IKE フェーズ 1 (メイン モード) のパラメーター

| **プロパティ**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| IKE のバージョン           |IKEv1              |IKEv1 および IKEv2    |
| Diffie-hellman グループ  |グループ 2 (1024 ビット) |グループ 2 (1024 ビット) |
| 認証方法 |事前共有キー     |事前共有キー     |
| 暗号化とハッシュ アルゴリズム |1.AES256、SHA256<br>2.AES256、SHA1<br>3.AES128、SHA1<br>4. 3DES、SHA1 |1.AES256、SHA1<br>2.AES256、SHA256<br>3.AES128、SHA1<br>4.AES128、SHA256<br>5. 3DES、SHA1<br>6. 3DES、SHA256 |
| SA の有効期間           |28,800 秒     |28,800 秒     |

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE フェーズ 2 (クイック モード) のパラメーター

| **プロパティ**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| IKE のバージョン                   |IKEv1          |IKEv1 および IKEv2                              |
| 暗号化とハッシュ アルゴリズム |1.AES256、SHA256<br>2.AES256、SHA1<br>3.AES128、SHA1<br>4. 3DES、SHA1 |[RouteBased QM SA プラン](#RouteBasedOffers) |
| SA の有効期間 (時間)            |3,600 秒  |27,000 秒                               |
| SA の有効期間 (バイト)           |102,400,000 KB |102,400,000 KB                               |
| Perfect Forward Secrecy (PFS) |いいえ             |[RouteBased QM SA プラン](#RouteBasedOffers) |
| Dead Peer Detection (DPD)     |サポートされていません  |サポートされています                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>RouteBased VPN IPsec セキュリティ アソシエーション (IKE クイック モード SA) プラン

以下の表は、IPsec SA (IKE クイック モード) プランの一覧です。 プランは提示される、または受け入れられる優先度順に表示されています。

#### <a name="azure-gateway-as-initiator"></a>発信側としての Azure ゲートウェイ

|-  |**暗号化**|**認証**|**PFS グループ**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |なし         |
| 2 |AES256        |SHA1              |なし         |
| 3 |3DES          |SHA1              |なし         |
| 4 |AES256        |SHA256            |なし         |
| 5 |AES128        |SHA1              |なし         |
| 6 |3DES          |SHA256            |なし         |

#### <a name="azure-gateway-as-responder"></a>応答側としての Azure ゲートウェイ

|-  |**暗号化**|**認証**|**PFS グループ**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |なし         |
| 2 |AES256        |SHA1              |なし         |
| 3 |3DES          |SHA1              |なし         |
| 4 |AES256        |SHA256            |なし         |
| 5 |AES128        |SHA1              |なし         |
| 6 |3DES          |SHA256            |なし         |
| 7 |DES           |SHA1              |なし         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |なし         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* RouteBased および HighPerformance VPN ゲートウェイで IPsec ESP NULL 暗号化を指定することができます。 Null ベースの暗号化では、転送中のデータ保護は提供されません。そのため、最大のスループットおよび最小の待機時間が必要な場合にのみ使用する必要があります。 クライアントは、VNet 間の通信シナリオ、または暗号化がソリューションの他の場所に適用されている場合に、この暗号化の使用を選択することができます。
* インターネット経由のクロスプレミス接続では、重要な通信のセキュリティを確保するため、上記の表にある暗号化およびハッシュ アルゴリズムによる既定の Azure VPN Gateway 設定を使用してください。

## <a name="known-device-compatibility-issues"></a><a name="known"></a>デバイスの互換性に関する既知の問題

> [!IMPORTANT]
> この内容は、サード パーティの VPN デバイスと Azure VPN ゲートウェイの互換性に関する既知の問題です。 Azure チームは、ここに記載されている問題に対処するためにベンダーと積極的に連携しています。 問題が解決されると、このページが最新の情報で更新されるため、 定期的に確認してください。
>
>

### <a name="feb-16-2017"></a>2017 年 2 月 16 日

Azure ルートベースの VPN 用の **Palo Alto Networks の 7.1.4 より前のバージョンのデバイス**:Palo Alto Networks の PAN-OS のバージョンが 7.1.4 より前の VPN デバイスを使用している場合、Azure ルートベースの VPN ゲートウェイへの接続の問題が発生したときは次の手順を実行してください。

1. Palo Alto Networks デバイスのファームウェアのバージョンを確認します。 PAN-OS バージョンが 7.1.4 よりも前の場合は、7.1.4 にアップグレードしてください。
2. Palo Alto Networks デバイスで、Azure VPN ゲートウェイに接続しているときにフェーズ 2 SA (またはクイック モード SA) の有効期間を 28,800 秒 (8 時間) に変更します。
3. 接続の問題が解消しない場合は、Azure Portal からサポート リクエストを作成してください。
