---
title: Azure Managed HSM の概要 - Azure Managed HSM | Microsoft Docs
description: Azure Managed HSM は、クラウド アプリケーションの暗号化キーを保護するクラウド サービスです。
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 8a83c26a5aaf5bc06609b0e8e889c18fadce1d2a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003101"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Azure Key Vault Managed HSM とは

Azure Key Vault Managed HSM は、フル マネージド、高可用性、シングル テナント、標準準拠を特徴とするクラウド サービスで、**FIPS 140-2 レベル 3** 適合の HSM (ハードウェア セキュリティ モジュール) を使用してクラウド アプリケーションの暗号化キーを保護することができます。 

価格情報については、[Azure Key Vault の価格に関するページ](https://azure.microsoft.com/pricing/details/key-vault/)の「マネージド HSM プール」セクションを参照してください。 サポートされているキーの種類については、「[キーについて](../keys/about-keys.md)」を参照してください。

## <a name="why-use-managed-hsm"></a>Managed HSM を使用する理由

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>フル マネージド、高可用性、シングル テナントのサービスとしての HSM

- **フル マネージド**: HSM のプロビジョニング、構成、パッチ適用、メンテナンスはサービスによって管理されます。 
- **高可用性とゾーン回復性** (可用性ゾーンがサポートされる): 各 HSM クラスターは、少なくとも 2 つの可用性ゾーンにまたがって存在する複数の HSM パーティションから成ります。 ハードウェアに障害が発生すると、HSM クラスターのメンバー パーティションが自動的に正常なノードに移行されます。
- **シングルテナント**: 各 Managed HSM インスタンスはお客様ごとに確保され、複数の HSM パーティションのクラスターから成ります。 各 HSM クラスターでは、個々のお客様を対象としたセキュリティ ドメインが使用され、お客様ごとに HSM クラスターが暗号的に分離されます。


### <a name="access-control-enhanced-data-protection--compliance"></a>アクセスの制御、強化されたデータ保護とコンプライアンス

- **キーの集中管理**: 組織のいたるところにあるきわめて重要で価値の高いキーが一元管理されます。 粒度の細かいキーごとのアクセス許可により、各キーに対するアクセスが "最低特権アクセス" の原則で管理されます。
- **分離されたアクセスの制御**: Managed HSM の "ローカル RBAC" アクセス制御モデルにより、指定された HSM クラスタ アドミニストレーターには、HSM に対する完全な制御権が与えられます。その権限は、管理グループやサブスクリプション管理者、リソース グループ管理者でもオーバーライドできません。
- **プライベート エンドポイント**: プライベート エンドポイントを使用して、仮想ネットワークで実行されているアプリケーションから Managed HSM に安全かつプライベートに接続します。
- **FIPS 140-2 レベル 3 適合の HSM**: FIPS (Federal Information Protection Standard) 140-2 レベル 3 適合の HSM によってデータを保護し、コンプライアンス要件を満たすことができます。 Managed HSM には、Marvell LiquidSecurity の HSM アダプターが使用されます。
- **監視と監査**: Azure Monitor と完全に統合されます。 すべてのアクティビティの完全なログを Azure Monitor 経由で取得できます。 分析とアラートには Azure Log Analytics を使用できます。
- **データ所在地**: マネージド HSM では、お客様が HSM インスタンスをデプロイするリージョンの外部で顧客データが格納または処理されることはありません。

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Azure と Microsoft PaaS (または SaaS) サービスとの統合 

- キーを生成 (または [BYOK](hsm-protected-keys-byok.md) を使用してインポート) し、それらを使用して、[Azure Storage](../../storage/common/customer-managed-keys-overview.md)、[Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)、[Azure Information Protection](/azure/information-protection/byok-price-restrictions) など、Azure サービスの保存データを暗号化できます。

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Key Vault と同じ API と管理インターフェイスを使用する

- 資格情報コンテナー (マルチテナント) を使用する既存のアプリケーションを、マネージド HSM を使用するアプリケーションに容易に移行できます。
- 使用されているキー管理ソリューション (マルチテナントの資格情報コンテナーまたはシングル テナントのマネージド HSM) に関係なく、すべてのアプリケーションに同じアプリケーション開発およびデプロイ パターンを使用できます。

### <a name="import-keys-from-your-on-premise-hsms"></a>オンプレミスの HSM からキーをインポートする

- HSM で保護されたキーをオンプレミス HSM で生成し、それらを Managed HSM に対して安全にインポートすることができます。

## <a name="next-steps"></a>次のステップ
- 「[クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照して、マネージド HSM を作成してアクティブにします。
- [Azure Key Vault Managed HSM を使用しているときのベスト プラクティス](best-practices.md)に関するページを参照してください。
- [Managed HSM の状態](https://status.azure.com)
- [Managed HSM のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/key-vault-managed-hsm/v1_0/)
- [Managed HSM リージョンの可用性](https://azure.microsoft.com/global-infrastructure/services/?products=key-vault)
