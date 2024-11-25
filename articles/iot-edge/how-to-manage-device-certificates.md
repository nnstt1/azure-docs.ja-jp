---
title: デバイス証明書を作成する - Azure IoT Edge | Microsoft Docs
description: テスト証明書を作成し、それらを Azure IoT Edge デバイスにインストールして運用環境のデプロイの準備をします。
author: kgremban
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3866217f0aa90cd3450d0f74e35eaa68cea3c559
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866546"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>IoT Edge デバイスで証明書を管理する

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

すべての IoT Edge デバイスは、証明書を使用して、デバイス上で実行されるランタイムと任意のモジュール間の安全な接続を作成します。 ゲートウェイとして機能している IoT Edge デバイスは、これらの同じ証明書を使用してダウンストリームのデバイスに接続します。

## <a name="install-production-certificates"></a>運用環境の証明書をインストールする

初めて IoT Edge をインストールしてデバイスをプロビジョニングする場合、サービスをテストできるように、デバイスは一時的な証明書を使用して設定されます。
これらの一時的な証明書は 90 日で有効期限が切れます。もしくは、マシンを再起動するとリセットされます。
運用環境に移行した後、またはゲートウェイデバイスを作成する場合は、独自の証明書を指定する必要があります。
この記事では、証明書を IoT Edge デバイスにインストールするステップを説明します。

さまざまな種類の証明書や、それらの役割についての詳細は、「[Azure IoT Edge 証明書の使用方法の詳細](iot-edge-certs.md)」を参照してください。

>[!NOTE]
>この記事全体で使用される「ルート CA」という用語は、自分の IoT ソリューションの証明書チェーンのうち、最上位の公開証明機関を示しています。 シンジケート証明機関の証明書ルートや、組織の証明機関のルートを使用する必要はありません。 多くの場合、実際は中間 CA の公開証明書です。

### <a name="prerequisites"></a>前提条件

* IoT Edge デバイス。

  IoT Edge デバイスを設定していない場合は、Azure 仮想マシンで作成できます。 クイックスタートの記事のいずれかの手順に従って、[仮想 Linux デバイスを作成](quickstart-linux.md)するか、[仮想 Windows デバイスを作成](quickstart.md)します。

* 自己署名したか、Baltimore、Verisign、DigiCert、GlobalSign などの信頼されている商用証明機関から購入したルート証明機関 (CA) の証明書がある。

  ルート証明機関がまだないものの、運用証明書を必要とする IoT Edge 機能を試してみたい (ゲートウェイ シナリオなど) 場合、「[デモ証明書を作成して IoT Edge デバイスの機能をテストする](how-to-create-test-certificates.md)」を参照してください。

### <a name="create-production-certificates"></a>運用証明書を作成する

独自の証明機関を使用して次のファイルを作成する必要があります。

* ルート CA
* デバイス CA 証明書
* デバイス CA 秘密キー

この記事で *ルート CA* と呼ばれているものは、組織の最上位の証明機関ではありません。 これは IoT Edge シナリオの最上位の証明機関であり、IoT Edge ハブ モジュール、ユーザー モジュール、他のダウンストリームのデバイスはこれを利用してお互いの間で信頼関係を確立します。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

:::moniker-end

これらの証明書の例を確認するには、「[サンプルとチュートリアルのためのテスト CA 証明書を管理する](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)」にある、デモ証明書を作成するスクリプトをご確認ください。

### <a name="install-certificates-on-the-device"></a>証明書をデバイスにインストールする

証明書チェーンを IoT Edge デバイスにインストールし、新しい証明書を参照するように IoT Edge ランタイムを構成します。

3 つの証明書とキー ファイルを IoT Edge デバイスにコピーします。 

サンプル スクリプトを使用して[デモ証明書を作成](how-to-create-test-certificates.md)した場合、3 つの証明書とキー ファイルは次のパスにあります。

* デバイス CA 証明書: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* デバイス CA 秘密キー: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* ルート CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

