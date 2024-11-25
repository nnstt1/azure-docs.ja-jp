---
title: マネージド ID を使用して Azure SQL を Azure Spring Cloud アプリに接続する
description: マネージド ID を設定して Azure SQL を Azure Spring Cloud アプリに接続します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: e950667e1132c8eba350a5ffcada5e5d667a1288
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485643"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>マネージド ID を使用して Azure SQL Database を Azure Spring Cloud アプリに接続する

**この記事の適用対象:** ✔️ Java

この記事では、Azure Spring Cloud アプリ用のマネージド ID を作成し、それを使用して Azure SQL Database にアクセスする方法を説明します。

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、クラウド向けに構築されたインテリジェントでスケーラブルなリレーショナル データベース サービスです。 パフォーマンスと持続性を最適化する、AI を活用した自動機能により、常に最新の状態に保たれます。 サーバーレス コンピューティングと Hyperscale ストレージ オプションを使用することで、リソースが必要に応じて自動的にスケーリングされるため、ストレージ サイズやリソースの管理に煩わされずに、新しいアプリケーションの構築に専念できます。

## <a name="prerequisites"></a>前提条件

* [Spring Data JPA のチュートリアル](/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server)に従って Azure SQL Database をプロビジョニングし、Java アプリでローカルに動作させる
* [Azure Spring Cloud システムで割り当てられたマネージド ID のチュートリアル](./how-to-enable-system-assigned-managed-identity.md)に従い、MI を有効にして Azure Spring Cloud アプリをプロビジョニングする

## <a name="grant-permission-to-the-managed-identity"></a>マネージド ID にアクセス許可を付与する

SQL サーバーに接続して、次の SQL クエリを実行します。

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

この `<MIName>` は、`<service instance name>/apps/<app name>` というルールに従います。たとえば、`myspringcloud/apps/sqldemo` のようになります。 Azure CLI を使用して MIName に対してクエリを実行することもできます。

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>マネージド ID を使用するように Java アプリを構成する

*src/main/resources/application.properties* ファイルを開き、次の行の末尾に `Authentication=ActiveDirectoryMSI;` を追加します。 $AZ _DATABASE_NAME 変数には、必ず正しい値を使用してください。

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>アプリを構築して Azure Spring Cloud にデプロイする

アプリをリビルドし、「前提条件」の 2 番目の箇条書きでプロビジョニングした Azure Spring Cloud アプリにデプロイします。 これで、マネージド ID によって認証された Spring Boot アプリケーションが得られました。これは、JPA を使用して、Azure Spring Cloud で Azure SQL Database のデータを格納および取得します。

## <a name="next-steps"></a>次のステップ

* [Azure Spring Cloud でマネージド ID を使用して Storage Blob にアクセスする方法](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Azure Spring Cloud のアプリケーションのシステム割り当てマネージド ID を有効にする方法](./how-to-enable-system-assigned-managed-identity.md)
* [Azure リソース用マネージド ID の詳細](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [GitHub Actions で Key Vault を使用して Azure Spring Cloud を認証する](./github-actions-key-vault.md)
