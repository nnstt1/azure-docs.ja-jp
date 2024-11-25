---
title: Azure Active Directory アプリケーション プロキシのネットワーク トポロジに関する注意事項
description: Azure Active Directory アプリケーション プロキシを使用する場合のネットワーク トポロジに関する注意事項について説明します。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 5f11448b88a84b6b61d387e2251fab96e9a3bad0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989192"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシを使用してトラフィック フローを最適化する

この記事では、アプリケーションをリモートで発行およびアクセスするために Azure Active Directory (Azure AD) アプリケーション プロキシを使用する場合のトラフィック フローを最適化する方法およびネットワーク トポロジに関する注意事項について説明しています。

## <a name="traffic-flow"></a>トラフィック フロー

アプリケーションが Azure AD アプリケーション プロキシを通じて発行される場合、ユーザーからアプリケーションへのトラフィックはすべて、次の 3 つの接続を経由して送信されます。

1. ユーザーから Azure AD アプリケーション プロキシ サービスのパブリック エンドポイントへの接続
1. アプリケーション プロキシ コネクタからアプリケーション プロキシ サービスへの接続 (アウトバウンド)
1. アプリケーション プロキシ コネクタから対象アプリケーションへの接続

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="ユーザーから対象アプリケーションへのトラフィック フローを示す図。" lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>最も近いアプリケーション プロキシ クラウド サービスを使用するようにコネクタ グループを最適化する (プレビュー)

Azure AD テナントにサインアップするとき、指定した国/地域によってテナントのリージョンが決まります。 アプリケーション プロキシを有効にすると、テナント用の **既定の** アプリケーション プロキシ クラウド サービス インスタンスは、Azure AD テナントと同じリージョン、またはその最寄りのリージョン内で選択されます。

たとえば、Azure AD テナントの国またはリージョンが英国の場合、**既定の** すべてのアプリケーション プロキシ コネクタは、ヨーロッパのデータ センターにあるサービス インスタンスを使用するように割り当てられます。 ユーザーが公開アプリケーションにアクセスする場合、トラフィックはこの場所のアプリケーション プロキシ クラウド サービス インスタンスを経由して送信されます。

既定のリージョンとは異なるリージョンにコネクタがインストールされている場合、これらのアプリケーションにアクセスするパフォーマンスを向上させるために、コネクタ グループが最適化されているリージョンを変更すると有益な場合があります。 コネクタ グループに対してリージョンを指定すると、指定したリージョンのアプリケーション プロキシ クラウド サービスに接続されます。

トラフィック フローを最適化し、コネクタ グループの待機時間を短縮するために、コネクタ グループを最も近いリージョンに割り当てます。 リージョンを割り当てるには、次の操作を行います。

> [!IMPORTANT]
> この機能を使用するには、バージョン 1.5.1975.0 以降のコネクタが使用されている必要があります。

