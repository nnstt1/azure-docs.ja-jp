---
title: パスワードレスの認証方法を登録するように Azure AD で一時アクセス パスを構成する
description: 一時アクセス パスを使用してパスワードレスの認証方法を登録するように構成し、ユーザーが利用できるようにする方法について学習します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef246412a172b6f5fcafcd409e4b6e29eb12c847
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471442"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>パスワードレスの認証方法を登録するように Azure AD で一時アクセス パスを構成する (プレビュー)

パスワードレスの認証方法 (FIDO2 や Microsoft Authenticator アプリを使用したパスワードレスの電話によるサインインなど) は、ユーザーがパスワードを使用せずに安全にサインインできるようにします。 ユーザーは、次の 2 つの方法のいずれかでパスワードレスの方法をブートストラップできます。

- 既存の Azure AD 多要素認証方法を使用する 
- 一時アクセス パス (TAP) を使用する 

一時アクセス パスは、管理者によって発行される期間限定のパスコードであり、強力な認証要件を満たし、パスワードレスの方法を含む他の認証方法をオンボードするために使用できます。 また、一時アクセス パスを使用すると、ユーザーが FIDO2 セキュリティ キーや Microsoft Authenticator アプリなどの強力な認証要素を紛失したり忘れたりした場合でも簡単に回復することができます。しかし、新しい強力な認証方法を登録するには、サインインする必要があります。

この記事では、Azure portal を使用して Azure AD で一時アクセス パスを有効にして使用する方法を示します。 これらのアクションは、REST API を使用して実行することもできます。 

>[!NOTE]
>一時アクセス パスは、現在パブリック プレビューの段階です。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。 

## <a name="enable-the-temporary-access-pass-policy"></a>一時アクセス パス ポリシーを有効にする

一時アクセス パス ポリシーでは、テナントに作成されたパスの有効期間や、一時アクセス パスを使用してサインインできるユーザーとグループなどの設定を定義します。 一時アクセス パスを使用してサインインできるようにするには、まず認証方法ポリシーを有効にし、一時アクセス パスを使用してサインインできるユーザーとグループを選択する必要があります。
任意のユーザー用の一時アクセス パスを作成できますが、それを使用してサインインできるのは、ポリシーに含まれているユーザーだけです。

グローバル管理者と認証方法ポリシー管理者の役割所有者は、一時アクセス パス認証方法ポリシーを更新できます。
一時アクセス パス認証方法ポリシーを構成するには、次のようにします。

1. グローバル管理者として Azure portal にサインインし、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[認証方法]**  >  **[一時アクセス パス]** の順にクリックします。
1. **[はい]** をクリックしてポリシーを有効にし、ポリシーが適用されているユーザーと、 **[全般]** の設定を選択します。

   ![一時アクセス パス認証方法ポリシーを有効にする方法のスクリーンショット](./media/how-to-authentication-temporary-access-pass/policy.png)

   次の表では、既定値と許可される値の範囲について説明します。


   | 設定 | 既定値 | 使用できる値 | 説明 |
   |---|---|---|---|
   | 最短有効期間 | 1 時間 | 10 ～ 43200 分 (30 日) | 一時アクセス パスが有効である最短時間 (分)。 |
   | 最長有効期間 | 24 時間 | 10 ～ 43200 分 (30 日) | 一時アクセス パスが有効である最長時間 (分)。 |
   | 既定の有効期間 | 1 時間 | 10 ～ 43200 分 (30 日) | 既定値は、ポリシーによって構成される最短および最長の有効期間内で、個々のパスによってオーバーライドできます。 |
   | 1 回限りの使用 | False | True/False | ポリシーが false に設定されている場合、テナントのパスは、その有効期間 (最大有効期間) 中に 1 回または複数回使用できます。 一時アクセス パス ポリシーで 1 回限りの使用を強制することで、テナントに作成されたすべてのパスが 1 回限りの使用として作成されます。 |
   | 長さ | 8 | 8 ～ 48 文字 | パスコードの長さを定義します。 |

## <a name="create-a-temporary-access-pass"></a>一時アクセス パスを作成する

ポリシーを有効にした後、Azure AD でユーザー用の一時アクセス パスを作成できます。 これらの役割で一時アクセス パスに関する次の操作を行うことができます。

