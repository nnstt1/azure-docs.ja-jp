---
title: 動的グループ メンバーシップのルールを検証する (プレビュー) - Azure AD | Microsoft Docs
description: Azure Active Directory の動的グループのメンバーシップ ルールに対してメンバーをテストする方法。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b12c1c69f160a51fbcf5650a3ebe30045584106
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471252"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Azure Active Directory で動的グループ メンバーシップ ルールを検証する (プレビュー)

Azure Active Directory (Azure AD) では、動的グループ ルールを検証する手段 (パブリック プレビュー) が提供されるようになりました。 **[ルールの検証]** タブで、サンプル グループ メンバーに対して動的ルールを検証し、ルールが想定どおりに機能していることを確認できます。 動的グループ ルールの作成時または更新時に、管理者は、ユーザーまたはデバイスがグループのメンバーであるかどうかを確認できます。 これは、ユーザーまたはデバイスがルールの条件を満たしているかどうかを評価し、メンバーシップが期待できない場合のトラブルシューティングを支援するのに役立ちます。

## <a name="prerequisites"></a>前提条件
動的グループ規則のメンバーシップの評価機能を使用するには、管理者が、グローバル管理者、グループ管理者、または Intune 管理者のいずれかの規則を直接割り当てられている必要があります。

> [!TIP]
> 間接的なグループ メンバーシップを使用して必要なロールの 1 つを割り当てることは、まだサポートされていません。
>

## <a name="step-by-step-walk-through"></a>ステップバイステップのチュートリアル

開始するには、 **[Azure Active Directory]**  >  **[グループ]** に移動します。 既存の動的グループを選択するか、新しい動的グループを作成して、[動的メンバーシップ ルール] をクリックします。 **[ルールの検証]** タブが表示されます。

![[ルールの検証] タブを見つけて既存のルールで開始する](./media/groups-dynamic-rule-validation/validate-tab.png)

**[ルールの検証]** タブで、メンバーシップを検証するユーザーを選択します。 一度に選択できるユーザーまたはデバイスの数は 20 です。

![既存のルールに対して検証するユーザーを追加する](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

ピッカーからユーザーまたはデバイスを選択し、 **[選択]** を選択すると、検証が自動的に開始されて検証結果が表示されます。

![ルール検証の結果を表示する](./media/groups-dynamic-rule-validation/validate-tab-results.png)

結果によって、ユーザーがグループのメンバーであるかどうかがわかります。 ルールが有効でない場合、またはネットワークの問題が発生した場合、結果は **[不明]** として表示されます。 **[不明]** が表示された場合、詳細なエラー メッセージに、問題と必要なアクションが示されます。

![ルールの検証結果の詳細を表示する](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

ルールを変更すると、メンバーシップの検証がトリガーされます。 ユーザーがグループのメンバーではない理由を確認するには、[詳細の表示] をクリックすると、ルールを構成するそれぞれの式の結果が検証の詳細に表示されます。 **[OK]** をクリックして終了します。

## <a name="next-steps"></a>次のステップ

- [グループの動的メンバーシップ ルール](groups-dynamic-membership.md)
