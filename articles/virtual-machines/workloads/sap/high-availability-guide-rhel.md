---
title: RHEL での SAP NW のための Azure VM 高可用性 | Microsoft Docs
description: Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/11/2021
ms.author: radeltch
ms.openlocfilehash: 2bbc8ccf445a9fb45e2dc91041287d6ca47653a2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966882"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

この記事では、仮想マシンのデプロイと構成、クラスター フレームワークのインストール、高可用性 SAP NetWeaver 7.50 システムのインストールの方法について説明します。
この例の構成やインストール コマンドなどでは、ASCS インスタンス番号として 00、ERS インスタンス番号として 02、SAP システム ID として NW1 が使用されています。 この例のリソース (仮想マシン、仮想ネットワークなど) の名前は、リソース プレフィックス NW1 で [ASCS/SCS テンプレート][template-multisid-xscs]を使用してリソースを作成していることを想定しています。

はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167] では、Red Hat Enterprise Linux 用の OS 設定が推奨されています
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [Red Hat Gluster Storage 用の製品ドキュメント](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.5)
* [Pacemaker クラスターでの SAP Netweaver](https://access.redhat.com/articles/3150081)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 7.5 内でスタンドアロン リソースを使用して SAP Netweaver のために ASCS/ERS を構成する](https://access.redhat.com/articles/3569681)
  * [RHEL 上の Pacemaker で SAP S/4HANA ASCS/ERS と Standalone Enqueue Server 2 (ENSA2) を構成する](https://access.redhat.com/articles/3974941)
* Azure 固有の RHEL ドキュメント:
  * [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>概要

高可用性を実現するため、SAP NetWeaver には共有ストレージが必要です。 GlusterFS は別のクラスターで構成されており、複数の SAP システムでそれを使用できます。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS、SAP HANA データベースでは、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) の使用をお勧めします。 (A)SCS および ERS ロード バランサーの構成を次に示します。

### <a name="ascs"></a>(A)SCS

* フロントエンドの構成
  * IP アドレス 10.0.0.7
* プローブ ポート
  * ポート 620<strong>&lt;nr&gt;</strong>
* 負荷分散規則
  * Standard Load Balancer を使用する場合は、 **[HA ポート]** を選択します
  * Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* フロントエンドの構成
  * IP アドレス 10.0.0.8
* プローブ ポート
  * ポート 621<strong>&lt;nr&gt;</strong>
* 負荷分散規則
  * Standard Load Balancer を使用する場合は、 **[HA ポート]** を選択します
  * Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み

## <a name="setting-up-glusterfs"></a>GlusterFS の設定

SAP NetWeaver では、転送とプロファイル ディレクトリ用の共有ストレージが必要です。 GlusterFS for SAP NetWeaver を設定する方法については、「[Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS][glusterfs-ha]」を参照してください。

## <a name="setting-up-ascs"></a>(A)SCS のセットアップ

GitHub にある Azure テンプレートを使用して、仮想マシン、可用性セット、ロード バランサーなどの必要なすべての Azure リソースをデプロイできます。また、各リソースを手動でデプロイすることもできます。

### <a name="deploy-linux-via-azure-template"></a>Azure テンプレートを使用した Linux のデプロイ

Azure Marketplace には Red Hat Enterprise Linux のイメージが含まれており、これを新しい仮想マシンのデプロイに使用できます。 GitHub にあるいずれかのクイック スタート テンプレートを使用して、必要なすべてのリソースをデプロイできます。 テンプレートでは、仮想マシン、ロード バランサー、可用性セットなどをデプロイできます。テンプレートをデプロイするには、次の手順に従います。

1. Azure portal 上で [ASCS/SCS テンプレート][template-multisid-xscs]を開きます  
1. 次のパラメーターを入力します
   1. Resource Prefix (リソース プレフィックス)  
      使用するプレフィックスを入力します。 この値は、デプロイされるリソースのプレフィックスとして使用されます。
   1. スタックの種類  
      SAP NetWeaver のスタックの種類を選択します
   1. OS の種類  
      いずれかの Linux ディストリビューションを選択します。 この例では RHEL 7 を選択します
   1. データベースの種類  
      HANA を選択します
   1. SAP システム数  
      このクラスター内で実行されている SAP システムの数です。 [1] を選択します。
   1. システムの可用性  
      [HA] を選択します
   1. 管理ユーザー名、管理パスワード、SSH キー  
      コンピューターへのサインインに使用できる新しいユーザーが作成されます。
   1. サブネット ID  
   VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、この ID は、/subscriptions/ **&lt;サブスクリプション ID&gt;** /resourceGroups/ **&lt;リソース グループ名&gt;** /providers/Microsoft.Network/virtualNetworks/ **&lt;仮想ネットワーク名&gt;** /subnets/ **&lt;サブネット名&gt;** のようになります。

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal を使用した手動による Linux のデプロイ

まず、このクラスターの仮想マシンを作成する必要があります。 その後、ロード バランサーを作成し、バックエンド プール内の仮想マシンを使用します。

1. リソース グループを作成します
1. 仮想ネットワークを作成します
1. 可用性セットを作成します  
   更新ドメインの最大数を設定します
1. 仮想マシン 1 を作成します  
   この Red Hat Enterprise Linux 7.4 イメージの例では、RHEL 7 以上を使用します<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   前に作成された可用性セットを選択します  
1. 仮想マシン 2 を作成します  
   この Red Hat Enterprise Linux 7.4 イメージの例では、RHEL 7 以上を使用します<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   前に作成された可用性セットを選択します  
1. 両方の仮想マシンに、少なくとも 1 つのデータ ディスクを追加します  
   データ ディスクは /usr/sap/`<SAPSID`> ディレクトリに使用されます
1. ロード バランサー (内部、標準) を作成します。  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 10.0.0.7
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **nw1-ascs-frontend**)
         1. 割り当てを "静的" に設定し、IP アドレスを入力します (例: **10.0.0.7**)
         1. [OK] をクリックします
      1. ASCS ERS の IP アドレス 10.0.0.8
         * 上記の手順を繰り返して、ERS の IP アドレスを作成します (例: **10.0.0.8** と **nw1-aers-frontend**)
   1. バックエンド プールの作成
      1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
      1. 新規のバックエンド プールの名前を入力します (例: **nw1-backend**)
      1. [仮想マシンの追加] をクリックします。
      1. 仮想マシンを選択します。
      1. (A)SCS クラスターの仮想マシンとその IP アドレスを選択します。
      1. [追加] をクリックします。
   1. 正常性プローブを作成します
      1. ASCS のポート 620 **00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **nw1-ascs-hp**)
         1. プロトコルに TCP、ポートに 620 **00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621 **02**
         * 上記の手順を繰り返して、ERS の正常性プローブを作成します (例: 621 **02** と **nw1-aers-hp**)
   1. 負荷分散規則
      1. ASCS の負荷分散規則
         1. ロード バランサーを開き、負荷分散規則を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **nw1-lb-ascs**)
         1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **nw1-ascs-frontend**、**nw1-backend**、および **nw1-ascs-hp**)
         1. **[HA ポート]** を選択します
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
         * 上記の手順を繰り返して、ERS の負荷分散規則を作成します (例: **nw1-lb-ers**)
