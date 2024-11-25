---
title: ポリシー キーの概要 - Azure Active Directory B2C
description: トークン、クライアント シークレット、証明書、パスワードに署名して検証するために Azure Active Directory B2C で使用できる暗号化ポリシー キーの種類について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c4fdd2910e1f5d65776420e8337de1d8b4434f0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596469"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のポリシー キーの概要

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) は、シークレットと証明書をポリシー キーの形式で保存して、統合対象のサービスとの信頼を確立します。 これらの信頼は次のもので構成されます。

- 外部 ID プロバイダー
- [REST API サービス](restful-technical-profile.md)との接続
- トークンの署名と暗号化

 この記事では、Azure AD B2C によって使用されるポリシーキーについて理解しておく必要がある事項について説明します。

> [!NOTE]
> 現時点では、ポリシー キーの構成は[カスタム ポリシー](./user-flow-overview.md)のみに制限されています。

Azure portal の **[ポリシー キー]** メニューで、サービス間の信頼を確立するためのシークレットと証明書を構成できます。 キーは、対称または非対称にすることができます。 "*対称*" 暗号化 (秘密キー暗号化) では、データの暗号化と復号化の両方に共有シークレットが使用されます。 "*非対称*" 暗号化 (公開キー暗号化) は、キーのペアを使用する暗号化システムであり、証明書利用者アプリケーションと共有される公開キーと、Azure AD B2C のみが認識する秘密キーで構成されます。

## <a name="policy-keyset-and-keys"></a>ポリシー キーセットとキー

Azure AD B2C のポリシー キーの最上位レベル リソースは、**キーセット** コンテナーです。 各キーセットには、少なくとも 1 つの **キー** が含まれます。 キーには次の属性があります。

| 属性 |  必須 | 注釈 |
| --- | --- |--- |
| `use` | Yes | 用途:公開キーの使用目的を示します。 `enc` はデータの暗号化を示し、`sig` はデータの署名の検証を示します。|
| `nbf`| No | アクティブ化の日時。 |
| `exp`| No | 有効期限の日時。 |

PKI 標準に従って、キーのアクティブ化と有効期限の値を設定することをお勧めします。 セキュリティやポリシー上の理由から、これらの証明書の定期的なローテーションが必要になる場合があります。 たとえば、すべての証明書を毎年ローテーションするポリシーを使用することがあります。

キーを作成するために、次のいずれかの方法を選択できます。

- **手動** - 定義した文字列を使用してシークレットを作成します。 シークレットは対称キーです。 アクティブ化と有効期限の日付を設定できます。
- **生成** - キーを自動生成します。 アクティブ化と有効期限の日付を設定できます。 2 つのオプションがあります。
  - **シークレット** - 対称キーを生成します。
  - **RSA** - キー ペア (非対称キー) を生成します。
- **アップロード** - 証明書または PKCS12 キーをアップロードします。 証明書には、秘密キーと公開キー (非対称キー) が含まれている必要があります。

## <a name="key-rollover"></a>キーのロールオーバー

セキュリティ上の理由から、Azure AD B2C は定期的に、または緊急時にすぐにキーをロールオーバーすることができます。 キーのロールオーバー イベントの発生頻度にかかわらず、それらのイベントを処理するように、Azure AD B2C と統合されるアプリケーション、ID プロバイダー、または REST API を準備する必要があります。 このようにしないと、アプリケーションまたは Azure AD B2C は期限切れのキーを使用して暗号化操作を実行しようとし、サインイン要求が失敗します。

Azure AD B2C キーセットに複数のキーがある場合は、次の条件に基づいて、一度にアクティブになるキーは 1 つだけです。

- キーのアクティブ化は、**アクティブ化の日付** に基づきます。
  - キーは、アクティブ化の日付により昇順で並べ替えられます。 アクティブ化の日付がさらに後になるキーは一覧の下の方に表示されます。 アクティブ化の日付がないキーは、一覧の最下部に配置されます。
  - 現在の日付と時刻がキーのアクティブ化の日付よりも大きい場合、Azure AD B2C はそのキーをアクティブ化し、前のアクティブ キーの使用を停止します。
- 現在のキーの有効期限が切れ、キー コンテナーに、"*期間の開始時刻*" と "*有効期限*" 時刻が有効な新しいキーが含まれる場合、その新しいキーが自動的にアクティブになります。
- 現在のキーの有効期限が切れ、キー コンテナーに、"*期間の開始時刻*" と "*有効期限*" 時刻が有効な新しいキーが含まれて "*いない*" 場合、Azure AD B2C は、有効期限が切れたキーを使用できなくなります。 Azure AD B2C により、カスタム ポリシーの依存コンポーネント内でエラー メッセージが生成されます。 この問題を回避するために、セーフティ ネットとしてアクティブ化と有効期限の日付がない既定キーを作成できます。
- キーが [JwtIssuer 技術プロファイル](./jwt-issuer-technical-profile.md)で参照されている場合、OpenId Connect の既知の構成エンドポイントのキーのエンドポイント (JWKS URI) には、キー コンテナーで構成されているキーが反映されます。 OIDC ライブラリを使用するアプリケーションは、正しいキーを使用してトークンを検証するために、このメタデータを自動的に取得します。 詳細については、最新のトークン署名キーを常に自動的に取得する、[Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md) の使用方法に関するページを参照してください。

## <a name="policy-key-management"></a>ポリシー キーの管理

キー コンテナー内の現在のアクティブ キーを取得するには、Microsoft Graph API の [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) エンドポイントを使用します。

署名キーと暗号化キーを追加または削除するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[ポリシー]** を選択してから **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択します。 
    1. 新しいキーを追加するには、 **[追加]** を選択します。
    1. 新しいキーを削除するには、キーを選択し、 **[削除]** を選択します。 キーを削除するには、削除するキー コンテナーの名前を入力します。 Azure AD B2C によってキーが削除され、サフィックスが .bak のキーのコピーが作成されます。

### <a name="replace-a-key"></a>キーの交換

キーセット内のキーは、交換可能でも削除可能でもありません。 既存のキーを変更する必要がある場合は、次のようにします。

- **アクティブ化した日付** が現在の日付と時刻に設定された新しいキーを追加することをお勧めします。 Azure AD B2C は新しいキーをアクティブ化し、以前のアクティブ キーの使用を停止します。
- または、正しいキーを使用して新しいキーセットを作成することもできます。 新しいキーセットを使用するようにポリシーを更新し、古いキーセットを削除します。 

## <a name="next-steps"></a>次の手順

- Microsoft Graph を使用して[キーセット](microsoft-graph-operations.md#trust-framework-policy-keyset)と[ポリシー キー](microsoft-graph-operations.md#trust-framework-policy-key)のデプロイを自動化する方法を学習します。

::: zone-end
