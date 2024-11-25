---
title: 制限 - Azure Database for PostgreSQL - Single Server
description: この記事では、接続数やストレージ エンジンのオプションなど、Azure Database for PostgreSQL - Single Server の制限について説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: d9d817077b1bdfb0bd53ec18f25def1c9615d2fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736409"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の制限
次のセクションでは、データベース サービス容量と機能の制限について説明します。 リソース (コンピューティング、メモリ、ストレージ) 層の詳細については、[価格レベル](concepts-pricing-tiers.md)の記事を参照してください。


## <a name="maximum-connections"></a>最大接続数
価格レベルと仮想コアごとの最大接続数は以下のとおりです。 Azure システムでは、Azure Database for PostgreSQL サーバーを監視する 5 つの接続が必要です。 

|**価格レベル**| **仮想コア数**| **最大接続数** | **最大ユーザー接続** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|General Purpose| 2| 150| 145|
|General Purpose| 4| 250| 245|
|General Purpose| 8| 480| 475|
|General Purpose| 16| 950| 945|
|General Purpose| 32| 1500| 1495|
|General Purpose| 64| 1900| 1895|
|メモリ最適化| 2| 300| 295|
|メモリ最適化| 4| 500| 495|
|メモリ最適化| 8| 960| 955|
|メモリ最適化| 16| 1900| 1895|
|メモリ最適化| 32| 1987| 1982|

接続数が制限を超えると、次のエラーが表示される場合があります。
> FATAL:  sorry, too many clients already

> [!IMPORTANT]
> 最適なエクスペリエンスを得るために、pgBouncer のような接続プーラーを使用して、接続を効率的に管理することをお勧めします。

PostgreSQL 接続はアイドル状態でも、約 10 MB のメモリを占有する可能性があります。 また、新しい接続の作成には時間もかかります。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 アイドル状態の接続を減らして既存の接続を再利用する接続プーラーは、これを回避するのに役立ちます。 詳細については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)を参照してください。

## <a name="functional-limitations"></a>機能制限
### <a name="scale-operations"></a>スケール操作
- Basic 価格レベルとの間の動的スケーリングは現在サポートされていません。
- 現在、サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./howto-migrate-using-dump-and-restore.md)します。

> PostgreSQL バージョン 10 より前は、[PostgreSQL のバージョン管理ポリシー](https://www.postgresql.org/support/versioning/)では、1 番目 _または_ 2 番目の番号が増えることが _メジャー バージョン_ のアップグレードと見なされました (たとえば、9.5 から 9.6 への変更は、_メジャー_ バージョンのアップグレードと見なされました)。
> バージョン 10 以降、1 番目の番号の変更のみメジャー バージョンのアップグレードと見なされます (たとえば、10.0 から 10.1 への変更は _マイナー_ バージョンのアップグレードであり、10 から 11 への変更は _メジャー_ バージョンのアップグレードです)。

### <a name="vnet-service-endpoints"></a>VNet サービス エンドポイント
- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

### <a name="restoring-a-server"></a>サーバーの復元
- PITR 機能を使うと、基になっているサーバーと同じ価格レベルの構成で新しいサーバーが作成されます。
- 復元中に作成される新しいサーバーには、元のサーバーに存在するファイアウォール規則はありません。 この新しいサーバー用のファイアウォール規則を個別に設定する必要があります。
- 削除されたサーバーへの復元はサポートされていません。

### <a name="utf-8-characters-on-windows"></a>Windows での UTF-8 文字
- 一部のシナリオにおいて、Windows 上のオープン ソース PostgreSQL では UTF-8 文字が完全にはサポートされません。これは、Azure Database for PostgreSQL に影響するためです。 詳細については、[PostgreSQL アーカイブ内の Bug #15476](https://www.postgresql.org/message-id/2101.1541220270%40sss.pgh.pa.us) のスレッドを参照してください。

### <a name="gss-error"></a>GSS エラー
**GSS** に関連するエラーが表示される場合は、Azure Postgres Single Server でまだ完全にサポートされていない新しいクライアントまたはドライバーのバージョンを使用している可能性があります。 このエラーは、[JDBC ドライバー バージョン 42.2.15 および 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868) に影響することがわかっています。
   - 更新は 11 月末までに完了する予定です。 それまでの間は、動作するドライバー バージョンを使用することを検討してください。
   - または、GSS 要求を無効にすることを検討してください。  `gssEncMode=disable` のような接続パラメーターを使用します。

### <a name="storage-size-reduction"></a>ストレージ サイズの削減
ストレージ サイズを小さくすることはできません。 必要なストレージ サイズで新しいサーバーを作成し、手動[ダンプと復元](./howto-migrate-using-dump-and-restore.md)を実行して、データベースを新しいサーバーに移行する必要があります。

## <a name="next-steps"></a>次のステップ
- [各価格レベルで使用できる内容](concepts-pricing-tiers.md)について理解します
- [サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)について学習します
- [Azure Portal を使用して Azure Database for PostgreSQL でサーバーをバックアップして復元する方法](howto-restore-server-portal.md)を確認します
