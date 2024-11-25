---
title: Storage FSLogix プロファイル コンテナー Azure Virtual Desktop - Azure
description: Azure Storage で Azure Virtual Desktop FSLogix プロファイルを保存するためのオプション。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 78dae3bbf31d4f22047288861afeebb11ee4648f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232098"
---
# <a name="storage-options-for-fslogix-profile-containers-in-azure-virtual-desktop"></a>Azure Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション

Azure には、FSLogix プロファイル コンテナーの格納に利用できるさまざまなストレージ ソリューションが用意されています。 この記事では、Azure Virtual Desktop FSLogix ユーザー プロファイル コンテナーのために Azure で用意されているストレージ ソリューションを比較します。 ほとんどのお客様には、Azure Files に FSLogix プロファイル コンテナーを保存することをお勧めします。

Azure Virtual Desktop では、推奨されるユーザー プロファイル ソリューションとして FSLogix プロファイル コンテナーが提供されます。 FSLogix は、Azure Virtual Desktop などのリモート コンピューティング環境でプロファイルをローミングするように設計されています。 サインイン時、このコンテナーは、ネイティブにサポートされた仮想ハード ディスク (VHD) と Hyper-V 仮想ハード ディスク (VHDX) を使用して、コンピューティング環境に動的に接続されます。 ユーザー プロファイルはすぐに利用できるようになり、ネイティブのユーザー プロファイルとまったく同じようにシステムに表示されます。

次の表では、Azure Virtual Desktop FSLogix プロファイル コンテナー ユーザー プロファイルのために Azure Storage で用意されているストレージ ソリューションを比較します。

## <a name="azure-platform-details"></a>Azure プラットフォームの詳細

