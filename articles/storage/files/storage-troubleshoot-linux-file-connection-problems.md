---
title: Linux での Azure Files に関する問題のトラブルシューティング | Microsoft Docs
description: Linux での Azure Files に関する問題のトラブルシューティング。 Linux クライアントから接続する場合の Azure Files に関連する一般的な問題と、考えられる解決方法を参照してください。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 6277e42daf370cef65724f8958a9851ecd51d57c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216747"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>Linux での Azure Files に関する問題のトラブルシューティング (SMB)

この記事では、Linux クライアントから接続するときに生じる、Azure File に関係する一般的な問題を示します。 これらの問題の考えられる原因と解決策についても説明します。 

この記事のトラブルシューティングの手順のほかに、[AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) を使用して、Linux クライアントが前提条件を適切に満たしていることを確認できます。 AzFileDiagnostics では、この記事で説明しているほとんどの症状が自動的に検出されます。 最適なパフォーマンスが得られる環境をセットアップするために役立ちます。 この情報は、[Azure ファイル共有のトラブルシューティング ツール](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)で入手することもできます。 トラブルシューティング ツールには、Azure Files 共有の接続、マッピング、およびマウントに関する問題の解決に役立つ手順が用意されています。

> [!IMPORTANT]
> この記事の内容は SMB 共有にのみ適用されます。 NFS 共有の詳細については、「[Azure NFS ファイル共有に関するトラブルシューティング](storage-troubleshooting-files-nfs.md)」を参照してください。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Azure ファイル共有を接続またはマウントできない

### <a name="cause"></a>原因

この問題の一般的な原因は次のとおりです。

- 使用している Linux ディストリビューションの SMB クライアントが最新ではありません。クライアントに互換性があって、なおかつ Azure で利用できる一般的な Linux ディストリビューションについては、[Linux での Azure Files の使用](storage-how-to-use-files-linux.md)に関するページを参照してください。
- クライアントに SMB ユーティリティ (cifs-utils) がインストールされていません。
- SMB の最小バージョン 2.1 がクライアントで利用できません。
- SMB 3.x 暗号化がクライアントでサポートされていません。 上記の表では、暗号化を使用してオンプレミスや複数のリージョンにまたがるマウントをサポートする Linux ディストリビューションの一覧を示しています。 他のディストリビューションの場合は、カーネル 4.11 以降のバージョンが必要です。
- Azure VM から Azure ファイル共有に接続しようとしていますが、VM はストレージ アカウントと同じリージョンにありません。
- [[安全な転送が必須]](../common/storage-require-secure-transfer.md) 設定がストレージ アカウントで有効になっている場合、Azure Files は暗号化付き SMB 3.x を使った接続のみを許可します。

### <a name="solution"></a>解決策

この問題を解決するには、「[Troubleshooting tool for Azure Files mounting errors on Linux (Linux での Azure Files のマウント エラー用トラブルシューティング ツール)](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux)」を使用します。 このツールには、以下の機能があります。

* クライアントの実行環境の検証を支援します。
* Azure Files へのアクセス エラーの原因となる、互換性のないクライアント構成を検出します。
* 自己修正に関する規範的なガイダンスを提供します。
* 診断トレースを収集します。

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>Azure ファイル共有をマウントしようとしたときに、"マウント エラー (13): アクセス許可が拒否されました" というエラーが発生する

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1:通信チャネルが暗号化されていない

通信チャネルが暗号化されていない場合や、接続の試行が Azure ファイル共有と同じデータセンターから行われていない場合、セキュリティ上の理由により、Azure ファイル共有への接続がブロックされます。 ストレージ アカウントで [[安全な転送が必須]](../common/storage-require-secure-transfer.md) 設定が有効になっている場合は、同じデータセンター内の暗号化されていない接続もブロックされる可能性があります。 ユーザーのクライアント OS が SMB 暗号化をサポートしている場合に限り、暗号化された通信チャネルを利用できます。

