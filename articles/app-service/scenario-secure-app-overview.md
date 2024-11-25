---
title: チュートリアル - Azure App Service でセキュリティで保護された Web アプリを構築する | Azure
description: このチュートリアルでは、Azure App Service を使用して Web アプリを構築する方法、認証を有効にする方法、Azure Storage を呼び出す方法、Microsoft Graph を呼び出す方法について説明します。
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/26/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: c6b236c3ce906c5b573db40e1c002924a3e75660
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065515"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>チュートリアル:App Service で認証を有効にし、ストレージと Microsoft Graph にアクセスする

このチュートリアルでは、一般的なアプリケーション シナリオについて説明します。学習内容は次のとおりです。

- [Web アプリの認証を構成](scenario-secure-app-authentication-app-service.md)し、アクセスを組織内のユーザーに制限します。 図の A を参照してください。
- マネージド ID を使用して、Web アプリケーションに代わって [Azure Storage に安全にアクセス](scenario-secure-app-access-storage.md)します。 図の B を参照してください。
- マネージド ID を使用して、[サインインしているユーザーに代わって](scenario-secure-app-access-microsoft-graph-as-user.md)、または [Web アプリケーションに代わって](scenario-secure-app-access-microsoft-graph-as-app.md) Microsoft Graph のデータにアクセスします。 図の C を参照してください。
- このチュートリアル用に作成した[リソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Microsoft ID プラットフォームでのアプリケーション シナリオを示す図。" border="false":::

最初に、Web アプリの認証を有効にする方法について学習します。

> [!div class="nextstepaction"]
> [Web アプリの認証を構成する](scenario-secure-app-authentication-app-service.md)
