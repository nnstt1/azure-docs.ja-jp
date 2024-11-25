---
title: Azure Files のデプロイの計画 | Microsoft Docs
description: Azure Files デプロイの計画について理解します。 Azure File Sync を使用し、Azure ファイル共有を直接マウントするか、オンプレミスでキャッシュできます。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2cd4423d1d006555eedcbf7a6f08f04e415ea9bf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292929"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files のデプロイの計画
[Azure Files](storage-files-introduction.md) は、サーバーレスの Azure ファイル共有を直接マウントすることと、Azure File Sync を使用してオンプレミスで Azure ファイル共有をキャッシュすることの 2 つの主な方法でデプロイできます。選択するデプロイ オプションによって、デプロイを計画する際に考慮する必要がある内容が変わります。 

- **Azure ファイル共有の直接マウント**: Azure Files からは Server Message Block (SMB) または Network File System (NFS) アクセスが提供されるため、Azure ファイル共有は、お使いの OS で利用できる標準の SMB または NFS (プレビュー) クライアントを利用し、オンプレミスまたはクラウドでマウントできます。 Azure ファイル共有はサーバーレスであるため、運用環境でデプロイするシナリオでは、ファイル サーバーや NAS デバイスを管理する必要ありません。 つまり、ソフトウェアの修正プログラムを適用したり、物理ディスクを交換したりする必要はありません。 

- **Azure File Sync を使用したオンプレミスでの Azure ファイル共有のキャッシュ**:Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files で組織のファイル共有を一元化できます。 Azure File Sync によって、オンプレミス (またはクラウド) の Windows Server が Azure SMB ファイル共有の高速キャッシュに変換されます。 

この記事では主に、オンプレミスまたはクラウド クライアントによって直接マウントされる Azure ファイル共有をデプロイする場合の、デプロイに関する考慮事項について説明します。 Azure File Sync のデプロイを計画する場合は、「[Azure File Sync のデプロイの計画](../file-sync/file-sync-planning.md)」を参照してください。

## <a name="available-protocols"></a>使用可能なプロトコル
Azure Files には、Azure ファイル共有のマウント用に、[サーバー メッセージ ブロック (SMB)](files-smb-protocol.md) プロトコルと[ネットワーク ファイル システム (NFS)](files-nfs-protocol.md) プロトコルの 2 つの業界標準プロトコルが用意されています。 Azure Files を使用すると、ワークロードに最適なファイル システム プロトコルを選択できます。 Azure ファイル共有では、SMB と NFS の両方のプロトコルを同じファイル共有で使用することはできません。ただし、同じストレージ アカウントに SMB と NFS の Azure ファイル共有を作成することはできます。 現在、NFS 4.1 のみが、新しい **FileStorage** ストレージ アカウント タイプ内でサポートされます (Premium ファイル共有のみ)。

SMB と NFS の両方のファイル共有に対し、Azure Files により、ストレージのニーズに合わせたスケールアップが可能で、数千ものクライアントによって同時にアクセスできる、エンタープライズ レベルのファイル共有が提供されます。

