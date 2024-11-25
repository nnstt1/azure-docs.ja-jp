---
title: Azure Active Directory を使用した SSH 認証
description: SSH と Azure Active Directory の統合の実現に関するアーキテクチャ ガイダンス
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1860fd614ab0074896f51a16a268d21f82c11a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049313"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) は、セキュリティで保護されていないネットワーク経由でネットワーク サービスを安全に操作するために暗号化を提供するネットワーク プロトコルです。 SSH は、コマンド ライン サインインを提供し、リモート コマンドを実行し、ファイルを安全に転送します。 Linux®などの Unix ベースのシステムでよく使用されています。 SSH は、セキュリティで保護されていないネットワークで暗号化を提供しない Telnet プロトコルの代わりに使用されます。 

Azure Active Directory (Azure AD) には、Azure で実行されている Linux® ベースのシステム用の仮想マシン (VM) 拡張機能が用意されています。 

## <a name="use-when"></a>使用する状況 

* リモート サインインが必要な Linux® ベースの VM を操作する

* Linux® ベースのシステムでリモート コマンドを実行する

* セキュリティで保護されていないネットワーク内でファイルを安全に転送する

![Azure AD と SSH プロトコルの図](./media/authentication-patterns/ssh-auth.png)

SSH と Azure AD

## <a name="components-of-system"></a>システムのコンポーネント 

* **[ユーザー]** :SSH クライアントを起動して、Linux® VM との接続を設定し、認証用の資格情報を指定します。

* **Web ブラウザー**:ユーザーが操作するコンポーネントです。 ID プロバイダー (Azure AD) と通信して、ユーザーを安全に認証、認可します。

* **SSH クライアント**:接続設定プロセスを駆動します。

* **Azure AD**:デバイス フローを使用してユーザーの ID を認証し、Linux VM に対してトークンを発行します。

* **Linux VM**:トークンを受け入れ、正常な接続を提供します。

## <a name="implement-ssh-with-azure-ad"></a>Azure AD を使用して SSH を実装する 

* [Azure Active Directory の資格情報を使用して Linux® VM にログインする - Azure 仮想マシン](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2.0 デバイス コード フロー - Microsoft ID プラットフォーム](../develop/v2-oauth2-device-code.md)

* [Azure Active Directory との統合 (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)
