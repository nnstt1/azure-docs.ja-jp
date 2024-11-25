---
title: Azure Cosmos DB のポイントインタイム リストア機能を使用した継続的バックアップ
description: Azure Cosmos DB のポイントインタイム リストア機能を使用すると、誤って行われた書き込みや削除の操作からデータを復旧したり、データを任意のリージョンに復元したりすることができます。 価格と現在の制限事項について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: c0e08a9aadc7389fa064ba03fbd026ace197cae1
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165544"
---
# <a name="continuous-backup-with-point-in-time-restore-in-azure-cosmos-db"></a>Azure Cosmos DB のポイントインタイム リストアを使用した継続的バックアップ
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB のポイントインタイム リストア機能は、次のような複数のシナリオで役立ちます。

* コンテナー内で誤って行われた書き込みまたは削除の操作から復旧する。
* 削除されたアカウント、データベース、またはコンテナーを復元する。
* 特定の復元ポイントで (バックアップが存在していた) 任意のリージョンに復元する。

Azure Cosmos DB では、データのバックアップがバックグラウンドで実行され、プロビジョニング済みのスループット (RU) を余計に消費することも、データベースのパフォーマンスや可用性に影響することもありません。 継続的バックアップは、アカウントが存在するすべてのリージョンで実行されます。 次の図は、米国西部の書き込みリージョン、米国東部と米国東部 2 の読み取りリージョンを含むコンテナーが、どのようにそれぞれのリージョンのリモート Azure Blob Storage アカウントにバックアップされるかを示しています。 既定の場合、各リージョンではバックアップがローカル冗長ストレージ アカウントに格納されます。 リージョンで[可用性ゾーン](high-availability.md#availability-zone-support)が有効になっている場合、バックアップはゾーン冗長ストレージ アカウントに格納されます。

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Blob Storage への Azure Cosmos DB データのバックアップ。" lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

復元に使用できる時間枠 (保持期間とも呼ばれます) は、次の 2 つのうち低い方の値です。"*今から 30 日前*" または "*リソースの作成時まで*" です。 復元する特定の時点には、保持期間内の任意のタイムスタンプを指定できます。

現時点では、特定時点の SQL API または MongoDB コンテンツの Azure Cosmos DB アカウントを、[Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[Azure コマンド ライン インターフェイス](restore-account-continuous-backup.md#restore-account-cli) (az CLI)、[Azure PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用して別のアカウントに復元できます。

## <a name="backup-storage-redundancy"></a>バックアップ ストレージの冗長性

既定では、Azure Cosmos DB は、連続モードのバックアップ データをローカル冗長ストレージ BLOB に格納します。 ゾーン冗長が構成されているリージョンでは、バックアップはゾーン冗長ストレージ BLOB に格納されます。 継続的バックアップ モードでは、バックアップ ストレージの冗長性を更新することはできません。

## <a name="what-is-restored"></a>復元される内容

安定した状態では、ソース アカウント (データベース、コンテナー、アイテムを含む) で実行されるすべての変更が 100 秒以内に非同期的にバックアップされます。 バックアップ メディア (Azure Storage) がダウンしているか使用できない場合、変更はメディアが再び使用できるようになるまでローカルに保存され、その後、復元される可能性のある操作の忠実性が失われないように消去されます。

プロビジョニングされたスループット コンテナー、共有スループット データベース、またはアカウント全体を任意に組み合わせて復元できます。 復元操作では、すべてのデータとそのインデックス プロパティが新しいアカウントに復元されます。 復元プロセスでは、アカウント、データベース、またはコンテナーで復元されるすべてのデータが、指定された復元時点まで整合していることが保証されます。 復元の期間は、復元する必要があるデータの量によって異なります。

> [!NOTE]
> 継続的バックアップ モードでは、バックアップは Azure Cosmos DB アカウントを使用できるすべてのリージョンで実行されます。 リージョン アカウントごとに作成されたバックアップは、既定ではローカル冗長であり、アカウントでそのリージョンに対して[可用性ゾーン](high-availability.md#availability-zone-support)機能が有効になっている場合はゾーン冗長です。 復元操作では、常に新しいアカウントにデータが復元されます。

## <a name="what-is-not-restored"></a>復元されない内容

ポイントインタイム リストア後に、次の構成は復元されません。

* ファイアウォール、VNET、プライベート エンドポイントの設定。
* 整合性の設定。 既定では、アカウントはセッションの整合性を使用して復元されます。  
* リージョン。
* ストアド プロシージャ、トリガー、UDF。

これらの構成は、復元の完了後に復元されたアカウントに追加できます。

## <a name="restore-scenarios"></a>復元シナリオ

以下に、ポイントインタイム リストア機能で対応する主なシナリオをいくつか示します。 シナリオ [a] ～ [c] は、復元のタイムスタンプが事前にわかっている場合に復元をトリガーする方法を示します。
ただし、偶発的に発生した削除や破損の正確な時刻がわからないというシナリオもあり得ます。 シナリオ [d] と [e] は、復元可能なデータベースまたはコンテナーで新しいイベント フィード API を使用して、復元のタイムスタンプを "_検出_" する方法を示します。

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="復元可能なアカウントでのタイムスタンプを含むライフサイクル イベント。" lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

1. **削除されたアカウントを復元する** - 削除された復元可能なすべてのアカウントは、 **[復元]** ウィンドウから表示できます。 たとえば、タイムスタンプ T3 で "*アカウント A*" が削除されたとします。 この場合、T3 の直前のタイムスタンプ、場所、ターゲット アカウント名、リソース グループ、ターゲット アカウント名を指定するだけで、[Azure portal](restore-account-continuous-backup.md#restore-deleted-account)、[PowerShell](restore-account-continuous-backup.md#trigger-restore-ps)、または [CLI](restore-account-continuous-backup.md#trigger-restore-cli) から復元できます。  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="復元可能なデータベースとコンテナーでのタイムスタンプを含むライフサイクル イベント。" lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

2. **特定のリージョンのアカウントのデータを復元する** - たとえば、タイムスタンプ T3 で "*アカウント A*" が "*米国東部*" と "*米国西部*" の 2 つのリージョンに存在するとします。 "*米国西部*" にアカウント A のコピーが必要な場合、ターゲットの場所として米国西部を指定して、[Azure portal](restore-account-continuous-backup.md#restore-deleted-account)、[PowerShell](restore-account-continuous-backup.md#trigger-restore-ps)、または [CLI](restore-account-continuous-backup.md#trigger-restore-cli) からポイントインタイム リストアを行うことができます。

3. **既知の復元タイムスタンプを使用してコンテナー内で誤って行われた書き込みまたは削除の操作から復旧する** - たとえば、"*データベース 1*" 内の "*コンテナー 1*" の内容が、タイムスタンプ T3 で誤って変更されたことが **わかっている** とします。 タイムスタンプ T3 で [Azure portal](restore-account-continuous-backup.md#restore-live-account)、[PowerShell](restore-account-continuous-backup.md#trigger-restore-ps)、または [CLI](restore-account-continuous-backup.md#trigger-restore-cli) から別のアカウントへのポイントインタイム リストアを実行して、コンテナーの必要な状態を復旧することができます。

4. **データベースが誤って削除される前の特定の時点にアカウントを復元する** - [Azure portal](restore-account-continuous-backup.md#restore-live-account)で、イベント フィード ウィンドウを使用して、データベースがいつ削除されたかを確認し、復元時刻を見つけることができます。 同様に、[Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) と [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) を使用して、データベース イベント フィードを列挙することでデータベースの削除イベントを検出し、必要なパラメーターを指定して復元コマンドをトリガーできます。

5. **コンテナーのプロパティを誤って削除または変更する前の特定時点にアカウントを復元する。** - [Azure portal](restore-account-continuous-backup.md#restore-live-account) で、イベント フィード ウィンドウを使用して、コンテナーがいつ作成、変更、または削除されたかを確認し、復元時刻を見つけることができます。 同様に、[Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) と [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) を使用して、コンテナー イベント フィードを列挙することですべてのコンテナー イベントを検出し、必要なパラメーターを指定して復元コマンドをトリガーできます。

## <a name="permissions"></a>アクセス許可

Azure Cosmos DB を使用すると、継続的バックアップ アカウントの復元のアクセス許可を切り分けて、特定のロールまたはプリンシパルに制限することができます。 アカウントの所有者は復元をトリガーし、他のプリンシパルにロールを割り当てて復元操作を実行できます。 詳細については、[アクセス許可](continuous-backup-restore-permissions.md)に関する記事を参照してください。

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>価格

継続的バックアップが有効になっている Azure Cosmos DB アカウントでは、"*バックアップを保存*" し、"*データを復元*" するために追加の月額料金が発生します。 復元コストは、復元操作が開始されるたびに加算されます。 継続的バックアップを行うようにアカウントを構成していてもデータを復元しない場合は、バックアップ ストレージのコストのみが請求書に含まれます。

次の例は、米国の非政府リージョンにデプロイされている Azure Cosmos アカウントの価格に基づいています。 価格と計算は、お客様が使用しているリージョンによって異なる場合があります。最新の価格情報については、「[Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)」ページをご覧ください。

* 継続的バックアップ ポリシーが有効になっているすべてのアカウントでは、バックアップ ストレージに対して追加の月額料金が発生し、次のように計算されます。

  $0.20/GB * アカウントのデータ サイズ (GB) * リージョンの数

* すべての復元 API の呼び出しで、1 回の課金が発生します。 料金はデータ復元量の関数であり、次のように計算されます。

  $0.15/GB * データ サイズ (GB)。

たとえば、2 つのリージョンに 1 TB のデータがある場合、次のようになります。

* バックアップ ストレージのコストは、1 か月あたり (1000 * 0.20 * 2) = $400 として計算されます。

* 復元コストは、復元あたり (1000 * 0.15) = $150 として計算されます。

## <a name="current-limitations"></a>現在の制限

現在、ポイントインタイム リストア機能には、次の制限があります。

* 継続的バックアップでは、SQL および MongoDB 用の Azure Cosmos DB API のみがサポートされます。 Cassandra、Table、Gremlin の API はまだサポートされていません。

* カスタマー マネージド キーを使用するアカウントは、継続的バックアップの使用に対応していません。

* 複数リージョンの書き込みアカウントはサポートされていません。

* Azure Synapse Link と定期的なバックアップ モードは、同じデータベース アカウント内で共存することができます。 ただし、バックアップと復元には、分析ストア データは含まれません。 Synapse Link が有効になっている場合、Azure Cosmos DB がトランザクション ストア内のデータを、スケジュールされたバックアップ間隔で引き続き自動的にバックアップします。

* Azure Synapse Link と継続的バックアップ モードは、同じデータベース アカウント内で共存することができません。 現在、Synapse Link が有効になっているデータベース アカウントでは継続的バックアップ モードを使用できず、その逆も同様です。

* 復元されるアカウントは、ソース アカウントが存在するものと同じリージョンに作成されます。 ソース アカウントが存在していないリージョンにアカウントを復元することはできません。

* 復元の時間枠は 30 日のみであり、変更することはできません。

* バックアップは、自動的に geo ディザスターに対応しません。 アカウントとバックアップについての回復性を持つ別のリージョンを明示的に追加する必要があります。

* 復元の進行中は、アカウントのアクセス許可を付与したり VNET、ファイアウォールの構成を変更したりする Identity and Access Management (IAM) ポリシーの変更と削除は行わないでください。

* コンテナーの作成後に一意のインデックスを作成する SQL または MongoDB アカウント用の Azure Cosmos DB API は、継続的バックアップではサポートされません。 最初のコンテナー作成の一環として一意のインデックスを作成するコンテナーのみがサポートされます。 MongoDB アカウントでは、[拡張機能コマンド](mongodb/custom-commands.md)を使用して一意のインデックスを作成します。

* ポイントインタイム リストア機能では、常に新しい Azure Cosmos アカウントに復元されます。 既存のアカウントへの復元は、現在サポートされていません。 インプレースでの復元に関するフィードバックをお送りいただく場合は、アカウント担当者を通じて Azure Cosmos DB チームにご連絡ください。

* 復元後は、特定のコレクションで consistent インデックスが再作成される可能性があります。 再作成操作の状況は、[IndexTransformationProgress](how-to-manage-indexing-policy.md) プロパティを使用して確認できます。

* 復元プロセスでは、その TTL 構成を含む、コンテナーのすべてのプロパティが復元されます。 そのため、その方法で構成した場合、復元されたデータが直ちに削除される可能性があります。 この状況を回避するには、復元のタイムスタンプを TTL プロパティがコンテナーに追加された時点より前にする必要があります。

* 継続的バックアップ モードのアカウントを作成する場合、またはアカウントを定期的から継続的なモードに移行する場合は、MongoDB 用 API の一意なインデックスを追加または更新することはできません。

## <a name="next-steps"></a>次のステップ

* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用して継続的バックアップ アカウントを復元します。
* [定期的なバックアップから継続的バックアップにアカウントを移行します](migrate-continuous-backup.md)。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
