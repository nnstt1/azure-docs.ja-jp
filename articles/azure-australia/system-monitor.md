---
title: Azure Australia におけるセキュリティのシステム監視
description: オーストラリアのリージョン内で、オーストラリア政府のポリシー、規制、法令に固有の要件を満たすようにシステム監視を構成するためのガイダンス。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 8c67cc2ff2d918b8d44d8a362b3b6c1609f17f13
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312851"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Azure Australia におけるセキュリティのシステム監視

リアルタイムの監視や定期的なセキュリティ評価をはじめとする堅牢なセキュリティ戦略を策定することは、クラウドを含む IT 環境の日常の運用セキュリティを強化するために不可欠です。

クラウド セキュリティには、お客様とクラウド プロバイダーが共同で取り組みます。 Microsoft Azure には、[Australian Cyber Security Centre (ACSC) の Information Security Manual Controls](https://acsc.gov.au/infosec/ism/index.htm) (ISM) に記載されている推奨事項、具体的には一元的なイベント ログ、イベント ログ監査、およびセキュリティの脆弱性の評価と管理の実装を考慮して上記の要件に対応できるようにするための 4 つのサービスがあります。 該当する Microsoft Azure サービスは次のとおりです。

* Microsoft Defender for Cloud
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC では、これらのサービスを **保護** データに使用することを推奨しています。 これらのサービスを使用することで、IT 環境のプロアクティブな監視と分析が可能になり、セキュリティを強化するためのリソースの最適な割り当て先を十分な情報に基づいて決定できます。 各サービスは、統合ソリューションの一部として、最適な分析情報、推奨事項、保護を提供します。

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

[Microsoft Defender for Cloud](../security-center/security-center-introduction.md) は、Azure リソースとホストされたデータのセキュリティを監視して強化するための統合セキュリティ管理コンソールです。 Microsoft Defender for Cloud にはセキュリティ スコアが表示されます。これは、Azure Advisor で行われるベスト プラクティス構成の状態の分析と、Azure Policy で行われる全体的なコンプライアンス評価に基づくスコアです。

Microsoft Defender for Cloud では、Azure のお客様に次の機能が提供されます。

* セキュリティ ポリシー、評価、および推奨事項
* セキュリティ イベントの収集と検索
* アクセスとアプリケーションの制御
* 高度な脅威検出
* Just-in-Time 仮想マシン アクセス制御
* ハイブリッド セキュリティ

Microsoft Defender for Cloud で監視されるリソースのスコープを、ハイブリッド クラウド環境でサポートされているオンプレミス リソースへと拡大できます。 これには、サポートされているバージョンの System Center Operations Manager で現在監視されているオンプレミス リソースも含まれます。

Defender for Cloud の強化されたセキュリティ機能により、[ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm) で必要なクラウドベースのセキュリティ制御が提供されます。 これには、アプリケーションのフィルター処理や Just-In-Time アクセスによる管理特権の制限などが含まれます。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../azure-monitor/overview.md) は、すべての Azure リソースを対象とした一元的なログ ソリューションであり、Log Analytics と Application Insights を搭載しています。 Azure リソースからは、2 種類の重要なデータとして、ログとメトリックが収集されます。 Azure Monitor で収集されたログ情報は、幅広いツールでさまざまな目的のために利用されます。

![Azure Monitor の概要](media/overview.png)

Azure Monitor には "Azure アクティビティ ログ" も含まれています。 アクティビティ ログには、Azure 内で発生したサブスクリプション レベルのイベントがすべて記録されます。 これにより Azure のお客様は、Azure リソースに対して "誰がいつ何を" 行ったかを確認できます。 Azure Monitor に送信されるリソース ベースのログと Azure アクティビティ ログのイベントは、どちらも組み込みの Kusto クエリ言語を使用して分析できます。 その後これらのログをエクスポートし、カスタムのダッシュボードとビューの作成に使用し、アラートと通知をトリガーするように構成できます。

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) では、Azure サブスクリプション内のサポート対象の Azure リソース、システムで生成されたログ ファイル、および現在のリソース構成が分析されます。 Azure Advisor での分析は、Microsoft が推奨するベスト プラクティスに基づいてリアルタイムで生成されます。 環境に追加されたサポート対象の Azure リソースはすべて分析され、適切な推奨事項が提供されます。 Azure Advisor の推奨事項は、次の 4 つのベスト プラクティス カテゴリに分類されます。

* セキュリティ
* 高可用性
* パフォーマンス
* コスト

Azure Advisor で生成されるセキュリティの推奨事項は、Microsoft Defender for Cloud で行われる全体的なセキュリティ分析の一部となります。

Azure Advisor で収集された情報から、管理者は次のデータを得ることができます。

* 推奨されるベスト プラクティスを満たしていないリソース構成に関する分析情報
* 具体的な修復アクションに関するガイダンス
* 必要な修復アクションの優先順位を示すランキング

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) では、Azure リソースの種類とその許可された構成を管理するルールを適用できます。 ポリシーを使用して、リソースの作成と構成を制御したり、環境全体の構成設定を監査したりできます。 この監査結果は、修復アクティビティのベースとして利用できます。 Azure Policy は Azure ロールベースのアクセス制御 (Azure RBAC) とは異なります。Azure Policy はリソースとその構成を制限するために使用し、Azure RBAC は特権アクセスを Azure ユーザーに制限するために使用します。

