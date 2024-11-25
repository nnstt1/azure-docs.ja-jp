---
title: Microsoft ID プラットフォームの進化 - Azure
description: Azure Active Directory (Azure AD) の ID サービスおよび開発者プラットフォームの進化版である Microsoft ID プラットフォームについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3e60b68ccb23980ee14b39ab62c59700e6d97690
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155498"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Microsoft ID プラットフォームの進化

[Microsoft ID プラットフォーム](../develop/index.yml)は、Azure Active Directory (Azure AD) 開発者プラットフォームの進化版です。 これにより、ユーザーをサインインし、Microsoft API (Microsoft Graph) や開発者が構築した API を呼び出すためのトークンを取得するアプリケーションを開発者がビルドできます。 これは、認証サービス、オープン ソース ライブラリ、(開発者ポータルとアプリケーション API による) アプリケーションの登録と構成、完全な開発者向けドキュメント、クイックスタート サンプル、コード サンプル、チュートリアル、ハウツー ガイド、その他の開発者向けコンテンツによって構成されています。 Microsoft ID プラットフォームでは、OAuth 2.0 や OpenID Connect など業界標準のプロトコルがサポートされています。

これまで、多くの開発者は Azure AD v1.0 プラットフォームを使用して (Azure AD によりプロビジョニングされた) 職場や学校のアカウントを認証してきました。これは、Azure AD Authentication Library (ADAL)、アプリケーションの登録と構成用の Azure portal、プログラムによるアプリケーション構成用の Microsoft Graph API を使用して、Azure AD v1.0 エンドポイントからトークンを要求することによって行われていました。

統合 Microsoft ID プラットフォーム (v2.0) を使用すると、コードを一回記述すれば、自分のアプリケーションで Microsoft のあらゆる ID を認証できます。 いくつかのプラットフォームでは、完全にサポートされているオープンソースの Microsoft Authentication Library (MSAL) を ID プラットフォームのエンドポイントに使用することが推奨されます。 MSAL は簡単に使えて、ユーザーに優れたシングル サインオン (SSO) 機能を提供します。開発者は Microsoft Secure Development Lifecycle (SDL) を使用して開発し、高い信頼性とパフォーマンスを実現できます。 API を呼び出すとき、増分同意を活用するようにアプリケーションを構成できます。これにより、実行時にアプリケーションの使用がこれを保証するまで、同意要求をより侵略的な範囲で遅延させることができます。  MSAL では Azure Active Directory B2C もサポートされるため、顧客は、好みのソーシャル、エンタープライズ、またはローカル アカウント ID を使用して、アプリケーションや API にシングル サインオン アクセスできます。

Microsoft ID プラットフォームを使用すれば、次のような種類のユーザーにも利用してもらうことができます。

- 職場または学校のアカウント (Azure AD によりプロビジョニングされるアカウント)
- 個人アカウント (Outlook.com や Hotmail.com など)
- MSAL や Azure AD B2C から自分のメールやソーシャル ID (LinkedIn、Facebook、Google など) を持ち込むカスタマー

Azure portal を使用してアプリケーションを登録し、構成したり、プログラミングによるアプリケーションの構成に Microsoft Graph API を使用したりできます。

自分のペースでアプリケーションを更新します。 ADAL ライブラリでビルドされたアプリケーションは引き続きサポートされます。 ADAL ライブラリでビルドされたアプリケーションと MSAL ライブラリでビルドされたアプリケーションから構成される混在アプリケーション ポートフォリオもサポートされます。 つまり、最新の ADAL と最新の MSAL を利用するアプリケーションは、これらのライブラリ間で共有トークン キャッシュにより、ポートフォリオをまたいで SSO を提供します。 ADAL から MSAL に更新されたアプリケーションは、アップグレード時、ユーザーのサインイン状態を維持します。

## <a name="microsoft-identity-platform-experience"></a>Microsoft ID プラットフォーム エクスペリエンス

次の図は、アプリ登録エクスペリエンス、SDK、エンドポイント、サポートされている ID など、高レベルでの Microsoft ID エクスペリエンスを示しています。

![現在の Microsoft ID プラットフォーム](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>アプリの登録エクスペリエンス

Azure portal **[アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)** エクスペリエンスでは、Microsoft ID プラットフォームと統合したすべてのアプリケーションを 1 つのポータルで管理します。 アプリケーション登録ポータルを使用した場合、代わりに Azure portal アプリの登録エクスペリエンスを最初に使用して開始します。

Azure AD B2C との統合では (ソーシャルまたはローカル ID を認証する場合)、Azure AD B2C テナントにアプリケーションを登録する必要があります。 このエクスペリエンスも Azure portal の一部です。

[アプリケーション API](/graph/api/resources/application) を使用して、Microsoft のあらゆる ID を認証するよう、Microsoft ID プラットフォームと統合されたアプリケーションをプログラミングで構成します。

### <a name="msal-libraries"></a>MSAL ライブラリ

MSAL ライブラリを使用し、すべての Microsoft ID を認証するアプリケーションをビルドできます。 .NET と JavaScript の MSAL ライブラリは一般提供されています。 iOS と Android の MSAL ライブラリはプレビュー段階であり、運用環境での使用に適しています。 プレビュー段階の MSAL ライブラリには一般公開されているバージョンの MSAL と ADAL と同じ運用レベルのサポートを提供しています。

MSAL ライブラリを利用し、アプリケーションを Azure AD B2C と統合することもできます。

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft ID プラットフォーム エンドポイント

Microsoft ID プラットフォーム (v2.0) エンドポイントは OIDC によって認定されています。 Microsoft Authentication Libraries (MSAL) またはその他の標準準拠ライブラリと連動します。 業界標準に準拠し、人間が読めるスコープを実装します。

## <a name="next-steps"></a>次のステップ

[Microsoft ID プラットフォームのドキュメント](../develop/index.yml)で詳細を確認してください。
