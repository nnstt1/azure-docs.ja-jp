---
title: Azure Spring Cloud Service のセキュリティ コントロール
description: Azure Spring Cloud Service に組み込まれているセキュリティ コントロールを使用します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: f980c67e22ca031617e9614d2e8facd2e9a9dafa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485701"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud Service のセキュリティ コントロール

**この記事の適用対象:** ✔️ Java ✔️ C#

セキュリティ コントロールは、Azure Spring Cloud Service に組み込まれています。

セキュリティ コントロールとは、セキュリティの脆弱性の防止、検出、および対処を行う Azure サービスの能力に寄与する、そのサービスの品質または機能です。  各コントロールについて、 *[はい]* または *[いいえ]* を使用して、サービスに現在設定されているかどうかを示します。  サービスに適用されないコントロールには *[N/A]* を使用します。

**データ保護のセキュリティ コントロール**

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|:-------------|:-------|:-------------------------------|:----------------------|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | ユーザーがアップロードしたソースと成果物、構成サーバーの設定、アプリの設定、永続的ストレージのデータは Azure Storage に格納されます。ここに格納されるコンテンツは自動的に暗号化されます。<br><br>構成サーバー キャッシュ、アップロードされたソースから構築されたランタイム バイナリ、およびアプリケーションの有効期間中のアプリケーション ログは、Azure マネージド ディスクに保存されます。ここに保存されるコンテンツは自動的に暗号化されます。<br><br>ユーザーがアップロードしたソースから構築されたコンテナー イメージは、Azure Container Registry に保存されます。ここに保存されるイメージ コンテンツは自動的に暗号化されます。 | [保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)<br><br>[Azure Managed Disks のサーバー側暗号化](../virtual-machines/disk-encryption.md)<br><br>[Azure Container Registry へのコンテナー イメージの保存](../container-registry/container-registry-storage.md) |
| 一時的な暗号化 | はい | ユーザー アプリのパブリック エンドポイントには、既定で受信トラフィックに HTTPS が使用されます。 |  |
| API 呼び出しの暗号化 | はい | Azure Spring Cloud サービスを構成するための管理呼び出しは、HTTPS 経由の Azure Resource Manager 呼び出しを介して行われます。 | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**ネットワーク アクセスのセキュリティ コントロール**

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|:-------------|:-------|:-------------------------------|:----------------------|
| サービス タグ | はい | **AzureSpringCloud** サービス タグを使用して、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) で送信ネットワーク アクセスの制御を定義し、Azure Spring Cloud 内のアプリケーションへのトラフィックを許可します。 | [サービス タグ](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>次の手順

* [クイック スタート: 初めての Spring Boot アプリを Azure Spring Cloud にデプロイする](./quickstart.md)
