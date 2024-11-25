---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7c06903720db4315bad04e88dfdb9c7cad604697
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820857"
---
ユーザー独自のキーを使用して、各マネージド ディスクのレベルで暗号化を管理できます。 カスタマー マネージド キーを使用したサーバー側でのマネージド ディスクの暗号化により、Azure Key Vault との統合されたエクスペリエンスが提供されます。 [ご使用の RSA キー](../articles/key-vault/keys/hsm-protected-keys.md)を Key Vault にインポートするか、Azure Key Vault で新しい RSA キーを生成することができます。 

Azure マネージド ディスクは[エンベロープ暗号化](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)を使用して、暗号化と暗号化解除を完全に透過的な方法で処理します。 これは、[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 ベースのデータ暗号化キー (DEK) を使用してデータを暗号化します。このキーは、ご使用のキーを使用して保護されます。 ストレージ サービスは、データ暗号化キーを生成し、RSA 暗号化を使用してカスタマー マネージド キーで暗号化します。 エンベロープ暗号化を使用すると、VM に影響を与えることなく、コンプライアンス ポリシーに従って定期的にキーをローテーション (変更) することができます。 キーを交換すると、ストレージ サービスによって、新しいカスタマー マネージド キーを使用してデータ暗号化キーが再暗号化されます。 

#### <a name="full-control-of-your-keys"></a>キーのフル コントロール

DEK の暗号化と暗号化解除にキーを使用するための、Key Vault 内のマネージド ディスクへのアクセス権の付与を行う必要があります。 これにより、データとキーを完全に制御できます。 任意の時点で、キーを無効にしたり、マネージド ディスクへのアクセスを取り消したりすることができます。 また、Azure Key Vault 監視で暗号化キーの使用状況を監査して、マネージド ディスクまたは他の信頼された Azure サービスのみがキーにアクセスしていることを確認することもできます。

キーを無効にするか削除すると、そのキーを使用しているディスクを持つ VM はすべて自動的にシャットダウンされます。 この後、キーが再度有効になるか、新しいキーを割り当てない限り、VM は使用できません。    

次の図は、マネージド ディスクで Azure Active Directory と Azure Key Vault を使用して、カスタマー マネージド キーを使って要求を行う方法を示しています。

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="マネージド ディスクとカスタマー マネージド キーのワークフロー。管理者は Azure Key Vault を作成し、ディスク暗号化セットを作成してから、ディスク暗号化セットを設定します。このセットは VM に関連付けられています。これにより、ディスクは Azure AD を使用して認証を行うことができます":::

次の一覧では、図について詳しく説明します。

1. Azure Key Vault の管理者が Key Vault リソースを作成します。
1. Key Vault 管理者は、RSA キーを Key Vault にインポートするか、Key Vault 内で新しい RSA キーを生成します。
1. この管理者は、Azure Key Vault ID とキー URL を指定して、ディスク暗号化セット リソースのインスタンスを作成します。 ディスク暗号化セットは、マネージド ディスクのキー管理を簡略化するために導入された新しいリソースです。 
1. ディスク暗号化セットを作成すると、[システムによって割り当てられたマネージド ID](../articles/active-directory/managed-identities-azure-resources/overview.md) が Azure Active Directory (AD) に作成され、ディスク暗号化セットに関連付けられます。 
1. Azure Key Vault の管理者はその後、Key Vault 内での操作を実行するためのアクセス許可をマネージド ID に付与します。
1. VM ユーザーは、ディスク暗号化セットにディスクを関連付けることによってディスクを作成します。 また、VM ユーザーは、既存のリソースに対するカスタマー マネージド キーを使用したサーバー側の暗号化を、ディスク暗号化セットへのリソースの関連付けによって有効にすることもできます。 
1. マネージド ディスクは、マネージド ID を使用して Azure Key Vault に要求を送信します。
1. データの読み取りまたは書き込みを行うために、マネージド ディスクは Azure Key Vault に要求を送信して、データの暗号化と暗号化解除を実行するためにデータ暗号化キーを暗号化 (ラップ) および暗号化解除 (ラップ解除) します。 

カスタマー マネージド キーへのアクセスを取り消すには、[Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) および [Azure Key Vault CLI](/cli/azure/keyvault) に関する記事をご覧ください。 アクセスを取り消すと、Azure Storage が暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全データへのアクセスが事実上ブロックされます。

#### <a name="automatic-key-rotation-of-customer-managed-keys"></a>カスタマー マネージド キーの自動キー ローテーション

最新のキー バージョンへの自動キー ローテーションを有効にすることを選択できます。 ディスクから、ディスク暗号化セットを介してキーが参照されます。 ディスク暗号化セットの自動ローテーションを有効にすると、ディスク暗号化セットを参照するすべてのマネージド ディスク、スナップショット、およびイメージがシステムによって自動的に更新され、1 時間以内に新しいバージョンのキーが使用されます。 自動キー ローテーションを使用してカスタマー マネージド キーを有効にする方法は、「[自動キー ローテーションを使用して Azure Key Vault と DiskEncryptionSet を設定する](../articles/virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-optionally-with-automatic-key-rotation)」を参照してください。
