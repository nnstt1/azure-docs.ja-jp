---
title: ISO 27001 ASE/SQL ワークロード ブループリント サンプルのコントロール
description: Azure Policy と Azure RBAC に対する ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルのコントロール マッピング。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: f29def6ba383a0f3f9237407393b3ef97cd76ec8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675062"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>ISO 27001 ASE/SQL ワークロード ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints ISO 27001 ASE/SQL Workload ブループリント サンプルが ISO 27001 コントロールにどのようにマップされているかを説明したものです。 コントロールについて詳しくは、[ISO 27001](https://www.iso.org/isoiec-27001-information-security.html) をご覧ください。

以下のマッピングは、**ISO 27001:2013** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 その後、 **\[プレビュー\] Audit ISO 27001:2013 コントロールを選択し、監査要件のビルトイン ポリシー イニシアチブをサポートするための VM 拡張機能をデプロイ** します。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../../../policy/overview.md) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスの評価](../../../policy/how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md)に関するページを参照してください。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 職務の分離

Azure サブスクリプションの所有者を 1 人しか設定しなかった場合、管理の冗長性は確保されません。 逆に、Azure サブスクリプションの所有者が多すぎると、所有者アカウントが侵害されてセキュリティ侵害が発生するリスクが高まります。 このブループリントでは、Azure サブスクリプションの所有者数を監査する 2 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サブスクリプションの所有者を適切な数に維持することができます。 サブスクリプション所有者のアクセス許可を管理することで、職務を適切に分離することができます。

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある

## <a name="a821-classification-of-information"></a>A.8.2.1 情報の機密指定

お使いのデータベースに格納されている機密データは、Azure の [SQL 脆弱性評価サービス](../../../../azure-sql/database/sql-vulnerability-assessment.md)を使って簡単に検出し、そのデータを機密扱いにするための推奨情報を含めることができます。 このブループリントでは、SQL 脆弱性評価スキャン中に特定された脆弱性が修復されたことを監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- SQL データベースの脆弱性を修復する必要がある

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 ネットワークおよびネットワーク サービスへのアクセス

Azure では、Azure リソースにアクセスするユーザーを管理するために、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) が実装されています。 このブループリントでは、7 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure リソースへのアクセスを制御できます。 これらのポリシーは、リソースの種類や構成の使用状況を監査することで、リソースへのアクセスをより厳格に制限するためのものです。
これらのポリシーに違反しているリソースを把握することで、適切な是正措置を実施し、承認済みのユーザーだけが Azure リソースにアクセスできるようにすることができます。

- パスワードなしのアカウントが存在する Linux VM の監査結果を表示する
- パスワードなしのアカウントからのリモート接続が許可されている Linux VM の監査結果を表示する
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある
- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある
- マネージド ディスクを使用していない VM の監査

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 アクセス特権の管理

このブループリントでは、所有者アクセス許可や書き込みアクセス許可を持つ外部アカウントと、所有者アクセス許可や書き込みアクセス許可を持つ、多要素認証が有効になっていないアカウントを監査するための、4 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、アクセス特権を制限および制御することができます。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースにアクセスするユーザーを管理できます。 また、このブループリントでは、SQL Server と Service Fabric に対する Azure Active Directory 認証の使用状況を監査するために、3 つの Azure Policy 定義が割り当てられます。 Azure Active Directory 認証を使用すると、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。 また、このブループリントによって、カスタム Azure RBAC 規則の使用状況を監査するための Azure Policy 定義も割り当てられます。 カスタム Azure RBAC 規則ではエラーが発生しやすいため、カスタム Azure RBAC 規則の実装状況を把握しておくと、実装の必要性や適切性の確認に役立ちます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある
- カスタム RBAC 規則の使用監査

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 ユーザーのシークレット認証情報の管理

