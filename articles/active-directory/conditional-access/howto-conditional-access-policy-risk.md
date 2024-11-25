---
title: サインイン リスクベースの条件付きアクセス - Azure Active Directory
description: Identity Protection サインイン リスクを使用して条件付きアクセス ポリシーを作成する
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88c653634b445b83a8d89b93fb91830ac64fffd1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720038"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>条件付きアクセス:サインイン リスクベースの条件付きアクセス

ほとんどのユーザーは、追跡できる正常な動作をしています。この規範から外れた場合は、そのユーザーにサインインを許可すると危険であることがあります。 そのユーザーをブロックしたり、多要素認証を実行してユーザーが本人であることを証明するように求めたりすることが必要な場合もあります。 

サインイン リスクは、特定の認証要求が ID 所有者によって承認されていない可能性があることを表します。 Azure AD Premium P2 のライセンスを所持する組織では、[Azure AD Identity Protection のサインイン リスク検出](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)を組み込んだ条件付きアクセス ポリシーを作成できます。

このポリシーを構成できる場所は 2 つあります。1 つは条件付きアクセスで、もう 1 つは Identity Protection です。 拡張診断データ、レポート専用モードの統合、Graph API のサポート、ポリシーで他の条件付きアクセス属性を使用する機能など、より多くのコンテキストを提供するときは、条件付きアクセス ポリシーを使用した構成をお勧めします。

## <a name="template-deployment"></a>テンプレートのデプロイ

組織は、このポリシーをデプロイするのに以下に示す手順を使用するか、[条件付きアクセス テンプレート (プレビュー) ](concept-conditional-access-policy-common.md#conditional-access-templates-preview)を使用するかを選ぶことができます。 

## <a name="enable-with-conditional-access-policy"></a>条件付きアクセス ポリシーを有効にする

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択します。
1. **[条件]**  >  **[サインイン リスク]** で、 **[構成]** を **[はい]** に設定します。 **[このポリシーを適用するサインイン リスク レベルを選択します]** で、以下の操作を実行します。 
   1. **[高]** と **[中]** を選択します。
   1. **[Done]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[[レポート専用モード]](howto-conditional-access-insights-reporting.md) を使用して設定を確認した後、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

## <a name="enable-through-identity-protection"></a>Identity Protection を有効にする

1. **Azure portal** にサインインします。
1. **[すべてのサービス]** を選択し、 **[Azure AD Identity Protection]** に移動します。
1. **[サインインのリスク ポリシー]** を選択します。
1. **[割り当て]** で、 **[ユーザー]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[対象外とするユーザーの選択]** を選択し、組織の緊急アクセス用または非常用アカウントを選択して、 **[選択]** を選びます。
   1. **[Done]** を選択します。
1. **[条件]** の下の **[Sign-in risk]\(サインインのリスク\)** を選択し、 **[中以上]** を選択します。
   1. **[選択]** 、 **[完了]** の順に選択します。
1. **[Controls]\(制御\)**  >  **[アクセス]** で、 **[アクセスを許可]** を選択し、 **[Require multi-factor authentication]\(多要素認証を要求する\)** を選択します。
   1. **[選択]** を選択します。
1. **[ポリシーの適用]** を **[オン]** に設定します。
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[ユーザー リスクベースの条件付きアクセス](howto-conditional-access-policy-risk-user.md)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)

[Azure Active Directory Identity Protection とは](../identity-protection/overview-identity-protection.md)
