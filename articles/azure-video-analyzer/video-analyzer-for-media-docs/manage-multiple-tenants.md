---
title: Azure Video Analyzer for Media (旧称 Video Indexer) を使用して複数のテナントを管理する - Azure
description: この記事では、Azure Video Analyzer for Media (旧称 Video Indexer) を使用して複数のテナントを管理するためのさまざまな統合オプションについて説明します。
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: d771c6add9b9b9b3484a15ee630b20f94cba7e4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673922"
---
# <a name="manage-multiple-tenants"></a>複数のテナントの管理

この記事では、Azure Video Analyzer for Media (旧称 Video Indexer) を使用して複数のテナントを管理するためのさまざまなオプションについて説明します。 自分のシナリオに最も適した方法を選択してください。

* テナントごとの Video Analyzer for Media アカウント
* すべてのテナントに対する単一の Video Analyzer for Media アカウント
* テナントごとの Azure サブスクリプション

## <a name="video-analyzer-for-media-account-per-tenant"></a>テナントごとの Video Analyzer for Media アカウント

このアーキテクチャを使用すると、テナントごとに Video Analyzer for Media アカウントが作成されます。 テナントは、永続的なコンピューティング レイヤーで完全に分離されます。  

![テナントごとの Video Analyzer for Media アカウント](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>考慮事項

* お客様は、(お客様が手動で構成しない限り) ストレージ アカウントを共有することはありません。
* お客様は、コンピューティング (予約ユニット) を共有せず、相互のジョブ処理時間に影響を与えることはありません。
* Video Analyzer for Media アカウントを削除することによって、システムからテナントを簡単に削除することができます。
* テナント間でカスタム モデルを共有する機能はありません。

    カスタム モデルを共有するビジネス要件がないことを確認してください。
* テナントごとに複数の Video Analyzer for Media (および関連する Media Services) アカウントがあるため、管理が困難になります。

> [!TIP]
> [Video Indexer 開発者ポータル](https://api-portal.videoindexer.ai/)でシステムの管理者ユーザーを作成し、Authorization API を使用して、関連する[アカウント アクセス トークン](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)をテナントに提供します。

## <a name="single-video-analyzer-for-media-account-for-all-users"></a>すべてのユーザーに対する単一の Video Analyzer for Media アカウント

このアーキテクチャを使用する場合は、お客様がテナントの分離を担当します。 すべてのテナントで、1 つの Video Analyzer for Media アカウントと 1 つの Azure Media Service アカウントを使用する必要があります。 コンテンツをアップロード、検索、削除するとき、お客様はそのテナントに対して適切な結果になるようにフィルター処理を行う必要があります。

![すべてのユーザーに対する単一の Video Analyzer for Media アカウント](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

このオプションでは、テナントによってモデルをフィルター処理することにより、テナント間でカスタマイズ モデル (ユーザー、言語、ブランド) を共有または分離することができます。

[ビデオをアップロードする](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)ときに、テナントごとに異なるパーティション属性を指定することができます。 これにより、[検索 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) での分離が可能になります。 検索 API でパーティション属性を指定することにより、指定したパーティションのみの結果を取得します。 

### <a name="considerations"></a>考慮事項

* テナント間でコンテンツとカスタマイズ モデルを共有する機能。
* あるテナントによって他のテナントのパフォーマンスに影響があります。
* お客様は、Video Analyzer for Media の上に複雑な管理レイヤーを構築する必要があります。

> [!TIP]
> [優先順位](upload-index-videos.md)属性を使用して、テナント ジョブの優先順位を設定できます。

## <a name="azure-subscription-per-tenant"></a>テナントごとの Azure サブスクリプション 

このアーキテクチャを使用するときは、テナントごとに専用の Azure サブスクリプションを使用します。 ユーザーごとに、テナント サブスクリプションに新しい Video Analyzer for Media アカウントを作成します。

![テナントごとの Azure サブスクリプション](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>考慮事項

* これは、課金を分離できる唯一のオプションです。
* この統合により、テナントごとの Video Analyzer for Media アカウントより管理オーバーヘッドが増加します。 課金の要件がない場合は、この記事で説明した他のオプションのいずれかを使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

[概要](video-indexer-overview.md)
