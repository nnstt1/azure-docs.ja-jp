---
title: Azure SQL データ同期のベスト プラクティス
description: Azure SQL データ同期の構成と実行に関するベスト プラクティスについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: sql-data-sync
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MaraSteiu
ms.author: masteiu
ms.reviewer: mathoma
ms.date: 12/20/2018
ms.openlocfilehash: 7b18b7ddcb6c2f737263cdaa7dd3e51cbde3da52
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715037"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Azure SQL データ同期のベスト プラクティス 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、Azure SQL データ同期のベスト プラクティスについて説明します。

SQL データ同期の概要については、[Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](sql-data-sync-data-sql-server-sql-database.md)に関する記事を参照してください。

> [!IMPORTANT]
> 現時点では、Azure SQL データ同期で Azure SQL Managed Instance はサポート **されていません**。

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>セキュリティと信頼性

### <a name="client-agent"></a>クライアント エージェント

-   ネットワーク サービスのアクセス許可を持つ最低限必要な特権ユーザー アカウントを使用して、クライアント エージェントをインストールします。  
-   SQL Server コンピューターではないコンピューターに、クライアント エージェントをインストールします。  
-   オンプレミス データベースを複数のエージェントに登録しないでください。    
    -   これは、同期グループが異なるさまざまなテーブルを同期する場合でも、行わないでください。  
    -   オンプレミス データベースを複数のクライアント エージェントに登録すると、同期グループのいずれかを削除したときに問題が発生します。

### <a name="database-accounts-with-least-required-privileges"></a>最低限必要な特権が付与されたデータベース アカウント

-   **同期のセットアップ**。 テーブルの作成/変更、データベースの変更、プロシージャの作成、スキーマの選択/変更、ユーザー定義型の作成。

-   **継続的な同期**。同期の対象として選択したテーブルと同期メタデータおよび追跡テーブルでの選択/挿入/更新/削除、サービスによって作成されたストアド プロシージャに対する実行アクセス許可、ユーザー定義テーブル型に対する実行アクセス許可。

-   **プロビジョニング解除**。 同期に含まれるテーブルでの変更、同期メタデータ テーブルでの選択/削除、同期追跡テーブル、ストアド プロシージャ、およびユーザー定義型の制御。

Azure SQL Database では、単一の資格情報セットのみをサポートしています。 この制約内でこれらのタスクを行うため、次のオプションを検討してください。

-   フェーズごとに資格情報を変更します (たとえば、セットアップには *credentials1* を使用し、継続的な同期には *credentials2* を使用します)。  
-   資格情報のアクセス許可を変更します (つまり、同期の設定後にアクセス許可を変更します)。

### <a name="auditing"></a>監査

同期グループ内のデータベース レベルで監査を有効にすることをお勧めします。 [Azure SQL データベースの監査を有効にする](./auditing-overview.md)方法、または[SQL Server データベースの監査を有効にする](/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15)方法について説明します。

## <a name="setup"></a>セットアップ

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>データベースの考慮事項と制約

#### <a name="database-size"></a>データベース サイズ

新しいデータベースを作成するときは、デプロイするデータベースよりも常に大きいサイズになるように最大サイズを設定します。 デプロイするデータベースより大きい最大サイズを設定していない場合、同期は失敗します。 SQL データ同期では自動拡張が行われませんが、`ALTER DATABASE` コマンドを実行して、作成後にデータベースのサイズを増やすことができます。 データベースのサイズが確実に制限を超えないようにしてください。

> [!IMPORTANT]
> SQL データ同期では、各データベースで追加のメタデータを格納します。 必要な領域を計算するときに、このメタデータを必ず考慮してください。 追加されるオーバーヘッドの量は、テーブルの幅 (たとえば、幅が狭いテーブルでは必要なオーバーヘッドが増えます) とトラフィックの量に関係しています。

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>テーブルの考慮事項と制約

#### <a name="selecting-tables"></a>テーブルの選択

