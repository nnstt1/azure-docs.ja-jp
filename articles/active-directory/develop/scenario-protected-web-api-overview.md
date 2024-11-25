---
title: 保護された Web API - 概要
titleSuffix: Microsoft identity platform
description: 保護された Web API を構築する方法 (概要) について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d34d8a361e22f25e82daf0576e65e35af192bdcd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462767"
---
# <a name="scenario-protected-web-api"></a>シナリオ:保護された Web API

このシナリオでは、Web API を公開する方法について説明します。 また、認証されたユーザーのみがアクセスできるように Web API を保護する方法についても説明します。

Web API を使用するには、職場または学校アカウントの両方で認証されたユーザーを有効にするか、Microsoft の個人アカウントを有効にします。

## <a name="specifics"></a>詳細

ここでは、Web API を保護するために必要な特定の情報について説明します。

- お使いのアプリの登録では、少なくとも 1 つの _スコープ_、または 1 つの _アプリケーション ロール_ を公開する必要があります。
  - スコープは、ユーザーの代わりに呼び出される Web API によって公開されます。
  - アプリケーション ロールは、(独自の方法で Web API を呼び出す) デーモン アプリケーションによって呼び出される Web API によって公開されます。
- 新しい Web API アプリの登録を作成する場合は、Web API で受け入れられる[アクセス トークンのバージョン](reference-app-manifest.md#accesstokenacceptedversion-attribute)を `2` に設定します。 レガシ Web API の場合、受け入れられるトークンのバージョンを `null` にできますが、この値により、サインイン対象ユーザーは組織のみに制限されます。個人の Microsoft アカウント (MSA) はサポートされなくなります。
- Web API のコード構成では、Web API が呼び出されたときに使用されるトークンを検証する必要があります。
- コントローラー アクション内のコードでは、トークン内のロールまたはスコープを検証する必要があります。

## <a name="recommended-reading"></a>推奨資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[アプリの登録](scenario-protected-web-api-app-registration.md)」に進みます。
