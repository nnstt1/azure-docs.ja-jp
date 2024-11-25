---
title: Azure Media Services v3 API に接続する - .NET
description: この記事では、.NET を使用して Media Services v3 API に接続する方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 310085b26c75087d6e4e7f69bf5b4f7ad44a9904
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419359"
---
# <a name="connect-to-media-services-v3-api---net"></a>Media Services v3 API に接続する - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、サービス プリンシパルによるログイン方式を使用して Azure Media Services v3 .NET SDK に接続する方法を説明します。

## <a name="prerequisites"></a>前提条件

- [Media Services アカウントを作成する](./account-create-how-to.md) リソース グループ名と Media Services アカウント名を覚えておいてください
- .NET の開発に使用するツールをインストールします。 この記事の手順では、[Visual Studio 2019 Community エディション](https://www.visualstudio.com/downloads/)を使用する方法を示します。 Visual Studio Code を使用できます。「[Working with C# (C# の使用)](https://code.visualstudio.com/docs/languages/csharp)」をご覧ください。 または、別のコード エディターを使用できます。

> [!IMPORTANT]
> [命名規則](media-services-apis-overview.md#naming-conventions)を確認してください。

## <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio を起動します。 
1. **[ファイル]** メニューから **[新規作成]**  >  **[プロジェクト]** をクリックします。 
1. **.NET Core** コンソール アプリケーションを作成します。

このトピックのサンプル アプリの対象は `netcoreapp2.0` です。 コードでは "async main" が使用されています。これは、C# 7.1 以降で利用できます。 詳しくは、こちらの[ブログ](/archive/blogs/benwilli/async-main-is-available-but-hidden)をご覧ください。

## <a name="add-required-nuget-packagesassemblies"></a>必要な NuGet パッケージおよびアセンブリを追加する

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[NuGet Manager Console]\(NuGet マネージャー コンソール\)** の順に選択します。
2. **[パッケージ マネージャー コンソール]** ウィンドウで、`Install-Package` コマンドを使用して次の NuGet パッケージを追加します。 たとえば、「 `Install-Package Microsoft.Azure.Management.Media` 」のように入力します。

|Package|説明|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK。 <br/>最新の Azure Media Services パッケージを使用していることを確認するには、[Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media) をチェックします。|

### <a name="other-required-assemblies"></a>その他の必要なアセンブリ

- Azure.Storage.Blobs
- Microsoft.Extensions.Configuration
- Microsoft.Extensions.Configuration.EnvironmentVariables
- Microsoft.Extensions.Configuration.Json
- Microsoft.Rest.ClientRuntime.Azure.Authentication

## <a name="create-and-configure-the-app-settings-file"></a>アプリ設定ファイルを作成して構成する

### <a name="create-appsettingsjson"></a>appsettings.json を作成する

1. **[全般]**  >  **[テキスト ファイル]** に移動します。
1. "appsettings.json" という名前に設定します。
1. .json ファイルの [出力ディレクトリにコピー] プロパティを [新しい場合はコピーする] に設定します (発行されたときにアプリケーションがアクセスできるように)。

### <a name="set-values-in-appsettingsjson"></a>appsettings.json の値を設定する

[API へのアクセス](./access-api-howto.md)に関する記事の説明に従って、`az ams account sp create` コマンドを実行します。 コマンドで返される json を、"appsettings.json" にコピーする必要があります。
 
## <a name="add-configuration-file"></a>Add configuration file

便利なように、"appsettings.json" から値を読み取る構成ファイルを追加します。

1. 新しい .cs クラスをプロジェクトに追加します。 これに `ConfigWrapper` という名前を付けます。 
1. 次のコードをこのファイルに貼り付けます (この例では、名前空間が `ConsoleApp1` であるとします)。

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>.NET クライアントに接続する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 次のコードでは、ローカル構成ファイルで指定された資格情報に基づいて、GetCredentialsAsync 関数により ServiceClientCredentials オブジェクトが作成されます。

1. `Program.cs`を開きます。
1. 次のコードを貼り付けます。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル: ビデオのアップロード、エンコード、ストリーミング - .NET](stream-files-tutorial-with-api.md) 
- [チュートリアル: Media Services v3 によるライブ ストリーミング - .NET](stream-live-tutorial-with-api.md)
- [チュートリアル: Media Services v3 でビデオを分析する - .NET](analyze-videos-tutorial.md)
- [ローカル ファイルからジョブの入力を作成する - .NET](job-input-from-local-file-how-to.md)
- [HTTPS URL からジョブの入力を作成する - .NET](job-input-from-http-how-to.md)
- [カスタム変換を使用してエンコードする - .NET](transform-custom-presets-how-to.md)
- [AES-128 動的暗号化とキー配信サービスを使用する - .NET](drm-playready-license-template-concept.md)
- [DRM 動的暗号化とライセンス配信サービスを使用する - .NET](drm-protect-with-drm-tutorial.md)
- [既存のポリシーから署名キーを取得する - .NET](drm-get-content-key-policy-dotnet-how-to.md)
- [Media Services を使用してフィルターを作成する - .NET](filters-dynamic-manifest-dotnet-how-to.md)
- [Azure Functions v2 と Media Services v3 の高度なビデオ オンデマンドの例](https://aka.ms/ams3functions)

## <a name="see-also"></a>関連項目

* [.NET リファレンス](/dotnet/api/overview/azure/mediaservices/management)
* コードの例について詳しくは、[.NET SDK サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet) リポジトリをご覧ください。
