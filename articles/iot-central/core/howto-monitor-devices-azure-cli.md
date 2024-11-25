---
title: Azure IoT Central エクスプローラーを使用してデバイスの接続を監視する
description: IoT Central エクスプローラー CLI を使用して、デバイスのメッセージを監視し、デバイス ツインの変更を観察します。
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
ms.openlocfilehash: 9c257b9df42af31c443ae3e609d578db6adeb1c8
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154833"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Azure CLI を使用してデバイスの接続性を監視する

Azure CLI IoT 拡張機能を使用して、デバイスから IoT Central に送信されるメッセージを確認し、デバイス ツインでの変更を観察します。 このツールを使用し、デバイスの接続状態をデバッグし、観察し、クラウドに達しないデバイス メッセージの問題や、ツイン変更に応答しないデバイスの問題を診断できます。

[詳細については、Azure CLI 拡張機能のリファレンスを参照してください。](/cli/azure/iot/central)

## <a name="prerequisites"></a>前提条件

Azure の職場または学校アカウント。IoT Central アプリケーションにユーザーとして追加されます。

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="install-the-iot-central-extension"></a>IoT Central 拡張機能をインストールする

インストールするには、コマンド ラインから次のコマンドを実行します。

```azurecli
az extension add --name azure-iot
```

次を実行して拡張機能のバージョンを確認します。

```azurecli
az --version
```

azure-iot 拡張機能が 0.9.9 以上になっているはずです。 そうでない場合は次を実行します。

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>拡張機能を使用する

次のセクションでは、`az iot central` を実行するときに使用できる一般的なコマンドとオプションについて説明します。 コマンドおよびオプションの完全なセットを表示するには、`--help` を `az iot central` またはそのいずれかのサブコマンドに渡します。

### <a name="login"></a>ログイン

まず、Azure CLI にサインインします。 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT Central アプリのアプリケーション ID を取得する
**[Administration/Application Settings]\(管理/アプリケーション設定\)** で **アプリケーション ID** をコピーします。 この値は、後の手順で使用します。

### <a name="monitor-messages"></a>メッセージの監視
デバイスから IoT Central アプリに送信されているメッセージを監視します。 出力には、すべてのヘッダーと注釈が含まれます。

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>デバイスのプロパティを表示する
特定のデバイスを対象に、現在の読み取りおよび読み取り/書き込みのデバイス プロパティを表示します。

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>次のステップ

次の手順は、[Azure IoT Central のデバイス接続機能](./concepts-get-connected.md)に関する記事を読むことをお勧めします。