- グローバル管理者は、任意のユーザー (自分以外) の一時アクセス パスを作成、削除、表示することができます
- 特権認証管理者は、管理者とメンバー (自分以外) の一時アクセス パスを作成、削除、表示することができます
- 認証管理者は、メンバー (自分以外) の一時アクセス パスを作成、削除、表示することができます
- グローバル管理者は (コード自体を読み取ることなく) ユーザーの一時アクセス パスの詳細を表示できます。

1. グローバル管理者、特権認証管理者、または認証管理者のいずれかとして Azure portal にサインインします。 
1. **[Azure Active Directory]** をクリックし、[ユーザー] を参照して、 *[Chris Green]* などのユーザーを選択してから、 **[認証方法]** を選択します。
1. 必要に応じて、 **[新しいユーザー認証方法のエクスペリエンスを試す]** ためのオプションを選択します。
1. **[認証方法の追加]** のオプションを選択します。
1. **[方法の選択]** で、 **[一時アクセス パス (プレビュー)]** をクリックします。
1. カスタムのアクティベーション時間または期間を定義し、 **[追加]** をクリックします。

   ![一時アクセス パスを作成する方法のスクリーンショット](./media/how-to-authentication-temporary-access-pass/create.png)

1. 追加されると、一時アクセス パスの詳細が表示されます。 実際の一時アクセス パスの値をメモしておきます。 この値を、ユーザーに伝えます。 **[OK]** をクリックした後は、この値は表示できません。
   
   ![一時アクセス パスの詳細のスクリーンショット](./media/how-to-authentication-temporary-access-pass/details.png)

次のコマンドは、PowerShell を使用して一時アクセス パスを作成して取得する方法を示しています。

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>一時アクセス パスを使用する

