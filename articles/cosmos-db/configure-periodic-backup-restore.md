---
title: 定期的バックアップを使用して Azure Cosmos DB アカウントを構成する
description: この記事では、バックアップ間隔を指定した定期的バックアップを使用して Azure Cosmos DB アカウントを構成する方法について説明します。 および保有期間。 また、サポートに連絡してデータを復元する方法についても説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/30/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 56e9bfe95a78c8bf0771acdc98c761df9994a708
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221064"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>定期的バックアップを使用して Azure Cosmos DB アカウントを構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB では、データのバックアップが一定の間隔で自動的に取得されます。 自動バックアップは、データベース操作のパフォーマンスや可用性に影響を与えずに取得されます。 すべてのバックアップは別々のストレージ サービス内に個別に保存されます。これらのバックアップは、リージョンの障害からの回復性を確保するためにグローバルにレプリケートされます。 Azure Cosmos DB では、データだけでなく、データのバックアップについても、冗長性とリージョンの障害からの回復性が高められています。 以下の手順は、Azure Cosmos DB によるデータのバックアップ方法を示しています。

* Azure Cosmos DB によって、データベースが 4 時間ごとと任意の時点で自動的に完全バックアップされ、既定では最新の 2 回分のバックアップのみが保存されます。 既定の間隔がワークロードにとって十分でない場合は、Azure portal からバックアップ間隔と保有期間を変更できます。 バックアップ構成は、Azure Cosmos アカウントの作成時または作成後に変更できます。 コンテナーまたはデータベースが削除された場合、Azure Cosmos DB には、特定のコンテナーまたはデータベースの既存のスナップショットが 30 日間保持されます。

* Azure Cosmos DB では、これらのバックアップが Azure Blob Storage に保存される一方、実際のデータは Azure Cosmos DB 内にローカルに存在します。

* 短い待機時間を保証するために、バックアップのスナップショットは、現在の書き込みリージョン (または、マルチリージョン書き込み構成の場合は、書き込みリージョンの **1 つ**) と同じリージョンの Azure Blob Storage に格納されます。 リージョンの障害に対する回復性を確保するために、Azure Blob Storage にあるバックアップ データの各スナップショットが、地理冗長ストレージ (GRS) 経由でもう一度レプリケートされます。 バックアップのレプリケート先のリージョンは、ソース リージョンと、ソース リージョンに関連付けられているリージョン ペアに基づいています。 詳細については、[Azure リージョンの geo 冗長ペアの一覧](../best-practices-availability-paired-regions.md)の記事を参照してください。 このバックアップに直接アクセスすることはできません。 サポート リクエストを通してお客様から要求があると、Azure Cosmos DB チームによりバックアップが復元されます。

  次の図は、3 つのプライマリ物理パーティションがすべて米国西部にある Azure Cosmos コンテナーを、米国西部のリモートの Azure Blob Storage アカウントにバックアップした後、米国東部にレプリケートするようすを示しています。

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="GRS Azure Storage 内のすべての Cosmos DB エンティティの定期的な完全バックアップ。" lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* バックアップは、お使いのアプリケーションのパフォーマンスや可用性に影響を与えずに取得されます。 Azure Cosmos DB では、データのバックアップがバックグラウンドで実行され、プロビジョニング済みのスループット (RU) を余計に消費することも、データベースのパフォーマンスや可用性に影響することもありません。

> [!Note]
> Azure Synapse Link が有効なアカウントの場合、分析ストア データはバックアップと復元に含まれません。 Synapse Link が有効になっている場合、Azure Cosmos DB がトランザクション ストア内のデータを、スケジュールされたバックアップ間隔で引き続き自動的にバックアップします。 現時点では、分析ストア内のデータの自動バックアップと復元はサポートされていません。

## <a name="backup-storage-redundancy"></a><a id="backup-storage-redundancy"></a>バックアップ ストレージの冗長性