| 機能 | SMB | NFS (プレビュー) |
|---------|-----|---------------|
| サポートされるプロトコルのバージョン | SMB 3.1.1、SMB 3.0、SMB 2.1 | NFS 4.1 |
| 推奨される OS | <ul><li>Windows 10 バージョン 21H1 以降</li><li>Windows Server 2019 以降</li><li>Linux カーネル バージョン 5.3 以降</li></ul> | Linux カーネル バージョン 4.3 以降 |
| [使用できるレベル](storage-files-planning.md#storage-tiers)  | Premium、トランザクション最適化、ホット、クール | Premium |
| 課金モデル | <ul><li>[プロビジョニング容量 (Premium ファイル共有)](./understanding-billing.md#provisioned-model)</li><li>[従量課金制 (Standard ファイル共有)](./understanding-billing.md#pay-as-you-go-model)</li></ul> | [プロビジョニング容量](./understanding-billing.md#provisioned-model) |
| [冗長性](storage-files-planning.md#redundancy) | LRS、ZRS、GRS、GZRS | LRS、ZRS |
| ファイル システム セマンティクス | Win32 | POSIX |
| 認証 | ID ベースの認証 (Kerberos)、共有キー認証 (NTLMv2) | ホストベースの認証 |
| 承認 | Win32 スタイルのアクセス制御リスト (ACL) | UNIX 形式のアクセス許可 |
| 大文字小文字の区別 | 大文字小文字は区別されないが、保持される | 大文字小文字は区別される |
| 開いているファイルの削除または変更 | ロックのみを使用する | はい |
| ファイル共有 | [Windows 共有モード](/windows/win32/fileio/creating-and-opening-files) | バイト範囲アドバイザリ ネットワーク ロック マネージャー |
| ハード リンクのサポート | サポートされていません | サポートされています |
| シンボリック リンクのサポート | サポートされていません | サポートされています |
| 必要に応じてインターネットからアクセス可能 | はい (SMB 3.0 以降のみ) | いいえ |
| FileREST のサポート | はい | サブセット: <br /><ul><li>[`FileService` に対する操作](/rest/api/storageservices/operations-on-the-account--file-service-)</li><li>[`FileShares` に対する操作](/rest/api/storageservices/operations-on-shares--file-service-)</li><li>[`Directories` に対する操作](/rest/api/storageservices/operations-on-directories)</li><li>[`Files` に対する操作](/rest/api/storageservices/operations-on-files)</li></ul> |

## <a name="management-concepts"></a>管理の概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Azure ファイル共有をストレージ アカウントにデプロイする場合は、次のことをお勧めします。

- 他の Azure ファイル共有を使用したストレージ アカウントへの Azure ファイル共有のデプロイのみ。 GPv2 ストレージ アカウントでは混在する目的のストレージ アカウントを使用できますが、Azure ファイル共有や BLOB コンテナーなどのストレージ リソースではストレージ アカウントの制限が共有されるため、リソースを混在させると、後でパフォーマンスの問題のトラブルシューティングがより困難になる可能性があります。 

- Azure ファイル共有をデプロイする際には、ストレージ アカウントの IOPS 制限に注意してください。 ファイル共有をストレージ アカウントに 1:1 でマップするのが理想的ですが、これは、組織と Azure の両方からのさまざまな制限や制約により、実現できない場合もあります。 1 つのストレージ アカウントに 1 つのファイル共有のみをデプロイすることができない場合は、使用頻度が高い共有と低い共有を考慮し、最もアクティブなファイル共有が同じストレージ アカウントに一緒にデプロイされないようにしてください。

- GPv2 と FileStorage アカウントのみをデプロイし、ご利用の環境内で GPv1 とクラシック ストレージ アカウントが見つかった場合はそれらをアップグレードします。 

## <a name="identity"></a>ID
Azure ファイル共有にアクセスするには、ファイル共有のユーザーを認証し、共有へのアクセスを承認する必要があります。 これは、ファイル共有にアクセスするユーザーの ID に基づいて行われます。 Azure Files は、次の 3 つの主な ID プロバイダーと統合されています。
- **オンプレミス Active Directory Domain Services (AD DS、またはオンプレミス AD DS)** :Azure ストレージ アカウントは、Windows Server ファイル サーバーや NAS デバイスと同様に、顧客所有の Active Directory Domain Services ドメインに参加させることができます。 ドメイン コントローラーは、Azure VM や、別のクラウド プロバイダーの VM としてもオンプレミスにデプロイできます。Azure Files は、ドメイン コントローラーがホストされている場所に依存しません。 ストレージ アカウントがドメインに参加すると、エンド ユーザーは、PC にサインインしたユーザー アカウントを使用してファイル共有をマウントできます。 AD ベースの認証では、Kerberos 認証プロトコルが使用されます。
- **Azure Active Directory Domain Services (Azure AD DS)** :Azure AD DS では、Azure リソースに使用できる Microsoft が管理するドメイン コントローラーが提供されます。 Azure AD DS ドメインにストレージ アカウントを参加させることで、顧客所有の Active Directory ドメインに参加させる場合と同様の利点が得られます。 このデプロイ オプションは、AD ベースのアクセス許可を必要とするアプリケーションのリフトアンドシフト シナリオに最も役立ちます。 Azure AD DS では AD ベースの認証が提供されるため、このオプションでも Kerberos 認証プロトコルが使用されます。
- **Azure ストレージ アカウント キー**:Azure ファイル共有は、Azure ストレージ アカウント キーを使用してマウントすることもできます。 この方法でファイル共有をマウントするために、ストレージ アカウント名がユーザー名として使用され、ストレージ アカウント キーがパスワードとして使用されます。 ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることは、実質的には管理者の操作です。これは、マウントされたファイル共有には、ACL がある場合でも、共有上のすべてのファイルとフォルダーに対する完全なアクセス許可が付与されるためです。 ストレージ アカウント キーを使用して SMB 経由でマウントする場合は、NTLMv2 認証プロトコルが使用されます。

オンプレミスのファイル サーバーから移行するか、あるいは Windows ファイル サーバーや NAS アプライアンスと同様に動作させることが目的の Azure Files で新しいファイル共有を作成するお客様の場合は、**顧客所有 Active Directory** ドメインにストレージ アカウントを参加させることをお勧めします。 顧客所有 Active Directory ドメインへのストレージ アカウントの参加の詳細については、[Azure Files Active Directory の概要](storage-files-active-directory-overview.md)に関するページを参照してください。

ストレージ アカウント キーを使って Azure ファイル共有にアクセスする場合は、「[ネットワーク](#networking)」セクションの説明に従って、サービス エンドポイントを使用することをお勧めします。

## <a name="networking"></a>ネットワーク
Azure ファイル共有には、ストレージ アカウントのパブリック エンドポイントを介して、どこからでもアクセスできます。 つまり、ユーザーのログオン ID によって承認された要求など、認証済みの要求は、Azure の内外から安全に送信できます。 多くのお客様の環境では、Azure VM からの Azure ファイル共有のマウントは成功しても、オンプレミス ワークステーションでの最初のマウントは失敗します。 その理由は、SMB での通信に使用されるポート (445) が、多くの組織とインターネット サービス プロバイダー (ISP) によってブロックされているためです。 ポート 445 からのアクセスを許可する ISP または許可しない ISP の概要を確認するには、[TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) を参照してください。

Azure ファイル共有へのアクセスのブロックを解除するための次の主な 2 つのオプションがあります。

- 組織のオンプレミス ネットワークに対してポート 445 のブロックを解除します。 Azure ファイル共有には、SMB 3.x や FileREST API などのインターネットで安全なプロトコルを使用して、パブリック エンドポイント経由でのみ外部からアクセスできます。 これが、オンプレミスから Azure ファイル共有にアクセスする最も簡単な方法です。これは、組織の送信ポート規則の変更のほかに、高度なネットワーク構成が必要ではないためです。しかし、SMB のレガシおよび非推奨バージョンの SMB (つまり、SMB 1.0) を削除することをお勧めします。 これを行う方法については、「[Windows/Windows Server のセキュリティ保護](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3)」と「[Linux のセキュリティ保護](files-remove-smb1-linux.md)」を参照してください。

- ExpressRoute または VPN 接続経由で Azure ファイル共有にアクセスします。 ネットワーク トンネル経由で Azure ファイル共有にアクセスする場合、SMB トラフィックが組織の境界を通過しないため、オンプレミスのファイル共有のように Azure ファイル共有をマウントすることができます。   

技術的な観点からは、パブリック エンドポイントを使用すると Azure ファイル共有をかなりマウントしやすくなりますが、ほとんどのお客様は、ExpressRoute または VPN 接続経由での Azure ファイル共有のマウントを選択することが予想されます。 これらのオプションは SMB 共有と NFS 共有の両方でマウントできます。 これを行うには、ご利用の環境に合わせて次のように構成する必要があります。  

- **ExpressRoute、サイト間、またはポイント対サイト VPN を使用したネットワーク トンネリング**:仮想ネットワークへのトンネリングでは、ポート 445 がブロックされている場合でも、オンプレミスから Azure ファイル共有にアクセスできます。
- **プライベート エンドポイント**:プライベート エンドポイントでは、仮想ネットワークのアドレス空間内から、ストレージ アカウントに専用 IP アドレスが提供されます。 これにより、Azure Storage クラスターによって所有されているすべての IP アドレス範囲に対して、オンプレミスのネットワークを開くことなくネットワーク トンネリングを行うことができます。 
- **DNS 転送**:オンプレミスの DNS を構成して、ストレージ アカウントの名前 (パブリック クラウド リージョンの `storageaccount.file.core.windows.net`) を解決し、プライベート エンドポイントの IP アドレスに解決するようにします。

> [!Important]  
> 複数のネットワーク ルーティング オプションが、Azure Files によってサポートされています。 既定のオプションである Microsoft ルーティングは、Azure Files のすべての構成で動作します。 インターネット ルーティング オプションでは、AD ドメイン参加シナリオまたは Azure File Sync はサポートされていません。

Azure ファイル共有のデプロイに関連するネットワークを計画する場合は、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

## <a name="encryption"></a>暗号化
Azure Files では、2 種類の暗号化がサポートされています。転送中の暗号化は、Azure ファイル共有のマウントとアクセス時に使用される暗号化に関連しており、保存時の暗号化は、データがディスクに格納されるときの暗号化の方法に関連します。 

### <a name="encryption-in-transit"></a>転送中の暗号化

> [!IMPORTANT]
> このセクションでは、SMB 共有の転送中の暗号化について詳しく取り上げます。 NFS 共有による転送中の暗号化の詳細については、「[セキュリティとネットワーク](files-nfs-protocol.md#security-and-networking)」を参照してください。

既定では、すべての Azure ストレージ アカウントで転送中の暗号化が有効になっています。 つまり、SMB 経由でファイル共有をマウントするか、または FileREST プロトコル (Azure portal、PowerShell/CLI、Azure SDK など) 経由でファイル共有にアクセスすると、Azure Files では、暗号化または HTTPS が設定されている SMB 3.x 以上で作成された接続のみが許可されます。 SMB 3.x をサポートしていないクライアント、または SMB 3.x をサポートしているが、SMB 暗号化をサポートしていないクライアントは、転送中の暗号化が有効になっている場合は Azure ファイル共有をマウントできません。 暗号化付き SMB 3.x がサポートされているオペレーティング システムの詳細については、[Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md)、および [Linux](storage-how-to-use-files-linux.md) に関する詳細なドキュメントを参照してください。 PowerShell、CLI、および SDK の現在のバージョンはすべて HTTPS をサポートしています。  

Azure ストレージ アカウントでの転送中の暗号化を無効にすることができます。 暗号化が無効になっている場合、Azure Files では、SMB 2.1、暗号化なしの SMB 3.x、および HTTP 経由の暗号化されていない FileREST API 呼び出しも許可されます。 転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のあるレガシ アプリケーションをサポートするためです。 Azure Files では、Azure ファイル共有と同じ Azure リージョン内の SMB 2.1 接続のみが許可されます。Azure ファイル共有の Azure リージョンの外部 (オンプレミスまたは異なる Azure リージョン内など) の SMB 2.1 クライアントは、ファイル共有にアクセスできません。

転送中のデータの暗号化が有効になっていることを確認することを強くお勧めします。

転送中の暗号化の詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="encryption-at-rest"></a>保存時の暗号化
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>データ保護
Azure Files には、データがバックアップされて回復可能であり、セキュリティの脅威から保護されることを保証するための、多層化された方法が用意されています。

### <a name="soft-delete"></a>論理的な削除
ファイル共有の論理的な削除は、ファイル共有が誤って削除された場合に回復できるようにするストレージ アカウント レベルの設定です。 ファイル共有が削除された場合、完全に消去されるのではなく、論理的に削除された状態に移行します。 論理的に削除されたデータが完全に削除され、復旧できなくなるまでの時間を構成することができます。この保有期間中はいつでも共有の削除を取り消すことができます。 

ほとんどのファイル共有に対しては、論理的な削除を有効にすることをお勧めします。 共有の削除が一般的かつ望まれているワークフローでは、保持期間を短く設定するか、または論理的な削除をまったく有効にしないように決定できます。

論理的な削除の詳細については、[データの誤削除の防止](./storage-files-prevent-file-share-deletion.md)に関する記事を参照してください。

### <a name="backup"></a>バックアップ
[共有スナップショット](./storage-snapshots-files.md)を利用して、Azure ファイル共有をバックアップすることができます。これは、特定の時点の共有の読み取り専用のコピーです。 スナップショットは増分であり、以前のスナップショット以降に変更されたデータだけが含まれます。 ファイル共有ごとに最大 200 のスナップショットを保持し、最大 10 年間保存できます。 これらのスナップショットを取得するには、Azure portal、PowerShell、またはコマンドライン インターフェイス (CLI) を使用して手動で行うか、または [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用することができます。 スナップショットはファイル共有内に格納されます。つまり、ファイル共有を削除すると、スナップショットも削除されます。 スナップショット バックアップを誤削除から保護するために、共有に対して論理的な削除が有効になっていることを確認します。

[Azure ファイル共有の Azure Backup ](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)によって、スナップショットのスケジュール設定と保持期間が処理されます。 三世代 (GFS: grandfather-father-son) 機能は、日次、週次、月次、年次のスナップショットを取得できることを意味し、それぞれに個別の保持期間が設けられています。 また、Azure Backup によって論理的な削除の有効化が調整され、その中のいずれかのファイル共有がバックアップに構成されると、すぐにストレージ アカウント上で削除のロックが行われます。 最後に、顧客が自身のバックアップ資産の統合されたビューを利用できるように、Azure Backup には、特定の主な監視機能およびアラート機能が用意されています。

Azure portal 上で、Azure Backup を使用して、項目レベルおよび共有レベルの両方の復元を実行できます。 必要な作業は、復元ポイント (特定のスナップショット)、特定のファイルまたはディレクトリ (該当する場合)、復元先の場所 (元の場所または別の場所) を選択することだけです。 バックアップ サービスによって、スナップショット データのコピーが処理され、ポータル上に復元の進行状況が表示されます。

バックアップの詳細については、「[Azure ファイル共有のバックアップについて](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="protect-azure-files-with-microsoft-defender-for-storage"></a>Microsoft Defender for Storage を使用して Azure Files を保護する
Microsoft Defender for Storage には、ストレージ アカウント上で異常なアクティビティ (通常とは異なるアクセス試行など) が検出されたときにアラートを生成するセキュリティ インテリジェンスの追加レイヤーが用意されています。 また、マルウェアのハッシュ評価分析も実行され、既知のマルウェアに関するアラート通知が行われます。 Microsoft Defender for Storage は、Microsoft Defender for Cloud を使用してサブスクリプションまたはストレージのアカウント レベルで構成できます。

詳細については、「[Microsoft Defender for Storage の概要](../../security-center/defender-for-storage-introduction.md)」を参照してください。

## <a name="storage-tiers"></a>ストレージ層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="limitations"></a>制限事項
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>冗長性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>移行
多くの場合、組織に対して新しいファイル共有を確立するのではなく、既存のファイル共有をオンプレミスのファイル サーバーまたは NAS デバイスから Azure Files に移行します。 移行を成功させるには、シナリオに適した移行戦略とツールを選択することが重要です。 

[移行の概要に関する記事](storage-files-migration-overview.md)に、基本についての説明と、シナリオに適した移行ガイドを紹介する表が含まれています。

## <a name="next-steps"></a>次のステップ
* [Azure File Sync のデプロイの計画](../file-sync/file-sync-planning.md)
* [Azure Files のデプロイ方法](./storage-how-to-create-file-share.md)
<<<<<<< HEAD
* [Azure File Sync のデプロイ方法](storage-sync-files-deployment-guide.md)
=======
* [Azure ファイル同期のデプロイ方法](../file-sync/file-sync-deployment-guide.md)
>>>>>>> repo_sync_working_branch
* [シナリオに適した移行ガイドを見つけるには、移行の概要に関する記事をご覧ください。](storage-files-migration-overview.md)