1. または、シナリオに基本的なロード バランサー (内部) が必要な場合は、次の手順に従ってください。  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 10.0.0.7
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **nw1-ascs-frontend**)
         1. 割り当てを "静的" に設定し、IP アドレスを入力します (例: **10.0.0.7**)
         1. [OK] をクリックします
      1. ASCS ERS の IP アドレス 10.0.0.8
         * 上記の手順を繰り返して、ERS の IP アドレスを作成します (例: **10.0.0.8** と **nw1-aers-frontend**)
   1. バックエンド プールの作成
      1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
      1. 新規のバックエンド プールの名前を入力します (例: **nw1-backend**)
      1. [仮想マシンの追加] をクリックします。
      1. 前の手順で作成した可用性セットを選択します
      1. (A)SCS クラスターの仮想マシンを選択します
      1. [OK] をクリックします
   1. 正常性プローブを作成します
      1. ASCS のポート 620 **00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **nw1-ascs-hp**)
         1. プロトコルに TCP、ポートに 620 **00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621 **02**
         * 上記の手順を繰り返して、ERS の正常性プローブを作成します (例: 621 **02** と **nw1-aers-hp**)
   1. 負荷分散規則
      1. ASCS の 32 **00** TCP
         1. ロード バランサーを開き、負荷分散規則を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **nw1-lb-3200**)
         1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **nw1-ascs-frontend**)
         1. プロトコルは **TCP** のままにし、ポートに「**3200**」を入力します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
      1. ASCS の追加のポート
         * ASCS のポート 36 **00**、39 **00**、81 **00**、5 **00** 13、5 **00** 14、5 **00** 16 と TCP に対して上記の手順を繰り返します
      1. ASCS ERS の追加のポート
         * ASCS ERS のポート 33 **02**、5 **02** 13、5 **02** 14、5 **02** 16 と TCP に対して上記の手順を繰り返します

> [!IMPORTANT]
> フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。  

> [!Note]
> パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。  

