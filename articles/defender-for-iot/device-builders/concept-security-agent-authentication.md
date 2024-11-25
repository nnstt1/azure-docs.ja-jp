---
title: セキュリティ エージェントの認証 (プレビュー)
description: 2 つの方法を使用して、マイクロ エージェント認証を実行します。
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: ab7d624ef206120751d273781384d440cea5802d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709538"
---
# <a name="micro-agent-authentication-methods-preview"></a>マイクロ エージェントの認証方法 (プレビュー)

Defender for IoT マイクロ エージェントを使用した認証には、次の 2 つのオプションがあります。 

- 接続文字列 

- Certificate 

## <a name="authentication-using-a-connection-string"></a>接続文字列を使用して認証する 

接続文字列を使用するには、Defender for Cloud エージェント ディレクトリで utf-8 でエンコードされた接続文字列を使用するファイルを、`connection_string.txt` というファイルに追加する必要があります。 たとえば、オブジェクトに適用された

```azurecli
echo “<connection string>” > connection_string.txt 
```

これが完了したら、次のコマンドを使用してサービスを再起動する必要があります。

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>証明書を使用して認証する 


証明書を使用して認証を実行するには、以下の手順に従います。 

1. PEM でエンコードされた証明書の公開部分を、Defender for Cloud エージェント ディレクトリである `certificate_public.pem` という名前のファイルに配置します。
1. PEM でエンコードされた秘密キーを、Defender for Cloud エージェント ディレクトリである `certificate_private.pem` という名前のファイルに配置します。
1. 適切な接続文字列を `connection_string.txt` という名前のファイルに配置します。 たとえば、オブジェクトに適用された

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    このアクションは、認証用に証明書が指定されることが、Defender for Cloud エージェントで想定されていることを示します。 

1. 次のコードを使用して、サービスを再起動します。 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>マイクロ エージェントが正しく実行されていることを確認する 

1. 次のコマンドを実行します。 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. サービスが **アクティブ** であること、およびプロセスのアップタイムが適切であることを確認して、サービスが安定していることを確認します。 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="サービスがアクティブであることを確認して、サービスが安定していることを確認します。":::

## <a name="next-steps"></a>次のステップ

[セキュリティ体制 - CIS ベンチマーク](concept-security-posture.md)を確認します。
