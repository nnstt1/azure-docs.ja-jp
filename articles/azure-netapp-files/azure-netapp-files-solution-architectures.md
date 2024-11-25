---
title: Azure NetApp Files を使用したソリューション アーキテクチャ | Microsoft Docs
description: Azure NetApp Files を使用したソリューション アーキテクチャのベスト プラクティスへの参照を提供します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/4/2021
ms.author: b-juche
ms.openlocfilehash: a69dd2528b4804e434cf6652d1ce2b03357025ee
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848612"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Azure NetApp Files を使用したソリューション アーキテクチャ
この記事では、Azure NetApp Files を使用するためのソリューション アーキテクチャを理解するうえで役立つベスト プラクティスへの参照を提供します。  

次の図は、Azure NetApp Files が提供するソリューション アーキテクチャのカテゴリをまとめたものです。

![ソリューション アーキテクチャのカテゴリ](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS アプリとデータベース ソリューション

このセクションでは、Linux OSS アプリケーションとデータベースのソリューションに関するリファレンスを提供します。 

### <a name="linux-oss-apps"></a>Linux OSS アプリ

* [AIX UNIX オンプレミスから Azure Linux への移行 - Azure シナリオ例](/azure/architecture/example-scenario/unix-migration/migrate-aix-azure-linux)

### <a name="mainframe-refactor"></a>メインフレームのリファクタリング

* [Azure への一般的なメインフレームのリファクタリング - Azure シナリオ例](/azure/architecture/example-scenario/mainframe/general-mainframe-refactor)
* [Advanced を使用したメインフレーム アプリケーションのリファクタリング - Azure シナリオ例](/azure/architecture/example-scenario/mainframe/refactor-mainframe-applications-advanced)

### <a name="oracle"></a>Oracle

* [Azure NetApp Files を使用した Oracle Database - Azure シナリオ例](/azure/architecture/example-scenario/file-storage/oracle-azure-netapp-files)
* [Azure NetApp Files を使用した Microsoft Azure 上の Oracle Database](https://www.netapp.com/media/17105-tr4780.pdf)
* [Microsoft Azure での Oracle VM イメージとそのデプロイ:共有ストレージの構成オプション](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Azure NetApp Files の単一ボリュームでの Oracle データベースのパフォーマンス](performance-oracle-single-volumes.md)
* [Oracle Database での Azure NetApp Files 利用のメリット](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)
*   [Azure での分散トレーニング: レーン検出 - ソリューション設計](https://www.netapp.com/media/32427-tr-4896-design.pdf)
*   [Azure での分散トレーニング: クリックスルー レート予測–ソリューション設計](https://docs.netapp.com/us-en/netapp-solutions/ai/aks-anf_introduction.html)

### <a name="education"></a>Education
* [Azure NetApp Files NFS ストレージ上の Moodle](https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-netapp-files-for-nfs-storage-with-moodle/ba-p/2300630)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows アプリと SQL Server ソリューション

このセクションでは、Windows アプリケーションと SQL Server ソリューションに関するリファレンスを提供します。

### <a name="file-sharing-and-global-file-caching"></a>ファイル共有とグローバル ファイル キャッシュ

* [Azure NetApp Files と DFS 名前空間によるエンタープライズ ファイル共有のディザスター リカバリー](https://techcommunity.microsoft.com/t5/azure-architecture-blog/disaster-recovery-for-enterprise-file-shares/ba-p/2808757)
* [Build Your Own Azure NFS?Wrestling Linux File Shares into Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares) (独自の Azure NFS の構築: Linux ファイル共有をクラウドに移行する)
* [Globally Distributed Enterprise File Sharing with Azure NetApp Files and NetApp Global File Cache](https://f.hubspotusercontent20.net/hubfs/525875/NA-580-0521-Architecture-Doc-R3.pdf) (Azure NetApp Files と NetApp Global File Cache による、グローバルに配布されるエンタープライズ ファイル共有)
* [Azure NetApp Files のクラウド コンプライアンス](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Azure NetApp Files を使用した Azure Virtual Machines 上の SQL Server - Azure シナリオ例](/azure/architecture/example-scenario/file-storage/sql-server-azure-netapp-files)
* [Azure NetApp Files を使用した Azure での SQL Server のデプロイ ガイド](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [SQL Server のデプロイに Azure NetApp Files を使用する利点](solutions-benefits-azure-netapp-files-sql-server.md)
* [Azure NetApp Files を使用して SMB 経由で SQL Server をデプロイする](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Azure NetApp Files を使用して SMB 経由で SQL Server Always On フェールオーバー クラスターをデプロイする](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Azure NetApp Files を使用して Always On 可用性グループをデプロイする](https://www.youtube.com/watch?v=y3VQmzzeyvc) 

## <a name="sap-on-azure-solutions"></a>SAP on Azure ソリューション

このセクションでは、SAP on Azure ソリューションのリファレンスを提供します。 

### <a name="generic-sap-and-sap-netweaver"></a>汎用 SAP と SAP Netweaver 

* [Azure 上の Windows で SAP NetWeaver を実行する - Azure アーキテクチャ センター](/azure/architecture/reference-architectures/sap/sap-netweaver)
* [Azure NetApp Files を使用した Microsoft Azure 上の SAP アプリケーション](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Red Hat Enterprise Linux for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [スケールアップ システムの Linux VM 向け SAP HANA - Azure アーキテクチャ センター](/azure/architecture/reference-architectures/sap/run-sap-hana-for-linux-virtual-machines)
* [Azure 上の Linux での SAP S/4HANA - Azure アーキテクチャ センター](/azure/architecture/reference-architectures/sap/sap-s4hana)
* [Linux VM を使用して SAP BW/4HANA を実行する - Azure アーキテクチャ センター](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
* [SAP HANA Azure 仮想マシンのストレージ構成](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Red Hat Enterprise Linux で Azure NetApp Files を使用した SAP HANA スケールアップの高可用性](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SUSE Linux Enterprise Server 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Red Hat Enterprise Linux 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [RHEL での HSR と Pacemaker を使用した SAP HANA スケールアウト - Azure Virtual Machines](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Azure アプリケーション整合性スナップショット ツール (AzAcSnap)](azacsnap-introduction.md)
* [Azure NetApp Files を使用した SAP HANA のディザスター リカバリー](https://docs.netapp.com/us-en/netapp-solutions-sap/pdfs/sidebar/SAP_HANA_Disaster_Recovery_with_Azure_NetApp_Files.pdf)

### <a name="sap-anydb"></a>SAP AnyDB

* [Azure 上の Oracle Database の SAP システム - Azure アーキテクチャ センター](/azure/architecture/example-scenario/apps/sap-on-oracle)
* [SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ - Azure Virtual Machines](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Azure NetApp Files を使用して SAP AnyDB (Oracle 19c) をデプロイする](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)
* [Azure NetApp Files を使用した、SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ](../virtual-machines/workloads/sap/dbms_guide_ibm.md#using-azure-netapp-files)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [SUSE Linux Enterprise Server で Azure NetApp Files を利用し、SAP IQ-NLS HA Solution をデプロイする](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [HA シナリオで SAP IQ ライセンスを管理する方法](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>SAP 技術コミュニティとブログ記事 

* [Azure NetApp Files – SAP HANA のバックアップ (秒単位)](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – スナップショット バックアップから HANA データベースを復元する](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – クラウド同期を使用した SAP HANA のオフロード バックアップ](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Azure NetApp Files を使用して SAP HANA システムのコピーを高速化する](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud Volumes ONTAP と Azure NetApp Files:SAP HANA システムの移行が簡単に](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [HANA N+M スケールアウト アーキテクチャで ANF 投資を最大化するためのアーキテクチャに関する決定 - パート 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [HANA N+M スケールアウト アーキテクチャで ANF 投資を最大化するためのアーキテクチャに関する決定 - パート 2](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [HANA N+M スケールアウト アーキテクチャで ANF 投資を最大化するためのアーキテクチャに関する決定 - パート 3](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [Azure NetApp Files を使用した SAP ランドスケープのサイズ設定とボリュームの統合](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Azure VMware Solutions

* [Azure NetApp Files と Azure VMware Solution - ゲスト OS マウント](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>仮想デスクトップ インフラストラクチャ ソリューション

このセクションでは、仮想デスクトップ インフラストラクチャ ソリューションに関するリファレンスを提供します。

### <a name="azure-virtual-desktop"></a><a name="windows-virtual-desktop"></a>Azure Virtual Desktop

* [Azure Virtual Desktop で Azure NetApp Files を利用するメリット](solutions-windows-virtual-desktop.md)
* [Azure Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Azure NetApp Files を使用してホスト プール用の FSLogix プロファイル コンテナーを作成する](../virtual-desktop/create-fslogix-profile-container.md)
* [エンタープライズ規模の Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [エンタープライズ向け Microsoft FSLogix - Azure NetApp Files ベスト プラクティス](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [MSIX アプリのアタッチ用の Azure NetApp Files の設定](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

### <a name="citrix"></a>Citrix   

* [Azure NetApp Files ベスト プラクティス ガイドによる Citrix プロファイル管理](https://www.netapp.com/pdf.html?item=/media/55973-tr-4901.pdf)


## <a name="hpc-solutions"></a>HPC ソリューション

このセクションでは、ハイ パフォーマンス コンピューティング (HPC) ソリューションに関するリファレンスを提供します。 

### <a name="generic-hpc"></a>汎用 HPC

* [Azure NetApp Files: クラウド ストレージを最大限に活用する](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Azure Batch と Azure NetApp Files で MPI ワークロードを実行する](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure CycleCloud:Azure NetApp Files 上の CycleCloud HPC 環境](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油、ガス

* [ハイ パフォーマンス コンピューティング (HPC):Azure での石油およびガス](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Azure 上での貯留層シミュレーション ソフトウェアの実行](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>電子設計自動化 (EDA)

* [電子設計自動化に Azure NetApp Files を使用するメリット](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud:Azure NetApp Files を使用した EDA HPC ラボ](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [半導体産業向け Azure](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analytics

* [Azure アーキテクチャの SAS ガイド - Azure アーキテクチャ センター | Azure NetApp Files](/azure/architecture/guide/sas/sas-overview#azure-netapp-files-nfs)
* [Azure NetApp Files: Microsoft Azure 上の SAS グリッドで使用する共有ファイル システム](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp Files: MS Azure 上の SAS グリッドで使用する共有ファイル システム – RHEL8.3/nconnect の更新情報](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [Microsoft Azure と SAS® を併用するためのベスト プラクティス](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

### <a name="healthcare"></a>医療

* [Azure NetApp Files を使用した Microsoft Azure 上の Epic EHR](https://www.netapp.com/pdf.html?item=/media/62375-tr-4909.pdf)

## <a name="azure-platform-services-solutions"></a>Azure プラットフォーム サービス ソリューション

このセクションでは、Azure プラットフォーム サービスのソリューションについて説明します。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services と Kubernetes

* [Astra: Azure NetApp Files で AKS ワークロードを保護、回復、管理する](https://cloud.netapp.com/hubfs/Astra%20Azure%20Documentation.pdf) 
* [Azure NetApp Files と Azure Kubernetes Service を統合する](../aks/azure-netapp-files.md)
* [Azure NetApp Files を使用した Azure での現実離れした Kubernetes のパフォーマンス](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = Kubernetes 用の動的および永続的ストレージ](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident - コンテナー用のストレージ オーケストレーター](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Azure Kubernetes Service (AKS) の Magento eコマース プラットフォーム](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-red-hat-openshift"></a>Azure Red Hat Openshift   

*   [Trident を使用して OpenShift から Azure NetApp Files を自動化](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/using-trident-to-automate-azure-netapp-files-from-openshift/ba-p/2367351)

### <a name="azure-batch"></a>Azure Batch

* [Azure Batch と Azure NetApp Files で MPI ワークロードを実行する](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
