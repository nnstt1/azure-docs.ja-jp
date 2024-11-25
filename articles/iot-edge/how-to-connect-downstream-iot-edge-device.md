---
title: ダウンストリーム IoT Edge デバイスを接続する - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge ゲートウェイ デバイスに接続するように IoT Edge デバイスを構成する方法。
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 7b76f98d13e959529aab2c16776ed34cb0f1a906
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706687"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>ダウンストリーム IoT Edge デバイスを Azure IoT Edge ゲートウェイに接続する

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

この記事では、IoT Edge ゲートウェイとダウンストリーム IoT Edge デバイス間の信頼関係接続を確立する手順について説明します。

ゲートウェイのシナリオでは、IoT Edge デバイスはゲートウェイとダウンストリーム デバイスの両方になることができます。 複数の IoT Edge ゲートウェイを階層化して、デバイスの階層を作成することができます。 ダウンストリーム (または子) デバイスは、そのゲートウェイ (または親) デバイスを介してメッセージの認証や送受信を行うことができます。

ゲートウェイ階層内の IoT Edge デバイスには 2 つの異なる構成があり、この記事ではその両方について説明します。 1 つ目は、**最上位レイヤー** の IoT Edge デバイスです。 複数の IoT Edge デバイスが互いを介して接続している場合、親デバイスがなく、IoT Hub に直接接続するデバイスは、最上位レイヤーにあると見なされます。 このデバイスは、その下にあるすべてのデバイスからの要求処理を担当します。 もう 1 つの構成は、階層の **下位レイヤー** にあるすべての IoT Edge デバイスに適用されます。 これらのデバイスは、他のダウンストリーム IoT や IoT Edge デバイスのゲートウェイになる場合もありますが、独自の親デバイスを介して通信をルーティングする必要もあります。

一部のネットワーク アーキテクチャでは、クラウドに接続できるのは、階層内の最上位の IoT Edge デバイスのみである必要があります。 この構成では、階層の下位レイヤーにあるすべての IoT Edge デバイスが通信できるのは、そのゲートウェイ (または親) デバイスとダウンストリーム (または子) デバイスのみになります。

この記事に記載されているすべての手順は、IoT Edge デバイスをダウンストリーム IoT デバイスのゲートウェイとして設定する、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」に基づいています。 同じ基本的手順がすべてのゲートウェイ シナリオに適用されます。

* **認証**:ゲートウェイ階層内のすべてのデバイスの IoT Hub ID を作成します。
* **承認**: IoT Hub で親子関係を設定して、子デバイスが IoT Hub に接続する場合と同様に親デバイスに接続することを承認します。
* **ゲートウェイの検出**: 子デバイスがローカル ネットワーク上の親デバイスを見つけられるようにします。
* **セキュリティで保護された接続**: 同じチェーンの一部である信頼できる証明書を使用して、セキュリティで保護された接続を確立します。

## <a name="prerequisites"></a>前提条件

* Free または Standard の IoT ハブ。
* 少なくとも 2 つの **IoT Edge デバイス**。最上位レイヤーのデバイスが 1 つと、1 つ以上の下位レイヤーのデバイス。 IoT Edge デバイスが使用できない場合は、[Ubuntu 仮想マシンで Azure IoT Edge を実行](how-to-install-iot-edge-ubuntuvm.md)できます。
* Azure CLI を使用してデバイスを作成および管理する場合は、Azure IoT 拡張機能 v0.10.6 以降を備えた Azure CLI v2.3.1 をインストールしてください。

この記事では、ご自身のシナリオに適したゲートウェイ階層を作成するための詳細な手順とオプションについて説明します。 ガイド付きチュートリアルについては、[ゲートウェイを使用した IoT Edge デバイス階層の作成](tutorial-nested-iot-edge.md)に関する記事を参照してください。

## <a name="create-a-gateway-hierarchy"></a>ゲートウェイ階層を作成する

このシナリオでは、IoT Edge デバイスの親子関係を定義することによって、IoT Edge ゲートウェイ階層を作成します。 新しいデバイス ID を作成するときに親デバイスを設定することも、既存のデバイス ID の親と子を管理することもできます。

親子関係を設定する手順では、子デバイスが IoT Hub に接続する場合と同様に親デバイスに接続することを承認します。