このブループリントでは、多要素認証が有効になっていないアカウントを監査するための、3 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。 また、このブループリントでは、Linux VM のパスワード ファイルのアクセス許可を監査し、それらが正しく設定されていない場合にアラートを生成する、2 つの Azure Policy 定義も割り当てられます。 これを設定すれば、必要な是正措置を行って、認証が侵害されないようにすることができます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- passwd ファイルのアクセス許可が 0644 に設定されていない Linux VM の監査結果を表示する

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 ユーザー アクセス権のレビュー

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 このブループリントでは、優先的にレビューする必要があるアカウント (非推奨のアカウントや、管理者特権のアクセス許可を持つ外部アカウントなど) を監査するための、4 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 アクセス権の削除や調整

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../../../role-based-access-control/overview.md) が実装されています。 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) と Azure RBAC を使用すると、ユーザー ロールを更新して組織の変更を反映できます。 必要な場合は、サインインしようとしているアカウントをブロック (または削除) して、Azure リソースへのアクセス権を直ちに削除することもできます。 このブループリントでは、削除を検討する必要がある非推奨アカウントを監査するための、2 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 安全なログオン手順

このブループリントでは、多要素認証が有効になっていないアカウントを監査するための、3 つの Azure Policy 定義が割り当てられます。 Azure AD Multi-Factor Authentication は、第 2 の認証形態を要求することでセキュリティを追加し、強力な認証を実現します。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="a943-password-management-system"></a>A.9.4.3 パスワード管理システム

