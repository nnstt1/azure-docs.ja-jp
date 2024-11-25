---
title: セキュリティで保護された PaaS デプロイのベスト プラクティス - Microsoft Azure
description: Azure 上でセキュリティで保護されたクラウド アプリケーションを設計、構築、管理するためのベスト プラクティスについて学習し、さらに PaaS と他のクラウド サービス モデルを比較し、セキュリティ上のメリットについて理解します。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2021
ms.author: terrylan
ms.openlocfilehash: 558eebf12179b04fb9a76c3db0195c4e812e956c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335444"
---
# <a name="securing-paas-deployments"></a>PaaS デプロイをセキュリティで保護する

この記事では、次のことに役立つ情報を提供します。

- クラウドでアプリケーションをホスティングすることに対するセキュリティ上の利点を理解する
- 他のクラウド サービス モデルに対するサービスとしてのプラットフォーム (PaaS) のセキュリティ上の利点を評価する
- セキュリティの取り組みを、ネットワークを中心としたアプローチから ID を中心とした境界セキュリティのアプローチに切り替える
- 一般的な PaaS セキュリティのベスト プラクティスの推奨事項を実装する

「[セキュリティで保護されたアプリケーションを Azure 上で開発する](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)」は、クラウド用のアプリケーションを開発する際に、ソフトウェア開発ライフサイクルの各段階で考慮する必要があるセキュリティの問題と制御に関する一般的なガイドです。

## <a name="cloud-security-advantages"></a>クラウド セキュリティの利点
お客様と Microsoft との[責任の分担](shared-responsibility.md)を理解することが重要です。 オンプレミスでは、お客様はスタック全体を所有しますが、クラウドに移行すると、責任の一部は Microsoft に移譲されます。

