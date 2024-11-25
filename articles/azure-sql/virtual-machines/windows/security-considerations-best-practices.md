---
title: セキュリティに関する考慮事項 | Microsoft Docs
description: このトピックでは、Azure 仮想マシンで実行されている SQL Server をセキュリティで保護するための一般的なガイダンスを示します。
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 327c2fa71fc8c95da654e7fca9450a8d0372e1ab
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301785"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

このトピックでは、Azure の仮想マシン (VM) の SQL Server インスタンスへのセキュリティで保護されたアクセスの確立に役立つ全体的なセキュリティ ガイドラインについて説明します。

Azure はいくつかの業界規制および標準に準拠しているため、ユーザーは仮想マシンで実行されている SQL Server を使用して、標準に準拠しているソリューションを構築できます。 Azure での法規制遵守に関する情報については、 [Azure トラスト センター](https://azure.microsoft.com/support/trust-center/)を参照してください。

このトピックで説明している手法に加えて、従来のオンプレミスのセキュリティ手法でのセキュリティ ベスト プラクティスと仮想マシンのセキュリティ ベスト プラクティスの両方を確認して実装することをお勧めします。 

## <a name="microsoft-defender-for-sql"></a>Microsoft Defender for SQL 

[Microsoft Defender for SQL](../../../security-center/defender-for-sql-introduction.md) では、脆弱性評価、セキュリティ アラートなど、Microsoft Defender for Cloud のセキュリティ機能が有効になります。 詳細については、[Microsoft Defender for SQL の有効化](../../../security-center/defender-for-sql-usage.md)に関するページを参照してください。 

## <a name="portal-management"></a>ポータル管理

[SQL Server VM を SQL IaaS 拡張機能 に登録](sql-agent-extension-manually-register-single-vm.md)した後、Azure Key Vault 統合の有効化や SQL 認証など、Azure portal の [SQL 仮想マシン リソース](manage-sql-vm-portal.md)を使用していくつかのセキュリティ設定を構成できます。 

さらに、[Microsoft Defender for SQL](../../../security-center/defender-for-sql-usage.md) を有効にした後、脆弱性評価やセキュリティ アラートなどの Defender for Cloud の機能を Azure portal の [SQL 仮想マシン リソース](manage-sql-vm-portal.md)で直接表示できます。 

詳細については、[ポータルでの SQL Server VM の管理](manage-sql-vm-portal.md)に関する記事を参照してください。 

## <a name="azure-key-vault-integration"></a>Azure Key Vault の統合 

透過的なデータ暗号化 (TDE)、列レベルの暗号化 (CLE)、バックアップ暗号化 など、SQL Server 暗号化機能が複数存在します。 これらの形態の暗号化では、暗号化に利用する暗号鍵を管理し、保存する必要があります。 Azure Key Vault サービスは、セキュリティを強化し、安全かつ可用性の高い場所で鍵を管理できるように設計されています。 SQL Server コネクタ を利用すると、SQL Server で Azure Key Vault にある鍵を利用できるようになります。
総合的な情報は、このシリーズ記事 ([チェックリスト](performance-guidelines-best-practices-checklist.md)、[VM のサイズ](performance-guidelines-best-practices-vm-size.md)、[ストレージ](performance-guidelines-best-practices-storage.md)、[HADR の構成](hadr-cluster-best-practices.md)、[ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)) の他の記事をご覧ください。 

詳細については、[Azure Key Vault の統合](azure-key-vault-integration-configure.md)に関する記事を参照してください。


## <a name="access-control"></a>アクセス制御 

SQL Server 仮想マシンを作成するときに、マシンと SQL Server へのアクセス権を持つユーザーを注意深く制御する方法を検討します。 一般に、次の作業を行う必要があります。

- SQL Server へのアクセスを必要とするアプリケーションとクライアントのみにアクセスを制限します。
- ユーザー アカウントとパスワードを管理するためのベスト プラクティスに従います。

次のセクションは、これらの点を考える上での提案を示しています。

## <a name="secure-connections"></a>セキュリティで保護された接続

ギャラリー イメージを使って SQL Server 仮想マシンを作成するときに、 **[SQL Server Connectivity]** オプションで、 **[ローカル (VM 内のみ)]** 、 **[プライベート (Virtual Network 内)]** 、または **[パブリック (インターネット)]** を選択できます。

![SQL Server 接続](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

セキュリティを最大限に強化するため、自分のシナリオで最も制限の厳しいオプションを選択します。 たとえば、同じ VM の SQL Server にアクセスするアプリケーションを実行している場合、最もセキュリティで保護された選択は **[ローカル]** です。 SQL Server へのアクセスを必要とする Azure アプリケーションを実行している場合、 **[プライベート]** では、指定された [Azure 仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)内の SQL Server への通信のみがセキュリティで保護します。 SQL Server VM にアクセスする [**パブリック** (インターネット)] が必要な場合、危険を回避するために、このトピックの他のベスト プラクティスに従ってください。

ポータルで選択されたオプションは、VM の[ネットワーク セキュリティ グループ](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) の受信セキュリティ ルールを使用して、仮想マシンへのネットワーク トラフィックを許可または拒否します。 SQL Server ポート (既定値 1433) へのトラフィックを許可するには、受信 NSG ルールを変更または新規作成します。 また、このポートでの通信を許可する、特定の IP アドレスを指定することもできます。

![ネットワーク セキュリティ グループ ルール](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

ネットワーク トラフィックを制限する NSG ルールに加え、仮想マシンで Windows ファイアウォールを使用することもできます。

クラシック デプロイ モデルでエンドポイントを使用している場合、使用しない仮想マシンのエンドポイントは削除します。 エンドポイントで ACL を使用する手順については、「 [エンドポイントの ACL の管理](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)」を参照してください。 これは、Azure Resource Manager を使用する VM には必要ありません。

最後に、Azure の仮想マシンの SQL Server データベース エンジンのインスタンスで、暗号化された接続オプションを有効にすることを検討してください。 署名付き証明書で SQL Server インスタンスを構成します。 詳細については、「[データベース エンジンへの暗号化接続の有効化](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)」および「[接続文字列の構文](/dotnet/framework/data/adonet/connection-string-syntax)」をご覧ください。

## <a name="encryption"></a>暗号化

マネージド ディスクでは、サーバー側暗号化と Azure Disk Encryption が提供されます。 [サーバー側暗号化](../../../virtual-machines/disk-encryption.md)では、保存時の暗号化が提供され、組織のセキュリティおよびコンプライアンス要件を満たすようにデータが保護されます。 [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) では、BitLocker または DM-Crypt テクノロジを使用し、Azure Key Vault と統合して OS とデータ ディスクの両方を暗号化します。 

## <a name="non-default-port"></a>既定以外のポート

既定では、SQL Server は既知のポート (1433) をリッスンします。 セキュリティ強化のために、既定以外のポート (1401 など) をリッスンするように、SQL Server を構成します。 Azure Portal で SQL Server のギャラリー イメージをプロビジョニングする場合、 **[SQL Server 設定]** ブレードでこのポートを指定できます。

プロビジョニングした後に、これを構成するには、次の 2 つのオプションがあります。

- Resource Manager VM の場合、[SQL 仮想マシン リソース](manage-sql-vm-portal.md#access-the-resource)から **[セキュリティ]** を選択できます。 ここには、ポートを変更するオプションがあります。

  ![ポータルの TCP ポートの変更](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- ポータルでプロビジョニングされていないクラシック VM または SQL Server VM の場合、VM にリモートで接続して、ポートを手動で構成できます。 構成手順については、「[特定の TCP ポートで受信待ちするようにサーバーを構成する](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)」を参照してください。 この手動の方法を使用する場合は、その TCP ポートで受信トラフィックを許可するために、Windows ファイアウォール ルールを追加する必要もあります。

> [!IMPORTANT]
> パブリック インターネットから SQL Server ポートに接続できる場合、既定以外のポートを指定することをお勧めします。

SQL Server が既定以外のポートをリッスンしている場合は、接続時のポートを指定する必要があります。 たとえば、サーバーの IP アドレスが 13.55.255.255 で、SQL Server がポート 1401 をリッスンしているシナリオを仮定します。 SQL Server に接続するには、接続文字列で `13.55.255.255,1401` を指定します。

## <a name="manage-accounts"></a>アカウントの管理

攻撃者に簡単にアカウント名やパスワードを推測されたくありません。 次のヒントを使用すると役立ちます。

- **Administrator** という名前ではない一意のローカル管理者アカウントを作成します。

- すべてのアカウントに複雑で強力なパスワードを使用します。 強力なパスワードを作成する方法の詳細については、「[強力なパスワードを作成する](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386)」を参照してください。

- 既定で、Azure は SQL Server 仮想マシンのセットアップ中に Windows 認証を選択します。 そのため、 **SA** ログインは無効となり、パスワードはセットアップによって割り当てられます。 **SA** ログインは使用せず、有効にしないことをお勧めします。 SQL ログインが必要な場合は、次の方法のいずれかを使用します。

  - **sysadmin** メンバーシップを持つ SQL アカウントを一意の名前で作成します。 プロビジョニング中に **SQL 認証** を有効にすると、ポータルからこの操作を行うことができます。

    > [!TIP] 
    > プロビジョニング中に SQL 認証を有効にしない場合は、認証モードを手動で **[SQL Server 認証モードと Windows 認証モード]** に変更する必要があります。 詳細については、「 [サーバーの認証モードの変更](/sql/database-engine/configure-windows/change-server-authentication-mode)」を参照してください。

  - **SA** ログインを使用する必要がある場合は、プロビジョニング後にログインを有効にし、新しい強力なパスワードを割り当てます。



## <a name="next-steps"></a>次のステップ

パフォーマンスに関するベスト プラクティスにも関心がある場合は、「[Azure Virtual Machines 上の SQL Server のパフォーマンスに関するベスト プラクティス](./performance-guidelines-best-practices-checklist.md)」をご覧ください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)」をご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.yml)に関するページをご覧ください。


詳細については、このシリーズの他の記事を参照してください。

- [クイック チェックリスト](performance-guidelines-best-practices-checklist.md)
- [VM サイズ](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Security](security-considerations-best-practices.md)
- [HADR の設定](hadr-cluster-best-practices.md)
- [ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)
