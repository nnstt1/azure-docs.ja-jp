---
title: Azure portal を使用して Windows 仮想マシンをプロビジョニングする
description: このガイドでは、Azure portal を使用して Windows 仮想マシンに SQL Server をプロビジョニングするために使用できるオプションについて説明します。
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: pamela
ms.reviewer: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 2bc4efc0be1e216a10328f5476f592a3dc1156f0
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167099"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Azure portal を使用して SQL Server がインストールされた Windows 仮想マシンをプロビジョニングする方法

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

このガイドでは、Azure portal を使用して Windows 仮想マシン (VM) に SQL Server をプロビジョニングするために使用できるオプションについて説明します。 この記事では、1 つの構成に重点が置かれている [SQL Server VM のクイックスタート](sql-vm-create-portal-quickstart.md)よりも多くの構成オプションについて説明します。 

このガイドは、SQL Server VM を作成する目的にも使用できますし、 Azure Portal で使用できるオプションを参照する目的にも使用できます。

> [!TIP]
> SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.yml)に関するページをご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> SQL Server 仮想マシン ギャラリー イメージ

SQL Server 仮想マシンを作成する際には、仮想マシン ギャラリーにあるいくつかの事前構成済みイメージの中から、使用するイメージを選択できます。 次の手順は、SQL Server 2017 イメージの中から、イメージを 1 つを選択する方法を示したものです。

1. Azure portal の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「*Azure SQL*」と入力します。 

   **[Azure SQL]** の横にある星を選択してお気に入りとして保存し、左側のナビゲーションの項目として追加することもできます。 

