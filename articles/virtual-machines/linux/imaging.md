---
title: Azure 用 Linux イメージの作成の概要
description: Linux VM イメージを取り込む方法、または Azure で使用する新しいイメージを作成する方法。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: d08c55a1449d159438b652bd45bbdc50e02fb126
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427933"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Azure での Linux イメージの取り込みと作成

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット 

この概要では、イメージングに関する基本的な概念と、Azure で Linux イメージを正常にビルドして使用する方法について説明します。 カスタム イメージを Azure に取り込む前に、使用できる種類とオプションに注意する必要があります。

この記事では、イメージの意思決定ポイントと要件について議論し、主要な概念について説明します。これに従うことで、独自のカスタム イメージを独自の仕様に合わせて作成できるようになります。

## <a name="difference-between-managed-disks-and-images"></a>マネージド ディスクとイメージの違い


Azure では、VHD をプラットフォームに取り込み、[マネージド ディスク](../faq-for-disks.yml)として使用したり、イメージのソースとして使用したりすることができます。 

Azure マネージド ディスクは単一の VHD です。 既存の VHD を取得してそこからマネージド ディスクを作成することも、空のマネージド ディスクをゼロから作成することもできます。 VM にディスクをアタッチすることでマネージド ディスクから VM を作成できますが、使用できるのは 1 つの VM を含む 1 つの VHD のみです。 OS のプロパティを変更することはできません。Azure は VM をオンにして、そのディスクを使用して起動しようとします。 

Azure イメージは、複数の OS ディスクとデータ ディスクで構成できます。 マネージド イメージを使用して VM を作成すると、プラットフォームによってイメージのコピーが作成され、それを使用して VM が作成されます。そのため、マネージド イメージでは、複数の VM で同じイメージを再利用することがサポートされます。 また、Azure には、グローバル レプリケーション、[Azure Compute Gallery](../shared-image-galleries.md) (旧称 Shared Image Gallery) を使用したバージョン管理など、イメージの高度な管理機能も用意されています。 



## <a name="generalized-and-specialized"></a>一般化されたイメージと専用イメージ

Azure では、一般化されたイメージと専用イメージの 2 種類のイメージが提供されます。 一般化されたイメージおよび専用イメージという用語は当初は Windows の用語でしたが、Azure に持ち込まれました。 これらの種類は、プラットフォームで VM がオンになったときの処理方法を定義します。 どちらの種類にも、利点と欠点、および前提条件があります。 作業を開始する前に、必要なイメージの種類を把握しておく必要があります。 次に、選択する必要があるシナリオと種類の概要を示します。

| シナリオ      | イメージの種類  | ストレージ オプション |
| ------------- |:-------------:| :-------------:| 
| 複数の VM で使用するように構成できるイメージを作成し、ホスト名を設定し、管理者ユーザーを追加して、最初の起動時に他のタスクを実行することができます。 | 一般化されたイメージ | Azure Compute Gallery またはスタンドアロンのマネージド イメージ |
| VM スナップショットまたはバックアップからイメージを作成します。 | 専用イメージ |Azure Compute Gallery またはマネージド ディスク |
| 複数の VM を作成するための構成を必要としないイメージを短時間で作成します。 |専用イメージ |Azure Compute Gallery |


### <a name="generalized-images"></a>一般化されたイメージ

一般化されたイメージとは、初回起動時にセットアップを完了する必要があるイメージです。 たとえば、初回起動時に、ホスト名、管理者ユーザー、その他の VM 固有の構成を設定します。 これは、イメージを複数回再利用する場合や、作成時にパラメーターを渡す必要がある場合に便利です。 一般化されたイメージに Azure エージェントが含まれている場合、エージェントはパラメーターを処理し、初期構成が完了したことをプラットフォームに送り返します。 このプロセスは[プロビジョニング](./provisioning.md)と呼ばれています。 

