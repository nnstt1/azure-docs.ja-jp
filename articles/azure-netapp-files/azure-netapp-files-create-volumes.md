---
title: Azure NetApp Files の NFS ボリュームを作成する | Microsoft Docs
description: この記事では、Azure NetApp Files の NFS ボリュームを作成する方法について説明します。 使用するバージョンやベスト プラクティスなどの考慮事項について説明します。
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
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: d1aafd863e35d8cb19f529928c22645496fff671
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536265"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files の NFS ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 または NFSv4.1)、SMB3、またはデュアル プロトコル (NFSv3 と SMB、または NFSv4.1 と SMB) を使用したボリュームの作成がサポートされています。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 

この記事では、NFS ボリュームを作成する方法について説明します。 SMB ボリュームについては、[SMB ボリュームの作成](azure-netapp-files-create-volumes-smb.md)に関する記事を参照してください。 デュアルプロトコル ボリュームについては、[デュアルプロトコル ボリュームの作成](create-volumes-dual-protocol.md)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に 
* あらかじめ容量プールを設定しておく必要があります。  
    「[容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。   
* サブネットが Azure NetApp Files に委任されている必要があります。  
    「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="considerations"></a>考慮事項 

* 使用する NFS のバージョンを決定する  
  NFSv3 は、さまざまなユース ケースの処理に使用でき、通常、ほとんどのエンタープライズ アプリケーションにデプロイされます。 アプリケーションで必要なバージョン (NFSv3 または NFSv4.1) を検証し、適切なバージョンを使用してボリュームを作成する必要があります。 たとえば、[Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave) を使用する場合は、NFSv4.1 でのファイル ロックの方が、NFSv3 より推奨されます。 

* Security  
  UNIX モード ビット (読み取り、書き込み、実行) のサポートは、NFSv3 と NFSv4.1 で利用できます。 NFS ボリュームをマウントするには、NFS クライアントでのルート レベルのアクセス権が必要です。

* NFSv4.1 に対するローカル ユーザー/グループと LDAP のサポート  
  現在、NFSv4.1 ではボリュームに対するルート アクセスのみがサポートされています。 「[Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md)」を参照してください。 

## <a name="best-practice"></a>ベスト プラクティス

* ボリュームに対して適切なマウント方法を使用していることを確認します。  「[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)」を参照してください。

* NFS クライアントは、Azure NetApp Files ボリュームと同じ VNet またはピアリングされた VNet に存在する必要があります。 VNet の外部からの接続はサポートされていますが、待機時間が長くなり、全体的なパフォーマンスが低下します。

* NFS クライアントが最新であり、オペレーティング システムの最新の更新プログラムが実行されていることを確認します。

## <a name="create-an-nfs-volume"></a>NFS ボリュームを作成する

1.  [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
    * **ボリューム名**      
        作成するボリュームの名前を指定します。   

        ボリューム名は、各容量プール内で一意である必要があります。 3 文字以上になるようにしてください。 名前は英字で始まる必要があります。 使用できるのは、文字、数字、アンダースコア ('_')、ハイフン ('-') のみです。

        `default` または `bin` をボリューム名として使用することはできません。

    * **容量プール**  
        ボリュームを作成する容量プールを指定します。

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **スループット (MiB/秒)**    
        ボリュームが手動 QoS 容量プールで作成されている場合は、ボリュームに必要なスループットを指定します。   

        ボリュームが自動 QoS 容量プールで作成されている場合は、このフィールドに表示される値は (クォータ x サービス レベルのスループット) です。   

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure 仮想ネットワーク (VNet) を指定します。  

        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet から、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * **ネットワーク機能**  
        サポートされているリージョンでは、ボリュームで使用するネットワーク機能を **Basic** または **Standard** から選ぶことができます。 詳細については、「[ボリュームのネットワーク機能を構成する](configure-network-features.md)」と「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md)」を参照してください。

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、スナップショットのパスを非表示にするかどうかを指定して、プルダウン メニューでスナップショット ポリシーを選択します。 

        スナップショット ポリシーの作成については、「[スナップショット ポリシーを管理する](snapshots-manage-policy.md)」を参照してください。

        ![詳細セクションの表示](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **[プロトコル]** をクリックし、次のアクションを実行します。  
    * ボリュームのプロトコルの種類として **[NFS]** を選択します。   

    * ボリュームに対する一意の **ファイル パス** を指定します。 このパスは、マウント ターゲットを作成するときに使用されます。 パスの要件は、次のとおりです。   
        - リージョン内の各サブネットにおいて一意である必要があります。 
        - 英文字で始まる必要があります。
        - 文字、数字、ダッシュ (`-`) だけで構成する必要があります。 
        - 長さが 80 文字以内である必要があります。

    * ボリュームの **[バージョン]** ( **[NFSv3]** または **[NFSv4.1]** ) を選択します。  

    * NFSv4.1 を使用している場合は、ボリュームの **Kerberos** 暗号化を有効にするかどうかを指定します。  

        NFSv4.1 で Kerberos を使用する場合は、追加の構成が必要です。 [NFSv4.1 Kerberos 暗号化の構成](configure-kerberos-encryption.md)に関する記事の手順に従います。

    * Active Directory LDAP ユーザーと拡張グループ (最大 1024 グループ) がボリュームにアクセスできるようにする場合は、**LDAP** オプションを選択します。 「[NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)」の手順に従って、必要な構成を完了します。 
 
    *  必要に応じて **[Unix のアクセス許可]** をカスタマイズして、マウント パスの変更アクセス許可を指定します。 この設定は、マウント パスの下のファイルには適用されません。 既定の設定は `0770` です。 この既定の設定では、所有者とグループに読み取り、書き込み、実行のアクセス許可が付与されますが、他のユーザーにはアクセス許可は付与されません。     
        **[Unix のアクセス許可]** の設定には、登録の要件と考慮事項が適用されます。 [Unix のアクセス許可の構成と所有権モードの変更](configure-unix-permissions-change-ownership-mode.md)に関する記事の手順に従ってください。   

    * 必要に応じて、[NFS ボリュームのエクスポート ポリシーを構成します](azure-netapp-files-configure-export-policy.md)。

    ![NFS プロトコルを指定する](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。  次に、 **[作成]** をクリックして、ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md)
* [NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)
* [Azure NetApp Files 用に ADDS LDAP over TLS を構成する](configure-ldap-over-tls.md)
* [NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)
* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [NFS ボリュームのエクスポート ポリシーを構成する](azure-netapp-files-configure-export-policy.md)
* [Unix のアクセス許可を構成して所有権モードを変更する](configure-unix-permissions-change-ownership-mode.md) 
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure サービスの仮想ネットワーク統合について理解する](../virtual-network/virtual-network-for-azure-services.md)