[Azure Key Vault](../key-vault/index.yml) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。 IoT Edge デバイス自体で証明書を生成した場合は、この手順をスキップして、作業ディレクトリへのパスを使用することができます。

IoT Edge for Linux on Windows を使用している場合は、Azure IoT Edge の `id_rsa` ファイルにある SSH キーを使用して、ホスト OS と Linux 仮想マシンとの間のファイル転送を認証する必要があります。 Linux 仮想マシンの IP アドレスを `Get-EflowVmAddr` コマンドで取得します。 その後、認証された SCP を次のコマンドを使用して実行できます。

   ```powershell
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

### <a name="configure-iot-edge-with-the-new-certificates"></a>新しい証明書で IoT Edge を構成する

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

# <a name="linux-containers"></a>[Linux コンテナー](#tab/linux)

1. IoT Edge セキュリティ デーモン構成ファイル `/etc/iotedge/config.yaml` を開きます。

1. config.yaml 内の **certificate** プロパティを、IoT Edge デバイス上の証明書ファイルとキー ファイルへのファイル URI パスに設定します。 証明書プロパティの前の `#` 文字を削除して、4 行をコメント解除します。 **certificates:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。 次に例を示します。

   ```yaml
   certificates:
      device_ca_cert: "file:///<path>/<device CA cert>"
      device_ca_pk: "file:///<path>/<device CA key>"
      trusted_ca_certs: "file:///<path>/<root CA cert>"
   ```

1. ユーザー **iotedge** に、証明書を保持するディレクトリの読み取り権限があることを確認します。

1. 以前にこのデバイスの IoT Edge に他の証明書を使用していた場合は、IoT Edge を起動または再起動する前に、次の 2 つのディレクトリにあるファイルを削除します。

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. IoT Edge を再起動します。

   ```bash
   sudo iotedge system restart
   ```

# <a name="windows-containers"></a>[Windows コンテナー](#tab/windows)

1. IoT Edge セキュリティ デーモン構成ファイル `C:\ProgramData\iotedge\config.yaml` を開きます。

1. config.yaml 内の **certificate** プロパティを、IoT Edge デバイス上の証明書ファイルとキー ファイルへのファイル URI パスに設定します。 証明書プロパティの前の `#` 文字を削除して、4 行をコメント解除します。 **certificates:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。 次に例を示します。

   ```yaml
   certificates:
      device_ca_cert: "file:///C:/<path>/<device CA cert>"
      device_ca_pk: "file:///C:/<path>/<device CA key>"
      trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
   ```

1. 以前にこのデバイスの IoT Edge に他の証明書を使用していた場合は、IoT Edge を起動または再起動する前に、次の 2 つのディレクトリにあるファイルを削除します。

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. IoT Edge を再起動します。

   ```powershell
   Restart-Service iotedge
   ```
---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge セキュリティ デーモン構成ファイル `/etc/aziot/config.toml` を開きます。

1. ファイルの先頭にある `trust_bundle_cert` パラメーターを見つけます。 この行をコメント解除し、デバイス上のルート CA 証明書にファイルの URI を指定します。

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. config.toml ファイルで `[edge_ca]` セクションを見つけます。 このセクションの行をコメント解除し、IoT Edge デバイス上の証明書とキー ファイルにファイル URI パスを指定します。

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. 証明書とキーが保持されているディレクトリの読み取りアクセス許可がサービスにあることを確認します。

   * プライベート キー ファイルは、**aziotks** グループによって所有されている必要があります。
   * 証明書ファイルは、**aziotcs** グループによって所有されている必要があります。

   >[!TIP]
   >証明書が読み取り専用の場合、つまり、作成した証明書を IoT Edge サービスでローテーションしない場合、プライベート キー ファイルをモード 0440 に設定し、証明書ファイルをモード 0444 に設定します。 初回ファイルを作成し、証明書を今後ローテーションするように証明書サービスを構成した場合、プライベート キー ファイルをモード 0660 に設定し、証明書ファイルをモード 0664 に設定します。

