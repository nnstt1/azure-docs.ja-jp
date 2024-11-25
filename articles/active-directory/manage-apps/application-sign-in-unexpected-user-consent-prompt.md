---
title: アプリケーションにサインインするときに、予期しない同意プロンプトが表示される
titleSuffix: Azure AD
description: Azure AD と統合されているアプリケーションでユーザーに予期しない同意プロンプトが表示されたときのトラブルシューティングの方法
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d10368a6225fe1e5d09e7d6088b500febd6b1ff
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620756"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>アプリケーションにサインインするときに、予期しない同意プロンプトが表示される

Azure Active Directory と統合する多くのアプリケーションを実行するためには、さまざまなリソースへのアクセス許可が必要です。 これらのリソースを Azure Active Directory に統合する際にも、Azure AD 同意フレームワークを使用してリソースへのアクセス許可が要求されます。

このため、アプリケーションを初めて使用する際に同意プロンプトが表示されます。通常、これは 1 回限りの操作です。

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>ユーザーに同意プロンプトが表示されるシナリオ

追加のプロンプトは、次のようなさまざまなシナリオで表示されます。

* アプリケーションは割り当てを要求するように構成されています。 割り当てを要求するアプリに対してユーザーの同意が現在サポートされていません。 割り当てを要求するようにアプリケーションを構成する場合、割り当てられたユーザーがサインインできるよう、テナント全体の管理者の同意も必ず付与してください。

* アプリケーションに必要なアクセス許可のセットが変更された場合。

* アプリケーションに最初に同意したユーザーが管理者ではなく、現在はさまざまな (管理者以外の) ユーザーが、アプリケーションを初めて使用している場合。

* アプリケーションに最初に同意したユーザーは管理者だったが、同意したのが組織全体の代表としてではなかった場合。

* 最初に同意が許可された後に、アプリケーションが追加のアクセス許可を要求するために[増分および動的な同意](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)を使用している場合。 通常、この方法は、アプリケーションのオプション機能を追加するために、ベースライン機能に必要なアクセス許可を超えるアクセス許可が必要になる場合に使用されます。

* 最初に同意が許可された後で取り消された場合。

* 開発者がアプリケーションを、使用するたびに同意プロンプトを表示するように構成した場合 (注: これはベスト プラクティスではありません)。

   > [!NOTE]
   > Microsoft の推奨事項とベスト プラクティスに従い、アプリに同意を付与するユーザーのアクセス許可を多くの組織が無効にしているか、制限しています。 テナント全体の管理者の同意を管理者が付与しているのにサインインのたびにアプリケーションからユーザーに同意の付与が強制される場合、ほとんどのユーザーはブロックされてアプリケーションを使用できません。 管理者の同意の付与後でもアプリケーションからユーザーの同意が要求された場合、サインイン毎のユーザーの同意の強制を停止する設定やオプションがないか、アプリの発行元に確認してください。

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory (v1.0 エンドポイント) のアプリ、アクセス許可、および同意](../develop/quickstart-register-app.md)

* [Azure Active Directory (v2.0 エンドポイント) のスコープ、アクセス許可、および同意](../develop/v2-permissions-and-consent.md)
