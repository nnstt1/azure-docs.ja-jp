---
title: Azure Marketplace アプリケーションの One Click シングル サインオン (SSO) の構成
description: Azure Marketplace のアプリケーションの SSO をワンクリックで構成する手順。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: ergreenl
ms.openlocfilehash: d0e0a9fcb3052132df33d6988468cb218c3d9fc2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556612"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>アプリの One Click シングル サインオンの構成

 このチュートリアルでは、SAML をサポートする Azure Marketplace の Azure Active Directory (Azure AD) アプリケーションの One Click シングル サインオン (SSO) を構成する方法について説明します。

## <a name="introduction-to-one-click-sso"></a>One Click SSO の概要

One Click SSO 機能は、SAML プロトコルをサポートする Azure Marketplace アプリのシングル サインオンを構成するように設計されています。 Azure AD SSO 構成ページでは、このオプションを使用して、お客様がアプリケーション側で Azure AD メタデータを自動的に構成できるようにしています。 この方法により、最小限の手順ですばやく SSO を設定できます。

## <a name="advantages-of-one-click-sso"></a>One Click SSO の利点

- アプリケーション側での手動セットアップを必要とする Azure Marketplace アプリケーションの SSO をすばやく構成。
- より効率的かつ正確な SSO 構成。
- セットアップにパートナーとのやり取りやサポートは不要。 アプリケーションによって SAML 構成用の UI が提供されます。

## <a name="prerequisites"></a>前提条件

- SSO を構成するアプリケーションのアクティブなサブスクリプション。 また、管理者の資格情報も必要です。
- ブラウザーにインストールされた、Microsoft の **My Apps Secure Sign-in 拡張機能**。 詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)」を参照してください。

## <a name="one-click-sso-configuration-steps"></a>One Click SSO の構成手順

1. Azure Marketplace からアプリケーションを追加します。

2. **[シングル サインオン]** を選択します。

3. **[シングル サインオンを有効にする]** を選択します。

4. **[基本的な SAML 構成]** セクションで、必須の構成値を設定します。

    > [!NOTE]
    > アプリケーションに構成する必要があるカスタム要求がある場合は、One Click SSO を実行する前にそれらを処理します。

5. Azure Marketplace アプリケーションで One Click SSO 機能を利用できる場合は、次の画面が表示されます。 場合によっては、 **[拡張機能のインストール]** を選択して、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

   ![My Apps Secure Sign-in ブラウザー拡張機能をインストールする](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. ブラウザーに拡張機能を追加したら、 **[Setup \<Application Name\>]\(<アプリケーション名> の設定\)** を選択します。 アプリケーション管理ポータルにリダイレクトされたら、管理者としてサインインします。

   ![Setup application name (アプリケーション名の設定)](./media/one-click-sso-tutorial/setup-sso.png)

7. ブラウザー拡張機能により、アプリケーションで SSO が自動的に構成されます。 **[はい]** を選択して確認します。

   ![自動設定データの保存](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > アプリケーションの SSO 構成に追加の手順が必要な場合は、指示に従って手順を実行します。

8. 構成が完了したら、 **[OK]** を選択して変更を保存します。

   ![自動設定データを保存する](./media/one-click-sso-tutorial/save-data.png)

9. SSO 設定が正常に構成されたことを知らせる確認ウィンドウが表示されます。

   ![構成された SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 構成が正常に完了したら、アプリケーションからサインアウトし、Azure portal に戻ります。

11. **[テスト]** を選択して、シングル サインオンをテストすることができます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
- [My Apps Secure Sign-in ブラウザー拡張機能の概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)