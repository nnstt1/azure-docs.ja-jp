---
title: Personalizer サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、Personalizer での保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: jeffmend
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jeffme
ms.openlocfilehash: d6d4aaa9b6a04614fdae4f794da9153a03031727
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829759"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Personalizer サービスによる保存データの暗号化

Personalizer サービスでは、クラウドに永続化されるときにデータが自動的に暗号化されます。 Personalizer サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、E0 価格レベルでのみ利用できます。 カスタマー マネージド キーを使用できるようにするには、[Personalizer サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Personalizer サービスでの CMK の使用が承認されたら、新しい Personalizer リソースを作成し、価格レベルとして E0 を選択する必要があります。 E0 価格レベルで Personalizer リソースを作成したら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>次のステップ

* [Personalizer サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](../../key-vault/general/overview.md)