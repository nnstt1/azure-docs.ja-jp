---
title: Azure HDInsight で使用されるネットワーク セキュリティ グループ (NSG) サービス タグ
description: NSG に IP アドレスを追加することなく、正常性と管理サービス ノードからクラスターへのインバウンド トラフィックを許可するには、HDInsight のサービス タグを使用します。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/07/2021
ms.author: guyhay
ms.openlocfilehash: f85ad29b11e45f5e906bfbf50321c86d16670f89
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004353"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight の NSG サービス タグ

ネットワーク セキュリティ グループ (NSG) 用の Azure HDInsight サービス タグは、正常性および管理サービスのための IP アドレスのグループです。 これらのグループを使用すると、セキュリティ規則の作成の複雑さを最小限に抑えることができます。 [サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)を使用すると、NSG に[管理 IP アドレス](hdinsight-management-ip-addresses.md)を個別に入力することなく、特定の IP からのインバウンド トラフィックを許可することができます。

これらのサービス タグは、HDInsight サービスによって管理されます。 独自のサービス タグを作成したり、既存のタグを変更したりすることはできません。 サービス タグと一致するアドレス プレフィックスの管理は Microsoft によって行われ、アドレスが変化するとサービス タグは自動的に更新されます。

特定のリージョンを使用する場合に、サービス タグがまだこのページに記載されていない場合は、[Service Tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) を使用してサービス タグを検索できます。 また、[サービス タグの JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)をダウンロードして、目的のリージョンを検索することもできます。

## <a name="get-started-with-service-tags"></a>サービス タグを使ってみる

ネットワーク セキュリティ グループでサービスタ グを使用するには、次の 2 つのオプションがあります。

- **単一のグローバル HDInsight サービス タグを使用する**: このオプションでは、すべてのリージョンでクラスターを監視するために HDInsight サービスによって使用されているすべての IP アドレスに対して、仮想ネットワークが開放されます。 このオプションは最も簡単な方法ですが、セキュリティ要件の制限がある場合は適さないことがあります。

- **複数のリージョン サービス タグを使用する**: このオプションでは、HDInsight がその特定のリージョンで使用する IP アドレスに対してのみ、仮想ネットワークが開放されます。 ただし、複数のリージョンを使用している場合は、複数のサービス タグを仮想ネットワークに追加する必要があります。

## <a name="use-a-single-global-hdinsight-service-tag"></a>単一のグローバル HDInsight サービス タグを使用する

HDInsight クラスターでサービス タグを使い始める最も簡単な方法は、NSG の規則にグローバル タグ `HDInsight` を追加することです。

