---
title: Azure Percept のセキュリティの推奨事項
description: Azure Percept ファイアウォールの構成とセキュリティに関する推奨事項について詳しく説明します
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 146b39db7aaae2ee043d14d61a7be4f00363c548
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222337"
---
# <a name="azure-percept-security-recommendations"></a>Azure Percept のセキュリティの推奨事項

Azure Percept でのファイアウォールの構成と一般的なセキュリティ ベストプラクティスについては、以下のガイドラインを参照してください。

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Azure Percept DK のファイアウォールの構成

ネットワーク設定で、Azure Percept DK デバイスから行われた接続を明示的に許可する必要がある場合、次のコンポーネントのリストを確認します。

このチェックリストを使用して、ファイアウォール規則を開始できます。

|URL (* = ワイルドカード)|送信 TCP ポート|使用法|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|443|Azure DK SOM の認証と承認|
|*.auth.projectsantacruz.azure.net|443|Azure DK SOM の認証と承認|

さらに、[Azure IoT Edge で使用される接続](../iot-edge/production-checklist.md#allow-connections-from-iot-edge-devices)の一覧を確認します。

## <a name="additional-recommendations-for-deployment-to-production"></a>運用環境にデプロイする場合の追加の推奨事項

Azure Percept DK には、すぐに使用できるセキュリティ機能が豊富に備わっています。 運用デプロイを検討している場合、現在のリリースに含まれているこれらの強力なセキュリティ機能に加えて、Microsoft はさらに以下をガイドラインとして推奨します。

- デバイス自体の強力な物理的保護
- 保存データの暗号化の確実な有効化
- デバイスの状況の継続的な監視とアラートへの迅速な対応
- デバイスへのアクセス権を持つ管理者の数の制限

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Percept のセキュリティの詳細を確認する](./overview-percept-security.md)