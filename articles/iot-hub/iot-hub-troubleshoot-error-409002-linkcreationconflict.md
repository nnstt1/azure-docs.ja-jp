---
title: Azure IoT Hub のエラー 409002 LinkCreationConflict のトラブルシューティング
description: エラー 409002 LinkCreationConflict を修正する方法の概要
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5433af10cef6ef4404d61d3b66aedc311d242c71
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515562"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

この記事では、**409002 LinkCreationConflict** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

エラー **409002 LinkCreationConflict** がデバイスの切断または cloud-to-device メッセージのエラーと共に表示されます。

<!-- When using AMQP? -->

## <a name="cause"></a>原因

一般に、このエラーは、クライアントに複数の接続があることが IoT Hub で検出されたときに発生します。 実際、既存の接続があるデバイスに対して新しい接続要求が到着すると、IoT Hub によって既存の接続が閉じられ、このエラーが発生します。

### <a name="cause-1"></a>原因 1

最も一般的なケースでは、別の問題 ([404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) など) によってデバイスの接続が切断されます。 デバイスではすぐに接続の再確立が試行されますが、IoT Hub ではまだデバイスが接続状態と見なされています。 IoT Hub では前の接続が閉じられ、このエラーが記録されます。

### <a name="cause-2"></a>原因 2

デバイス側のロジックに問題があると、既に接続されているときに、デバイスによって接続が確立されます。

## <a name="solution"></a>解決策

このエラーは通常、別の一時的な問題の副作用として現れます。そのため、問題解決を続けるには、ログで他のエラーを探してください。 そうでなければ、接続が切断された場合にのみ、新しい接続要求を発行してください。