1. 以前にデバイスで IoT Edge に他の証明書を使用していた場合次のディレクトリにあるファイルを削除します。 それらは IoT Edge によって、指定した新しい CA 証明書で再作成されます。

   * `/var/lib/aziot/certd/certs`

1. 構成の変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="customize-certificate-lifetime"></a>証明書の有効期間をカスタマイズする

IoT Edge では、次のような複数の場合に、デバイスに証明書を自動的に生成します。

* IoT Edge のインストールとプロビジョニングを行うときに自身の運用証明書を提供しない場合は、IoT Edge セキュリティ マネージャーによって、**デバイス CA 証明書** が自動的に生成されます。 この自己署名証明書は、運用環境ではなく、開発およびテスト シナリオのみを対象としています。 この証明書は 90 日後に有効期限が切れます。
* IoT Edge セキュリティ マネージャーは、デバイス CA 証明書によって署名された **ワークロード CA 証明書** も生成します

IoT Edge デバイスでのさまざまな証明書の機能の詳細については、「[Azure IoT Edge での証明書の使用方法について理解する](iot-edge-certs.md)」を参照してください。

自動的に生成されたこれら 2 つの証明書に対して、構成ファイルでフラグを設定して証明書の有効期間の日数を構成することもできます。

>[!NOTE]
>IoT Edge セキュリティ マネージャーによって自動生成される 3 つ目の証明書として、**IoT Edge ハブ サーバー証明書** があります。 この証明書の有効期間は常に 90 日ですが、有効期限が切れる前に自動的に更新されます。 構成ファイルに設定されている自動生成の CA 有効期間値はこの証明書に影響を与えません。

指定された日数が経過して有効期限が切れると、IoT Edge を再起動してデバイス CA 証明書を再生成する必要があります。 デバイス CA 証明書は自動的に更新されません。

<!-- 1.1. -->
:::moniker range="iotedge-2018-06"

# <a name="linux-containers"></a>[Linux コンテナー](#tab/linux)

1. 証明書の有効期限を既定値の 90 日以外に構成するには、構成ファイルの **certificates** セクションに日数の値を追加します。

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

1. `hsm` フォルダーの内容を削除して、以前に生成された証明書をすべて削除します。

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. IoT Edge サービスを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

1. 有効期間の設定を確認します。

   ```bash
   sudo iotedge check --verbose
   ```

   自動的に生成されたデバイス CA 証明書の有効期限が切れるまでの日数が表示された **production readiness: certificates** チェックの出力を確認します。

# <a name="windows-containers"></a>[Windows コンテナー](#tab/windows)

1. 証明書の有効期限を既定値の 90 日以外に構成するには、構成ファイルの **certificates** セクションに日数の値を追加します。

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

1. `hsm` フォルダーの内容を削除して、以前に生成された証明書をすべて削除します。

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. IoT Edge サービスを再起動します。

   ```powershell
   Restart-Service iotedge
   ```

1. 有効期間の設定を確認します。

   ```powershell
   iotedge check --verbose
   ```

   自動的に生成されたデバイス CA 証明書の有効期限が切れるまでの日数が表示された **production readiness: certificates** チェックの出力を確認します。

---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 証明書の有効期限を既定値の 90 日以外に構成するには、構成ファイルの **Edge CA 証明書 (クイックスタート)** セクションに日数の値を追加します。

   ```toml
   [edge_ca]
   auto_generated_edge_ca_expiry_days = <value>
   ```

1. `certd` フォルダーと `keyd` フォルダーの内容を削除し、以前に生成された証明書 `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys` を削除します。

1. 構成の変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

1. 新しい有効期間設定を確認します。

   ```bash
   sudo iotedge check --verbose
   ```

   自動的に生成されたデバイス CA 証明書の有効期限が切れるまでの日数が表示された **production readiness: certificates** チェックの出力を確認します。
:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>次のステップ

証明書を IoT Edge デバイス上にインストールするステップは、ソリューションを運用環境にデプロイする前に必ず実行する必要があります。 詳細については、「[IoT Edge ソリューションを運用環境にデプロイするための準備を行う](production-checklist.md)」を参照してください。
