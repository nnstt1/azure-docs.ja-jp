---
title: Azure Arc 対応 Kubernetes の概要
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: overview
description: この記事では、Azure Arc 対応 Kubernetes の概要を示します。
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: 7338664698e40a1e4a1280cc08ee57a922518d79
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312840"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes とは

Azure Arc 対応 Kubernetes を使用すると、Azure の内部または外部にある Kubernetes クラスターを接続して構成することができます。 Kubernetes クラスターを Azure Arc に接続すると、クラスターは次のようになります。
* Azure portal に表示され、Azure Resource Manager ID とマネージド ID が割り当てられる。 
* Azure サブスクリプションおよびリソース グループに配置される。
* 他の Azure リソースと同様にタグを追加できる。 

Kubernetes クラスターを Azure に接続するには、クラスター管理者がエージェントをデプロイする必要があります。 エージェントは次のことを行います。
* `azure-arc` Kubernetes 名前空間で、標準の Kubernetes デプロイとして実行される。
* Azure への接続を処理する。
* Azure Arc のログとメトリックを収集する。
* 構成要求を監視する。 

Azure Arc 対応 Kubernetes では、転送中のデータをセキュリティで保護する業界標準の SSL がサポートされます。 また、保存データは Azure Cosmos DB データベースに暗号化された状態で格納されるので、データの機密性が確保されます。

## <a name="supported-kubernetes-distributions"></a>サポートされている Kubernetes ディストリビューション

Azure Arc 対応 Kubernetes は、すべての Cloud Native Computing Foundation (CNCF) 認定 Kubernetes クラスターで動作します。 Azure Arc チームは、[主要な業界パートナーと協力して、各 Kubernetes ディストリビューションと Azure Arc 対応 Kubernetes の適合性を検証](./validation-program.md)してきました。

## <a name="supported-scenarios"></a>サポートされるシナリオ 

Azure Arc 対応 Kubernetes は、次のシナリオをサポートします。 

* インベントリ、グループ化、タグ付けのために Azure 外部で実行されている Kubernetes を接続する。

* アプリケーションをデプロイし、GitOps ベースの構成管理を使用して構成を適用する。 

* コンテナーに対して Azure Monitor を使用して、クラスターを表示および監視する。

* Microsoft Defender for Kubernetes を使用して脅威保護を適用する

* Kubernetes 用の Azure Policy を使用してポリシー定義を適用する。

* Azure Arc 対応 Data Services、[Azure Arc 上の App Services](../../app-service/overview-arc-integration.md) (Web、関数、ロジック アプリを含む) および [Kubernetes 上の Event Grid](../../event-grid/kubernetes/overview.md) をデプロイするターゲットの場所として、[カスタムの場所](./custom-locations.md)を作成します。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="next-steps"></a>次のステップ

クラスターを Azure Arc に接続する方法について学習します。
> [!div class="nextstepaction"]
> [Azure Arc にクラスターを接続する](./quickstart-connect-cluster.md)
