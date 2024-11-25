---
title: ISO 27001 共有サービス ブループリント サンプルの概要
description: ISO 27001 共有サービスのブルー プリント サンプルの概要とアーキテクチャ。 このブループリント サンプルは、お客様が特定の ISO 27001 コントロールを評価するのに役立ちます。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: d038345ad07cb41a7a3161dcfb4175d558a34ea3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632027"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 共有サービス ブループリント サンプルの概要

ISO 27001 共有サービス ブループリント サンプルでは、ISO 27001 構成証明に役立つ一連の準拠インフラストラクチャ パターンおよびポリシー ガードレールが提供されます。 このブループリントにより、お客様は、認定またはコンプライアンスの要件があるシナリオにソリューションを提供するクラウド ベースのアーキテクチャをデプロイできます。

[ISO 27001 App Service Environment/SQL Database ワークロード](../iso27001-ase-sql-workload/index.md) ブループリント サンプルによりこのサンプルは拡張されます。

## <a name="architecture"></a>Architecture

ISO 27001 共有サービス ブループリント サンプルにより、仮想データセンター (VDC) アプローチに基づいて複数のワークロードをホストするために組織が使用できる Azure 内の基盤インフラストラクチャがデプロイされます。
VDC は、Microsoft がその最大規模のエンタープライズのお客様と共に使用する参照アーキテクチャ、オートメーション ツール、およびエンゲージメント モデルの実証済みのセットです。 共有サービスのブループリント サンプルは、次に示す完全にネイティブな Azure VDC 環境に基づきます。

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="ISO 27001 共有サービスのブループリント サンプル設計" border="false":::

この環境は、ISO 27001 標準に基づいた、完全に監視された安全なエンタープライズ対応の共有サービス インフラストラクチャを提供するために使用される複数の Azure サービスで構成されます。 この環境は、以下で構成されます。

- [Azure ロール](../../../../role-based-access-control/overview.md)。コントロール プレーンの観点から義務の分離のために使用します。 すべてのインフラストラクチャのデプロイの前に、3 つのロールが定義されています。
  - NetOps ロールには、ファイアウォール設定、NSG 設定、ルーティング、その他のネットワーク機能を含むネットワーク環境を管理する権限があります。
  - SecOps ロールには、[Azure Security Center](../../../../security-center/security-center-introduction.md) をデプロイ、管理し、[Azure Policy](../../../policy/overview.md) 定義を定義するのに必要な権限、およびその他のセキュリティ関連の権限があります
  - SysOps ロールには、サブスクリプション内で [Azure Policy](../../../policy/overview.md) 定義を定義し、環境全体のその他の運用権限の中で [Log Analytics](../../../../azure-monitor/overview.md) を管理するのに必要な権限があります
- [Log Analytics](../../../../azure-monitor/overview.md) は、セキュリティで保護されたデプロイの開始以降、すべてのアクションとサービス ログを一元化する最初の Azure サービスとしてデプロイされます。
- オンプレミス データセンター、インターネット接続用のイングレス/エグレス スタック、および以下を含むフル マイクロセグメンテーション用に NSG および ASG を使用する共有サービス サブネットへの折り返し接続用のサブネットをサポートする仮想ネットワーク。
  - 管理目的で使用されるジャンプボックスまたは踏み台ホスト。イングレス スタック サブネットにデプロイされた [Azure Firewall](../../../../firewall/overview.md) 経由でのみアクセスできます。
  - ジャンプボックスからのみアクセスできる Azure Active Directory Domain Services (Azure AD DS) および DNS を実行する 2 つの仮想マシン。VPN または [ExpressRoute](../../../../expressroute/expressroute-introduction.md) 接続 (ブループリントでデプロイされていない) 経由で AD をレプリケートするためだけに構成できます
  - [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) と標準 DDoS 保護の使用
- [Azure Key Vault](../../../../key-vault/general/overview.md) インスタンス。共有サービス環境にデプロイされた VM 用に使用されるシークレットをホストします。

これらの要素はすべて、「[Azure アーキテクチャ センター - 参照アーキテクチャ](/azure/architecture/reference-architectures/)」で公開されている実証済みのプラクティスに従っています。

> [!NOTE]
> ISO 27001 共有サービス インフラストラクチャによって、ワークロード用の基本アーキテクチャが示されています。
> この基本アーキテクチャに従ってワークロードをデプロイする必要があります。

詳細については、[Virtual Datacenter のドキュメント](/azure/architecture/vdc/)を参照してください。

## <a name="next-steps"></a>次のステップ

ISO 27001 共有サービス ブループリント サンプルの概要とアーキテクチャを確認しました。
次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [ISO 27001 共有サービスのブループリント - コントロール マッピング](./control-mapping.md)
> [ISO 27001 共有サービスのブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
