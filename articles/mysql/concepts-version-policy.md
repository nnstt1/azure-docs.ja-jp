---
title: バージョン サポート ポリシー - Azure Database for MySQL - シングル サーバーとフレキシブル サーバー (プレビュー)
description: Azure Database for MySQL における MySQL のメジャーおよびマイナー バージョンに関するポリシーについて説明します
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 11/03/2020
ms.openlocfilehash: 248234c816cc3341929417282521d30c2105c671
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323255"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL バージョン サポート ポリシー

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

このページでは、Azure Database for MySQL のバージョン管理ポリシーについて説明します。これは、Azure Database for MySQL - シングル サーバーと Azure Database for MySQL - フレキシブル サーバー (プレビュー) のデプロイ モードに適用できます。

## <a name="supported-mysql-versions"></a>サポートされる MySQL のバージョン

Azure Database for MySQL は、InnoDB ストレージ エンジンを使用して [MySQL Community Edition](https://www.mysql.com/products/community/) から開発されました。 このサービスは、コミュニティでサポートされている現在のメジャー バージョン (MySQL 5.6、5.7、8.0) をすべてサポートしています。 MySQL では、X.Y.Z の名前付けスキームが使用されています。ここで、X はメジャー バージョン、Y はマイナー バージョン、Z はバグ修正リリースです。 スキームの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)をご覧ください。

Azure Database for MySQL では、現在、次の MySQL メジャーおよびマイナー バージョンがサポートされています。

| Version | [単一サーバー](overview.md) <br/> 現行のマイナー バージョン |[フレキシブル サーバー](./flexible-server/overview.md) <br/> 現行のマイナー バージョン  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL バージョン 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (廃止) | サポートされていません|
|MySQL バージョン 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL バージョン 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> シングルサーバー デプロイ オプションでは、ゲートウェイを使用してサーバー インスタンスに接続がリダイレクトされます。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。 アプリケーションに、特定のメジャー バージョン (たとえば、v5.7 や v8.0) に接続する要件がある場合は、[こちら](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)のドキュメントで説明されているように、サーバー接続文字列のポートを変更することによって実行できます。

> [!IMPORTANT]
> 単一サーバー上の MySQL v5.6 は、2021 年 2 月に廃止されました。 2021 年 9 月 1 日以降は、Azure Database for MySQL - 単一サーバー デプロイ オプションで新しい v5.6 サーバーを作成できません。 ただし、特定の時点への回復を実行し、既存のサーバーの読み取りレプリカを作成することはできます。

[バージョン サポート ポリシーのドキュメント](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)で、廃止されたバージョンのバージョン サポート ポリシーを参照してください。

## <a name="major-version-support"></a>メジャー バージョンのサポート

MySQL の各メジャー バージョンは、[バージョン管理ポリシー](https://www.mysql.com/support/eol-notice.html)に記載されているように、Azure によってバージョンのサポートが開始される日付から MySQL コミュニティによってバージョンが提供終了されるまで、Azure Database for MySQL によってサポートされます。

## <a name="minor-version-support"></a>マイナー バージョンのサポート

Azure Database for MySQL では、定期的なメンテナンスの一環として、Azure で優先される MySQL バージョンへのマイナー バージョン アップグレードが自動的に実行されます。 

## <a name="major-version-retirement-policy"></a>メジャー バージョンの提供終了ポリシー

次の表は、MySQL メジャー バージョンの提供終了の詳細を示しています。 日付は、[MySQL バージョン管理ポリシー](https://www.mysql.com/support/eol-notice.html)に従っています。

| Version | 新着記事 | Azure サポートの開始日 | 提供終了日|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [機能](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018 年 3 月 20 日 | 2021 年 2 月
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [機能](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018 年 3 月 20 日 | 2023 年 10 月
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [機能](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 2019 年 12 月 11 日 | 2026 年 4 月

## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Azure Database for MySQL でサポートされていない、提供終了した MySQL エンジンのバージョン

各 MySQL データベース バージョンの提供終了日後も、提供終了したバージョンを引き続き実行する場合は、次の制限事項に注意してください。

- コミュニティではバグの修正やセキュリティの修正プログラムがリリースされることはないため、Azure Database for MySQL には、提供終了したデータベース エンジンにバグやセキュリティ上の問題に対応するパッチが適用されたり、提供終了したデータベース エンジンに関してセキュリティ対策が実施されたりすることはありません。 ただし、Azure では、ホスト、OS、コンテナー、およびその他のサービス関連のコンポーネントに対して、定期的なメンテナンスとパッチの適用が引き続き実行されます。
- MySQL データベースに関連するサポートの問題が発生した場合、Microsoft ではサポートを提供できない場合があります。 このような場合、Microsoft がサポートを提供するには、お客様がデータベースをアップグレードする必要があります。
- 提供終了したバージョンの新しいデータベース サーバーを作成することはできません。 ただし、特定の時点への回復を実行し、既存のサーバーの読み取りレプリカを作成することはできます。
- Azure Database for MySQL で開発された新しいサービス機能は、サポートされているデータベース サーバーのバージョンでのみ使用できます。
- アップタイム SLA は、Azure Database for MySQL のサービス関連の問題に対してのみ適用され、データベース エンジンに関連するバグに起因するダウンタイムには適用されません。  
- サービスに深刻な脅威を及ぼす MySQL データベース エンジンの脆弱性が廃止されたデータベース バージョンに見つかった場合、Azure はサービスをセキュリティで保護するため、ユーザーのデータベース サーバーの計算ノードを停止する場合があります。 サーバーを稼働させる前に、サーバーをアップグレードするように求められます。 アップグレード プロセス中、データは常にサービスで実行される自動バックアップを使用して保護されます。これを使用すると、必要に応じて古いバージョンに戻すことができます。 

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL - シングル サーバーで[サポートされるバージョン](./concepts-supported-versions.md)を参照してください
- Azure Database for MySQL - フレキシブル サーバーで[サポートされるバージョン](flexible-server/concepts-supported-versions.md)を参照してください。
- アップグレードを実行するには、MySQL の[ダンプと復元](./concepts-migrate-dump-restore.md)に関するページを参照してください。