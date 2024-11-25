---
title: Azure AD エンタイトルメント管理でのアクセス パッケージのアクセスのレビュー
description: Azure Active Directory アクセス レビューで、エンタイトルメント管理アクセス パッケージのアクセス レビューを行う方法について説明します。
services: active-directory
documentationCenter: ''
author: amsliu
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2021
ms.author: amsliu
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b86ddd01b155b54eaa954df2a67df907901b288
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639681"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でのアクセス パッケージのアクセスのレビュー

企業が Azure AD エンタイトルメント管理を使うと、グループ、アプリケーション、および SharePoint サイトへのアクセスを管理する方法が簡単になります。 この記事では、指定されたレビュー担当者としてアクセス パッケージに割り当てられている他のユーザーに対してアクセス レビューを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

ユーザーのアクティブなアクセス パッケージの割り当てをレビューするには、レビューの作成者がこれらの前提条件を満たしている必要があります。
- Azure AD Premium P2
- グローバル管理者、ID ガバナンス管理者、またはユーザー管理者

詳細については、「[License requirements ライセンスの要件](entitlement-management-overview.md#license-requirements)」を参照してください。

>[!NOTE]
>レビュー担当者は、レビューの作成者が選択することができます (グループの所有者、ユーザーの上司、ユーザー自身、または選択したユーザーまたはグループ)。


## <a name="open-the-access-review"></a>アクセス レビューを開く

アクセス レビューを見つけて開くには、次のステップ実行します。

1. アクセスのレビューを求めるメールが Microsoft から送信される場合があります。 メールを探してアクセス レビューを開きます。 アクセスをレビューするように求めるメールの例を次に示します。
    
    ![アクセス レビューのレビュー担当者のメール](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. **[Review user access]** \(ユーザー アクセスのレビュー) リンクをクリックして、アクセス レビューを開きます。 

1. メールが届いていない場合は、https://myaccess.microsoft.com に直接移動して、保留中のアクセス レビューを見つけることができます。  (米国政府の場合は、代わりに `https://myaccess.microsoft.us` を使用します)。

1. 左側のナビゲーション バーの **[アクセス レビュー]** をクリックして、割り当てられている保留中のアクセス レビューの一覧を表示します。
    
    ![マイ アクセスでアクセス レビューを選択する](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 開始するレビューをクリックします。
    
    ![アクセス レビューを選択する](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>アクセス レビューを実行する

アクセス レビューを開くと、レビューが必要なユーザーの名前が表示されます。 2 つの方法でアクセスを承認または拒否できます。
- 1 名以上のユーザーのアクセスを手動で承認または拒否できます。
- システムの推奨事項を承認することができます。

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>1 名以上のユーザーのアクセスを手動で承認または拒否する
1. ユーザーの一覧を確認し、アクセスを継続する必要があるユーザーを特定します。

    ![レビューするユーザーの一覧](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. アクセスを承認または拒否するには、ユーザー名の左側にあるラジオ ボタンを選択します。

1. ユーザー名の上のバーで **[承認]** または **[拒否]** を選択します。

    ![ユーザーを選択する](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. わからない場合は、**[不明]** ボタンをクリックします。

    このオプションを選択した場合、ユーザーはアクセスを維持し、この選択は監査ログに記録されます。 このログには、レビューを完了している他のレビュー担当者が表示されます。

1. 場合によっては決定の理由を入力する必要があります。 理由を入力し、**[送信]** をクリックします。

    ![アクセスの承認または拒否](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. レビューが終了する前に、いつでも決定を変更することができます。 これを行うには、一覧からユーザーを選択し、決定を変更します。 たとえば、以前に拒否したユーザーのアクセスを承認できます。

レビュー担当者が複数いる場合、最後に送信された応答内容が記録されます。 管理者が Alice と Bob という 2 人のレビュー担当者を指名した場合を考えてみましょう。 最初に Alice がレビューを開いて、アクセスを承認します。 レビューが終了する前に、Bob がレビューを開き、アクセスを拒否します。 この場合、最後のアクセス拒否の決定が記録されます。

>[!NOTE]
>ユーザーは、レビューでアクセスを拒否されても、すぐにアクセス パッケージから削除されるわけではありません。 レビューが閉じられた後にレビュー結果が適用されると、ユーザーはアクセス パッケージから削除されます。 レビューは、管理者が手動でレビューを停止した場合、レビュー期間の最後またはそれ以前に自動的に閉じられます。 

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>システムによって生成された推奨事項を使用してアクセスを承認または拒否する

複数のユーザーのアクセスをより迅速に確認するには、システムによって生成された推奨事項を使用して、1 回のクリックで推奨事項を承認することができます。 推奨事項は、ユーザーのサインイン アクティビティに基づいて生成されます。

1.  ページの上部にあるバーで、**[推奨事項の承認]** をクリックします。
    
    ![推奨事項の承認を選択する](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    推奨されているアクションの概要が表示されます。

1.  **[送信]** をクリックして、推奨事項を承認します。

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージのセルフレビュー](entitlement-management-access-reviews-self-review.md)
