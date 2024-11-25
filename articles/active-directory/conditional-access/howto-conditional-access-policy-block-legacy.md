---
title: 条件付きアクセス - レガシ認証をブロックする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、レガシ認証プロトコルをブロックします
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
ms.openlocfilehash: 9baa605a8ee97129c589d08e2ea1c3beee95ff83
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708971"
---
# <a name="conditional-access-block-legacy-authentication"></a>条件付きアクセス:レガシ認証をブロックする

レガシ認証プロトコルに関連したリスクが増大しているため、Microsoft では組織に対して、これらのプロトコルを使用した認証要求をブロックし、先進認証を必須にすること推奨しています。 レガシ認証をブロックすることが重要である理由について詳しくは、「[方法: 条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする](block-legacy-authentication.md)」を参照してください。

## <a name="template-deployment"></a>テンプレートのデプロイ

組織は、このポリシーをデプロイするのに以下に示す手順を使用するか、[条件付きアクセス テンプレート (プレビュー) ](concept-conditional-access-policy-common.md#conditional-access-templates-preview)を使用するかを選ぶことができます。 

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順を実行すると、レガシ認証要求をブロックする条件付きアクセス ポリシーを作成できます。 このポリシーは[レポート専用モード](howto-conditional-access-insights-reporting.md)となり、管理者が既存のユーザーに与える影響を判断できるようになります。 ポリシーが意図したとおりに適用されると管理者が判断した場合は、**オン** に切り替えたり、特定のグループを追加し他のグループを除外することでデプロイをステージングしたりすることができます。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[Exclude]\(除外\)** で、 **[ユーザーとグループ]** を選択し、今後もレガシ認証を使用できる必要があるアカウントをすべて選択します。 自分がロックアウトされないように、少なくとも 1 つのアカウントを除外します。アカウントを 1 つも除外しない場合、このポリシーを作成することはできません。
   1. **[Done]** を選択します。
1. **[クラウド アプリまたはアクション]** で、 **[すべてのクラウド アプリ]** を選択します。
   1. **[Done]** を選択します。
1. **[条件]**  >  **[クライアント アプリ]** で、 **[構成]** を **[はい]** に設定します。
   1. **[Exchange ActiveSync クライアント]** と **[その他のクライアント]** のボックスのみをオンにします。
   1. **[Done]** を選択します。
1. **アクセス制御** > **許可** で、**アクセスのブロック** を選択します。
   1. **[選択]** を選択します。
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[[レポート専用モード]](howto-conditional-access-insights-reporting.md) を使用して設定を確認した後、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

> [!NOTE]
> 条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 条件付きアクセスはサービス拒否 (DoS) 攻撃などのシナリオに対する組織の防御の最前線を意図したものではありませんが、これらのイベントからのシグナルを使用してアクセス権を判定できます。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)

[Microsoft 365 を使用して電子メールを送信するように多機能機器またはアプリケーションを設定する方法](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
