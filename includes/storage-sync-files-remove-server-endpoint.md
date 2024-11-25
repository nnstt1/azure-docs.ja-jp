---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ad8eaf1c5d347beaa87ce05bdbf5efa2eef18fc9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353986"
---
いいえ。サーバー エンドポイントの削除は、サーバーの再起動とは異なります。 サーバー エンドポイントを削除して再作成する操作が、同期、クラウドの階層化などの Azure File Sync の側面に関する問題を解決するために適した解決策になることはほぼありません。サーバー エンドポイントの削除は、破壊的な操作です。 階層化されたファイルがサーバー エンドポイントの名前空間の外部に存在する場合、データが失われる可能性があります。 詳細については、「[階層化されたファイルがサーバー エンドポイント名前空間の外部に存在するのはなぜですか](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)」を参照してください。 または、サーバーエンド ポイント名前空間内に存在する階層化されたファイルのファイルにアクセスできなくなる可能性があります。 このような問題は、サーバー エンドポイントを再作成しても解決しません。 クラウドの階層化を有効にしていない場合でも、階層化されたファイルがサーバー エンドポイントの名前空間内に存在する可能性があります。 そのため、特定のフォルダーで Azure File Sync の使用を停止したい場合や、Microsoft エンジニアから明示的に指示された場合を除き、サーバー エンドポイントを削除しないことをお勧めします。 サーバー エンドポイントの削除の詳細については、「[サーバー エンドポイントを削除する](../articles/storage/file-sync/file-sync-server-endpoint-delete.md)」を参照してください。
