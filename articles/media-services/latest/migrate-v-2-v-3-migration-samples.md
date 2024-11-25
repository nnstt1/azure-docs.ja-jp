---
title: Media Services v2 から v3 への移行でのサンプルの比較
description: Azure Media Services v2 と v3 とのコードの違いを比較するのに役立つ一連のサンプルです。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: cdfad63bbf70d40ddb78ac5708ce684d12f57698
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797581"
---
# <a name="media-services-migration-code-sample-comparison"></a>Media Services 移行のコード サンプルの比較

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

## <a name="compare-the-sdks"></a>SDK を比較する

いくつかのコード サンプルを使用して、SDK 間の処理方法を比較することができます。

## <a name="samples-for-comparison"></a>比較のサンプル

次の表は、一般的なシナリオで v2 と v3 を比較するためのサンプルを示しています。

|シナリオ|v2 API|v3 API|
|---|---|---|
|アセットの作成とファイルのアップロード |[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|ジョブの送信|[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>まず変換を作成し、次にジョブを送信する方法を示しています。|
|AES 暗号化を使用してアセットを公開する |1.`ContentKeyAuthorizationPolicyOption` を作成します。<br/>2.`ContentKeyAuthorizationPolicy` を作成します。<br/>3.`AssetDeliveryPolicy` を作成します。<br/>4.`Asset` を作成してコンテンツをアップロードするか、`Job` を送信して `OutputAsset` を使用します。<br/>5.`AssetDeliveryPolicy` を `Asset` に関連付けます。<br/>6.`ContentKey` を作成します。<br/>7.`ContentKey` を `Asset` にアタッチします。<br/>8.`AccessPolicy` を作成します。<br/>9.`Locator` を作成します。<br/><br/>[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1.`ContentKeyPolicy` を作成します。<br/>2.`Asset` を作成します。<br/>3.コンテンツをアップロードするか、`Asset` を `JobOutput` として使用します。<br/>4.`StreamingLocator` を作成します。<br/><br/>[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|ジョブの詳細を取得し、ジョブを管理する |[v2 を使用したジョブの管理](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[v3 を使用したジョブの管理](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|
