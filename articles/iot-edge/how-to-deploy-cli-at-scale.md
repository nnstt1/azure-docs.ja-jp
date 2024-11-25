---
title: Azure CLI を使用した大規模なモジュールの展開 - Azure IoT Edge
description: Azure CLI 向け IoT 拡張機能を使用して、一連の IoT Edge デバイスの自動展開を作成します。
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 504ae03ecff532fff5a8343d02fd8bba21524cfd
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397321"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-by-using-the-azure-cli"></a>Azure CLI を使用した大規模な IoT Edge モジュールのデプロイと監視

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure コマンドライン インターフェイスを使用して [Azure IoT Edge の自動展開](module-deployment-monitoring.md)を作成して、多数のデバイスの進行中の展開を一度に管理します。 IoT Edge の自動展開は、Azure IoT Hub の[自動デバイス管理](../iot-hub/iot-hub-automatic-device-management.md)機能の一部です。 デプロイは、複数のモジュールを複数のデバイスにデプロイし、モジュールの状態と正常性を追跡し、必要に応じて変更できる動的プロセスです。

この記事では、Azure CLI と IoT 拡張機能をセットアップします。 次に、使用可能な CLI コマンドを使用して一連の IoT Edge デバイスにモジュールをデプロイして、進行状況を監視する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* 1 つまたは複数の IoT Edge デバイス。

  IoT Edge デバイスがセットアップされていない場合は、Azure 仮想マシンで作成できます。 [仮想 Linux デバイスの作成](quickstart-linux.md)または[仮想 Windows デバイスの作成](quickstart.md)に関するクイックスタート記事のいずれかの手順に従います。

* 環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンが 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。

Azure CLI を使用してモジュールをデプロイするには、配置マニフェストを .txt ファイルとしてローカル環境に保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

>[!NOTE]
>このサンプルの配置マニフェストでは、IoT Edge エージェントとハブにスキーマ バージョン 1.1 を使用します。 スキーマ バージョン 1.1 は、IoT Edge バージョン 1.0.10 でリリースされました。 それを使用すると、モジュールの起動順序やルートの順序付けのような機能が有効になります。

## <a name="layered-deployment"></a>多層デプロイ

多層デプロイは、互いの上に積み重ねることができる自動デプロイの一種です。 多層デプロイの詳細については、「[1 台のデバイスまたは多数のデバイスを対象とした IoT Edge 自動展開について](module-deployment-monitoring.md)」を参照してください。

多層デプロイは、自動デプロイのように Azure CLI を使用して作成および管理できますが、いくつかの違いがあります。 多層デプロイが作成されたら、他のデプロイと同じように、同じ Azure CLI を使用して多層デプロイを操作できます。 多層デプロイを作成するには、create コマンドに `--layered` フラグを追加します。

2 番目の違いは、デプロイ マニフェストの構築です。 標準の自動展開には、ユーザー モジュールに加えてシステム ランタイム モジュールを含める必要がありますが、多層デプロイにはユーザー モジュールだけを含めることができます。 多層デプロイには、システム ランタイム モジュールなど、すべての IoT Edge デバイスに必要なコンポーネントを提供するために、標準の自動展開もデバイス上に必要です。

例として、1 つのモジュールでの基本的な多層デプロイ マニフェストを次に示します。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```
>[!NOTE]
> この多層配置マニフェストの形式は、標準的な配置マニフェストとは若干異なります。 ランタイム モジュールの必要なプロパティは階層化されており、ドット表記を使用します。 Azure portal が多層デプロイを認識するためには、この書式が必要となります。 次に例を示します。
>
> - `properties.desired.modules.<module_name>`
> - `properties.desired.routes.<route_name>`

前の例では、モジュールの `properties.desired` を設定する多層デプロイを示しました。 この多層デプロイは、同じモジュールが既に適用されているデバイスをターゲットにしていた場合、既存の適切なプロパティをすべて上書きしていました。 必要なプロパティを上書きするのではなく更新するには、新しいサブセクションを定義できます。 次に例を示します。

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

同じことを次のように表現することもできます。

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties.SendData" : true,
  "properties.desired.layeredProperties.SendInterval": 5
}
```

>[!NOTE]
>現在、どの多層デプロイも、有効と見なされるためには、`edgeAgent` オブジェクトが含まれている必要があります。 多層デプロイで更新されるのがモジュールのプロパティだけだとしても、空のオブジェクトを追加してください。 (例: `"$edgeAgent":{}`)。 空の `edgeAgent` オブジェクトが含まれる多層デプロイは、`edgeAgent` モジュール ツインでは、**applied** ではなく **targeted** と表示されます。

まとめると、多層デプロイを作成するには、次のようにします。

- Azure CLI の create コマンドに `--layered` フラグを追加します。
- システム モジュールは含めないでください。
- `$edgeAgent` と `$edgeHub` では、完全なドット表記を使用します。

