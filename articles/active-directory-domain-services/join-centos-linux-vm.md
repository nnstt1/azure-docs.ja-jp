---
title: CentOS VM を Azure AD Domain Services に参加させる | Microsoft Docs
description: CentOS Linux 仮想マシンを構成して Azure Active Directory Domain Services のマネージド ドメインに参加させる方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/17/2021
ms.author: justinha
ms.openlocfilehash: dc82c2c2793495009b9200ac0976c796ef2ccc9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603754"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>CentOS Linux 仮想マシンを Azure Active Directory Domain Services のマネージド ドメインに参加させる

ユーザーが 1 セットの資格情報を使用して Azure の仮想マシン (VM) にサインインできるようにするには、Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインに VM を参加させます。 VM を Azure AD DS のマネージド ドメインに参加させると、ドメインのユーザー アカウントと資格情報を使用して、サーバーにサインインして管理することができます。 マネージド ドメインのグループ メンバーシップも適用され、VM 上のファイルまたはサービスへのアクセスを制御できるようになります。

この記事では、CentOS Linux VM をマネージド ドメインに参加させる方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* マネージド ドメインの一部であるユーザー アカウント。
* 名前の切り詰めによって生じる Active Directory での競合を防ぐため、最大 15 文字の一意の Linux VM 名。

## <a name="create-and-connect-to-a-centos-linux-vm"></a>CentOS Linux VM を作成してそれに接続する

