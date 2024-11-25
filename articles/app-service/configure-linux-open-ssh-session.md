---
title: SSH access for Linux コンテナー
description: Azure App Service で Linux コンテナーへの SSH セッションを開くことができます。 カスタムの Linux コンテナーは、カスタムイメージにいくつかの変更を加えてサポートされます。
keywords: Azure App Service, Web アプリ, Linux, OSS
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 09/10/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 955df8f28345baae89dafc8f3b6116b89e0e1b96
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768023"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Azure App Service で Linux コンテナーへの SSH セッションを開く

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) は、一般にコマンド ライン ターミナルから管理コマンドをリモートで実行するために使用されます。 Linux の App Service では、アプリコンテナーへの SSH サポートが提供されます。 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

SSH と SFTP を使用して、ローカル開発マシンからコンテナーに直接接続することもできます。

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>カスタム Docker イメージで SSH サポートを使用する

[カスタム コンテナーでの SSH の構成](configure-custom-container.md#enable-ssh)に関するページを参照してください。

## <a name="open-ssh-session-from-remote-shell"></a>リモート シェルから SSH セッションを開く

> [!NOTE]
> 現在、この機能はプレビュー段階にあります。
>

TCP トンネリングを使用して、認証済みの WebSocket 接続経由で開発用マシンと Web App for Containers 間にネットワーク接続を作成できます。 これにより、任意のクライアントから App Service で実行されているコンテナーとの SSH セッションを開くことができます。

最初に、[Azure CLI](/cli/azure/install-azure-cli) をインストールする必要があります。 Azure CLI をインストールしないとどのように動作するかを確認するには、[Azure Cloud Shell](../cloud-shell/overview.md) を開きます。 

[az webapp create-remote-connection](/cli/azure/webapp#az_webapp_create_remote_connection) コマンドを使用して、アプリへのリモート接続を開きます。 アプリの _\<subscription-id>_ 、 _\<group-name>_ 、 _\<app-name>_ を指定します。

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> コマンドの末尾の `&` は、Cloud Shell を使用している場合の便宜のためにあります。 同じシェルで次のコマンドを実行できるように、プロセスをバック グラウンドで実行します。

> [!NOTE]
> このコマンドが失敗した場合は、次のコマンドで [リモート デバッグ](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)が *無効* になっていることを確認してください。
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

コマンドの出力では、SSH セッションを開くために必要な情報が示されます。

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

ローカル ポートを使用して、任意のクライアントでコンテナーとの SSH セッションを開きます。 次の例では、既定の [ssh](https://ss64.com/bash/ssh.html) コマンドを使用しています。

```bash
ssh root@127.0.0.1 -p <port>
```

プロンプトが表示されたら、`yes` と入力して接続を続行します。 パスワードを入力するように求められます。 前に示された `Docker!` を使用します。

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

認証されると、セッションのようこそ画面が表示されます。

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

これでコネクタに接続されました。  

[top](https://ss64.com/bash/top.html) コマンドを実行してみます。 プロセスの一覧にアプリのプロセスが表示されます。 次の出力例では、`PID 263` のものです。

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>次のステップ

質問や問題は、[Azure フォーラム](/answers/topics/azure-webapps.html)に投稿できます。

Web App for Containers について詳しくは、以下をご覧ください。

* [VS Code からの Azure App Service 上での Node.js アプリのリモート デバッグの概要](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [クイック スタート: App Service でカスタム コンテナーを実行する](quickstart-custom-container.md?pivots=container-linux)
* [Azure App Service on Linux で Ruby を使用する](quickstart-ruby.md)
* [Azure App Service Web App for Containers の FAQ](faq-app-service-linux.yml)
