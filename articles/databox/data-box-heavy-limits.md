---
title: Azure Data Box Heavy の制限 | Microsoft Docs
description: Microsoft Azure Data Box Heavy のコンポーネントと接続に関するシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 06/28/2021
ms.author: alkohli
ms.openlocfilehash: fedd7417f6f199289bfd5c6e11a1cf1bd8c98a82
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113038356"
---
# <a name="azure-data-box-heavy-limits"></a>Azure Data Box Heavy の制限

Azure Data Box Heavy デバイスをデプロイおよび運用する際には、以下の制限事項を考慮してください。 次の表で、Data Box の制限について説明します。


## <a name="data-box-heavy-service-limits"></a>Data Box Heavy サービスの制限

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-heavy-limits"></a>Data Box Heavy の制限

- Data Box Heavy では、ノードあたり最大 10 億ファイルを格納できます。
- Data Box Heavy は、クラウドでノードあたり最大 512 個のコンテナーまたは共有をサポートしています。 ユーザー共有内の最上位レベルのディレクトリは、クラウド内のコンテナーまたは Azure ファイル共有になります。 

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="azure-storage-account-size-limits"></a>Azure ストレージ アカウントのサイズ制限

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure ブロック BLOB、ページ BLOB およびファイルの名前付け規則

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