Azure に CentOS Linux VM が既にある場合は、SSH を使用してそれに接続した後、次の手順に進んで [VM の構成を開始](#configure-the-hosts-file)します。

CentOS Linux VM を作成する必要がある場合、またはこの記事で使用するためのテスト VM を作成する場合は、次のいずれかの方法を使用できます。

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM を作成するときは、VM がマネージド ドメインと通信できるように、仮想ネットワークの設定に注意してください。

* Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク、またはピアリングされた仮想ネットワークに、VM をデプロイします。
* マネージド ドメインとは別のサブネットに VM をデプロイします。

VM をデプロイした後、SSH を使用して VM に接続する手順に従います。

## <a name="configure-the-hosts-file"></a>hosts ファイルを構成する

マネージド ドメインに対して VM ホスト名が正しく構成されていることを確認するには、 */etc/hosts* ファイルを編集して、ホスト名を設定します。

```console
sudo vi /etc/hosts
```

*hosts* ファイルで、*localhost* アドレスを更新します。 次の例では

* *aaddscontoso.com* は、マネージド ドメインの DNS ドメイン名です。
* *centos* は、マネージド ドメインに参加させる CentOS VM のホスト名です。

これらの名前を実際の値に更新します。

```console
127.0.0.1 centos.aaddscontoso.com centos
```

終わったら、エディターの `:wq` コマンドを使用して、*hosts* ファイルを保存して終了します。

## <a name="install-required-packages"></a>必要なパッケージをインストールする

VM をマネージド ドメインに参加させるには、VM にいくつかの追加パッケージが必要です。 これらのパッケージをインストールして構成するには、`yum` を使用してドメイン参加ツールを更新およびインストールします。

```console
sudo yum install adcli realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>VM をマネージド ドメインに参加させる

必要なパッケージが VM にインストールされたので、VM をマネージド ドメインに参加させます。

1. `realm discover` コマンドを使用して、マネージド ドメインを検出します。 次の例では、領域 *AADDSCONTOSO.COM* が検出されます。 独自のマネージド ドメイン名を、すべて大文字で指定します。

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   `realm discover` コマンドでマネージド ドメインが見つからない場合は、次のトラブルシューティング手順を確認してください。

    * ドメインに VM からアクセスできることを確認します。 `ping aaddscontoso.com` を試し、肯定応答が返されるかどうかを確認します。
    * VM が、マネージド ドメインを利用可能な仮想ネットワークと同じ仮想ネットワーク、またはそれとピアリングされた仮想ネットワークに、デプロイされていることを確認します。
    * 仮想ネットワークに対する DNS サーバーの設定が、マネージド ドメインのドメイン コントローラーを指すように更新されていることを確認します。

1. 次に、`kinit` コマンドを使用して Kerberos を初期化します。 マネージド ドメインの一部であるユーザーを指定します。 必要に応じて、[Azure AD のグループにユーザー アカウントを追加します](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    やはり、マネージド ドメインの名前をすべて大文字で入力する必要があります。 次の例では、`contosoadmin@aaddscontoso.com` という名前のアカウントを使用して Kerberos を初期化しています。 マネージド ドメインの一部である独自のユーザー アカウントを入力します。

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 最後に、`realm join` コマンドを使用して、VM をマネージド ドメインに参加させます。 前の `kinit` コマンドで指定した、マネージド ドメインの一部である同じユーザー アカウントを使用します (`contosoadmin@AADDSCONTOSO.COM` など)。

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --membership-software=adcli
    ```

VM をマネージド ドメインに参加させるにはしばらくかかります。 次の出力例では、VM がマネージド ドメインに正常に参加したことが示されています。

```output
Successfully enrolled machine in realm
```

VM のドメイン参加プロセスを正常に完了できない場合は、VM のネットワーク セキュリティ グループで、マネージド ドメインの仮想ネットワーク サブネットに対する TCP + UDP ポート 464 での送信 Kerberos トラフィックが許可されていることを確認します。

## <a name="allow-password-authentication-for-ssh"></a>SSH のパスワード認証を許可する

既定では、ユーザーは SSH 公開キーベースの認証を使用することによってのみ、VM にサインインできます。 パスワードベースの認証は失敗します。 VM をマネージド ドメインに参加させるときは、これらのドメイン アカウントでパスワードベースの認証を使用する必要があります。 次のようにして、パスワードベースの認証を許可するように SSH の構成を更新します。

1. エディターで *sshd_conf* ファイルを開きます。

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *PasswordAuthentication* の行を *yes* に更新します。

    ```console
    PasswordAuthentication yes
    ```

    終わったら、エディターの `:wq` コマンドを使用して、*sshd_conf* ファイルを保存して終了します。

1. 変更を適用し、ユーザーがパスワードを使用してサインインできるようにするには、SSH サービスを再起動します。

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>"AAD DC Administrators" グループに sudo 特権を付与する

*AAD DC Administrators* グループのメンバーに CentOS VM での管理特権を付与するには、 */etc/sudoers* にエントリを追加します。 追加した後、*AAD DC Administrators* グループのメンバーは、CentOS VM で `sudo` コマンドを使用できるようになります。

1. *sudoers* ファイルを編集用に開きます。

    ```console
    sudo visudo
    ```

1. */etc/sudoers* ファイルの最後に、次のエントリを追加します。 *AAD DC Administrators* グループの名前に空白が含まれているため、グループ名に円記号のエスケープ文字を含めます。 独自のドメイン名 (*aaddscontoso.com* など) を追加します。

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    終わったら、エディターの `:wq` コマンドを使用してエディターを保存して終了します。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>ドメイン アカウントを使用して VM にサインインする

VM がマネージド ドメインに正常に参加したことを確認するには、ドメイン ユーザー アカウントを使用して新しい SSH 接続を開始します。 ホーム ディレクトリが作成されていること、およびドメインのグループ メンバーシップが適用されていることを確認します。

1. コンソールから新しい SSH 接続を作成します。 `ssh -l` コマンドを使用して、マネージド ドメインに属しているドメイン アカウントを使用し (`contosoadmin@aaddscontoso.com` など)、VM のアドレス (*centos.aadds.contoso.com* など) を入力します。 Azure Cloud Shell を使用する場合は、内部 DNS 名ではなく、VM のパブリック IP アドレスを使用します。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. VM に正常に接続したら、ホーム ディレクトリが正しく初期化されていることを確認します。

    ```console
    pwd
    ```

    ユーザー アカウントと一致する独自のディレクトリの */home* ディレクトリにいる必要があります。

1. 次に、グループ メンバーシップが正しく解決されていることを確認します。

    ```console
    id
    ```

    マネージド ドメインからのグループ メンバーシップが表示される必要があります。

1. *AAD DC Administrators* グループのメンバーとして VM にサインインした場合は、`sudo` コマンドを正しく使用できることを確認します。

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>次のステップ

マネージド ドメインへの VM の接続、またはドメイン アカウントでのサインインに関して問題がある場合は、「[ドメイン参加の問題のトラブルシューティング](join-windows-vm.md#troubleshoot-domain-join-issues)」を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
