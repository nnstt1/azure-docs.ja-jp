---
title: モバイル アプリの準備 - Web API を実稼働用に準備する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法について説明します。 (アプリを実稼働用に準備します。)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7756029d4b3ed43f55c86fa12128e1943c99b6bb
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778388"
---
# <a name="prepare-mobile-apps-for-production"></a>モバイル アプリを実稼働用に準備する

この記事では、運用環境に移行する前にモバイル アプリの品質と信頼性を向上させる方法に関する詳細情報を提供します。

## <a name="handle-errors"></a>エラーの処理

モバイル アプリを実稼働用に準備するときに、いくつかのエラー状態が発生する可能性があります。 処理する主なケースは、サイレント障害と対話へのフォールバックです。 また、ネットワークが利用できない状況、サービスの停止、管理者の同意の要件、およびその他のシナリオ固有のケースなどの他の条件についても、検討する必要があります。

Microsoft Authentication Library (MSAL) の種類ごとに、エラー状態の処理方法を説明するサンプル コードと wiki コンテンツを見つけることができます。

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

追加のサンプルを試すには、[モバイル パブリック クライアント アプリケーション](sample-v2-code.md#mobile)に関する記事を参照してください。