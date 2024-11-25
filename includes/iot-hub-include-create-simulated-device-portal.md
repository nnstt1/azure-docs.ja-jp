---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28c3ccbd7dc0aae27cd64c0c213869cf968c0a9f
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969357"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

次に、デバイス ID を作成し、後で使用するのでそのキーを保存します。 このデバイス ID は、IoT ハブにメッセージを送信するためにシミュレーション アプリケーションによって使われます。 この機能は、PowerShell では利用できず、また、Azure Resource Manager テンプレートを使用している場合も利用できません。 以降の手順で紹介する方法では、シミュレートされたデバイスを [Azure portal](https://portal.azure.com) を使用して作成しています。

1. [Azure portal](https://portal.azure.com) を開き、Azure アカウントにログインします。

2. **[リソース グループ]** を選択し、対象のリソース グループを選択します。 このチュートリアルでは、**ContosoResources** を使います。

3. リソースの一覧から目的の IoT ハブを選択します。 このチュートリアルでは、**ContosoTestHub** を使います。 [ハブ] ウィンドウで **[IoT デバイス]** を選びます。

4. **[IoT デバイス]** ペインの **[+Add Device]\(+デバイスの追加\)** を選択します。 [デバイスの追加] ウィンドウで、デバイス ID を入力します。 このチュートリアルでは、**Contoso-Test-Device** を使います。 キーは空のままにし、**[キーの自動生成]** をオンにします。 **[デバイスを IoT Hub に接続]** を有効にします。 **[保存]** を選択します。

   ![[デバイスの追加] 画面](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. デバイスが作成されたので、デバイスを選択して生成されたキーを表示します。 プライマリ キーの [コピー] アイコンを選択し、このチュートリアルのテスト フェーズのために、メモ帳などの場所に保存します。

   ![デバイスの詳細 (キーを含む)](./media/iot-hub-include-create-simulated-device-portal/device-details.png)