1. **[+ 追加]** を選択して、 **[Select SQL deployment option]\(SQL デプロイ オプションの選択\)** ページを開きます。 **[詳細の表示]** を選択すると、追加情報を表示できます。 
1. **[SQL 仮想マシン]** タイルの SQL Server イメージ検索ボックスに「*2017*」と入力し、ドロップダウンから **[Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016]** を選択します。 

   ![SQL VM イメージの選択](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > この記事では Developer エディションを使用しています。これは、開発テスト用に SQL Server のフル機能を使用できる無償エディションです。 ユーザーは VM を実行するコストに対してのみ課金されます。 ただし、このチュートリアルでは、任意のイメージを選択してかまいません。 使用可能なイメージの説明は、[SQL Server Windows 仮想マシンの概要](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)に関する記事をご覧ください。

   > [!TIP]
   > SQL Server のライセンス コストは、作成した VM の 1 秒あたりの料金に組み込まれており、エディションやコア数によって異なります。 ただし、SQL Server Developer エディションは、運用ではなく開発とテストのために無料で提供されています。 また、SQL Express は軽量のワークロード (1 GB 未満のメモリ、10 GB 未満のストレージ) の場合に無料です。 また、ライセンス持ち込み (BYOL) により VM の料金のみを支払うこともできます。 それらのイメージの名前には、{BYOL} というプレフィックスが付きます。 
   >
   > これらのオプションの詳細については、「[Pricing guidance for SQL Server Azure VMs (SQL Server Azure VM の料金ガイダンス)](pricing-guidance.md)」を参照してください。


1. **［作成］** を選択します


## <a name="1-configure-basic-settings"></a>1.基本設定を構成する

**[基本]** タブで次の情報を指定します。

* **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認します。 
* **[リソース グループ]** セクションで、一覧から既存のリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループを作成します。 リソース グループとは、Azure の関連リソース (仮想マシン、ストレージ アカウント、仮想ネットワークなど) のコレクションです。 

  ![サブスクリプション](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Azure における SQL Server のデプロイについてテストまたは調査のみを実施する場合は、新しいリソース グループを使用すると便利です。 テストが完了したら、リソース グループを削除します。そうすると、そのリソース グループに関連付けられている VM とすべてのリソースが自動的に削除されます。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../../active-directory-b2c/overview.md)」を参照してください。


* **[インスタンスの詳細]** で、以下の操作を行います。

    1. 一意の **[仮想マシン名]** を入力します。  
    1. **[リージョン]** で場所を選択します。 
    1. このガイドでは、 **[可用性オプション]** の設定を _[インフラストラクチャ冗長は必要ありません]_ のままにしておきます。 可用性オプションの詳細については、[可用性](../../../virtual-machines/availability.md)に関するページを参照してください。 
    1. **[イメージ]** の一覧で、_Free SQL Server License:SQL Server 2017 Developer on Windows Server 2016_ という名前のイメージを選択します。  
    1. 仮想マシンの **[サイズ]** で **[サイズの変更]** を選択し、 **[A2 Basic]** プランを選択します。 予期しない課金を防ぐために、利用を終了したリソースは必ずクリーンアップしてください。 運用時のワークロードについては、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](./performance-guidelines-best-practices-checklist.md)」のマシンのサイズと構成に関する推奨事項を参照してください。

    ![インスタンスの詳細](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> **[サイズの選択]** ウィンドウに表示される月額料金の見積もりには、SQL Server のライセンス費用は含まれていません。 この見積もり料金は VM 単体の費用です。 SQL Server Express エディションと SQL Server Developer エディションでは、この見積もり料金が概算費用の合計になります。 他のエディションについては、「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」で、ターゲットの SQL Server エディションを選択して確認できます。 また、「[SQL Server Azure VM の料金ガイダンス](pricing-guidance.md)」と[仮想マシンのサイズ](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

* **[管理者アカウント]** で、ユーザー名とパスワードを指定します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../../../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。

   ![[Administrator account] (管理者アカウント)](./media/create-sql-vm-portal/basics-administrator-account.png)

* **[受信ポートの規則]** で **[選択したポートを許可する]** を選択し、ドロップダウンから **[RDP (3389)]** を選択します。 

   ![受信ポートの規則](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2.オプション機能を構成する

### <a name="disks"></a>ディスク

**[ディスク]** タブでディスク オプションを構成します。 

* **[OS ディスクの種類]** で、ドロップダウンから OS に使用するディスクの種類を選択します。 Premium は、運用環境システムにはお勧めしますが、Basic VM には使用できません。 Premium SSD を使用するには、仮想マシンのサイズを変更してください。 
* **[詳細]** で、 **[Managed Disks を使用]** の下の **[はい]** を選択します。

   > [!NOTE]
   > SQL Server には、Managed Disks の使用をお勧めします。 Managed Disks はバックグラウンドでストレージを管理します。 さらに、仮想マシンと Managed Disks が同じ可用性セットにある場合、Azure は適切な冗長性を提供するためにストレージ リソースを分散させます。 詳細については、[Azure Managed Disks の概要](../../../virtual-machines/managed-disks-overview.md)に関する記事をご覧ください。 可用性セットのマネージド ディスクの詳細については、「[可用性セット内の VM にマネージド ディスクを使用する](../../../virtual-machines/availability.md)」を参照してください。

![SQL VM ディスク設定](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>ネットワーク

**[ネットワーク]** タブで、ネットワーク オプションを構成します。 

* SQL Server VM 用に新しい **仮想ネットワーク** を作成するか、既存の仮想ネットワークを使用します。 **[サブネット]** も指定します。 

* **[NIC ネットワーク セキュリティ グループ]** で、[Basic] セキュリティ グループまたは [詳細] セキュリティ グループを選択します。 [Basic] オプションを選択すると、 **[基本]** タブに構成されているものと同じ値である SQL Server VM の受信ポートを選択できます。[詳細] オプションを選択すると、既存のネットワーク セキュリティ グループを選択するか、新しいグループを作成することができます。 

* ネットワーク設定に対して他の変更を行ったり、既定値のままにしたりできます。

![SQL VM ネットワークの設定](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>監視

**[監視]** タブで、監視と自動シャットダウンを構成します。 

* **[ブート診断]** は、既定では VM に指定されているものと同じストレージ アカウントで有効になります。 このタブでは、これらの設定を変更して、 **[OS のゲスト診断]** を有効にすることができます。 
* このタブでは、 **[システム割り当てマネージド ID]** と **自動シャットダウン** を有効にすることもできます。 

![SQL VM の管理設定](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3.SQL Server の設定を構成する

**[SQL Server の設定]** タブで、SQL Server の個々の設定と最適化を構成します。 SQL Server の次の設定を構成できます。

- [接続](#connectivity)
- [認証](#authentication)
- [Azure Key Vault の統合](#azure-key-vault-integration)
- [ストレージの構成](#storage-configuration)
- [自動修正](#automated-patching)
- [自動バックアップ](#automated-backup)
- [Machine Learning サービス](#machine-learning-services)


### <a name="connectivity"></a>接続

**[SQL の接続]** で、この VM 上の SQL Server インスタンスに必要なアクセスの種類を指定します。 このチュートリアルでは、 **[パブリック (インターネット)]** を選択して、インターネット上のコンピューターやサービスから SQL Server に接続できるようにします。 このオプションを選択すると、選択したポートのトラフィックを許可するように、ファイアウォールとネットワーク セキュリティ グループが自動的に構成されます。

> [!TIP]
> 既定では、SQL Server は既知のポート (**1433**) をリッスンします。 セキュリティ強化のためには、前のダイアログでポートを変更して、1401 など、既定以外のポートをリッスンするようにします。 ポートを変更する場合は、SQL Server Management Studio (SSMS) などのクライアント ツールからそのポートを使用して接続する必要があります。

![SQL VM のセキュリティ](./media/create-sql-vm-portal/azure-sqlvm-security.png)

インターネット経由で SQL Server に接続するには、次のセクションで説明する SQL Server 認証を有効にする必要もあります。

インターネット経由によるデータベース エンジンへの接続を有効にしない場合は、次のいずれかのオプションを選択します。

* **ローカル (VM 内のみ)** : VM 内からのみ SQL Server への接続を許可します。
* **プライベート (Virtual Network 内)** : 同じ仮想ネットワーク内のマシンまたはサービスから SQL Server への接続を許可します。

一般的に、シナリオで許容される最も制限の厳しい接続を選択すると、セキュリティが向上します。 ただし、ネットワーク セキュリティ グループ (NSG) の規則と SQL 認証または Windows 認証を使用すると、すべてのオプションをセキュリティで保護できます。 NSG は、VM の作成後に編集できます。 詳細については、「 [Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

### <a name="authentication"></a>認証

SQL Server 認証が必要な場合は、 **[SQL Server の設定]** タブで **[SQL 認証]** の **[有効にする]** を選択します。

![SQL Server 認証](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> インターネット経由で SQL Server にアクセスする場合 (パブリック接続オプション)、ここで SQL 認証を有効にする必要があります。 SQL Server へのパブリック アクセスには、SQL 認証が必要です。

SQL Server 認証を有効にする場合は、 **[ログイン名]** と **[パスワード]** を指定します。 このログイン名が、SQL Server 認証ログインと **sysadmin** 固定サーバー ロールのメンバーとして構成されます。 認証モードの詳細については、「[Choose an Authentication Mode (認証モードの選択)](/sql/relational-databases/security/choose-an-authentication-mode)」を参照してください。

SQL Server 認証を有効にしない場合は、VM のローカル管理者アカウントを使用して SQL Server インスタンスに接続できます。

### <a name="azure-key-vault-integration"></a>Azure Key Vault の統合

暗号化のために Azure にセキュリティ シークレットを格納するには、 **[SQL Server の設定]** を選択し、 **[Azure Key Vault の統合]** まで下にスクロールします。 **[有効にする]** を選択し、必要な情報を入力します。 

![Azure Key Vault の統合](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

次の表では、Azure Key Vault (AKV) の統合の構成に必要なパラメーターを示します。

| PARAMETER | Description | 例 |
| --- | --- | --- |
| **Key Vault の URL** |Key Vault の場所。 |`https://contosokeyvault.vault.azure.net/` |
| **プリンシパル名** |Azure Active Directory サービスのプリンシパル名。 クライアント ID とも呼ばれます。 |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **プリンシパル シークレット** |Azure Active Directory サービスのプリンシパル シークレット。 クライアント シークレットとも呼ばれます。 |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **[資格情報名]** |**資格情報名**:AKV の統合によって、SQL Server 内に資格情報が作成され、VM からキー コンテナーにアクセスできるようになります。 この資格情報の名前を選択します。 |`mycred1` |

詳細については、 [Azure VM 上の SQL Server に関する Azure Key Vault 統合の構成](azure-key-vault-integration-configure.md)に関するページを参照してください。

### <a name="storage-configuration"></a>ストレージの構成

**[SQL Server の設定]** タブの **[ストレージの構成]** で、 **[構成の変更]** を選択してパフォーマンス最適化ストレージの構成ページを開き、ストレージの要件を指定します。

![ストレージ構成を変更できる場所を示すスクリーンショット。](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

**[ストレージの最適化]** で、次のいずれかのオプションを選択します。

* **[全般]** は、既定の設定であり、ほとんどのワークロードをサポートします。
* **[トランザクション]** は、従来のデータベース OLTP ワークロード用にストレージを最適化します。
* **[データ ウェアハウス]** は、分析とレポートのワークロード用にストレージを最適化します。

![SQL VM Storage の構成](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

既定値のままにすることも、IOPS のニーズに合わせてストレージ トポロジを手動で変更することもできます。 詳細については、[ストレージの構成](storage-configuration.md)に関する記事を参照してください。 

### <a name="sql-server-license"></a>SQL Server ライセンス

ソフトウェア アシュアランスをご利用のお客様は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して自分の SQL Server ライセンスを取得し、リソースを節約することができます。 

![SQL VM ライセンス](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>自動修正

**[自動修正]** は、既定で有効になります。 自動修正を有効にすると、Azure は SQL Server とオペレーティング システムに修正プログラムを自動的に適用します。 メンテナンス ウィンドウの曜日、時刻、および期間を指定します。 Azure は、修正プログラムの適用をこのメンテナンス ウィンドウで実行します。 メンテナンス ウィンドウのスケジュールには VM のロケールが使用されます。 Azure で SQL Server とオペレーティング システムに修正プログラムを自動的に適用しない場合は、 **[無効]** を選択します。  

![SQL VM の自動修正](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

詳細については、 [Azure Virtual Machines での SQL Server の自動修正](automated-patching.md)に関するページを参照してください。

### <a name="automated-backup"></a>自動化されたバックアップ

**[自動バックアップ]** では、すべてのデータベースの自動データベース バックアップを有効にすることができます。 既定では、自動バックアップは無効です。

SQL の自動バックアップを有効にするときは、以下の設定の構成を行います。

* バックアップのリテンション期間 (日数)
* バックアップに使用するストレージ アカウント
* バックアップの暗号化オプションとパスワード
* システム データベースのバックアップ
* バックアップ スケジュールの構成

バックアップを暗号化するには、 **[有効]** を選択します。 **パスワード** を指定します。 Azure は、バックアップを暗号化するための証明書を作成し、指定されたパスワードを使用してその証明書を保護します。 スケジュールは既定で自動的に設定されますが、 **[手動]** を選択して手動スケジュールを作成できます。 

![SQL VM の自動バックアップ](./media/create-sql-vm-portal/automated-backup.png)

詳細については、「 [Azure Virtual Machines での SQL Server の自動バックアップ](automated-backup-sql-2014.md)」をご覧ください。


### <a name="machine-learning-services"></a>Machine Learning サービス

[Machine Learning サービス](/sql/advanced-analytics/)を有効にするオプションがあります。 このオプションを使用すると、SQL Server 2017 で、Python と R で機械学習を使用できます。 **[SQL Server の設定]** ウィンドウで **[有効にする]** を選択します。


## <a name="4-review--create"></a>4.確認と作成

**[確認と作成]** タブで次の手順を実行します。
1. 概要を確認します。
1. **[作成]** を選択して、この VM に指定された SQL サーバー、リソース グループ、およびリソースを作成します。

Azure Portal でデプロイを監視できます。 画面の上部にある **[通知]** ボタンをクリックすると、デプロイの基本的な状態が表示されます。

> [!NOTE]
> Azure で SQL Server VM をデプロイする時間の例を次に示します。米国東部リージョンに既定の設定でテスト SQL Server VM をプロビジョニングした場合、完了するまでに約 12 分かかります。 リージョンや選択した設定によっては、デプロイに必要な時間が変わる可能性があります。

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> リモート デスクトップを使用して VM を開く

リモート デスクトップ プロトコル (RDP) を使用して SQL Server 仮想マシンに接続するには、次の手順を実行します。

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server 仮想マシンに接続した後は、SQL Server Management Studio を起動し、ローカル管理者の資格情報を使用して Windows 認証で接続できます。 SQL Server 認証を有効にした場合は、プロビジョニングの間に構成した SQL のログインとパスワードを使用して SQL 認証で接続することもできます。

マシンにアクセスすると、要件に基づいてマシンと SQL Server の設定を直接変更することができます。 たとえば、ファイアウォールの設定を構成したり、SQL Server の構成設定を変更したりできます。

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> SQL Server にリモート接続する

このチュートリアルでは、仮想マシンと **SQL Server 認証** に **SQL Server 認証** アクセスを選択しています。 これらの設定により、インターネット経由による任意のクライアントから SQL Server への接続を許可するように仮想マシンが自動的に構成されています (適切な SQL ログインを持っている場合)。

> [!NOTE]
> プロビジョニング中に [パブリック] を選択しなかった場合は、プロビジョニング後にポータルで SQL 接続設定を変更できます。 詳細については、「[Change your SQL connectivity settings (SQL 接続設定の変更)](ways-to-connect-to-sql.md#change)」を参照してください。

以下のセクションでは、インターネット経由で SQL Server VM インスタンスに接続する方法を示します。

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > この例では、一般的なポートの 1433 を使用します。 ただし、SQL Server VM のデプロイ時に別のポート (1401 など) を指定した場合は、この値を変更する必要があります。 


## <a name="next-steps"></a>次のステップ

Azure での SQL Server の使用に関するその他の情報については、[Azure 仮想マシンにおける SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) に関する記事と[よく寄せられる質問](frequently-asked-questions-faq.yml)に関するページを参照してください。