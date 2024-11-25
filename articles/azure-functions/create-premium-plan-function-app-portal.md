---
title: ポータルでの Azure Functions の Premium プランの作成
description: Azure portal を使用して、Premium プランで実行される関数アプリを作成する方法について説明します。
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: e510eb85cb0e30cd6cd0fcfa1a1979dd421dd266
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573253"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Azure portal で Premium プランの関数アプリを作成する

Azure Functions には、仮想ネットワーク接続とプレミアム ハードウェアを提供し、コールド スタートなしのスケーラブルな Premium プランが用意されています。 詳細については、「[Azure Functions の Premium プラン](functions-premium-plan.md)」を参照してください。 

この記事では、Azure portal を使用して、Premium プランの関数アプリを作成する方法について説明します。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

この時点で、新しい関数アプリに関数を作成できます。 これらの関数では、[Premium プラン](functions-premium-plan.md)の利点を活用できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [HTTP によってトリガーされる関数の追加](./functions-create-function-app-portal.md#create-function)