親デバイスになれるのは IoT Edge デバイスのみですが、IoT Edge デバイスと IoT デバイスは両方とも子になることができます。 親は多数の子を持つことができますが、子は 1 つの親しか持つことができません。 ゲートウェイ階層は、あるデバイスの子が別のデバイスの親になるように親子セットを連結することで作成されます。

既定では、親は最大 100 の子を持つことができます。 この制限を変更するには、親デバイスの edgeHub モジュールで **Maxconnectedclients** 環境変数を設定します。

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal では、新しいデバイス ID を作成するとき、または既存のデバイスを編集することで、親子関係を管理できます。

新しい IoT Edge デバイスを作成するときに、そのハブ内の既存の IoT Edge デバイスの一覧から親と子のデバイスを選択するオプションがあります。

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
1. ナビゲーション メニューから **[IoT Edge]** を選択します。
1. **[IoT Edge デバイスの追加]** を選択します。
1. デバイス ID と認証の設定を行うとともに、**親デバイスを設定** したり、**子デバイスを選択** したりすることができます。
1. 親または子として使用するデバイスを 1 つまたは複数選択します。

また、既存のデバイスの親子関係を作成または管理することもできます。

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
1. ナビゲーション メニューから **[IoT Edge]** を選択します。
1. **IoT Edge デバイス** の一覧から、管理するデバイスを選択します。
1. **[親デバイスの設定]** または **[子デバイスの管理]** を選択します。
1. 任意の親または子デバイスを追加または削除します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の [azure-iot](/cli/azure/iot) 拡張機能には、IoT リソースを管理するためのコマンドが用意されています。 新しいデバイス ID を作成するとき、または既存のデバイスを編集することで、IoT および IoT Edge デバイスの親子関係を管理できます。

[az iot hub device-identity](/cli/azure/iot/hub/device-identity) コマンド セットを使用すると、指定したデバイスの親子関係を管理できます。

`create` コマンドには、デバイス作成時に子デバイスを追加したり親デバイスを設定したりするためのパラメーターが含まれています。

`add-children`、`list-children`、および `remove-children`、または `get-parent` と`set-parent` など、追加の device-identity コマンドを使用すると、既存のデバイスの親子関係を管理できます。

---

>[!NOTE]
>プログラムで親子関係を確立する場合は、C#、Java、または Node.js の [IoT Hub Service SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用できます。
>
>C# SDK を使用した子デバイスの割り当て例は、[こちら](https://github.com/Azure/azure-iot-sdk-csharp/blob/main/e2e/test/iothub/service/RegistryManagerE2ETests.cs)です。 タスク `RegistryManager_AddAndRemoveDeviceWithScope()` は、プログラムで 3 層階層を作成する方法を示しています。 第 1 層にある IoT Edge デバイスは、親として機能します。 第 2 層にある別の IoT Edge デバイスは、子と親の両方として機能します。 最後の第 3 層にある IoT デバイスは、最下層の子デバイスとして機能します。

## <a name="prepare-certificates"></a>証明書の準備

一貫した証明書チェーンを同じゲートウェイ階層内のデバイス間でインストールして、それら自体の間にセキュリティで保護された通信を確立する必要があります。 階層内のすべてのデバイスには、IoT Edge デバイスか IoT リーフ デバイスかにかかわらず、同じルート CA 証明書のコピーが必要です。 階層内の各 IoT Edge デバイスでは、そのルート CA 証明書が、そのデバイス CA 証明書のルートとして使用されます。

このセットアップでは、各ダウンストリーム IoT Edge デバイスまたは IoT リーフ デバイスは、接続先の edgeHub に、共有ルート CA 証明書によって署名されたサーバー証明書があることを確認することで、親の ID を検証できます。

<!-- TODO: certificate graphic -->

次の証明書を作成します。

* **ルート CA 証明書**。これは、指定されたゲートウェイ階層内のすべてのデバイスにとって最上位の共有証明書です。 この証明書はすべてのデバイスにインストールされます。
* ルート証明書チェーンに含める **中間証明書**。
* ルートおよび中間証明書によって生成される **デバイス CA 証明書** とその **秘密キー**。 ゲートウェイ階層内の IoT Edge デバイスごとに、一意のデバイス CA 証明書が 1 つ必要です。