特定のポリシーが適用されているか、ポリシーの影響が監査されているかにかかわらず、ポリシーのコンプライアンスが継続的に監視され、全体的なコンプライアンス情報とリソース別のコンプライアンス情報が管理者に提供されます。 Azure Policy のコンプライアンス データは、Microsoft Defender for Cloud に提供され、セキュリティ スコアに反映されます。

## <a name="key-design-considerations"></a>設計上の主な考慮事項

イベント ログ戦略の実施にあたっては、ACSC ISM で次の考慮事項が強調されています。

* ログ機能の一元化
* ログに記録する特定のイベント
* イベント ログの保護
* イベント ログのリテンション期間
* イベント ログの監査

ISM では、ログの収集と管理に加えて、組織の IT 環境の脆弱性評価を定期的に実施することが推奨されています。

### <a name="centralised-logging"></a>ログの一元化

どのログ ソリューションでも、キャプチャしたログをできるだけ 1 つのデータ リポジトリに統合するべきです。 これにより、運用の複雑さが軽減され、多数のデータ サイロの発生が防止されるだけでなく、複数のソースから収集されたデータをまとめて分析して、相互に関連しているイベントを特定できるようになります。 これは、サイバー セキュリティ インシデントの範囲を検出して管理するために不可欠です。

Azure Monitor を使用する Azure のお客様は、この要件を満たしています。 このオファリングでは、すべての Azure リソースを対象に Azure でログ リポジトリを一元化できるだけでなく、Azure イベント ハブにデータをストリーミングできます。 Azure Event Hubs は、フル マネージドのリアルタイム データ インジェスト サービスです。 Azure イベント ハブにストリーミングされた Azure Monitor データは、サポートされる既存のセキュリティ情報イベント管理 (SIEM) リポジトリやその他のサードパーティ製監視ツールにも簡単に接続できます。

Microsoft では、独自の Azure ネイティブ SIEM ソリューションである Microsoft Sentinel も提供しています。 Microsoft Sentinel は、さまざまなデータ コネクタをサポートしており、企業全体でセキュリティ イベントを監視するために使用できます。 セキュリティ管理者は、サポートされる[データ コネクタ](../sentinel/connect-data-sources.md)、Microsoft Sentinel の組み込み機械学習、Kusto クエリ言語からのデータを結合することで、アラートの検出、脅威の可視化、予防的ハンティング、脅威への対応のための単一ソリューションを用意できます。 Microsoft Sentinel には、ハンティングとノートブックの機能も備わっています。セキュリティ管理者は、これらの機能を使用して、セキュリティ調査の一部として実行されるすべての手順を再利用可能なプレイブックに記録し、組織内で共有できます。 組み込みの[ユーザー分析](../sentinel/overview.md)を使用すれば、指定した 1 人のユーザーのアクションを調査することもできます。

### <a name="logged-events-and-log-detail"></a>ログに記録されるイベントとログの詳細

ISM には、あらゆるログ戦略で対象に含める必要があるイベント ログの種類の詳細なリストが示されています。 キャプチャされるログには、分析と調査で実際に役立つ十分な詳細情報が含まれていなければなりません。

Azure で収集されるログは、次の 3 つのカテゴリに分類されます。

* **コントロールおよび管理ログ**: これらのログは、Azure Resource Manager の CREATE、UPDATE、DELETE 操作に関する情報を提供します。

* **データ プレーン ログ**: Azure リソースが使用されたときに発生するイベントが記録されます。 Windows イベント ログ (システム ログ、セキュリティ ログ、アプリケーション ログ) などのソースが含まれます。

* **処理済みイベント**: Azure がお客様に代わって自動的に処理したイベントとアラートに関する情報が含まれます。 処理済みイベントの例として、Microsoft Defender for Cloud のアラートがあります。

Windows と Linux のどちらの場合も、仮想マシン エージェントのデプロイによって Azure 仮想マシンの監視が強化されます。 これにより、収集されるログ情報の幅が大きく広がります。 このエージェントのデプロイが Microsoft Defender for Cloud から自動的に行われるように構成できます。

Microsoft では、Azure リソース固有のログとその[スキーマ](../security/fundamentals/log-audit.md)に関する詳細情報を提供しています。

### <a name="log-retention-and-protection"></a>ログのリテンション期間と保護

 イベント ログは、必要なリテンション期間にわたって安全に保存する必要があります。 ISM では、最低でも 7 年間ログを保持することが推奨されています。 Azure には、収集されたログを長期間保存するためのさまざまな手段があります。 Azure ログ イベントの既定の保存期間は 90 日です。 Azure Monitor でキャプチャされたログ データを長期間保持する必要がある場合は、Azure Storage アカウントにデータを移動して保存できます。 Azure Storage アカウントに保存されたアクティビティ ログは、設定した日数だけ保持することも、必要に応じて無期限に保持することもできます。