[クラウド内に存在することには、セキュリティ上の利点](shared-responsibility.md#cloud-security-advantages)があります。 オンプレミス環境では、組織は責任を果たしきれず、セキュリティに投資できるリソースが限られていることが多いため、攻撃者がすべての階層で脆弱性を悪用できる環境が生まれています。

組織は、プロバイダーの提供するクラウドベースのセキュリティ機能とクラウド インテリジェンスを利用して、脅威の検出と応答時間を向上させることができます。  クラウド プロバイダーに責任をシフトすることで、セキュリティの適用範囲を広げることができるため、これまでセキュリティに費やしてきたリソースと予算を、事業のその他の優先事項に割り当てることができます。

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>PaaS クラウド サービス モデルのセキュリティ上の利点
オンプレミスに対する Azure PaaS デプロイのセキュリティ上の利点を見ていきましょう。

![PaaS のセキュリティ上の利点](./media/paas-deployments/advantages-of-paas.png)

スタックの一番下、つまり物理インフラストラクチャから始めますが、Microsoft によって一般的なリスクと責任が軽減されます。 Microsoft Cloud は Microsoft によって継続的に監視されているため、攻撃することは困難です。 Microsoft Cloud を標的として追い続けることは、攻撃者にとって意味がありません。 莫大な資金とリソースがない限り、攻撃者は他の標的に移動します。  

スタックの真ん中の部分に関しては、PaaS デプロイとオンプレミスで違いはありません。 アプリケーション層、アカウントの管理層、アクセスの管理層などでは同じリスクがあります。 この記事の「次のステップ」セクションでは、これらのリスクを取り除いたり最小限に抑えるためのベスト プラクティスを紹介します。

スタックの一番上、つまりデータ ガバナンスと権利管理においては、キー管理によって軽減されるリスクが 1 つあります。 (キー管理についてはベスト プラクティスで説明します。)キー管理は付加的な責任ですが、お客様の管理が不要になった領域が PaaS デプロイにあるため、リソースをキー管理に移すことができます。

Azure プラットフォームではネットワークベースのさまざまなテクノロジを使用して、強力な DDoS 保護を提供します。 ただし、ネットワークベースのすべての種類の DDoS 保護方法には、リンクごとやデータセンターごとに限界があります。 大規模な DDoS 攻撃の影響を回避するには、DDoS 攻撃に対する防御を迅速かつ自動的にスケール アウトする Azure のコア クラウド機能を活用できます。 これを実行する方法については、推奨されるプラクティスの記事で詳しく説明します。

## <a name="modernizing-the-defender-for-clouds-mindset"></a>Defender for Cloud の思考を最新式にする
PaaS デプロイにより、セキュリティへのアプローチ全般に変革がもたらされます。 すべて自分で制御しなければならない状況から、Microsoft と責任を分担する状況へと変わります。

PaaS と従来のオンプレミス デプロイにおけるもう 1 つの重要な相違点は、主要なセキュリティ境界を定義する新しい視点です。 これまでは、ネットワークこそがオンプレミスの主要なセキュリティ境界であり、オンプレミス セキュリティ設計のほとんどでは、主要なセキュリティの中心としてネットワークが使用されてきました。 PaaS デプロイでは、ID を主要なセキュリティ境界と考えることによって、お客様により充実したサービスを提供します。

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>主要なセキュリティ境界として ID のポリシーを採用
クラウド コンピューティングの 5 つの重要な特性の 1 つは広範なネットワーク アクセスであるため、ネットワーク中心の考えは関連性が低くなります。 クラウド コンピューティングが目指していることは、どこにいるかに関係なくユーザーがリソースにアクセスできるようにすることです。 ほとんどのユーザーにとって、自分たちのいる場所はインターネット上のどこかになります。

次の図は、セキュリティ境界がネットワーク境界から ID 境界へと発展してきたことを示しています。 セキュリティにおいては、ネットワークの保護よりもデータの保護、アプリとユーザーのセキュリティ管理が重要になってきています。 主な違いは、お客様が自社にとって重要なものの近くにセキュリティを配備することを望んでいる点です。

![新しいセキュリティ境界としての ID](./media/paas-deployments/identity-perimeter.png)

当初 Azure PaaS サービス (Web ロールや Azure SQL など) では、従来のネットワーク境界における防御はほとんどまたはまったく提供していませんでした。 攻撃者のねらいはインターネットに公開される (Web ロール) ことであり、認証によって新しい境界 (BLOB または Azure SQL など) が提供されると認識されていました。

最新のセキュリティ プラクティスでは、攻撃者がネットワーク境界に不正侵入していることを仮定しています。 そのため、最新の防御対策は ID に移行しています。 組織は、強力な認証と承認によるウィルス予防策 (ベスト プラクティス) を講じて、ID ベースのセキュリティ境界を確立する必要があります。

ネットワーク境界の基本原則とパターンは、数十年もの間提供されてきました。 これに対して、ID を主要なセキュリティ境界として使用する業界の経験は比較的浅いものです。 とは言え、Microsoft では一般的な推奨事項を提供するのに十分なノウハウを蓄積してきました。この推奨事項は現場における実証済みで、ほぼすべての PaaS サービスに適用されます。

ID 境界を管理するためのベスト プラクティスを次に示します。

**ベスト プラクティス**: キーと資格情報をセキュリティ保護して PaaS デプロイをセキュリティ保護する   
**詳細**: キーや資格情報の紛失は、よくある問題です。 キーやシークレットをハードウェア セキュリティ モジュール (HSM) に格納する一元化されたソリューションを使用できます。 [Azure Key Vault](../../key-vault/general/overview.md) は、HSM によって保護されているキーを使用して、認証キー、ストレージ アカウント キー、データ暗号化キー、.pfx ファイル、およびパスワードを暗号化することによって、キーとシークレットを保護します。

**ベスト プラクティス**: 資格情報やその他のシークレットをソース コードや GitHub に格納しない。   
**詳細**: 資格情報やその他のシークレットを紛失するよりも悪い唯一のことは、権限のない第三者がキーや資格情報にアクセスすることです。 攻撃者はボット テクノロジを利用して、GitHub などのコード レポジトリに格納されているキーやシークレットを検索することができます。 これらのパブリックなコード レポジトリには、キーやシークレットを格納しないようにします。

**ベスト プラクティス**: VM を直接リモート管理できる管理インターフェイスを使用して、PaaS サービスと IaaS サービスのハイブリッドで VM 管理インターフェイスを保護する。   
**詳細**: [SSH](https://en.wikipedia.org/wiki/Secure_Shell)、[RDP](https://support.microsoft.com/kb/186607)、[PowerShell リモート処理](/powershell/module/microsoft.powershell.core/enable-psremoting)などのリモート管理プロトコルを使用できます。 通常は、インターネットから VM に直接リモート アクセスできないように設定することをお勧めします。

可能であれば、Azure 仮想ネットワークの仮想プライベート ネットワークを使用するなどの代替アプローチを使用します。 別の方法が利用できない場合は、複雑なパスフレーズおよび 2 要素認証 ([Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) など) を使用するようにします。

**ベスト プラクティス**: 強力な認証と承認のプラットフォームを使用する。   
**詳細**: Azure AD で、カスタム ユーザー ストアではなくフェデレーション ID を使用するようにします。 フェデレーション ID を使用すると、プラットフォームベースのアプローチを活用して、承認された ID の管理をパートナーに委任することができます。 従業員を解雇し、その情報を複数の ID と承認システムに反映する必要がある場合は、フェデレーション ID によるアプローチが特に重要になってきます。

カスタム コードではなく、プラットフォームが提供する認証と承認のメカニズムを使用するようにします。 その理由は、カスタム認証コードの開発ではエラーが生じやすいためです。 開発者のほとんどはセキュリティ専門家ではなく、細部に留意したり、認証や承認の最新動向を把握している可能性は低くなります。 商用コード (たとえば Microsoft のコード) は、通常徹底的にセキュリティ レビューされています。

2 要素認証を使用します。 2 要素認証はユーザー名や認証パスワードの種類に固有のセキュリティ脆弱性が回避されるため、認証と承認において現在標準になっています。 Azure 管理 (ポータル/リモート PowerShell) インターフェイスと、顧客に提供されるサービスへのアクセスは、[Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) を使用するように設計、構成する必要があります。

OAuth2 や Kerberos などの標準的な認証プロトコルを使用します。 これらのプロトコルは幅広くピア レビューされており、認証と承認用にプラットフォーム ライブラリの一部として実装される可能性があります。

## <a name="use-threat-modeling-during-application-design"></a>アプリケーション設計時に脅威モデリングを使用する
Microsoft [セキュリティ開発ライフ サイクル (Security Development Lifecycle)](https://www.microsoft.com/en-us/sdl) は、設計段階にチームが脅威モデリングと呼ばれるプロセスを行う必要があることを指定しています。 このプロセスを支援するために Microsoft は [SDL Threat Modeling Tool](../develop/threat-modeling-tool.md) を作成しました。 アプリケーションの設計をモデリングし、すべての信頼境界を超える [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) 脅威を列挙することによって、早い段階で設計エラーを検出できます。

次の表は STRIDE 脅威の一覧と Azure の機能が使用する軽減策の例です。 これらの軽減策はあらゆる状況で機能するわけではありません。

| Threat | セキュリティ プロパティ | 潜在的な Azure プラットフォームの軽減策 |
| --- | --- | --- |
| なりすまし | 認証 | HTTPS 接続を要求する。 |
| 改ざん | 整合性 | TLS または SSL 証明書を検証する。 |
| 否認 | 否認防止 | Azure の[監視と診断](/azure/architecture/best-practices/monitoring)を有効にする。 |
| 情報漏えい | 機密情報 | [サービス証明書](/rest/api/appservice/certificates)を使用して保存データを暗号化する。 |
| Denial of service (サービス拒否) | 可用性 | 潜在的なサービス拒否状態のパフォーマンス メトリックを監視する。 接続のフィルターを実装する。 |
| 権限の昇格 | 承認 | [Privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md) を使用する。 |

## <a name="develop-on-azure-app-service"></a>Azure App Service での開発
PaaS である [Azure App Service](../../app-service/overview.md) を使用すると、任意のプラットフォームまたはデバイスを対象とした Web アプリとモバイル アプリを作成し、クラウドやオンプレミスにあるあらゆる場所のデータにアクセスできます。 App Service には、以前は Azure Websites および Azure Mobile Services として個別に提供されていた Web 機能とモバイル機能が含まれています。 さらに、ビジネス プロセスの自動化やクラウド API のホストに利用できる新しい機能も備えています。 単一の統合サービスである App Service により、Web、モバイル、および統合シナリオで豊富な機能セットを利用できます。

App Service 使用時のベスト プラクティスを次に示します。

**ベスト プラクティス**: [Azure Active Directory を使用して認証する](../../app-service/overview-authentication-authorization.md)。   
**詳細**: App Service は、ID プロバイダーに対して OAuth 2.0 サービスを提供します。 OAuth 2.0 は、Web アプリケーション、デスクトップ アプリケーション、および携帯電話に特定の認証フローを提供しながら、クライアント開発者のシンプル性を実現することに焦点を当てています。 Azure AD が OAuth 2.0 を使用することにより、ユーザーはモバイル アプリケーションと Web アプリケーションへのアクセスを承認することができます。

**ベスト プラクティス**: 知る必要性と最小権限という 2 つのセキュリティ原則に基づいて、アクセスを制限します。   
**詳細**: アクセスの制限は、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。 Azure RBAC を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ユーザーへのアプリケーション アクセス付与の詳細については、[アクセス管理の概要](../../role-based-access-control/overview.md)に関するページを参照してください。

**ベスト プラクティス**: キーを保護します。   
**詳細**: Azure Key Vault は、クラウド アプリケーションやサービスで使われる暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 さらに安心感を高めたい場合には、HSM でキーのインポートや生成を行うことができます。 詳細については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」を参照してください。 Key Vault を使用して、自動更新で TLS 証明書の管理することもできます。

**ベスト プラクティス**: 受信ソース IP アドレスを制限します。   
**詳細**: [App Service Environment](../../app-service/environment/intro.md) には、ネットワーク セキュリティ グループによる受信ソース IP アドレスの制限に役立つ、仮想ネットワーク統合機能が用意されています。 仮想ネットワークを使用すると、Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 詳細については、「[アプリを Azure 仮想ネットワークに統合する](../../app-service/overview-vnet-integration.md)」を参照してください。

<<<<<<< HEAD
**ベスト プラクティス**: App Service Environment のセキュリティ状態を監視する。   
**詳細**: Azure Security Center を使用して App Service Environment を監視します。 Security Center は、潜在的なセキュリティの脆弱性を識別すると、必要な管理を構成するプロセスを説明する[推奨事項](../../security-center/asset-inventory.md)を作成します。
=======
**ベスト プラクティス**: App Service 環境のセキュリティ状態を監視する。   
**詳細**: Microsoft Defender for Cloud を使用し、お使いの App Service 環境を監視します。 Defender for Cloud では、潜在的なセキュリティの脆弱性を特定すると、必要な抑制力を構成するプロセスを指示する[推奨事項](../../security-center/asset-inventory.md)が作成されます。
>>>>>>> repo_sync_working_branch

## <a name="azure-cloud-services"></a>Azure クラウド サービス
[Azure Cloud Services](../../cloud-services/cloud-services-choose-me.md) は、PaaS の 1 つの例です。 このテクノロジは、Azure App Service と同様に、スケーラブルで信頼性が高く、運用コストが低いアプリケーションをサポートするように設計されています。 App Service と同様に、Azure Cloud Services も仮想マシン (VM) 上でホストされます。 しかし、VM に対してより細かな制御を行うことができます。 Azure Cloud Services を使用する VM に独自のソフトウェアをインストールし、それらにリモートでアクセスできます。

## <a name="install-a-web-application-firewall"></a>Web アプリケーション ファイアウォールをインストールする
Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の的になるケースが増えています。 よくある攻撃の例として、SQL インジェクション攻撃やクロス サイト スクリプティング攻撃が挙げられます。 アプリケーション コードでこのような攻撃を防ぐことは困難な場合があり、厳格な保守、パッチの適用、アプリケーション トポロジの多数のレイヤーの監視が必要になることもあります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になり、アプリケーション管理者にとっては侵入の脅威からより確実に保護されるようになります。 また、WAF のソリューションは、1 か所に既知の脆弱性の修正プログラムを適用することで、個々の Web アプリケーションをセキュリティで保護する場合と比較して、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールに対応したアプリケーション ゲートウェイに簡単に変換できます。

[Web アプリケーション ファイアウォール (WAF)](../../web-application-firewall/afds/afds-overview.md) は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Application Gateway の機能です。 WAF は、[OWASP (Open Web Application Security Project) コア ルール セット](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 または 2.2.9 の規則に基づいています。

## <a name="monitor-the-performance-of-your-applications"></a>アプリケーションのパフォーマンスを監視する
監視とは、アプリケーションのパフォーマンス、正常性、可用性を見極めるために、データを収集し、分析することを指します。 効果的な監視戦略を策定することで、アプリケーションのコンポーネントの動作状況を詳細に把握できます。 問題が顕在化する前に解決できるように重大な問題を通知して、アップタイムを向上させることができます。 セキュリティに関連する可能性がある異常を検出することもできます。

[Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) を使用すると、クラウドにホストされているかオンプレミスかにかかわらず、アプリケーションの可用性、パフォーマンス、使用状況を監視できます。 Application Insights を使用することによって、アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断できます。 収集した情報を活用すれば、アプリケーションのメンテナンスや機能強化に関する選択を十分な情報に基づいて判断することができます。

Application Insights には、収集されたデータを操作するための豊富なツールが用意されています。 Application Insights では、そのデータは共通リポジトリに格納されます。 アラート、ダッシュボード、Kusto クエリ言語を使用した詳細分析などの共有機能を活用できます。

## <a name="perform-security-penetration-testing"></a>セキュリティ侵入テストを実施する
セキュリティ上の防御を検証することは、他の機能をテストすることと同じくらい重要です。 [侵入テスト](pen-testing.md)を、標準的なビルドおよびデプロイ プロセスの一環として実施してください。 デプロイしたアプリケーションに対して定期的なセキュリティ テストと脆弱性スキャンをスケジュールし、開放ポート、エンドポイント、攻撃を監視します。

ファジー テストは、形式が正しくないの入力データを、このデータを解析して使用するプログラム インターフェイス (エントリ ポイント) に提供することで、プログラム エラー (コード エラー) を見つける方法です。 「[Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/)」 (Microsoft のセキュリティ リスク検出) はクラウド ベースのツールであり、これを使用することで、ご自分のソフトウェアを Azure にデプロイする前に、バグやその他のセキュリティの脆弱性を探すことができます。 このツールは、ソフトウェアをデプロイする前に脆弱性を捕捉できるように設計されているため、ソフトウェアのリリース後にバグの修正、クラッシュの処理、攻撃への対応を行わなくても済みます。

## <a name="next-steps"></a>次のステップ
この記事では、Azure PaaS デプロイのセキュリティ上の利点およびクラウド アプリケーションのセキュリティのベスト プラクティスに重点を置いてきました。 次に、特定の Azure サービスを使用して PaaS の Web ソリューションとモバイル ソリューションをセキュリティ保護するための推奨されるプラクティスについて説明します。 まず、Azure App Service、Azure SQL Database、Azure Synapse Analytics、Azure Storage、および Azure Cloud Services から始めましょう。 他の Azure サービスの推奨されるプラクティスに関する記事が公開されると、次の一覧にリンクが提供されます。

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database と Azure Synapse Analytics](paas-applications-using-sql.md)
- [Azure ストレージ](paas-applications-using-storage.md)
- [Azure Cloud Services](../../cloud-services/security-baseline.md)
- Azure Cache for Redis
- Azure Service Bus
- Web アプリケーション ファイアウォール

クラウド用のアプリケーションを開発する際に、ソフトウェア開発ライフサイクルの各段階で考慮する必要があるセキュリティの問題と制御については、「[セキュリティで保護されたアプリケーションを Azure 上で開発する](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)」を参照してください。

Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](best-practices-and-patterns.md)」を参照してください。

Azure のセキュリティとそれに関連する Microsoft サービスの一般情報については、以下のリソースを参照してください。

- [Azure セキュリティ チーム ブログ](/archive/blogs/azuresecurity/) – Azure のセキュリティに関する最新情報を提供しています。
- [Microsoft セキュリティ レスポンス センター](https://technet.microsoft.com/library/dn440717.aspx) - このサイトでは、Azure に関する問題を含め、マイクロソフトのセキュリティの脆弱性を報告できます。メールの場合は、secure@microsoft.com 宛に報告してください。
