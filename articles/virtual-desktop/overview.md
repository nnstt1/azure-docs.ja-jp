---
title: Azure Virtual Desktop とは - Azure
description: Azure Virtual Desktop の概要。
author: Heidilohr
ms.topic: overview
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c0e61760b130631c4f688b06f2cebdb163ea3910
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452150"
---
# <a name="what-is-azure-virtual-desktop"></a>Azure Virtual Desktop とは

Azure Virtual Desktop は、クラウド上で実行されるデスクトップおよびアプリの仮想化サービスです。

Azure 上で Azure Virtual Desktop を実行すると、次のことができます。

* マルチセッションの Windows 10 デプロイを設定し、スケーラビリティを備えた完全版の Windows 10 を提供
* Microsoft 365 Apps for enterprise を仮想化および最適化し、マルチユーザーの仮想シナリオで運用
* Windows 7 仮想デスクトップに無料の延長セキュリティ更新プログラムを提供
* 既存のリモート デスクトップ サービス (RDS) と Windows Server のデスクトップやアプリをあらゆるコンピューターで利用
* デスクトップとアプリの両方を仮想化
* 統一的な管理エクスペリエンスを使って Windows 10、Windows Server、Windows 7 のデスクトップとアプリを管理

## <a name="introductory-video"></a>紹介ビデオ

Azure Virtual Desktop について、その独自性や新機能を、このビデオで学習してください。

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Azure Virtual Desktop に関するその他のビデオについては、[プレイリスト](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)を参照してください。

## <a name="key-capabilities"></a>主な機能

Azure Virtual Desktop を使用すると、スケーラブルで柔軟な環境を設定することができます。

* ゲートウェイ サーバーを一切稼働させることなく、Azure サブスクリプション内に完全な仮想デスクトップ環境を作成できます。
* 多種多様なワークロードに対応するうえで必要なホスト プールをいくつでも公開できます。
* Azure ギャラリーから独自のイメージを取得して運用ワークロードに使用したり、テストしたりできます。
* プールしたマルチセッション リソースを使ってコストを節約できます。 Azure Virtual Desktop 限定の新しい Windows 10 Enterprise マルチセッション機能と、Windows Server 上のリモート デスクトップ セッション ホスト (RDSH) ロールを併用すれば、ユーザーにこれまでと同じリソースを提供しつつ、仮想マシンの数やオペレーティング システム (OS) のオーバーヘッドを大幅に削減できます。
* (永続的な) 個人用デスクトップを提供できるので、個々人が自分のマシンを保有しているような感覚を持たせることができます。

仮想デスクトップをデプロイおよび管理できます。

* Azure portal と、Azure Virtual Desktop の PowerShell および REST インターフェイスを使用して、ホスト プールの構成、アプリ グループの作成、ユーザーの割り当て、リソースの公開を行うことができます。
* 単一のホスト プールから完全版のデスクトップと個々のリモート アプリのどちらも公開できます。また、ユーザー グループに応じてアプリ グループを個別に作成できるほか、ユーザーを複数のアプリ グループに割り当ててイメージの数を削減することも可能です。
* 環境の管理では、ロールの割り当てにあらかじめ用意されている委任アクセス権を使用したり、診断情報を収集してさまざまな構成やユーザー エラーに対する理解を深めたりすることができます。
* 新しい診断サービスを使ってエラーのトラブルシューティングが可能です。
* 管理するのはイメージと仮想マシンのみです。インフラストラクチャの管理は必要ありません。 お使いの Azure サブスクリプション内の仮想マシンだけを管理すれば良いので、リモート デスクトップ サービスの場合のように、リモート デスクトップ関連のロールを個人で管理する必要がありません。

このほか、仮想デスクトップにユーザーを割り当てて接続することもできます。

* 割り当てられたユーザーは、Azure Virtual Desktop クライアントを起動して、公開されている Windows デスクトップやアプリケーションに接続できるようになります。 デバイスにインストールされているネイティブ アプリケーションと Azure Virtual Desktop HTML5 Web クライアントのどちらかを使って、あらゆるデバイスから接続できます。
* サービスには逆接続を使って安全にユーザーを接続するので、受信ポートを開く必要が一切ありません。

