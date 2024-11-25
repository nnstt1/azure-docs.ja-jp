---
title: Azure Active Directory アプリケーション プロキシと Qlik Sense
description: Azure Active Directory アプリケーション プロキシと Qlik Sense を統合します。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 825ec4927a1db3011560a221868816a1e6089a35
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988888"
---
# <a name="azure-active-directory-application-proxy-and-qlik-sense"></a>Azure Active Directory アプリケーション プロキシと Qlik Sense 
Azure Active Directory アプリケーション プロキシと Qlik Sense は一緒に連携動作し、アプリケーション プロキシを使用して Qlik Sense 配置用のリモート アクセスを容易に提供できるようにします。  

## <a name="prerequisites"></a>前提条件 
このシナリオの残りは、以下を終えていることが前提となっています。
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) を構成した。 
- [アプリケーション プロキシ コネクタをインストールした](../app-proxy/application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>アプリケーションを Azure に発行する 
QlikSense を発行するには、Azure で 2 つのアプリケーションを発行する必要があります。  

### <a name="application-1"></a>アプリケーション 1: 
アプリを公開するには、次の手順に従います。 手順 1 ～ 8 の詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../app-proxy/application-proxy-add-on-premises-application.md)」を参照してください。 


1. Azure Portal にグローバル管理者としてサインインします。 
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。 
3. ブレード上部の **[追加]** を選択します。 
4. **[オンプレミスのアプリケーション]** を選択します。 
5. 新しいアプリに関する情報を必須フィールドに入力します。 次のガイダンスに従って設定してください。 
   - **[内部 URL]**: このアプリケーションは、QlikSense URL そのものである内部 URL を持っている必要があります。 たとえば、**https&#58;//demo.qlikemm.com:4244** です。 
   - **[事前認証方法]**: Azure Active Directory (推奨ですが必須ではありません) 
1. ブレード下部の **[追加]** を選択します。 アプリケーションが追加されて、クイック スタート メニューが表示されます。 
2. クイック スタート メニューで **[テスト用のユーザーを割り当てる]** を選択し、少なくとも 1 ユーザーをアプリケーションに追加します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。 
3. **[割り当て]** を選択して、テスト ユーザーの割り当てを保存します。 
4. (オプション) アプリの管理ブレードで [シングル サインオン] を選択します。 ドロップダウン メニューから **[Kerberos の制約付き委任]** を選択し、Qlik 構成に基づいて必要なフィールドに記入します。 **[保存]** を選択します。 

### <a name="application-2"></a>アプリケーション 2: 
次の例外を除き、アプリケーション 1 の場合と同じ手順に従います。 

**手順 5.**: 内部 URL は、アプリケーションによって使用される認証ポートを持つ QlikSense URL になっているはずです。 2018 年 4 月以前の QlikSense リリースの既定値は、HTTPS の場合は **4244**、HTTP の場合は **4248** です。 2018 年 4 月以降の QlikSense リリースの既定値は、HTTPS の場合は **443**、HTTP の場合は **80** です。  例 **https&#58;//demo.qlik.com:4244**</br></br>
**手順 10.:** SSO を設定しないで、**シングルサインオンを無効** のままにします。
 
 
## <a name="testing"></a>テスト 
これでアプリケーションをテストする準備ができました。 アプリケーション 1 で QlikSense を発行するために使用した外部 URL にアクセスし、割り当てられているユーザーとして、両方のアプリケーションにログインします。  

## <a name="additional-references"></a>その他の参照情報
アプリケーション プロキシを使用した Qlik Sense の発行の詳細については、次の Qlik コミュニティの記事を参照してください。 
- [Kerberos の制約付き委任と Qlik Sense を使用した統合 Windows 認証での Azure AD](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense と Azure AD アプリケーション プロキシの統合](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>次のステップ

- [アプリケーション プロキシを使用してアプリケーションを発行する](../app-proxy/application-proxy-add-on-premises-application.md)
- [アプリケーション プロキシ コネクタの使用方法](../app-proxy/application-proxy-connector-groups.md)
