---
title: Azure Portal を使用して OpenAPI 仕様をインポートする | Microsoft Docs
description: API Management を使用して OpenAPI 仕様をインポートし、Azure portal と開発者ポータルで API をテストする方法について説明します。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: danlep
ms.openlocfilehash: 438e779d5eb7718a5cf9b56cf9bbe7404f6861c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679335"
---
# <a name="import-an-openapi-specification"></a>OpenAPI 仕様のインポート

この記事では、 https://conferenceapi.azurewebsites.net?format=json に存在する "OpenAPI の仕様" のバックエンド API をインポートする方法を示します。 このバックエンド API は Microsoft によって提供され、Azure でホストされています。 また、APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * "OpenAPI の仕様" バックエンド API のインポート
> * Azure Portal での API のテスト

## <a name="prerequisites"></a>前提条件

次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>バックエンド API のインポートと公開

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[OpenAPI の仕様]** を選択します。

    ![OpenAPI の仕様](./media/import-api-from-oas/oas-api.png)
3. API 設定を入力します。 値は、作成時に設定することも、後で **[設定]** タブに移動して構成することもできます。設定については、「[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)」のチュートリアルで説明されています。
4. **［作成］** を選択します

> [!NOTE]
> API インポートの制限事項については、[別の記事](api-management-api-import-restrictions.md)で説明されています。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
