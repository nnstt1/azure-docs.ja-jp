---
title: セキュリティに関する推奨事項
description: セキュリティに関する推奨事項を実装することにより、共有責任モデルに記載されたセキュリティに関する義務を果たすことができます。 アプリのセキュリティを強化します。
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 42eaec619097d673c77b6b233a2f2316605971b6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348428"
---
# <a name="security-recommendations-for-app-service"></a>App Service のセキュリティに関する推奨事項

この記事には、Azure App Service のセキュリティに関する推奨事項が含まれています。 これらの推奨事項を実装することにより、お客様は共同責任モデルに記載されたセキュリティに関する義務を果たすことができ、お客様の Web アプリ ソリューションの全体的なセキュリティが向上します。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みについて詳しくは、「[Azure インフラストラクチャのセキュリティ](../security/fundamentals/infrastructure.md)」をご覧ください。

## <a name="general"></a>全般

| 推奨 | 説明 |
|-|-|----|
| 常に最新の状態に保つ | サポート対象プラットフォーム、プログラミング言語、プロトコル、およびフレームワークの最新バージョンを使用します。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| 推奨 | 説明 |
|-|----|
| 匿名アクセスを無効にする | 匿名の要求をサポートする必要がない限り、匿名アクセスを無効にします。 Azure App Service の認証オプションの詳細については、「[Azure App Service での認証および認可](overview-authentication-authorization.md)」を参照してください。|
| 認証を必須にする | 可能な限り、コードを記述する代わりに App Service の認証モジュールを使用して、認証と認可を処理します。 「[Azure App Service での認証および認可](overview-authentication-authorization.md)」を参照してください。 |
| 認証済みのアクセスを使用してバックエンド リソースを保護する | ユーザーの ID を使用するかアプリケーション ID を使用して、バックエンド リソースに対して認証を行います。 アプリケーション ID の使用を選択した場合は、[マネージド ID](overview-managed-identity.md) を使用します。
| クライアント証明書認証を必須にする | クライアント証明書認証では、指定された証明書を使用して認証できるクライアントからの接続のみを許可することにより、セキュリティが向上します。 |

## <a name="data-protection"></a>データ保護

| 推奨 | 説明 |
|-|-|
| HTTP を HTTPS にリダイレクトする | 既定では、クライアントは HTTP と HTTPS のどちらを使用しても Web アプリに接続できます。 HTTPS では、SSL/TLS プロトコルの使用によりセキュリティで保護された接続 (暗号化と認証の両方) が提供されるため、HTTP を HTTPS にリダイレクトすることをお勧めします。 |
| Azure リソースへの通信を暗号化する | アプリから [SQL Database](https://azure.microsoft.com/services/sql-database/) や [Azure Storage](../storage/index.yml) などの Azure リソースへの接続では、接続が Azure 内にとどまります。 接続は Azure 内の共有ネットワークを通過するため、すべての通信を常に暗号化する必要があります。 |
| 可能な限り最新の TLS バージョンを使用する | 2018 年以降、新しい Azure App Service アプリでは TLS 1.2 が使用されています。 新しいバージョンの TLS では、以前のバージョンのプロトコルに比べてセキュリティが強化されています。 |
| FTPS を使用する | App Service は、ファイルの展開に FTP と FTPS の両方をサポートしています。 可能な限り FTP ではなく FTPS を使用します。 これらのプロトコルの 1 つまたは両方が使用されていない場合は、[無効にする](deploy-ftp.md#enforce-ftps)ことをお勧めします。 |
| アプリケーション データをセキュリティで保護する | データベースの資格情報、API トークン、秘密キーなどのアプリケーション シークレット情報をコードや構成ファイルに保存しないでください。 一般に受け入れられているのは、選択した言語の標準パターンを使用して[環境変数](https://wikipedia.org/wiki/Environment_variable)としてアクセスする方法です。 Azure App Service では、[アプリ設定](./configure-common.md)と[接続文字列](./configure-common.md)を通して環境変数を定義できます。 アプリ設定と接続文字列は、Azure で暗号化されて格納されます。 アプリ設定は、アプリの起動時にアプリのプロセス メモリに挿入される前にのみ、暗号化が解除されます。 暗号化キーは定期的に回転されます。 また、高度なシークレット管理のために、Azure App Service アプリを [Azure Key Vault](../key-vault/index.yml) と統合することもできます。 [マネージド ID を使用してキー コンテナーにアクセスする](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)ことで、App Service アプリは必要なシークレットに安全にアクセスできます。 |

## <a name="networking"></a>ネットワーク

| 推奨 | 説明 |
|-|-|
| 静的 IP の制限を使用する | Windows 上の Azure App Service では、アプリへのアクセスを許可されている IP アドレスの一覧を定義できます。 許可一覧には、個々 の IP アドレスまたはサブネット マスクによって定義された IP アドレスの範囲を含めることができます。 詳細については、「[Azure App Service 静的 IP 制限](app-service-ip-restrictions.md)」を参照してください。  |
| Isolated 価格レベルを使用する | Isolated 価格レベルを除くすべての価格レベルでは、Azure App Service の共有ネットワーク インフラストラクチャ上でアプリが実行されます。 Isolated 価格レベルでは、専用の [App Service Environment](environment/intro.md)内でアプリを実行することで完全なネットワークの分離を実現しています。 App Service Environment は、[Azure Virtual Network](../virtual-network/index.yml) の独自のインスタンスで実行されます。|
| オンプレミス リソースへのアクセス時にセキュリティで保護された接続を使用する | オンプレミス リソースへの接続には、[ハイブリッド接続](app-service-hybrid-connections.md)、[仮想ネットワーク統合](./overview-vnet-integration.md)、または [App Service Environment](environment/intro.md)を使用できます。 |
| 受信ネットワーク トラフィックへの露出を制限する | ネットワーク セキュリティ グループを使用すると、ネットワーク アクセスを制限し、公開するエンドポイントの数を制御できます。 詳細については、[App Service Environment への受信トラフィックを制御する方法](environment/app-service-app-service-environment-control-inbound-traffic.md)に関する記事を参照してください。 |

## <a name="monitoring"></a>監視

| 推奨 | 説明 |
|-|-|
|Microsoft Defender for Cloud の Microsoft Defender for App Service を使用する | [Microsoft Defender for App Service](../security-center/defender-for-app-service-introduction.md) は Azure App Service にネイティブ統合されています。 App Service プランの対象となるリソースが Defender for Cloud によって評価され、その結果に基づき、セキュリティ上の推奨事項が生成されます。 [こちらの推奨事項]()../security-center/recommendations-reference.md#appservices-recommendations) にある詳しい手順を利用し、App Service リソースを強化してください。 Microsoft Defender for Cloud からは脅威防止機能も提供され、無数の脅威を検出できます。事前攻撃からコマンド & コントロールまで、ほぼすべての MITRE ATT&CK 作戦に対応しています。 Azure App Service アラートの完全な一覧については、[Microsoft Defender for App Service のアラート](../security-center/alerts-reference.md#alerts-azureappserv)に関するページを参照してください。|

## <a name="next-steps"></a>次のステップ

アプリケーション プロバイダーに追加のセキュリティ要件があるかどうかを確認する。 セキュリティで保護されたアプリケーションの開発の詳細については、[セキュリティで保護された開発に関するドキュメント](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)を参照してください。
