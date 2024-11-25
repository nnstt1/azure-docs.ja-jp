---
title: クイックスタート - Azure CLI を使用してキー コンテナーを作成する
description: Azure CLI を使用して Azure キー コンテナーを作成する方法を示すクイックスタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815128"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用してキー コンテナーを作成する

Azure Key Vault は、[キー](../keys/index.yml)、[シークレット](../secrets/index.yml)、[証明書](../certificates/index.yml)用の Secure Store を提供するクラウド サービスです。 Key Vault の詳細については、「[Azure Key Vault について](overview.md)」を参照してください。キー コンテナーに格納できる内容の詳細については、「[キー、シークレット、証明書について](about-keys-secrets-certificates.md)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このクイックスタートには、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Key Vault を作成します

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成して削除しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](overview.md)を確認する
- 「[Azure Key Vault のセキュリティの概要](security-features.md)」を確認する
- [Azure CLI az keyvault コマンド](/cli/azure/keyvault)のリファレンスを参照する

