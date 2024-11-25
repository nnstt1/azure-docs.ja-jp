---
title: Azure NetApp Files 用に NFS クライアントを構成する | Microsoft Docs
description: Azure NetApp Files で使用するために NFS クライアントを構成する方法について説明します。
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
ms.date: 09/22/2021
ms.author: b-juche
ms.openlocfilehash: 6c946ed17d08a24e41f304c268c874ed7082691c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062711"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files 用に NFS クライアントを構成する

この記事で説明する NFS クライアントの構成は、[NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)または[デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)際のセットアップの一環です。 Azure NetApp Files では、さまざまな Linux ディストリビューションを使用できます。 この記事では、一般的によく使用される RHEL 8 と Ubuntu 18.04 の 2 つの環境の構成について説明します。 

## <a name="requirements-and-considerations"></a>要件と考慮事項  

使用する Linux フレーバーに関係なく、次の構成が必要です。

* 時間のずれに関する問題を回避するように NTP クライアントを構成します。
* 名前解決を行うように Linux クライアントの DNS エントリを構成します。  
    この構成には、"A" (前方) レコードと PTR (逆引き) レコードが含まれている必要があります。 
* ドメイン参加の場合は、対象の Active Directory に Linux クライアント用のコンピューター アカウントを作成します (これは、realm join コマンドの実行時に作成されます)。 
    > [!NOTE] 
    > 以下のコマンドで使用される `$SERVICEACCOUNT` 変数は、対象となる組織単位でコンピューター アカウントを作成するためのアクセス許可または委任を持つユーザー アカウントである必要があります。

## <a name="rhel-8-configuration"></a>RHEL 8 の構成 

このセクションでは、NFSv4.1 Kerberos 暗号化とデュアル プロトコルに必要な RHEL 構成について説明します。  

このセクションの例では、次のドメイン名と IP アドレスを使います。  

* ドメイン名: `contoso.com`
* プライベート IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>NFSv4.1 の Kerberos 暗号化を使用している場合の RHEL 8 構成

1. 適切な DNS サーバーを使って `/etc/resolv.conf` を構成します。  

    次に例を示します。  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. DNS 前方および逆引き参照ゾーンの DNS サーバーに NFS クライアント レコードを追加します。