> [!IMPORTANT]
> Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](../../../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。

### <a name="create-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Setting up Pacemaker on Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md)」 (Azure で Red Hat Enterprise Linux に Pacemaker を設定する) に記載の手順に従って、この (A)SCS サーバーに対して基本的な Pacemaker クラスターを作成します。

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver のインストールの準備

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** 共有ディレクトリを作成します

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** GlusterFS クライアントとその他の要件をインストールします

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** resource-agents-sap のバージョンを確認します

   インストールされている resource-agents-sap パッケージのバージョンが、3.9.5-124.el7 以上であることを確認します
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** マウント エントリを追加します

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   新しい共有をマウントします

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** スワップ ファイルを構成します

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   エージェントを再起動して変更をアクティブにします

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** RHEL 構成

   SAP Note [2002167] 内の説明に従って RHEL を構成します

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS のインストール

1. **[1]** クラスターの既定のプロパティの構成

   ```
   pcs resource defaults resource-stickiness=1
   pcs resource defaults migration-threshold=3
   ```

1. **[1]** ASCS インスタンス用の仮想 IP リソースと正常性プローブを作成します

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver ASCS をインストールします  

   root として SAP NetWeaver ASCS を最初のノードにインストールします。その際、ASCS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>nw1-ascs</b>、<b>10.0.0.7</b>) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、<b>00</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   インストールで /usr/sap/**NW1**/ASCS **00** へのサブフォルダーの作成に失敗する場合は、ASCS **00** フォルダーの所有者とグループを設定し、もう一度試してください。

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** ERS インスタンス用の仮想 IP リソースと正常性プローブを作成します

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver ERS をインストールします  

   root として SAP NetWeaver ERS を 2 番目のノードにインストールします。その際、ERS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>nw1-aers</b>、<b>10.0.0.8</b>) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、<b>02</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   インストールで /usr/sap/**NW1**/ERS **02** へのサブフォルダーの作成に失敗する場合は、ERS **02** フォルダーの所有者とグループを設定し、もう一度試してください。

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** ASCS/SCS および ERS インスタンス プロファイルを適用します

   * ASCS/SCS プロファイル

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   ENSA1 と ENSA2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。    

   * ERS プロファイル

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** キープ アライブを構成します

   SAP NetWeaver アプリケーション サーバーと ASCS/SCS の間の通信は、ソフトウェア ロード バランサーを介してルーティングされます。 ロード バランサーは、構成可能なタイムアウト後に非アクティブな接続を切断します。 これを防止するには、SAP NetWeaver ASCS/SCS プロファイル内にパラメーターを設定し (ENSA1 を使用している場合)、ENSA1/ENSA2 の両方について、すべての SAP サーバーで Linux システムの `keepalive` 設定を変更する必要があります。 詳細については、[SAP Note 1410736][1410736] を参照してください。

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** /usr/sap/sapservices ファイルを更新します

   Sapinit スタートアップ スクリプトによってインスタンスが開始されるのを防ぐために、Pacemaker によって管理されているすべてのインスタンスを /usr/sap/sapservices ファイルからコメント アウトする必要があります。 SAP HANA インスタンスが HANA SR と一緒に使用されている場合は、SAP HANA インスタンスをコメント アウトしないでください。

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** SAP クラスター リソースを作成します

  エンキュー サーバー 1 のアーキテクチャ (ENSA1) を使用する場合は、次のようにリソースを定義します。

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource meta g-<b>NW1</b>_ASCS resource-stickiness=3000

   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP では、SAP NW 7.52 の時点で、レプリケーションを含むエンキュー サーバー 2 のサポートが導入されました。 ABAP Platform 1809 以降では、エンキュー サーバー 2 が既定でインストールされます。 エンキュー サーバー 2 のサポートについては、SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) を参照してください。
   エンキュー サーバー 2 アーキテクチャ ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) を使用する場合は、resource-agents-sap-4.1.1-12.el7.x86_64 以降のリソース エージェントをインストールし、リソースを次のように定義します。

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS

   sudo pcs resource meta g-<b>NW1</b>_ASCS resource-stickiness=3000
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS

   sudo pcs resource meta rsc_sap_<b>NW1</b>_<b>ERS02</b> resource-stickiness=3000
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then start g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   以前のバージョンからアップグレードし、エンキュー サーバー 2 に切り替えている場合は、SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322) を参照してください。 

   > [!NOTE]
   > 上記のタイムアウト構成はほんの一例であり、特定の SAP 設定では調整する必要がある場合があります。 

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** ASCS および ERS に対するファイアウォール規則を両方のノード上に追加します

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver アプリケーション サーバーの準備

一部のデータベースでは、データベース インスタンスのインストールがアプリケーション サーバーで実行される必要があります。 このような場合に使用できるようにアプリケーション サーバー仮想マシンを準備します。

次の手順では、ASCS/SCS および HANA サーバーとは別のサーバーにアプリケーション サーバーをインストールすることを前提としています。 それ以外の場合、以下の手順の一部 (ホスト名解決の構成など) は必要ありません。

1. ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. sapmnt ディレクトリを作成します

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. GlusterFS クライアントとその他の要件をインストールします

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. マウント エントリを追加します

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   新しい共有をマウントします

   <pre><code>sudo mount -a
   </code></pre>

