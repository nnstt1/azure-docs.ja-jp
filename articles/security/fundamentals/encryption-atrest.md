---
title: 保存時の Azure データの暗号化 - Azure セキュリティ
description: この記事では、保存時の Azure データの暗号化の概要、全体的な機能、一般的な考慮事項を提供しています。
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 174c7d552097bb3d15db198c9874755a919816e4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428332"
---
# <a name="azure-data-encryption-at-rest"></a>保存時の Azure データの暗号化

Microsoft Azure には、会社のセキュリティとコンプライアンスのニーズに合わせてデータを保護するためのツールが含まれています。 このドキュメントでは次に重点を置いています。

- Microsoft Azure 全体で保存データを保護する方法
- データ保護実装を構成しているさまざまなコンポーネントについて説明する
- さまざまなキー管理保護アプローチの長所と短所を確認する

保存中データの暗号化は、一般的なセキュリティ要件です。 Azure では、組織は、リスクやカスタム キー管理ソリューションのコストなしで、保存データを暗号化できます。 組織は、Azure のみを使用して保存データの暗号化を完全に管理することを選択できます。 さらに、組織には、暗号化または暗号化キーを厳密に管理するさまざまなオプションが提供されています。

## <a name="what-is-encryption-at-rest"></a>保存データの暗号化とは

暗号化は、データの機密保護に使用されるデータを安全に符号化することです。 Azure の保存時の暗号化では、対称暗号化を使用して暗号化し、単純な概念モデルに基づいて大量のデータをすばやく復号化します。

- データがストレージに書き込まれるときに、対称暗号化キーを使用してデータが暗号化されます。
- メモリで使用する準備を行ときに、同じ暗号化キーを使用してデータが複合化されます。
- データはパーティション分割されることがあり、パーティションごとに異なるキーが使用されることがあります。
- キーは、ID ベースのアクセス制御と監査ポリシーが適用される、セキュリティで保護された場所に保存する必要があります。 安全ではない場所に保管されているデータ暗号化キーは、安全な場所に保管されているキー暗号化キーで暗号化されます。

実際には、キーの管理と制御のシナリオに加え、拡張性と可用性を保証するには、追加の構造が必要です。 Microsoft Azure Encryption at Rest の概念とコンポーネントは次のとおりです。

## <a name="the-purpose-of-encryption-at-rest"></a>保存時の暗号化の目的

保存時の暗号化は、格納されているデータ (保存されているデータ) を保護します。 保存データへの攻撃では、データが保存されているハードウェアへの物理的なアクセスを取得し、その中に含まれるデータが侵害されます。 このような攻撃では、メンテナンス中にサーバーのハード ドライブの保護を誤り、攻撃者にハード ドライブを削除する機会を与えることがあります。 攻撃者は、この後、攻撃者があやつるハード ドライブをコンピューターに戻し、データにアクセスします。

Encryption at Rest は、データをディスク上で確実に暗号化することで、暗号化されていないデータに攻撃者がアクセスできないよう設計されています。 攻撃者は、暗号化されたデータを含むハード ドライブを入手しても、暗号化キーがないと、データを読み取るためには暗号化を破る必要があります。 この攻撃は、ハード ドライブの暗号化されていないデータにアクセスするよりも大幅に複雑で、多くのリソースを消費します。 このため、保存時に暗号化することは、多くの組織の高優先度要件として強く推奨されます。

保存時の暗号化は、組織のデータ ガバナンスとコンプライアンス活動の要件にもなります。 HIPAA、PCI、FedRAMP などの業界および政府規制には、データ保護と暗号化要件について、具体的な保護対策が規定されています。 保存時の暗号化は、いくつかの規制では、規制に準拠するための必須対策と規定されています。 FIPS 140-2 の検証に対する Microsoft のアプローチについて詳しくは、「[連邦情報処理規格 (FIPS) 文書 140-2](/microsoft-365/compliance/offering-fips-140-2)」を参照してください。

コンプライアンスと規制の要件に対応することに加えて、保存時の暗号化によって、多層防御の保護が提供されます。 Microsoft Azure では、サービス、アプリケーション、およびデータのために、準拠しているプラットフォームを提供します。 また、包括的な設備と物理的セキュリティ、データ アクセスの制御、および監査を提供します。 ただし、他のセキュリティ対策の 1 つが失敗した場合に「一部が重複する」セキュリティ対策を提供することが重要であり、保存時の暗号化は、そのようなセキュリティ対策となります。

