---
title: Azure での RHEL に対する Pacemaker の設定 | Microsoft Docs
description: Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする
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
ms.custom: subject-rbac-steps
ms.date: 08/26/2021
ms.author: radeltch
ms.openlocfilehash: 412bbd6f7414cdeaab1c116210b511bc8000c270
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109802"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧。
  * Azure VM サイズの容量に関する重要な情報。
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ。
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン。
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167] では、Red Hat Enterprise Linux 用の OS 設定が推奨されています
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [Pacemaker クラスターでの SAP HANA システム レプリケーション](https://access.redhat.com/articles/3004101)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 高可用性クラスターのサポートポリシー - sbd と fence_sbd](https://access.redhat.com/articles/2800691)
* Azure 固有の RHEL ドキュメント:
  * [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)
  * [RHEL 8 の導入に関する考慮事項 - 高可用性とクラスター](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [RHEL 7.6 上の Pacemaker で SAP S/4HANA ASCS/ERS と Standalone Enqueue Server 2 (ENSA2) を構成する](https://access.redhat.com/articles/3974941)
  * [Azure 上の RHEL for SAP オファリング](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>クラスターのインストール

![RHEL における Pacemaker の概要](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat では、ソフトウェアでエミュレートするウォッチドッグはサポートしていません。 Red Hat では、クラウド プラットフォームの SBD はサポートしていません。 詳細については、「[Support Policies for RHEL High Availability Clusters - sbd and fence_sbd](https://access.redhat.com/articles/2800691)」(RHEL 高可用性クラスターのサポートポリシー - sbd と fence_sbd) に関するページを参照してください。
> Azure 上の Pacemaker が使用された Red Hat Enterprise Linux クラスターでサポートされるフェンス メカニズムは、Azure フェンス エージェントのみです。  

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** 登録します。 RHEL SAP の HA が有効になっているイメージを使用する場合、この手順は必要ありません。  

   使用する仮想マシンを登録し、それを、RHEL 7 リポジトリを含むプールに追加します。

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Azure Marketplace PAYG RHEL イメージにプールをアタッチすると、RHEL の使用に対して二重に請求されることになります。これは、PAYG イメージに対して 1 回、アタッチするプールの RHEL エンタイトルメントに対して 1 回です。 これを防ぐ目的で、Azure では BYOS RHEL イメージが提供されるようになりました。 詳細については、[Red Hat Enterprise Linux のサブスクリプション持ち込み Azure イメージ](../redhat/byos.md)に関するページを参照してください。

1. **[A]** SAP のリポジトリ用に RHEL を有効にします。 RHEL SAP の HA が有効になっているイメージを使用する場合、この手順は必要ありません。  

   必要なパッケージをインストールするには、次のリポジトリを有効にします。

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** RHEL HA アドオンをインストールします

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > リソースの停止に失敗した場合や、クラスター ノードが互いに通信できなくなった場合に、お客様がフェールオーバー時間の高速化の恩恵を受けられるよう、次のバージョン (またはこれら以降) の Azure Fence Agent をお勧めします。  
   > RHEL 7.7 以降では、使用可能な最新バージョンの fence-agents パッケージを使用  
   > RHEL 7.6: fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5: fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4: fence-agents-4.0.11-66.el7_4.12  
   > 詳細については、「[Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711)」 (RHEL 高可用性クラスターのメンバーを実行している Azure VM のフェンシングに非常に長い時間がかかる、フェンシングが失敗する、VM がシャットダウンする前にタイムアウトする) をご覧ください。

   Azure Fence Agent のバージョンを確認します。 必要に応じて、前述したものと同じかそれ以降のバージョンに更新します。

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Azure Fence Agent を更新する必要があり、カスタム ロールを使用している場合は、カスタム ロールを更新してアクション **powerOff** を含めるようにします。 詳細については、「[フェンス エージェントのカスタム ロールを作成する](#1-create-a-custom-role-for-the-fence-agent)」を参照してください。  

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。  

   >[!IMPORTANT]
   > クラスター構成でホスト名を使用する場合は、信頼できるホスト名解決が不可欠です。 名前が使用できず、クラスターのフェールオーバーの遅延につながる可能性がある場合、クラスター通信は失敗します。
   > /etc/hosts を使用する利点は、単一障害点になる可能性もある DNS からクラスターを独立させられることです。  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster のパスワードを同じパスワードに変更します

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Pacemaker のファイアウォール規則を追加します

   クラスター ノード間のすべてのクラスターの通信用に、次のファイアウォール規則を追加します。

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** 基本的なクラスター サービスを有効にします

   次のコマンドを実行し、Pacemaker サービスを有効にして、それを開始します。

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Pacemaker クラスターを作成します

   次のコマンドを実行し、ノードを認証してクラスターを作成します。 トークンを 30000 に設定してメモリ保持メンテナンスを可能にします。 詳細については、[Linux のこの記事][virtual-machines-linux-maintenance]を参照してください。  
   
   **RHEL 7. x** でクラスターを構築する場合は、次のコマンドを使用します。  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   **RHEL 8.X** でクラスターを構築する場合は、次のコマンドを使用します。  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   次のコマンドを実行して、クラスターの状態を確認します。  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** 予想される票を設定します。 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > マルチノード クラスター (2 つ以上のノードを持つクラスター) をビルドする場合は、投票を 2 に設定しないでください。    

1. **[1]** 同時フェンス アクションを許可します

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>STONITH デバイスの作成

STONITH デバイスは、サービス プリンシパルを使用して Microsoft Azure を承認します。 サービス プリンシパルを作成するには、次に手順に従います。

1. [https://resources.azure.com](<https://portal.azure.com>) に移動します
1. [Azure Active Directory] ブレードを開きます  
   [プロパティ] にアクセスして、ディレクトリ ID をメモします。 これは、**テナント ID** です。
1. [アプリの登録] を選択します
1. [New Registration]\(新規登録\) をクリックします
1. 名前を入力し、[Accounts in this organization directory only]\(この組織ディレクトリ内のアカウントのみ\) を選択します 
2. [アプリケーションの種類] は "Web" を選択し、サインオン URL (たとえば http:\//localhost) を入力して、[追加] をクリックします  
   サインオン URL は使用されず、任意の有効な URL を指定することができます
1. [Certificates and Secrets]\(証明書とシークレット\) を選択し、[New client secret]\(新しいクライアント シークレット\) をクリックします
1. 新しいキーの説明を入力し、[Never expires]\(有効期限なし\) を選択して [追加] をクリックします
1. ノードを値に設定します。 この値は、サービス プリンシパルの **パスワード** として使用します
1. [概要] を選択します。 アプリケーション ID をメモしておきます。 これは、サービス プリンシパルのユーザー名 (下記の手順の **ログイン ID**) として使用します

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** フェンス エージェントのカスタム ロールを作成する

既定では、サービス プリンシパルには、Azure のリソースにアクセスする権限はありません。 クラスターのすべての仮想マシンを開始および停止 (電源オフ) する権限を、サービス プリンシパルに付与する必要があります。 まだカスタム ロールを作成していない場合は、[PowerShell](../../../role-based-access-control/role-assignments-powershell.md) または [Azure CLI](../../../role-based-access-control/role-assignments-cli.md) を使って作成してください

入力ファイルには次の内容を使用します。 実際のサブスクリプションに合わせて内容を調整する必要があります。つまり、c276fc76-9cd4-44c9-99a7-4fd71546436e and e91d47c4-76f3-4271-a796-21b4ecfe3624 は、ご利用のサブスクリプションの ID に置き換えます。 ご利用のサブスクリプションが 1 つしかない場合は、AssignableScopes の 2 つ目のエントリは削除してください。

```json
{
      "Name": "Linux Fence Agent Role",
      "description": "Allows to power-off and start virtual machines",
      "assignableScopes": [
              "/subscriptions/e663cc2d-722b-4be1-b636-bbd9e4c60fd9",
              "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
      ],
      "actions": [
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/powerOff/action",
              "Microsoft.Compute/virtualMachines/start/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** サービス プリンシパルにカスタム ロールを割り当てる

最後の章で作成したカスタム ロール "Linux Fence Agent Role" をサービス プリンシパルに割り当てます。 所有者ロールは今後使わないでください。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../../role-based-access-control/role-assignments-portal.md)」を参照してください。   
必ず両方のクラスターノードにロールを割り当ててください。    
      
### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH デバイスを作成します

仮想マシンのアクセス許可を編集したあとで、クラスターの STONITH デバイスを構成できます。

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> 'pcmk_host_map' のオプションは、RHEL ホスト名と Azure VM 名が同一でない場合のみ、このコマンドで必要です。 **hostname:vm-name** の形式でマッピングを指定します。
> このコマンドの太字のセクションを参照してください。 詳細については、[pcmk_host_map で stonith デバイスへのノード マッピングを指定する場合に使用する形式](https://access.redhat.com/solutions/2619961)に関するページを参照してください。

RHEL **7.X** の場合は、次のコマンドを使用してフェンス デバイスを構成します。    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 pcmk_delay_max=15 \
op monitor interval=3600
</code></pre>

RHEL **8.X** の場合は、次のコマンドを使用してフェンス デバイスを構成します。  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 pcmk_delay_max=15 \
op monitor interval=3600
</code></pre>

> [!TIP]
> `pcmk_delay_max` 属性は、2 ノード Pacemaker クラスターでのみ構成します。 2 ノード Pacemaker クラスターでのフェンス レース防止の詳細については、[2 ノード クラスターでフェンスを遅延させて "フェンスが機能停止する" シナリオのフェンス レースを防ぐこと](https://access.redhat.com/solutions/54829)に関するページを参照してください。 
 

> [!IMPORTANT]
> 監視およびフェンス操作は逆シリアル化されます。 その結果、長時間実行されている監視操作と同時フェンス イベントがある場合、既に実行されている監視操作により、クラスターのフェールオーバーに遅延は発生しません。  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** STONITH デバイスの使用を有効にします

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Azure Fence Agent では、[標準 ILB を使用した VM 用のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)に関する記事で説明されているように、使用可能なソリューションと共に、パブリック エンドポイントへの送信接続が必要です。  


## <a name="optional-stonith-configuration"></a>オプションの STONITH 構成  

> [!TIP]
> このセクションは、特殊なフェンス デバイス `fence_kdump` を構成する必要がある場合にのみ適用されます。  

VM 内で診断情報を収集する必要がある場合は、フェンス エージェント `fence_kdump` に基づいて追加の STONITH デバイスを構成すると有用な場合があります。 `fence_kdump` エージェントでは、他のフェンス メソッドが呼び出される前に、kdump クラッシュ復旧に入ったノードを検出してクラッシュ復旧サービスを完了できるようにすることができます。 `fence_kdump` は、Azure VM 使用時の Azure Fence Agent のような、従来のフェンス メカニズムに代わるものではないことに注意してください。   

> [!IMPORTANT]
> `fence_kdump` が第 1 レベルの stonith として構成されている場合、それによってフェンス操作で遅延が発生し、アプリケーション リソースのフェールオーバーでそれぞれ遅延が発生することに注意してください。  
> 
> クラッシュ ダンプが正常に検出された場合、フェンスはクラッシュ復旧サービスが完了するまで遅延されます。 失敗したノードが到達不能な場合や応答しない場合は、構成されている繰り返しの回数と、`fence_kdump` のタイムアウトによって決まる時間だけフェンスが遅延されます。 詳細については、「[Red Hat Pacemaker クラスターで fence_kdump を構成する方法](https://access.redhat.com/solutions/2876971)」を参照してください。  
> 提案されている fence_kdump のタイムアウトは、実際の環境に合わせて調整する必要がある場合があります。
>     
> `fence_kdump` stonith を構成するのは、VM 内で診断情報を収集する必要がある場合のみとし、必ず Azure Fence Agent のような従来のフェンス方式と組み合わせることをお勧めします。   

以下の Red Hat KB には、`fence_kdump` stonith の構成に関する重要な情報が含まれています。

* [Red Hat Pacemaker クラスターで fence_kdump を構成する方法](https://access.redhat.com/solutions/2876971)
* [Pacemaker を使用する RHEL クラスターで STONITH レベルを構成および管理する方法](https://access.redhat.com/solutions/891323)
* [2.0.14 よりも古い kexec-tools を使用すると "X 秒後にタイムアウトします" と表示されて RHEL 6 0r 7 HA クラスターで fence_kdump が失敗する](https://access.redhat.com/solutions/2388711)
* 既定のタイムアウトを変更する方法については、「[RHEL 6、7、8 HA アドオンで使用するために kdump を構成する方法](https://access.redhat.com/articles/67570)」を参照してください
* `fence_kdump` の使用時にフェールオーバーの遅延を減らす方法については、「[fence_kdump 構成の追加時に予想されるフェールオーバーの遅延を減らすことができるか](https://access.redhat.com/solutions/5512331)」を参照してください
   
Azure Fence Agent の構成に加えて以下のオプションの手順を実行し、第 1 レベルの STONITH 構成として `fence_kdump` を追加します。 


1. **[A]** kdump がアクティブになっていて、構成されていることを確認します。  
    ```
    systemctl is-active kdump
    # Expected result
    # active
    ```
2. **[A]** `fence_kdump` フェンス エージェントをインストールします。  
    ```
    yum install fence-agents-kdump
    ```
3. **[1]** クラスター内に `fence_kdump` stonith デバイスを作成します。   
    <pre><code>
    pcs stonith create rsc_st_kdump fence_kdump pcmk_reboot_action="off" <b>pcmk_host_list="prod-cl1-0 prod-cl1-1</b>" timeout=30
    </code></pre>

4. **[1]** stonith レベルを構成して、`fence_kdump` フェンス メカニズムが最初に動作するようにします。  
    <pre><code>
    pcs stonith create rsc_st_kdump fence_kdump pcmk_reboot_action="off" <b>pcmk_host_list="prod-cl1-0 prod-cl1-1</b>"
    pcs stonith level add 1 <b>prod-cl1-0</b> rsc_st_kdump
    pcs stonith level add 1 <b>prod-cl1-1</b> rsc_st_kdump
    pcs stonith level add 2 <b>prod-cl1-0</b> rsc_st_azure
    pcs stonith level add 2 <b>prod-cl1-1</b> rsc_st_azure
    # Check the stonith level configuration 
    pcs stonith level
    # Example output
    # Target: <b>prod-cl1-0</b>
    # Level 1 - rsc_st_kdump
    # Level 2 - rsc_st_azure
    # Target: <b>prod-cl1-1</b>
    # Level 1 - rsc_st_kdump
    # Level 2 - rsc_st_azure
    </code></pre>

5. **[A]** ファイアウォールを通過する `fence_kdump` のために必要なポートを許可します
    ```
    firewall-cmd --add-port=7410/udp
    firewall-cmd --add-port=7410/udp --permanent
    ```

6. **[A]** `initramfs` イメージ ファイルに `fence_kdump` と `hosts` のファイルが含まれることを確認します。 詳細については、「[Red Hat Pacemaker クラスターで fence_kdump を構成する方法](https://access.redhat.com/solutions/2876971)」を参照してください。   
    ```
    lsinitrd /boot/initramfs-$(uname -r)kdump.img | egrep "fence|hosts"
    # Example output 
    # -rw-r--r--   1 root     root          208 Jun  7 21:42 etc/hosts
    # -rwxr-xr-x   1 root     root        15560 Jun 17 14:59 usr/libexec/fence_kdump_send
    ```

7. **[A]** `kexec-tools` の一部のバージョンでタイムアウトによる `fence_kdump` の失敗が発生しないように、`/etc/kdump.conf` で `fence_kdump_nodes` の構成を実行します。 詳細については、「[kexec-tools バージョン 2.0.15 以降の使用時に fence_kdump_nodes が指定されていないと fence_kdump がタイムアウトする](https://access.redhat.com/solutions/4498151)」と、「[2.0.14 より前のバージョンの kexec-tools の使用時に RHEL 6 または 7 の高可用性クラスターで "X 秒後にタイムアウトします" と表示されて fence_kdump が失敗する](https://access.redhat.com/solutions/2388711)」を参照してください。 下に、2 ノード クラスターの構成例を示します。 `/etc/kdump.conf` で変更を加えた後に、kdump イメージを再生成する必要があります。 これは、`kdump` サービスを再起動することで実現できます。  

    <pre><code>
    vi /etc/kdump.conf
    # On node <b>prod-cl1-0</b> make sure the following line is added
    fence_kdump_nodes  <b>prod-cl1-1</b>
    # On node <b>prod-cl1-1</b> make sure the following line is added
    fence_kdump_nodes  <b>prod-cl1-0</b>

    # Restart the service on each node
    systemctl restart kdump
    </code></pre>

8. ノードをクラッシュさせて構成をテストします。 詳細については、「[Red Hat Pacemaker クラスターで fence_kdump を構成する方法](https://access.redhat.com/solutions/2876971)」を参照してください。  

    > [!IMPORTANT]
    > クラスターが既に運用環境で使用中の場合は、ノードをクラッシュさせるとアプリケーションに影響があるため、それに応じてテストを計画します。   

    ```
    echo c > /proc/sysrq-trigger
    ```
## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