3. DNS を確認するには、NFS クライアントから次のコマンドを使います。   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. パッケージをインストールします。   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  NTP クライアントを構成します。  

    RHEL 8 では、既定で chrony が使用されます。 「[`Chrony` スイートを使用した NTP の設定](https://access.redhat.com/documentation/en-us/red-hat-enterprise-linux/8/guide/6c230de2-39f1-455a-902d-737eea31ad34)」の構成ガイドラインに従います。

6.  Active Directory ドメインに参加します。  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    次に例を示します。 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    `/etc/krb5.conf` 内の指定された領域に `default_realm` が確実に設定されていようにします。  そうでない場合は、次の例に示すように、ファイル内の `[libdefaults]` セクションの下に追加します。
    
    ```
    [libdefaults]
        default_realm = CONTOSO.COM
        default_tkt_enctypes = aes256-cts-hmac-sha1-96
        default_tgs_enctypes = aes256-cts-hmac-sha1-96
        permitted_enctypes = aes256-cts-hmac-sha1-96
    [realms]
        CONTOSO.COM = {
            kdc = dc01.contoso.com
            admin_server = dc01.contoso.com
            master_kdc = dc01.contoso.com
            default_domain = contoso.com
        }
    [domain_realm]
        .contoso.com = CONTOSO.COM
        contoso.com = CONTOSO.COM
    [logging]
        kdc = SYSLOG:INFO
        admin_server = FILE=/var/kadm5.log
    ```

7. すべての NFS サービスを再起動します。  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    再起動することで、Kerberos のマウント中にエラー状態 `“mount.nfs: an incorrect mount option was specified”` が発生しなくなります。

8. チケットを取得するユーザー アカウントを使って `kinit` コマンドを実行します。 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    次に例を示します。   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>デュアル プロトコルを使用している場合の RHEL 8 構成

以下の手順は省略可能です。 NFS クライアントでユーザー マッピングを使用する場合にのみ、この手順を実行する必要があります。 

1. 「[NFSv4.1 の Kerberos 暗号化を使用している場合の RHEL 8 構成](#rhel8_nfsv41_kerberos)」セクションで説明されているすべての手順を完了します。   

2. SSSD 構成ファイルの IP アドレスを使う代わりに、AD の完全修飾ドメイン名 (FQDN) を使うように、/etc/hosts ファイルに静的 DNS レコードを追加します。  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. AD LDAP サーバーからの識別子を解決するために、ドメインにセクションを追加します。    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
    
    上記の `[domain/contoso-ldap]` 構成では、次のようになります。
    * `id_provider` は `ad` ではなく、`ldap` に設定されています。
    * 構成では、検索ベースと、検索用のユーザー クラスおよびグループ クラスを指定しています。
    * `ldap_sasl_authid` は、`klist -kte` からのコンピューター アカウント名です。
    * `use_fully_qualified_names` は `false` に設定されます。  この設定は、短い名前の使用時に、この構成が使用されることを意味します。
    * `ldap_id_mapping` は指定されていません。その既定値は `false` です。

    `realm join` 構成はクライアントによって生成され、次のようになります。
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   
    
    上記の `[domain/contoso.com]` 構成では、次のようになります。
    * `id_provider` が `ad` に設定されます。
    * `ldap_id_mapping` が `true` に設定されます。 SSSD によって生成された ID が使用されます。 あるいは、すべてのスタイルのユーザー名に POSIX UID を使用する場合は、この値を `false` に設定することもできます。 値は、クライアント構成に基づいて決定できます。 
    * `use_fully_qualified_names` は `true` です。 この設定は、`user@CONTOSO.COM` で、この構成が使用されることを意味しています。

4. `/etc/nsswitch.conf` に `sss` エントリがあることを確認します。   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. `sssd` サービスを再起動し、キャッシュをクリアします。   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. テストを実施して、クライアントが LDAP サーバーと統合されていることを確認します。   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu の構成   

このセクションでは、NFSv4.1 Kerberos 暗号化とデュアル プロトコルに必要な Ubuntu 構成について説明します。 

このセクションの例では、次のドメイン名と IP アドレスを使います。  

* ドメイン名: `contoso.com`
* プライベート IP: `10.6.1.4`

1. 適切な DNS サーバーを使って `/etc/resolv.conf` を構成します。

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. DNS 前方および逆引き参照ゾーンの DNS サーバーに NFS クライアント レコードを追加します。
 
    DNS を確認するには、NFS クライアントから次のコマンドを使います。

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. パッケージをインストールします。
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    プロンプトが表示されたら、既定の Kerberos 領域として `$DOMAIN.NAME` (`CONTOSO.COM` のように大文字を使用) を入力します。

4. サービス `rpc-gssd.service` を再起動します。 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18.04 では、既定で chrony が使用されます。 「[Ubuntu Bionic: Chrony を使用して NTP を構成する](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)」の構成ガイドラインに従います。

6. Active Directory ドメインに参加します。   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    次に例を示します。    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. チケットを取得するユーザーとして `kinit` を実行します。 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    次に例を示します。    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>デュアル プロトコルを使用している場合の Ubuntu 構成  

以下の手順は省略可能です。  NFS クライアントでユーザー マッピングを使用する場合にのみ、この手順を実行する必要があります。  

1. 次のコマンドを実行して、インストール済みパッケージをアップグレードします。   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    次の例では、サンプル値を使用します。 コマンドで入力を求められたら、環境に基づいて入力を指定する必要があります。 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. `/etc/nsswitch.conf` ファイルに次の `ldap` エントリが含まれていることを確認します。   
    `passwd:    compat systemd ldap`   
    `group:     compat systemd ldap`

3. 次のコマンドを実行して、サービスを再起動して有効にします。

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

次の例では、LDAP ユーザー `‘hari1’` について、Ubuntu LDAP クライアントから AD LDAP サーバーに照会します。 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes"></a>ホスト名が同じ 2 つの VM が NFSv 4.1 ボリュームにアクセスするように構成する 

このセクションでは、ホスト名が同じ 2 つの VM が Azure NetApp Files NFSv 4.1 ボリュームにアクセスするように構成する方法について説明します。 この手順は、ディザスター リカバリー (DR) テストを実施するとき、テスト システムのホスト名をプライマリ DR システムのホスト名と同じにする必要がある場合に役立ちます。 この手順は、同じ Azure NetApp Files ボリュームにアクセスしている 2 つの VM 上のホスト名が同じときにのみ必要です。  

NFSv4. x では、各クライアントが "*一意*" の文字列を使用して、サーバーに対して自身を特定する必要があります。 1 つのクライアントと 1 つのサーバーの間で共有されるファイルのオープンおよびロック状態は、この ID に関連付けられています。 堅牢な NFSv4. x 状態回復と透過的な状態移行をサポートするには、この ID 文字列が、クライアントの再起動後も変更されないようにする必要があります。

1. 次のコマンドを使用して、VM クライアント上に `nfs4_unique_id` 文字列を表示します。
    
    `# systool -v -m nfs | grep -i nfs4_unique`     
    `    nfs4_unique_id      = ""`

    DR システムなど、ホスト名が同じ追加 VM 上で同じボリュームをマウントするには、`nfs4_unique_id` を作成して、Azure NetApp Files NFS サービスに対して自身を一意に特定できるようにします。  この手順を使用すると、サービスが、同じホスト名の 2 つの VM を区別して、両方の VM 上で NFSv 4.1 ボリュームのマウントを有効にできます。  

    この手順は、テスト DR システム上でのみ実行する必要があります。 一貫性を確保するために、関連する各仮想マシン上で一意の設定の適用を検討することができます。

2. テスト DR システム上で、次の行を `nfsclient.conf` ファイルに追加します。このファイルは通常、`/etc/modprobe.d/` 内にあります。

    `options nfs nfs4_unique_id=uniquenfs4-1`  

    文字列 `uniquenfs4-1` は、サービスに接続する VM 全体で一意である限り、任意の英数字の文字列にできます。

    NFS クライアント設定の構成方法については、ディストリビューションのドキュメントをご確認ください。

    変更を有効にするために VM を再起動します。

3. テスト DR システム上で、`nfs4_unique_id` が、VM が再起動された後も設定されていることを確認します。       

    `# systool -v -m nfs | grep -i nfs4_unique`   
    `   nfs4_unique_id      = "uniquenfs4-1"`   

4. 通常どおり、両方の VM 上に [NFSv 4.1 ボリュームをマウント](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)します。

    これでホスト名が同じ両方の VM が NFSv 4.1 ボリュームをマウントし、アクセスできるようになります。  

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)
* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 