詳しくは、[「Linux で cifs-utils パッケージを使用してAzure ファイル共有をマウントするための前提条件」](storage-how-to-use-files-linux.md#prerequisites)をご覧ください。 

### <a name="solution-for-cause-1"></a>原因 1 の解決策

1. SMB 暗号化をサポートするクライアントから接続するか、Azure ファイル共有に使用されている Azure ストレージ アカウントと同じデータ センターにある仮想マシンから接続します。
2. クライアントが SMB 暗号化をサポートしていない場合、ストレージ アカウントで [[安全な転送が必須]](../common/storage-require-secure-transfer.md) 設定が無効になっていることを確認します。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2:ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが有効になっている 

仮想ネットワーク (VNET) またはファイアウォール ルールがストレージ アカウントに構成されている場合、クライアント IP アドレスまたは仮想ネットワークがアクセスを許可されていない限り、ネットワーク トラフィックは拒否されます。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが適切に構成されていることを確認します。 仮想ネットワークまたはファイアウォール ルールが問題の原因となっているかどうかをテストするには、ストレージ アカウントの設定を一時的に **[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** に変更する必要があります。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](../common/storage-network-security.md)を参照してください。

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>ファイルを開こうとしたときの "[アクセス許可が拒否されました] ディスク クォータを超えました"

Linux では、次のようなエラー メッセージが表示されます。

**\<filename> [アクセス許可が拒否されました] ディスク クォータを超えました**

### <a name="cause"></a>原因

ファイルまたはディレクトリで許容される、同時に開くことのできるハンドルの上限に達しました。

開くことができるハンドルのクォータは、1 つのファイルまたはディレクトリにつき 2,000 個です。 開いているハンドルが 2,000 個になると、クォータに達したことを伝えるエラー メッセージが表示されます。

### <a name="solution"></a>解決策

ハンドルをいくつか閉じて、同時に開いているハンドルの数を減らしてから、操作を再試行します。

ファイル共有、ディレクトリ、またはファイルの開いているハンドルを表示するには、[Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell コマンドレットを使用します。  

ファイル共有、ディレクトリ、またはファイルの開いているハンドルを閉じるには、[Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell コマンドレットを使用します。

> [!Note]  
> Get-AzStorageFileHandle および Close-AzStorageFileHandle コマンドレットは、Az PowerShell モジュールのバージョン 2.4 以降に含まれています。 最新の Az PowerShell モジュールをインストールするには、「[Install the Azure PowerShell module](/powershell/azure/install-az-ps)」(Azure PowerShell モジュールのインストール) を参照してください。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux で Azure Files との間でのファイルのコピーが遅い

- 特定の最小 I/O サイズ要件がない場合は、最適なパフォーマンスを得るために I/O サイズとして 1 MiB を使用することをお勧めします。
- 次のように適切なコピー方法を使用します。
    - 2 つのファイル共有間の転送には、[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用します。
    - Parallel と一緒に cp または dd を使用することで、コピー速度が向上し、スレッド数はユース ケースとワークロードで決まります。 次の例では、6 を使用しています。 
    - cp の例 (cp はファイル システムの既定のブロック サイズをチャンク サイズとして使用します): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`。
    - dd の例 (このコマンドは、チャンク サイズを明示的に 1 MiB に設定します): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`。
    - オープンソースのサード パーティ製のツールの例:
        - [GNU Parallel](https://www.gnu.org/software/parallel/)。
        - [Fpart](https://github.com/martymac/fpart) - ファイルを並べ替え、パーティションにパックします。
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - Fpart、および複数のインスタンスを生成するコピー ツールを使用して、データを src_dir から dst_url に移行させます。
        - [Multi](https://github.com/pkolano/mutil) - GNU coreutils に基づくマルチスレッド cp と md5sum。
- すべての書き込みを拡張性のある書き込みにするのではなく、事前にファイル サイズを設定すると、ファイル サイズがわかっているシナリオでコピー速度の向上に役立ちます。 拡張性のある書き込みを避ける必要がある場合は、`truncate - size <size><file>` コマンドを使用して宛先のファイル サイズを設定できます。 その後は、`dd if=<source> of=<target> bs=1M conv=notrunc` コマンドでソース ファイルをコピーする際にターゲット ファイルのサイズを繰り返し更新する必要はありません。 たとえば、コピーするすべてのファイルに対してコピー先のファイル サイズを設定できます (共有が /mnt/share の下にマウントされていると仮定します)。
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - その後は、並行して書き込みを拡張せずにファイルをコピーします: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-3x"></a>SMB 3.x を使用して Azure Files をマウントするときの "マウント エラー (115): 操作は現在実行中です"

### <a name="cause"></a>原因

一部の Linux ディストリビューションは、SMB 3.x の暗号化機能を現時点ではサポートしていません。 ユーザーが SMB 3.x を使用して Azure Files をマウントしようとしたときに、機能が見つからないために "115" エラー メッセージが表示される場合があります。 完全な暗号化機能を備えた SMB 3.x は、Ubuntu 16.04 以降を使用している場合にのみサポートされます。

### <a name="solution"></a>解決策

Linux 用の SMB 3.x の暗号化機能は 4.11 カーネルで導入されました。 この機能によって、オンプレミスから、または異なる Azure リージョンから Azure ファイル共有をマウントできます。 Linux ディストリビューションによっては、4.11 カーネルから以前のバージョンの Linux カーネルへの移植変更が加えられている場合があります。 使用している Linux のバージョンが暗号化を使用した SMB 3.x をサポートしているかどうかを判断するには、[Linux での Azure Files の使用](storage-how-to-use-files-linux.md)に関するページを参照してください。 

Linux SMB クライアントが暗号化をサポートしていない場合は、ファイル共有と同じデータ センターにある Azure Linux VM から SMB 2.1 を使用して Azure Files をマウントします。 ストレージ アカウントで [[安全な転送が必須]](../common/storage-require-secure-transfer.md) 設定が無効になっていることを確認します。 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Azure ファイル共有にアクセスするか、Azure ファイル共有を削除しようとしたときに "アクセス権なし" というエラーが発生する  
ポータルで Azure ファイル共有にアクセスするか、Azure ファイル共有を削除しようとした時、以下のエラーが発生する場合があります。

アクセス権なし  
エラー コード:403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 1:ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが有効になっている

### <a name="solution-for-cause-1"></a>原因 1 の解決策

ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが適切に構成されていることを確認します。 仮想ネットワークまたはファイアウォール ルールが問題の原因となっているかどうかをテストするには、ストレージ アカウントの設定を一時的に **[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** に変更する必要があります。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](../common/storage-network-security.md)を参照してください。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 2:現在のユーザー アカウントには、ストレージ アカウントへのアクセス権がありません

### <a name="solution-for-cause-2"></a>原因 2 の解決策

Azure ファイル共有が置かれたストレージ アカウントを参照して、 **[アクセス制御 (IAM)]** をクリックし、ユーザー アカウントにストレージ アカウントへのアクセス権があることを確認します。 詳しくは、[ロールベースのアクセス制御 (RBAC) を使用してストレージ アカウントをセキュリティで保護する方法](../blobs/security-recommendations.md#data-protection)に関するページをご覧ください。

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Azure ファイル共有のファイルまたはディレクトリを削除できない

### <a name="cause"></a>原因
この問題は、通常、ファイルまたはディレクトリのハンドルが開いている場合に発生します。 

### <a name="solution"></a>解決策

開いているすべてのハンドルを SMB クライアントで閉じた後も問題が引き続き発生する場合は、次の手順を行います。

- [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell コマンドレットを使用して、開いているハンドルを表示します。

- [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell コマンドレットを使用して、開いているハンドルを閉じます。 

> [!Note]  
> Get-AzStorageFileHandle および Close-AzStorageFileHandle コマンドレットは、Az PowerShell モジュールのバージョン 2.4 以降に含まれています。 最新の Az PowerShell モジュールをインストールするには、「[Install the Azure PowerShell module](/powershell/azure/install-az-ps)」(Azure PowerShell モジュールのインストール) を参照してください。

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM にマウントされている Azure ファイル共有のパフォーマンスが低下している

### <a name="cause-1-caching"></a>原因 1:キャッシュ

パフォーマンスの低下の原因の 1 つとして考えられるのは、キャッシュの無効化です。 キャッシュは、ファイルに繰り返しアクセスする場合に便利ですが、そうでなければオーバーヘッドでもかまいません。 キャッシュを使用しているかどうか、これを無効にする前に確認します。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

キャッシュが無効になっているかどうかを確認するには、**cache=** エントリを探します。

**cache=none** であれば、キャッシュが無効になっています。 既定のマウント コマンドを使用して共有を再マウントするか、マウント コマンドに明示的に **cache=strict** オプションを追加して、既定のキャッシュまたは "strict" キャッシュ モードを有効にします。

一部のシナリオでは、**serverino** マウント オプションが原因となり、**ls** コマンドによってすべてのディレクトリ エントリに対して stat が実行されることがあります。 この動作のために、大規模なディレクトリを一覧表示するときのパフォーマンスが低下します。 **/etc/fstab** エントリで、マウント オプションを確認できます。

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

**sudo mount | grep cifs** コマンドを実行し、その出力を調べることによって、正しいオプションが使用されているかどうかを確認することもできます。 以下に、出力の例を示します。

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

**cache=strict** または **serverino** オプションが存在しない場合は、[ドキュメント](./storage-how-to-use-files-linux.md)のマウント コマンドを実行して、Azure Files をマウント解除してから再マウントします。 その後、 **/etc/fstab** エントリに正しいオプションが指定されていることを再確認します。

### <a name="cause-2-throttling"></a>原因 2:Throttling

スロットリングが生じたり、要求がキューに送信されたりしている可能性があります。 [Azure Monitor の Azure Storage メトリック](../blobs/monitor-blob-storage.md)を利用して、これを確認することができます。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

アプリが [Azure Files のスケール ターゲット](storage-files-scale-targets.md#azure-files-scale-targets)内にあることを確認します。

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Windows から Linux にファイルをコピーすると、タイム スタンプが失われる

Linux/Unix プラットフォームでは、ファイル 1 とファイル 2 を別のユーザーが所有している場合、**cp -p** コマンドが失敗します。

### <a name="cause"></a>原因

COPYFILE の強制フラグ **f** を使用すると、Unix 上で **cp -p -f** が実行されます。 このコマンドでも、所有者でないファイルのタイム スタンプの保持が失敗します。

### <a name="workaround"></a>回避策

ファイルをコピーする際に、ストレージ アカウント ユーザーを使用します。

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: '&lt;path&gt;' にアクセスできません入出力エラー

ls コマンドを使用して Azure ファイル共有のファイル一覧を表示しようとすると、ファイルの一覧表示時にコマンドが停止します。 次のようなエラーが表示されます。

**ls: '&lt;path&gt;' にアクセスできません入出力エラー**


### <a name="solution"></a>解決策
この問題が修正されている次のバージョンに Linux カーネルをアップグレードします。

- 4.4.87 以降
- 4.9.48 以降
- 4.12.11 以降
- 4\.13 以降のすべてのバージョン

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>シンボリック リンクを作成できない - ln: failed to create symbolic link 't':操作がサポートされていません。

### <a name="cause"></a>原因
既定では、CIFS を使用して Linux 上の Azure ファイル共有をマウントしても、シンボリック リンク (symlinks) のサポートは有効になりません。 次のようなエラーが表示されます。
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>解決策
Linux CIFS クライアントは、SMB 2 または 3 プロトコルでの Windows スタイル シンボリック リンクの作成をサポートしていません。 現在のところ、Linux クライアントは、[Minshall+French symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) という別形式のシンボリック リンクを作成操作とフォロー操作の両方でサポートしています。 シンボリック リンクが必要な場合は、"mfsymlinks" マウント オプションを使用できます。 "mfsymlinks" 形式は Mac でも使用されるため、この形式の使用をお勧めします。

シンボリック リンクを使用するには、CIFS mount コマンドの末尾に以下を追加します。

```
,mfsymlinks
```

そのため、コマンドは以下のようになります。

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

そのようにすると、[wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers) で提案されているようにシンボリック リンクを作成することができます。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>再接続タイムアウトによる"マウント エラー (112): ホストがダウンしています"

"112" マウント エラーは、Linux クライアント上で、クライアントが長時間アイドル状態になった場合に発生します。 アイドル状態の時間が長いと、クライアントが切断され、接続がタイムアウトになります。  

### <a name="cause"></a>原因

接続は、以下の理由でアイドル状態になることがあります。

-   既定の "ソフト" マウント オプションを使用している場合に、ネットワーク通信エラーによりサーバーへの TCP 接続を再確立できない
-   再接続に関する問題の最新の修正プログラムが存在しない比較的古いカーネルを使用している

### <a name="solution"></a>解決策

Linux カーネルの再接続に関するこの問題は、以下の変更の一環として修正されました。

- [ソケットの再接続後、smb3 セッションの再接続を長時間保留しないようにする修正](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [ソケットの再接続後すぐにエコー サービスを呼び出す](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS:再接続中にメモリが破損する可能性がある問題の修正](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS:再接続中にミューテックスのダブル ロックが発生する可能性がある問題の修正 (カーネル v4.9 以降)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

ただし、これらの変更が既にすべての Linux ディストリビューションに移植されているとは限りません。 一般的な Linux ディストリビューションを使用している場合は、「[Linux で Azure Files 使用する](storage-how-to-use-files-linux.md)」で、必要なカーネル変更が加えられているディストリビューションのバージョンを確認できます。

### <a name="workaround"></a>回避策

ハード マウントを指定することによって、この問題を回避することができます。 ハード マウントでは、接続が確立されるか、明示的に中断されるまで、クライアントが強制的に待機させられます。 この機能を使用することで、ネットワーク タイムアウトによるエラーを防ぐことができます。 ただし、この回避策では、待機が無期限に続く可能性があります。 必要に応じて、接続を停止できるように準備しておいてください。

最新バージョンのカーネルにアップグレードできない場合は、Azure ファイル共有にファイルを保持し、30 秒以下の間隔で書き込みを実行することによって、この問題を回避できます。 これは、ファイルの作成日/変更日が書き換えられるような書き込み操作である必要があります。 そうでない場合、キャッシュされた結果が取得され、操作によって再接続がトリガーされない可能性があります。

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-3x"></a>SMB 3.x を使用して Azure ファイル共有をマウントするときの、"CIFS VFS: error -22 on ioctl to get interface list" (CIFS VFS: インターフェイス リストを取得するための ioctl でのエラー -22)

### <a name="cause"></a>原因
このエラーは、Azure Files では[現在 SMB マルチチャネルがサポートされていない](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)ために、ログに記録されます。

### <a name="solution"></a>解決策
このエラーは無視してかまいません。


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>名前の終わりにスペースまたはドットが含まれるフォルダーまたはファイルにアクセスできない

Linux 上にマウントされているとき、Azure ファイル共有からフォルダーまたはファイルにアクセスできません。共有にアクセスしようとすると、du や Is のようなコマンド、あるいはサードパーティ アプリケーションが失敗し、"ファイルまたはディレクトリが存在しません" というエラーが表示されることがあります。しかし、そのようなフォルダーには、ポータルからファイルをアップロードできます。

### <a name="cause"></a>原因

名前の終わりの文字を別の文字にエンコードするシステムからフォルダーまたはファイルがアップロードされました。Macintosh コンピューターからアップロードされたファイルの場合、0x20 (スペース) または 0X2E (ドット) ではなく、"0xF028" または "0xF029" 文字が使用されている可能性があります。

### <a name="solution"></a>解決策

Linux に共有をマウントするとき、共有で mapchars オプションを使用します。 

次の代わりに

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

を使う代わりに、

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。