---
title: Azure AD の ID インフラストラクチャをセキュリティ保護する
titleSuffix: Azure Active Directory
description: このドキュメントでは、管理者が Azure AD 機能を使って組織をセキュリティで保護するうえで実行すべき一連の重要なアクションについて概説します
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: 160af623d29391e22ab9ffaf4014d3b1996e97ee
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113551094"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>ID インフラストラクチャをセキュリティ保護する 5 つのステップ

このドキュメントをご覧になっているお客様は、セキュリティの重要性を認識なさっていると思います。 おそらく、既に組織をセキュリティで保護する責任を負っていらっしゃるでしょう。 セキュリティの重要性を他の人に理解してもらう必要がある場合は、最新の [Microsoft セキュリティ インテリジェンス レポート](https://www.microsoft.com/security/business/security-intelligence-report)を読むようご案内ください。

このドキュメントは、Azure Active Directory の機能を使用してセキュリティ体制を強化するのに役立ちます。そのために、サイバー攻撃への対策を組織に取り入れる 5 ステップのチェックリストが使用されます。

このチェックリストは、重要な推奨アクションを迅速に展開して、組織を直ちに保護するうえで役に立ちます。ここでは、以下の方法について説明されています。

* 資格情報を強化する。
* 攻撃の対象となる領域を減らす。
* 脅威への対応を自動化する。
* クラウド インテリジェンスを利用します。
* エンドユーザー セルフサービスを有効にします。

このチェックリストの確認時に、どの機能と手順が完了しているかを必ず追跡しておいてください。

> [!NOTE]
> このドキュメントの推奨事項の多くは、Azure Active Directory を ID プロバイダーとして使用するよう構成されたアプリケーションにのみ適用されます。 シングル サインオンを使用するようアプリを構成すると、資格情報ポリシーや脅威の検出、監査、ログの記録などの機能がこれらのアプリケーションに追加されるという様々な利点があります。 [Azure AD アプリケーション管理](../../active-directory/manage-apps/what-is-application-management.md)は、これらのすべての推奨事項の基盤となります。

このドキュメントの推奨事項は、Azure AD テナントの ID セキュリティ構成の自動化された評価である [ID セキュリティ スコア](../../active-directory/fundamentals/identity-secure-score.md)と一致しています。 組織は Azure AD ポータルの ID セキュリティ スコア ページを使用して、現在のセキュリティ構成におけるギャップを探し、セキュリティに関する最新の Microsoft の[ベスト プラクティス](identity-management-best-practices.md)に従っていることを確認できます。 セキュリティ スコア ページで各推奨事項を実装すると、スコアが上がり、進行状況を追跡することができ、さらに他の似た規模の組織や業界と実装を比較するのに役立ちます。

![ID セキュリティ スコア](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> ここで説明する機能の多くでは、Azure AD Premium サブスクリプションが必要ですが、一部は無料です。 詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」と [Azure AD デプロイに関するチェックリスト](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md)をご覧ください。

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>作業を開始する前に、次のことを行います。MFA で特権アカウントを保護します

このチェックリストを読み始める前に、読んでいる間にセキュリティが侵害されないようにしてください。 まず、特権アカウントを保護する必要があります。

攻撃者に特権アカウントを支配されると、多大な損害が発生する可能性があるので、まず特権アカウントを保護することが重要です。 [Azure AD セキュリティの既定値](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)または[条件付きアクセス](../../active-directory/conditional-access/plan-conditional-access.md)を使用して、すべての管理者に関して [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) を有効にして要求します。 MFA を実装していない場合はすぐに実装してください。 これは非常に重要です。

準備はできたでしょうか。 それでは、チェックリストの確認に入りましょう。

## <a name="step-1---strengthen-your-credentials"></a>ステップ 1. - 資格情報を強化する

ほとんどのエンタープライズのセキュリティ侵害では、パスワード スプレーや侵害リプレイ、フィッシングなど、いくつかある手法のいずれかによるアカウント侵害が原因です。 次の動画 (45 分) で、これらの攻撃についてご確認ください。
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>組織で強力な認証が使用されるようにする

パスワードの推測、フィッシング、マルウェアによる盗難、または再利用が頻繁に行われることを考えると、何らかの強力な資格情報でパスワードを強化することが非常に重要です。[Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) の詳細をご確認ください。

ID セキュリティの基本レベルを簡単に有効にするために、[Azure AD セキュリティの既定値](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)によりワンクリックでの有効化を使用できます。 セキュリティの既定値により、テナント内のすべてのユーザーに Azure AD MFA が適用され、従来のプロトコル テナント全体からのサインインがブロックされます。

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>攻撃されやすいパスワードを禁止して、従来の複雑さと有効期限にのルールを無効にする。

多くの組織は、従来の複雑さ (特殊文字、数字、大文字、小文字を要求する) とパスワードの有効期限に関するルールを使用しています。 [Microsoft の調査](https://aka.ms/passwordguidance)によると、これらのポリシーは推測されやすいパスワードをユーザーが選択する原因となります。

Azure AD の[パスワードの動的禁止](../../active-directory/authentication/concept-password-ban-bad.md)機能では、簡単に推測できるパスワードをユーザーが設定するの防ぐために、現在の攻撃者の行動が利用されます。 この機能はユーザーがクラウドで作成されるときは常に有効ですが、[Windows Server Active Directory 用の Azure AD パスワード保護](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)をデプロイするときにハイブリッド組織に対しても使用できるようになっています。 Azure AD パスワード保護は、ユーザーがこれらの一般的なパスワードを選択することをブロックし、指定したカスタム キーワードを含むパスワードをブロックするように拡張できます。 たとえば、ユーザーが会社の製品名や地元のスポーツ チームを含むパスワードを選択するのを防止することができます。

[NIST ガイダンス](https://pages.nist.gov/800-63-3/sp800-63b.html)に基づく次のような先進のパスワード ポリシーを採用することをお勧めします。

1. パスワードに 8 文字以上を必要とする。 ユーザーが予測可能なパスワードを選んだり、パスワードをファイルに保存したり、書き留めたりする原因となるため、必ずしも長ければ長いほど良いというわけではありません。
2. 有効期限のルールを無効にする。このルールは、簡単に推測されるパスワード (**Spring2019!** など) をユーザーが使用する原因になります
3. 文字構成の要件を無効にして、よく攻撃されるパスワードをユーザーが選択するのを防ぐ。これは、文字構成の要件が、パスワードで予測可能な文字置換をユーザーが選択する原因となるためです。

Azure AD で直接 ID を作成した場合、[PowerShell を使用してユーザーのパスワードの期限切れを防ぐ](../../active-directory/authentication/concept-sspr-policy.md)ことができます。 ハイブリッド組織では、[ドメイン グループ ポリシー設定](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10))または [Windows PowerShell](/powershell/module/activedirectory/set-addefaultdomainpasswordpolicy) を使用してこれらのポリシーを実装する必要があります。

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>資格情報の漏洩から保護し、障害に対する回復力を高める

組織がパススルー認証またはフェデレーションによるハイブリッド ID ソリューションを使用する場合、次の 2 つの理由から、パスワード ハッシュ同期を有効にする必要があります。

* Azure AD 管理の[資格情報が漏洩したユーザー](../../active-directory/identity-protection/overview-identity-protection.md) レポートでは、"闇サイト" で公開されているユーザー名とパスワードのペアについて、警告を受け取れます。 驚くほど大量のパスワードが、後にセキュリティ侵害されるサードパーティ サイトでのパスワードの再利用、フィッシング、マルウェアによって漏洩しています。 Microsoft は、漏洩した資格情報を多数発見しており、それらがお客様の組織の資格情報に一致する場合に、このレポートでお客様に報告します。ただし、そのためには、[パスワード ハッシュの同期を有効にしておくか](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)、クラウド専用 ID を所有している必要があります。
* (たとえばランサムウェア攻撃で) オンプレミスの障害が発生した場合、[パスワード ハッシュ同期を使用するクラウド認証](../../active-directory/hybrid/choose-ad-authn.md)に切り替えることができます。このバックアップ認証方法では、Azure Active Directory による認証が構成されたアプリ (Microsoft 365 など) へのアクセスを継続できます。 この場合、IT スタッフはオンプレミスの停止が解決されるまで、個人のメール アカウントに頼ってデータを共有する必要はありません。

[パスワード ハッシュ同期](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)のしくみについて、詳しくご確認ください。

> [!NOTE]
> パスワード ハッシュ同期を有効にしているときに Azure AD Domain Services を使用すると、Kerberos (AES 256) ハッシュとオプションで NTLM (RC4、ソルトなし) ハッシュも暗号化され、Azure AD と同期されます。

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS エクストラネットのスマート ロックアウトを実装する

Azure AD で直接認証されるようアプリケーションを構成している組織は、[Azure AD スマート ロックアウト](../../active-directory/authentication/concept-sspr-howitworks.md)を利用できます。 Windows Server 2012 R2 で AD FS を使っている場合は、AD FS の[エクストラネット ロックアウト保護](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)を実装します。 Windows Server 2016 で AD FS を使っている場合は、[エクストラネット スマート ロックアウト](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)を実装します。 AD FS スマート エクストラネット ロックアウトは、ユーザーが Active Directory からロックアウトされないようにしながら、AD FS に対するブルート フォース攻撃を防ぎます。

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>本質的に安全な、より使いやすい資格情報を活用する

[Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) を使用して、PC およびモバイル デバイスでパスワードを強力な 2 要素認証に置き換えることができます。 この認証はデバイスに安全に関連付けられた新しい種類のユーザー資格情報で構成され、生体認証または PIN が使用されます。

## <a name="step-2---reduce-your-attack-surface"></a>ステップ 2. - 攻撃の対象となる領域を減らす

漏洩したパスワードが広まりやすいことを考えると、組織において攻撃の対象となる領域を最小化することが重要です。 安全性が低い旧式のプロトコルの使用をやめ、アクセス エントリ ポイントを制限して、リソースへの管理アクセス権の制御を強化すると、攻撃の対象となる領域を減らせます。

### <a name="block-legacy-authentication"></a>レガシ認証をブロックする

Azure AD による認証と会社データへのアクセスに、独自の古い方法がアプリで使用されている場合、組織には別のリスクが生じます。 レガシ認証が使用されているアプリは、POP3 クライアント、IMAP4 クライアント、SMTP クライアントなどです。 レガシ認証アプリでは、ユーザーの代わりに認証が行われ、Azure AD による高度なセキュリティ評価の実行が妨げられます。 代わりとなる最新の認証では、多要素認証と条件付きアクセスがサポートされているので、セキュリティ リスクを抑えられます。 次の 3 つのアクションをお勧めします。

1. [AD FS を使用している場合はレガシ認証](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)をブロックする。
2. [最新の認証を使用するよう SharePoint Online と Exchange Online](../../active-directory/conditional-access/block-legacy-authentication.md) を設定する。
3. Azure AD Premium がある場合は、条件付きアクセス ポリシーを使用して[レガシ認証をブロック](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md)し、それ以外の場合は、[Azure AD セキュリティの既定値](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)を使用します。

### <a name="block-invalid-authentication-entry-points"></a>無効な認証エントリ ポイントをブロックする

侵害想定の考え方を取り入れて、ユーザー資格情報の漏洩が発生した場合にその影響を抑える必要があります。 環境内のアプリごとに有効なユース ケースを検討して、どのグループ、ネットワーク、デバイスなどの要素が承認されるかを判断し、残りをブロックします。 [Azure AD の条件付きアクセス](../../active-directory/conditional-access/overview.md)では、定義した特定の条件に基づいて、承認されたユーザーがどのようにアプリとリソースにアクセスするかを制御できます。

### <a name="restrict-user-consent-operations"></a>ユーザーの同意操作を制限する

さまざまな [Azure AD アプリケーションの同意](../../active-directory/develop/application-consent-experience.md)、[アクセス許可と同意の種類](../../active-directory/develop/v2-permissions-and-consent.md)、それらが組織のセキュリティ方針に与える意味を理解することが重要です。 既定では、Azure AD のすべてのユーザーは、Microsoft ID プラットフォームを活用するアプリケーションに組織のデータにアクセスすることを許可できます。 ユーザーが自分で同意することを許可すると、Microsoft 365、Azure、その他のサービスと統合する便利なアプリケーションを簡単に入手できますが、慎重に使用し、監視しないとリスクが発生する可能性があります。

Microsoft では、[エンドユーザーの同意を制限](../../active-directory/manage-apps/configure-user-consent.md)して、検証された発行元からのアプリに対してのみユーザーに同意を許可すること、および選択したアクセス許可のみを許可することをお勧めします。 エンド ユーザーの同意を無効にした場合でも、以前の同意の許可は有効ですが、それより後のすべての同意操作は管理者が実行する必要があります。 制限された場合、ユーザーは、統合された[管理者の同意要求ワークフロー](../../active-directory/manage-apps/configure-admin-consent-workflow.md)または独自のサポート プロセスを通して管理者の同意を要求できます。 エンドユーザーの同意を制限する前に、[推奨事項](../../active-directory/manage-apps/manage-consent-requests.md)を使用して、組織におけるこの変更を計画してください。 すべてのユーザーにアクセスを許可するアプリケーションについては、[すべてのユーザーの代わりに同意を与え](../../active-directory/develop/v2-admin-consent.md)、個人としてまだ同意していないユーザーがアプリにアクセスできるようにすることを検討してください。 シナリオによってはアプリケーションを利用できるユーザーを限定する場合、[アプリケーション割り当て](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)と条件付きアクセスを利用し、[特定のアプリ](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md)へのユーザー アクセスを制限してください。

ユーザーの摩擦を減らすために、サポート量を最小限に抑えるために、また、ユーザーが Azure AD 以外の資格情報でアプリケーションに新規登録することを防ぐために、ユーザーが新しいアプリケーションに関する管理者承認を要求できるようにしてください。 同意操作を規制したら、管理者はアプリと同意済みのアクセス許可を定期的に監査します。


### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を実装する

さらに "侵害想定" に基づくと、セキュリティ侵害されたアカウントが特権ロールで操作され得る確率を最小限に抑える必要があります。

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) は、以下をサポートしており、アカウントの特権を最小限に抑えるのに役立ちます。

* 管理者ロールに割り当てられたユーザーを特定して管理する。
* 削除すべき未使用の特権ロールまたは余計な特権ロールを把握する。
* 多要素認証によって特権ロールが保護されるようルールを確立する。
* 特権タスクを完了するのに十分な期間のみ特権ロールが付与されるようルールを確立する。

Azure AD PIM を有効にして管理者ロールが割り当てられたユーザーを表示し、それらのロールの不必要なアカウントを削除します。 残りの特権ユーザーを永続から候補に変更します。 最後に、適切な変更制御により、これらの特権ロールにアクセスできる必要がある場合に安全にアクセスできるよう、適切なポリシーを確立します。

特権アカウントのプロセスをデプロイする一環として、自分自身をロックアウトしてしまった場合でも Azure AD にアクセスできるよう、[少なくとも 2 つの緊急アカウントを作成するベスト プラクティス](../../active-directory/roles/security-planning.md)に従ってください。

## <a name="step-3---automate-threat-response"></a>ステップ 3. - 脅威への対応を自動化する

Azure Active Directory には、検出と対応に時間差が生じないよう、攻撃を自動的に遮断する多数の機能が備わっています。 犯罪者が環境に侵入するために使える時間を減らせれば、コストとリスクを抑えることができます。 実行できる具体的な手順は以下のとおりです。

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection を使用してユーザーのリスク セキュリティ ポリシーを実装する

ユーザー リスクは、ユーザーの ID がセキュリティ侵害された確率を示すもので、ユーザーの ID に関連付けられている[ユーザー リスク検出](../../active-directory/identity-protection/overview-identity-protection.md)に基づいて計算されます。 ユーザー リスク ポリシーは、特定のユーザーまたはグループに関してリスク レベルを評価する条件付きアクセスポリシーです。 低、中、高のリスクレベルに基づいて、アクセスをブロックしたり、多要素認証を使用して安全なパスワードへの変更を要求したりするよう、ポリシーを構成できます。 Microsoft は、リスクの高いユーザーについて、安全なパスワードへの変更を要求することをお勧めします。

![[ユーザー] が選択された、リスクのフラグが設定されたユーザーを示すスクリーンショット。](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection を使用してサインインのリスク ポリシーを実装する

サインイン リスクは、アカウント所有者でないユーザーが ID を使用してサインオンを試みている確率です。 [サインイン リスク ポリシー](../../active-directory/identity-protection/overview-identity-protection.md)は、特定のユーザーまたはグループに関してリスク レベルを評価する条件付きアクセスポリシーです。 リスク レベル (高/中/低) に基づいて、アクセスをブロックしたり、多要素認証を適用したりするよう、ポリシーを構成できます。 リスクが中以上のサインインには多要素認証を適用するようにしてください。

![匿名の IP からのサインイン](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>ステップ 4 - クラウド インテリジェンスを利用する

セキュリティ関連イベントの監査とログ、および関連する警告は、効率的な保護戦略に欠かせない構成要素です。 セキュリティのログやレポートは、疑わしいアクティビティの電子記録となり、外部からネットワークへの侵入または内部からの攻撃が試みられたこと、または成功したことを示すパターンを検出するために役立ちます。 監査機能を使うと、ユーザー アクティビティの監視、規制へのコンプライアンスの文書化、フォレンジック分析の実行などが可能になります。 警告によってセキュリティ イベントが通知されます。

### <a name="monitor-azure-ad"></a>Azure AD を監視する

Microsoft Azure のサービスや機能が提供する構成可能なセキュリティ監査およびログのオプションは、セキュリティ ポリシーやセキュリティ メカニズムのギャップを識別してセキュリティ侵害を防止するための対策を講じるうえで役立ちます。 [Azure のログと監査](log-audit.md)を使用できるほか、[Azure Active Directory ポータルの監査アクティビティ レポート](../../active-directory/reports-monitoring/concept-audit-logs.md)を使用できます。

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>ハイブリッド環境で Azure AD Connect Health を監視する

[Azure AD Connect Health による AD FS の監視](../../active-directory/hybrid/how-to-connect-health-adfs.md)では、潜在的な問題に関するより優れた分析情報と、AD FS インフラストラクチャへの攻撃の可視性が得られます。 Azure AD Connect Health では、詳細、解決手順、関連ドキュメントへのリンクが含まれた警告のほか、認証トラフィックに関するいくつかのメトリックの利用状況分析、パフォーマンスの監視、レポートが提供されます。

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD Identity Protection イベントを監視する

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) は、通知、監視、およびレポートのツールであり、組織の ID に影響する潜在的な脆弱性を検出するために使用できます。 感染しているデバイス、匿名の IP アドレス、疑わしいアクティビティに関連した IP アドレス、および不明な場所からのサインイン、漏洩した資格情報、あり得ない移動など、リスク検出を検出します。 通知アラートを有効にすると、危険な状態のユーザーのメールや週間ダイジェスト メールが届きます。

Azure AD Identity Protection で提供される 2 つの重要なレポートを、毎日監視する必要があります。
1. リスクの高いサインイン レポートでは、調査する必要があるユーザー サインイン アクティビティが明らかになります。正当な所有者がサインインを実行していない可能性があります。
2. リスクの高いユーザー レポートでは、侵害された可能性があるユーザー アカウントが明らかになります。たとえば、リークが検出された資格情報や、あり得ない移動イベントが発生する別の場所からサインインしたユーザーなどです。

![スクリーンショットに、ユーザーとそのリスク レベルがある Azure AD Identity Protection のウィンドウが示されています。](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>監査アプリと同意されたアクセス許可

だまされたユーザーが侵害された Web サイトやアプリに移動することで、それらがユーザーのプロファイル情報やメールなどのユーザー データにアクセスできるようになる可能性があります。 悪意のあるアクターは、受け取った同意されたアクセス許可を使用して、メールボックスの内容を暗号化し、メールボックス データを回復するための身代金を要求できます。 ユーザーによって与えられたアクセス許可を[管理者が見直し、監視します](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)。あるいは、既定でユーザーが同意を与えられるようになっている場合、それを管理者が無効にします。

ユーザーによって付与されたアクセス許可を監査するだけでなく、プレミアム環境で[危険または望ましくない OAuth アプリケーションを検索する](/cloud-app-security/investigate-risky-oauth) こともできます。

## <a name="step-5---enable-end-user-self-service"></a>ステップ 5 - エンドユーザー セルフサービスを有効にする

できるだけセキュリティと生産性のバランスをとる必要があります。 長期的なセキュリティの基盤を構築するという考え方と同様の方針の運用アプローチに従って、警戒を緩めないままユーザーに権限を与えることで、組織の手間を省くことができます。

### <a name="implement-self-service-password-reset"></a>セルフサービス パスワード リセットを実装する

Azure AD の[セルフサービス パスワード リセット (SSPR)](../../active-directory/authentication/tutorial-enable-sspr.md) では、IT 管理者は簡単に、ユーザーがヘルプ デスクや管理者の手を借りずにパスワードやアカウントのリセットまたはロック解除を行えるようにできます。 このシステムには、ユーザーがいつパスワードをリセットしたかを追跡する詳細なレポートと、誤用または悪用について警告する通知が用意されています。

### <a name="implement-self-service-group-and-application-access"></a>セルフサービス グループとアプリケーションのアクセスを実装する

Azure AD を使用すると、セキュリティ グループ、Microsoft 365 グループ、アプリケーション ロール、アクセス パッケージ カタログを使用して、管理者以外のユーザーがリソースへのアクセスを管理できます。  [セルフサービス グループ管理](../../active-directory/enterprise-users/groups-self-service-management.md)を使用すると、グループ所有者は、管理者ロールを割り当てられることなく、自分のグループを管理できます。 ユーザーは、自身の依頼の処理に管理者に頼らずに Microsoft 365 グループを作成および管理することもでき、未使用のグループも自動的に期限切れになります。  [Azure AD エンタイトルメント管理](../../active-directory/governance/entitlement-management-overview.md)を使用すると、包括的なアクセス要求ワークフローと自動有効期限を使用して委任と可視化をさらに高めることができます。  管理者以外のユーザーが所有するグループ、Teams、アプリケーション、および SharePoint Online サイト用に独自のアクセス パッケージを構成する機能を、それらのユーザーに委任することができます。これには、従業員のマネージャーやビジネス パートナー スポンサーの承認者としての構成など、アクセスを承認する必要があるユーザー用のカスタム ポリシーを使用します。

### <a name="implement-azure-ad-access-reviews"></a>Azure AD アクセス レビューを実装する

[Azure AD アクセス レビュー](../../active-directory/governance/access-reviews-overview.md)では、アクセス パッケージとグループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、特権ロールの割り当てを管理して、セキュリティ標準を確実に維持できます。  ユーザー自身、リソース所有者、その他のレビュー担当者による通常の監視によって、ユーザーが必要なくなったアクセスを長期間にわたって保持しないですみます。

## <a name="summary"></a>まとめ

ID インフラストラクチャのセキュリティについては多数の側面があります。しかし、この 5 ステップのチェックリストを利用すれば、セキュリティで保護されたより安全な ID インフラストラクチャをすばやく実現できます。

* 資格情報を強化する。
* 攻撃の対象となる領域を減らす。
* 脅威への対応を自動化する。
* クラウド インテリジェンスを利用します。
* セルフヘルプによる、より予測可能で完全なエンドユーザー セキュリティを有効にする。

Microsoft は、お客様が ID セキュリティの重要性を認識していることを理解しています。ぜひ、このドキュメントを、組織のセキュリティ体制を強化するためのロードマップとしてお役立てください。

## <a name="next-steps"></a>次のステップ

推奨アクションの計画と展開にサポートが必要な場合は、[Azure AD プロジェクト デプロイ計画](../../active-directory/fundamentals/active-directory-deployment-plans.md)をヘルプとして参照してください。

これらの手順を完全に修了したら、Microsoft の [ID セキュリティ スコア](../../active-directory/fundamentals/identity-secure-score.md)を使用します。これにより、[最新のベスト プラクティス](identity-management-best-practices.md)とセキュリティ上の脅威について常に把握し続けることができます。