自己署名証明機関を使用するか、Baltimore、Verisign、Digicert、GlobalSign などの信頼できる商用証明機関から購入することができます。

使用できる独自の証明書がない場合は、[IoT Edge デバイスの機能をテストするためのデモ証明書を作成](how-to-create-test-certificates.md)できます。 その記事の手順に従って、ルートと中間証明書のセットを 1 つ作成し、デバイスごとに IoT Edge デバイス CA 証明書を作成します。

## <a name="configure-iot-edge-on-devices"></a>デバイスで IoT Edge を構成する

IoT Edge をゲートウェイとして設定する手順は、IoT Edge をダウンストリーム デバイスとして設定する手順と非常によく似ています。

ゲートウェイの検出を有効にするには、すべての IoT Edge ゲートウェイ デバイスを、その子デバイスがローカル ネットワーク上でそれを見つけるために用いる **ホスト名** を使用して構成する必要があります。 すべてのダウンストリーム IoT Edge デバイスは、接続先の **parent_hostname** を使用して構成する必要があります。 1 つの IoT Edge デバイスが親と子デバイスの両方である場合は、両方のパラメーターが必要です。

セキュリティで保護された接続を有効にするには、ゲートウェイ シナリオ内のすべての IoT Edge デバイスを、一意のデバイス CA 証明書と、ゲートウェイ階層内のすべてのデバイスで共有されるルート CA 証明書のコピーを使用して構成する必要があります。

IoT Edge は自分のデバイスに既にインストールされている必要があります。 されていない場合は、手順に従って、[1 つの Linux IoT Edge デバイスを手動でプロビジョニング](how-to-provision-single-device-linux-symmetric.md)します。

