---
title: SAP on RHEL のための Azure Large Instances の高可用性
description: Red Hat Enterprise Linux で Pacemaker クラスターを使用して SAP HANA データベースのフェールオーバーを自動化する方法について説明します。
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 199d38a4c6ddca96c745342bcef0b07dc78b48bd
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401296"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>SAP on RHEL のための Azure Large Instances の高可用性

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*ブラックリスト*" と "*スレーブ*" という用語への言及があります。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

この記事では、SAP HANA データベースのフェールオーバーが自動化されるように、RHEL 7 で Pacemaker クラスターを構成する方法について説明します。 このガイドの手順を完了するには、Linux、SAP HANA、Pacemaker についてよく理解している必要があります。

次の表は、この記事全体で使用されるホスト名を示したものです。 この記事のコード ブロックでは、実行する必要のあるコマンドと、それらのコマンドの出力が示されています。 各コマンドで参照されているノードに注意してください。

| Type | ホスト名 | Node|
|-------|-------------|------|
|プライマリ ホスト|`sollabdsm35`|ノード 1|
|セカンダリ ホスト|`sollabdsm36`|ノード 2|

## <a name="configure-your-pacemaker-cluster"></a>Pacemaker クラスターを構成する


クラスターの構成を始める前に、SSH キーの交換を設定して、ノード間に信頼を確立します。