Microsoft は、クラウド サービス全体にわたって保存時の暗号化オプションを提供し、お客様が暗号化キーとキーの使用ログを管理できるようにすることに注力しています。 Microsoft はさらに、既定でお客様の保存データがすべて暗号化されるよう取り組んでいます。

## <a name="azure-encryption-at-rest-components"></a>Azure Encryption at Rest のコンポーネント

前述のように、保存時の暗号化の目的は、ディスクに保存されているデータを秘密暗号化キーで暗号化することです。 この目標を達成するためには、セキュリティ保護されたキーの作成、ストレージ、アクセス制御および暗号化キーの管理を提供する必要があります。 詳細情報が異なる場合がありますが、Azure services Encryption at Rest で実装されている暗号化サービスは、次の図に示す用語で説明できます。

![Components](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

暗号化キーの保存場所と、これらのキーへのアクセス制御は、保存時暗号化モデルの中心になります。 キーは高度のセキュリティで保護される必要がありますが、特定のユーザーによって管理でき、特定のサービスで利用できる必要があります。 Azure サービスの場合は、サービス全体で共通した管理エクスペリエンスを提供するキー保存のソリューションである、Azure Key Vault をお勧めします。 キーはキー コンテナーで保存、管理され、キー コンテナーへのアクセスをユーザーまたはサービスに提供できます。 Azure Key Vault では、ユーザーが管理する暗号化キーのシナリオで使用するための、ユーザーによるキーの作成またはユーザー キーのインポートがサポートされています。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Key Vault に保存されたキーを、その管理、または保存時の暗号化および複合化時にアクセスして使用するためのアクセス許可を、Azure Active Directory アカウントに付与できます。

### <a name="envelope-encryption-with-a-key-hierarchy"></a>キー階層を使用したエンベロープ暗号化

保存時の暗号化の実装には、複数の暗号化キーが使用されます。 Azure Key Vault に暗号化キーを格納することで、キーの安全なアクセスと集中管理が可能になります。 ただし、データ操作のたびに Key Vault とやりとりするより、暗号化キーにサービス ローカル アクセスするほうが一括の暗号化/復号の場合は効率的です。暗号化が強化され、パフォーマンスが改善されます。 1 つの暗号化キーに使用を制限すると、キーが侵害されるリスクと、キーを置き換える必要があった場合の再暗号化のコストを低減できます。 Azure の保存時暗号化モデルでは、キー暗号化キーによってデータ暗号化キーを暗号化するエンベロープ暗号化を使用します。 このモデルでは、パフォーマンスとセキュリティの要件により効果的に対応できるキー階層を形成します。

- **データ暗号化キー (DEK)** - データのパーティションまたはブロックを暗号化するために使用される対称 AES256 キー (単にデータ キーとも呼ばれることもあります)。  1 つのリソースに、複数のパーティションおよび複数のデータ暗号化キーを保有できます。 データの各ブロックを異なるキーで暗号化することによって、暗号化分析攻撃がより困難になります。 また、データを暗号化および復号化するサービスに対して DEK をローカルに保持すると、パフォーマンスが最大になります。
- **キー暗号化キー (KEK)** - エンベロープ暗号化を使用してデータ暗号化キーを暗号化するために使用される暗号化キー (ラッピングとも呼ばれます)。 Key Vault を離れることがないキーの暗号化キーを使用すると、データの暗号化キー自体が暗号化され、管理できます。 KEK へのアクセス権を持つエンティティは、DEK を必要とするエンティティとは異なるエンティティに設定できます。 エンティティは、DEK へのアクセスを仲介して、各 DEK の特定のパーティションへのアクセスを制限できます。 KEK は DEK の暗号化を解除するために必要なものであるため、お客様は KEK を無効にすることで DEK とデータを暗号的に消去できます。

リソース プロバイダーとアプリケーション インスタンスでは、暗号化されたデータ暗号化キーをメタデータとして格納します。 キー暗号化キーにアクセスできるエンティティのみが、これらのデータ暗号化キーを復号できます。 キー保存のさまざまなモデルがサポートされます。 詳細については、「[データ暗号化モデル](encryption-models.md)」を参照してください。

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft クラウド サービスでの保存時の暗号化

Microsoft Cloud サービスは、IaaS、PaaS、SaaS の3 つのクラウド モデルすべてで使用されます。 以下は、各モデルにこれらがどのように対応するかを示した例です。

- Software as a Server (SaaS) と呼ばれるソフトウェア サービスでは、Microsoft 365 など、クラウドで提供されるアプリケーションが利用できます。
- プラットフォーム サービスでは、お客様のアプリケーションのストレージ、分析、サービス バス機能などにクラウドが使用されています。
- インフラストラクチャ サービス、別称サービスとしてのインフラストラクチャ (IaaS) では、顧客がクラウドでホスティングされているオペレーティング システムやアプリケーションをデプロイし、他のクラウド サービスを活用することもあります。

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS 顧客ためのの保存時の暗号化

サービスとしてのソフトウェア (SaaS) の顧客は、通常は、保存時の暗号化を有効化しているか、各サービスで利用できます。 Microsoft 365 には、保存時の暗号化を、お客様が確認または有効化できるオプションがいくつか用意されています。 Microsoft 365 サービスの詳細については、[Microsoft 365 でのデータ暗号化](/microsoft-365/compliance/encryption)に関するページを参照してください。

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS 顧客のための保存時の暗号化

サービスとしてのプラットフォーム (PaaS) 顧客のデータは通常、Blob Storage などのストレージ サービスに存在しますが、仮想マシンなどのアプリケーションの実行環境でキャッシュまたは格納される場合もあります。 使用できる保存時の暗号化のオプションを確認するには、[データ暗号化モデル: サポート サービスの表](encryption-models.md#supporting-services)で、使用しているストレージとアプリケーション プラットフォームを調べてください。

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS 顧客のための保存時の暗号化

サービスとしてのインフラストラクチャ (IaaS) のユーザーは、さまざまなサービスとアプリケーションを使用できます。 IaaS サービスは、 Azure Disk Encryption を使用して、Azure がホスティングされている仮想マシンや VHD で保存時の暗号化を有効化できます。

#### <a name="encrypted-storage"></a>暗号化されたストレージ

PaaS と同様、IaaS ソリューションは、保存時のデータの暗号化を保存する他の Azure サービスを利用できます。 この場合は、それぞれ使用している Azure サービスによって提供されるように、保存時の暗号化の暗号化を有効にできます。 [データ暗号化モデル: サポート サービスの表](encryption-models.md#supporting-services)には、サポートされている保存時の暗号化の主要なストレージ、サービス、アプリケーション プラットフォームとモデルが列挙されています。

#### <a name="encrypted-compute"></a>暗号化されたコンピューティング

すべてのマネージド ディスク、スナップショット、イメージは、サービス管理キーを使用する Storage Service Encryption を使って暗号化されます。 より完全な保存時暗号化ソリューションによって、暗号化されていない形式でデータが保持されることがないことが保証されます。 仮想マシン上でのデータの処理中に、Windows のページ ファイルや Linux のスワップ ファイル、クラッシュ ダンプ、またはアプリケーション ログにデータを保持することができます。 IaaS アプリケーションは、Azure IaaS 仮想マシン (Windows または Linux) および仮想ディスクで Azure Disk Encryption を使用して、このデータが保存時に暗号化されているかどうかを確認できます。

#### <a name="custom-encryption-at-rest"></a>保存データのカスタム暗号化

IaaS アプリケーションでは、可能な限り、Azure Disk Encryption および使用される Azure サービスによって提供される 保存時暗号化オプションを活用することをお勧めします。 不規則な暗号化の要件または Azure を使用しないストレージなどの場合、IaaS アプリケーションの開発者は、保存時の暗号化を独自に実装する必要があります。 IaaS ソリューションの開発者は、特定の Azure コンポーネントを活用することで Azure の管理機能と顧客からの期待に応える機能を統合できます。 特に、開発者は、Azure Key Vault サービスを使用することで、セキュリティ保護されたキーの保存場所と、ほとんどの Azure プラットフォーム サービスで提供されている一貫したキー管理オプションを顧客に提供できます。 また、カスタム ソリューションでは、Azure のマネージド サービス ID を使用すれば、サービス アカウントが暗号化キーにアクセスできるようになります。 Azure Key Vault およびマネージド サービス ID の管理に関する開発者向け情報については、それぞれの SDK を参照してください。

## <a name="azure-resource-providers-encryption-model-support"></a>Azure リソース プロバイダー暗号化モデルのサポート

各 Microsoft Azure サービスでは、1 つ以上の保存時暗号化モデルをサポートしています。 ただし、一部のサービスでは、1 つ以上の暗号化モデルが適用できない場合があります。 ユーザー管理の主要なシナリオをサポートするサービスでは、Azure Key Vault が主要な暗号化キーに対してサポートしているキーの種類のうち、一部のみをサポートする場合があります。 さらに、サービスによって異なるスケジュールでこれらのシナリオとキーの種類へのサポートがリリースされることがあります。 このセクションでは、このドキュメントの作成時点での、主な Azure データ ストレージ サービスの保存時暗号化サポートについて説明します。

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

サービスとしての Azure インフラストラクチャ (IaaS) 機能を使用しているお客様は、Azure Disk Encryption を通して IaaS 仮想マシンの保存時の暗号化を実行できます。 Azure Disk Encryption の詳細については、[Azure Disk Encryption のドキュメント](./azure-disk-encryption-vms-vmss.md)を参照してください。

#### <a name="azure-storage"></a>Azure Storage

すべての Azure Storage サービス (Blob Storage、Queue Storage、Table Storage、Azure Files) では、サーバー側の保存データの暗号化がサポートされます。一部のサービスではさらにユーザー管理キーとクライアント側の暗号化がサポートされます。

- サーバー側:すべての Azure Storage Services は、既定でサービス管理キーを使用してサーバー側の暗号化を有効にしています。この処理はアプリケーションに対して透過的です。 詳細については、「[保存データ向け Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md)」をご覧ください。 Azure Blob Storage と Azure Files は、Azure Key Vault での RSA 2048 ビット ユーザー管理キーもサポートしています。 詳細については、「[ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する](../../storage/common/customer-managed-keys-configure-key-vault.md)」を参照してください。
- クライアント側:Azure BLOB、Azure テーブル、および Azure キューは、クライアント側の暗号化をサポートしています。 クライアント側の暗号化を使用した場合、お客様は暗号化された BLOB としてデータをアップロードします。 キー管理は、ユーザーによって行われます。 詳しくは、「[Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](../../storage/common/storage-client-side-encryption.md)」をご覧ください。

#### <a name="azure-sql-database"></a>Azure SQL データベース

Azure SQL Database は現在、Microsoft が管理するサービス側とクライアント側の暗号化のシナリオで、保存時の暗号化をサポートしています。

サーバー側の暗号化は Transparent Data Encryption と呼ばれる SQL 機能を通して提供されています。 Azure SQL Database ユーザーが有効化すると、TDE キーは自動的に作成および管理されます。 保存時の暗号化は、データベース レベルおよびサーバー レベルで有効にすることができます。 2017 年 6 月の時点で [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) は、新しく作成されたデータベースで既定で有効に設定されています。 Azure SQL Database は、Azure Key Vault での RSA 2048 ビット ユーザー管理キーをサポートしています。 詳細については、[Azure SQL Database と Azure SQL Data Warehouse での Bring Your Own Key (BYOK) のサポートによる Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) に関するページを参照してください。

Azure SQL Database データのクライアント側の暗号化は、[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 機能を通してサポートされています。 Always Encrypted では、クライアントによって作成、保存されたキーが使用されます。 ユーザーは、Windows 証明書ストア、Azure Key Vault、またはローカルのハードウェア セキュリティ モジュール (HSM) にマスター キーを格納できます。 SQL ユーザーは、SQL Server Management Studio を使用して、どの列で何を使用して暗号化するかを選択できます。

## <a name="conclusion"></a>まとめ

Azure サービス内で保存される顧客データの保護は、Microsoft にとってきわめて重要です。 すべての Azure ホステッド サービスは、保存時暗号化オプションを提供することを目的としています。 Azure サービスでは、サービス管理キー、顧客管理キー、クライアント側の暗号化のいずれかがサポートされます。 Azure サービスは、保存時の暗号化の可用性を大きく広げており、今後はプレビューや一般的な可用性のための新しいオプションを提供することが計画されています。

## <a name="next-steps"></a>次のステップ

- 「[データ暗号化モデル](encryption-models.md)」を参照して、サービス管理キーとカスタマー マネージド キーの詳細について確認します。
- データの暗号化に付随する脅威を軽減するため、Azure でどのように[二重暗号化](double-encryption.md)が使用されているかを確認します。
- ハードウェアとファームウェアの構築、統合、運用化、および修復のパイプラインを通過するホストの[プラットフォームの整合性とセキュリティ](platform.md)を確保するために Microsoft が何を行っているか確認します。
