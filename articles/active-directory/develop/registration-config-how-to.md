---
title: Azure AD アプリ登録のエンドポイントを取得する
titleSuffix: Microsoft identity platform
description: Azure AD で開発中または登録中のカスタム アプリケーションの認証エンドポイントを見つける方法。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 6d557a35c9e98a8941a94d5871f578101d935580
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153750"
---
# <a name="how-to-discover-endpoints"></a>エンドポイントを検出する方法

アプリケーションの認証エンドポイントは [Azure Portal](https://portal.azure.com) で見つけることができます。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[管理]** で、 **[アプリの登録]** を選択し、上部のメニューで **[エンドポイント]** を選択します。

    テナントのすべての認証エンドポイントが一覧表示された **[エンドポイント]** ページが表示されます。
    
    使用している認証プロトコルに対応するエンドポイントを **アプリケーション (クライアント) ID** と共に使用して、アプリケーション固有の認証要求を作成します。

**各国のクラウド** (Azure AD China、Germany、US Government など) には、独自のアプリ登録ポータルと Azure AD 認証エンドポイントがあります。 詳細については、[各国のクラウドの概要](authentication-national-cloud.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

さまざまな Azure 環境におけるエンドポイントの詳細については、[各国のクラウドの概要](authentication-national-cloud.md)を参照してください。
