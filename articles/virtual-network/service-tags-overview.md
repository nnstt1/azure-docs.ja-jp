---
title: Azure サービス タグの概要
titlesuffix: Azure Virtual Network
description: サービス タグについて確認します。 サービス タグを使用すると、セキュリティ規則の作成の複雑さを最小限に抑えることができます。
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2affaf7b66c1a7872e1db2b92871c5d07e77cdd2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324633"
---
# <a name="virtual-network-service-tags"></a>仮想ネットワーク サービス タグ
<a name="network-service-tags"></a>

サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。これにより、ネットワーク セキュリティ規則に対する頻繁な更新の複雑さを最小限に抑えられます。

サービス タグを使用して、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義できます。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用します。 規則の適切な *ソース* または *宛先* フィールドにサービス タグ名 (**ApiManagement** など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 

> [!NOTE] 
> 2021 年 3 月の時点で、[ユーザー定義のルート](./virtual-networks-udr-overview.md)において、明示的な IP 範囲の代わりにサービス タグを使用することもできます。 現在、この機能はパブリック プレビュー段階にあります。 

サービス タグを使用すると、ネットワーク分離を実現し、パブリック エンドポイントを持つ Azure サービスへのアクセス時に一般のインターネットから Azure リソースを保護できます。 受信/送信ネットワーク セキュリティ グループ規則を作成して、**インターネット** との間のトラフィックを拒否し、**AzureCloud** または他の特定の Azure サービスの [利用可能なサービス タグ](#available-service-tags)との間のトラフィックを許可します。

![サービス タグを使用した Azure サービスのネットワーク分離](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>利用可能なサービス タグ
次の表には、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md#security-rules)の規則で使用できるすべてのサービス タグが含まれています。

各列は、タグが次を満たすかどうかを示しています。

- 受信または送信トラフィックを扱う規則に適している。
- [リージョン](https://azure.microsoft.com/regions) スコープをサポートしている。
- [Azure Firewall](../firewall/service-tags.md) 規則で使用可能である。

既定では、サービス タグにはクラウド全体の範囲が反映されます。 サービス タグによっては、対応する IP 範囲を指定されたリージョンに制限することで、より詳細な制御を実現することもできます。 たとえば、サービス タグ **Storage** はクラウド全体の Azure Storage を表していますが、**Storage.WestUS** を使用すると、WestUS リージョンのストレージ IP アドレスのみに範囲が限定されます。 次の表に、各サービス タグでそのようなリージョン スコープがサポートされるかどうかを示します。 各タグに示されている指示が推奨事項であることに注意してください。 たとえば、AzureCloud タグを使用して受信トラフィックを許可することができます。 ただし、この方法はほとんどのシナリオで推奨されません。これは、他の Azure ユーザーが使用するトラフィックを含め、すべての Azure IP からのトラフィックを許可するためです。 

| タグ | 目的 | 受信または送信で使用できるか | リージョン別か | Azure Firewall と共に使用できるか |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | アクション グループ。 | 受信 | いいえ | いいえ |
| **ApiManagement** | Azure API Management 専用デプロイのための管理トラフィック。 <br/><br/>**注**: このタグは、リージョンごとのコントロール プレーンの Azure API Management サービス エンドポイントを表します。 これにより、顧客は API Management サービス上で構成された API、操作、ポリシー、NamedValues に対する管理操作を実行できるようになります。  | 受信 | はい | はい |
| **ApplicationInsightsAvailability** | Application Insights の可用性。 | 受信 | いいえ | いいえ |
| **AppConfiguration** | App Configuration。 | 送信 | いいえ | いいえ |
| **AppService**    | Azure App Service。 このタグは、Web アプリと関数アプリに対して、送信セキュリティ規則で推奨されています。  | 送信 | はい | はい |
| **AppServiceManagement** | App Service Environment 専用デプロイのための管理トラフィック。 | 両方 | いいえ | はい |
| **AzureActiveDirectory** | Azure Active Directory。 | 送信 | いいえ | はい |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory Domain Services 専用デプロイのための管理トラフィック。 | 両方 | いいえ | はい |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection。 | 送信 | いいえ | いいえ |
| **AzureArcInfrastructure** | Azure Arc 対応サーバー、Azure Arc 対応 Kubernetes、ゲスト構成トラフィック。<br/><br/>**注**: このタグは、**AzureActiveDirectory**、**AzureTrafficManager**、**AzureResourceManager** の各タグに依存しています。 現在、このタグは Azure portal で構成することはできません。| 送信 | いいえ | はい |
| **AzureAttestation** | Azure Attestation。<br/><br/>**注**: 現在、このタグを Azure portal で構成することはできません。 | 送信 | いいえ | はい | 
| **AzureBackup** |Azure Backup。<br/><br/>**注**: このタグは、**Storage** と **AzureActiveDirectory** の各タグに依存します。 | 送信 | いいえ | はい |
| **AzureBotService** | Azure Bot Service。 | 送信 | いいえ | いいえ |
| **AzureCloud** | すべての[データセンター パブリック IP アドレス](https://www.microsoft.com/download/details.aspx?id=56519)。 | 送信 | はい | はい |
| **AzureCognitiveSearch** | Azure Cognitive Search。 <br/><br/>このタグまたはこのタグによってカバーされる IP アドレスは、データ ソースへのセキュリティで保護されたアクセスをインデクサーに付与するために使用できます。 詳細については、[インデクサーの接続に関するドキュメント](../search/search-indexer-troubleshooting.md#connection-errors)を参照してください。 <br/><br/> **注**:検索サービスの IP アドレスは、このサービス タグの IP 範囲の一覧に含まれておらず、またデータ ソースの IP ファイアウォール **にも追加される必要があります**。 | 受信 | いいえ | いいえ |
| **AzureConnectors** | このタグは、Azure Logic Apps サービスへの受信 Webhook コールバックと、Azure Storage や Azure Event Hubs などの各サービスへの送信呼び出しを行う、マネージド コネクタに使用される IP アドレスを表します。 | 受信/送信 | はい | はい |
| **AzureContainerRegistry** | Azure Container Registry。 | 送信 | はい | はい |
| **AzureCosmosDB** | Azure Cosmos DB。 | 送信 | はい | はい |
| **AzureDatabricks** | Azure Databricks。 | 両方 | いいえ | いいえ |
| **AzureDataExplorerManagement** | Azure Data Explorer 管理。 | 受信 | いいえ | いいえ |
| **AzureDataLake** | Azure Data Lake Storage Gen1。 | 送信 | いいえ | はい |
| **AzureDeviceUpdate** | Device Update for IoT Hub。 | 両方 | いいえ | はい |
| **AzureDevSpaces** | Azure Dev Spaces。 | 送信 | いいえ | いいえ |
| **AzureDevOps** | Azure Dev Ops。<br/><br/>**注**: 現在、このタグを Azure portal で構成することはできません。| 受信 | いいえ | はい |
| **AzureDigitalTwins** | Azure Digital Twins。<br/><br/>**注**: このタグ、またはこのタグによってカバーされる IP アドレスを使用すると、イベント ルート用に構成されたエンドポイントへのアクセスを制限することができます。 現在、このタグは Azure portal で構成することはできません。 | 受信 | いいえ | はい |
| **AzureEventGrid** | Azure Event Grid。 | 両方 | いいえ | いいえ |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door。 | 両方 | いいえ | いいえ |
| **AzureInformationProtection** | Azure Information Protection。<br/><br/>**注**: このタグは、**AzureActiveDirectory**、**AzureFrontDoor.Frontend**、**AzureFrontDoor.FirstParty** の各タグに依存します。 | 送信 | いいえ | いいえ |
| **AzureIoTHub** | Azure IoT Hub。 | 送信 | はい | いいえ |
| **AzureKeyVault** | Azure Key Vault。<br/><br/>**注**: このタグは、**AzureActiveDirectory** タグに依存します。 | 送信 | はい | はい |
| **AzureLoadBalancer** | Azure インフラストラクチャのロード バランサー。 このタグは、Azure の正常性プローブの送信元となる[ホストの仮想 IP アドレス](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) に変換されます。 これにはプローブ トラフィックのみが含まれ、バックエンド リソースへの実際のトラフィックは含まれません。 Azure Load Balancer を使っていない場合は、この規則をオーバーライドできます。 | 両方 | いいえ | いいえ |
| **AzureMachineLearning** | Azure Machine Learning | 両方 | いいえ | はい |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon、およびカスタム メトリック (GiG エンドポイント)。<br/><br/>**注**: Log Analytics の場合は、**Storage** タグも必要です。 Linux エージェントが使用されている場合は、**GuestAndHybridManagement** タグも必要です。 | 送信 | いいえ | はい |
| **AzureOpenDatasets** | Azure Open Datasets。<br/><br/>**注**: このタグは、**AzureFrontDoor.Frontend** と **Storage** の各タグに依存します。 | 送信 | いいえ | いいえ |
| **AzurePlatformDNS** | 基本インフラストラクチャ (既定) の DNS サービス。<br/><br>このタグを使用すると、既定の DNS を無効にすることができます。 このタグを使用する場合は注意が必要です。 「[Azure プラットフォームに関する考慮事項](./network-security-groups-overview.md#azure-platform-considerations)」を参照することをお勧めします。 また、このタグを使用する前にテストを実行することをお勧めします。 | 送信 | いいえ | いいえ |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS)。これは基本的なインフラストラクチャ サービスです。<br/><br/>このタグを使用すると、既定の IMDS を無効にすることができます。 このタグを使用する場合は注意が必要です。 「[Azure プラットフォームに関する考慮事項](./network-security-groups-overview.md#azure-platform-considerations)」を参照することをお勧めします。 また、このタグを使用する前にテストを実行することをお勧めします。 | 送信 | いいえ | いいえ |
| **AzurePlatformLKM** | Windows のライセンスまたはキー管理サービス。<br/><br/>このタグを使用すると、ライセンスの既定値を無効にすることができます。 このタグを使用する場合は注意が必要です。 「[Azure プラットフォームに関する考慮事項](./network-security-groups-overview.md#azure-platform-considerations)」を参照することをお勧めします。  また、このタグを使用する前にテストを実行することをお勧めします。 | 送信 | いいえ | いいえ |
| **AzureResourceManager** | Azure Resource Manager。 | 送信 | いいえ | いいえ |
| **AzureSignalR** | Azure SignalR。 | 送信 | いいえ | いいえ |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>**注**: このタグは、**AzureActiveDirectory**、**AzureKeyVault**、**EventHub**、**GuestAndHybridManagement**、**Storage** の各タグに依存します。 | 送信 | いいえ | いいえ |
| **AzureSphere** | このタグ、またはこのタグによってカバーされる IP アドレスを使用すると、Azure Sphere Security Services へのアクセスを制限することができます。 </br> **注**: 現在、このタグを Azure portal で構成することはできません。 | 両方 | いいえ | はい | 
| **AzureStack** | Azure Stack Bridge サービス。 </br> このタグは、リージョンごとの Azure Stack Bridge サービス エンドポイントを表します。 </br> *注: 現在、このタグを Azure portal で構成することはできません。* | 送信 | いいえ | はい |
| **AzureTrafficManager** | Azure Traffic Manager プローブ IP アドレス。<br/><br/>Traffic Manager プローブ IP アドレスについて詳しくは、「[Traffic Manager についてよく寄せられる質問 (FAQ)](../traffic-manager/traffic-manager-faqs.md)」をご覧ください。 | 受信 | いいえ | はい |  
| **AzureUpdateDelivery** | Windows Update へのアクセス用。 <br/><br/>**注**: このタグにより、Windows Update メタデータ サービスへのアクセスが提供されます。 更新プログラムを正常にダウンロードするには、**AzureFrontDoor.FirstParty** サービス タグも有効にして、次のように定義されたプロトコルとポートを使用してアウトバウンド セキュリティ規則を構成する必要があります。 <ul><li>AzureUpdateDelivery: TCP、ポート 443</li><li>AzureFrontDoor.FirstParty: TCP、ポート 80</li></ul>*現在、このタグを Azure portal で構成することはできません*| 送信 | いいえ | いいえ |  
| **BatchNodeManagement** | Azure Batch 専用デプロイのための管理トラフィック。 | 両方 | いいえ | はい |
| **CognitiveServicesManagement** | Azure Cognitive Services のトラフィックのアドレス範囲。 | 両方 | いいえ | いいえ |
| **DataFactory**  | Azure Data Factory | 両方 | いいえ | いいえ |
| **DataFactoryManagement** | Azure Data Factory の管理トラフィック。 | 送信 | いいえ | いいえ |
| **Dynamics365ForMarketingEmail** | Dynamics 365 のマーケティング電子メール サービスのアドレス範囲。 | 送信 | はい | いいえ |
| **EOPExternalPublishedIPs** | このタグは、セキュリティ/コンプライアンス センター PowerShell に使用される IP アドレスを表します。 詳細については、[EXO V2 モジュールを使用したセキュリティ/コンプライアンス センター PowerShell への接続](/powershell/exchange/connect-to-scc-powershell)に関するページを参照してください。 <br/><br/> **注**: 現在、このタグを Azure portal で構成することはできません。 | 両方 | いいえ | はい |
| **EventHub** | Azure Event Hubs。 | 送信 | はい | はい |
| **GatewayManager** | Azure VPN Gateway と Application Gateway 専用デプロイのための管理トラフィック。 | 受信 | いいえ | いいえ |
| **GuestAndHybridManagement** | Azure Automation とゲスト構成。 | 送信 | いいえ | はい |
| **HDInsight** | Azure HDInsight。 | 受信 | はい | いいえ |
| **Internet** | パブリック インターネットによってアクセスできる仮想ネットワークの外部の IP アドレス空間。<br/><br/>このアドレス範囲には、[Azure によって所有されているパブリック IP アドレス空間](https://www.microsoft.com/download/details.aspx?id=41653)が含まれています。 | 両方 | いいえ | いいえ |
| **LogicApps** | Logic Apps。 | 両方 | いいえ | いいえ |
| **LogicAppsManagement** | Logic Apps の管理トラフィック。 | 受信 | いいえ | いいえ |
| **MicrosoftAzureFluidRelay** | このタグは、Azure Microsoft Fluid Relay Server に使用される IP アドレスを表します。 | 送信 | いいえ | いいえ |
| **MicrosoftCloudAppSecurity** | Microsoft Defender for Cloud Apps。 | 送信 | いいえ | いいえ |
| **MicrosoftContainerRegistry** | Microsoft コンテナー イメージ用のコンテナー レジストリ。 <br/><br/>**注**: このタグは **AzureFrontDoor.FirstParty** タグに依存します。 | 送信 | はい | はい |
| **PowerBI** | を公開します。 **注**: 現在、このタグを Azure portal で構成することはできません。 | 両方 | いいえ | いいえ|
| **PowerPlatformInfra** | このタグは、Power Platform サービスをホストするためにインフラストラクチャによって使用される IP アドレスを表します。 **注**: 現在、このタグを Azure portal で構成することはできません。 | 送信 | いいえ | いいえ |
| **PowerQueryOnline** | Power Query Online。 | 両方 | いいえ | いいえ |
| **ServiceBus** | Premium サービス レベルを使用する Azure Service Bus トラフィック。 | 送信 | はい | はい |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>**注**: このタグは、リージョンごとのコントロール プレーンの Service Fabric サービス エンドポイントを表します。 これにより、顧客は VNET から Service Fabric クラスターに対する管理操作を実行できるようになります (エンドポイントの例: https:// westus.servicefabric.azure.com)。 | 両方 | いいえ | いいえ |
| **Sql** | Azure SQL Database、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Database for MariaDB、Azure Synapse Analytics。<br/><br/>**注**: このタグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure SQL Database サービスを表しますが、特定の SQL データベースや SQL サーバーは表しません。 このタグは、SQL マネージド インスタンスには適用されません。 | 送信 | はい | はい |
| **SqlManagement** | SQL 専用デプロイのための管理トラフィック。 | 両方 | いいえ | はい |
| **Storage** | Azure Storage です。 <br/><br/>**注**: このタグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure Storage サービスを表しますが、特定の Azure Storage アカウントは表しません。 | 送信 | はい | はい |
| **StorageSyncService** | ストレージ同期サービス。 | 両方 | いいえ | いいえ |
| **WindowsAdminCenter** | Windows Admin Center バックエンド サービスが Windows Admin Center のお客様のインストールと通信できるようにします。 **注**: 現在、このタグを Azure portal で構成することはできません。 | 送信 | いいえ | はい |
| **WindowsVirtualDesktop** | Windows Virtual Desktop。 | 両方 | いいえ | はい |
| **VirtualNetwork** | 仮想ネットワーク アドレス空間 (仮想ネットワークに対して定義されているすべての IP アドレスの範囲)、すべての接続されたオンプレミスのアドレス空間、[ピアリング](virtual-network-peering-overview.md)された仮想ネットワーク、[仮想ネットワーク ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)に接続された仮想ネットワーク、[ホストの仮想 IP アドレス](./network-security-groups-overview.md#azure-platform-considerations)、および[ユーザーが定義したルート](virtual-networks-udr-overview.md)で使用されるアドレス プレフィックス。 このタグには、既定のルートも含まれる場合もあります。 | 両方 | いいえ | いいえ |

> [!NOTE]
>
> - Azure サービスのサービス タグは、使用される特定のクラウドのアドレス プレフィックスを表します。 たとえば、Azure パブリック クラウドの **Sql** タグ値に対応する基になる IP 範囲は、Azure China クラウドの基になる範囲とは異なります。
>
<<<<<<< HEAD
>| クラシックのスペル | 同等のリソース マネージャー タグ |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |
=======
> - Azure Storage や Azure SQL Database などのサービスの[仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)を実装する場合、Azure はサービスの仮想ネットワーク サブネットへの[ルート](virtual-networks-udr-overview.md#optional-default-routes)を追加します。 ルートのアドレス プレフィックスは、対応するサービス タグと同じアドレス プレフィックスまたは CIDR 範囲になります。
>>>>>>> repo_sync_working_branch


### <a name="tags-supported-in-the-classic-deployment-model"></a>クラシック デプロイ モデルでサポートされるタグ

(Azure Resource Manager の前の) クラシック デプロイ モデルでは、前の表に示したタグのごく一部がサポートされます。 クラシック デプロイ モデルのタグは、次の表に示すように、スペルが異なります。

| Resource Manager のタグ | クラシック デプロイ モデルの対応するタグ |
|---|---|
| **AzureLoadBalancer** | AZURE_LOADBALANCER |
| **Internet** | INTERNET |
| **VirtualNetwork** | VIRTUAL_NETWORK |


## <a name="service-tags-on-premises"></a>オンプレミスのサービス タグ  
現在のサービス タグと範囲情報を、オンプレミスのファイアウォール構成の一部として取得して含めることができます。 この情報は、各サービス タグに対応する現在の特定時点の IP 範囲の一覧です。 次のセクションで説明するように、プログラムまたは JSON ファイルのダウンロードを使用して、この情報を取得できます。

### <a name="use-the-service-tag-discovery-api"></a>Service Tag Discovery API を使用する
サービス タグの現在の一覧を IP アドレス範囲の詳細と共にプログラムで取得できます。

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az_network_list_service_tags)

たとえば、Storage サービス タグのすべてのプレフィックスを取得するには、次の PowerShell コマンドレットを使用できます。 

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus2
$storage = $serviceTags.Values | Where-Object { $_.Name -eq "Storage" }
$storage.Properties.AddressPrefixes
```

> [!NOTE]
> 
> - 新しいサービス タグ データがすべての Azure リージョンの API の結果に反映されるまでに、最大 4 週間かかります。 
> - 現在のサブスクリプションに対して、認証され、読み取りアクセス許可を持つロールを持っている必要があります。 
> - この API データは、NSG 規則で使用できるタグを表しており、ダウンロード可能な JSON ファイルに現在含まれているタグのサブセットを表しています。 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>ダウンロード可能な JSON ファイルを使用してサービス タグを検出する 
サービス タグの現在の一覧と IP アドレス範囲の詳細を含む JSON ファイルをダウンロードできます。 これらの一覧は、毎週更新されて公開されます。 各クラウドの場所は次のとおりです。

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

これらのファイル内の IP アドレス範囲は CIDR 表記です。 

次の AzureCloud タグには、標準スキーマに従って書式設定された地域名が含まれていません。 
- AzureCloud.centralfrance (FranceCentral)
- AzureCloud.southfrance (FranceSouth)
- AzureCloud.germanywc (GermanyWestCentral)
- AzureCloud.germanyn (GermanyNorth)
- AzureCloud.norwaye (NorwayEast)
- AzureCloud.norwayw (NorwayWest)
- AzureCloud.switzerlandn (SwitzerlandNorth)
- AzureCloud.switzerlandw (SwitzerlandWest)
- AzureCloud.usstagee (EastUSSTG)
- AzureCloud.usstagec (SouthCentralUSSTG)


> [!NOTE]
> [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653)、[Azure China](https://www.microsoft.com/download/details.aspx?id=42064)、および [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770) については、この情報のサブセットが XML ファイルで公開されています。 これらの XML ダウンロードは、2020 年 6 月 30 日に非推奨となり、その後は使用できなくなります。 前のセクションの説明に従って、Discovery API または JSON ファイルのダウンロードの使用に移行してください。

> [!TIP]
> 
> - ある公開からその次の公開に更新されたかどうかは、JSON ファイル内の *changeNumber* の値の増加に注目することで理解できます。 各サブセクション (たとえば **Storage.WestUS**) には、変更が発生するたびに増えていく固有の *changeNumber* があります。 ファイルの *changeNumber* の最上位レベルは、サブセクションのいずれかが変更されると増加します。
>
> - サービス タグ情報を解析する方法の例 (WestUS のストレージについてのすべてのアドレス範囲を取得する方法など) については、[Service Tag Discovery API PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag) のドキュメントを参照してください。
>
> - 新しい IP アドレスがサービス タグに追加されると、それらは少なくとも 1 週間は Azure で使用されません。 これにより、サービス タグに関連付けられた IP アドレスを追跡する必要がある可能性のあるシステムを更新する時間が得られます。


## <a name="next-steps"></a>次のステップ
- [ネットワーク セキュリティ グループの作成](tutorial-filter-network-traffic.md)方法を確認します。
