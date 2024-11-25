---
title: SAML ベースのシングル サインオンをデバッグする
titleSuffix: Azure AD
description: Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグします。
services: active-directory
ms.author: davidmu
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: ergreenl
ms.openlocfilehash: 588c5d7149a88ef56692cc59db9c14f244c7b6d2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553156"
---
# <a name="debug-saml-based-single-sign-on-to-applications"></a>アプリケーションに対する SAML に基づいたシングル サインオンをデバッグする

SAML ベースのシングル サインオンを使用する Azure Active Directory (Azure AD) のアプリケーションについて、[シングル サインオン](what-is-single-sign-on.md)の問題を見つけて修正する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

[マイ アプリによるセキュリティで保護されたサインイン拡張機能](https://support.microsoft.com/account-billing/troubleshoot-problems-with-the-my-apps-portal-d228da80-fcb7-479c-b960-a1e2535cbdff#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)をインストールすることをお勧めします。 このブラウザー拡張機能により、シングル サインオンに関する問題の解決に必要な SAML 要求および SAML 応答の情報を収集しやすくなります。 拡張機能をインストールできない場合でも、この記事では、拡張機能がインストールされている場合とされていない場合の両方について、問題を解決する方法が示されています。

マイ アプリによるセキュリティで保護されたサインイン拡張機能ををダウンロードしてインストールするには、次のいずれかのリンクを使用します。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://microsoftedge.microsoft.com/addons/detail/my-apps-secure-signin-ex/gaaceiggkkiffbfdpmfapegoiohkiipl)

## <a name="test-saml-based-single-sign-on"></a>SAML に基づいたシングル サインオンをテストする

Azure AD と対象アプリケーションの間の、SAML に基づいたシングル サインオンをテストするには:

1. グローバル管理者またはアプリケーションを管理する権限があるその他の管理者として、[Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のブレードで **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
1. [エンタープライズ アプリケーション] の一覧で、シングル サインオンをテストするアプリケーションを選択し、左側のオプションの **[シングル サインオン]** を選択します。
1. SAML ベースのシングル サインオン テスト エクスペリエンスを開くには、 **[シングル サインオンのテスト]** (手順 5) に進みます。 **[テスト]** ボタンが淡色表示される場合は、まず、 **[基本的な SAML 構成]** セクションで必須の属性を入力して保存する必要があります。
1. **[シングル サインオンのテスト]** ブレードで、会社の資格情報を使用して対象のアプリケーションにサインインします。 現在のユーザーまたは別のユーザーとしてサインインできます。 別のユーザーとしてサインインする場合は、認証するよう求めるプロンプトが表示されます。

    ![SAML SSO ページのテストを示すスクリーンショット](./media/debug-saml-sso-issues/test-single-sign-on.png)

正常にサインインしている場合は、テストに合格しました。 この場合、Azure AD がアプリケーションに、SAML 応答トークンを発行しました。 アプリケーションはこの SAML トークンを使用して、正常にユーザーをサインインさせました。

会社のサインイン ページまたはアプリケーションのページでエラーが発生する場合は、以降のセクションのいずれかに従ってエラーを解決します。

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>会社のサインイン ページでのサインイン エラーを解決する

サインインしようとすると、会社のサインイン ページに次の例のようなエラーが表示されることがあります。

![会社のサインイン ページのエラーを示す例](./media/debug-saml-sso-issues/error.png)

このエラーをデバッグするには、エラー メッセージと SAML 要求が必要です。 マイ アプリによるセキュリティで保護されたサインイン拡張機能は、この情報を自動的に収集し、Azure AD に解決ガイダンスを表示します。

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>インストールしたマイ アプリによるセキュリティで保護されたサインイン拡張機能を使用してサインインのエラーを解決するには

1. エラーが発生すると、拡張機能によって、ユーザーには Azure AD の **[シングル サインオンのテスト]** ブレードが表示されます。
1. **[シングル サインオンのテスト]** ブレードで、 **[SAML 要求をダウンロードします]** を選択します。
1. エラーと SAML 要求内の値に基づいて、具体的な解決ガイダンスが表示されます。
1. Azure AD の構成を自動的に更新して問題を解決する **[修正する]** ボタンが表示されます。 このボタンが表示されない場合、サインインの問題は Azure AD の不適切な構成が原因ではありません。

サインイン エラーの解決策が表示されない場合は、フィードバック ボックスを使用して Microsoft に問い合わせることをお勧めします。

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>MyApps Secure Sign-in 拡張機能をインストールせずにエラーを解決するには

1. ページの右下隅のエラー メッセージをコピーします。 エラー メッセージには以下が含まれています。
    - CorrelationID とタイムスタンプ。 これらの値は、Microsoft のエンジニアが問題を識別して、実際の問題に対する正確な解決策を提供する助けとなるため、Microsoft とのサポート ケースを作成するときに重要です。
    - 問題の根本原因を明らかにしている文章。
1. Azure AD に戻り、 **[シングル サインオンのテスト]** ブレードを見つけます。
1. **[Get resolution guidance]** (解決ガイダンスの取得) の上にあるテキスト ボックスに、エラー メッセージを貼り付けます。
1. **[Get resolution guidance]** (解決ガイダンスの取得) をクリックし、問題を解決するための手順を表示します。 ガイダンスには、SAML 要求または SAML 応答からの情報が必要な場合があります。 マイ アプリによるセキュリティで保護されたサインイン拡張機能を使用していない場合は、SAML の要求や応答を取得するために [Fiddler](https://www.telerik.com/fiddler) などのツールが必要なことがあります。
1. SAML 要求に含まれる送信先が、Azure AD から取得した SAML シングル サインオン サービス URL に対応していることを確認します。
1. SAML 要求に含まれる発行者は、Azure AD のアプリケーションのために構成した識別子と同じです。 Azure AD は、発行者を使用してディレクトリ内のアプリケーションを検索します。
1. AssertionConsumerServiceURL は、アプリケーションが Azure AD から SAML トークンを受け取ることになっている場所であることを確認します。 この値は Azure AD 内で構成できますが、SAML 要求の一部としては必須の値ではありません。

## <a name="resolve-a-sign-in-error-on-the-application-page"></a>アプリケーションのページでサインイン エラーを解決する

正常にサインインしてから、アプリケーションのページにエラーが表示される場合があります。 これは、Azure AD はアプリケーションにトークンを発行したのに、アプリケーションが応答を受け付けないときに発生します。

このエラーを解決するには、次の手順に従います。または、こちらの [Azure AD を使用して SAML SSO のトラブルシューティングを行う方法についての短いビデオ](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)をご覧ください。

1. アプリケーションが Azure AD ギャラリー内にある場合は、アプリケーションを Azure AD と統合するすべての手順に従ったことを確認します。 アプリケーションの統合手順については、[SaaS アプリケーションの統合に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)を参照してください。
1. SAML 応答を取得します。
    - マイ アプリによるセキュリティで保護されたサインイン拡張機能がインストールされている場合、 **[シングル サインオンのテスト]** ブレードで、 **[download the SAML response]** (SAML 応答のダウンロード) をクリックします。
    - この拡張機能がインストールされていない場合は、[Fiddler](https://www.telerik.com/fiddler) などのツールを使用して SAML 応答を取得します。
1. SAML 応答トークン内の以下の要素に注目します。
   - ユーザーの一意の識別子である NameID の値形式
   - そのトークンで発行された要求
   - トークンの署名に使用された証明書。

     SAML 応答の詳細については、「[シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)」を参照してください。

1. SAML 応答の内容を確認したので、問題の解決方法のガイダンスについて、「[サインイン後、アプリケーションのページでエラーが発生する](application-sign-in-problem-application-error.md)」を参照してください。
1. まだ正常にサインインできない場合は、SAML 応答には何が不足しているか、アプリケーション ベンダーに問い合わせることができます。

## <a name="next-steps"></a>次のステップ

アプリケーションに対してシングル サインオンが動作するようになったので、[SaaS アプリケーションに対するユーザーのプロビジョニングとプロビジョニング解除を自動化](../app-provisioning/user-provisioning.md)するか、[条件付きアクセスを使ってみる](../conditional-access/app-based-conditional-access.md)ことができます。