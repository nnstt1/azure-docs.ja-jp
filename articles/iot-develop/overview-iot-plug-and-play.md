---
title: IoT プラグ アンド プレイの概要 | Microsoft Docs
description: IoT プラグ アンド プレイについて説明します。 IoT プラグ アンド プレイは、スマート IoT デバイスがその機能を宣言できるようにするオープン モデリング言語に基づいています。 IoT デバイスは、クラウド ソリューションに接続するとき、デバイス モデルと呼ばれるその宣言を提示します。 これで、クラウド ソリューションでは、デバイスを自動的に認識して、デバイスとのやり取りを開始できるようになります。すべてコードを記述することなく行うことができます。
author: rido-min
ms.author: rmpablos
ms.date: 08/20/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
manager: eliotgra
ms.custom:
- references_regions
- contperf-fy22q1
ms.openlocfilehash: ae5c2db75b868732bc5a6790e37111b8b1a493ef
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542424"
---
# <a name="what-is-iot-plug-and-play"></a>IoT プラグ アンド プレイとは

IoT プラグ アンド プレイにより、ソリューション ビルダーは、手動で構成することなく、独自のソリューションに IoT デバイスを統合することができます。 IoT プラグ アンド プレイの中核となるのは、デバイスが自身の機能を IoT プラグ アンド プレイ対応アプリケーションに公開するために使用するデバイス _モデル_ です。 このモデルは、次の内容を定義する要素のセットとして構成されます。

- デバイスまたは他のエンティティの読み取り専用および書き込み可能な状態を表す _プロパティ_。 たとえば、デバイスのシリアル番号は読み取り専用のプロパティであり、サーモスタットでの目標温度は書き込み可能なプロパティとなります。
- デバイスによって出力されるデータである "_テレメトリ_"。このデータはセンサー読み取り値の通常のストリーム、偶発的なエラー、または情報メッセージのいずれかです。
- デバイス上で実行できる関数または操作を記述した "_コマンド_"。 たとえば、コマンドでは、ゲートウェイを再起動したり、リモート カメラを使用して写真を撮影したりすることが可能です。

インターフェイス内でこれらの要素をグループ化してモデル間で再利用すれば、コラボレーションを容易にし、開発を高速化することができます。

IoT プラグ アンド プレイを [Azure Digital Twins](../digital-twins/overview.md) と連携させるには、[Digital Twin Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) を使用してモデルとインターフェイスを定義します。 IoT プラグ アンド プレイと DTDL はコミュニティにも開かれており、Microsoft はお客様、パートナー、業界とのコラボレーションを歓迎しています。 どちらも、サービスおよびツールをまたいで簡単に導入できるオープンな W3C 標準 (JSON-LD や RDF など) に基づいています。

IoT プラグ アンド プレイと DTDL を使用する場合、追加料金は発生しません。 [Azure IoT Hub](../iot-hub/about-iot-hub.md) およびその他の Azure サービスの標準料金は変わりません。

この記事では以下の内容について概説します。

- IoT プラグ アンド プレイを使用するプロジェクトに関連付けられる一般的なロール。
- ご利用のアプリケーション内で IoT プラグ アンド プレイ デバイスを使用する方法。
- IoT プラグ アンド プレイをサポートする IoT デバイス アプリケーションを開発する方法。

## <a name="user-roles"></a>ユーザー ロール

IoT プラグ アンド プレイは、次の 2 種類の開発者にとって有用です。

- "_ソリューション ビルダー_": Azure IoT Hub およびその他の Azure リソースを使用して IoT ソリューションを開発すると共に、統合する IoT デバイスを特定する役割を担います。 詳細については、[IoT プラグ アンド プレイ サービスの開発者ガイド](concepts-developer-guide-service.md)を参照してください。
- "_デバイス ビルダー_": ご利用のソリューションに接続されたデバイス上で実行するコードを作成します。 詳細については、[IoT プラグ アンド プレイ デバイスの開発者ガイド](concepts-developer-guide-device.md)を参照してください。