既定では、Azure Cosmos DB は、[ペアのリージョン](../best-practices-availability-paired-regions.md)にレプリケートされる geo 冗長 [BLOB ストレージ](../storage/common/storage-redundancy.md)に定期的モードのバックアップ データを格納します。  

Azure Cosmos DB がプロビジョニングされているのと同じリージョンにバックアップ データが保持されるようにするには、既定の geo 冗長バックアップ ストレージを変更し、ローカル冗長またはゾーン冗長のストレージを構成することができます。 ストレージの冗長性メカニズムでは、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止や停電、大規模な自然災害など) からバックアップを保護するため、バックアップのコピーが複数格納されます。

Azure Cosmos DB のバックアップ データは、プライマリ リージョンで 3 回レプリケートされます。 定期的なバックアップ モード用のストレージの冗長性は、アカウントの作成時に構成することも、既存のアカウントに対して更新することもできます。 定期的なバックアップ モードでは、次の 3 つのデータ冗長オプションを使用できます。

* **geo 冗長バックアップ ストレージ:** このオプションは、ペアのリージョン間でデータを非同期的にコピーします。

* **ゾーン冗長バックアップ ストレージ:** このオプションは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間でデータを非同期的にコピーします。

* **ローカル冗長ストレージ:** このオプションは、プライマリ リージョンの 1 つの物理的な場所内で、データを非同期的に 3 回コピーします。

