---
title: 条件付きアクセス - 管理者に対して MFA を必須にする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、管理者に対して多要素認証の実行を必須にします
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
ms.openlocfilehash: 0b7c5af60dc613538b376069381d51722e654846
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715665"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>条件付きアクセス:管理者に対して MFA を必須にする

管理者権限が割り当てられているアカウントは、攻撃者の標的になります。 これらのアカウントに対して多要素認証 (MFA) を必須にすることは、これらのアカウントが侵害されるリスクを軽減する簡単な方法です。

Microsoft では、少なくとも以下のロールに対して MFA を必須にすることを推奨しています。

- 全体管理者
- アプリケーション管理者
- 認証管理者
- 課金管理者
- クラウド アプリケーション管理者
- 条件付きアクセス管理者
- Exchange 管理者
- ヘルプデスク管理者
- パスワード管理者
- 特権認証管理者
- 特権ロール管理者
- セキュリティ管理者
- SharePoint 管理者
- ユーザー管理者

組織では、ニーズに応じてロールを含めるか除外するかを選択できます。

## <a name="user-exclusions"></a>ユーザーの除外

条件付きアクセス ポリシーは強力なツールであり、以下のアカウントはポリシーから除外することをお勧めします。

- **緊急アクセス用** アカウントまたは **非常用** アカウント。テナント全体でアカウントがロックアウトされるのを防ぎます。 発生する可能性は低いシナリオですが、すべての管理者がテナントからロックアウトされた場合に、ご自身の緊急アクセス用管理アカウントを使用してテナントにログインし、アクセスの復旧手順を実行できます。
   - 詳細については、「[Azure AD で緊急アクセス用アカウントを管理する](../roles/security-emergency-access.md)」を参照してください。
- **サービス アカウント** と **サービス プリンシパル** (Azure AD Connect 同期アカウントなど)。 サービス アカウントは、特定のユーザーに関連付けられていない非対話型のアカウントです。 これらは通常、アプリケーションへのプログラムによるアクセスを可能にするバックエンド サービスによって使用されますが、管理目的でシステムにサインインする場合にも使用されます。 プログラムでは MFA を完了できないため、このようなサービス アカウントは対象外とする必要があります。 サービス プリンシパルによって行われた呼び出しは、条件付きアクセスによってブロックされることはありません。
   - 組織のスクリプトまたはコードでこれらのアカウントが使用されている場合は、それを[マネージド ID](../managed-identities-azure-resources/overview.md) に置き換えることを検討してください。 これらの特定のアカウントは、一時的な回避策として、ベースライン ポリシーの対象外にすることができます。

## <a name="template-deployment"></a>テンプレートのデプロイ

組織は、このポリシーをデプロイするのに以下に示す手順を使用するか、[条件付きアクセス テンプレート (プレビュー) ](concept-conditional-access-policy-common.md#conditional-access-templates-preview)を使用するかを選ぶことができます。 

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順では、割り当てられた管理者ロールに対して、多要素認証の実行を必須にする条件付きアクセス ポリシーを作成します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[含める]** で、 **[ディレクトリ ロール]** を選択し、次のような組み込みロールを選択します。
      - 全体管理者
      - アプリケーション管理者
      - 認証管理者
      - 課金管理者
      - クラウド アプリケーション管理者
      - 条件付きアクセス管理者
      - Exchange 管理者
      - ヘルプデスク管理者
      - パスワード管理者
      - 特権認証管理者
      - 特権ロール管理者
      - セキュリティ管理者
      - SharePoint 管理者
      - ユーザー管理者
   
      > [!WARNING]
      > 条件付きアクセス ポリシーでは、組み込みロールがサポートされています。 条件付きアクセス ポリシーは、[管理単位スコープ](../roles/admin-units-assign-roles.md)や[カスタム ロール](../roles/custom-create.md)など、その他の種類のロールには適用されません。

   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択し、 **[完了]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
1. 設定を確認し、 **[ポリシーの有効化]** を **[レポート専用]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

管理者は、[[レポート専用モード]](howto-conditional-access-insights-reporting.md) を使用して設定を確認した後、 **[ポリシーの有効化]** トグルを **[レポートのみ]** から **[オン]** に移動できます。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
