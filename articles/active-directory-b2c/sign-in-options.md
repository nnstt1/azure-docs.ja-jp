---
title: Azure AD B2C でサポートされるサインイン オプション
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で使用できるサインアップおよびサインインのオプションについて学習します。これには、ユーザー名とパスワード、電子メール、電話、ソーシャルまたは外部の ID プロバイダーとのフェデレーションなどが含まれます。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 4f0a713eacb259348f04fe61fefde72a3142b419
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043246"
---
# <a name="sign-in-options-in-azure-ad-b2c"></a>Azure AD B2C のサインイン オプション

Azure AD B2C には、アプリケーションのユーザー向けにサインアップおよびサインインの方法としていくつかが用意されています。 ユーザーがアプリケーションにサインアップするときに、彼らがユーザー名、電子メール アドレス、または電話番号を使用して所定の Azure AD B2C テナントにローカル アカウントを作成できるようにするかどうかを決定します。 また、ソーシャル ID プロバイダー (Facebook、LinkedIn、Twitter など) と標準の ID プロトコル (OAuth 2.0、OpenID Connect など) とフェデレーションすることもできます。

この記事では Azure AD B2C サインイン オプションの概要について説明します。

## <a name="email-sign-in"></a>メール アドレスでのサインイン

ローカル アカウント ID プロバイダーの設定では、メール アドレスでのサインアップが既定で有効になっています。 メール アドレス オプションを使用すると、ユーザーは自分のメール アドレスとパスワードを使用してサインインおよびサインアップすることができます。

- **サインイン**: ユーザーは自分のメール アドレスとパスワードを入力するように求められます。
- **サインアップ**: ユーザーはメール アドレスを入力するように求められます。これがサインアップ時に検証され (省略可能)、ログイン ID になります。 次にユーザーは、サインアップ ページで要求されたその他の情報 (表示名、名、姓など) を入力します。 次に、 **[続行]** を選択してアカウントを作成します。
- **パスワードのリセット**: ユーザーは自分のメール アドレスを入力し、それを確認すると、パスワードをリセットできるようになります。

![メール アドレスでのサインアップまたはサインインの操作](./media/sign-in-options/local-account-email-experience.png)

ローカル アカウント ID プロバイダーで電子メール サインインを構成する方法について学習します。
## <a name="username-sign-in"></a>ユーザー名でのサインイン

ご利用のローカル アカウント ID プロバイダーには、ユーザーがユーザー名とパスワードを使用してアプリケーションにサインアップおよびサインインできるようにするためのユーザー名オプションが含まれています。

- **サインイン**: ユーザーは、ユーザー名とパスワードを入力するように求められます。
- **サインアップ**: ユーザーはユーザー名を入力するように求められます。これがログイン ID になります。 ユーザーはメール アドレスの入力も求められます。このアドレスはサインアップ時に検証されます。 このメール アドレスは、パスワード リセット フローで使用されます。 ユーザーは、サインアップ ページで要求されたその他の情報 (表示名、名、姓など) を入力します。 次にユーザーは、[続行] を選択してアカウントを作成します。
- **パスワードのリセット**: ユーザーは、自分のユーザー名と、関連付けられたメール アドレスを入力する必要があります。 このメール アドレスを確認する必要があります。その後、ユーザーはパスワードをリセットできます。

![ユーザー名でのサインアップまたはサインインの操作](./media/sign-in-options/local-account-username-experience.png)

## <a name="phone-sign-in"></a>電話によるサインイン

ローカル アカウント ID プロバイダーの設定で、電話によるサインインはパスワードレスのオプションです。 この方法を設定すると、ユーザーが電話番号を自分のプライマリ ID として使用して、アプリにサインアップできます。 ワンタイム パスワードが SMS テキスト メッセージを介してユーザーに送信されます。 ユーザーはサインアップおよびサインイン時に次の操作を行うことになります。