## <a name="use-iot-plug-and-play-devices"></a>IoT プラグ アンド プレイ デバイスを使用する

ソリューション ビルダーは、[IoT Central](../iot-central/core/overview-iot-central.md) または [IoT Hub](../iot-hub/about-iot-hub.md) を使用して、IoT プラグ アンド プレイ デバイスを使用する、クラウドでホストされた IoT ソリューションを開発できます。

IoT Central の Web UI では、デバイスの状態を監視し、ルールを作成し、ライフ サイクル全体を通して何百万ものデバイスとそのデータを管理することができます。 IoT プラグ アンド プレイ デバイスは、カスタマイズ可能なダッシュボードを使用してデバイスを監視および制御できる IoT Central アプリケーションに直接接続します。 IoT Central Web UI のデバイス テンプレートを使用して、DTDL モデルを作成および編集することもできます。

マネージド クラウド サービスである IoT Hub は、ご利用の IoT アプリケーションとデバイスとの間で、セキュリティで保護された双方向通信を行うためのメッセージ ハブとして機能します。 IoT プラグ アンド プレイ デバイスを IoT ハブに接続すると、[Azure IoT エクスプローラー](../iot-fundamentals/howto-use-iot-explorer.md) ツールを使用することで、DTDL モデル内で定義されているテレメトリ、プロパティ、およびコマンドを表示できます。

Windows または Linux ゲートウェイに接続されている既存のセンサーがある場合は、[IoT プラグ アンド プレイ ブリッジ](./concepts-iot-pnp-bridge.md)を使用してこれらのセンサーを接続し、([サポートされているプロトコル](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)用の) デバイス ソフトウェアまたはファームウェアを記述することなく IoT プラグ アンド プレイ デバイスを作成することができます。

詳細については、「[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)」を参照してください。

## <a name="develop-an-iot-device-application"></a>IoT デバイス アプリケーションを開発する

デバイス ビルダーは、IoT プラグ アンド プレイをサポートする IoT ハードウェア製品を開発できます。 このプロセスには、次の 3 つの主な手順が含まれます。

1. デバイス モデルを定義します。 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl) を使用してデバイスの機能を定義する一連の JSON ファイルを作成します。 モデルには、物理的な製品などの完全なエンティティが記述され、さらにそのエンティティによって実装される一連のインターフェイスが定義されます。 インターフェイスは、デバイスでサポートされているテレメトリ、プロパティ、コマンドを一意に識別する共有コントラクトです。 インターフェイスは、さまざまなモデル間で再利用できます。

1. デバイスのソフトウェアまたはファームウェアを作成する場合は、それらのテレメトリ、プロパティ、およびコマンドが [IoT プラグ アンド プレイ規則](concepts-convention.md)に従うようにします。 Windows または Linux ゲートウェイに接続されている既存のセンサーを接続する場合は、[IoT プラグ アンド プレイ ブリッジ](./concepts-iot-pnp-bridge.md)を使用すると、このステップを簡略化できます。

1. MQTT 接続の一環としてモデル ID がデバイスから通知されます。 Azure IoT SDK には、接続時にモデル ID を提供する新しいコンストラクトが含まれています。

> [!Important]
> IoT プラグ アンド プレイ デバイスでは、WebSocket 経由で MQTT または MQTT を使用する必要があります。 AMQP や HTTP などの他のプロトコルは、IoT プラグ アンド プレイ デバイスの実装には無効です。

## <a name="device-certification"></a>デバイス認定

[IoT プラグ アンド プレイ デバイス認定プログラム](../certification/program-requirements-pnp.md)は、IoT プラグ アンド プレイの認定要件をデバイスが満たしていることを確認するものです。 認定されたデバイスは、公開されている [Azure IoT 認定デバイス カタログ](https://aka.ms/devicecatalog)に登録できます。

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイの概要を説明したので、次の手順では、クイックスタートのいずれかを試してみましょう。

- [デバイスを IoT Hub に接続する](./tutorial-connect-device.md)
- [ソリューションからデバイスを操作する](./tutorial-service.md)
