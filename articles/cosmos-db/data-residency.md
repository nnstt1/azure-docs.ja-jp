---
title: Azure Cosmos DB のデータ所在地の要件を満たす方法
description: データとバックアップを 1 つのリージョンに保持するために、Azure Cosmos DB のデータ所在地の要件を満たす方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 869b28c8c65c402936dcb7b7641a6e4af8db3f47
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100941"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Azure Cosmos DB のデータ所在地の要件を満たす方法
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB では、[所在地の要件](https://azure.microsoft.com/global-infrastructure/data-residency/)を満たすために、データとバックアップを 1 つのリージョンに保持するように構成できます。

## <a name="residency-requirements-for-data"></a>データの所在地の要件

Azure Cosmos DB では、リージョン間のデータ レプリケーションを明示的に構成する必要があります。 [Azure portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)、[Azure CLI](scripts/cli/common/regions.md) を使用して geo レプリケーションを構成する方法について学習してください。 データ所在地の要件を満たすために、特定のリージョンから望ましくないリージョンへのデータ レプリケーションを防ぐことができる Azure Policy の定義を作成できます。

## <a name="residency-requirements-for-backups"></a>バックアップの所在地の要件

**連続モード バックアップ**: これらのバックアップは、既定でローカル冗長またはゾーン冗長ストレージに格納されるものとして存在します。 詳細については、[継続的バックアップ](provision-account-continuous-backup.md)の記事を参照してください。

**定期的モードバックアップ**: 既定では、定期的モードのアカウント バックアップは geo 冗長ストレージに格納されます。 定期的なバックアップ モードでは、データの冗長性をアカウント レベルで構成できます。 バックアップ ストレージには、3 つの冗長オプションがあります。 これらはローカル冗長性、ゾーン冗長性、または geo 冗長性です。 詳細については、ポータルを使用して[バックアップの冗長性を構成する](configure-periodic-backup-restore.md#configure-backup-interval-retention)方法を参照してください。

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Azure Policy を使用して所在地の要件を適用する

すべてのデータを 1 つの Azure リージョンに保持する必要があるデータ所在地の要件がある場合は、Azure Policy を使用して、アカウントに対してゾーン冗長またはローカル冗長のバックアップを適用することができます。  また、Azure Cosmos DB アカウントが他のリージョンに geo レプリケートされないようにするポリシーを適用することもできます。

Azure Policy は、Azure リソースにルールを適用するポリシーの作成、割り当て、管理に使用できるサービスです。 Azure Policy を使用すると、これらのリソースが会社の標準やサービス レベル アグリーメントに準拠した状態を維持するのに役立ちます。 詳細については、[Azure Policy](policy.md) を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する方法をご覧ください

## <a name="next-steps"></a>次の手順

* [Azure portal](configure-periodic-backup-restore.md) を使用して定期的なバックアップを構成および管理する
* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用して継続的バックアップ アカウントを復元します。
* [定期的なバックアップから継続的バックアップにアカウントを移行します](migrate-continuous-backup.md)。