このセクションの手順では、この記事の前半で説明した **ルート CA 証明書** および **デバイス CA 証明書と秘密キー** を参照しています。 それらの証明書を別のデバイスで作成した場合は、それらをこのデバイスで使用できるようにします。 USB ドライブを使用したり、[Azure Key Vault](../key-vault/general/overview.md) などのサービスを使用したり、[セキュア ファイル コピー](https://www.ssh.com/ssh/scp/)などの機能を使用して、ファイルを物理的に転送できます。

次の手順を使用して、お使いのデバイスで IoT Edge を構成します。

ユーザー **iotedge** に、証明書とキーが保持されているディレクトリの読み取り権限があることを確認します。

1. この IoT Edge デバイスに **ルート CA 証明書** をインストールします。

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. 証明書ストアを更新します。

   ```bash
   sudo update-ca-certificates
   ```

   このコマンドにより、1 つの証明書が /etc/ssl/certs に追加されたことが出力されます。

1. IoT Edge 構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >デバイスにまだ構成ファイルが存在しない場合は、次のコマンドを使用し、テンプレート ファイルに基づいて作成します。
   >
   >```bash
   >sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   >```

1. 構成ファイルで **ホスト名** セクションを見つけます。 `hostname` パラメーターが含まれる行をコメント解除し、IoT Edge デバイスの完全修飾ドメイン名 (FQDN) または IP アドレスになるように、値を更新します。

   このパラメーターの値は、このゲートウェイに接続するためにダウンストリーム デバイスによって使用されるものです。 hostname には既定でマシン名が使用されますが、ダウンストリーム デバイスを接続するために FQDN または IP アドレスが必要です。

   64 文字未満のホスト名を使用します。これは、サーバー証明書共通名の文字数制限です。

   ゲートウェイ階層全体のホスト名パターンとの一貫性を確保します。 FQDN または IP アドレスのいずれか (両方ではない) を使用します。

1. "*このデバイスが子デバイスの場合は*"、**親ホスト名** セクションを見つけます。 コメント解除して、`parent_hostname` パラメーターが、親デバイスの FQDN または IP アドレスになるように更新します (親デバイスの構成ファイルでホスト名として指定されたものと一致させます)。

1. **信頼バンドル証明書** セクションを見つけます。 コメント解除し、ファイルの URI を使用して、`trust_bundle_cert` パラメーターをデバイスのルート CA 証明書に更新します。

1. IoT Edge デバイスが起動時に正しいバージョンの IoT Edge エージェントを使用することを確認します。

   「**既定の Edge エージェント**」セクションを見つけて、イメージの値が IoT Edge バージョン 1.2 であることを確認します。 それ以外の場合は、次のように更新します。

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. 構成ファイルで、**Edge CA 証明書** セクションを見つけます。 このセクションの行をコメント解除し、IoT Edge デバイス上の証明書とキー ファイルにファイル URI パスを指定します。

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. 構成ファイルを保存 (`Ctrl+O`) して閉じます(`Ctrl+X`)。

1. 以前に IoT Edge に他の証明書を使用していた場合は、次の 2 つのディレクトリ内のファイルを削除して、新しい証明書が適用されるようにします。

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. 変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

1. 構成にエラーがあるかどうかを確認します。

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge チェック ツールでは、コンテナーを使用していくつかの診断チェックが実行されます。 ダウンストリーム IoT Edge デバイスでこのツールを使用する場合は、それが `mcr.microsoft.com/azureiotedge-diagnostics:latest` にアクセスできることを確認するか、コンテナー イメージをプライベート コンテナー レジストリ内に入れてください。

## <a name="network-isolate-downstream-devices"></a>ネットワークでダウンストリーム デバイスを分離する

この記事のこれまでの手順では、IoT Edge デバイスをゲートウェイまたはダウンストリーム デバイスとして設定し、それらの間に信頼関係接続を作成しています。 ゲートウェイ デバイスでは、ダウンストリーム デバイスと IoT Hub 間の相互作用 (認証とメッセージ ルーティングなど) が処理されます。 ダウンストリーム IoT Edge デバイスにデプロイされたモジュールでも、クラウド サービスへの独自の接続を作成できます。

ISA-95 標準に準拠するものなど、一部のネットワーク アーキテクチャでは、インターネット接続数を最小限にすることが模索されています。 このようなシナリオでは、直接のインターネット接続なしでダウンストリーム IoT Edge デバイスを構成することができます。 ゲートウェイ デバイス経由で IoT Hub 通信をルーティングするだけでなく、ダウンストリーム IoT Edge デバイスは、すべてのクラウド接続についてゲートウェイ デバイスに依存できます。

このネットワーク構成では、ゲートウェイ階層の最上位レイヤーにある IoT Edge デバイスだけがクラウドに直接接続できる必要があります。 下位レイヤーの IoT Edge デバイスは、その親デバイスまたは子デバイスとのみ通信できます。 ゲートウェイ デバイス上の特別なモジュールを使用すると、このシナリオが可能になります。次のものを含みます。

* **API プロキシ モジュール** は、下に別の IoT Edge デバイスがあるすべての IoT Edge ゲートウェイで必要です。 つまり、最下位レイヤーを除く、ゲートウェイ階層の "*すべてのレイヤー*" にある必要があります。 このモジュールでは、[nginx](https://nginx.org) リバース プロキシを使用して、1 つのポートでネットワーク レイヤーを介して HTTP データがルーティングされます。 これは、モジュール ツインと環境変数を使用して高度に構成可能であるため、ご自身のゲートウェイ シナリオの要件に合うように調整できます。

* **Docker レジストリ モジュール** は、ゲートウェイ階層の "*最上位レイヤー*" にある IoT Edge ゲートウェイにデプロイできます。 このモジュールは、下位レイヤーにあるすべての IoT Edge デバイスに代わってコンテナー イメージを取得してキャッシュする役割を担います。 このモジュールを最上位レイヤーにデプロイする代わりに、ローカル レジストリを使用したり、コンテナー イメージを手動でデバイスに読み込んで、モジュールのプル ポリシーを **never** に設定したりすることもできます。

* **IoT Edge 上の Azure Blob Storage** は、ゲートウェイ階層の "*最上位レイヤー*" にある IoT Edge ゲートウェイにデプロイできます。 このモジュールは、下位レイヤーにあるすべての IoT Edge デバイスに代わって BLOB をアップロードする役割を担います。 また、BLOB をアップロードする機能により、モジュール ログのアップロードやサポート バンドルのアップロードなど、下位レイヤーの IoT Edge デバイスに対する便利なトラブルシューティング機能が使用できるようになります。

### <a name="network-configuration"></a>ネットワークの構成

ネットワーク オペレーターは、最上位レイヤーの各ゲートウェイ デバイスに対して次のことを行う必要があります。

* 静的 IP アドレスまたは完全修飾ドメイン名 (FQDN) を指定します。
* ポート 443 (HTTPS) と 5671 (AMQP) 経由での、この IP アドレスから Azure IoT Hub ホスト名への送信方向の通信を承認します。
* ポート 443 (HTTPS) 経由での、この IP アドレスから Azure Container Registry ホスト名への送信方向の通信を承認します。

  API プロキシ モジュールでは、一度に 1 つのコンテナー レジストリへの接続のみを処理できます。 Microsoft Container Registry (mcr.microsoft.com) によって提供される公開イメージを含むすべてのコンテナー イメージをプライベート・コンテナー・レジストリに格納することをお勧めします。

ネットワーク オペレーターは、下位レイヤーの各ゲートウェイ デバイスに対して次のことを行う必要があります。

* 静的 IP アドレスを指定します。
* ポート 443 (HTTPS) と 5671 (AMQP) 経由での、この IP アドレスから親ゲートウェイの IP アドレスへの送信方向の通信を承認します。

### <a name="deploy-modules-to-top-layer-devices"></a>モジュールを最上位レイヤーのデバイスにデプロイする

ゲートウェイ階層の最上位レイヤーの IoT Edge デバイスには、デバイスで実行する可能性があるワークロード モジュールに加えて、デプロイする必要がある一連の必須モジュールがあります。

API プロキシ モジュールは、ほとんどの一般的なゲートウェイ シナリオを処理するためにカスタマイズするように設計されています。 この記事では、基本的構成でそれらのモジュールを設定する例を示します。 詳細情報と例については、[ゲートウェイ階層シナリオ用の API プロキシ モジュールの構成](how-to-configure-api-proxy-module.md)に関する記事を参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
1. ナビゲーション メニューから **[IoT Edge]** を選択します。
1. **IoT Edge デバイス** の一覧から、構成する上位レイヤーのデバイスを選択します。
1. **[Set modules]\(モジュールの設定\)** を選びます。
1. **[IoT Edge モジュール]** セクションで **[追加]** を選択し、次に **[Marketplace モジュール]** を選択します。
1. **[IoT Edge API プロキシ]** モジュールを検索して選択します。
1. デプロイされているモジュールの一覧から API プロキシ モジュールの名前を選択し、次のモジュール設定を更新します。
   1. **[環境変数]** タブで、**NGINX_DEFAULT_PORT** の値を `443` に更新します。
   1. **[コンテナーの作成オプション]** タブで、ポートのバインドを、ポート 443 を参照するように更新します。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   これらの変更により、ポート 443 でリッスンするように API プロキシ モジュールが構成されます。 ポートのバインドが競合しないようにするには、ポート 443 でリッスンしないように edgeHub モジュールを構成する必要があります。 代わりに、API プロキシ モジュールは、すべての edgeHub トラフィックをポート 443 でルーティングします。

1. **[ランタイム設定]** を選択し、edgeHub モジュールの作成オプションを見つけます。 ポート 443 のポート バインドを削除し、ポート 5671 と 8883 のバインドはそのままにします。

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. **[保存]** を選択して、ランタイム設定の変更を保存します。
1. もう一度 **[追加]** を選択してから、 **[IoT Edge モジュール]** を選択します。
1. 次の値を指定して、Docker レジストリ モジュールをご自身のデプロイに追加します。
   1. **IoT Edge モジュールの名前**: `registry`
   1. **[モジュール設定]** タブで、**Image URI**: `registry:latest`
   1. **[環境変数]** タブで、次の環境変数を追加します。

      * **名前**: `REGISTRY_PROXY_REMOTEURL` **[値]** :このレジストリ モジュールのマップ先となるコンテナー レジストリの URL。 たとえば、「 `https://myregistry.azurecr` 」のように入力します。

        レジストリモジュールは 1 つのコンテナー レジストリにしかマップできないため、すべてのコンテナー イメージを 1 つのプライベート コンテナー レジストリに入れることをお勧めします。

      * **名前**: `REGISTRY_PROXY_USERNAME` **[値]** :コンテナー レジストリに対して認証するユーザー名。

      * **名前**: `REGISTRY_PROXY_PASSWORD` **[値]** :コンテナー レジストリに対して認証するパスワード。

   1. **[コンテナーの作成オプション]** タブで、次のものを貼り付けます。

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. **[追加]** を選択して、モジュールをデプロイに追加します。
1. **Next:ルート** を選択して、次の手順に進みます。
1. ダウンストリーム デバイスからの device-to-cloud メッセージが IoT Hub に到達できるようにするには、すべてのメッセージを IoT Hub に渡すルートを含めます。 次に例を示します。
    1. **名前**: `Route`
    1. **値**: `FROM /messages/* INTO $upstream`
1. **[確認と作成]** を選択して、最終手順に進みます。
1. **[作成]** を選択して、ご自身のデバイスにデプロイします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で、デプロイ JSON ファイルを作成します。 次に例を示します。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": "{}"
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

   このデプロイ ファイルは、ポート 443 でリッスンするように API プロキシ モジュールを構成します。 ポートのバインドが競合しないようにするには、このファイルで、ポート 443 でリッスンしないように edgeHub モジュールを構成します。 代わりに、API プロキシ モジュールは、すべての edgeHub トラフィックをポート 443 でルーティングします。

1. 次のコマンドを入力して、IoT Edge デバイスへのデプロイを作成します。

   ```azurecli
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>下位レイヤーのデバイスにモジュールをデプロイする

ゲートウェイ階層の下位レイヤーにある IoT Edge デバイスには、デバイスで実行する可能性があるワークロード モジュールに加えて、デプロイする必要がある 1 つの必須モジュールがあります。

#### <a name="route-container-image-pulls"></a>コンテナー イメージ プルをルーティングする

ゲートウェイ階層内の IoT Edge デバイスに必要なプロキシ モジュールについて説明する前に、下位レイヤーのIoT Edge デバイスでのモジュール イメージの取得方法を理解することが重要です。

下位レイヤーのデバイスがクラウドに接続できなくても、通常どおりにモジュール イメージをプルできるようにする必要がある場合は、これらの要求を処理するようにゲートウェイ階層の最上位レイヤーのデバイスを構成する必要があります。 最上位レイヤーのデバイスは、コンテナー レジストリにマップされている Docker **レジストリ** モジュールを実行する必要があります。 そこで、コンテナー要求がルーティングされるように API プロキシ モジュールを構成します。 これらの詳細については、この記事の前のセクションで説明しています。 この構成では、下位レイヤーのデバイスは、クラウド コンテナー レジストリではなく、最上位レイヤーで実行されているレジストリを指している必要があります。

たとえば、下位レイヤーのデバイスは、`mcr.microsoft.com/azureiotedge-api-proxy:1.0` を呼び出すのではなく、`$upstream:443/azureiotedge-api-proxy:1.0` を呼び出す必要があります。

**$upstream** パラメーターは、下位レイヤー デバイスの親を指しています。したがって、要求は、コンテナー要求をレジストリ モジュールにルーティングするプロキシ環境がある最上位レイヤーに到達するまで、すべてのレイヤーを経由してルーティングされます。 この例の `:443` ポートは、親デバイスの API プロキシ モジュールがリッスンしているポートに置き換える必要があります。

API プロキシ モジュールは、1 つのレジストリ モジュールにのみルーティングでき、各レジストリ モジュールは 1 つのコンテナー レジストリにのみマップできます。 したがって、下位レイヤー デバイスでプルする必要があるイメージはすべて、単一のコンテナー レジストリに格納されている必要があります。

下位レイヤー デバイスに、ゲートウェイ階層経由でモジュールのプル要求を行わせたくない場合は、ローカル レジストリ ソリューションを管理するという選択肢もあります。 または、デプロイを作成する前にモジュール イメージをデバイスにプッシュしてから、**imagePullPolicy** を **never** に設定します。

#### <a name="bootstrap-the-iot-edge-agent"></a>IoT Edge エージェントをブートストラップする

IoT Edge エージェントは、すべての IoT Edge デバイス上で起動する最初のランタイム コンポーネントです。 ダウンストリームの IoT Edge デバイスが起動時に edgeAgent モジュール イメージにアクセスし、その後、デプロイにアクセスして残りのモジュール イメージを開始できるようにする必要があります。

IoT Edge デバイス上の構成ファイルにアクセスして認証情報、証明書、および親ホスト名を指定するときに、edgeAgent コンテナーイメージも更新します。

コンテナー イメージ要求を処理するように最上位レベルのゲートウェイ デバイスが構成されている場合は、`mcr.microsoft.com` を親ホスト名と API プロキシのリスニング ポートに置き換えます。 配置マニフェストで `$upstream` をショートカットとして使用できますが、そのためには、ルーティングを処理するために edgeHub モジュールが必要であり、この時点ではそのモジュールは開始されていません。 次に例を示します。

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

ローカル コンテナー レジストリを使用している場合、またはデバイスでコンテナー イメージを手動で提供している場合は、それに応じて構成ファイルを更新してください。

#### <a name="configure-runtime-and-deploy-proxy-module"></a>ランタイムを構成してプロキシ モジュールをデプロイする

**API プロキシ モジュール** は、クラウドとダウンストリーム IoT Edge デバイス間のすべての通信をルーティングするために必要です。 ダウンストリーム IoT Edge デバイスがない、階層の最下位レイヤーにある IoT Edge デバイスには、このモジュールは必要ありません。

API プロキシ モジュールは、ほとんどの一般的なゲートウェイ シナリオを処理するためにカスタマイズするように設計されています。 この記事では、基本的構成でモジュールを設定する手順について簡単に説明します。 詳細情報と例については、[ゲートウェイ階層シナリオ用の API プロキシ モジュールの構成](how-to-configure-api-proxy-module.md)に関する記事を参照してください。

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
1. ナビゲーション メニューから **[IoT Edge]** を選択します。
1. **IoT Edge デバイス** の一覧から、構成する下位レイヤーのデバイスを選択します。
1. **[Set modules]\(モジュールの設定\)** を選びます。
1. **[IoT Edge モジュール]** セクションで **[追加]** を選択し、次に **[Marketplace モジュール]** を選択します。
1. **[IoT Edge API プロキシ]** モジュールを検索して選択します。
1. デプロイされているモジュールの一覧から API プロキシ モジュールの名前を選択し、次のモジュール設定を更新します。
   1. **[モジュール設定]** タブで、 **[イメージ URI]** の値を更新します。 `mcr.microsoft.com` を `$upstream:443` で置き換え
   1. **[環境変数]** タブで、**NGINX_DEFAULT_PORT** の値を `443` に更新します。
   1. **[コンテナーの作成オプション]** タブで、ポートのバインドを、ポート 443 を参照するように更新します。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   これらの変更により、ポート 443 でリッスンするように API プロキシ モジュールが構成されます。 ポートのバインドが競合しないようにするには、ポート 443 でリッスンしないように edgeHub モジュールを構成する必要があります。 代わりに、API プロキシ モジュールは、すべての edgeHub トラフィックをポート 443 でルーティングします。

1. **[ランタイムの設定]** を選択します。
1. edgeHub モジュールの設定を更新します。

   1. **[イメージ]** フィールドで、`mcr.microsoft.com` を `$upstream:443` に置き換えます。
   1. **[作成オプション]** フィールドで、ポート 443 のポート バインドを削除し、ポート 5671 と 8883 のバインドはそのままにします。

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. edgeAgent モジュールの設定を更新します。
   1. **[イメージ]** フィールドで、`mcr.microsoft.com` を `$upstream:443` に置き換えます。

1. **[保存]** を選択して、ランタイム設定の変更を保存します。
1. **Next:ルート** を選択して、次の手順に進みます。
1. ダウンストリーム デバイスからの device-to-cloud メッセージが IoT Hub に到達できるようにするには、すべてのメッセージを `$upstream` に渡すルートを含めます。 下位レイヤーのデバイスの場合、アップストリーム パラメーターは親デバイスを指します。 次に例を示します。
    1. **名前**: `Route`
    1. **値**: `FROM /messages/* INTO $upstream`
1. **[確認と作成]** を選択して、最終手順に進みます。
1. **[作成]** を選択して、ご自身のデバイスにデプロイします。

## <a name="next-steps"></a>次のステップ

[IoT Edge デバイスをゲートウェイとして使用する方法](iot-edge-as-gateway.md)

[ゲートウェイ階層シナリオ用に API プロキシ モジュールを構成する](how-to-configure-api-proxy-module.md)