データベース内のすべてのテーブルを同期グループに含める必要はありません。 同期グループに含めるテーブルは、効率性とコストに影響します。 業務上必要な場合のみ、テーブルと、そのテーブルが依存しているテーブルを同期グループに含めます。

#### <a name="primary-keys"></a>主キー

同期グループ内の各テーブルには主キーが必要です。 SQL データ同期は、主キーを持たないテーブルと同期できません。

運用環境で SQL データ同期を使用する前に、初期同期と継続的な同期のパフォーマンスをテストしてください。

#### <a name="empty-tables-provide-the-best-performance"></a>空のテーブルが最高のパフォーマンスを提供する

初期化時には空のテーブルが最高のパフォーマンスを提供します。 ターゲット テーブルが空の場合、データ同期は一括挿入を使用してデータを読み込みます。 それ以外の場合、データ同期は行単位の比較と挿入を行って競合を確認します。 ただし、パフォーマンスが重要でない場合は、既にデータが含まれているテーブル間の同期を設定することができます。

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>同期先データベースをプロビジョニングする

SQL データ同期は、データベースの基本的な自動プロビジョニングを提供します。

このセクションでは、SQL データ同期のプロビジョニングの制限事項について説明します。

#### <a name="autoprovisioning-limitations"></a>自動プロビジョニングの制限事項

SQL データ同期には、自動プロビジョニングについて次のような制限事項があります。

-   選択できるのは同期先テーブルに作成された列のみです。 同期グループに含まれていない列はすべて、同期先テーブルにプロビジョニングされません。
-   選択した列のインデックスだけが作成されます。 同期元テーブルのインデックスに同期グループに含まれていない列がある場合、それらのインデックスは同期先テーブルにプロビジョニングされません。  
-   XML 型の列のインデックスはプロビジョニングされません。  
-   CHECK 制約はプロビジョニングされません。  
-   同期元テーブルの既存のトリガーはプロビジョニングされません。  
-   ビューとストアド プロシージャは同期先データベースに作成されません。
-   外部キー制約の UPDATE CASCADE および ON DELETE CASCADE アクションは、同期先テーブルに再作成されません。
-   有効桁数が 28 を超える decimal 列または numeric 列がある場合、SQL データ同期では同期中に変換オーバーフローの問題が発生する可能性があります。decimal 列または numeric 列の有効桁数は 28 未満に制限することをお勧めします。

#### <a name="recommendations"></a>Recommendations

-   サービスをテストする場合にのみ、SQL データ同期の自動プロビジョニング機能を使用します。  
-   運用環境では、データベース スキーマをプロビジョニングします。

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>ハブ データベースを配置する場所

#### <a name="enterprise-to-cloud-scenario"></a>企業とクラウド間のシナリオ

待機時間を最小限に抑えるために、同期グループのデータベース トラフィックが最も集中する場所の近くにハブ データベースを保持します。

#### <a name="cloud-to-cloud-scenario"></a>クラウド間のシナリオ

-   同期グループのすべてのデータベースが 1 つのデータセンターにある場合は、同じデータセンターにハブを配置します。 この構成により、待機時間と、データセンター間でのデータ転送のコストが削減されます。
-   同期グループのデータベースが複数のデータセンターにある場合は、大多数のデータベースおよびデータベース トラフィックと同じデータセンターにハブを配置します。

#### <a name="mixed-scenarios"></a>混合シナリオ

企業とクラウドの間のシナリオと、クラウド間のシナリオが混在している場合など、複雑な同期グループ構成になっている場合に、上記のガイドラインを適用します。

## <a name="sync"></a>同期

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>時間とコストがかかる初期同期の回避

このセクションでは、同期グループの初期同期について説明します。 初期同期に余計な時間がかかったり、必要以上にコストがかかったりしないようにする方法を説明します。

#### <a name="how-initial-sync-works"></a>初期同期のしくみ

