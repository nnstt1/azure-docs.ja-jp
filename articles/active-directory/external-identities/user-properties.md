---
title: B2B ゲスト ユーザーのプロパティ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B の招待されたゲスト ユーザーの、招待に応じる前と後のプロパティと状態
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6a0eb5c97905fa82b15b15f6997568c1d3c03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262605"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B コラボレーション ユーザーのプロパティ

この記事では、招待された Azure Active Directory B2B (Azure AD B2B) コラボレーション ユーザー オブジェクトの、招待に応じる前と後のプロパティと状態について説明します。 Azure AD B2B コラボレーション ユーザーは、一般にはパートナー組織からの外部ユーザーであり、Azure AD 組織に自分たちの資格情報を使用してサインインするように招待します。 この B2B コラボレーション ユーザー (一般に、"*ゲスト ユーザー*" とも呼ばれます) は、共有するアプリとリソースにアクセスできます。 B2B コラボレーション ユーザーのユーザー オブジェクトは、従業員と同じディレクトリに作成されます。 B2B コラボレーション ユーザー オブジェクトはディレクトリ内の特権が既定で制限されており、これらを従業員のように管理したり、グループに追加したりすることが可能です。

招待側の組織のニーズに応じて、Azure AD B2B コラボレーション ユーザーは、以下のいずれかのアカウント状態になります。

