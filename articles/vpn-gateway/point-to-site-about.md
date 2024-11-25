---
title: Azure ポイント対サイト VPN 接続について
titleSuffix: Azure VPN Gateway
description: ポイント対サイト VPN について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ddcb49b1ce2bd882590b1d85cb634846ef3b4d40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729632"
---
# <a name="about-point-to-site-vpn"></a>ポイント対サイト VPN について

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 このソリューションは、在宅勤務者が自宅や会議室など、遠隔地から Azure VNet に接続する場合に便利です。 P2S VPN は、VNet への接続を必要とするクライアントが数台である場合に、S2S VPN の代わりに使用するソリューションとしても便利です。 この記事は、[Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)に適用されます。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S で使用されるプロトコル

ポイント対サイト VPN では、次のいずれかのプロトコルを使用できます。

* SSL/TLS ベースの VPN プロトコルである **OpenVPN® プロトコル**。 ほとんどのファイアウォールは、TLS で使用されるアウトバウンド TCP ポート 443 を開いているため、TLS VPN ソリューションはファイアウォールを通過できます。 OpenVPN は、Android、iOS (バージョン 11.0 以上)、Windows、Linux、および Mac デバイス (macOS バージョン 10.13 以上) から接続する際に使用できます。

* Secure Socket トンネリング プロトコル (SSTP)。これは、TLS ベースの独自の VPN プロトコルです。 ほとんどのファイアウォールは、TLS で使用されるアウトバウンド TCP ポート 443 を開いているため、TLS VPN ソリューションはファイアウォールを通過できます。 SSTP は、Windows デバイスでのみサポートされます。 Azure では、SSTP を備え、TLS 1.2 をサポートするすべてのバージョンの Windows (Windows 8.1 以降) がサポートされています。

* IKEv2 VPN。これは、標準ベースの IPsec VPN ソリューションです。 IKEv2 VPN は、Mac デバイス (macOS バージョン 10.11 以上) から接続する際に使用できます。


>[!NOTE]
>P2S 用 IKEv2 および OpenVPN は、[Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)でのみ使用できます。 これらは、クラシック デプロイ モデルでは使用できません。
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>P2S VPN クライアントの認証方法

Azure が P2S VPN 接続を受け入れる前に、ユーザーはまず認証を受ける必要があります。 Azure では、接続するユーザーを認証するメカニズムが 2 つ用意されています。

### <a name="authenticate-using-native-azure-certificate-authentication"></a>ネイティブ Azure 証明書認証を使用した認証

ネイティブ Azure 証明書認証を使用する場合、デバイス上にあるクライアント証明書が、接続するユーザーの認証に使用されます。 クライアント証明書は信頼されたルート証明書から生成され、各クライアント コンピューターにインストールされます。 エンタープライズ ソリューションを使って生成されたルート証明書を使用することも、自己署名証明書を生成することもできます。

クライアント証明書の検証は、P2S VPN 接続が確立される間、VPN ゲートウェイによって実行されます。 検証にはルート証明書が必要なため、そのルート証明書を Azure にアップロードする必要があります。

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>ネイティブ Azure Active Directory 認証を使用した認証