このブループリントでは、最小強度やその他のパスワード要件が適用されていない Windows VM を監査する、10 個の [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、強力なパスワード ポリシーを実施できます。 パスワード強度のポリシーに違反している VM を把握できるようになるので、適切な是正措置を実施し、すべての VM ユーザー アカウントに対して、パスワード ポリシーへの準拠を徹底させることができます。

- パスワードの複雑さの設定が有効になっていない Windows VM の監査結果を表示する
- パスワードの最長有効期間が 70 日になっていない Windows VM の監査結果を表示する
- パスワードの最短有効期間が 1 日になっていない Windows VM の監査結果を表示する
- パスワードの最小文字数が 14 文字に制限されていない Windows VM の監査結果を表示する
- 以前の 24 個のパスワードの再利用が許可されている Windows VM の監査結果を表示する

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 暗号化コントロールの使用に関するポリシー

このブループリントでは、特定の暗号化コントロールを実施し、脆弱な暗号化設定の使用を監査するための、13 個の [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、暗号化コントロールの使用に関するポリシーを徹底させることができます。
最適でない暗号化構成が Azure リソースのどこに存在しているかを把握することにより、適切な是正措置を実施し、リソースの構成を情報セキュリティ ポリシーに準拠させることができます。 このブループリントによって割り当てられるポリシーでは、次の要件が定義されています: BLOB ストレージ アカウントとデータ レイク ストレージの暗号化を実施する。SQL データベースで透過的データ暗号化を実施する。ストレージ アカウント、SQL データベース、仮想マシン ディスク、および自動化アカウント変数の中に、暗号化されていないものがないか監査する。ストレージ アカウント、Function App、Web App、API Apps、および Redis Cache に対する接続の中に、安全でないものがないか監査する。仮想マシンのパスワード暗号化に脆弱なものがないか監査する。暗号化されていない Service Fabric 通信がないか監査する。

- Function App には HTTPS 経由でのみアクセスできるようにする
- Web アプリケーションには HTTPS を介してのみアクセスできるようにする
- API アプリには HTTPS を介してのみアクセスできるようにする
- 元に戻せる暗号化を使用してパスワードを格納しない Windows VM の監査結果を表示する
- 仮想マシンでディスク暗号化を適用する必要がある
- Automation アカウント変数は、暗号化する必要がある
- Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある
- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある
- SQL データベースで Transparent Data Encryption を有効にする必要がある

## <a name="a1241-event-logging"></a>A.12.4.1 イベント ログ

このブルー プリントでは、Azure リソースのログ設定を監査する 7 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、システム イベントのログ記録を徹底させることができます。
診断ログは、Azure リソース内で実行された操作の分析情報を提供します。

- Dependency Agent のデプロイの監視 - VM イメージ (OS) が一覧にない
- Virtual Machine Scale Sets における Dependency Agent のデプロイの監査 - VM イメージ (OS) が一覧にない
- [プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- 診断設定の監査
- SQL Server の監査を有効にする必要があります

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 管理者とオペレーターのログ

このブルー プリントでは、Azure リソースのログ設定を監査する 7 つの Azure Policy 定義を割り当てることで、システム イベントのログ記録を徹底させることができます。 診断ログは、Azure リソース内で実行された操作の分析情報を提供します。

- Dependency Agent のデプロイの監視 - VM イメージ (OS) が一覧にない
- Virtual Machine Scale Sets における Dependency Agent のデプロイの監査 - VM イメージ (OS) が一覧にない
- [プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- 診断設定の監査
- SQL Server の監査を有効にする必要があります

## <a name="a1244-clock-synchronization"></a>A.12.4.4 時計の同期

このブルー プリントでは、Azure リソースのログ設定を監査する 7 つの Azure Policy 定義を割り当てることで、システム イベントのログ記録を徹底させることができます。 Azure のログでは、リソース全体にまたがるイベントの時間相関レコードを作成するために、同期された内部時計が使用されます。

- Dependency Agent のデプロイの監視 - VM イメージ (OS) が一覧にない
- Virtual Machine Scale Sets における Dependency Agent のデプロイの監査 - VM イメージ (OS) が一覧にない
- [プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- Virtual Machine Scale Sets における Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)
- 診断設定の監査
- SQL Server の監査を有効にする必要があります

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 運用システムへのソフトウェアのインストール

アダプティブ アプリケーション制御は、Azure Security Center から提供されるソリューションです。Azure 内に配置された VM 上で、どのアプリケーションを実行できようにするかを制御するのに役立ちます。 このブループリントでは、許可されたアプリケーション セットへの変更を監視する Azure Policy 定義が割り当てられます。 この機能を利用して、Azure VM へのソフトウェアやアプリケーションのインストールを効率的に制御することができます。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 技術的脆弱性の管理

このブループリントでは、未実行のシステム更新、オペレーティング システムの脆弱性、SQL の脆弱性、および仮想マシンの脆弱性を Azure Security Center で監視する 5 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、情報システムの脆弱性管理を改善できます。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。

- Endpoint Protection の欠落の Azure Security Center での監視
- システム更新プログラムをマシンにインストールする必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 ソフトウェアのインストールに関する制限

アダプティブ アプリケーション制御は、Azure Security Center から提供されるソリューションです。Azure 内に配置された VM 上で、どのアプリケーションを実行できようにするかを制御するのに役立ちます。 このブループリントでは、許可されたアプリケーション セットへの変更を監視する Azure Policy 定義が割り当てられます。 ソフトウェアのインストールに関する制限は、ソフトウェアの脆弱性が発生する可能性を減らすのに役立ちます。

- 安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある

## <a name="a1311-network-controls"></a>A.13.1.1 ネットワーク制御

このブルー プリントでは、寛容なルールが適用されているネットワーク セキュリティ グループを監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、ネットワークの管理と制御を強化することができます。 ルールが寛容すぎると、意図しないネットワーク アクセスが許可される恐れがあるため、そのようなルールがないか確認する必要があります。 また、このブルー プリントでは、保護されていないエンドポイント、アプリケーション、およびストレージ アカウントを監視する 3 つの Azure Policy 定義を割り当てることができます。 ファイアウォールで保護されていないエンドポイントやアプリケーションがあったり、アクセス制限のないストレージ アカウントがあると、情報システム内の情報に対する意図しないアクセスが許可される恐れがあります。

- インターネットに接続するエンドポイント経由のアクセスを制限する必要がある
- ストレージ アカウントではネットワーク アクセスを制限する必要がある

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 情報転送のポリシーと手順

このブループリントでは、ストレージ アカウントや Azure Cache for Redis との接続の安全性を監査する 2 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サービスとの情報転送のセキュリティを強化できます。

- Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある

## <a name="next-steps"></a>次のステップ

ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルのコントロール マッピングが確認できたら、以下の記事に進んで、アーキテクチャの詳細と、このサンプルのデプロイ方法を確認してください。

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database ワークロード ブループリント - 概要](./index.md)
> [ISO 27001 App Service Environment/SQL Database ワークロード ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
