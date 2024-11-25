---
title: Azure AD での Azure AD Connect クラウド同期の前提条件
description: この記事では、クラウド同期に必要な前提条件とハードウェア要件について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31850f855cdf109e2337898736d273237ff65058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018203"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期の前提条件
この記事では、ID ソリューションとして Azure Active Directory (Azure AD) クラウド同期を選択して使用する方法に関するガイダンスを示します。

## <a name="cloud-provisioning-agent-requirements"></a>クラウド プロビジョニング エージェントの要件
Azure AD Connect クラウド同期を使用するには、次のものが必要です。

- エージェント サービスを実行する Azure AD Connect Cloud Sync gMSA (グループ管理サービス アカウント) を作成するためのドメイン管理者またはエンタープライズ管理者の資格情報。 
- ゲスト ユーザーではない、Azure AD テナントのハイブリッド ID 管理者アカウント。
- Windows 2016 以降を搭載した、プロビジョニング エージェント用のオンプレミス サーバー。  このサーバーは、[Active Directory 管理層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)に基づいた階層 0 のサーバーである必要があります。  ドメイン コントローラーへのエージェントのインストールがサポートされています。
- 高可用性とは、クラウド同期Azure AD Connectクラウド同期長時間障害なく継続的に動作する機能を指します。  複数のアクティブなエージェントをインストールして実行することで、1つのエージェントで障害が発生した場合でも、Azure AD Connect クラウド同期を引き続き機能させることができます。  Microsoft では、高可用性のために3つのアクティブなエージェントをインストールすることをお勧めします。
- オンプレミスのファイアウォールの構成

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
グループ管理サービス アカウントは、パスワードの自動管理、簡略化されたサービス プリンシパル名 (SPN) の管理、管理を他の管理者に委任する機能を提供し、またこの機能を複数のサーバーに拡張する、マネージド ドメイン アカウントです。  Azure AD Connect Cloud Sync では、エージェントを実行するための gMSA がサポートされ、使用されています。  このアカウントを作成するために、セットアップ中に管理者資格情報の入力を求められます。  このアカウントは、(domain\provAgentgMSA$) として表示されます。  gMSA の詳細については、[グループ管理サービス アカウント](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)に関するページを参照してください。 

### <a name="prerequisites-for-gmsa"></a>gMSA の前提条件:
1.  gMSA ドメインのフォレスト内の Active Directory スキーマを Windows Server 2012 以降に更新する必要があります。
2.  ドメイン コントローラー上の [PowerShell RSAT モジュール](/windows-server/remote/remote-server-administration-tools)
3.  ドメイン内の少なくとも 1 つのドメイン コントローラーで Windows Server 2012 以降が実行されている必要があります。
4.  エージェントをインストールするドメイン参加済みサーバーは、Windows Server 2016 以降である必要があります。

### <a name="custom-gmsa-account"></a>カスタムの gMSA アカウント
カスタムの gMSA アカウントを作成する場合は、アカウントに次のアクセス許可があることを確実にする必要があります。

|Type |名前 |アクセス |適用対象| 
|-----|-----|-----|-----|
|Allow |gMSA アカウント |すべてのプロパティの読み取り |デバイスの子孫オブジェクト| 
|Allow |gMSA アカウント|すべてのプロパティの読み取り |InetOrgPerson の子孫オブジェクト| 
|Allow |gMSA アカウント |すべてのプロパティの読み取り |コンピューターの子孫オブジェクト| 
|Allow |gMSA アカウント |すべてのプロパティの読み取り |foreignSecurityPrincipal の子孫オブジェクト| 
|Allow |gMSA アカウント |フル コントロール |グループの子孫オブジェクト| 
|Allow |gMSA アカウント |すべてのプロパティの読み取り |ユーザーの子孫オブジェクト| 
|Allow |gMSA アカウント |すべてのプロパティの読み取り |連絡先の子孫オブジェクト| 
|Allow |gMSA アカウント |ユーザー オブジェクトの作成または削除|このオブジェクトとすべての子孫オブジェクト| 

gMSA アカウントを使用するように既存のエージェントをアップグレードする手順については、「[グループ管理サービス アカウント](how-to-install.md#group-managed-service-accounts)」を参照してください。

#### <a name="create-gmsa-account-with-powershell"></a>PowerShell と アカウントgMSAを作成する
次の PowerShell スクリプトを使用して、カスタム gMSA アカウントを作成できます。  その後、 [cloud sync の gMSA コマンドレット](how-to-gmsa-cmdlets.md) を使用して、より詳細なアクセス許可を適用できます。

```powershell
# Filename:    1_SetupgMSA.ps1
# Description: Creates and installs a custom gMSA account for use with Azure AD Connect cloud sync.
#
# DISCLAIMER:
# Copyright (c) Microsoft Corporation. All rights reserved. This 
# script is made available to you without any express, implied or 
# statutory warranty, not even the implied warranty of 
# merchantability or fitness for a particular purpose, or the 
# warranty of title or non-infringement. The entire risk of the 
# use or the results from the use of this script remains with you.
#
#
#
#
# Declare variables
$Name = 'provAPP1gMSA'
$Description = "Azure AD Cloud Sync service account for APP1 server"
$Server = "APP1.contoso.com"
$Principal = Get-ADGroup 'Domain Computers'

# Create service account in Active Directory
New-ADServiceAccount -Name $Name `
-Description $Description `
-DNSHostName $Server `
-ManagedPasswordIntervalInDays 30 `
-PrincipalsAllowedToRetrieveManagedPassword $Principal `
-Enabled $True `
-PassThru

# Install the new service account on Azure AD Cloud Sync server
Install-ADServiceAccount -Identity $Name
```

上記のコマンドレットの詳細については、「グループの管理され [たサービスアカウントを使用したはじめに](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)?redirectedfrom=MSDN)」を参照してください。

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センター

