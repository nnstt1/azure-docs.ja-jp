---
title: Azure NetApp Files の Active Directory 接続の作成と管理 | Microsoft Docs
description: この記事では、Azure NetApp Files の Active Directory 接続を作成および管理する方法について説明します。
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
ms.date: 11/02/2021
ms.author: b-juche
ms.openlocfilehash: e05850686fca42a8d21bc477e39171ff792db307
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473834"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Azure NetApp Files の Active Directory 接続の作成と管理

Azure NetApp Files のいくつかの機能では、Active Directory 接続が必要です。  たとえば、[SMB ボリューム](azure-netapp-files-create-volumes-smb.md)、[NFSv4.1 Kerberos ボリューム](configure-kerberos-encryption.md)、または[デュアルプロトコル ボリューム](create-volumes-dual-protocol.md)を作成するには、事前に Active Directory 接続を用意する必要があります。  この記事では、Azure NetApp Files の Active Directory 接続を作成および管理する方法について説明します。

## <a name="before-you-begin"></a>開始する前に  

* あらかじめ容量プールを設定しておく必要があります。 「[容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。   
* サブネットが Azure NetApp Files に委任されている必要があります。 「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="requirements-and-considerations-for-active-directory-connections"></a><a name="requirements-for-active-directory-connections"></a>Active Directory 接続の要件と考慮事項

* Active Directory (AD) 接続を構成できるのは、サブスクリプションごとおよびリージョンごとに 1 つだけです。   

    Azure NetApp Files は、AD 接続が異なる NetApp アカウントにある場合でも、1 つの "*リージョン*" で複数の AD 接続をサポートしていません。 ただし、AD 接続が異なるリージョンにある場合は、1 つのサブスクリプションで複数の AD 接続を使用できます。 1 つのリージョンに複数の AD 接続が必要な場合は、別のサブスクリプションを使用してこれを行うことができます。  

    AD 接続は、それが作成された NetApp アカウントを介してのみ表示されます。 ただし、共有 AD 機能を有効にすると、同じサブスクリプションおよび同じリージョン内の NetApp アカウントが、いずれかの NetApp アカウントで作成された AD サーバーを使用できるようになります。 「[同じサブスクリプションとリージョンにある複数の NetApp アカウントを AD 接続にマップする](#shared_ad)」を参照してください。 この機能を有効にすると、同じサブスクリプションおよび同じリージョンにあるすべての NetApp アカウントの AD 接続を確認できるようになります。 

* 使用する管理者アカウントは、指定する組織単位 (OU) パスにマシン アカウントを作成できる必要があります。  

* Azure NetApp Files で使用されている Active Directory ユーザー アカウントのパスワードを変更する場合は必ず、[[Active Directory 接続]](#create-an-active-directory-connection) で構成されているパスワードを更新してください。 そうしないと、新しいボリュームを作成できなくなり、セットアップによっては既存のボリュームへのアクセスも影響を受ける場合があります。  

* 適切なポートは、該当する Windows Active Directory (AD) のサーバーで開く必要があります。  
    必要なポートは次のとおりです。 

    |     サービス           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD Web サービス    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    該当なし       |    エコー応答    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 名       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* ターゲットの Active Directory Domain Services のサイト トポロジは、特に Azure NetApp Files がデプロイされる Azure VNet ではガイドラインに従う必要があります。  

    (Azure NetApp Files によって到達可能なドメイン コントローラーが存在する) 新規または既存の Active Directory サイト に、Azure NetApp Files がデプロイされる仮想ネットワークのアドレス空間を追加する必要があります。 

* Azure NetApp Files の[委任されたサブネット](./azure-netapp-files-delegate-subnet.md)から、指定された DNS サーバーに到達可能である必要があります。  

    サポートされるネットワーク トポロジについては、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。

    ネットワーク セキュリティ グループ (NSG) とファイアウォールに、Active Directory と DNS トラフィック要求を許可するように適切に構成された規則が必要です。 

* Azure NetApp Files の委任されたサブネットは、すべてのローカルおよびリモート ドメイン コントローラーを含め、ドメイン内のすべての Active Directory Domain Services (ADDS) ドメイン コントローラーに接続できる必要があります。 そうでない場合、サービスの中断が発生する可能性があります。  

    Azure NetApp Files の委任されたサブネットで到達できないドメイン コントローラーがある場合は、Active Directory 接続の作成中に Active Directory サイトを指定できます。  Azure NetApp Files は、Azure NetApp Files の委任されたサブネットのアドレス空間が存在するサイト内のドメイン コントローラーとのみ通信する必要があります。

    AD サイトとサービスに関する「[サイト トポロジの設計](/windows-server/identity/ad-ds/plan/designing-the-site-topology)」を参照してください。 
    
* AD 認証の AES 暗号化を有効にするには、[[Active Directory に参加する]](#create-an-active-directory-connection) ウィンドウの **[AES の暗号化]** ボックスをオンにします。 Azure NetApp Files では、暗号化の種類として DES、Kerberos AES 128、および Kerberos AES 256 がサポートされています (安全性の最も低いものから安全性の最も高いものまで)。 AES の暗号化を有効にする場合、Active Directory に参加するのに使用するユーザー資格情報では、ご利用の Active Directory で有効になっている機能と一致するアカウント オプションの中で最上位のものが有効になっている必要があります。    

    たとえば、ご利用の Active Directory に AES-128 機能のみが含まれている場合は、ユーザー資格情報に対して AES-128 アカウント オプションを有効にする必要があります。 ご利用の Active Directory に AES-256 機能が含まれている場合は、AES-256 アカウント オプション (AES-128 もサポートしている) を有効にする必要があります。 Active Directory に Kerberos 暗号化機能が含まれていない場合、Azure NetApp Files では既定で DES が使用されます。  

    アカウント オプションは、[Active Directory ユーザーとコンピューター] Microsoft 管理コンソール (MMC) のプロパティで有効にすることができます。   

    ![Active Directory ユーザーとコンピューター MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files では [LDAP 署名](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server)がサポートされているため、Azure NetApp Files サービスと、ターゲットとなる [Active Directory ドメイン コントローラー](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)との間で LDAP トラフィックを安全に転送することができます。 LDAP 署名に関する Microsoft アドバイザリ [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) のガイダンスに従っている場合は、Azure NetApp Files の LDAP 署名機能を有効にする必要があります。そのためには [[Active Directory に参加する]](#create-an-active-directory-connection) ウィンドウで **[LDAP 署名]** ボックスをオンにします。 

    [LDAP チャネル バインド](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry)構成が単体で Azure NetApp Files サービスに影響することはありません。 ただし、LDAP チャネル バインドとセキュリティで保護された LDAP (LDAPS や `start_tls`など) の両方を使用すると、SMB ボリュームの作成は失敗します。

* AD 統合 DNS ではない場合、"フレンドリ名" を使用して Azure NetApp Files が機能するようにするには、DNS A/PTR レコードを追加する必要があります。 

* 次の表では、LDAP キャッシュの Time to Live (TTL) 設定について説明します。 クライアントを使用してファイルまたはディレクトリにアクセスしようとする前に、キャッシュが更新されるまで待機する必要があります。 そうでないと、アクセスまたはアクセス許可の拒否メッセージがクライアントに表示されます。 

    |     エラー状態    |     解像度    |
    |-|-|
    | キャッシュ |  [Default Timeout]\(既定のタイムアウト\) |
    | グループ メンバーシップの一覧  | 24 時間の TTL  |
    | Unix グループ  | 24 時間の TTL、1 分の負の TTL  |
    | Unix ユーザー  | 24 時間の TTL、1 分の負の TTL  |

    キャッシュには、*Time to Live* と呼ばれる特定のタイムアウト期間があります。 タイムアウト期間が経過すると、古いエントリが残らないようにエントリが期限切れになります。 *負の TTL* の値は、存在しない可能性があるオブジェクトの LDAP クエリに起因するパフォーマンスの問題を回避するために、失敗した参照が存在する場所です。   

## <a name="decide-which-domain-services-to-use"></a>使用するドメイン サービスを決定する 

Azure NetApp Files では、AD 接続用に [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) と Azure Active Directory Domain Services (AADDS) の両方がサポートされています。  AD 接続を作成する前に、ADDS と AADDS のどちらを使用するかを決定する必要があります。  

詳しくは、「[自己管理型の Active Directory Domain Services、Azure Active Directory、およびマネージド Azure Active Directory Domain Services の比較](../active-directory-domain-services/compare-identity-solutions.md)」を参照してください。 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Azure NetApp Files では、 [[Active Directory サイトとサービス]](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) の任意のスコープを使用できます。 このオプションを使用すると、[Azure NetApp Files によってアクセス可能な](azure-netapp-files-network-topologies.md) Active Directory Domain Services (ADDS) ドメイン コントローラーに対する読み取りと書き込みが可能になります。 また、指定された [Active Directory サイトとサービス] サイトに存在しないドメイン コントローラーとサービスが通信できなくなります。 

ADDS の使用時にサイト名を確認するには、Active Directory Domain Services を担当する組織内の管理グループに問い合わせてください。 次の例は、サイト名が表示されている [Active Directory サイトとサービス] プラグインを示しています。 

![Active Directory サイトとサービス](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Azure NetApp Files の AD 接続を構成するときに、 **[AD サイト名]** フィールドのスコープにサイト名を指定します。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services (AADDS) の構成とガイダンスについては、「[Azure AD Domain Services のドキュメント](../active-directory-domain-services/index.yml)」を参照してください。

Azure NetApp Files では、以下の追加の考慮事項が適用されます。 

* AADDS がデプロイされている VNet またはサブネットが、Azure NetApp Files デプロイと同じ Azure リージョンにあることを確認してください。
* Azure NetApp Files がデプロイされているリージョンで別の VNet を使用する場合は、2 つの VNet 間のピアリングを作成する必要があります。
* Azure NetApp Files では、`user` と `resource forest` の種類がサポートされています。
* 同期の種類では、`All` または `Scoped` を選択できます。   
    `Scoped` を選択した場合、SMB 共有にアクセスするための正しい Azure AD グループが選択されていることを確認してください。  不明な場合は、`All` の同期の種類を使用できます。
* デュアルプロトコル ボリュームで AADDS を使用する場合、POSIX 属性を適用するにはカスタム OU 内にいる必要があります。 詳細については、「[LDAP POSIX 属性を管理する](create-volumes-dual-protocol.md#manage-ldap-posix-attributes)」を参照してください。

Active Directory 接続を作成する場合は、次のような AADDS の詳細を確認してください。

* AADDS メニューで **プライマリ DNS**、**セカンダリ DNS**、**AD DNS ドメイン名** の情報を確認できます。  
DNS サーバーでは、Active Directory 接続を構成する際に 2 つの IP アドレスが使用されます。 
* **組織単位パス** は `OU=AADDC Computers` です。  
この設定は、 **[NetApp アカウント]** の下の **[Active Directory 接続]** で構成されます。

  ![組織単位パス](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **[ユーザー名]** 資格情報には、Azure AD グループの **Azure AD DC 管理者** のメンバーである任意のユーザーを設定できます。


## <a name="create-an-active-directory-connection"></a>Active Directory 接続を作成する

1. NetApp アカウントで **[Active Directory 接続]** をクリックし、 **[参加]** をクリックします。  

    Azure NetApp Files でサポートされるのは、同じリージョンおよび同じサブスクリプション内で 1 つの Active Directory 接続のみです。 Active Directory が同じサブスクリプションおよびリージョン内の別の NetApp アカウントによって既に構成されている場合、お使いの NetApp アカウントで異なる Active Directory を構成して参加することはできません。 ただし、共有 AD 機能を有効にすると、同じサブスクリプションと同じリージョン内の複数の NetApp アカウントで Active Directory 構成を共有できるようになります。 「[同じサブスクリプションとリージョンにある複数の NetApp アカウントを AD 接続にマップする](#shared_ad)」を参照してください。

    ![Active Directory 接続](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. [Active Directory に参加します] ウィンドウで、使用するドメイン サービスに基づいて、次の情報を指定します。  

    使用するドメイン サービスに固有の情報については、「[使用するドメイン サービスを決定する](#decide-which-domain-services-to-use)」を参照してください。 

    * **プライマリ DNS**  
        これは、Active Directory ドメイン参加と SMB 認証操作に必要な DNS です。 
    * **セカンダリ DNS**   
        これは、冗長ネーム サービスを確保するためのセカンダリ DNS サーバーです。 
    * **AD DNS ドメイン名**  
        これは、参加させる Active Directory Domain Services のドメイン名です。
    * **AD サイト名**  
        これは、ドメイン コントローラーの検出が制限されるサイト名です。 これは Active Directory のサイトとサービスのサイト名に一致している必要があります。
    * **SMB サーバー (コンピューター アカウント) プレフィックス**  
        これは、Azure NetApp Files で新しいアカウントの作成に使用される Active Directory のコンピューター アカウントの命名規則プレフィックスです。

        たとえば、ファイル サーバーに対して組織が使用する命名規則標準が NAS-01, NAS-02..., NAS-045 であれば、プレフィックスとして「NAS」を入力します。 

        サービスによって、必要に応じて、Active Directory で追加のコンピューター アカウントが作成されます。

        > [!IMPORTANT] 
        > Active Directory 接続を作成した後に SMB サーバー プレフィックスの名前を変更すると混乱が生じます。 SMB サーバー プレフィックスの名前を変更したら、既存の SMB 共有を再マウントする必要があります。

    * **組織単位名**  
        これは、SMB サーバー コンピューター アカウントが作成される組織単位 (OU) の LDAP パスです。 つまり、OU=second level, OU=first level です。 

        Azure Active Directory Domain Services と組み合わせて Azure NetApp Files を使用している場合、NetApp アカウント用に Active Directory を構成する際の組織単位のパスは `OU=AADDC Computers` になります。

        ![Active Directory に参加する](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES の暗号化**   
        AD 認証で AES 暗号化を有効にする場合、または [SMB ボリュームの暗号化](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)が必要な場合は、このチェックボックスをオンにします。   
        
        要件については、「[Active Directory 接続の要件](#requirements-for-active-directory-connections)」を参照してください。  
  
        ![Active Directory の AES の暗号化](../media/azure-netapp-files/active-directory-aes-encryption.png)

        **AES の暗号化** 機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、使用する前に機能を登録してください。 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

    * **LDAP 署名**   
        このチェックボックスをオンにすると、LDAP 署名が有効になります。 この機能により、Azure NetApp Files サービスと、ユーザー指定の [Active Directory Domain Services ドメイン コントローラー](/windows/win32/ad/active-directory-domain-services)との間で、セキュリティで保護された LDAP 参照が可能になります。 詳細については、「[ADV190023 | LDAP チャネル バインディングと LDAP 署名を有効にするためのマイクロソフト ガイダンス](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023)」を参照してください。  

        ![Active Directory の LDAP 署名](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        **LDAP 署名** 機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、使用する前に機能を登録してください。 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

     * **セキュリティ特権ユーザー**   <!-- SMB CA share feature -->   
        Azure NetApp Files ボリュームにアクセスするための昇格された特権を必要とするユーザーには、セキュリティ特権 (`SeSecurityPrivilege`) を付与できます。 指定されたユーザー アカウントは、ドメイン ユーザーに対して既定では割り当てられていないセキュリティ特権を必要とする Azure NetApp Files SMB 共有に対して特定のアクションを実行できます。   

        たとえば、特定のシナリオで SQL Server のインストールに使用するユーザー アカウントには、昇格されたセキュリティ特権が付与されている必要があります。 SQL Server のインストールに管理者以外の (ドメイン) アカウントを使用していて、アカウントにセキュリティ特権が割り当てられていない場合、そのアカウントにセキュリティ特権を追加する必要があります。  

        > [!IMPORTANT]
        > **[Security privilege users]\(セキュリティ特権ユーザー\)** 機能を使用するには、 **[[Azure NetApp Files SMB Continuous Availability Shares Public Preview]\(Azure NetApp Files の SMB の継続的な可用性の共有パブリック プレビュー\) 順番待ち送信ページ](https://aka.ms/anfsmbcasharespreviewsignup)** から、順番待ち要求を送信する必要があります。 この機能を使用する前に、Azure NetApp Files チームから正式な確認メールが届くのをお待ちください。        
        > 
        > この機能の使用はオプションであり、SQL Server でのみサポートされています。 SQL Server のインストールに使用するドメイン アカウントは、 **[Security privilege users]\(セキュリティ特権ユーザー\)** フィールドに追加する前に既に作成されている必要があります。 SQL Server インストーラーのアカウントを **[Security privilege users]\(セキュリティ特権ユーザー\)** に追加するときに、ドメイン コントローラーに接続することで Azure NetApp Files サービスによってアカウントが検証される場合があります。 ドメイン コントローラーに接続できないと、コマンドが失敗するおそれがあります。  

        `SeSecurityPrivilege` と SQL Server の詳細については、「[セットアップ アカウントに特定のユーザー権限がない場合に SQL Server のインストールが失敗する](/troubleshoot/sql/install/installation-fails-if-remove-user-right)」を参照してください。

        ![Active Directory 接続ウィンドウの [Security privilege users]\(セキュリティ特権ユーザー\) ボックスを示すスクリーンショット。](../media/azure-netapp-files/security-privilege-users.png) 

     * **バックアップ ポリシー ユーザー**  
        Azure NetApp Files で使用するために作成されたコンピューター アカウントに対して昇格された特権を必要とする追加のアカウントを含めることができます。 指定したアカウントは、ファイルまたはフォルダー レベルで NTFS アクセス許可を変更できます。 たとえば、Azure NetApp Files の SMB ファイル共有にデータを移行するために使用される非特権サービス アカウントを指定できます。  

        ![Active Directory バックアップ ポリシーユーザー](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        **バックアップ ポリシー ユーザー** 機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、使用する前に機能を登録してください。 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。  

    * **Administrators** 

        ボリュームに対する管理者権限が付与されるユーザーまたはグループを指定できます。 

        ![[Active Directory 接続] ウィンドウの [管理者] ボックスを示すスクリーンショット。](../media/azure-netapp-files/active-directory-administrators.png) 
        
        現在、 **[管理者]** 機能はプレビュー段階にあります。 この機能を初めて使用する場合は、使用する前に機能を登録してください。 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAdAdministrators
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAdAdministrators
        ```
        
        また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

    * **ユーザー名** や **パスワード** などの資格情報

        ![Active Directory の資格情報](../media/azure-netapp-files/active-directory-credentials.png)

3. **[結合]** をクリックします。  

    作成した Active Directory の接続が表示されます。

    ![作成された Active Directory 接続](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="map-multiple-netapp-accounts-in-the-same-subscription-and-region-to-an-ad-connection"></a><a name="shared_ad"></a>同じサブスクリプションとリージョンにある複数の NetApp アカウントを AD 接続にマップする  

共有 AD 機能を使用すると、同じサブスクリプションで同じリージョンに属する NetApp アカウントのいずれかによって作成された Active Directory (AD) 接続を、すべての NetApp アカウントで共有できます。 たとえば、この機能を使用すると、サブスクリプションとリージョンが同じであるすべての NetApp アカウントに共通の AD 構成を使用して、[SMB ボリューム](azure-netapp-files-create-volumes-smb.md)、[NFSv4.1 Kerberos ボリューム](configure-kerberos-encryption.md)、または[デュアルプロトコル ボリューム](create-volumes-dual-protocol.md)を作成できます。 この機能を使用すると、同じサブスクリプションで同じリージョンであるすべての NetApp アカウントの AD 接続を確認できるようになります。   

現在、この機能はプレビュー段階にあります。 初めて使用する前に、機能を登録する必要があります。 登録が完了すると、機能が有効になり、バックグラウンドで動作します。 UI コントロールは必要ありません。 

1. 機能を登録します。 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```

2. 機能の登録の状態を確認します。 

    > [!NOTE]
    > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が **Registered** になってから続行してください。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```
また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 
 
## <a name="next-steps"></a>次のステップ  

* [SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)
* [デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)
* [NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)
* [Azure CLI を使用して新しい Active Directory フォレストをインストールする](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