同期グループを作成する場合、最初は 1 つのデータベースのデータだけを使用します。 複数のデータベースにデータがあると、SQL データ同期は、解決が必要な競合としてそれぞれの行を扱います。 この競合の解決のために、初期同期に時間がかかります。 複数のデータベースにデータがあると、データベースのサイズによって、初期同期に数日や数か月かかる場合があります。

複数のデータベースが別々のデータセンターにある場合、それぞれの行が異なるデータセンター間を行き来することになります。 これにより、初期同期のコストが増加します。

#### <a name="recommendation"></a>推奨

可能であれば、最初は同期グループのいずれかのデータベースのデータだけを使用します。

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>同期ループを避ける設計

同期ループは、同期グループ内で循環参照がある場合に発生します。 このシナリオでは、1 つのデータベースにおける各変更が、同期グループ内の複数のデータベース間で際限なく循環的にレプリケートされます。   

パフォーマンスの低下とコストの大幅な増加を引き起こす可能性があるため、必ず同期ループを回避するようにしてください。

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>反映されない変更

#### <a name="reasons-that-changes-fail-to-propagate"></a>変更が反映されない理由

次のいずれかの原因で、変更が反映されない場合があります。

-   スキーマまたはデータ型に互換性がない。
-   null 非許容列に null 値を挿入している。
-   外部キー制約に違反している。

#### <a name="what-happens-when-changes-fail-to-propagate"></a>変更が反映されないとどうなるか

-   同期グループが **[警告]** 状態で表示されます。
-   ポータル UI のログ ビューアーに詳細が表示されます。
-   45 日経っても問題が解決しない場合、データベースは最新ではなくなります。

> [!NOTE]
> これらの変更が反映されることはありません。 このシナリオにおいて回復する唯一の方法は、同期グループを再作成することです。

#### <a name="recommendation"></a>推奨

ポータルとログ インターフェイスを使用して、同期グループとデータベースの正常性を定期的に監視します。


## <a name="maintenance"></a>メンテナンス

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>データベースと同期グループが古くならないようにする

同期グループまたは同期グループ内のデータベースが最新でなくなる場合があります。 同期グループの状態が **[Out-of-date]\(最新ではありません\)** の場合、同期グループは機能しなくなります。 データベースの状態が **[Out-of-date]\(最新ではありません\)** の場合、データが失われる可能性があります。 このシナリオからの回復を試みるよりも、このシナリオを回避することをお勧めします。

#### <a name="avoid-out-of-date-databases"></a>データベースが古くならないようにする

データベースが 45 日以上オフラインになっている場合、データベースの状態が **[Out-of-date]\(最新ではありません\)** に設定されます。 データベースが **[Out-of-date]\(最新ではありません\)** の状態になることを回避するために、45 日以上オフラインになっているデータベースがないことを確認します。

#### <a name="avoid-out-of-date-sync-groups"></a>同期グループが古くならないようにする

同期グループ内での変更が、45 日以上経っても同期グループの残りのデータベースに反映されていない場合、同期グループの状態が **[Out-of-date]\(最新ではありません\)** に設定されます。 同期グループが **[Out-of-date]\(最新ではありません\)** の状態になることを回避するために、同期グループの履歴ログを定期的に確認します。 すべての競合が解決され、同期グループのデータベース全体に変更が正常に反映されていることを確認します。

次のいずれかの原因で、同期グループに変更が反映されない場合があります。

-   テーブル間でスキーマに互換性がない。
-   テーブル間でデータに互換性がない。
-   null 値が許可されていない列に、null 値が含まれた行が挿入されている。
-   外部キー制約に違反する値で行が更新されている。

同期グループが古くなることを防ぐには、次のことを行います。

-   スキーマを更新して、失敗した行に含まれている値を許可します。
-   外部キーの値を更新して、失敗した行に含まれている値を含めます。
-   失敗した行のデータ値を更新して、ターゲット データベースのスキーマまたは外部キーと互換性を持たせます。

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>プロビジョニング解除の問題を回避する

