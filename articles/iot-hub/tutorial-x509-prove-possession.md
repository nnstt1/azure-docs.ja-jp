---
title: チュートリアル - Azure IoT Hub の CA 証明書の所有権を証明する | Microsoft Docs
description: チュートリアル - Azure IoT Hub の CA 証明書を所有していることを証明する
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 79b55e938d832602dca87af8ee366cd44aa44a91
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547167"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>チュートリアル: CA 証明書の所有を証明する

ルート証明機関 (CA) 証明書または下位 CA 証明書を IoT ハブにアップロードするとき、有効性が自動的に検証されるように設定するか、証明書を所有していることを手動で証明する必要があります。

## <a name="verify-certificate-automatically"></a>証明書の有効性を自動的に検証する 

1. Azure portal で自分の IoT ハブに移動し、 **[設定] > [証明書]** の順に選択します。

2. **[追加]** を選択して、新しい CA 証明書を追加します。

3. **[証明書名]** フィールドに表示名を入力し、追加する PEM 証明書を選択します。

4. 証明書を自動的に検証するには、 **[証明書の状態をアップロード時に確認済みに設定する]** の横にあるチェック ボックスをオンにします。

  :::image type="content" source="media/tutorial-x509-prove-possession/skip-pop.png" alt-text="所有証明をスキップするチェックボックスの場所を示すスクリーンショット":::

5. **[保存]** を選択します。  証明書が証明書の一覧に **[確認済み]** の状態で表示されます。

## <a name="verify-certificate-manually-after-upload"></a>アップロード後に証明書の有効性を手動で確認する

1. アップロード中に証明書を自動的に検証しなかった場合は、証明書の一覧に **[未確認]** の状態で証明書が表示されます。 

2. 証明書を選択して、 **[証明書の詳細]** ダイアログを表示します。

3. ダイアログ ボックスの **[確認コードを生成します]** を選択します。

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{[証明書の詳細] ダイアログ}":::

4. 確認コードをクリップボードにコピーします。 証明書のサブジェクトとして確認コードを設定する必要があります。 たとえば、確認コードが 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3 の場合は、次の手順で示すように、証明書のサブジェクトとしてそれを追加します。

5. 検証証明書を生成するには、次の 3 つの方法があります。

    * Microsoft によって提供された PowerShell スクリプトを使用している場合は、`New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` を実行して、`VerifyCert4.cer` という名前の証明書を作成します。 詳細については、[Microsoft 提供のスクリプトの使用](tutorial-x509-scripts.md)に関するページを参照してください。

    * Microsoft によって提供された Bash スクリプトを使用している場合は、`./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` を実行して、`verification-code.cert.pem` という名前の証明書を作成します。 詳細については、[Microsoft 提供のスクリプトの使用](tutorial-x509-scripts.md)に関するページを参照してください。

    * 証明書を生成するために OpenSSL を使用している場合は、まず秘密キーを生成し、次に証明書署名要求 (CSR) を生成する必要があります。

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      次に、ルート CA 構成ファイル (以下を参照) または下位 CA 構成ファイルと CSR を使用して、証明書を作成します。

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    詳細については、[OpenSSL を使用したテスト証明書の作成](tutorial-x509-openssl.md)に関するページを参照してください。

6. **[証明書の詳細]** ビューで、新しい証明書を選択します。

7. 証明書をアップロードしたら、 **[確認]** を選択します。 CA 証明書の状態が **[確認済み]** に変わるはずです。