多層デプロイでのモジュール ツインの構成の詳細については、[多層デプロイ](module-deployment-monitoring.md#layered-deployment)に関するページを参照してください。

## <a name="identify-devices-by-using-tags"></a>タグを使用してデバイスを識別する

デプロイを作成する前に、影響を与えるデバイスを指定できる必要があります。 Azure IoT Edge では、デバイス ツイン内の "*タグ*" を使用してデバイスを識別します。 

各デバイスには、対象のソリューションにとって意味のある方法で定義した複数のタグを設定することができます。 たとえば、スマート ビルのキャンパスを管理している場合は、デバイスに次のタグを追加できます。

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

デバイス ツインとタグの詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。

## <a name="create-a-deployment"></a>デプロイの作成

モジュールをターゲット デバイスにデプロイするには、配置マニフェストと他のパラメーターで構成されるデプロイを作成します。

デプロイを作成するには、[az iot edge deployment create](/cli/azure/iot/edge/deployment) コマンドを使用します。

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

同じコマンドと `--layered` フラグを使用して、多層デプロイを作成します。

デプロイ用の create コマンドは、次のパラメーターを受け取ります。

* **--layered**。 デプロイを多層デプロイとして識別する省略可能なフラグ。
* **--deployment-id**。IoT ハブに作成されるデプロイの名前。 デプロイに一意の名前を付けます。名前は最大 128 文字の英小文字で指定します。 スペースや、無効な文字は使用しないでください。`& ^ [ ] { } \ | " < > /` このパラメーターは必須です。
* **--content**。 デプロイ マニフェスト JSON へのファイルパス。 このパラメーターは必須です。
* **--hub-name**。 デプロイが作成される IoT ハブの名前。 ハブは現在のサブスクリプションにある必要があります。 `az account set -s [subscription name]` コマンドを使用して、現在のサブスクリプションを変更します。
* **--labels**。 デプロイを説明し、その追跡に役立つ名前と値のペア。 ラベルの名前と値には、JSON 形式を使用します。 (例: `{"HostPlatform":"Linux", "Version:"3.0.1"}`)。
* **--target-condition**。 このデプロイのターゲットとなるデバイスを決定するターゲット条件。 条件は、デバイス ツイン タグか、デバイス ツインから報告されるプロパティに基づいて指定し、式の形式に一致させる必要があります。 (例: `tags.environment='test' and properties.reported.devicemodel='4000x'`)。
* **--priority**。 正の整数。 複数のデプロイが同じデバイスをターゲットしている場合には、優先度の数値が最も大きいデプロイが適用されます。
* **--metrics**。 `edgeHub` によって報告されたプロパティのクエリを実行し、デプロイの状態を追跡するメトリック。 メトリックは JSON 入力またはファイル パスを受け取ります。 (例: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`)。

Azure CLI を使用してデプロイを監視するには、「[IoT Edge デプロイの監視](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)」を参照してください。

## <a name="modify-a-deployment"></a>デプロイの変更

デプロイを変更すると、変更はすべての対象デバイスにただちにレプリケートされます。

対象の条件を更新すると、次の更新が実行されます。

* デバイスが古い対象条件を満たさないが、新しい対象条件を満たしており、このデプロイのそのデバイスに対する優先度が最も高い場合は、このデプロイは、このデバイスに適用されます。
* このデプロイを現在実行しているデバイスが対象条件を満たさなくなった場合、このデプロイはアンインストールされ、次に優先度の高いデプロイが適用されます。
* このデプロイを現在実行しているデバイスが対象条件を満たさなくなり、他のデプロイの対象条件を満たさない場合は、デバイスではなにも変更されません。 デバイスは現在の状態で現在のモジュールを実行し続けますが、このデプロイの一部としては管理されなくなります。 デバイスが他のデプロイの対象の条件を満たすと、このデプロイはアンインストールされ、新しいデプロイが適用されます。

配置マニフェストで定義されたモジュールとルートを含むデプロイの内容は更新できません。 デプロイの内容を更新する場合は、同じデバイスをターゲットにした、より優先順位が高い新しいデプロイを作成します。 ターゲット条件、ラベル、メトリック、優先順位など、既存のモジュールの特定のプロパティを変更できます。

デプロイを更新するには、[az iot edge deployment update](/cli/azure/iot/edge/deployment) コマンドを使用します。

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

deployment update コマンドは、次のパラメーターを受け取ります。

* **--deployment-id**。IoT ハブに存在するデプロイの名前。
* **--hub-name**。 デプロイが存在する IoT ハブの名前。 ハブは現在のサブスクリプションにある必要があります。 `az account set -s [subscription name]` コマンドを使用して目的のサブスクリプションに切り替えます。
* **--set**。 デプロイのプロパティを更新します。 次のプロパティを更新することができます。
  * `targetCondition` (例: `targetCondition=tags.location.state='Oregon'`)
  * `labels`
  * `priority`
* **--add**。 ターゲット条件やラベルなど、新しいプロパティをデプロイに追加します。
* **--remove**。 ターゲット条件やラベルなど、既存のプロパティを削除します。

## <a name="delete-a-deployment"></a>デプロイの削除

デプロイを削除すると、デバイスには、次に高い優先順位のデプロイが適用されます。 デバイスが他のいずれのデプロイの対象条件も満たさない場合、デプロイが削除されても、モジュールは削除されません。

デプロイを削除するには、[az iot edge deployment delete](/cli/azure/iot/edge/deployment) コマンドを使用します。

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

`deployment delete` コマンドは、次のパラメーターを受け取ります。

* **--deployment-id**。IoT ハブに存在するデプロイの名前。
* **--hub-name**。 デプロイが存在する IoT ハブの名前。 ハブは現在のサブスクリプションにある必要があります。 `az account set -s [subscription name]` コマンドを使用して目的のサブスクリプションに切り替えます。

## <a name="next-steps"></a>次のステップ

[IoT Edge デバイスへのモジュールのデプロイ](module-deployment-monitoring.md)の詳細について学習します。