Azure AD 認証では、ユーザーは Azure Active Directory 資格情報を使用して、Azure に接続できます。 ネイティブ Azure AD 認証は、OpenVPN プロトコルと Windows 10 のみでサポートされており、[Azure VPN クライアント](https://go.microsoft.com/fwlink/?linkid=2117554)を使用する必要があります。

ネイティブ Azure AD 認証を使用すると、Azure AD の条件付きアクセスに加えて、VPN 用の Multi-Factor Authentication (MFA) 機能を利用できます。

大まかに言えば、Azure AD 認証を構成するには、次の手順を実行する必要があります。

1. [Azure AD テナントを構成する](openvpn-azure-ad-tenant.md)

2. [ゲートウェイでの Azure AD 認証を有効にする](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Azure VPN クライアントをダウンロードして構成する](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Active Directory (AD) ドメイン サーバーを使用した認証

AD ドメイン認証では、ユーザーは組織のドメイン資格情報を使用して Azure に接続できます。 これには AD サーバーと統合する RADIUS サーバーが必要です。 また、組織は既存の RADIUS デプロイを利用することもできます。

RADIUS サーバーは、オンプレミスまたは Azure VNet にデプロイできます。 認証が行われる間、Azure VPN ゲートウェイがパススルーとして機能し、接続するデバイスと RADIUS サーバーの間で認証メッセージを転送します。 そのため、ゲートウェイが RADIUS サーバーにアクセスできることが重要です。 RADIUS サーバーがオンプレミスに存在する場合、アクセスのために、Azure からオンプレミス サイトへの VPN サイト間接続が必要になります。

また、RADIUS サーバーは、AD 証明書サービスとも統合できます。 これにより、Azure 証明書認証の代替手段として、P2S 証明書認証に RADIUS サーバーとエンタープライズ証明書デプロイを使用できます。 この利点は、ルート証明書と失効した証明書を Azure にアップロードする必要がないことです。

RADIUS サーバーは、他の外部 ID システムと統合することもできます。 これにより、多要素認証のオプションなど、P2S VPN 向けの多数の認証オプションを利用できるようになります。

![オンプレミス サイトを使用したポイント対サイト VPN を示す図。](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>クライアントの構成要件について

>[!NOTE]
>Windows クライアントの場合、クライアント デバイスから Azure に VPN 接続を開始するには、クライアント デバイスで管理権限が必要です。
>

ユーザーは、P2S 用の Windows デバイスと Mac デバイスでネイティブ VPN クライアントを使用します。 これらのネイティブ クライアントが Azure に接続するために必要な設定が含まれた VPN クライアント構成 zip ファイルは、Azure に用意されています。

* Windows デバイスの場合、VPN クライアント構成は、ユーザーがデバイスにインストールするインストーラー パッケージで構成されています。
* Mac デバイスの場合、これは、ユーザーがデバイスにインストールする mobileconfig ファイルで構成されています。

zip ファイルでは、Azure 側のいくつかの重要な設定の値も指定されており、この値を使用することで、これらのデバイス用に独自のプロファイルを作成できます。 このような値には、VPN ゲートウェイ アドレス、構成されたトンネルの種類、ルート、ゲートウェイ検証用のルート証明書などがあります。

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>P2S VPN がサポートされるゲートウェイ SKU の種類

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* ゲートウェイ SKU の推奨事項については、[VPN Gateway の設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)に関するページを参照してください。

>[!NOTE]
>Basic SKU では、IKEv2 と RADIUS 認証はサポートされません。
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>P2S 用に VPN ゲートウェイではどの IKE/IPsec ポリシーが構成されていますか。


**IKEv2**

| **暗号化** | **整合性** | **PRF** | **DH グループ** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **暗号化** | **整合性** | **PFS グループ** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S 用に VPN ゲートウェイではどの TLS ポリシーが構成されていますか。
**TLS**

|**ポリシー** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>P2S 接続の構成方法

P2S 構成で必要な手順には、特有のものが非常に多くあります。 次の記事では、P2S 構成の詳細な手順と、VPN クライアント デバイスの構成方法のリンクをご紹介します。

* [P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

* [P2S 接続の構成 - Azure ネイティブ証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN の構成](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>P2S 接続の構成を削除するには

PowerShell または CLI を使用して、接続の構成を削除できます。 例については、「[FAQ](vpn-gateway-vpn-faq.md#removeconfig)」を参照してください。

## <a name="how-does-p2s-routing-work"></a>P2S ルーティングのしくみ

次の記事をご覧ください。

* [ポイント対サイト VPN ルーティングについて](vpn-gateway-about-point-to-site-routing.md)

* [カスタム ルートを公開するには](vpn-gateway-p2s-advertise-custom-routes.md)

## <a name="faqs"></a>FAQ

認証に基づく P2S の FAQ セクションが複数あります。

* [FAQ - 証明書の認証](vpn-gateway-vpn-faq.md#P2S)

* [FAQ - RADIUS の認証](vpn-gateway-vpn-faq.md#P2SRADIUS)
 
## <a name="next-steps"></a>次の手順

* [P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

* [P2S 接続の構成 - Azure 証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" は OpenVPN Inc. の商標です。**