1. [[Azure portal]](https://portal.azure.com/) で、使用しているネットワーク セキュリティ グループを選択します。

1. **[設定]** で **[受信セキュリティ規則]** を選択し、 **[+ 追加]** を選択します。

1. **[ソース]** ドロップダウン リストで、 **[サービス タグ]** を選択します。

1. **[発信元サービス タグ]** ドロップダウン リストから、 **[HDInsight]** を選択します。

    :::image type="content" source="./media/hdinsight-service-tags/azure-portal-add-service-tag.png" alt-text="Azure portal からサービス タグを追加する":::

このタグには、HDInsight が使用可能なすべてのリージョンの正常性および管理サービスの IP アドレスが含まれています。 このタグでは、クラスターが作成された場所に関係なく、必要な正常性および管理サービスと通信できることが保証されます。

## <a name="use-regional-hdinsight-service-tags"></a>リージョンごとの HDInsight サービス タグを使用する

より制限の厳しいアクセス許可が必要であるため、グローバル タグのオプションでは機能しない場合は、ご利用のリージョンに適用されるサービス タグのみを許可することができます。 クラスターが作成されるリージョンによっては、複数のサービス タグになる可能性があります。

リージョンに追加されるサービス タグを確認するには、以降のセクションを参照してください。

### <a name="use-a-single-regional-service-tag"></a>1 つのリージョン サービス タグを使用する

ご利用のクラスターがこの表に一覧表示されているリージョンにある場合、NSG に追加する必要があるリージョン サービス タグは 1 つのみです。

| Country | リージョン | サービス タグ |
| ---- | ---- | ---- |
| オーストラリア | オーストラリア東部 | HDInsight.AustraliaEast |
| &nbsp; | オーストラリア南東部 | HDInsight.AustraliaSoutheast |
| &nbsp; | オーストラリア中部 | HDInsight.AustraliaCentral |
| アジア | 東アジア | HDInsight.EastAsia |
| &nbsp; | 東南アジア | HDInsight.SoutheastAsia |
| ブラジル | ブラジル南部 | HDInsight.BrazilSouth |
| &nbsp; | ブラジル南東部 | HDInsight.BrazilSoutheast |
| 中国 | 中国東部 2 | HDInsight.ChinaEast2 |
| &nbsp; | 中国北部 2 | HDInsight.ChinaNorth2 |
<<<<<<< HEAD
| アメリカ | 米国中北部 | HDInsight.NorthCentralUS |
| &nbsp; | 米国西部 2 | HDInsight.WestUS2 |
| &nbsp; | 米国中西部 | HDInsight.WestCentralUS |
| カナダ | カナダ東部 | HDInsight.CanadaEast |
| ブラジル | ブラジル南部 | HDInsight.BrazilSouth |
=======
| 日本 | 東日本 | HDInsight.JapanEast |
| &nbsp; | 西日本 | HDInsight.JapanWest |
>>>>>>> repo_sync_working_branch
| 韓国 | 韓国中部 | HDInsight.KoreaCentral |
| &nbsp; | 韓国南部 | HDInsight.KoreaSouth |
| インド | インド中部 | HDInsight.CentralIndia |
| &nbsp; | JIO インド西部 | HDInsight.JioIndiaWest |
| &nbsp; | インド南部 | HDInsight.SouthIndia |
| 南アフリカ | 南アフリカ北部 | HDInsight.SouthAfricaNorth |
| UAE | アラブ首長国連邦北部 | HDInsight.UAENorth |
| &nbsp; | アラブ首長国連邦中部 | HDInsight.UAECentral |
| ヨーロッパ | 北ヨーロッパ | HDInsight.NorthEurope |
| &nbsp; | 西ヨーロッパ | HDInsight.WestEurope |
| フランス | フランス中部| HDInsight.FranceCentral |
| ドイツ | ドイツ中西部| HDInsight.GermanyWestCentral |
| ノルウェー | ノルウェー東部 | HDInsight.NorwayEast |
| スウェーデン | スウェーデン中部 | HDInsight.SwedenCentral |
| &nbsp; | スウェーデン南部 | HDInsight.SwedenSouth |
| スイス | スイス北部 | HDInsight.SwitzerlandNorth |
| &nbsp; | スイス西部 | HDInsight.SwitzerlandWest |
| 英国 | 英国南部 | HDInsight.UKSouth |
| &nbsp; | 英国西部 | HDInsight.UKWest |
| United States | 米国中北部 | HDInsight.NorthCentralUS |
| &nbsp; | 米国中部 | HDInsight.CentralUS |
| &nbsp; | 米国西部 2 | HDInsight.WestUS2 |
| &nbsp; | 米国西部 3 | HDInsight.WestUS3 |
| &nbsp; | 米国中西部 | HDInsight.WestCentralUS |
| Canada | カナダ東部 | HDInsight.CanadaEast |
| &nbsp; | カナダ中部 | HDInsight.CanadaCentral |
| Azure Government | USDoD 中部 | HDInsight.USDoDCentral |
| &nbsp; | USGov テキサス | HDInsight.USGovTexas |
| &nbsp; | USDoD 東部 | HDInsight.USDoDEast |
| &nbsp; | USGov アリゾナ | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>複数のリージョン サービス タグを使用する

クラスターを作成したリージョンが前の表に一覧表示されていない場合は、複数のリージョン サービス タグを許可する必要があります。 複数を使用する必要があるのは、さまざまなリージョンによってリソース プロバイダーの配置に違いがあるためです。

残りのリージョンは、使用されるリージョン サービス タグに基づいてグループに分割されます。

#### <a name="group-1"></a>グループ 1

次の表のいずれかのリージョンにクラスターが作成されている場合は、サービス タグ `HDInsight.WestUS` と `HDInsight.EastUS` を許可します。 また、リージョン サービス タグも一覧されています。 このセクションのリージョンには、3 つのサービス タグが必要です。

たとえば、クラスターが `East US 2` リージョンに作成される場合は、次のサービス タグをネットワーク セキュリティ グループに追加する必要があります。

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | リージョン | サービス タグ |
| ---- | ---- | ---- |
| アメリカ | 米国東部 2 | HDInsight.EastUS2 |
| &nbsp; | 米国中部 | HDInsight.CentralUS |
| &nbsp; | 米国中北部 | HDInsight. NorthCentralUS |
| &nbsp; | 米国中南部 | HDInsight.SouthCentralUS |
| &nbsp; | 米国東部 | HDInsight.EastUS |
| &nbsp; | 米国西部 | HDInsight.WestUS |
| 日本 | 東日本 | HDInsight.JapanEast |
| ヨーロッパ | 北ヨーロッパ | HDInsight.NorthEurope |
| &nbsp; | 西ヨーロッパ| HDInsight.WestEurope |
| アジア | 東アジア | HDInsight.EastAsia |
| &nbsp; | 東南アジア | HDInsight.SoutheastAsia |
| オーストラリア | オーストラリア東部 | HDInsight.AustraliaEast |

#### <a name="group-2"></a>グループ 2

"*中国北部*" および "*中国東部*" リージョンのクラスターでは、2 つのサービス タグ `HDInsight.ChinaNorth` と `HDInsight.ChinaEast` を許可する必要があります。

#### <a name="group-3"></a>グループ 3

"*US Gov アイオワ*" および "*US Gov バージニア*" リージョンのクラスターでは、2 つのサービス タグ `HDInsight.USGovIowa` と `HDInsight.USGovVirginia` を許可する必要があります。

#### <a name="group-4"></a>グループ 4

"*ドイツ中部*" および "*ドイツ北東部*" リージョンのクラスターでは、2 つのサービス タグ `HDInsight.GermanyCentral` と `HDInsight.GermanyNortheast` を許可する必要があります。

## <a name="next-steps"></a>次のステップ

- [ネットワーク セキュリティ グループ: サービス タグ](../virtual-network/network-security-groups-overview.md#security-rules)
- [Azure HDInsight クラスターの仮想ネットワークの作成](hdinsight-create-virtual-network.md)