- **サインイン**: 自身の電話番号を ID として使用する既存のアカウントを持っている場合、ユーザーはその電話番号を入力し、 *[サインイン]* を選択します。 *[続行]* を選択して国と電話番号を確定すると、1 回限りの認証コードがそのユーザーの電話に送信されます。 ユーザーが確認コードを入力し、 *[続行]* を選択してサインインします。
- **サインアップ**: アプリケーションのアカウントをまだ持っていない場合、ユーザーは *[今すぐサインアップ]* リンクをクリックしてアカウントを作成できます。
    1. サインアップ ページが表示されます。ここでユーザーは *[国]* を選択し、電話番号を入力して、 *[コードの送信]* を選択します。 
    1. 1 回限りの確認コードがユーザーの電話番号に送信されます。 ユーザーは、サインアップ ページで *確認コード* を入力し、 *[コードの確認]* を選択します (ユーザーがコードを取得できない場合、 *[新しいコードを送信します]* を選択できます)。
    1. ユーザーは、サインアップ ページで要求されたその他の情報 (表示名、名、姓など) を入力します。 その後 [続行] を選択します。
    1. 次に、ユーザーは **回復用メール アドレス** を入力するように求められます。 ユーザーはメール アドレスを入力し、 *[確認コードを送信する]* を選択します。 ユーザーはメールの受信トレイに送信されたコードを取得して、 [確認コード] ボックスに入力できます。 次に、ユーザーは [コードの確認] を選択します。
    1. コードが確認されたら、ユーザーは *[作成]* を選択してアカウントを作成します。

![電話でのサインアップまたはサインインの操作](./media/sign-in-options/local-account-phone-experience.png)

### <a name="pricing-for-phone-sign-in"></a>電話でのサインインの料金

ワンタイム パスワードが SMS テキスト メッセージを使用してユーザーに送信されます。 携帯電話会社によっては、送信されるメッセージごとに課金される場合があります。 価格情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」の「**別料金**」セクションを参照してください。

> [!NOTE]
> 電話でのサインアップを使用したユーザー フローを構成するときは、多要素認証 (MFA) が既定で無効化されます。 電話でのサインアップを使用したユーザー フローで MFA を有効にすることはできますが、電話番号がプライマリ ID として使用されるため、2 番目の認証要素に使用できる唯一のオプションは電子メールによるワンタイム パスコードです。

### <a name="phone-recovery"></a>電話での回復

ユーザー フローで電話でのサインアップとサインインを有効にする場合、回復用メール機能を有効にすることもお勧めします。 この機能を使用すると、ユーザーが自分の電話を持っていないときにアカウントを回復するために使用できるメール アドレスを指定できます。 このメール アドレスは、アカウントの回復のみに使用されます。 サインインに使用することはできません。

- 回復用メール アドレスのプロンプトが **オン** の場合、ユーザーが初めてサインアップするときに、バックアップのメール アドレスを確認するように求められます。 前に回復用メールを提供していないユーザーは、次回のサインイン時にバックアップのメール アドレスの確認を求められます。

- 回復用メールが **オフ** の場合、ユーザーがサインアップまたはサインインしても、回復用メール プロンプトは表示されません。

次のスクリーンショットは、電話での回復フローを示しています。

![電話での回復のユーザー フロー](./media/sign-in-options/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in"></a>電話またはメール アドレスでのサインイン

ローカル アカウント ID プロバイダーの設定で、[電話でのサインイン](#phone-sign-in)と[メール アドレスでのサインイン](#email-sign-in)を組み合わせることを選択できます。 サインアップまたはサインインのページで、ユーザーは電話番号またはメール アドレスを入力できます。 ユーザー入力に基づいて、対応するフローにユーザーが移動されます。

![電話またはメール アドレスでのサインアップまたはサインインの操作](./media/sign-in-options/local-account-phone-and-email-experience.png)

## <a name="next-steps"></a>次のステップ

- 「[Azure Active Directory B2C のユーザー フロー](user-flow-overview.md)」によって提供される組み込みのポリシーの詳細について学習します。
- [ローカル アカウント ID プロバイダーを構成する](identity-provider-local.md)