状況によっては、クライアント エージェントへのデータベースの登録を解除すると、同期に失敗することがあります。

#### <a name="scenario"></a>シナリオ

1. SQL データベース インスタンスと、ローカル エージェント 1 に関連付けられている SQL Server データベースを使用して、同期グループ A が作成されました。
2. 同じオンプレミス データベースがローカル エージェント 2 に登録されています (このエージェントは、どの同期グループにも関連付けられていません)。
3. ローカル エージェント 2 からオンプレミス データベースの登録を解除すると、オンプレミス データベースの同期グループ A の追跡およびメタ テーブルが削除されます。
4. 同期グループ A の操作は、"The current operation could not be completed because the database is not provisioned for sync or you do not have permissions to the sync configuration tables" (データベースが同期用にプロビジョニングされていないか、同期構成テーブルへのアクセス許可がないため、現在の操作を完了できませんでした) というエラーが表示されて失敗します。

#### <a name="solution"></a>解決策

このシナリオを回避するには、1 つのデータベースを複数のエージェントに登録しないようにします。

このシナリオから回復するには、次の手順に従います。

1. データベースが属している各同期グループからデータベースを削除します。  
2. データベースを削除した各同期グループにデータベースを戻します。  
3. 影響を受けた各同期グループをデプロイします (この操作によりデータベースをプロビジョニングします)。  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>同期グループを変更する

同期グループからデータベースを削除した後、変更のいずれかをデプロイするまで同期グループを編集しないでください。

その代わりにまず、同期グループからデータベースを削除します。 次に、この変更をデプロイし、プロビジョニング解除が完了するまで待ちます。 プロビジョニング解除が完了したら、同期グループを編集して変更をデプロイできます。

データベースを削除した後、変更のいずれかをデプロイする前に同期グループを編集すると、いずれか一方の操作が失敗します。 ポータルのインターフェイスが不整合な状態になる場合があります。 その場合は、ページを更新することで正しい状態を復元できます。

### <a name="avoid-schema-refresh-timeout"></a>スキーマ更新のタイムアウトを回避する

複雑なスキーマを同期する場合、同期メタデータ データベースの SKU が低いと (例: Basic)、スキーマの更新時に "操作のタイムアウト" が発生する可能性があります。 

#### <a name="solution"></a>解決策

この問題を軽減するには、同期メタデータ データベースを S3 などの上位の SKU にスケールアップします。 

## <a name="next-steps"></a>次のステップ
SQL データ同期の詳細については、以下を参照してください。

-   概要 - [Azure SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-data-sync-data-sql-server-sql-database.md)
-   SQL データ同期の設定
    - ポータル - [チュートリアル:Azure SQL Database と SQL Server の間でデータを同期するように SQL データ同期を設定する](sql-data-sync-sql-server-configure.md)
    - PowerShell の場合
        -  [PowerShell を使用して Azure SQL Database の複数データベース間で同期する](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell を使用して SQL Database と SQL Server インスタンスのデータベース間で同期する](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   データ同期エージェント - [Azure SQL データ同期のデータ同期エージェント](sql-data-sync-agent-overview.md)
-   監視 - [Azure Monitor ログによる SQL データ同期の監視](./monitor-tune-overview.md)
-   トラブルシューティング - [Azure SQL データ同期に関する問題のトラブルシューティング](sql-data-sync-troubleshoot.md)
-   同期スキーマの更新
    -   Transact-SQL の場合 - [Azure SQL データ同期内でスキーマ変更のレプリケートを自動化する](sql-data-sync-update-sync-schema.md)
    -   PowerShell の場合 - [PowerShell を使用して、既存の同期グループの同期スキーマを更新する](scripts/update-sync-schema-in-sync-group.md)

SQL Database の詳細については、以下をご覧ください。

-   [SQL Database の概要](sql-database-paas-overview.md)
-   [データベースのライフサイクル管理](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))