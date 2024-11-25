---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 47637db9cbfb0a7b1e69e52f1da8248563c1796b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353831"
---
ここでは、[Azure portal](https://portal.azure.com) を使用して IoT ハブを作成する方法について説明します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure ホームページから **[+ リソースの作成]** ボタンを選択し、 **[Marketplace を検索]** フィールドに「*IoT Hub*」と入力します。

1. 検索結果の **[IoT Hub]** を選択し、 **[作成]** を選択します。

1. **[基本]** タブで、次のように各フィールドに入力します。

   - **サブスクリプション**:ハブで使用するサブスクリプションを選択します。

   - **リソース グループ**:リソース グループを選択するか、新しく作成します。 新たに作成するには、 **[新規作成]** を選択して、使用する名前を入力します。 既存のリソース グループを使用するには、そのリソース グループを選択します。 詳しくは、[「Manage Azure Resource Manager resource groups (Azure Resource Manager のリソース グループの管理)](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)」をご覧ください。

   - **[リージョン]** :ハブを配置するリージョンを選択します。 ユーザーに最も近い場所を選択します。 一部の機能 ([IoT Hub デバイス ストリーム](../articles/iot-hub/iot-hub-device-streams-overview.md)など) は、特定のリージョンでのみご利用いただけます。 これらの制限のある機能については、サポート対象のいずれかのリージョンを選択する必要があります。

   - **[IoT Hub 名]** : ハブの名前を入力します。 この名前は、3 から 50 文字の英数字でグローバルに一意である必要があります。 名前には、ダッシュ (`'-'`) 文字を含めることもできます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png" alt-text="Azure portal でハブを作成する。":::

1. **Next:Networking\(次へ: ネットワーク\)** を選択して、ハブの作成を続けます。

   デバイスから IoT Hub に接続するために使用できるエンドポイントを選択します。 既定の設定である **[パブリック エンドポイント (すべてのネットワーク)]** を選択できるほか、 **[Public endpoint (selected IP ranges)]\(パブリック エンドポイント (選択された IP 範囲)\)** または **[プライベート エンドポイント]** を選択できます。 この例では、既定の設定をそのまま使用しています。

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png" alt-text="接続できるエンドポイントを選択する。":::

1. **Next:Management\(次へ: 管理\)** を選択して、ハブの作成を続けます。

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-management.png" alt-text="Azure portal を使用して新しいハブのサイズとスケールを設定する。":::

    ここでは、既定の設定をそのまま使用できます。 必要に応じて、次のフィールドに変更を加えることができます。

    - **[価格とスケールティア]** : 選択したレベル。 必要な機能およびソリューションで 1 日に送信するメッセージの数に応じて、複数のレベルから適切なものを選びます。 無料レベルは、テストおよび評価用です。 ハブに接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、Free レベルの IoT ハブを 1 つ作成できます。

      IoT Hub デバイス ストリームのクイックスタートに取り組んでいる場合は、Free レベルを選択してください。

    - **[IoT Hub ユニット]** : ユニットごとに許可される 1 日あたりのメッセージの数は、ハブの価格レベルによって決まります。 たとえば、ハブで 700,000 件のイングレス メッセージをサポートする場合は、S1 レベルのユニットを 2 つ選択します。
    他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](../articles/iot-hub/iot-hub-scaling.md)に関するページをご覧ください。

    - **[Defender for IoT]** : IoT およびお使いのデバイスに、脅威に対する保護のレイヤーを別途追加するには、これをオンにします。 このオプションは、Free レベルのハブでは使用できません。 この機能の詳細については、[Microsoft Defender for IoT](/azure/asc-for-iot/) に関するページを参照してください。

    - **[詳細設定]**  >  **[Device-to-cloud パーティション]** : このプロパティでは、device-to-cloud メッセージがそのメッセージの同時閲覧者数に関連付けられます。 ほとんどのハブでは、4 つのパーティションのみが必要となります。

1. **次へ:[Next]\(次へ\)** を選択して、次の画面に進みます。

    タグは、名前と値の組です。 複数のリソースおよびリソース グループに同じタグを割り当てることで、リソースを分類したり、課金情報を統合したりすることができます。 このドキュメントでは、タグを追加しません。 詳細については、[タグを使用した Azure リソースの整理](../articles/azure-resource-manager/management/tag-resources.md)に関するページを参照してください。

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-tags.png" alt-text="Azure portal を使用してハブにタグを割り当てる。":::

1. **次へ:次へ: レビューと作成** をクリックして、選択内容を確認します。 次の画面のようになります。ただし、表示されるのはハブの作成時に選択した値です。

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-review-and-create.png" alt-text="新しいハブを作成するための情報を確認する。":::

1. **[作成]** を選択して新しいハブのデプロイを開始します。 ハブの作成中、数分間にデプロイが進行中になります。 デプロイが完了したら、 **[リソースに移動]** を選択し、新しいハブを開きます。
