---
title: 2 段階認証 Azure AD MFA と ADFS - Azure Active Directory
description: Azure AD MFA および AD FS を開始する方法について説明する Azure AD Multi-Factor Authentication のページです。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9abc9e2e2ca2b9305cccf5cec554994c3703ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214506"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Azure AD Multi-Factor Authentication と Active Directory フェデレーション サービスの概要

<center>

![Azure AD MFA と ADFS の概要](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

組織がオンプレミスの Active Directory と Azure Active Directory を AD FS を使ってフェデレーションする場合、Azure AD Multi-Factor Authentication の使用には 2 つの選択肢があります。

* Azure AD Multi-Factor Authentication または Active Directory フェデレーション サービスを使用したクラウド リソースのセキュリティ保護
* Azure Multi-Factor Authentication Server を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護

Azure AD Multi-Factor Authentication と AD FS を使用したリソースのセキュリティ保護の認証エクスペリエンスを次の表に要約します

| 認証エクスペリエンス - ブラウザー ベースのアプリ | 認証エクスペリエンス - 非ブラウザー ベースのアプリ |
|:--- |:--- |
| Azure AD Multi-Factor Authentication を使用した Azure AD リソースのセキュリティ保護 |<li>認証の第 1 段階は、AD FS を使用してオンプレミスで実行されます。</li> <li>第 2 段階は、クラウド認証を使用して実行される電話ベースの方式です。</li> |
| Active Directory フェデレーション サービスを使用した Azure AD リソースのセキュリティ保護 |<li>認証の第 1 段階は、AD FS を使用してオンプレミスで実行されます。</li><li>第 2 段階は、要求を受け入れることによりオンプレミスで実行されます。</li> |

フェデレーション ユーザーに対するアプリ パスワードに関する注意事項があります。

* アプリ パスワードはクラウド認証を使用して検証されるため、フェデレーションはバイパスされます。 フェデレーションは、アプリ パスワードを設定するときにのみアクティブに使用されます。
* オンプレミスのクライアント アクセス制御設定は、アプリ パスワードには適用されません。
* アプリ パスワードでは、オンプレミスの認証ログ機能が失われます。
* ディレクトリ同期のアカウントの無効化/削除には 3 時間かかり、クラウド ID のアプリのパスワードの無効化/削除も遅延します。

AD FS を使用した Azure AD Multi-Factor Authentication または Azure Multi-Factor Authentication Server の設定の詳細については、次の記事を参照してください。

* [Azure AD Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](howto-mfa-adfs.md)
* [Windows Server での Azure Multi-Factor Authentication Server を使用したクラウドとオンプレミスのリソースのセキュリティ保護](howto-mfaserver-adfs-windows-server.md)
* [Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](howto-mfaserver-adfs-2.md)
