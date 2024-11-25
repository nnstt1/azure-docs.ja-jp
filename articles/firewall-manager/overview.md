---
title: Azure Firewall Manager とは
description: Azure Firewall Manager の機能について説明します
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 08/03/2021
ms.author: victorh
ms.openlocfilehash: 9a6e6a0713179295379e758f454617484c75b9a2
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779158"
---
# <a name="what-is-azure-firewall-manager"></a>Azure Firewall Manager とは

Azure Firewall Manager は、クラウドベースのセキュリティ境界に対して、集約型セキュリティ ポリシーとルート管理を提供するセキュリティ管理サービスです。 

Firewall Manager は、次の 2 種類のネットワーク アーキテクチャのセキュリティ管理機能を備えています。

- **セキュリティ保護付き仮想ハブ**

   [Azure Virtual WAN ハブ](../virtual-wan/virtual-wan-about.md#resources)は、ハブとスポークのアーキテクチャを簡単に作成できる Microsoft の管理対象リソースです。 セキュリティとルーティングのポリシーがそのようなハブに関連付けられている場合は、 *[セキュリティ保護付き仮想ハブ](secured-virtual-hub.md)* と呼ばれます。 
- **ハブ仮想ネットワーク**

   自分で作成して管理できる標準の Azure 仮想ネットワークです。 そのようなハブにセキュリティ ポリシーが関連付けられている場合は、"*ハブ仮想ネットワーク*" と呼ばれます。 現時点では、Azure Firewall ポリシーのみがサポートされています。 ワークロード サーバーが含まれるスポーク仮想ネットワークとサービスをピアリングすることができます。 どのスポークにもピアリングされていないスタンドアロンの仮想ネットワークでファイアウォールを管理することもできます。

"*セキュリティ保護付き仮想ハブ*" と "*ハブ仮想ネットワーク*" アーキテクチャの種類の詳しい比較については、「[Azure Firewall Manager のアーキテクチャ オプション](vhubs-and-vnets.md)」を参照してください。

![Firewall Manager](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Azure Firewall Manager の機能

Azure Firewall Manager には、次の機能が用意されています。

### <a name="central-azure-firewall-deployment-and-configuration"></a>一元的な Azure Firewall のデプロイと構成

異なる Azure リージョンとサブスクリプションにまたがる複数の Azure Firewall インスタンスを一元的にデプロイし、構成することができます。 

### <a name="hierarchical-policies-global-and-local"></a>階層型ポリシー (グローバルおよびローカル)

Azure Firewall Manager を使用して、複数のセキュリティで保護された仮想ハブにまたがる Azure Firewall ポリシーを一元的に管理できます。 中央の IT チームは、グローバル ファイアウォール ポリシーを作成し、チームを越えて組織全体のファイアウォール ポリシーを適用することができます。 ローカルで作成されたファイアウォール ポリシーを使用すると、DevOps のセルフサービス モデルで機敏性を向上させることができます。

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>セキュリティを強化するためのサードパーティのサービスとしてのセキュリティとの統合

Azure Firewall だけでなく、サードパーティのサービスとしてのセキュリティ (SECaaS) プロバイダーを統合して、VNet とブランチのインターネット接続に追加のネットワーク保護を提供することもできます。

この機能は、セキュリティ保護付き仮想ハブのデプロイでのみ利用できます。

- VNet からインターネット (V2I) へのトラフィックのフィルター処理

   - 任意のサードパーティ セキュリティ プロバイダーを使用して、送信仮想ネットワーク トラフィックをフィルター処理します。
   - Azure 上で実行されているクラウド ワークロードに対して、高度なユーザー対応のインターネット保護を利用します。

- ブランチからインターネット (B2I) へのトラフィックのフィルター

   Azure の接続とグローバル分散を利用して、ブランチからインターネットへのシナリオにサードパーティのフィルター処理を簡単に追加できます。

セキュリティ プロバイダーの詳細については、[Azure Firewall Manager のセキュリティ パートナー プロバイダー](trusted-security-partners.md)に関するページを参照してください。

### <a name="centralized-route-management"></a>一元的なルート管理

スポーク仮想ネットワーク上でユーザー定義ルート (UDR) を手動で設定しなくても、フィルター処理とログ記録のためにセキュリティ保護付きハブにトラフィックをルーティングすることができます。 

この機能は、セキュリティ保護付き仮想ハブのデプロイでのみ利用できます。

ブランチからインターネット (B2I) へのトラフィックのフィルター処理にはサードパーティ プロバイダーを使用し、ブランチから VNet (B2V)、VNet から VNet (V2V)、および VNet からインターネット (V2I) には Azure Firewall を同時に使用できます。 

## <a name="region-availability"></a>利用可能なリージョン

Azure Firewall ポリシーは、複数のリージョンで使用できます。 たとえば、米国西部でポリシーを作成し、米国東部で使用することができます。 

## <a name="known-issues"></a>既知の問題

Azure Firewall Manager には、次の既知の問題があります。

|問題  |説明  |対応策  |
|---------|---------|---------|
|トラフィックの分割|Microsoft 365 と Azure パブリック PaaS トラフィックの分割は現在サポートされていません。 そのため、V2I または B2I にサードパーティ プロバイダーを選択すると、パートナー サービスを介してすべての Azure Public PaaS および Microsoft 365 トラフィックも送信されます。|ハブでのトラフィックの分割を調査中です。
|セキュリティ保護付き仮想ハブがリージョンごとに 1 つである|1 つのリージョンで複数のセキュリティ保護付き仮想ハブを使用することはできません。|1 つのリージョンに複数の仮想 WAN を作成します。|
|基本ポリシーがローカル ポリシーと同じリージョンにある必要がある|基本ポリシーと同じリージョンにすべてのローカル ポリシーを作成します。 セキュリティ保護付きハブ上の 1 つのリージョンで作成されたポリシーを、別のリージョンから適用することもできます。|調査中|
|セキュリティで保護された仮想ハブ デプロイでのハブ間トラフィックのフィルター処理|セキュリティ保護付き仮想ハブからセキュリティ保護付き仮想ハブへの通信のフィルター処理は、まだサポートされていません。 ただし、Azure Firewall によるプライベート トラフィックのフィルター処理が有効になっていない場合は、ハブからハブへの通信は引き続き機能します。|調査中|
|プライベート トラフィック フィルターが有効になっている場合のブランチ間のトラフィック|プライベート トラフィック フィルターが有効になっている場合、ブランチ間のトラフィックはサポートされていません。 |調査中。<br><br>ブランチ間の接続が重要である場合は、プライベート トラフィックをセキュリティで保護しないでください。|
|同じ Virtual WAN を共有するすべてのセキュリティ保護付き仮想ハブは同じリソース グループに存在する必要がある|この動作は、今日の Virtual WAN ハブに合わせたものです。|複数の異なるリソース グループにセキュリティ保護付き仮想ハブを作成できるようにするには、複数の Virtual WAN を作成します。|
|一括 IP アドレス追加が失敗する|複数のパブリック IP アドレスを追加すると、セキュリティで保護されたハブ ファイアウォールがエラー状態になります。|より少ない増分のパブリック IP アドレスを追加します。 たとえば、一度に 10 個を追加します。|
|セキュリティ保護付き仮想ハブで DDoS Protection Standard がサポートされていない|DDoS Protection Standard は vWAN と統合されていません。|調査中|
|アクティビティ ログが完全にはサポートされていない|現在、ファイアウォール ポリシーでは、アクティビティ ログはサポートされていません。|調査中|
|ルールの説明が完全にサポートされていない|ファイアウォール ポリシーで、ARM エクスポート内のルールの説明が表示されません。|調査中|
|Azure Firewall Manager で、静的およびカスタムのルートが上書され、仮想 WAN ハブのダウンタイムの原因となっている。|Azure Firewall Manager を使用して、カスタムまたは静的ルートを使用して構成されたデプロイの設定を管理しないでください。 Firewall Manager からの更新によって、静的またはカスタムのルート設定が上書きされる可能性があります。|静的またはカスタムのルートを使用する場合は、[Virtual WAN] ページを使用してセキュリティ設定を管理し、Azure Firewall Manager を使用した構成は避けてください。<br><br>詳細については、「[シナリオ: Azure Firewall - カスタム](../virtual-wan/scenario-route-between-vnets-firewall.md)」をご覧ください。|

## <a name="next-steps"></a>次のステップ

- [Learn モジュール: Azure Firewall Manager の概要](/learn/modules/intro-to-azure-firewall-manager/)。
- [Azure Firewall Manager のデプロイ概要](deployment-overview.md)を確認する
- [セキュリティで保護された仮想ハブ](secured-virtual-hub.md)について学習します。
