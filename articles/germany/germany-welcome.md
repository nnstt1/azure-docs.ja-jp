---
title: Azure Germany の概要 | Microsoft Docs
description: この記事では、Azure Germany のクラウド機能と、ドイツのデータ プライバシー規制のコンプライアンス要件をサポートする信頼性の高い設計とセキュリティの概要について説明します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 394e804faa0d6f6036a025cb4cad0119af5c3837
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029201"
---
# <a name="welcome-to-azure-germany"></a>Azure Germany へようこそ

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="overview"></a>概要
Microsoft Azure Germany は、[セキュリティ、プライバシー、コンプライアンス、透明性の根本原則](https://azure.microsoft.com/overview/clouds/germany/)に基づいて構築されたクラウド プラットフォームを実現します。 Azure Germany は、Microsoft Azure の物理的に独立したインスタンスです。 そのアーキテクチャに基づいて構築されたすべてのシステムとアプリケーションのためのドイツのデータ プライバシー規制に不可欠である世界クラスのセキュリティおよび[コンプライアンス サービス](https://azure.microsoft.com/support/trust-center/compliance/)を使用します。 Azure Germany は、データ保護受託者によって運営されており、オンプレミスまたはクラウドでソリューションを構築およびデプロイするための複数のハイブリッド シナリオをサポートしています。 また、ハイパースケールのクラウド サービスの迅速なスケール変更や、保証されたアップタイムを利用することもできます。

ドイツにデータ所在地が提供され、データが運ばれ、保管されます。そしてビジネス継続性のため、ドイツのデータセンター間でデータのレプリケーションが行われます。 2 つのデータセンターの顧客データは、データ トラスティの T-Systems International によって管理されます。 このトラスティは、ドイツの独立した企業であり、Deutsche Telekom の子会社です。 アクセスはお客様のアクセス許可またはデータ トラスティによってのみ提供されるため、お客様のデータをより厳重に管理できます。

これらのデータセンターで提供される Microsoft の商用クラウド サービスはドイツのデータ取り扱いに関する規制に準拠しているため、お客様のデータの取り扱い方法と場所の選択肢がさらに広がります。

Azure Germany には、サービスとしてのインフラストラクチャ (IaaS)、サービスとしてのプラットフォーム (PaaS)、およびサービスとしてのソフトウェア (SaaS) のコア コンポーネントがあります。 これらのコンポーネントには、インフラストラクチャ、ネットワーク、ストレージ、データ管理、ID 管理、その他多くのサービスが含まれます。

Azure Germany は、静止データのレプリケーションや自動スケーリングなど、世界各地の Azure のお客様が使用してきた優れた機能のほとんどをサポートしています。 

## <a name="azure-germany-documentation"></a>Azure Germany ドキュメント
このサイトでは、 [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) サービスの機能について説明すると共に、すべてのお客様に該当する一般的なガイダンスを提供しています。 特に規制されたデータを Azure Germany のサブスクリプションに含める前に、Azure Germany の機能について理解を深めておいてください。

特定の適格性認定や規制の対象となる Azure Germany サービスの最新情報については、[Microsoft Azure セキュリティ センター コンプライアンス ページ](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)を参照してください。 その他の Microsoft サービスが利用できる可能性もありますが、Azure Germany がカバーするサービスおよびこのドキュメントの範囲には含まれていません。 また、Azure Germany サービスでは、その他さまざまなリソース、アプリケーション、サービスを使用することが認められている場合があります。そうしたリソースやアプリケーション、サービスは、サード パーティまたは Microsoft が、独立した使用条件とプライバシー ポリシーの下で提供しています。 それらもこのドキュメントの範囲に含まれていません。 そうした "付加的" サービス (Azure Marketplace サービスなど) については、付随する条項をご自身の責任ですべて確認し、コンプライアンスに関する要件を確実に満たしてください。

Azure Germany は、英国を含む EU/EFTA 圏内でビジネスを行う予定の全世界の有資格のお客様およびパートナーにご利用いただけます。

## <a name="general-guidance-for-customers"></a>お客様向けの一般的なガイダンス
現在利用可能な技術的コンテンツのほとんどは、アプリケーションが Azure Germany 用ではなくグローバル Azure 用に開発されることを前提としています。 Azure Germany でホストされるように開発されたアプリケーションの主な違いを開発者に認識させることが重要です。

* グローバル Azure の特定のリージョンにある特定のサービスと機能は、Azure Germany では使用できない場合があります。 一般公開されている最新のサービスについては、[リージョンに関するページ](https://azure.microsoft.com/regions/services)を参照してください。 
* Azure Germany で提供されている機能の場合、グローバル Azure とは構成上の相違点があります。 サンプル コード、構成、手順を確認し、Azure Germany 環境内で確実に構築および実行するようにしてください。
* Azure Government の機能領域を示す情報、およびお客様が規制/制御するデータのガイダンスとベスト プラクティスについては、こちらのサイトの Azure Germany テクニカル サービス ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
補足情報と更新情報については、[Azure Germany のブログ](/archive/blogs/azuregermany/)を参照してください。

Azure Germany の詳細をご希望の場合は、次のリンクを使用してください。

* [サインアップして試用](https://azure.microsoft.com/free/germany/)
* [サインイン](https://portal.microsoftazure.de/) (Azure Germany アカウントを既にお持ちの場合)
* [Azure Germany の入手とアクセス](https://azure.microsoft.com/overview/clouds/germany/)