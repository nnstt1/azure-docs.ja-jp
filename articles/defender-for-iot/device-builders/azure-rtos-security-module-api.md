---
title: Azure RTOS 用の Defender for IoT マイクロ エージェント API
description: Azure RTOS 用の Defender for IoT マイクロ エージェントの API を参照します。
ms.topic: reference
ms.date: 11/09/2021
ms.author: mlottner
ms.openlocfilehash: ad79ec853f3ee625c9b8c3c4bb4063aee87481f1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286642"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Azure RTOS 用の Defender for IoT マイクロ エージェント API (プレビュー)

Defender for IoT API は、[Microsoft API ライセンスと利用規約](/legal/microsoft-apis/terms-of-use)によって管理されています。

この API は、Azure RTOS 用の Defender for IoT マイクロ エージェントでのみ使用することを目的としています。 その他のリソースについては、[Azure RTOS 用の Defender for IoT マイクロ エージェントに関連する GitHub リソース](https://github.com/azure-rtos/azure-iot-preview/releases)を参照してください。

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Azure RTOS 用の Defender for IoT マイクロ エージェントを有効にする

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>プロトタイプ

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>説明

このルーチンでは、Azure IoT の Defender for IoT マイクロ エージェントのサブシステムを有効にします。 内部ステート マシンによってセキュリティ イベントのコレクションが管理され、それらが Azure IoT Hub に送信されます。 データ コレクションを管理するために必要なのは、1 つの NX_AZURE_IOT_SECURITY_MODULE インスタンスだけです。

### <a name="parameters"></a>パラメーター

| 名前 | 説明 |
|---------|---------|
| nx_azure_iot_ptr  [in]    | `NX_AZURE_IOT` を指すポインター。  |

### <a name="return-values"></a>戻り値

|戻り値  |説明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT セキュリティ モジュールが正常に有効になりました。     |
|NX_AZURE_IOT_FAILURE   |  内部エラーのため、Azure IoT セキュリティ モジュールを有効にできませんでした。    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  セキュリティ モジュールには、有効な #NX_AZURE_IOT インスタンスが必要です。      |

### <a name="allowed-from"></a>許可元

Threads

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Azure IoT の Defender for IoT マイクロ エージェントを無効にする

**nx_azure_iot_security_module_disable**

### <a name="prototype"></a>プロトタイプ

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>説明

このルーチンでは、Azure IoT の Defender for IoT マイクロ エージェントのサブシステムを無効にします。

### <a name="parameters"></a>パラメーター

| 名前 | 説明 |
|---------|---------|
| nx_azure_iot_ptr  [in]    | `NX_AZURE_IOT` を指すポインターです。 NULL の場合、シングルトン インスタンスは無効になります。 |

### <a name="return-values"></a>戻り値

|戻り値  |説明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Azure IoT セキュリティ モジュールが正常に無効にされた場合は成功です。      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub インスタンスがシングルトン複合インスタンスと異なります。       |
|NX_AZURE_IOT_FAILURE    |  内部エラーのため、Azure IoT セキュリティ モジュールを無効にできませんでした。       |

### <a name="allowed-from"></a>許可元

Threads

## <a name="next-steps"></a>次の手順

Azure RTOS の Defender for IoT マイクロ エージェントの使用を開始する方法の詳細については、次の記事を参照してください。

- Defender for IoT RTOS の Defender for IoT マイクロ エージェントの[概要](iot-security-azure-rtos.md)に関する記事を参照してください。