> [!NOTE]
> ゾーン冗長ストレージは現在、[特定のリージョン](high-availability.md#availability-zone-support)でのみ利用できます。 新規アカウント対して選択したリージョンか、既存アカウント用のリージョンかによって異なります。ゾーン冗長オプションは使用できません。
>
> バックアップ ストレージの冗長性を更新しても、バックアップ ストレージの価格には影響しません。

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>バックアップ間隔と保有期間を変更する

Azure Cosmos DB によって、データが 4 時間ごとと任意の時点で自動的に完全バックアップされ、最新の 2 回分のバックアップが保存されます。 この構成は既定のオプションであり、追加コストなしで提供されます。 Azure Cosmos アカウントの作成時またはアカウントの作成後に、既定のバックアップ間隔と保有期間を変更できます。 バックアップ構成は Azure Cosmos アカウント レベルで設定されるので、アカウントごとに構成する必要があります。 アカウントのバックアップ オプションを構成すると、そのアカウント内のすべてのコンテナーに適用されます。 現在、バックアップ オプションは Azure portal でのみ変更できます。

誤ってデータを削除または破損した場合は、**データを復元するためのサポート リクエストを作成する前に、アカウントのバックアップ保有期間を 7 日以上に増やしてください。このイベントの 8 時間以内に保有期間を延長することをお勧めします。** このようにすると、Azure Cosmos DB チームはアカウントを復元するのに十分な時間を確保できます。

### <a name="modify-backup-options-for-an-existing-account"></a>既存アカウントのバックアップ オプションを変更する

既存の Azure Cosmos アカウントの既定のバックアップ オプションを変更するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. Azure Cosmos アカウントに移動して、 **[バックアップと復元]** ペインを開きます。 必要に応じて、バックアップ間隔とバックアップ保有期間を更新します。

   * **バックアップ間隔** - Azure Cosmos DB によってデータのバックアップ作成が試みられる間隔です。 バックアップには時間がかかり、場合によってはダウンストリームの依存関係が原因で失敗する可能性があります。 Azure Cosmos DB では、構成されている間隔でのバックアップ作成が可能な限り試みられますが、その期間内にバックアップが完了することは保証されません。 この値は、時間または分の単位で構成できます。 バックアップ間隔を 1 時間より短く、または 24 時間より長くすることはできません。 この間隔を変更すると、最後のバックアップが作成された時点から新しい間隔が有効になります。

   * **バックアップ保有期間** - 各バックアップが保有される期間を表します。 時間単位または日単位で構成できます。 最小保有期間を、バックアップ間隔 (時間数) の 2 倍より短く、または 720 時間より長くすることはできません。

   * **Copies of data retained (保持するデータのコピー数)** - 既定では、データの 2 つのバックアップ コピーが無料で提供されます。 必要なコピーが 2 つを超える場合、追加料金が発生します。 追加コピーの正確な価格については、[価格ページ](https://azure.microsoft.com/pricing/details/cosmos-db/)の「消費されたストレージ」セクションを参照してください。

   * **バックアップ ストレージの冗長性** - 必要なストレージ冗長オプションを選択します。使用可能なオプションについては、「[バックアップ ストレージの冗長性](#backup-storage-redundancy)」セクションをご覧ください。 既定では、既存の定期的なバックアップ モード アカウントに geo 冗長ストレージがあります。 ローカル冗長など他のストレージを選択して、バックアップが別のリージョンにレプリケートされないようにすることができます。 既存のアカウントに加えられた変更は、今後のバックアップのみに適用されます。 既存のアカウントのバックアップ ストレージの冗長性を更新した後は、変更が有効になるまでに最大 2 倍のバックアップ間隔がかかる場合があります。また、**以前のバックアップを直ちに復元するためのアクセス権が失われます**。

   > [!NOTE]
   > バックアップ ストレージの冗長性を構成するには、サブスクリプション レベルで Azure [Cosmos DB アカウントの閲覧者ロール](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)が割り当てられている必要があります。

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-existing-accounts.png" alt-text="既存の Azure Cosmos アカウントのバックアップ間隔、保有期間、ストレージ冗長を構成します。" border="true":::

### <a name="modify-backup-options-for-a-new-account"></a>新しいアカウントのバックアップ オプションを変更する

新しいアカウントをプロビジョニングする場合は、 **[バックアップ ポリシー]** タブで、**定期的** _ バックアップ ポリシーを選択します。 定期的なポリシーでは、バックアップ間隔、バックアップ保有期間、バックアップ ストレージ冗長を構成できます。 たとえば、_ *ローカル冗長バックアップ ストレージ** または **ゾーン冗長バックアップ ストレージ** オプションを選択して、リージョン外でのバックアップ データ レプリケーションを防ぐことができます。

:::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-new-accounts.png" alt-text="新しい Azure Cosmos アカウントに対して定期的または継続的バックアップ ポリシーを構成する。" border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>バックアップからのデータ復元を要求する

データベースまたはコンテナーを誤って削除した場合は、[サポート チケットを申請するか](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)、[Azure サポートに連絡して](https://azure.microsoft.com/support/options/)、自動オンライン バックアップからデータを復元できます。 Azure サポートは、**Standard**、**Developer**、またはそれよりも高度なプランなどの特定のプランでのみ利用できます。 Azure サポートは、**Basic** プランでは利用できません。 さまざまなサポート プランについては、「[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)」ページを参照してください。

バックアップの特定のスナップショットを復元するには、Azure Cosmos DB では、該当のスナップショットのバックアップ サイクル期間中にデータが利用可能であることが求められます。
復元を要請する前に、次の詳細を確認する必要があります。

* サブスクリプション ID を準備ししておきます。

* データが誤って削除または変更された際の方法に基づいて、詳細情報を準備しておく必要があります。 情報を探す時間を最小限に抑えるために、事前に情報を入手しておくことをお勧めします。時間に制約がある一部のケースでは、探す時間が悪影響になる場合があります。

* Azure Cosmos DB アカウント全体が削除された場合は、削除されたアカウントの名前を提示する必要があります。 削除されたアカウントと同じ名前の別のアカウントを作成する場合は、選択する適切なアカウントを判断できるように、サポート チームに報告してください。 削除されたアカウントごとに別のサポート チケットを申請することをお勧めします。そうすることで、復元の状態についての混乱を最小限に抑えられます。

* 1 つ以上のデータベースが削除された場合は、Azure Cosmos アカウントと Azure Cosmos データベース名を入力し、同じ名前の新しいデータベースが存在するかどうかを指定する必要があります。

* 1 つ以上のコンテナーが削除された場合は、Azure Cosmos アカウント名、データベース名、およびコンテナー名を提示する必要があります。 そうすることで、同じ名前のコンテナーが存在するかどうかを明確にします。

* 誤ってデータを削除した場合またはデータが破損した場合は、Azure Cosmos DB チームがバックアップからのデータの復元をサポートできるように、8 時間以内に [Azure サポート](https://azure.microsoft.com/support/options/)に連絡してください。 **データを復元するためのサポート リクエストを作成する前に、アカウントの [バックアップ保有期間を 7 日以上に増やしてください](#configure-backup-interval-retention)。このイベントの 8 時間以内に保有期間を延長することをお勧めします。** このようにすると、Azure Cosmos DB サポート チームはアカウントを復元するのに十分な時間を確保できます。

Azure Cosmos アカウント名、データベース名、コンテナー名に加えて、データが復元可能な特定の時点を指定する必要があります。 その時点で利用可能な最善のバックアップを特定できるように、できるだけ厳密に示すことが重要です。 **また、UTC で時間を指定することも重要です。**

次のスクリーンショットは、Azure portal を使ってデータを復元するコンテナー (コレクション/グラフ/テーブル) に関するサポート リクエストを作成する方法を示しています。 要請の優先度付けに役立つように、データの種類、復元の目的、データが削除された時刻などのその他の詳細を提示します。

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Azure portal を使用してバックアップのサポート リクエストを作成する。" border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>バックアップからのデータ復元に関する考慮事項

データを誤って削除または変更する可能性のあるシナリオには、次のようなものがあります。  

* Azure Cosmos アカウント全体を削除する。

* 1 つ以上の Azure Cosmos データベースを削除する。

* 1 つ以上の Azure Cosmos コンテナーを削除する。

* コンテナー内の Azure Cosmos 項目 (たとえばドキュメント) を削除または変更する。 この特定のケースは一般にデータの破損と呼ばれます。

* 共有オファー データベース、または共有オファー データベース内のコンテナーが、削除されるか破損する。

Azure Cosmos DB では、上記のすべてのシナリオにおいてデータを復元できます。 復元時には、復元されたデータを保持するために新しい Azure Cosmos アカウントが作成されます。 新しいアカウントの名前が指定されていない場合、名前は `<Azure_Cosmos_account_original_name>-restored1` の形式になります。 復元が複数回試行されると、最後の桁がインクリメントされます。 事前に作成した Azure Cosmos アカウントにデータを復元することはできません。

Azure Cosmos アカウントを誤って削除した場合は、そのアカウント名が使用されていなければ、同じ名前の新しいアカウントにデータを復元できます。 そのため、削除した後でアカウントを再作成しないことをお勧めします。 復元されたデータに同じ名前が使用されなくなるだけでなく、復元の基になる適切なアカウントを検出することが難しくなるからです。

Azure Cosmos データベースを誤って削除したときは、データベース全体またはそのデータベース内のコンテナーのサブセットを復元することができます。 また、データベース全体で特定のコンテナーを選択し、それらを新しい Azure Cosmos アカウントに復元することもできます。

コンテナー内の 1 つ以上の項目を誤って削除または変更した場合 (データの破損のケース)、どの時点まで復元するかを指定する必要があります。 データが破損している場合は、時間が重要です。 コンテナーはライブ状態であるため、バックアップはまだ実行中です。したがって、保有期間 (既定では 8 時間) より長く待つと、バックアップが上書きされます。 バックアップが上書きされないようにするには、アカウントのバックアップ保有を少なくとも 7 日間に増やします。 データの破損から 8 時間以内に、データの保有を延長することをお勧めします。

誤ってデータを削除した場合またはデータが破損した場合は、Azure Cosmos DB チームがバックアップからのデータの復元をサポートできるように、8 時間以内に [Azure サポート](https://azure.microsoft.com/support/options/)に連絡してください。 このようにすると、Azure Cosmos DB サポート チームはアカウントを復元するのに十分な時間を確保できます。

> [!NOTE]
> データの復元後、ソースの機能または設定の一部は回復後のアカウントに継承されません。 次の設定は、新しいアカウントに引き継がれません。
> * VNET アクセス制御リスト
> * ストアド プロシージャ、トリガー、ユーザー定義関数
> * マルチリージョン設定  

データベース レベルでスループットをプロビジョニングしている場合、このケースでのバックアップと復元のプロセスは、個々のコンテナー レベルではなく、データベース全体のレベルで行われます。 そのような場合、復元するコンテナーのサブセットを選択することはできません。

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>ポータルから保有または復元を変更するために必要なアクセス許可
[CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)、所有者、または共同作成者のロールの一部であるプリンシパルは、復元を要求したり、保有期間を変更したりできます。

## <a name="understanding-costs-of-extra-backups"></a>追加バックアップのコストについて
2 回のバックアップは無料で提供され、追加のバックアップは、[バックアップ ストレージの料金](https://azure.microsoft.com/pricing/details/cosmos-db/)セクションに記載されている、リージョンに基づくバックアップ ストレージの価格に従って課金されます。 たとえば、バックアップの保有期間が 240 時間 (つまり、10日間)、バックアップ間隔が 24 時間に構成されているとします。 これは、バックアップ データのコピー 10 個を意味します。 米国西部 2 で 1 TB のデータを想定した場合、任意の月のバックアップ ストレージのコストは 0.12 * 1000 * 8 になります。

## <a name="options-to-manage-your-own-backups"></a>独自のバックアップを管理するためのオプション

Azure Cosmos DB SQL API アカウントをお持ちの場合は、次のいずれかの方法を使用して、独自のバックアップを維持することもできます。

* [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) を使用してデータを定期的に任意のストレージに移動します。

* Azure Cosmos DB の[変更フィード](change-feed.md)を使用して、完全バックアップまたは増分変更のデータを定期的に読み取り、独自のストレージに保存します。

## <a name="post-restore-actions"></a>復元後の操作

データ復元の主な目的は、誤って削除または変更したデータを復旧することです。 そのため、最初に、復旧したデータの内容を検査して、必要なデータが含まれているか確認することをお勧めします。 すべて問題ないようであれば、データをプライマリ アカウントに移行し直すことができます。 復元したアカウントを新しいアクティブなアカウントとして使用することはできますが、運用ワークロードが存在する場合のオプションとしてはお勧めしません。 

データを復元した後は、新しいアカウント名 (通常は `<original-name>-restored1` 形式) とアカウントが復元された時刻に関する通知を受け取ります。 復元されたアカウントは、同じプロビジョニング済みのスループットとインデックス作成ポリシーを保持し、元のアカウントと同じリージョン内にあります。 サブスクリプションの管理者または共同管理者になっているユーザーには、復元されたアカウントが表示されます。

### <a name="migrate-data-to-the-original-account"></a>元のアカウントにデータを移行する

以下に、データを元のアカウントに移行し直すさまざまな方法を示します。

* [Azure Cosmos DB データ移行ツール](import-data.md)を使用します。
* [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) を使用します。
* Azure Cosmos DB の[変更フィード](change-feed.md)を使用します。
* 独自のカスタム コードを作成できます。

データの移行後は、コンテナーやデータベースをすぐに削除することをお勧めします。 復元されたデータベースまたはコンテナーを削除しなかった場合は、要求ユニット、ストレージ、およびエグレスに対するコストが発生します。

## <a name="next-steps"></a>次のステップ

* 復元の要請を行うために、Azure サポートに連絡して [Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用して継続的バックアップ アカウントを復元します。
* [定期的なバックアップから継続的バックアップにアカウントを移行します](migrate-continuous-backup.md)。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
