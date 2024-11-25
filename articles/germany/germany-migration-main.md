---
title: Azure Germany からグローバル Azure に移行する
description: Azure リソースの Azure Germany からグローバル Azure への移行の紹介。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 89a65c133a2478b38755b05a8c2fe5d1bb6623a7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081298"
---
# <a name="overview-of-migration-guidance-for-azure-germany"></a>Azure Germany の移行ガイダンスの概要

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

このセクションの記事は、ワークロードを Azure Germany からグローバル Azure に移行する場合に役立つように作成されました。 [Azure Migration Center](https://azure.microsoft.com/migration/) は、リソースの移行に役立つツールを提供していますが、Azure Migration Center の一部のツールは、同じテナントまたは同じリージョン内で発生する移行にのみ役立ちます。

Germany の 2 つのリージョンは、グローバル Azure から完全に分離されています。 グローバル Azure のクラウドと Germany のクラウドには、独自で個別の Azure Active Directory (Azure AD) インスタンスがあります。 このため、Azure Germany のテナントはグローバル Azure テナントから分離されています。 この記事では、*異なる* テナント間で移行するときに使用できる移行ツールについて説明します。

ID/テナントに関するガイダンスは、Azure のみの顧客を対象にしています。 Azure と Microsoft 365 (または他の Microsoft 製品) に共通の Azure Active Directory (Azure AD) テナントを使用している場合、ID の移行は複雑なものとなります。まず、「[移行フェーズのアクションと Microsoft Cloud Deutschland からの移行に対する影響](/microsoft-365/enterprise/ms-cloud-germany-transition-phases)」を読む必要があります。 ご質問がある場合は、担当のアカウント マネージャーまたは Microsoft サポートにお問い合わせください。

Azure クラウド ソリューション プロバイダーは、新しいドイツ データセンター リージョンへの移行中と移行後に顧客をサポートするため、追加の手順を実行する必要があります。 [追加の手順](/microsoft-365/enterprise/ms-cloud-germany-transition-add-csp)の詳細を確認してください。

## <a name="migration-process"></a>移行プロセス

Azure Germany からグローバル Azure にワークロードを移行するために使用するプロセスは、アプリケーションをクラウドに移行するために使用するプロセスに似ています。 移行プロセスの手順は次のとおりです。

![移行プロセスの 4 つの手順である評価、計画、移行、検証を示す画像](./media/germany-migration-main/migration-steps.png)

### <a name="assess"></a>評価

Azure アカウント所有者、サブスクリプション管理者、テナント管理者、および財務/会計チームをまとめることによって、組織の Azure Germany のフットプリントを理解することが重要です。 これらの役割で作業するユーザーによって、大規模組織の Azure の使用状況の詳細を把握できます。

評価ステージでは、リソースのインベントリをコンパイルします。
  - 各サブスクリプション管理者とテナント管理者は、一連のスクリプトを実行して、リソース グループ、各リソース グループ内のリソース、およびリソース グループ デプロイ設定を一覧表示する必要があります。
  - Azure 内のアプリケーション間と外部システムとの依存関係をドキュメント化します。
  - 移行する各インスタンスに関連付けられている各 Azure リソースの数とデータの量をドキュメント化します。
  - アプリケーション アーキテクチャのドキュメントが、Azure リソース一覧と整合性があることを確認します。

このステージの最後には、以下のものが得られます。

- 使用されている Azure リソースの完全な一覧。
- リソース間の依存関係の一覧。
- 移行作業の複雑さに関する情報。

### <a name="plan"></a>プラン

計画ステージでは、次のタスクを行います。

- 評価ステージで完了した依存関係の分析の出力を使用して、関連コンポーネントを定義します。 *移行パッケージ* で、関連コンポーネントをまとめて移行することを検討します。
- (オプション) 移行を、[Gartner 5-R 条件](https://www.gartner.com/en/documents/3873016/evaluation-criteria-for-cloud-management-platforms-and-t)を適用し、ワークロードを最適化するための機会として使用します。
- グローバル Azure のターゲット環境を決定します。
  1. ターゲットのグローバル Azure テナントを特定します (必要に応じて作成します)。
  1. サブスクリプションを作成します。
  1. 移行するグローバル Azure の場所を選択します。
  1. Azure Germany のアーキテクチャをグローバル Azure のアーキテクチャに一致させるテスト移行シナリオを実行します。
- 移行の適切なタイムラインとスケジュールを決定します。 各移行パッケージのユーザー受け入れテスト計画を作成します。

### <a name="migrate"></a>移行

移行フェーズでは、Azure Germany の移行に関する記事に記載されているツール、テクニック、推奨事項を使用して、グローバル Azure に新しいリソースを作成します。 次に、アプリケーションを構成します。

### <a name="validate"></a>検証

検証ステージでは、次のタスクを行います。

1. ユーザー受け入れテストを実行します。
1. アプリケーションが期待どおりに動作していることを確認します。
1. 該当する場合、最新データをターゲット環境に同期させます。
1. グローバル Azure で新しいアプリケーション インスタンスに切り替えます。
1. 運用環境が期待どおりに動作することを確認します。
1. Azure Germany でリソースを使用停止します。

## <a name="terms"></a>用語

Azure Germany の移行に関する記事では、以下の用語が使用されています。

**ソース** はリソースの移行元の場所 (Azure Germany など) を示します。

- **ソース テナント名**: Azure Germany のテナントの名前 (アカウント名の **\@** の後のすべての部分)。 Azure Germany のテナント名はすべて **microsoftazure.de** で終わります。
- **ソース テナント ID**: Azure Germany のテナントの ID。 Azure portal で右上隅のアカウント名にマウスを移動すると、テナント ID が表示されます。
- **ソース サブスクリプション ID**: Azure Germany のリソース サブスクリプションの ID。 同じテナントで複数のサブスクリプションを使用することができます。 常に正しいサブスクリプションを使用していることを確認してください。
- **ソース リージョン**: 移行するリソースが存在する場所によって、ドイツ中部 (**germanycentral**) またはドイツ北東部 (**germanynortheast**) のいずれか。

**ターゲット** または **宛先** は、リソースの移行先の場所を示します。

- **ターゲット テナント名**: グローバル Azure のテナントの名前。
- **ターゲット テナント ID**: グローバル Azure のテナントの ID。
- **ターゲット サブスクリプション ID**: グローバル Azure のリソースのサブスクリプション ID。
- **ターゲット リージョン**: グローバル Azure ではほとんどすべてのリージョンを使用できます。 西ヨーロッパ (**westeurope**) または北ヨーロッパ (**northeurope**) にリソースを移行する可能性もあります。

> [!NOTE]
> ターゲット リージョンで、移行する Azure サービスが提供されていることを確認します。 Azure Germany で使用できるすべての Azure サービスは、西ヨーロッパで使用できます。 Azure Germany で使用できるすべての Azure サービスは、Machine Learning Studio (クラシック) と Azure Virtual Machines の G/GS VM シリーズを除き、北ヨーロッパで使用できます。

お使いのブラウザーでソースおよびターゲット ポータルをブックマークすることをお勧めします。

- Azure Germany portal は [https://portal.microsoftazure.de/](https://portal.microsoftazure.de/) にあります。
- グローバル Azure portal は [https://portal.azure.com/](https://portal.azure.com/) にあります。

## <a name="next-steps"></a>次のステップ

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