- 状態 1:Azure AD の外部インスタンスに所属し、招待側組織のゲスト ユーザーとして表されます。 この場合、B2B ユーザーは招待されたテナントに属している Azure AD アカウントを使用してサインインします。 パートナー組織が Azure AD を使用しない場合でも、Azure AD にゲスト ユーザーが作成されます。 要件は、ゲスト ユーザーが招待に応じること、および Azure AD が彼らの電子メール アドレスを検証することです。 この状態は、Just-In-Time (JIT) テナント、"バイラル テナント"、またはアンマネージド Azure AD とも呼ばれます。

   > [!IMPORTANT]
   > **2021 年 11 月 1 日以降**、既存のすべてのテナントに対して電子メールのワンタイム パスコード機能をオンにし、新しいテナントに対して既定で有効にするように、変更の展開を開始します。 この変更の一環として、アカウントとテナントの作成を停止します。B2B コラボレーションの招待の引き換え中に、Microsoft は、管理されていない (「バイラル」) Azure AD アカウントとテナントの作成を停止します。 休暇中の中断およびデプロイ ロックダウンを最小限に抑えるために、大多数のテナントは 2022 年 1 月に変更が表示されます。 電子メール ワンタイム パスコード機能を有効にするのは、これによりゲスト ユーザーにシームレスなフォールバック認証方法が提供されるからです。 ただし、この機能が自動的に有効にならないようにする場合は、[無効にする](one-time-passcode.md#disable-email-one-time-passcode)ことができます。

- 状態 2:Microsoft アカウントまたは他のアカウントに所属し、ホスト組織のゲスト ユーザーとして表されます。 この場合、ゲスト ユーザーは Microsoft アカウントまたはソーシャル アカウント (google.com など) でサインインします。 招待されたユーザーの ID は、招待に応じる際に招待側組織のディレクトリ内に Microsoft アカウントとして作成されます。

- 状態 3:ホスト組織のオンプレミス Active Directory に所属し、ホスト組織の Azure AD と同期されます。 Azure AD Connect を使用し、UserType = Guest の Azure AD B2B ユーザーとして、パートナー アカウントをクラウドと同期することができます。 [ローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する方法](hybrid-on-premises-to-cloud.md)に関する記事を参照してください。

- 状態 4:ホスト組織の Azure AD に所属し、UserType = Guest で、資格情報はホスト組織によって管理されています。

  ![ユーザーの 4 つの状態を示す図](media/user-properties/redemption-diagram.png)


ここで、Azure AD B2B コラボレーション ユーザーが Azure AD でどのように見えるかを見てみましょう。

### <a name="before-invitation-redemption"></a>招待に応じる前

状態 1 アカウントと状態 2 アカウントは、ゲスト ユーザー自身の資格情報を使用して共同作業するようにゲスト ユーザーを招待した結果です。 招待が最初にゲスト ユーザーに送信されると、アカウントがディレクトリに作成されます。 認証はゲスト ユーザーの ID プロバイダーによって実行されるため、このアカウントには関連付けられた資格情報はありません。 ディレクトリ内のゲスト ユーザー アカウントの **[ソース]** プロパティは **[ユーザーが招待されました]** に設定されています。 

![招待に応じる前のユーザー プロパティを示すスクリーンショット](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>招待に応じた後

ゲスト ユーザーが招待を受け入れると、 **[ソース]** プロパティはゲスト ユーザーの ID プロバイダーに基づいて更新されます。

状態 1 のゲスト ユーザーの場合、 **[ソース]** は **[外部の Azure Active Directory]** です。

![オファーに応じた後の状態 1 のゲスト ユーザー](media/user-properties/after-redemption-state1.png)

状態 2 のゲスト ユーザーの場合、 **[ソース]** は **[Microsoft アカウント]** です。

![オファーに応じた後の状態 2 のゲスト ユーザー](media/user-properties/after-redemption-state2.png)

状態 3 と状態 4 のゲスト ユーザーの場合、次のセクションで説明するように、 **[ソース]** プロパティは **[Azure Active Directory]** または **[Windows Server AD]** に設定されます。

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B コラボレーション ユーザーの主要なプロパティ
### <a name="usertype"></a>UserType
このプロパティは、ユーザーとホスト テナントとの関係を示します。 このプロパティは次の 2 つの値を取ります。
- メンバー:この値は、ホスト組織の従業員や組織の給与支払い名簿にあるユーザーを示します。 たとえば、このユーザーは、内部専用のサイトにアクセスできることが想定されます。 このユーザーは外部コラボレーターとは見なされません。

- ゲスト:この値は、社内ユーザーと見なされないユーザー (外部コラボレーター、パートナー、顧客など) を示します。 このようなユーザーに、CEO の社内メモの送信や各種手当の給付が行われることは想定されません。

  > [!NOTE]
  > UserType は、ユーザーのサインイン方法、ユーザーのディレクトリ ロールなどとは関係ありません。 このプロパティは、単にユーザーとホスト組織との関係を示しており、組織はこのプロパティに基づくポリシーを強制できます。

価格に関する詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory)」を参照してください。

### <a name="source"></a>source
このプロパティは、ユーザーのサインイン方法を示します。

- ユーザーが招待されました:このユーザーは招待されましたが、まだ招待に応じていません。

- 外部の Azure Active Directory:このユーザーは外部組織に所属し、他の組織に属している Azure AD アカウントを使用して認証を行います。 この種類のサインインは、状態 1 に対応しています。

- Microsoft アカウント:このユーザーは Microsoft アカウントに所属し、Microsoft アカウントを使用して認証を行います。 この種類のサインインは、状態 2 に対応しています。

- Windows Server AD: このユーザーは、この組織に属しているオンプレミス Active Directory からサインインされました。 この種類のサインインは、状態 3 に対応しています。

- Azure Active Directory:このユーザーは、この組織に属している Azure AD アカウントを使用して認証されます。 この種類のサインインは、状態 4 に対応しています。
  > [!NOTE]
  > Source と UserType は、独立したプロパティです。 Source の値は、UserType の特定の値を意味しません。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B ユーザーをゲストではなくメンバーとして追加できるか
通常は、Azure AD B2B ユーザーとゲスト ユーザーは同義です。 そのため、Azure AD B2B コラボレーション ユーザーは、既定では UserType = Guest のユーザーとして追加されます。 ただし、一部のケースでは、パートナー組織がより大きな組織のメンバーであり、ホスト組織もその組織に所属しています。 そのような場合は、ホスト組織がパートナー組織のユーザーをゲストではなくメンバーとして扱いたいことがあります。 Azure AD B2B Invitation Manager API を使用して、パートナー組織のユーザーをホスト組織にメンバーとして追加または招待します。

## <a name="filter-for-guest-users-in-the-directory"></a>ディレクトリのゲスト ユーザーのフィルター処理

![ゲスト ユーザーのフィルターを示すスクリーンショット](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType の変換
PowerShell を使用して、UserType をメンバーからゲストに、またはその逆に変換できます。 ただし、UserType プロパティはユーザーと組織の関係を表しています。 そのため、このプロパティは、ユーザーと組織の関係が変化した場合にのみ変更するようにします。 ユーザーの関係が変化した場合に、ユーザー プリンシパル名 (UPN) を変更する必要はありますか。 また、同じリソースへのアクセス権を引き続きユーザーに持たせる必要がありますか。 メールボックスを割り当てる必要があるか、などの疑問です。 

## <a name="remove-guest-user-limitations"></a>ゲスト ユーザー制限の削除
ゲスト ユーザーに、より高い権限を付与したい場合があります。 ゲスト ユーザーを任意のロールに追加することができます。また、メンバーと同じ権限を付与するために、ディレクトリでの既定のゲスト ユーザー制限を削除することもできます。

既定の制限を無効にして、会社のディレクトリのゲスト ユーザーにメンバー ユーザーと同じアクセス許可を持たせることができます。

![ユーザー設定の [外部ユーザー] オプションを示すスクリーンショット](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Exchange のグローバル アドレス一覧にゲスト ユーザーを表示できますか。
はい。 既定では、ゲスト オブジェクトは組織のグローバル アドレス一覧には表示されませんが、Azure Active Directory PowerShell を使用してそれらを表示できます。 詳細については、[Microsoft 365 グループごとのゲスト アクセスに関する記事](/microsoft-365/solutions/per-group-guest-access)の「グローバル アドレス一覧にゲストを追加する」を参照してください。

## <a name="can-i-update-a-guest-users-email-address"></a>ゲスト ユーザーのメール アドレスを更新できますか。

ゲスト ユーザーが招待を承諾し、その後メール アドレスを変更した場合、新しいメールはディレクトリ内のゲスト ユーザー オブジェクトに自動的に同期されません。 メールのプロパティは、[Microsoft Graph API](/graph/api/resources/user) を介して作成されます。 メールのプロパティは、Microsoft Graph API、Exchange 管理センター、または [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser) 経由で更新できます。 この変更は、Azure AD ゲスト ユーザー オブジェクトに反映されます。

## <a name="next-steps"></a>次のステップ

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [B2B コラボレーション ユーザーのトークン](user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](claims-mapping.md)
