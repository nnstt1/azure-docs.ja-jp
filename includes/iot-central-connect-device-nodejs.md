---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 319567fba40d2412c882685b8d7d3b916b699968
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520555"
---
[![コードを参照](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript)

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のリソースが必要です。

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- [Node.js](https://nodejs.org/) バージョン 6 以降がインストールされた開発用マシン。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 このチュートリアルの手順は、Windows コマンド プロンプトから **node** コマンドを実行することを前提としています。 ただし、他の多くのオペレーティング システムで Node.js を使用できます。

- サンプル コードが格納された [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub リポジトリのローカル コピー。 このリンク ([ZIP のダウンロード](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)) を使用してリポジトリのコピーをダウンロードしてください。 次に、ローカル コンピューター上の適切な場所にファイルを解凍します。

## <a name="review-the-code"></a>コードの確認

先ほどダウンロードした Microsoft Azure IoT SDK for Node.js のコピーにある *azure-iot-sdk-node/device/samples/javascript/pnp/pnpTemperatureController.js* ファイルをテキスト エディターで開きます。

このサンプルを実行して IoT Central に接続すると、Device Provisioning Service (DPS) を使用してデバイスが登録され、接続文字列が生成されます。 このサンプルでは、必要な DPS 接続情報がコマンドライン環境から取得されます。

`main` メソッド:

* `client` オブジェクトを作成し、接続が開かれる前に `dtmi:com:example:TemperatureController;2` モデル ID を設定します。 IoT Central では、モデル ID を使用して、このデバイスのデバイス テンプレートを識別または生成します。 詳細については、「[デバイス テンプレートへのデバイスの関連付け](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)」をご覧ください。
* 3 つのコマンドのコマンド ハンドラーを作成します。
* 各サーモスタット コンポーネントから 5 秒ごとに温度テレメトリを送信するループを開始します。
* 既定のコンポーネントから 6 秒ごとにワーキング セット サイズ テレメトリを送信するループを開始します。
* サーモスタット コンポーネントごとに `maxTempSinceLastReboot` プロパティを送信します。
* デバイス情報プロパティを送信します。
* 3 つのコンポーネントの書き込み可能なプロパティ ハンドラーを作成します。

```javascript
async function main() {
  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);
  console.log('Connecting using connection string: ' + deviceConnectionString);
  let resultTwin;

  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();
    console.log('Enabling the commands on the client');
    client.onDeviceMethod(commandNameGetMaxMinReport1, commandHandler);
    client.onDeviceMethod(commandNameGetMaxMinReport2, commandHandler);
    client.onDeviceMethod(commandNameReboot, commandHandler);

    // Send Telemetry after some interval
    let index1 = 0;
    let index2 = 0;
    let index3 = 0;
    intervalToken1 = setInterval(() => {
      const data = JSON.stringify(thermostat1.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index1, thermostat1ComponentName).catch((err) => console.log('error ', err.toString()));
      index1 += 1;
    }, 5000);

    intervalToken2 = setInterval(() => {
      const data = JSON.stringify(thermostat2.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index2, thermostat2ComponentName).catch((err) => console.log('error ', err.toString()));
      index2 += 1;
    }, 5500);


    intervalToken3 = setInterval(() => {
      const data = JSON.stringify({ workingset: 1 + (Math.random() * 90) });
      sendTelemetry(client, data, index3, null).catch((err) => console.log('error ', err.toString()));
      index3 += 1;
    }, 6000);

    // attach a standard input exit listener
    exitListener(client);

    try {
      resultTwin = await client.getTwin();
      // Only report readable properties
      const patchRoot = helperCreateReportedPropertiesPatch({ serialNumber: serialNumber }, null);
      const patchThermostat1Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat1.getMaxTemperatureValue(),
      }, thermostat1ComponentName);

      const patchThermostat2Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat2.getMaxTemperatureValue(),
      }, thermostat2ComponentName);

      const patchDeviceInfo = helperCreateReportedPropertiesPatch({
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
      }, deviceInfoComponentName);

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot, null);
      updateComponentReportedProperties(resultTwin, patchThermostat1Info, thermostat1ComponentName);
      updateComponentReportedProperties(resultTwin, patchThermostat2Info, thermostat2ComponentName);
      updateComponentReportedProperties(resultTwin, patchDeviceInfo, deviceInfoComponentName);
      desiredPropertyPatchListener(resultTwin, [thermostat1ComponentName, thermostat2ComponentName, deviceInfoComponentName]);
    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 関数は、このデバイスが DPS を使用して IoT Central に登録、接続する方法を示しています。 ペイロードには、[デバイスをデバイス テンプレートに関連付ける](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)ために IoT Central が使用するモデル ID が含まれています。

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
    console.log('registration succeeded');
    console.log('assigned hub=' + result.assignedHub);
    console.log('deviceId=' + result.deviceId);
    console.log('payload=' + JSON.stringify(result.payload));
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 関数は、温度テレメトリをデバイスから IoT Central に送信する方法を示しています。 コンポーネントからのテレメトリについては、`$.sub` というプロパティをコンポーネント名と共に追加します。

```javascript
async function sendTelemetry(deviceClient, data, index, componentName) {
  if (!!(componentName)) {
    console.log('Sending telemetry message %d from component: %s ', index, componentName);
  } else {
    console.log('Sending telemetry message %d from root interface', index);
  }
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` メソッドは、`helperCreateReportedPropertiesPatch` というヘルパー メソッドを使用してプロパティ更新メッセージを作成します。 このメソッドは、プロパティを送信するコンポーネントを指定するための省略可能なパラメーターを受け取ります。

```javascript
const helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  if (!!(componentName)) {
    console.log('The following properties will be updated for component: ' + componentName);
  } else {
    console.log('The following properties will be updated for root interface.');
  }
  console.log(patch);
  return patch;
};
```

`main` メソッドは、次のメソッドを使用して、IoT Central から受け取った書き込み可能なプロパティの更新を処理します。 このメソッドからの応答が、バージョンと状態コードを使用して構築されていることがわかります。

```javascript
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    console.log('Received an update for device with value: ' + JSON.stringify(delta));
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            console.log('Will update property: ' + propertyName + ' to value: ' + propertyValue + ' of component: ' + componentName);
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        console.log('Will update property: ' + key + ' to value: ' + values + ' for root');
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

`main` メソッドは、次のメソッドを使用して、IoT Central からのコマンドを処理します。

```javascript
const commandHandler = async (request, response) => {
  helperLogCommandRequest(request);
  switch (request.methodName) {
  case commandNameGetMaxMinReport1: {
    await sendCommandResponse(request, response, 200, thermostat1.getMaxMinReportObject());
    break;
  }
  case commandNameGetMaxMinReport2: {
    await sendCommandResponse(request, response, 200, thermostat2.getMaxMinReportObject());
    break;
  }
  case commandNameReboot: {
    await sendCommandResponse(request, response, 200, 'reboot response');
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method: ' + request.methodName + ' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' + err.toString());
  }
};
```

## <a name="get-connection-information"></a>接続情報の取得

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>コードの実行

サンプル アプリケーションを実行するには、コマンド ライン環境を開き、*pnpTemperatureController.js* サンプル ファイルがある *azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

必要なパッケージをインストールします。

```cmd/sh
npm install
```

サンプルを実行します。

```cmd/sh
node pnpTemperatureController.js
```

次の出力は、デバイスが IoT Central に登録して接続するようすを示しています。 その後、サンプルは、2 つのサーモスタット コンポーネントから `maxTempSinceLastReboot` プロパティを送信した後、テレメトリの送信を開始します。

```output
registration succeeded
assigned hub=iotc-....azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string: HostName=iotc-....azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=qdv...IpAo=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface.
{ serialNumber: 'alwinexlepaho8329' }
The following properties will be updated for component: thermostat1
{ thermostat1: { maxTempSinceLastReboot: 1.5902294191855972, __t: 'c' } }
The following properties will be updated for component: thermostat2
{ thermostat2: { maxTempSinceLastReboot: 16.181771928614545, __t: 'c' } }
The following properties will be updated for component: deviceInformation
{ deviceInformation:
   { manufacturer: 'Contoso Device Corporation',
     model: 'Contoso 47-turbo',
     swVersion: '10.89',
     osName: 'Contoso_OS',
     processorArchitecture: 'Contoso_x86',
     processorManufacturer: 'Contoso Industries',
     totalStorage: 65000,
     totalMemory: 640,
     __t: 'c' } }
executed sample
Received an update for device with value: {"$version":1}
Properties have been reported for component: thermostat1
Properties have been reported for component: thermostat2
Properties have been reported for component: deviceInformation
Properties have been reported for root interface.
Sending telemetry message 0 from component: thermostat1 
Sending telemetry message 0 from component: thermostat2 
Sending telemetry message 0 from root interface
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

コマンドとプロパティの更新に対してデバイスがどのように応答するかを確認できます。 `getMaxMinReport` コマンドは `thermostat2` コンポーネントに含まれており、`reboot` コマンドは既定のコンポーネントに含まれています。 書き込み可能な `targetTemperature` プロパティが、"thermostat2" コンポーネントに対してが設定されました。

```output
Received command request for command name: thermostat2*getMaxMinReport
The command request payload is:
2021-03-26T06:00:00.000Z
Response to method: thermostat2*getMaxMinReport sent successfully.

...

Received command request for command name: reboot
The command request payload is:
10
Response to method: reboot sent successfully.

...

Received an update for device with value: {"thermostat2":{"targetTemperature":76,"__t":"c"},"$version":2}
Will update property: targetTemperature to value: 76 of component: thermostat2
Properties have been reported for component: thermostat2
```