1. アプリケーション プロキシを使用するディレクトリのアプリケーション管理者として、[Azure portal](https://portal.azure.com/) にサインインします。 たとえば、テナントのドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他の管理者エイリアスであることが必要です。
1. 右上隅で自分のユーザー名を選択します。 アプリケーション プロキシを使用するディレクトリにサインインしていることを確認します。 ディレクトリを変更する必要がある場合は、 **[ディレクトリの切り替え]** を選択し、アプリケーション プロキシを使用するディレクトリを選択します。
1. 左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
1. **[管理]** で、 **[アプリケーション プロキシ]** を選択します。
1. **[新しいコネクタ グループ]** を選択し、コネクタ グループの **[名前]** を指定します。
1. 次に、 **[詳細設定]** の下で、[特定のリージョン用に最適化] の下にあるドロップダウンを選択し、コネクタに最も近いリージョンを選択します。
1. **［作成］** を選択します
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="新しいコネクタ グループを構成する。" lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. 新しいコネクタ グループを作成したら、このコネクタ グループに割り当てるコネクタを選択できます。 
   - コネクタ グループにコネクタを移動できるのは、既定のリージョンを使用するコネクタ グループにそれが属している場合のみです。 最適な方法は、最初は常に "既定のグループ" にコネクタを配置し、それを適切なコネクタ グループに移動することです。
   - コネクタ グループのリージョンを変更できるのは、コネクタ グループに割り当てられているコネクタおよびアプリが **ない** 場合のみです。
1. 次に、コネクタ グループをアプリケーションに割り当てます。 これで、アプリにアクセスするときに、トラフィックはコネクタ グループが最適化されているリージョンのアプリケーション プロキシ クラウド サービスに送信されます。

## <a name="considerations-for-reducing-latency"></a>待機時間を削減するための考慮事項

プロキシ ソリューションでは、ネットワーク接続に必ず待ち時間が発生します。 リモート アクセス ソリューションとして選択したプロキシまたは VPN ソリューションに関係なく、ソリューションには、企業ネットワーク内部に接続できるようにする一連のサーバーが必ず含まれます。

多くの組織では、サーバーのエンドポイントが境界ネットワーク内に含まれています。 しかし、Azure AD では、コネクタが企業ネットワーク上に配置されていてもトラフィックはクラウド内のプロキシ サービスを経由して送信されます。 このため、境界ネットワークは必要ありません。

次のセクションで、待機時間をさらに削減するための推奨事項を示します。 

### <a name="connector-placement"></a>コネクタの配置

アプリケーション プロキシでは、テナントの場所に基づいてインスタンスの場所が自動的に選択されます。 ただし、コネクタのインストール場所は自分で決定できます。これにより、ネットワーク トラフィックの待機時間特性を定義できます。

アプリケーション プロキシ サービスを設定する場合は、次の質問について考えてください。

- アプリケーションの場所はどこか。
- アプリケーションにアクセスする大半のユーザーの場所はどこか。
- アプリケーション プロキシ インスタンスの場所はどこか。
- Azure データセンターへの専用のネットワーク接続 (Azure ExpressRoute や類似の VPN など) が既に設定されているかどうか。

コネクタは Azure とアプリケーションの両方と通信する必要があるため (トラフィック フロー図の手順 2 と 3)、コネクタの配置場所はこれら 2 つの接続の待機時間に影響します。 コネクタの配置を評価する際は、次の点に留意してください。

- シングル サインオンに Kerberos の制約付き委任 (KCD) を使用する場合、コネクタからデータセンターへの通信経路が必要になります。 さらに、コネクタ サーバーはドメインに参加している必要があります。  
- 使用するかどうかが不明な場合は、アプリケーションの近くにコネクタをインストールします。

### <a name="general-approach-to-minimize-latency"></a>待機時間を最小限に抑えるための一般的なアプローチ

各ネットワーク接続を最適化することによって、エンド ツー エンドのトラフィック待機時間を最小化することができます。 各接続は次の方法で最適化できます。

- ホップの両端間の距離を短縮します。
- 経由する適切なネットワークを選択します。 たとえば、パブリック インターネットではなく、プライベート ネットワークを経由した方が、専用リンクのため高速である可能性があります。

Azure と企業ネットワークの間に専用の VPN または ExpressRoute リンクがある場合は、これを利用することをお勧めします。

## <a name="focus-your-optimization-strategy"></a>最適化戦略に焦点を合わせる

ユーザーとアプリケーション プロキシ サービス間の接続を制御するために実行できることはほとんどありません。 ユーザーは、ホーム ネットワーク、コーヒー ショップ、または別の国/地域からアプリにアクセスする可能性があります。 代わりに、アプリケーション プロキシ サービスからアプリケーション プロキシ コネクタへの接続とアプリケーション プロキシ コネクタからアプリへの接続を最適化できます。 環境内に次のパターンを組み込むことを検討してください。

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>パターン 1: アプリケーションの近くへのコネクタの配置

顧客のネットワーク内の対象アプリケーションの近くにコネクタを配置します。 この構成では、コネクタとアプリケーションが閉じているため、トポロジ図の手順 3 が最小限に抑えられます。

コネクタがドメイン コントローラーを見通す必要がある場合は、このパターンが得策です。 このパターンはほとんどのシナリオに適しているため、大半のユーザーが使用しています。 このパターンとパターン 2 を組み合わせて、サービスとコネクタ間のトラフィックを最適化することもできます。

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>パターン 2: Microsoft ピアリングを使用した ExpressRoute の活用

Microsoft ピアリングを使用して ExpressRoute が設定されている場合、アプリケーション プロキシとコネクタ間のトラフィック用により高速な ExpressRoute 接続を利用できます。 この場合でも、コネクタはネットワーク上のアプリケーションに近い場所に配置します。

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>パターン 3: プライベート ピアリングを使用した ExpressRoute の活用

Azure と企業ネットワークとの間に、プライベート ピアリングを使用して専用の VPN または ExpressRoute を設定済みの場合、別の選択肢があります。 この構成では、通常、Azure の仮想ネットワークは企業ネットワークの拡張機能と見なされます。 そのため、Azure データセンターにコネクタをインストールする一方で、コネクタからアプリへの接続について厳しい待ち時間の要件を満たすことができます。

専用接続を介して送信されるため、待機時間は損なわれません。 さらに、コネクタが Azure AD テナントに近い Azure データセンターへとインストールされるため、アプリケーション プロキシ サービスからコネクタへの接続待ち時間も改善されます。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="Azure データ センター内にインストールされたコネクタを示す図" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

### <a name="other-approaches"></a>その他のアプローチ

この記事ではコネクタの配置を中心に取り上げていますが、アプリケーションの配置を変更して待ち時間特性を改善することもできます。

ホスト環境にネットワークを移動する組織が増えています。 これにより組織は、企業ネットワークの一部でもあり、ドメイン内のままであるホスト環境にアプリを配置できます。 この場合は、前のセクションで説明したパターンをアプリケーションの新しい場所に適用できます。 このオプションを検討する場合は、[Azure AD Domain Services](../../active-directory-domain-services/overview.md) に関するページをご覧ください。

さらに、別々の場所やネットワーク内にある対象アプリケーションに接続するコネクタを、[コネクタ グループ](application-proxy-connector-groups.md)を使用して整理することを検討してください。

## <a name="common-use-cases"></a>一般的なユース ケース

このセクションでは、いくつかの一般的なシナリオについて説明します。 ここでは、Azure AD テナント (およびそれに伴うプロキシ サービス エンドポイント) が米国 (US) 内にあると仮定しています。 これらのユース ケースで説明されている考慮事項は、世界各地の他のリージョンにも当てはまります。

以下のシナリオでは、説明をわかりやすくするため、各接続に番号を付けて "ホップ" と呼びます。

- **ホップ 1**: ユーザーからアプリケーション プロキシ サービスへ
- **ホップ 2**: アプリケーション プロキシ サービスからアプリケーション プロキシ コネクタへ
- **ホップ 3**: アプリケーション プロキシ コネクタからターゲット アプリケーションへ 

### <a name="use-case-1"></a>ユース ケース 1

**シナリオ:** アプリは米国内の組織のネットワークにあり、ユーザーは同じリージョン内に存在している。 Azure データセンターと企業ネットワークの間に ExpressRoute または VPN は存在しません。

**推奨事項:** 前のセクションで説明したパターン 1 に従います。 待ち時間を改善するには、必要に応じて ExpressRoute の使用を検討してください。

これは、単純なパターンです。 アプリの近くにコネクタを配置することによって、ホップ 3 を最適化します。 コネクタは、KCD 操作を実行するために、通常、アプリとデータセンターとの通信経路にインストールされるため、これは自然な選択でもあります。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="ユーザー、プロキシ、コネクタ、アプリケーションがすべて米国内に存在することを示す図。" lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>ユース ケース 2

**シナリオ:** アプリは米国内の組織のネットワークにあり、ユーザーは世界中に分散している。 Azure データセンターと企業ネットワークの間に ExpressRoute または VPN は存在しません。

**推奨事項:** 前のセクションで説明したパターン 1 に従います。

繰り返しますが、一般的なパターンはホップ 3 の最適化です。これにより、コネクタはアプリの近くに配置されます。 ホップ 3 は、すべて同じリージョン内にある場合、通常、コストは高くありません。 ただし、世界各地のユーザーが米国内のアプリケーション プロキシ インスタンスにアクセスするため、ユーザーの場所によってはホップ 1 のコストが高くなる可能性があります。 どのプロキシ ソリューションも、グローバルに分散しているユーザーに関して類似した特性を持つことは注目に値します。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="ユーザーは世界中に分散していますが、それ以外はすべて米国に存在します" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>ユース ケース 3

**シナリオ:** アプリは米国内の組織のネットワークにある。 Microsoft ピアリングを使用した ExpressRoute が、Azure と企業ネットワークの間に存在します。

**推奨事項:**: 前のセクションで説明したパターン 1 とパターン 2 に従います。

最初に、コネクタを可能な限りアプリケーションの近くに配置します。 これで、ホップ 2 には ExpressRoute が自動的に使用されます。

ExpressRoute リンクで Microsoft ピアリングを使用している場合、プロキシとコネクタの間のトラフィックはそのリンク経由で送信されます。 ホップ 2 の待ち時間が最適化されます。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="プロキシとコネクタ間の ExpressRoute を示す図" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>ユース ケース 4

**シナリオ:** アプリは米国内の組織のネットワークにある。 プライベート ピアリングを使用した ExpressRoute が、Azure と企業ネットワークの間に存在します。

**推奨事項:**: 前のセクションで説明したパターン 3 に従います。

ExpressRoute プライベート ピアリング経由で企業ネットワークに接続されている Azure データセンターにコネクタを配置します。

コネクタは、Azure データセンターに配置できます。 プライベート ネットワークを経由しても、コネクタからアプリケーションとデータセンターへの通信経路は保たれるため、ホップ 3 は最適化されたままになります。 加えて、ホップ 2 はさらに最適化されます。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Azure データセンターのコネクタ、コネクタとアプリの間の ExpressRoute" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>ユース ケース 5

**シナリオ:** このアプリはヨーロッパの組織のネットワークにあり、既定のテナント リージョンは米国で、ほとんどのユーザーはヨーロッパにいます。

**推奨事項:** コネクタをアプリケーションの近くに配置します。 コネクタ グループを更新して、ヨーロッパのアプリケーション プロキシ サービス インスタンスを使用するように最適化します。 手順については、「[最も近いアプリケーション プロキシ クラウド サービスを使用するようにコネクタ グループを最適化する](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)」を参照してください。

ヨーロッパのユーザーは、同じリージョンのアプリケーション プロキシ インスタンスにアクセスしているため、ホップ 1 のコストは高くありません。 ホップ 3 は最適化されています。 ExpressRoute を使用して ホップ 2 を最適化することを検討してください。

### <a name="use-case-6"></a>ユース ケース 6

**シナリオ:** このアプリはヨーロッパの組織のネットワークにあり、既定のテナント リージョンは米国で、ほとんどのユーザーは米国にいます。

**推奨事項:** コネクタをアプリケーションの近くに配置します。 コネクタ グループを更新して、ヨーロッパのアプリケーション プロキシ サービス インスタンスを使用するように最適化します。 手順については、「[最も近いアプリケーション プロキシ クラウド サービスを使用するようにコネクタ グループを最適化する](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)」を参照してください。 米国のすべてのユーザーがヨーロッパのアプリケーション プロキシ インスタンスにアクセスする必要があるため、ホップ 1 はコストが高くなる可能性があります。

このような状況では、もう 1 つのバリエーションを使用することも検討できます。 組織内のほとんどのユーザーが米国内に存在する場合、ネットワークが米国まで拡張される可能性があります。 コネクタを米国内に配置し、コネクタ グループについては既定の米国リージョンをそのまま使用し、ヨーロッパ内のアプリケーションには専用の社内ネットワーク回線を使用します。 これにより、ホップ 2 と 3 が最適化されます。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="ユーザー、プロキシ、コネクタは米国内に、アプリケーションはヨーロッパ内に存在することを示す図。" lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>次のステップ

- [アプリケーション プロキシを有効にする](application-proxy-add-on-premises-application.md)
- [シングル サインオンを有効にする](application-proxy-configure-single-sign-on-with-kcd.md)
- [条件付きアクセスを有効にする](./application-proxy-integrate-with-sharepoint-server.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](application-proxy-troubleshoot.md)
