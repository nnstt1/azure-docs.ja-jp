---
title: CAF 移行ランディング ゾーン ブループリント サンプルの概要
description: Cloud Adoption Framework for Azure (CAF) 移行ランディング ゾーン ブループリント サンプルの概要とアーキテクチャ。
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 98978215c711a41fa281880aa93cb028b896d0d5
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108757801"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Microsoft Cloud Adoption Framework for Azure 移行ランディング ゾーン ブループリント サンプルの概要

Microsoft Cloud Adoption Framework for Azure (CAF) 移行ランディング ゾーン ブループリントは、最初のワークロードの移行の設定とクラウド資産の管理に役立つ、CAF と連携した一連のインフラストラクチャです。

[CAF 基盤](../caf-foundation/index.md)ブループリント サンプルによりこのサンプルは拡張されます。

## <a name="architecture"></a>Architecture

CAF 移行ランディング ゾーン ブループリント サンプルは、仮想マシンを移行するためのサブスクリプションを準備する際に組織で使用できる基本インフラストラクチャ リソースを Azure にデプロイします。 また、クラウド資産を管理するために必要なガバナンス コントロールの導入にも役立ちます。 このサンプルは、組織が自信を持って Azure を使い始めるために役立つリソース、ポリシー、テンプレートをデプロイして適用します。

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="CAF 移行ランディング ゾーン、インストール内容の説明図 (初期のランディング ゾーンに関する CAF ガイダンスの一部)。" border="false":::
   CAF 移行ブループリントをデプロイすることによって達成される Azure アーキテクチャを説明しています。 これは、Azure 仮想ネットワーク、ログを格納するためのストレージ アカウント、ストレージ アカウントに格納するように構成された Log Analytics で構成されるリソース グループを使用するサブスクリプションに適用可能です。 また、構成済みの Azure Key Vault と初期セットアップが作成された Azure Migrate も示されています。 これらのすべてのコア インフラストラクチャは、Azure Active Directory を使用してアクセスされます。
:::image-end:::

この環境は、セキュリティで保護され、完全に監視されたエンタープライズ対応のガバナンスを提供するために使用される複数の Azure サービスで構成されます。 この環境は、以下で構成されます。

- [Azure Key Vault](../../../../key-vault/general/overview.md) インスタンス。共有サービス環境にデプロイされた証明書、キー、シークレット用に使用されるシークレットをホストします。
- [Log Analytics](../../../../azure-monitor/overview.md)。移行の開始以降、すべてのアクションとサービス ログが中心的な場所に確保されるようにデプロイされます。
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md)。仮想マシン用の分離されたネットワークとサブネットを提供します。
- [Azure Migrate プロジェクト](../../../../migrate/migrate-services-overview.md)。検出と評価のために使用されます。 サーバーの評価、サーバーの移行、データベースの評価、データベースの移行のためのツールが今後追加される予定です。

これらの要素はすべて、「[Azure アーキテクチャ センター - 参照アーキテクチャ](/azure/architecture/reference-architectures/)」で公開されている実証済みのプラクティスに従っています。

> [!NOTE]
> CAF 移行ブループリントは、ワークロードのランディング ゾーンを示します。 この基本アーキテクチャの上で仮想マシンまたはデータベースの評価と移行を優先的に行う必要もあります。

詳細については、[Microsoft Cloud Adoption Framework for Azure の移行](/azure/architecture/cloud-adoption/migrate/)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

CAF 移行ランディング ゾーン ブループリント サンプルの概要とアーキテクチャを確認しました。

> [!div class="nextstepaction"]
> [CAF 移行ランディング ゾーン ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
