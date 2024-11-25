---
title: Azure AD で非アクティブなユーザー アカウントを管理する方法 |Microsoft Docs
description: 使われなくなった Azure AD のユーザー アカウントを検出して処理する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/06/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: efc82f409c6d05ea6441747e718ec2ea3c1429a6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131994974"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>方法:Azure AD で非アクティブなユーザー アカウントを管理する

大規模な環境では、従業員が退職したときに、ユーザー アカウントが必ず削除されるとは限りません。 こうした使われなくなったユーザー アカウントはセキュリティ上のリスクとなるため、IT 管理者はそれらを検出して処理する必要があります。

この記事では、Azure AD で不使用になったユーザー アカウントを処理する方法について説明します。 

> [!IMPORTANT]
> Microsoft Graph 内の `/beta` 版の API は変更されることがあります。 実稼働アプリケーションにおけるこれらの API の使用はサポートされていません。 v1.0 で API を使用できるかどうかを確認するには、**バージョン** セレクターを使用します。

## <a name="what-are-inactive-user-accounts"></a>非アクティブなユーザーアカウントとは

非アクティブなアカウントとは、組織のメンバーがリソースにアクセスするためにもう必要としなくなったユーザー アカウントのことです。 非アクティブなアカウントを見極める重要な判断基準の 1 つは、環境へのサインインにそれらが *しばらくの間* 使用されていないことです。 非アクティブなアカウントはサインイン アクティビティに関連付けられているため、最後に成功したサインインのタイムスタンプを使用してそれらを検出できます。 

この方法の課題は、環境について言えば、*しばらくの間* が意味するものをどう定義するかです。 たとえば、ユーザーが休暇中のために、*しばらくの間* 環境にサインインしていない可能性があります。 非アクティブなユーザー アカウントのデルタを定義するときは、環境にサインインしていない正当な理由をすべて考慮に入れる必要があります。 多くの組織では、非アクティブなユーザー アカウントのデルタは 90 ～ 180 日です。 

最後に成功したサインインによって、ユーザーがリソースへのアクセスを継続的に必要とする可能性があるかを的確に判断できます。  グループ メンバーシップまたはアプリへのアクセスがまだ必要とされているか、それとも削除された可能性があるかを判断するのに役立つこともあります。 外部ユーザーの管理では、外部ユーザーがテナント内でまだアクティブのままか、それともクリーンアップされたかを判断できます。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>非アクティブなユーザー アカウントの検出方法

非アクティブなアカウントを検出するには、**Microsoft Graph** API のリソースの種類 **signInActivity** によって表示される **lastSignInDateTime** プロパティを評価します。 **lastSignInDateTime** プロパティは、ユーザーが Azure AD への対話型サインインを正常に実行した最終時刻を表示します。 このプロパティを使用すると、次のシナリオの解決策を実行できます。

- **名前別のユーザー**:このシナリオでは、特定のユーザーを名前で検索することで、lastSignInDateTime を評価できます。`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **日付別のユーザー**:このシナリオでは、指定した日付よりも前の lastSignInDateTime を持つユーザーの一覧を要求します。 `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> すべてのユーザーの最後のサインイン日のレポートを生成する必要がある場合があります。その場合、次のシナリオを使用できます。
> **すべてのユーザーの最後のサインイン日時**: このシナリオでは、すべてのユーザーの一覧と、それぞれのユーザーの最後の lastSignInDateTime を要求します。`https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>知っておくべきこと

ここでは、lastSignInDateTime プロパティについて理解しておく必要がある事項を示します。

### <a name="how-can-i-access-this-property"></a>このプロパティにアクセスするにはどうすればよいですか?

[Microsoft Graph](/graph/overview#whats-in-microsoft-graph) API のリソースの種類 [signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) によって **lastSignInDateTime** プロパティが表示されます。   

> [!NOTE]
> signInActivity Graph API エンドポイントは、米国政府機関 GCC High 環境ではまだサポートされていません。

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Get-AzureAdUser コマンドレットで LastSignInDateTime プロパティを使用できますか?

いいえ。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>このプロパティにアクセスするために必要な Azure AD のエディションは何ですか?

このプロパティにアクセスするには、Azure Active Directory Premium エディションが必要です。

### <a name="what-permission-do-i-need-to-read-the-property"></a>このプロパティを読み取るために必要な権限は何ですか?

このプロパティを読み取るには、次の権限を付与する必要があります。 

- AuditLogs.Read.All
- Organization.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>このプロパティが Azure AD で更新されるのはいつですか?

対話型サインインが正常に実行されるたびに、基になるデータ ストアが更新されます。 通常、成功したサインインは、10 分以内に関連するサインイン レポートに表示されます。
 

### <a name="what-does-a-blank-property-value-mean"></a>空のプロパティ値は何を意味するのでしょうか?

LastSignInDateTime タイムスタンプを生成するには、サインインを成功させる必要があります。 LastSignInDateTime プロパティは新機能であるため、次の場合は lastSignInDateTime プロパティの値が空になる可能性があります。

- 前回成功したユーザーのサインインは、2020 年 4 月より前に行われました。
- 影響を受けたユーザー アカウントが、成功したサインインに使用されたことがありませんでした。

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory レポート API と証明書を使用してデータを取得します](tutorial-access-api-with-certificates.md)
* [監査 API リファレンス](/graph/api/resources/directoryaudit) 
* [サインイン アクティビティ レポート API リファレンス](/graph/api/resources/signin)

