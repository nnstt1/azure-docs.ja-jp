---
title: プロファイル編集フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でプロファイル編集フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/07/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f019fdc64ca30954017afc34267dcef998cf5fb8
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198838"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でプロファイル編集フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>プロファイル編集フロー

プロファイル編集ポリシーを使用すると、ユーザーは、表示名、姓、名、市区町村などのプロファイル属性を管理できます。 プロファイル編集フローには、次の手順が含まれます。 

1. ローカルまたはソーシャル アカウントによるサインアップまたはサインイン。 セッションがまだアクティブである場合、Azure AD B2C によってユーザーが承認され、次の手順にスキップします。
1. Azure AD B2C によってディレクトリからユーザー プロファイルが読み取られ、ユーザーが属性を編集できるようになります。

![プロファイル編集フロー](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>[前提条件]

まだそうしていない場合は、[Azure Active Directory B2C に Web アプリケーションを登録](tutorial-register-applications.md)します。

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>プロファイル編集ユーザー フローを作成する

アプリケーションでユーザーによるプロファイル編集を有効にする場合は、プロファイル編集ユーザー フローを使用します。

1. Azure AD B2C テナントの [概要] ページのメニューで、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、 **[プロファイル編集]** ユーザー フローを選択します。 
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。
1. ユーザー フローの **[名前]** を入力します。 たとえば、「*profileediting1*」と入力します。
1. **[ID プロバイダー]** で、少なくとも 1 つの ID プロバイダーを選択します。

   * **[ローカル アカウント]** で、 **[Email signin]\(メールでのサインイン\)** 、 **[User ID signin]\(ユーザー ID でのサインイン\)** 、 **[Phone signin]\(電話でのサインイン\)** 、 **[Phone/Email signin]\(電話とメールでのサインイン\)** 、 **[User ID/Email signin]\(ユーザー ID とメールでのサインイン\)** 、 **[None]\(なし\)** のいずれかを選択します。 [詳細については、こちらを参照してください](sign-in-options.md)。
   * **[ソーシャル ID プロバイダ]** で、既に設定してある外部のソーシャル ID プロバイダまたはエンタープライズ ID プロバイダーをどれか選択します。 [詳細については、こちらを参照してください](add-identity-provider.md)。
1. 第 2 の認証方法で ID を証明することをユーザーに要求したい場合は、 **[多要素認証]** で、認証方法の種類を選択し、さらに、いつ多要素認証 (MFA) を適用するかを選択します。 [詳細については、こちらを参照してください](multi-factor-authentication.md)。
1. Azure AD B2C テナントに対して条件付きアクセス ポリシーを構成してある場合で、なおかつ、このユーザー フローでそれらを有効にしたい場合は、 **[条件付きアクセス]** の **[Enforce conditional access policies]\(条件付きアクセス ポリシーを強制\)** チェック ボックスをオンにします。 ポリシー名を指定する必要はありません。 [詳細については、こちらを参照してください](conditional-access-user-flow.md?pivots=b2c-user-flow)。
1. **[ユーザー属性]** で、ユーザーが自分のプロファイルで編集できる属性を選択します。 すべての値を表示するために **[Show more]\(詳細表示\)** を選択して値を選び、 **[OK]** を選択します。
1. **[作成]** を選択して、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックし、以前に作成したアカウントでサインインします。
1. これで、ユーザーの表示名と役職を変更できるようになりました。 **[Continue]** をクリックします。 トークンが `https://jwt.ms` に返され、表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 サインアップとサインイン、パスワードのリセット、プロファイル編集ポリシーなど、いくつかの事前に構築されたポリシーを含むスターター パックが用意されています。 詳細については、「[Azure AD B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」を参照してください。

::: zone-end

## <a name="next-steps"></a>次のステップ

* [ソーシャル ID プロバイダーを使用するサインイン](add-identity-provider.md)を追加します。