1. Azure AD テナントで、クラウド専用のハイブリッド ID 管理者アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなった場合にテナントの構成を管理できます。 [クラウド専用のハイブリッド ID 管理者アカウントを追加する](../fundamentals/add-users-azure-active-directory.md)方法について確認してください。 テナントからロックアウトされないようにするには、この手順を実行する必要があります。
1. 1 つ以上の[カスタム ドメイン名](../fundamentals/add-custom-domain.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してサインインできます。

### <a name="in-your-directory-in-active-directory"></a>Active Directory のディレクトリ内

[IdFix ツール](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)を実行して、同期用にディレクトリ属性を準備します。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. 4 GB 以上の RAM と .NET 4.7.1 以降のランタイムを搭載した、Windows Server 2016 以降が実行されているドメイン参加済みホスト サーバーを特定します。

2. ローカル サーバーの PowerShell 実行ポリシーを、Undefined または RemoteSigned に設定する必要があります。

3. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成します。
    - エージェントが次のポートを介して Azure AD に "*送信*" 要求を発行できるようにします。

      | ポート番号 | 用途 |
      | --- | --- |
      | **80** | TLS/SSL 証明書を検証する際に、証明書失効リスト (CRL) をダウンロードします。  |
      | **443** | サービスを使用したすべての送信方向の通信を処理します。 |
      | **8080** (省略可能) | ポート 443 が使用できない場合、エージェントは、ポート 8080 経由で 10 分おきにその状態をレポートします。 この状態は Azure AD ポータルに表示されます。 |

    - ご利用のファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。
    - ファイアウォールまたはプロキシで安全なサフィックスの指定が許可されている場合は、\*.msappproxy.net および \*.servicebus.windows.net への接続を追加します。 そうでない場合は、毎週更新される [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可します。
    - エージェントは、初期登録のために login.windows.net と login.microsoftonline.com にアクセスする必要があります。 これらの URL にもファイアウォールを開きます。
    - 証明書の検証のために、URL mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80、www\.microsoft.com:80 のブロックを解除します。 他の Microsoft 製品でもこれらの URL を証明書の検証に使用しているので、URL のブロックを既に解除している可能性もあります。

    >[!NOTE]
    > Windows Server Core へのクラウド プロビジョニング エージェントのインストールはサポートされていません。

### <a name="additional-requirements"></a>その他の要件

- [Microsoft .NET Framework 4.7.1](https://dotnet.microsoft.com/download/dotnet-framework/net471) 

#### <a name="tls-requirements"></a>TLS の要件

> [!NOTE]
> トランスポート層セキュリティ (TLS) は、セキュリティで保護された通信を規定するプロトコルです。 TLS 設定を変更すると、フォレスト全体に影響します。 詳細については、「[Windows の WinHTTP における既定の安全なプロトコルとして TLS 1.1 および TLS 1.2 を有効化する更新プログラム](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)」を参照してください。

Azure AD Connect クラウド プロビジョニング エージェントをホストする Windows サーバーでは、インストールする前に TLS 1.2 を有効にする必要があります。

TLS 1.2 を有効にするには、次の手順に従います。

1. 次のレジストリ キーを設定します。

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. サーバーを再起動します。
## <a name="ntlm-requirement"></a>NTLM 要件

Azure AD Connect Provisioning Agent を実行している Windows Server では NTLM を有効にしないでください。有効になっている場合は、必ず無効にしてください。 

## <a name="known-limitations"></a>既知の制限事項

既知の制限事項には、次のようなものがあります。

### <a name="delta-synchronization"></a>差分同期

- 差分同期のグループ スコープ フィルターでは、50,000 人を超えるメンバーはサポートされません。
- グループ スコープ フィルターの一部として使用されているグループを削除すると、そのグループのメンバーであるユーザーが削除されません。 
- スコープ内の OU またはグループの名前を変更すると、差分同期を実行してもユーザーが削除されません。

### <a name="provisioning-logs"></a>プロビジョニング ログ
- プロビジョニング ログを使用すると、作成操作と更新操作が明確に区別されません。  更新時に作成操作、および作成時に更新操作が表示される場合があります。

### <a name="group-re-naming-or-ou-re-naming"></a>グループ名の変更または OU 名の変更
- 特定の構成のスコープ内にある AD でグループまたは OU の名前を変更すると、クラウド同期ジョブによって AD での名前の変更が認識されなくなります。 ジョブは検疫されず、正常な状態のままになります。

### <a name="scoping-filter"></a>スコープ フィルター
OU スコープ フィルターを使用する場合
- 特定の構成に対して同期できるのは、最大 59 の個別の OU のみです。 
- 入れ子になった OU がサポートされています (つまり、130 の入れ子になった OU を持つ OU を同期 **できます** が、同じ構成で 60 の個別の OU を同期することは **できません**)。 

### <a name="password-hash-sync"></a>パスワード ハッシュの同期
- InetOrgPerson とのパスワード ハッシュ同期の使用はサポートされていません。


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
