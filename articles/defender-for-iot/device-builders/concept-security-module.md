---
title: Defender for IoT マイクロ エージェントとデバイス ツイン
description: Defender for IoT マイクロ エージェント ツインの概念と、それらを Defender for IoT で使用する方法について説明します。
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: f8a634eaa3947d08e6ab88b8f05e119fbcf54665
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278932"
---
# <a name="defender-iot-micro-agent"></a>Defender for IoT マイクロ エージェント

この記事では、Defender for IoT でのデバイス ツインとモジュールの使用方法について説明します。

## <a name="device-twins"></a>デバイス ツイン

Azure に構築された IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。

Defender for IoT は、既存の IoT デバイス管理プラットフォームと完全に統合されるため、デバイスのセキュリティ状態を管理したり、既存のデバイス制御機能を利用したりすることができます。 統合は、IoT Hub ツイン メカニズムを利用することによって実現されます。

Azure IoT Hub での[デバイス ツイン](../../iot-hub/iot-hub-devguide-device-twins.md#device-twins)の概念の詳細を確認してください。

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-agent ツイン

Defender for IoT により、サービス内の各デバイスの Defender for IoT マイクロ エージェント ツインが保持されます。
Defender for IoT マイクロ エージェント ツインでは、ソリューション内の特定のデバイスごとに、デバイスのセキュリティに関連するすべての情報が保持されます。
デバイスのセキュリティのプロパティは、通信の安全性を高め、必要なリソースが少なくて済む更新とメンテナンスを可能にするために、専用の Defender for IoT マイクロ エージェント ツインに保持されます。

ツインの作成、カスタマイズ、および構成方法の詳細については、[Defender for IoT マイクロ エージェント ツインの作成](quickstart-create-security-twin.md)および[セキュリティ エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。 IoT Hub でのモジュール ツインの概念の詳細については、「[IoT Hub のモジュール ツインの理解と使用](../../iot-hub/iot-hub-devguide-module-twins.md)」を参照してください。

## <a name="see-also"></a>関連項目

- [Defender for IoT の概要](overview.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)
