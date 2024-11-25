---
title: チュートリアル - OpenSSL を使用して Azure IoT Hub 向けの自己署名証明書を作成する | Microsoft Docs
description: チュートリアル - OpenSSL を使用して Azure IoT Hub 向けの自己署名 X.509 証明書を作成する
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: fe08b1dd87c754254067a9fc2ae4d022b9224d3d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551465"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>チュートリアル: OpenSSL を使用して自己署名証明書を作成する

IoT ハブに対するデバイスの認証は、2 つの自己署名デバイス証明書を使用して行うことができます。 IoT ハブに送信する拇印 (ハッシュ値) が証明書に含まれることから、これは拇印認証と呼ばれることがあります。 次の手順では、2 つの自己署名証明書を作成する方法について説明します。 この種類の証明書は、主にテストに使用されます。

## <a name="step-1---create-a-key-for-the-first-certificate"></a>手順 1 - 1 つ目の証明書のキーを作成する

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>手順 2 - 1 つ目の証明書の CSR を作成する

確認を求められたら必ずデバイス ID を指定してください。

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>手順 3 - CSR を確認する

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>手順 4 - 証明書 1 に自己署名する

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device1.crt
```

## <a name="step-5---create-a-key-for-the-second-certificate"></a>手順 5 - 2 つ目の証明書のキーを作成する

```bash
openssl genpkey -out device2.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-6---create-a-csr-for-the-second-certificate"></a>手順 6 - 2 つ目の証明書の CSR を作成する

確認を求められたら、証明書 1 に使用したものと同じデバイス ID を指定します。

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:
```

## <a name="step-7---self-sign-certificate-2"></a>手順 7 - 証明書 2 に自己署名する

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-1"></a>手順 8 - 証明書 1 の拇印を取得する

```bash
openssl x509 -in device1.crt -noout -fingerprint
```

## <a name="step-9---retrieve-the-thumbprint-for-certificate-2"></a>手順 9 - 証明書 2 の拇印を取得する

```bash
openssl x509 -in device2.crt -noout -fingerprint
```

## <a name="step-10---create-a-new-iot-device"></a>手順 10 - 新しい IoT デバイスを作成する

Azure portal で自分の IoT ハブに移動し、次の特性で新しい IoT デバイス ID を作成します。

* 2 つの証明書のサブジェクト名と一致する **[デバイス ID]** を指定します。
* 認証タイプは **[X.509 自己署名済み]** を選択します。
* デバイスのプライマリとセカンダリの証明書からコピーした 16 進数の文字列の拇印を貼り付けます。 16 進数の文字列に、コロンの区切り記号がないことを確認してください。


## <a name="next-steps"></a>次の手順

「[証明書認証のテスト](tutorial-x509-test-certificate.md)」を参照し、証明書を使ってデバイスを IoT ハブに認証できるかどうかを確認します。 そのページのコードでは、PFX 証明書を使用する必要があります。 デバイスの .crt 証明書を .pfx 形式に変換するには、次の OpenSSL コマンドを使用します。

```bash
openssl pkcs12 -export -in device.crt -inkey device.key -out device.pfx
```