一時アクセス パスは、ユーザーが最初のサインイン時に認証の詳細を登録する場合に最もよく使用されます。その場合、追加のセキュリティ プロンプトを完了する必要はありません。 認証方法は [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に登録されています。 ユーザーは、ここで既存の認証方法を更新することもできます。

1. Web ブラウザーを開いて [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) にアクセスします。
1. 一時アクセス パスを作成したアカウントの UPN を入力します ( *tapuser@contoso.com* など)。
1. ユーザーが一時アクセス パス ポリシーに含まれている場合は、その一時アクセス パスを入力するための画面が表示されます。
1. Azure portal に表示された一時アクセス パスを入力します。

   ![一時アクセス パスを入力する方法のスクリーンショット](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>フェデレーション ドメインの場合は、フェデレーションよりも一時アクセス パスが優先されます。 一時アクセス パスを使用しているユーザーは Azure AD で認証を完了するため、フェデレーション ID プロバイダー (IDP) にリダイレクトされません。

これでユーザーがサインインし、FIDO2 セキュリティ キーなどの方法を更新または登録できるようになりました。 資格情報やデバイスを紛失したために認証方法を更新するユーザーは、古い認証方法を必ず削除する必要があります。
また、ユーザーは、引き続き自分のパスワードを使用してサインインできます。TAP はユーザーのパスワードに代わるものではありません。

### <a name="passwordless-phone-sign-in"></a>パスワードレスの電話によるサインイン

ユーザーは一時アクセス パスを使用して、Authenticator アプリから直接パスワードレスの電話によるサインインに登録することもできます。 詳細については、「[Microsoft Authenticator アプリに職場または学校アカウントを追加する](https://support.microsoft.com/account-billing/add-your-work-or-school-account-to-the-microsoft-authenticator-app-43a73ab5-b4e8-446d-9e54-2a4cb8e4e93c)」を参照してください。

![職場または学校アカウントを使用して一時アクセス パスを入力する方法のスクリーンショット](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

### <a name="guest-access"></a>ゲスト アクセス

ゲスト ユーザーはホーム テナントによって発行された一時アクセス パスを使用してリソース テナントにサインインできますが、そのためにはこの一時アクセス パスがホーム テナントの認証要件を満たしていることが必要です。 リソース テナントに MFA が必要な場合、ゲスト ユーザーはリソースにアクセスするために MFA を実行する必要があります。

### <a name="expiration"></a>有効期限

有効期限が切れたか、または削除された一時アクセス パスを、対話型または非対話型認証に使用することはできません。 一時アクセス パスの有効期限が切れたか、または削除された後、ユーザーは別の認証方法で再認証する必要があります。 

## <a name="delete-an-expired-temporary-access-pass"></a>期限切れの一時アクセス パスを削除する

ユーザーの **[認証方法]** の **[詳細]** 列に、一時アクセス パスがいつ期限切れになったかが表示されます。 次の手順を使用して、期限切れの一時アクセス パスを削除することができます。

1. Azure AD ポータルで、 **[ユーザー]** を参照し、 *[TAP ユーザー]* などのユーザーを選択してから、 **[認証方法]** を選択します。
1. 一覧に表示されている **[一時アクセス パス (プレビュー)]** の認証方法の右側で、 **[削除]** を選択します。

PowerShell を使用することもできます。

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>一時アクセス パスを置き換える 

- ユーザーは一時アクセス パスを 1 つだけ持つことができます。 パスコードは一時アクセス パスの開始時から終了時にかけて使用できます。
- ユーザーが新しい一時アクセス パスを必要とする場合:
  - 既存の一時アクセス パスが有効な場合、管理者は既存の一時アクセス パスを削除し、ユーザーの新しいパスを作成する必要があります。 
  - 既存の一時アクセス パスの有効期限が切れている場合、新しい一時アクセス パスにより既存の一時アクセス パスがオーバーライドされます。

オンボードと復旧の NIST 標準の詳細については、「[Nist Special Publication 800-63](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4)」を参照してください。

## <a name="limitations"></a>制限事項

以下の制限事項に留意してください。

- 1 回限りの一時アクセス パスを使用して FIDO2 や電話によるサインインなどのパスワードレスの方法を登録する場合、ユーザーは 1 回限りの一時アクセス パスでのサインインの登録を 10 分以内に完了する必要があります。 この制限は、複数回使用できる一時アクセス パスには適用されません。
- 一時アクセス パスはパブリック プレビューの段階にあり、現在のところ、Azure for US Government ではご利用いただけません。
- セルフサービス パスワード リセット (SSPR) 登録ポリシー *または* [Identity Protection の多要素認証登録ポリシー](../identity-protection/howto-identity-protection-configure-mfa-policy.md)の対象ユーザーは、一時アクセス パスを使用してサインインした後、認証方法を登録する必要があります。 これらのポリシーの対象ユーザーは、[統合された登録の中断モード](concept-registration-mfa-sspr-combined.md#combined-registration-modes)にリダイレクトされます。 現在、このエクスペリエンスでは、FIDO2 と電話によるサインインの登録はサポートされていません。 
- 一時アクセス パスは、ネットワーク ポリシー サーバー (NPS) 拡張機能と Active Directory フェデレーション サービス (AD FS) アダプターでは使用できません。また、Windows の設定および Out-of-Box-Experience (OOBE) や、オートパイロットの間は使用できません。Windows Hello for Business のデプロイにも使用できません。 
- テナントでシームレス SSO が有効になっている場合は、ユーザーにパスワードの入力が求められます。 ユーザーが一時アクセス パスを使用してサインインできるように、 **[代わりに一時アクセス パスを使用する]** リンクが使用可能になります。

  ![[代わりに一時アクセス パスを使用する] のスクリーンショット](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>トラブルシューティング    

- サインイン時にユーザーに一時アクセス パスが提供されない場合は、次のことを確認してください。
  - ユーザーが一時アクセス パス認証方法ポリシーの対象である。
  - ユーザーが有効な一時アクセス パスを持っていて、1 回限りの使用の場合はまだ使用されていない。
- 一時アクセス パスを使用してサインインする際に **[ユーザー資格情報ポリシーにより、一時アクセス パスでのサインインはブロックされました]** が表示される場合は、次のことを確認してください。
  - ユーザーは複数回使用できる一時アクセス パスを持っているが、認証方法ポリシーでは 1 回限りの一時アクセス パスが必要とされている。
  - 1 回限りの一時アクセス パスが既に使用されている。
- 一時アクセス パスでのサインインが、ユーザー資格情報ポリシーのためにブロックされる場合は、ユーザーが TAP ポリシーのスコープ内であることを確認してください。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory でパスワードレス認証のデプロイを計画する](howto-authentication-passwordless-deployment.md)