---
title: キーについて - Azure Key Vault
description: キーに関する Azure Key Vault の REST インターフェイスと開発者の詳細の概要です。
services: key-vault
author: mbaldwin
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: mbaldwin
ms.openlocfilehash: d89cd4faa67f769f01c0d0a1c1288b6dc8776aed
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722622"
---
# <a name="about-keys"></a>キーについて

Azure Key Vault には、暗号化キーを格納および管理するための 2 種類のリソースが用意されています。 コンテナーは、ソフトウェアで保護されたキーと HSM (ハードウェア セキュリティ モジュール) で保護されたキーをサポートしています。 マネージド HSM は、HSM で保護されたキーのみをサポートしています。 

|リソースの種類|キーの保護方法|データプレーン エンドポイント ベース URL|
|--|--|--|
<<<<<<< HEAD
| **コンテナー** | ソフトウェアによる保護<br/><br/>および<br/><br/>HSM で保護された (Premium SKU の場合)</li></ul> | https://{vault-name}.vault.azure.net |
| **マネージド HSM ** | HSM で保護された | https://{hsm-name}.managedhsm.azure.net |
=======
| **資格情報コンテナー** | ソフトウェアによる保護<br/><br/>および<br/><br/>HSM で保護された (Premium SKU の場合)</li></ul> | https://{vault-name}.vault.azure.net |
| **マネージド HSM** | HSM で保護された | https://{hsm-name}.managedhsm.azure.net |
>>>>>>> repo_sync_working_branch
||||

- **コンテナー** - コンテナーは、最も一般的なクラウド アプリケーションのシナリオに適した、低コスト、容易なデプロイ、マルチテナント、ゾーン回復性 (使用可能な場合)、高可用性の、キー管理ソリューションを提供します。
- **マネージド HSM** - マネージド HSM は、暗号化キーを格納および管理するために、シングルテナント、ゾーン回復性 (使用可能な場合)、高可用性の HSM を提供します。 価値の高いキーを処理するアプリケーションと使用シナリオに最適です。 最も厳密なセキュリティ、コンプライアンス、および規制の要求を満たすためにも役立ちます。 

> [!NOTE]
> また、コンテナーを使用すると、暗号化キーの他に、シークレット、証明書、ストレージ アカウント キーなど、いくつかの種類のオブジェクトを格納および管理することもできます。

Key Vault の暗号化キーは、JSON Web Key (JWK) オブジェクトとして表されます。 JavaScript Object Notation (JSON) および JavaScript Object Signing and Encryption (JOSE) の仕様は、次のとおりです。

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](https://datatracker.ietf.org/doc/html/draft-jones-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](https://datatracker.ietf.org/doc/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

基本の JWK および JWA の仕様は、Azure Key Vault および Managed HSM の実装に固有のキーの種類も有効にするように拡張されます。 

HSM で保護されたキー (HSM キーとも呼ばれます) は、HSM (ハードウェア セキュリティ モジュール) で処理され、常に HSM の保護境界内に留まります。 

- コンテナーは、**FIPS 140-2 レベル 2** で検証された HSM を使用して、共有 HSM バックエンド インフラストラクチャ内の HSM キーを保護します。 
- マネージド HSM は、**FIPS 140-2 レベル 3** で検証された HSM モジュールを使用して、キーを保護します。 各 HSM プールは、独自の[セキュリティ ドメイン](../managed-hsm/security-domain.md)を備えた、分離されたシングルテナント インスタンスであり、同じハードウェア インフラストラクチャを共有する他のすべての HSM から完全に分離された暗号化を提供します。

これらのキーは、シングルテナントの HSM プールで保護されます。 RSA、EC、および対称キーのインポートを、ソフト形式で、またはサポートされている HSM デバイスからのエクスポートにより、行うことができます。 HSM プールでキーを生成することもできます。 [BYOK (Bring Your Own Key) の仕様](../keys/byok-specification.md)で説明されている方法を使用して HSM キーをインポートすると、Managed HSM プールへの安全なトランスポート キー マテリアルが有効になります。 

地理的境界について詳しくは、[Microsoft Azure トラスト センター](https://azure.microsoft.com/support/trust-center/privacy/)をご覧ください。

## <a name="key-types-and-protection-methods"></a>キーの種類と保護方法

Key Vault は、RSA および EC キーをサポートしています。 マネージド HSM は、RSA、EC、および対称キーをサポートしています。 

### <a name="hsm-protected-keys"></a>HSM で保護されたキー

|キーの種類|コンテナー (Premium SKU のみ)|マネージド HSM|
|--|--|--|
|**EC-HSM**:楕円曲線キー | サポートされています (P-256、P-384、P-521、P-256K)| サポートされています (P-256、P-256K、P-384、P-521)|
|**RSA-HSM**:RSA キー|サポートされています (2048 ビット、3072 ビット、4096 ビット)|サポートされています (2048 ビット、3072 ビット、4096 ビット)|
|**oct-HSM**: 対称キー|サポートされていません|サポートされています (128 ビット、192 ビット、256 ビット)|
|||

### <a name="software-protected-keys"></a>ソフトウェアで保護されるキー

|キーの種類|コンテナー|マネージド HSM|
|--|--|--|
**RSA**:"ソフトウェアで保護される" RSA キー|サポートされています (2048 ビット、3072 ビット、4096 ビット)|サポートされていません
**EC**:"ソフトウェアで保護される" 楕円曲線キー |サポートされています (P-256、P-384、P-521、P-256K)|サポートされていません
|||

### <a name="compliance"></a>コンプライアンス

|キーの種類と出力先|コンプライアンス|
|---|---|
|コンテナー内のソフトウェアで保護されたキー (Premium および Standard SKU) | FIPS 140-2 レベル 1|
|コンテナー内の HSM で保護されたキー (Premium SKU)| FIPS 140-2 Level 2|
|マネージド HSM 内の HSM で保護されたキー|FIPS 140-2 レベル 3|
|||

キーの種類、アルゴリズム、操作、属性、タグの詳細については、「[キーの種類、アルゴリズム、および操作](about-keys-details.md)」を参照してください。

## <a name="usage-scenarios"></a>使用シナリオ

| 使用する場合 | 例 |
|--------------|-------------|
| カスタマー マネージド キーを使用した統合リソース プロバイダー向けの Azure サーバー側のデータ暗号化 | - [Azure Key Vault でのカスタマー マネージド キーを使用したサーバー側暗号化](../../security/fundamentals/encryption-models.md#server-side-encryption-using-customer-managed-keys-in-azure-key-vault) |
| クライアント側のデータ暗号化 | - [クライアント側の暗号化と Azure Key Vault](../../storage/common/storage-client-side-encryption.md?tabs=dotnet)|
| キーのない TLS | - キーの[クライアント ライブラリ](../general/client-libraries.md#client-libraries-per-language-and-object)を使用する |

## <a name="next-steps"></a>次のステップ
- [Key Vault について](../general/overview.md)
- [Managed HSM について](../managed-hsm/overview.md)
- [シークレットについて](../secrets/about-secrets.md)
- [証明書について](../certificates/about-certificates.md)
- [Key Vault REST API の概要](../general/about-keys-secrets-certificates.md)
- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)