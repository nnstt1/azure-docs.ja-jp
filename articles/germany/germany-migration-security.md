---
title: Azure セキュリティ リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure セキュリティ リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: db8c13e214145df91f4b385f95c54e8084f3206c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029220"
---
# <a name="migrate-security-resources-to-global-azure"></a>セキュリティ リソースをグローバル Azure に移行する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

この記事には、Azure セキュリティ リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory の移行については、[ID の移行](./germany-migration-identity.md#azure-active-directory)に関するページを参照してください。

## <a name="key-vault"></a>Key Vault

Azure Key Vault の一部の機能は、Azure Germany からグローバル Azure に移行できません。

### <a name="encryption-keys"></a>暗号化キー

暗号化キーは移行できません。 ターゲット リージョンで新しいキーを作成した後、そのキーを使用してターゲット リソース (Azure Storage や Azure SQL Database など) を保護します。 古いリージョンから新しいリージョンにデータを安全に移行してください。

### <a name="application-secrets"></a>アプリケーション シークレット

アプリケーション シークレットは、証明書、ストレージ アカウント キー、およびその他のアプリケーション関連のシークレットです。 移行中に、まずグローバル Azure で新しいキー コンテナーを作成します。 次に、次のいずれかのアクションを完了します。

- 新しいアプリケーション シークレットを作成します。
- Azure Germany で現在のシークレットを読み取ってから、新しいコンテナーに値を入力します。

```powershell
Get-AzKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```

詳細情報:

- [Key Vault のチュートリアル](../key-vault/index.yml)を完了することによって知識を更新してください。
- [Key Vault の概要](../key-vault/general/overview.md)を確認してください。
- [Key Vault PowerShell コマンドレット](/powershell/module/az.keyvault/)を確認してください。

## <a name="vpn-gateway"></a>VPN Gateway

現時点では、Azure VPN Gateway インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、VPN Gateway の新しいインスタンスを作成および構成することをお勧めします。

現在の VPN Gateway 構成に関する情報は、ポータルまたは PowerShell を使用して収集できます。 PowerShell で、`Get-AzVirtualNetworkGateway*` で始まる一連のコマンドレットを使用します。

オンプレミスの構成を確実に更新するようにしてください。 また、Azure ネットワーク環境を更新した後、古い IP アドレス範囲の既存のルールもすべて削除してください。

詳細情報:

- [VPN Gateway のチュートリアル](../vpn-gateway/index.yml)を完了することによって知識を更新してください。
- [サイト間接続を作成する](../vpn-gateway/tutorial-site-to-site-portal.md)方法を学習してください。
- [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell コマンドレットを確認してください。
- [サイト間接続の作成](/archive/blogs/ralfwi/connecting-clouds)に関するブログの投稿を参照してください。
  
## <a name="application-gateway"></a>Application Gateway

現時点では、Azure Application Gateway インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しいゲートウェイを作成および構成することをお勧めします。

現在のゲートウェイ構成に関する情報は、ポータルまたは PowerShell を使用して収集できます。 PowerShell で、`Get-AzApplicationGateway*` で始まる一連のコマンドレットを使用します。

詳細情報:

- [Application Gateway のチュートリアル](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)を完了することによって知識を更新してください。
- [アプリケーション ゲートウェイを作成する](../application-gateway/quick-create-portal.md)方法を学習してください。
- [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell コマンドレットを確認してください。

## <a name="next-steps"></a>次のステップ

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)