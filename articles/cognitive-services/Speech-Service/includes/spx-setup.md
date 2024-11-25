---
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/28/2021
ms.author: eric-urban
ms.custom: ignite-fall-2021
ms.openlocfilehash: 197c186f87eacfb19412c12bc171b46fd59b0ef2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501976"
---
## <a name="download-and-install"></a>ダウンロードしてインストールする

#### <a name="windows-install"></a>[Windows のインストール](#tab/windowsinstall)

Windows に Speech CLI をインストールするには、次の手順に従います。

1. Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。
1. [.NET Core 3.1 SDK](/dotnet/core/install/windows) をインストールします。
2. 次のコマンドを入力して、NuGet を使用して Speech CLI をインストールします。

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI
   ```
「`spx`」と入力して、Speech CLI のヘルプを表示します。

> [!NOTE]
> NuGet を使用せずに、Speech CLI for Windows を [ZIP ファイル](https://aka.ms/speech/spx-windows)としてダウンロードして展開することもできます。

### <a name="font-limitations"></a>フォントの制限事項

Windows の Speech CLI では、ローカル コンピューター上のコマンド プロンプトで使用できるフォントのみを表示できます。
<<<<<<< HEAD
[Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701)では、Speech CLI によって対話的に生成されるすべてのフォントがサポートされます。
=======
[Windows ターミナル](https://www.microsoft.com/p/windows-terminal/9n0dx20hk701)では、Speech CLI によって対話的に生成されるすべてのフォントがサポートされます。
>>>>>>> repo_sync_working_branch

ファイルに出力すると、メモ帳などのテキスト エディターや、Microsoft Edge などの Web ブラウザーでも、すべてのフォントを表示できます。

#### <a name="linux-install"></a>[Linux のインストール](#tab/linuxinstall)

Speech CLI を使用する x64 アーキテクチャでは、次の Linux ディストリビューションがサポートされています。

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04 (9 月まで)、Ubuntu 18.04/20.04

> [!NOTE]
> 追加のアーキテクチャが Speech SDK (Speech CLI ではない) によってサポートされています。 詳細については、「[Speech SDK について](../speech-sdk.md)」を参照してください。

Linux の x64 CPU で Speech CLI をインストールするには、次の手順に従います。

1. [.NET Core 3.1 SDK](/dotnet/core/install/linux) をインストールします。
2. 次のコマンドを入力して、NuGet を使用して Speech CLI をインストールします。

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI`

3. RHEL または CentOS Linux で、[Linux 用の OpenSSL を構成](../how-to-configure-openssl-linux.md)します。
4. Ubunutu 20.04 Linux に、[GStreamer をインストール](../how-to-use-codec-compressed-audio-input-streams.md)します。

「`spx`」と入力して、Speech CLI のヘルプを表示します。

> [!NOTE]
> NuGet を使用せずに、Linux バイナリを [ZIP ファイル](https://aka.ms/speech/spx-linux)としてダウンロードすることもできます。
> `spx-netcore-30-linux-x64.zip` を新しい `~/spx` ディレクトリに抽出し、バイナリに対する「`sudo chmod +r+x spx`」を入力して、PATH システム変数に `~/spx` パスを追加します。


#### <a name="docker-install-windows-linux-macos"></a>[Docker のインストール (Windows、Linux、macOS)](#tab/dockerinstall)

> [!IMPORTANT]
> Docker コンテナー内で Speech CLI を実行するときは、コンピューターのマイクを使用することはできません。 ただし、ローカルにマウントされたディレクトリ内のオーディオ ファイルを読み取って保存することができます。 

> [!NOTE]
> 次の例では、パブリック コンテナー イメージを Docker Hub からプルします。 匿名の pull request を行うのではなく、最初に Docker Hub アカウント (`docker login`) で認証を行うことをお勧めします。 パブリック コンテンツを使用するときの信頼性を向上させるには、プライベートの Azure Container Registry にイメージをインポートして管理します。 [パブリック イメージの操作に関する詳細を参照してください](../../../container-registry/buffer-gate-public-content.md)。

Docker コンテナー内に Speech CLI をインストールするには、次の手順に従います。

1. プラットフォームにインストールされていない場合、<a href="https://www.docker.com/get-started" target="_blank">Docker Desktop をインストール</a>します。
2. 新しいコマンド プロンプトまたはターミナルで、次のコマンドを入力します: 
   ```console   
   docker pull msftspeech/spx
   ```
3. 次のコマンドを入力します。 Speech CLI のヘルプ情報が表示されます: 
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>コンテナーにディレクトリをマウントする

Speech CLI ツールは、構成設定をファイルとして保存し、ヘルプ コマンドを除く任意のコマンドを実行するときに、これらのファイルを読み込みます。
Docker コンテナー内で Speech CLI を使用する際は、コンテナーからローカル ディレクトリをマウントする必要があります。これにより、ツールで構成設定を格納したり検索したりできるようになります。また、音声のオーディオ ファイルなど、コマンドに必要なファイルの読み取りまたは書き込みを行うことができるようになります。

Windows の場合は、次のコマンドを入力して、Speech CLI がコンテナー内から使用できるローカル ディレクトリを作成します。

`mkdir c:\spx-data`

Linux または macOS の場合は、ターミナルでこのコマンドを入力してディレクトリを作成し、その絶対パスを確認します。

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Speech CLI を呼び出すときは、この絶対パスを使用します。

### <a name="run-speech-cli-in-the-container"></a>コンテナーで Speech CLI を実行する

このドキュメントでは、Docker 以外のインストールで使用される Speech CLI `spx` コマンドについて説明します。
Docker コンテナーで `spx` コマンドを呼び出す場合は、Speech CLI が構成値を格納および検索してファイルを読み書きできるように、コンテナー内のディレクトリをファイル システムにマウントする必要があります。

Windows の場合、コマンドは次のように始まります。

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

Linux または macOS の場合、コマンドは以下の例のようになります。 `ABSOLUTE_PATH` をマウントされたディレクトリの絶対パスに置き換えます。 このパスは、前のセクションの `pwd` コマンドによって返されました。 

キーとリージョンを設定する前にこのコマンドを実行すると、キーとリージョンを設定するよう求めるエラーが表示されます。
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

コンテナーにインストールされている `spx` コマンドを使用するには、必ず上記の完全なコマンドを入力し、その後に要求のパラメーターを入力します。
たとえば、Windows では、次のコマンドによってキーが設定されます。

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

コマンド ライン ツールとのより拡張された操作については、entrypoint パラメーターを追加することで、対話型の bash シェルでコンテナーを開始できます。
Windows では、このコマンドを入力して、対話型のコマンド ライン インターフェイスを公開するコンテナーを起動します。ここでは、複数の `spx` コマンドを入力できます。
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>サブスクリプション構成の作成

Speech CLI の使用を開始するには、Speech サブスクリプション キーとリージョン識別子を入力する必要があります。 「[Speech Service を無料で試す](../overview.md#try-the-speech-service-for-free)」の手順に従って、これらの資格情報を取得します。
サブスクリプション キーとリージョン識別子 (たとえば、 `eastus`、`westus`) を入手したら、次のコマンドを実行します。

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

これで、今後の SPX 要求のためのサブスクリプション認証が格納されるようになりました。 これらの格納されている値のいずれかを削除する必要がある場合は、`spx config @region --clear` または `spx config @key --clear` を実行します。
