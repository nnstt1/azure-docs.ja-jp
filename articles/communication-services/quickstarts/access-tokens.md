---
title: クイックスタート - アクセス トークンを作成して管理する
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services Identity SDK を使用して、ID とアクセス トークンを管理する方法について説明します。
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 4b83fa7aec50d11c6ec4eac0baf2c15b95e29e6c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677287"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>クイック スタート:アクセス トークンを作成して管理する

Communication Services Identity SDK を使用して、Azure Communication Services の使用を開始します。 これを使用することにより、ID を作成し、アクセス トークンを管理できます。 ID は、Azure Communication Services のアプリケーションのエンティティ (ユーザーやデバイスなど) を表します。 アクセス トークンを使用すると、Chat SDK と Calling SDK が Azure Communication Services に対して直接認証を行うことができます。 アクセス トークンは、サーバー側のサービスで生成することをお勧めします。 その後アクセス トークンは、クライアント デバイス上で Communication Services の SDK を初期化するために使用されます。

このチュートリアルを通して画像に表示されている料金は、例示のみを目的としています。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

アプリの出力に、完了した各アクションが表示されます。
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](./create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * ID を管理する
> * アクセス トークンを発行する
> * Communication Services Identity SDK を使用する


> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](./voice-video-calling/getting-started-with-calling.md)

次のことも実行できます。

 - [認証について学習する](../concepts/authentication.md)
 - [チャットをアプリに追加する](./chat/get-started.md)
 - [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
