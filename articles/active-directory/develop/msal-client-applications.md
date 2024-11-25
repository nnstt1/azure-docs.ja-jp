---
title: パブリックおよび機密クライアント アプリ (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) でのパブリック クライアント アプリケーションと機密クライアント アプリケーションについて説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 03c14170a1093913fe69af2407f587835a2bb76a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440927"
---
# <a name="public-client-and-confidential-client-applications"></a>パブリック クライアント アプリケーションと機密クライアント アプリケーション

Microsoft Authentication Library (MSAL) には、2 種類のクライアントが定義されています。パブリック クライアントと機密クライアントです。 その 2 種類のクライアントは、認証サーバーを使用してセキュリティを確保した認証を行い、クライアント資格情報の機密性を維持する能力によって区別されます。 これに対し、Azure Active Directory 認証ライブラリ (ADAL) では、"_認証コンテキスト_" (Azure Active Directory への接続) と呼ばれるものが使用されます。

- **機密クライアント アプリケーション** は、サーバー上で実行されるアプリ (Web アプリ、Web API アプリ、さらにはサービス アプリやデーモン アプリ) です。 これらはアクセスが困難であると考えられているため、アプリケーション シークレットを保持することができます。 機密クライアントは、構成時のシークレットを保持することができます。 クライアントの各インスタンスに個別の構成 (クライアント ID とクライアント シークレットを含む) があります。 これらの値を、エンド ユーザーが抽出することは困難です。 Web アプリは最も一般的な機密クライアントです。 クライアント ID は Web ブラウザーを介して公開されますが、シークレットはバック チャネルでのみ渡され、直接公開されることはありません。

  機密クライアント アプリ: 

  ![Web アプリ](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![デーモンまたはサービス](media/msal-client-applications/daemon-service.png)

- **パブリック クライアント アプリケーション** は、デバイスまたはデスクトップ コンピューターあるいは Web ブラウザーで実行されるアプリです。 それらにはアプリケーション シークレットを安全に保持するという信頼がないため、ユーザーに代わって Web API にアクセスすることしかできません  (パブリック クライアント フローのみがサポートされます)。パブリック クライアントは構成時のシークレットを保持できません。そのため、クライアント シークレットを備えていません。

  パブリック クライアント アプリ: 

  ![デスクトップ アプリ](media/msal-client-applications/desktop-app.png) ![ブラウザーレス API](media/msal-client-applications/browserless-app.png) ![モバイル アプリ](media/msal-client-applications/mobile-app.png)

MSAL.js では、パブリック クライアント アプリと機密クライアント アプリは分離されません。 MSAL.js ではクライアント アプリをユーザー エージェントベースのアプリ (クライアント コードが Web ブラウザーなどのユーザー エージェント内で実行されるパブリック クライアント) として表します。 これらのクライアントでは、シークレットは格納されません。ブラウザーのコンテキストが公開されアクセス可能であるからです。

## <a name="comparing-the-client-types"></a>クライアントの種類の比較

パブリック クライアント アプリと機密クライアント アプリの共通点および相違点をいくつか以下に示します。

- どちらの種類のアプリもユーザー トークン キャッシュを保持しており、トークンを暗黙的に取得できます (トークンが既にトークン キャッシュ内にある場合)。 機密クライアント アプリには、アプリ自体のためのトークン用のアプリ トークン キャッシュもあります。
- どちらの種類のアプリでもユーザー アカウントを管理し、ユーザー トークン キャッシュからアカウントを取得することや、識別子からアカウントを取得すること、またはアカウントを削除することができます。
- パブリック クライアント アプリには、トークンを取得する方法が 4 つあります (4 つの認証フロー)。 機密クライアント アプリには、トークンを取得する方法が 3 つ (また、ID プロバイダー承認エンドポイントの URL を計算する方法が 1 つ) あります。 詳細については、[トークンの取得](msal-acquire-cache-tokens.md)に関するページを参照してください。

MSAL ではクライアント ID ("_アプリケーション ID_" または "_アプリ ID_" とも呼ばれる) がアプリケーションの構築時に 1 回渡されます。 アプリがトークンを取得する際にこれが再度渡される必要はありません。 これは、パブリックと機密の両方のクライアント アプリに当てはまります。 機密クライアント アプリのコンストラクターにも、ID プロバイダーと共有するシークレットであるクライアント資格情報が渡されます。

## <a name="next-steps"></a>次のステップ

アプリケーションの構成とインスタンス化の詳細については、以下を参照してください。

- [クライアント アプリケーション構成オプション](msal-client-application-configuration.md)
- [MSAL.NET を使用したクライアント アプリケーションのインスタンス化](msal-net-initializing-client-applications.md)
- [MSAL.js を使用したクライアント アプリケーションのインスタンス化](msal-js-initializing-client-applications.md)