プロビジョニングを行うには、プロビジョナーがイメージに含まれている必要があります。 次の 2 つのプロビジョナーがあります。
- [Azure Linux エージェント](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

これはイメージを作成するための[前提条件](./create-upload-generic.md)です。


### <a name="specialized-images"></a>専用イメージ
これらは、完全に構成され、VM と特別なパラメーターを必要としないイメージであり、プラットフォームは VM をオンにするだけです。ホスト名の設定など、VM 内での一意性を処理し、同じ VNET での DNS の競合を回避する必要があります。 

これらのイメージにはプロビジョニング エージェントは必要ありませんが、拡張機能の処理機能が必要になる場合があります。 Linux エージェントをインストールすることはできますが、プロビジョニング オプションは無効にしてください。 プロビジョニング エージェントを必要としない場合でも、イメージは Azure イメージの[前提条件](./create-upload-generic.md)を満たす必要があります。


## <a name="image-storage-options"></a>イメージ ストレージ オプション
Linux イメージを作成するときは、次の 2 つのオプションがあります。

- 開発環境とテスト環境で簡単に VM を作成するためのマネージド イメージ。
- 大規模にイメージを作成および共有するための [Azure Compute Gallery](../shared-image-galleries.md)。


### <a name="managed-images"></a>マネージド イメージ

マネージド イメージは複数の VM を作成するために使用できますが、多数の制限があります。 マネージド イメージは、一般化されたソース (VM または VHD) からのみ作成できます。 同じリージョンに VM を作成する場合にのみ使用でき、サブスクリプションとテナント間で共有することはできません。

マネージド イメージは開発環境やテスト環境に使用できます。この場合、単一のリージョンとサブスクリプション内で使用するために、2 つの単純な一般化されたイメージが必要です。 

### <a name="azure-compute-gallery"></a>Azure Compute Gallery

[Azure Compute Gallery](../shared-image-galleries.md) (旧称 Shared Image Gallery) は、大規模にイメージを作成、管理、共有する場合に推奨されます。 ギャラリーを使用すると、イメージに関連する構造と編成を構築できます。  

- 一般化されたイメージと専用イメージの両方がサポートされます。
- 第 1 世代と第 2 世代の両方のイメージがサポートされます。
- イメージのグローバル レプリケーション。
- 容易な管理のためのバージョン管理とグループ化。
- 可用性ゾーンがサポートされるリージョンでのゾーン冗長ストレージ (ZRS) による高可用性イメージ。 ZRS では、ゾーンの障害に対する回復性の向上が提供されます。
- Azure RBAC を使用した、サブスクリプション間、および Active Directory (AD) テナント間の共有。
- 各リージョン内のイメージ レプリカを使用したデプロイのスケーリング。

大まかに言えば、次のもので構成されるギャラリーを作成します。
- イメージの定義 - イメージのグループを保持するコンテナーです。
- イメージのバージョン - 実際のイメージです。



## <a name="hyper-v-generation"></a>Hyper-V の世代

Azure は、Hyper-V 第 1 世代 (Gen1) と第 2 世代 (Gen2) をサポートしています。Gen2 は最新世代であり、Gen1 よりも多くの機能を提供します。 たとえば、メモリの増加、Intel ソフトウェア ガード エクステンションズ (Intel SGX)、および仮想化された永続メモリ (vPMEM) が含まれます。 オンプレミスで実行される第 2 世代 VM には、Azure ではまだサポートされていない機能がいくつかあります。 詳細については、「特徴と機能」セクションを参照してください。 詳細については、[こちらの記事](../generation-2.md)を参照してください。 追加機能が必要な場合は、Gen2 イメージを作成します。

それでも独自のイメージを作成する必要がある場合は、[イメージの前提条件](./create-upload-generic.md)を満たしていることを確認し、Azure にアップロードします。 ディストリビューション固有の要件については、以下を参照してください。


- [CentOS ベースのディストリビューション](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES と openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>次のステップ

[Azure Compute Gallery](tutorial-custom-images.md) を作成する方法について説明します。