|特徴|Azure Files|Azure NetApp Files|記憶域スペース ダイレクト|
|--------|-----------|------------------|---------------------|
|使用事例|汎用|オンプレミスの NetApp からの Ultra パフォーマンスまたは移行|クロス プラットフォーム|
|プラットフォーム サービス|はい。Azure ネイティブ ソリューションです。|はい。Azure ネイティブ ソリューションです。|いいえ。自己管理型です。|
|リージョン別の提供状況|すべてのリージョン|[リージョンの選択](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|すべてのリージョン|
|冗長性|ローカル冗長、ゾーン冗長、地域冗長、地域ゾーン冗長|ローカル冗長|ローカル冗長/ゾーン冗長/geo 冗長|
|サービス レベルとパフォーマンス| Standard (トランザクション最適化)<br>Premium<br>共有あたり最大 100K IOPS と 10 GBps、約 3 ミリ秒の待ち時間|Standard<br>Premium<br>Ultra<br>ボリュームあたり最大 4.5 GBps、約 1 ミリ秒の待ち時間。 IOPS とパフォーマンスの詳細については、「[Azure NetApp Files のパフォーマンスに関する考慮事項](../azure-netapp-files/azure-netapp-files-performance-considerations.md)」および [FAQ](../azure-netapp-files/faq-performance.md#how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops) を参照してください。|Standard HDD: ディスクあたり最大 500 IOPS の上限<br>Standard SSD: ディスクあたり最大 4k IOPS の上限<br>Premium SSD: ディスクあたり最大 20k IOPS の上限<br>記憶域スペース ダイレクトには Premium ディスクをお勧めします|
|容量|共有あたり 100 TiB、汎用目的アカウントあたり最大 5 PiB |ボリュームあたり 100 TiB、サブスクリプションあたり最大 12.5 PiB|ディスクあたりの最大 32 TiB|
|必要なインフラストラクチャ|最小共有サイズ 1 GiB|最小容量プール 4 TiB、最小ボリュームサイズ 100 GiB|Azure IaaS (+ Cloud Witness) で 2 つの VM、またはディスクのコストなしで 3 つの VM|
|プロトコル|SMB 3.0 または 2.1、NFSv 4.1 (プレビュー)、REST|NFSv3、NFSv4.1 (プレビュー)、SMB 3.x/2.x|NFSv3、NFSv4.1、SMB 3.1|

## <a name="azure-management-details"></a>Azure 管理の詳細

|特徴|Azure Files|Azure NetApp Files|記憶域スペース ダイレクト|
|--------|-----------|------------------|---------------------|
|アクセス|クラウド、オンプレミス、ハイブリッド (Azure ファイル同期)|クラウド、オンプレミス (ExpressRoute 経由)|クラウド、オンプレミス|
|バックアップ|Azure Backup スナップショット統合|Azure NetApp Files スナップショット|Azure Backup スナップショット統合|
|セキュリティとコンプライアンス|[Azure でサポートされているあらゆる証明書](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 完了|[Azure でサポートされているあらゆる証明書](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory の統合|[Native Active Directory と Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services と Native Active Directory](../azure-netapp-files/faq-smb.md#does-azure-netapp-files-support-azure-active-directory)|Native Active Directory または Azure Active Directory Domain Services サポートのみ|

ストレージ方法を選択したら、[「Azure Virtual Desktop の価格」](https://azure.microsoft.com/pricing/details/virtual-desktop/)で Microsoft の価格設定に関する情報をご覧ください。

## <a name="azure-files-tiers"></a>Azure Files のレベル

Azure Files では、Premium と Standard の 2 種類のストレージ レベルが提供されています。 これらのレベルを使用すると、シナリオの要件を満たすためにファイル共有のパフォーマンスとコストを調整できます。

- Premium ファイル共有は、ソリッドステート ドライブ (SSD) が基になっており、FileStorage ストレージ アカウント タイプでデプロイされます。 Premium ファイル共有では、入出力 (IO) 集中型のワークロードに対して、高いパフォーマンスと低遅延が一貫して提供されます。 

- Standard ファイル共有は、ハード ディスク ドライブ (HDD) が基になっており、汎用バージョン 2 (GPv2) ストレージ アカウント タイプでデプロイされます。 Standard ファイル共有では、汎用のファイル共有や開発/テスト環境など、パフォーマンスの変動の影響を受けにくい IO ワークロードに対して、信頼性の高いパフォーマンスが提供されます。 Standard ファイル共有は、従量課金制の課金モデルでのみ利用できます。

次の表に、ワークロードに基づいて、どのパフォーマンス レベルを使用すればよいかについて推奨事項を示します。 これらの推奨事項は、パフォーマンス目標、予算、および地域に関する考慮事項を満たすパフォーマンス レベルを選択するのに役立ちます。 これらの推奨事項は、[リモート デスクトップ ワークロード タイプ](/windows-server/remote/remote-desktop-services/remote-desktop-workloads)のシナリオ例に基づいています。 

| ワークロードの種類 | 推奨されるファイル レベル |
|--------|-----------|
| Light (200 人未満のユーザー) | Standard ファイル共有 |
| Light (200 人を超えるユーザー) | Premium ファイル共有、または複数のファイル共有を備えた Standard |
|Medium|Premium ファイル共有|
|ヘビー|Premium ファイル共有|
|Power|Premium ファイル共有|

Azure Files のパフォーマンスの詳細については、「[ファイル共有とファイルのスケール ターゲット](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets)」を参照してください。 価格の詳細については、「[Azure Files の料金](https://azure.microsoft.com/pricing/details/storage/files/)」を参照してください。

## <a name="next-steps"></a>次のステップ

FSLogix プロファイル コンテナー、ユーザー プロファイルディスク、その他のユーザー プロファイル テクノロジの詳細については、「[FSLogix プロファイル コンテナーと Azure のファイル](fslogix-containers-azure-files.md)」の表を参照してください。

独自の FSLogix プロファイル コンテナーを作成する準備ができたら、次のいずれかのチュートリアルを開始してください。

- [Azure Virtual Desktop で Azure Files の FSLogix プロファイル コンテナーを開始する](create-file-share.md)
- [Azure NetApp Files を使用してホスト プール用の FSLogix プロファイル コンテナーを作成する](create-fslogix-profile-container.md)
- ユーザー プロファイル ディスクの代わりに FSLogix プロファイル コンテナーを使用するとき、「[Azure での UPD 記憶域用に 2 ノードの記憶域スペース ダイレクト スケールアウト ファイル サーバーを展開する](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)」の指示も適用されます。

「[Azure Virtual Desktop でテナントを作成する](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)」で、独自の Azure Virtual Desktop ソリューションを一番最初から始めて設定することもできます。