## <a name="requirements"></a>必要条件

Azure Virtual Desktop を設定し、ユーザーを Windows のデスクトップやアプリケーションに正常に接続するうえで必要なことがいくつかあります。

次のオペレーティング システムがサポートされています。デプロイする予定のデスクトップとアプリに基づいて、ユーザーのための[適切なライセンス](https://azure.microsoft.com/pricing/details/virtual-desktop/)があることを確認してください。

|OS|必要とされるライセンス|
|---|---|
|Windows 10 Enterprise マルチセッションまたは Windows 10 Enterprise|Microsoft 365 E3、E5、A3、A5、F3、Business Premium<br>Windows E3、E5、A3、A5|
|Windows 7 Enterprise |Microsoft 365 E3、E5、A3、A5、F3、Business Premium<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019、2022|ソフトウェア アシュアランス付きの RDS クライアント アクセス ライセンス (CAL)|

インフラストラクチャが Azure Virtual Desktop をサポートするために必要なものは次のとおりです。

* [Azure Active Directory](../active-directory/index.yml)。
* Azure Active Directory と同期している Windows Server Active Directory。 これは、Azure AD Connect (ハイブリッド組織の場合) または Azure AD Domain Services (ハイブリッドまたはクラウド組織の場合) を使用して構成できます。
  * Azure Active Directory と同期している Windows Server AD。 ユーザーのソースは Windows Server AD であり、Azure Virtual Desktop VM は Windows Server AD ドメインに参加しています。
  * Azure Active Directory と同期している Windows Server AD。 ユーザーのソースは Windows Server AD であり、Azure Virtual Desktop VM は Azure AD Domain Services ドメインに参加しています。
  * Azure AD Domain Services ドメイン。 ユーザーのソースは Azure Active Directory であり、Azure Virtual Desktop VM は Azure AD Domain Services ドメインに参加しています。
* Windows Server Active Directory または Azure AD DS インスタンスを含むかインスタンスに接続されている仮想ネットワークを含む、同じ Azure AD テナントを親とする Azure サブスクリプション。

Azure Virtual Desktop に接続するためのユーザー要件は次のとおりです。

* ユーザーのソースは、Azure AD に接続されている同じ Active Directory である必要があります。 Azure Virtual Desktop では、B2B または MSA アカウントはサポートされていません。
* Azure Virtual Desktop のサブスクライブに使用する UPN は、VM が参加している Active Directory ドメインに存在する必要があります。

Azure Virtual Desktop 用に作成する Azure 仮想マシンに必要な条件は次のとおりです。

* [標準ドメイン参加済み](../active-directory-domain-services/compare-identity-solutions.md)または [Hybrid AD 参加済み](../active-directory/devices/hybrid-azuread-join-plan.md)であること。 [Azure AD 参加済み](deploy-azure-ad-joined-vm.md)の仮想マシンがプレビューでご利用いただけます。
* 次のいずれかの[サポート対象となる OS イメージ](#supported-virtual-machine-os-images)を実行していること。

>[!NOTE]
>Azure サブスクリプションが必要な場合には、[1 か月間の無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。 無料試用版の Azure をご利用中の場合には、Windows Server Active Directory が Azure Active Directory と同期した状態を保つことができるよう、Azure AD Domain Services を使用する必要があります。

Azure Virtual Desktop のデプロイを意図したとおりに機能させるためにブロックを解除する必要がある URL の一覧については、「[必要な URL リスト](safe-url-list.md)」を参照してください。

Azure Virtual Desktop には、お客様がユーザーに配信する Windows のデスクトップとアプリのほか、Microsoft が Azure 上でサービスとしてホストしている管理ソリューションが含まれています。 デスクトップとアプリは任意の Azure リージョン内の仮想マシン (VM) にデプロイでき、これらの VM の管理ソリューションとデータは米国に配置されます。 このため、米国を宛先とするデータ転送が発生することがあります。

最適なパフォーマンスを実現するために、お使いのネットワークが次の要件を満たしていることを確認してください。

* クライアントのネットワークからホスト プールをデプロイした Azure リージョンまでのラウンドトリップ (RTT) 待ち時間は、150 ミリ秒を下回っている必要があります。 [エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment)を使用して、接続の正常性と推奨される Azure リージョンを表示します。
* デスクトップとアプリをホストしている VM が管理サービスに接続する際には、ネットワーク トラフィックが国/リージョン外に流れることがあります。
* ネットワークのパフォーマンスを最適化するために、セッション ホストの VM をユーザーに最も近い Azure リージョンに配置することをお勧めします。

エンタープライズ向け Azure Virtual Desktop の標準的なアーキテクチャのセットアップについては、[アーキテクチャのドキュメント](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)で確認できます。

## <a name="supported-remote-desktop-clients"></a>サポートされているリモート デスクトップ クライアント

次のリモート デスクトップ クライアントは、Azure Virtual Desktop をサポートします。

* [Windows デスクトップ](./user-documentation/connect-windows-7-10.md)
* [Web](./user-documentation/connect-web.md)
* [macOS](./user-documentation/connect-macos.md)
* [iOS](./user-documentation/connect-ios.md)
* [Android](./user-documentation/connect-android.md)
* Microsoft Store クライアント

> [!IMPORTANT]
> Azure Virtual Desktop では、RemoteApp とデスクトップ接続 (RADC) クライアントおよびリモート デスクトップ接続 (MSTSC) クライアントはサポートされていません。

クライアントを使用するためにブロックを解除する必要がある URL の詳細については、「[安全な URL リスト](safe-url-list.md)」をご覧ください。

## <a name="supported-virtual-machine-os-images"></a>サポートされている仮想マシン OS イメージ

Azure Virtual Desktop は、[Microsoft ライフサイクル ポリシー](/lifecycle/)に従っており、次の x64 オペレーティング システム イメージをサポートしています。

* Windows 11 Enterprise マルチセッション (プレビュー)
* Windows 11 Enterprise (プレビュー)
* Windows 10 (Enterprise マルチセッション)
* Windows 10 Enterprise
* Windows 7 Enterprise
* Windows Server 2022
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Azure Virtual Desktop では、x86 (32 ビット)、Windows 10 Enterprise N、Windows 10 LTSB、Windows 10 LTSC、Windows 10 Pro、および Windows 10 Enterprise KN のオペレーティング システム イメージはサポートされていません。 また、Windows 7 では、セクター サイズの制限により、マネージド Azure Storage でホストされている VHD または VHDX ベースのプロファイル ソリューションもサポートされていません。

利用できる自動化とデプロイ オプションは、選択した OS とバージョンによって異なります。次の表を参照してください。

|オペレーティング システム|Azure イメージ ギャラリー|手動での VM のデプロイ|Azure Resource Manager テンプレート統合|Azure Marketplace でのホスト プールのプロビジョニング|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 11 Enterprise マルチセッション (プレビュー)|はい|はい|はい|はい|
|Windows 11 Enterprise (プレビュー)|はい|はい|はい|はい|
|Windows 10 Enterprise マルチセッション、バージョン 1909 以降|はい|はい|はい|はい|
|Windows 10 Enterprise、バージョン 1909 以降|はい|はい|はい|はい|
|Windows 7 Enterprise|はい|はい|いいえ|いいえ|
|Windows Server 2022|はい|はい|いいえ|いいえ|
|Windows Server 2019|はい|はい|いいえ|いいえ|
|Windows Server 2016|はい|はい|はい|はい|
|Windows Server 2012 R2|はい|はい|いいえ|いいえ|

## <a name="next-steps"></a>次のステップ

Azure Virtual Desktop (クラシック) を使用している場合は、「[Azure Virtual Desktop でテナントを作成する](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)」のチュートリアルを開始できます。

Azure Resource Manager 統合で Azure Virtual Desktop を使用している場合は、代わりにホスト プールを作成する必要があります。 作業を開始するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)