Azure ログ イベントを保存する Azure Storage アカウントは、geo 冗長にすることができ、Azure Backup を使用してバックアップできます。 Azure Backup でキャプチャされたログを含んだバックアップを削除するには、管理者の承認が必要です。削除対象としてマークされたバックアップでも、復旧に備えて 14 日間保持されます。 Azure Backup では、保護されたインスタンスのコピーを 9999 個作成できるので、日次バックアップを 27 年以上実行できることになります。

Azure ログで使用されるリソースへのアクセスを制御するには、Azure ロールベースのアクセス制御 (RBAC) を使用する必要があります。 ログ内のデータのセキュリティを確保するには、Azure Monitor、Azure Storage アカウント、および Azure Backup で RBAC を構成する必要があります。

### <a name="log-auditing"></a>ログの監査

ログの真の価値は、分析されて初めて実現されます。 自動と手動の両方の分析を利用すれば (さらに提供されているツールに習熟していれば)、組織のセキュリティ ポリシーの違反やサイバー セキュリティ インシデントの検出と管理に役立ちます。 Azure Monitor は、収集したログを分析するためのツールが充実しています。 分析結果は、システム間で共有できるほか、複数の形式で視覚化したり配布したりすることができます。

Azure Monitor で保存されたログ データは、Log Analytics ワークスペースに保持されます。 分析の出発点となるのはクエリです。 Azure Monitor のクエリは Kusto クエリ言語で記述されます。 クエリは、Azure のダッシュボードからアラート ルールに至る Azure Monitor のあらゆる出力のベースとなります。

![Azure のログ クエリの概要](media/queries-overview.png)

ログの監査は、監視ソリューションを使用することで強化できます。 このあらかじめパッケージ化されたソリューションには、収集ロジック、クエリ、データ視覚化ビューが含まれています。 Microsoft ではさまざまな監視ソリューションを[提供](../azure-monitor/monitor-reference.md)しています。その他にも、製品ベンダー各社が Azure Marketplace でソリューションを提供しています。

### <a name="vulnerability-assessment-and-management"></a>脆弱性の評価と管理

ISM には、定期的な脆弱性の評価と管理が不可欠であることが記されています。 IT 環境は絶えず進化し、外部のセキュリティ脅威は際限なく変化しています。 Microsoft Defender for Cloud では、脆弱性の自動評価を行い、修復アクティビティを計画して実施する方法についてガイダンスを得ることができます。

Microsoft Defender for Cloud のセキュリティ スコアには、推奨事項が一覧表示されます。これらの推奨事項を適用すると、環境のセキュリティが向上します。 この一覧は、全体的なセキュリティ スコアへの影響が高いものから順に並べられます。 一覧を影響度の並べることで、セキュリティを強化するうえで最も価値の高い推奨事項に集中して取り組むことができます。

Azure Policy も、継続的な脆弱性評価で重要な役割を果たします。 Azure Policy で提供されるポリシーの種類は、リソース タグと値を適用するものから、リソースを作成できる Azure リージョンを制限するもの、さらに特定のリソースの種類の作成を完全にブロックするものに及びます。 Azure のポリシーは、複数のイニシアティブにグループ化することができます。 関連する Azure ポリシーがイニシアティブを使用してグループとしてまとめて適用され、それが特定のセキュリティ目標またはコンプライアンス目標のベースとなります。

Azure Policy にはポリシー定義のライブラリが搭載されており、その規模は常に拡大しています。 また、Azure portal で Azure Policy の定義を独自に作成することもできます。 既存のライブラリで見つけたポリシーや新規に作成したポリシーは、Azure リソースに割り当てることができます。 リソース管理階層のさまざまなレベルで、この割り当ての[スコープを設定](../governance/policy/tutorials/create-and-manage.md)できます。 ポリシー割り当ては継承されます。つまり、スコープ内のすべての子リソースに同じポリシーが割り当てられます。 必要に応じて、スコープが設定されたポリシー割り当てからリソースを除外することもできます。

デプロイされたすべての Azure ポリシーが、組織のセキュリティ スコアに関係します。 高度なオーダーメイド環境では、カスタムの Azure Policy 定義を作成しデプロイして、特定のワークロードに合わせた監査情報を提供できます。

## <a name="getting-started"></a>はじめに

Microsoft Defender for Cloud の使用を開始して、Azure Monitor、Advisor、Policy を最大限活用するために、次の初期手順をお勧めします。

* Microsoft Defender for Cloud を有効にする
* Microsoft Defender for Cloud の強化されたセキュリティ機能を有効にする
* サポートされるマシンへの Log Analytics エージェントの自動プロビジョニングを有効にする
* Defender for Cloud ダッシュボードに表示されるセキュリティに関する推奨事項とアラートを確認し、優先順位を付け、対処する

## <a name="next-steps"></a>次のステップ

[Azure Policy と Azure Blueprints](azure-policy.md) に関するページを参照して、Azure Australia のリソースに対するガバナンスと制御の実装について詳細を確認する。