1. 次のコマンドを使用して、両方のノードに同一の `/etc/hosts` を作成します。

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  SSH キーを作成して交換します。
    1. SSH キーを生成します。

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. パスワードレスの SSH のために、キーを他のホストにコピーしします。
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  両方のノードで SELinux を無効にします。
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. サーバーを再起動した後、次のコマンドを使用して SELinux の状態を確認します。
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. NTP (ネットワーク タイム プロトコル) を構成します。 両方のクラスター ノードの時刻とタイム ゾーンが一致している必要があります。 次のコマンドを使用して `chrony.conf` を開き、ファイルの内容を確認します。
    1. 次の内容を config ファイルに追加する必要があります。 実際の値は、お使いの環境に合わせて変更してください。
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. chrony サービスを有効にします。 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. システムを更新する
    1. SBD デバイスのインストールを始める前に、まず、最新の更新プログラムをシステムにインストールします。
    1. お客様は、「[RHEL 高可用性クラスターのサポート ポリシー - クラスターでの SAP HANA の管理](https://access.redhat.com/articles/3397471)」で説明されているように、resource-agents-sap-hana パッケージの 4.1.1-12.el7_6.26 以上がインストールされていることを確認する必要があります
    1. システムを完全に更新したくない場合は、推奨ではありませんが、少なくとも次のパッケージを更新してください。
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. SAP HANA と RHEL-HA のリポジトリをインストールします。

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. すべてのノードに Pacemaker、SBD、OpenIPMI、ipmitool、fencing_sbd の各ツールをインストールします。

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>ウォッチドッグを構成する

このセクションでは、ウォッチドッグを構成する方法について説明します。 このセクションでは、この記事の冒頭で参照されている 2 つの同じホスト `sollabdsm35` と `sollabdsm36` を使用します。

1. どのシステムでもウォッチドッグ デーモンが実行されていないことを確認します。
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. インストールの間にインストールされる既定の Linux ウォッチドッグは iTCO ウォッチドッグであり、これは UCS と HPE SDFlex システムではサポートされていません。 したがって、このウォッチドッグは無効にする必要があります。
    1. 間違ったウォッチドッグがインストールされ、システムに読み込まれます。
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. 環境から間違ったドライバーをアンロードします。
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. 次回のシステム起動時にドライバーが読み込まれないようにするには、ドライバーをブロックリストに登録する必要があります。 iTCO モジュールをブロックリストに登録するには、`50-blacklist.conf` ファイルの最後に以下を追加します。
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. そのファイルをセカンダリ ホストにコピーします。
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. ipmi サービスが開始されているかどうかをテストします。 IPMI タイマーが実行されていないことが重要です。 タイマーの管理は SBD Pacemaker サービスから行われます。
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. 既定では、必要なデバイス /dev/watchdog は作成されません。

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. IPMI ウォッチドッグを構成します。

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. ウォッチドッグ構成ファイルをセカンダリにコピーします。
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  ipmi サービスを有効にして開始します。
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     これで、IPMI サービスが開始され、デバイスの /dev/watchdog が作成されました。しかし、タイマーはまだ停止されています。 後で、SBD によってウォッチドッグのリセットが管理され、IPMI タイマーが有効にされます。
7.  /dev/watchdog は存在しているが使用されていないことを確認します。
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD の構成
このセクションでは、SBD を構成する方法について説明します。 このセクションでは、この記事の冒頭で参照されている 2 つの同じホスト `sollabdsm35` と `sollabdsm36` を使用します。

1.  iSCSI または FC ディスクを両方のノードで表示できることを確認します。 この例では、FC ベースの SBD デバイスを使用します。 SBD フェンスの詳細については、「[RHEL 高可用性クラスターの設計ガイダンス - SBD の考慮事項](https://access.redhat.com/articles/2941601)」および「[RHEL 高可用性クラスターのサポート ポリシー - sbd と fence_sbd](https://access.redhat.com/articles/2800691)」を参照してください
2.  すべてのノードで LUN-ID が同じである必要があります。
  
3.  SBD デバイスのマルチパスの状態を確認します。
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  SBD ディスクを作成し、クラスターのプリミティブ フェンスを設定します。 この手順は、最初のノードで実行する必要があります。
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  SBD の構成をノード 2 にコピーします。
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  SBD ディスクを両方のノードから表示できることを確認します。
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  SBD デバイスを SBD 構成ファイルに追加します。

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>クラスターの初期化
このセクションでは、クラスターを初期化します。 このセクションでは、この記事の冒頭で参照されている 2 つの同じホスト `sollabdsm35` と `sollabdsm36` を使用します。

1.  クラスター ユーザーのパスワード (すべてのノード) を設定します。
    ```
    passwd hacluster
    ```
2.  すべてのシステムで PCS を起動します。
    ```
    systemctl enable pcsd
    ```
  

3.  ファイアウォールを停止し、(すべてのノードで) 無効にします。
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  pcsd サービスを開始します。
    ```
    systemctl start pcsd
    ```

5.  ノード 1 からのクラスター認証のみを実行します。

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  クラスターを作成します。
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  クラスターの状態を確認します。

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. 1 つのノードがクラスターに参加していない場合は、ファイアウォールがまだ実行されているかどうかを確認します。

9. SBD デバイスを作成して有効にする
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. クラスターを停止し、(すべてのノードで) クラスター サービスを再起動します。

    ```
    pcs cluster stop --all
    ```

11. (すべてのノードで) クラスター サービスを再起動します。

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync で SBD サービスを開始する必要があります。

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. クラスターを再起動します (pcsd から自動的に起動されない場合)。

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. STONITH の設定を有効にします。
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. 1 つのリソースで新しいクラスターの状態を確認します。
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. 今度は、IPMI タイマーが実行されていて、/dev/watchdog デバイスが sbd によって開かれる必要があります。

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. SBD の状態を確認します。

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. カーネルをクラッシュさせることで SBD フェンスをテストします。

    * カーネルのクラッシュをトリガーします。

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * 実行する 2 番目のテストは、PCS コマンドを使用してノードをフェンスすることです。

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. SAP HANA クラスタリングの残りの部分では、次の設定により STONITH を無効にすることができます:

   * pcs property set `stonith-enabled=false`
   * クラスターのセットアップ中は、システムの予期しない再起動を避けるため、STONITH を非アクティブにしておく方が簡単な場合があります。
   * 運用環境で使用するには、このパラメーターを true に設定する必要があります。 このパラメーターを true に設定しないと、クラスターはサポートされません。
   * pcs property set `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>クラスターへの HANA の統合

このセクションでは、HANA をクラスターに統合します。 このセクションでは、この記事の冒頭で参照されている 2 つの同じホスト `sollabdsm35` と `sollabdsm36` を使用します。

既定のサポートされている方法は、データベースを直接切り替えることができるパフォーマンス最適化シナリオを作成することです。 このドキュメントのこちらでは、このシナリオについてのみ説明します。 この場合、QAS システム用に 1 つのクラスターをインストールし、PRD システム用に別のクラスターをインストールすることをお勧めします。 この場合のみ、運用環境に移行する前にすべてのコンポーネントをテストすることができます。


* このプロセスは、次のページでの RHEL の説明のビルドです。

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>HSR を構成する手順

 | **ログ レプリケーション モード**            | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **メモリ内同期 (既定)** | メモリ内同期 (mode=syncmem) は、ログ書き込みが成功とみなされることを意味します。このとき、ログ エントリがプライマリのログ ボリュームに書き込まれ、メモリへのコピー後にログの送信がセカンダリ インスタンスに認識されます。 セカンダリ システムへの接続が失われると、プライマリ システムはトランザクション処理を継続し、変更をローカル ディスクにのみ書き込みます。 セカンダリ システムが接続されている間にプライマリとセカンダリで同時に障害が発生するまたはセカンダリ システムが切断している間に引き継ぎが実行されると、データ損失が発生する可能性があります。 このオプションを使用すると、セカンダリ インスタンスのディスク入出力を待機する必要がないため、パフォーマンスは向上しますが、データ損失に対して脆弱になります。                                                                                                                                                                                                                                                                                                                     |
| **Synchronous**                     | 同期 (mode=sync) は、ログ書き込みが成功とみなされることを意味します。このとき、ログ エントリはプライマリ インスタンスおよびセカンダリ インスタンスのログ ボリュームに書き込まれます。 セカンダリ システムへの接続が失われると、プライマリ システムはトランザクション処理を継続し、変更をローカル ディスクにのみ書き込みます。 このシナリオでは、セカンダリ システムが接続されている限り、データの損失は発生しません。 セカンダリ システムが切断している間に引き継ぎが実行されると、データ損失が発生する可能性があります。 また、このレプリケーション モードは完全同期オプションを使用して実行できます。 これは、ログ バッファーがプライマリ インスタンスおよびセカンダリ インスタンスのログ ファイルに書き込まれた場合に、ログ書き込みが正常に行われることを意味します。 また、セカンダリ システムが切断されている場合 (たとえば、ネットワーク障害が原因で)、セカンダリ システムへの接続が再確立されるまで、プライマリ システムはトランザクション処理を中断します。 このシナリオでは、データ損失は発生しません。 パラメーター \[system\_replication\]/enable\_full\_sync) を使用している場合のみ、システム レプリケーションに完全同期オプションを設定できます。 完全同期オプションを有効にする方法の詳細については、「システム レプリケーションで完全同期オプションを有効にする」を参照してください。                                                                                                                                                                                                                                                                                                              |
| **非同期**                    | 非同期 (mode = async) は、プライマリシステムが redo ログ バッファーをセカンダリ システムに非同期で送信することを意味します。 プライマリ システムは、プライマリ システムのログ ファイルに書き込みが行われ、ネットワーク経由でセカンダリ システムに送信されたときに、トランザクションをコミットします。 セカンダリ システムからの確認は待機しません。 このオプションを使用すると、セカンダリ システムのログ I/O を待機する必要がないため、パフォーマンスが向上します。 セカンダリ システム上のすべてのサービスでのデータベースの一貫性が保証されます。 ただし、データ損失に対して脆弱になります。 データの変更は、引き継ぎ時に失われる可能性があります。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

1.  ノード 1 (プライマリ) で以下のことを行います。
    1. データベースのログ モードが通常に設定されていることを確認します。

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA システムのレプリケーションは、最初のバックアップが実行された後でのみ機能します。 次のコマンドにより、`/tmp/` ディレクトリに最初のバックアップを作成します。 データベースの適切なバックアップ ファイル システムを選択します。 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. このデータベースのすべてのデータベース コンテナーをバックアップします。
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. ソース システムで HSR プロセスを有効にします。
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. プライマリ システムの状態を確認します。
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. ノード 2 (セカンダリ) で実行するアクションを次に示します。
     1. データベースを停止します。
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. SAP HANA 2.0 の場合のみ、SAP HANA システムの `PKI SSFS_HR2.KEY` および `SSFS_HR2.DAT` ファイルを、プライマリ ノードからセカンダリ ノードにコピーします。
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. レプリケーション サイトとしてセカンダリを有効にします。
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. データベースを起動します。
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. データベースの状態を確認します。
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. レプリケーションの状態に関する詳細情報を取得することもできます。
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>ログ レプリケーション モードの説明

ログ レプリケーション モードの詳細については、[SAP の公式ドキュメント](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html)を参照してください。
  

#### <a name="network-setup-for-hana-system-replication"></a>HANA システム レプリケーションのためのネットワークのセットアップ


レプリケーション トラフィックにとって適切な VLAN がレプリケーションに使用されるようにするには、`global.ini` でそれを適切に構成する必要があります。 この手順を省略すると、HANA によりレプリケーションにアクセス VLAN が使用されますが、これは望ましくない可能性があります。


次の例では、セカンダリ サイトへのシステム レプリケーションのためのホスト名解決の構成を示します。 3 つの異なるネットワークを識別できます。

* アドレスの範囲が 10.0.1.* であるパブリック ネットワーク

* 各サイトのホスト間の内部 SAP HANA 通信のためのネットワーク: 192.168.1.*

* システム レプリケーションの専用ネットワーク: 10.5.1.*

最初の例では、`[system_replication_communication]listeninterface` パラメーターが `.global` に設定されており、隣接するレプリケーション サイトのホストのみが指定されています。

次の例では、`[system_replication_communication]listeninterface` パラメーターが `.internal` に設定されており、両方のサイトのすべてのホストが指定されています。

  

詳細については、「[SAP HANA システム レプリケーションのネットワーク構成](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)」を参照してください。

  

システム レプリケーションの場合、`/etc/hosts` ファイルを編集する必要はありません。システム レプリケーション専用のネットワークを作成するため、内部 ("仮想") ホスト名は `global.ini` ファイル内の IP アドレスにマップされている必要があります。 これに関する構文は次のようになります。

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Pacemaker クラスターで SAP HANA を構成する
このセクションでは、Pacemaker クラスターで SAP HANA を構成する方法について説明します。 このセクションでは、この記事の冒頭で参照されている 2 つの同じホスト `sollabdsm35` と `sollabdsm36` を使用します。

次の前提条件が満たされていることを確認します。  

* Pacemaker クラスターがドキュメントに従って構成されており、適切で動作するフェンスがある

* 開始と停止はクラスターによって管理されるので、すべてのクラスター ノードでブート時の SAP HANA スタートアップが無効になっている

* SAP のツールを使用した SAP HANA システムのレプリケーションと引き継ぎが、クラスター ノード間で適切に動作する

* SAP HANA に、クラスターによって両方のクラスター ノードから使用できる監視アカウントが含まれている

* 両方のノードにより "高可用性" の " RHEL for SAP HANA" (RHEL 6、RHEL 7) チャネルがサブスクライブされている

  

* 一般に、CIB は pcs シェルから自動的に更新されるため、すべての pcs コマンドはノードでのみ実行してください。

* [Quorum ポリシーに関する詳細情報](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>構成する手順 
1. pcs を構成します。
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  corosync を構成します。
    詳細については、[pacemaker と corosync を使用して RHEL 7 高可用性クラスターを構成する方法](https://access.redhat.com/solutions/1293523)に関する記事を参照してください。
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  クローンされた SAPHanaTopology リソースを作成します。
    SAPHanaTopology リソースは、各ノードの SAP HANA システム レプリケーションの状態と構成を収集しています。 SAPHanaTopology では次の属性を構成する必要があります。
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 op start timeout=600 \
       op stop timeout=300 \
       op monitor interval=10 timeout=600 \
       clone clone-max=2 clone-node-max=1 interleave=true

       ```

    | 属性名 | 説明  |
    |---|---|
    | SID | SAP HANA インストールの SAP システム識別子 (SID)。 すべてのノードで同じである必要があります。 |
    | InstanceNumber | 2 桁の SAP インスタンス ID。|

    * リソースの状態
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       Clone: SAPHanaTopology_HR2_00-clone
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat type=SAPHanaTopology)
         Attributes: InstanceNumber=00 SID=HR2
         Operations: monitor interval=60 timeout=60 (SAPHanaTopology_HR2_00-monitor-interval-60)
                     start interval=0s timeout=180 (SAPHanaTopology_HR2_00-start-interval-0s)
                     stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
       
         
       ```

4.  プライマリとセカンダリの SAPHana リソースを作成します。
    * SAPHana リソースは、SAP HANA データベースを開始、停止、および再配置する役割を担います。 このリソースは、プライマリまたはセカンダリのクラスター リソースとして実行する必要があります。 このリソースには次の属性があります。

| 属性名            | 必須 | 既定値 | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | はい       | なし          | SAP HANA インストールの SAP システム識別子 (SID)。 すべてのノードで同じである必要があります。                                                                                                                                                                                                                                                                                                                                                                                       |
| InstanceNumber            | はい       | なし          | 2 桁の SAP インスタンス ID。                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | いいえ        | はい           | クラスターをローカルのプライマリで再起動するのではなく、セカンダリ インスタンスへの切り替えを優先しますか? ("no": ローカルで再起動することを優先します。"yes": リモート サイトへの引き継ぎを優先します)                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | いいえ        | FALSE         | 引き継ぎおよび DUPLICATE_PRIMARY_TIMEOUT の後、SAP HANA の旧プライマリをセカンダリとして登録しますか。 ("false": いいえ。手動介入が必要になります。"true": はい。旧プライマリはリソース エージェントによってセカンダリとして登録されます)                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | いいえ        | 7200          | 二重プライマリ状態が発生した場合、プライマリのタイム スタンプ間に必要な時間差 (秒単位)。 この時間差が時間間隔より小さい場合、クラスターは 1 つまたは両方のインスタンスを "待機中" 状態で保持します。 これは、管理者がフェールオーバーに対応する機会を得られるようにするためです。 障害が発生した旧プライマリは、時間差が経過した後に登録されます。 この新しいプライマリへの登録後に、すべてのデータがシステム レプリケーションによって上書きされます。 |

5.  HANA リソースを作成します。
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=true op start timeout=3600 \
    op stop timeout=3600 \
    op monitor interval=61 role="Slave" timeout=700 \
    op monitor interval=59 role="Master" timeout=700 \
    op promote timeout=3600 \
    op demote timeout=3600 \
    master meta notify=true clone-max=2 clone-node-max=1 interleave=true


    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary
     Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true
     Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)
      Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200 InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2
      Operations: demote interval=0s timeout=320 (SAPHana_HR2_00-demote-interval-0s)
                  monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)
                  monitor interval=121 role=Secondary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-121)
                  monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-119)
                  promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)
                  start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)
                  stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)
   

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  仮想 IP アドレス リソースを作成します。
    クラスターには、SAP HANA のプライマリ インスタンスに接続するための仮想 IP アドレスが含まれるようになります。 次に、IP 10.7.0.84/24 で IPaddr2 リソースを作成するコマンドの例を示します。
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  制約を作成します。
    * 正しい操作を行うには、SAPHana リソースを開始する前に SAPHanaTopology リソースが開始されているようにし、さらに SAPHana のプライマリ リソースが実行されているノード上に仮想 IP アドレスが存在するようにする必要があります。 これを行うには、次の 2 つの制約を作成する必要があります。
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>別のノードへの SAPHana リソースの手動移動のテスト

#### <a name="sap-hana-takeover-by-cluster"></a>(クラスターによる SAP Hana の引き継ぎ)


あるノードから別のノードへの SAPHana リソースの移動をテストするには、次のコマンドを使用します。 SAPHana リソースの内部動作方法のため、次のコマンドを実行するときは、`--primary` オプションを使用しないでください。

`pcs resource move SAPHana_HR2_00-primary`

pcs リソース移動コマンドの各呼び出しの後で、クラスターにより、リソースの移動を実現するための場所の制約が作成されます。 後で、自動フェールオーバーを可能にするには、これらの制約を削除する必要があります。
それらを削除するには、次のコマンドを使用します。

```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* 検証として HANA にログインします。

  * 降格されたホスト:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * 昇格されたホスト:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

`AUTOMATED_REGISTER=false` オプションを使用すると、双方向に切り替えることはできません。

このオプションが false に設定されている場合は、ノードを再登録する必要があります。
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

これで、プライマリであったノード 2 がセカンダリ ホストとして機能するようになります。

降格されたホストの登録を自動化するには、このオプションを true に設定することを検討してください。
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

自動登録を優先するかどうかは顧客のシナリオによって異なります。 運用チームにとっては、引き継ぎ後にノードが自動的に再登録される方が簡単です。 ただし、最初に追加のテストを実行して、すべてが期待どおりに動作することを確認するために、ノードを手動で登録することもできます。

##  <a name="references"></a>参照文献

1. [pacemaker クラスターでの自動 SAP HANA システム レプリケーション (スケールアップ)](https://access.redhat.com/articles/3397471)
2. [RHEL 高可用性クラスターのサポート ポリシ - クラスター内 SAP HANA の管理](https://access.redhat.com/articles/3397471)
3. [Azure での RHELに対する Pacemaker の設定 - Azure 仮想マシン](high-availability-guide-rhel-pacemaker.md)
4. [Azure portal を介した Azure HANA Large Instances の制御 - Azure 仮想マシン](hana-li-portal.md)
