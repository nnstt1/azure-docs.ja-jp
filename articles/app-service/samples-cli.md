---
title: Azure App Service 用の Azure CLI サンプル | Microsoft Docs
description: App Service の一般的なシナリオをピックアップした Azure CLI サンプルをご覧いただけます。 App Service のデプロイまたは管理タスクを自動化する方法について説明します。
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 09/17/2021
ms.custom: mvc, devx-track-azurecli, seo-azure-cli
keywords: Azure CLI サンプル, Azure CLI の例, Azure CLI のコード サンプル
ms.openlocfilehash: 622c26488a3a92cfd63e744246014a7e5bdaf71f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676411"
---
# <a name="cli-samples-for-azure-app-service"></a>Azure App Service の CLI サンプル

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| スクリプト | 説明 |
|-|-|
|**アプリの作成**||
| [アプリを作成し、FTP を使用してファイルをデプロイする](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、FTP を使用してファイルをデプロイします。 |
| [アプリを作成して GitHub からコードをデプロイする](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、パブリックの GitHub リポジトリからコードをデプロイします。 |
| [GitHub からの継続的なデプロイでアプリを作成する](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、所有する GitHub リポジトリから継続的に発行します。 |
| [アプリを作成してローカル Git リポジトリからコードをデプロイする](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、ローカル Git リポジトリからのコードのプッシュを構成します。 |
| [アプリを作成してステージング環境にコードをデプロイする](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | コードの変更をステージングするためのデプロイ スロットを備える App Service アプリを作成します。 |
| [Docker コンテナーに ASP.NET Core アプリを作成する](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Linux 上で App Service アプリを作成し、Docker Hub から Docker イメージを読み込みます。 |
| [アプリを作成してプライベート エンドポイントで公開する](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリとプライベート エンドポイントを作成します |
|**アプリケーションの構成**||
| [アプリにカスタム ドメインをマップする](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、カスタム ドメイン名をマップします。 |
| [カスタム TLS/SSL 証明書をアプリにバインドする](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、カスタム ドメイン名の TLS/SSL 証明書をバインドします。 |
|**アプリのスケール**||
| [アプリを手動でスケーリングする](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、2 つのインスタンス間でスケーリングします。 |
| [高可用性アーキテクチャを使用して世界規模でアプリをスケーリングする](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 2 つの異なる地理的リージョンに 2 つの App Service アプリを作成し、Azure Traffic Manager を使用して、1 つのエンドポイントを介してそれらを利用できるようにします。 |
|**アプリの保護**||
| [Azure Application Gateway との統合](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、サービス エンドポイントとアクセス制限を使用して Application Gateway と統合します。 |
|**アプリのリソースへの接続**||
| [アプリを SQL Database に接続する](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリと Azure SQL Database のデータベースを作成し、データベース接続文字列をアプリ設定に追加します。 |
| [アプリをストレージ アカウントに接続する](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリとストレージ アカウントを作成し、ストレージ接続文字列をアプリ設定に追加します。 |
| [Azure Cache for Redis にアプリを接続する](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリと Azure Cache for Redis を作成し、Redis の接続の詳細をアプリ設定に追加します。 |
| [Cosmos DB にアプリを接続する](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリと Cosmos DB を作成し、Cosmos DB の接続の詳細をアプリ設定に追加します。 |
|**アプリをバックアップおよび復元する**||
| [アプリをバックアップする](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、そのアプリの 1 回限りのバックアップを作成します。 |
| [アプリのスケジュールされたバックアップを作成する](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、そのアプリのスケジュールされたバックアップを作成します。 |
| [アプリをバックアップから復元する](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリをバックアップから復元します。 |
|**アプリの監視**||
| [Web サーバー ログを使用してアプリを監視する](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、ログ記録を有効にし、ログをローカル コンピューターにダウンロードします。 |
| | |
