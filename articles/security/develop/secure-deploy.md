---
title: セキュリティで保護されたアプリケーションを Microsoft Azure にデプロイする
description: この記事では、Web アプリケーション プロジェクトのリリースと対応のフェーズ中に考慮すべきベスト プラクティスについて説明します。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ab2188e0a59216ab01b54f430506003529129bb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342986"
---
# <a name="deploy-secure-applications-on-azure"></a>セキュリティで保護されたアプリケーションを Azure 上にデプロイする
この記事では、クラウド向けのアプリケーションをデプロイするときに考慮すべきセキュリティ アクティビティと制御について説明します。 Microsoft [セキュリティ開発ライフサイクル (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) のリリースと対応のフェーズ中に考慮するセキュリティの質問と概念について説明します。 目標は、より安全なアプリケーションのデプロイに使用できるアクティビティと Azure サービスの定義を手助けすることです。

この記事では、次の SDL フェーズについて説明します。

- Release
- Response

## <a name="release"></a>Release
リリース フェーズの焦点は、公開リリース用のプロジェクトを準備することです。 これには、リリース後のサービス タスクを効果的に実行し、後で発生する可能性のあるセキュリティの脆弱性に対処する方法を計画することが含まれます。

### <a name="check-your-applications-performance-before-you-launch"></a>起動前にアプリケーションのパフォーマンスを確認する

アプリケーションを起動したり更新プログラムを運用環境にデプロイしたりする前に、アプリケーションのパフォーマンスを確認します。 Visual Studio を使用してクラウドベースの[ロード テスト](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)を実行してアプリケーションのパフォーマンスの問題を検出して、デプロイの品質を改善し、アプリケーションを常に稼働または使用可能状態に維持して、起動のためにアプリケーションがトラフィックを処理できることを確認します。

### <a name="install-a-web-application-firewall"></a>Web アプリケーション ファイアウォールをインストールする

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の的になるケースが増えています。 よくある攻撃の例として、SQL インジェクション攻撃やクロス サイト スクリプティング攻撃が挙げられます。 アプリケーション コードでのこれらの攻撃の阻止は困難な可能性があります。 アプリケーション トポロジの多くの層で厳格な保守、パッチ適用、監視が必要になる場合があります。 WAF を一元化することで、セキュリティの管理が簡単になります。 また、WAF のソリューションは、個々の Web アプリケーションをセキュリティで保護する場合と比較して、1 か所に既知の脆弱性の修正プログラムを適用することでセキュリティの脅威に対応できます。

[Azure Application Gateway WAF](../../web-application-firewall/ag/ag-overview.md) は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護します。 WAF は、[OWASP コア ルール セット](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 または 2.2.9 の規則に基づいています。

### <a name="create-an-incident-response-plan"></a>インシデント対応計画を作成する

時間の経過と共に出現する可能性のある新しい脅威に対処するためには、インシデント対応計画を準備することがきわめて重要です。 インシデント対応計画の準備には、セキュリティに関する適切な緊急連絡先を確認することと、組織の他のグループから継承したコードとライセンスを付与されたサード パーティのコードに対するセキュリティ サービス プランの確立が含まれます。

### <a name="conduct-a-final-security-review"></a>最終セキュリティ レビューを実施する

実行されたすべてのセキュリティ アクティビティを丹念にレビューすることで、ソフトウェア リリースやアプリケーションの準備できていることを確認できます。 最終セキュリティ レビュー (FSR) には、通常、脅威モデル、ツールの出力、要件フェーズで定義した品質ゲートとバグ バーに対するパフォーマンスを調べることが含まれます。

### <a name="certify-release-and-archive"></a>リリースとアーカイブを認定する

リリース前にソフトウェアを認定することで、セキュリティとプライバシーの要件が満たされていることを保証することができます。 すべての関連データをアーカイブすることは、リリース後のサービス タスクの実行には不可欠です。 また、アーカイブにより、持続的なソフトウェア エンジニアリングに関連する長期的なコストを削減することもできます。

## <a name="response"></a>Response

リリース後の対応フェーズは、ソフトウェアに対する新種の脅威や脆弱性の報告に対して開発チームが適切に対応できるようにすることに重点を置いています。

### <a name="execute-the-incident-response-plan"></a>インシデント対応計画を実行する

リリース フェーズで実施したインシデント対応計画を実施可能にすることは、出現するソフトウェアのセキュリティやプライバシーの脆弱性から顧客を保護するためには不可欠です。

### <a name="monitor-application-performance"></a>アプリケーション パフォーマンスを監視する

アプリケーションのデプロイ後の継続的な監視により、パフォーマンスの問題とセキュリティの脆弱性を検出できる可能性があります。

アプリケーションの監視を支援する Azure サービスは次のとおりです。

  - Azure Application Insights
  - Microsoft Defender for Cloud

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中の Web アプリケーションを監視することができます。 Application Insights は、パフォーマンスの異常を自動的に検出します。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

#### <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

[Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) では、Web アプリケーションなどの Azure リソースのセキュリティ状況が非常にわかりやすく表示され、かつ、抑制機能にも優れており、脅威を回避し、検出し、それらに対応できます。 Microsoft Defender for Cloud では、このツールでなければ気付かないような脅威を検出できます。 これは、さまざまなセキュリティ ソリューションと連携します。

Defender for Cloud の Free レベルでは、Azure リソースに対してのみ限定的なセキュリティを提供します。 [Defender for Cloud の Standard レベル](../../security-center/security-center-get-started.md)では、機能が使用できる対象が増え、オンプレミスのリソースや他のクラウドでも使用できるようになります。
Defender for Cloud Standard では、次のことが可能になります。

  - セキュリティの脆弱性を検出して修正する。
  - 悪意のあるアクティビティをブロックするため、アクセスとアプリケーションの制御を適用する。
  - 分析とインテリジェンスを使用して脅威を検出する。
  - 攻撃を受けたときに迅速に対応する。

## <a name="next-steps"></a>次のステップ
次の記事では、セキュリティで保護されたアプリケーションを設計して開発するのに役立つセキュリティ制御とアクティビティが推奨されています。

- [セキュリティで保護されたアプリケーションを設計する](secure-design.md)
- [セキュリティで保護されたアプリケーションを開発する](secure-develop.md)