1. スワップ ファイルを構成します
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   エージェントを再起動して変更をアクティブにします

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>データベースのインストール

この例では、SAP HANA に SAP NetWeaver がインストールされます。 このインストールではサポートされているすべてのデータベースを使用できます。 Azure への SAP HANA のインストール方法の詳細については、「[High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]」 (Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性) をご覧ください。

1. SAP データベース インスタンスのインストールを実行します

   root として SAP NetWeaver データベース インスタンスをインストールします。その際、データベースのロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>nw1-db</b> と <b>10.0.0.13</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver アプリケーション サーバーのインストール

次の手順に従って、SAP アプリケーション サーバーをインストールします。

1. アプリケーション サーバーを準備します

   前述の「[SAP NetWeaver アプリケーション サーバーの準備](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)」の章の手順に従って、アプリケーション サーバーを準備します。

1. SAP NetWeaver アプリケーション サーバーをインストールします

   プライマリまたは追加の SAP NetWeaver アプリケーション サーバーをインストールします。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA Secure Store を更新します

   SAP HANA システム レプリケーション セットアップの仮想名を指すように SAP HANA Secure Store を更新します。

   次のコマンドを実行して、エントリを \<sapsid>adm として一覧表示します

   <pre><code>hdbuserstore List
   </code></pre>

   これにより、次のようにすべてのエントリが一覧表示されます
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   出力には、既定のエントリの IP アドレスがロード バランサーの IP アドレスではなく仮想マシンを指していることが示されます。 このエントリは、ロード バランサーの仮想ホスト名を指すように変更する必要があります。 同じポート (上の出力内の **30313**) とデータベース名 (上の出力内の **HN1**) を必ず使用してください。

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

1. ASCS インスタンスを手動で移行する

   テスト開始前のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   次のコマンドを root として実行して、ASCS インスタンスを移行します。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   テスト後のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. ノードのクラッシュをシミュレートする

   テスト開始前のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ASCS インスタンスが実行されているノードで、次のコマンドを root として実行します。

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   ノードの起動後の状態は、再び次のようになります。

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   次のコマンドを実行して、失敗したリソースを取り除きます。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   テスト後のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. メッセージ サーバー プロセスを強制終了する

   テスト開始前のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   次のコマンドを root として実行して、メッセージ サーバーのプロセスを特定し、強制終了します。

   <pre><code>[root@nw1-cl-0 ~]# pgrep -f ms.sapNW1 | xargs kill -9
   </code></pre>

   メッセージ サーバーは、1 回だけ強制終了しても、`sapstart` によって再起動されます。 強制終了を複数回実行すると、Pacemaker は最終的に ASCS インスタンスを他のノードに移動します。 テスト後に、次のコマンドを root として実行して、ASCS と ERS インスタンスのリソースの状態をクリーンアップします。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   テスト後のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. エンキュー サーバー プロセスを強制終了する

   テスト開始前のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ASCS インスタンスが実行されているノードで、次のコマンドを root として実行して、エンキュー サーバーを強制終了します。

   <pre><code>
    #If using ENSA1 
    [root@nw1-cl-1 ~]# pgrep -f en.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enq.sapNW1 | xargs kill -9
   </code></pre>

   ENSA1 の場合、ASCS インスタンスがすぐに他のノードにフェールオーバーします。 ASCS インスタンスの開始後に、ERS インスタンスもフェールオーバーします。 テスト後に、次のコマンドを root として実行して、ASCS と ERS インスタンスのリソースの状態をクリーンアップします。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   テスト後のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. エンキュー レプリケーション サーバー プロセスを強制終了する

   テスト開始前のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ERS インスタンスが実行されているノードで、次のコマンドを root として実行して、エンキュー レプリケーション サーバー プロセスを強制終了します。

   <pre><code>
    #If using ENSA1
    [root@nw1-cl-1 ~]# pgrep -f er.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enqr.sapNW1 | xargs kill -9
   </code></pre>

   このコマンドを 1 回だけ実行しても、プロセスは `sapstart` によって再起動されます。 複数回実行すれば、`sapstart` によるプロセスの再起動はなくなり、リソースは停止状態になります。 テスト後に、次のコマンドを root として実行して、ERS インスタンスのリソースの状態をクリーンアップします。

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   テスト後のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. エンキュー sapstartsrv プロセスを強制終了する

   テスト開始前のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS が実行されているノードで、次のコマンドを root として実行します。

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   sapstartsrv プロセスは、監視の一環として Pacemaker リソース エージェントによって常に再起動される必要があります。 テスト後のリソースの状態:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>次のステップ

* [RHEL for SAP アプリケーション マルチ SID 上の Azure VM での SAP NW の HA ガイド](./high-availability-guide-rhel-multi-sid.md)
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
