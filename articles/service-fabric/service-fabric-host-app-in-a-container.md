---
title: コンテナー内の .NET アプリを Azure Service Fabric にデプロイする
description: Visual Studio を使って既存の .NET アプリケーションをコンテナーに格納し、Service Fabric 内のコンテナーをローカルでデバッグする方法を紹介します。 コンテナーに格納されたアプリケーションは Azure のコンテナー レジストリにプッシュされ、Service Fabric クラスターにデプロイされます。 Azure にデプロイされたアプリケーションは、データの保持に Azure SQL DB を使用します。
ms.topic: tutorial
ms.date: 07/08/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ae7069b155266b8fc8049b9660d38acbb1103d6c
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236425"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>チュートリアル:Windows コンテナー内の .NET アプリケーションを Azure Service Fabric にデプロイする

このチュートリアルでは、既存の ASP.NET アプリケーションをコンテナーに格納して Service Fabric アプリケーションとしてパッケージ化する方法を説明します。  ローカルの Service Fabric 開発クラスターでコンテナーを実行した後、アプリケーションを Azure にデプロイします。  アプリケーションは、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) にデータを保持します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Visual Studio を使った既存のアプリケーションのコンテナー格納
> * Azure SQL Database でデータベースを作成する
> * Azure コンテナー レジストリの作成
> * Azure への Service Fabric アプリケーションのデプロイ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
2. Windows の **Hyper-V** および **Containers** 機能を有効にします。
3. Windows 10 でコンテナーを実行できるように、[Docker Desktop for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) をインストールします。
4. [Service Fabric ランタイムのバージョン 6.2 以降](service-fabric-get-started.md)と、[Service Fabric SDK のバージョン 3.1](service-fabric-get-started.md) 以降をインストールします。
5. [Visual Studio](https://www.visualstudio.com/) をインストールして **Azure 開発** および **ASP.NET と Web 開発ワークロード** を有効にします。
6. [Azure PowerShell][link-azure-powershell-install] をインストールする

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Fabrikam Fiber CallCenter をダウンロードして実行する

1. GitHub から [Fabrikam Fiber CallCenter][link-fabrikam-github] というサンプル アプリケーションをダウンロードします。

2. Fabrikam Fiber CallCenter アプリケーションがビルドされ、問題なく実行できていることを確認します。  Visual Studio を **管理者** として起動し、[VS2015\FabrikamFiber.CallCenter.sln][link-fabrikam-github] ファイルを開きます。 F5 キーを押してアプリケーションを実行し、デバッグします。

   ![ローカル ホストで実行されている Fabrikam Fiber CallCenter アプリケーション ホーム ページのスクリーンショット。 このページには、サポート コールの一覧が含まれるダッシュボードが表示されます。][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Azure SQL DB を作成する

運用環境で Fabrikam Fiber CallCenter アプリケーションを実行するときは、データベースにデータを保持する必要があります。 現在コンテナー内のデータの永続化を保証する方法はありません。そのため、コンテナー内に SQL Server の運用データを格納することはできません。

Microsoft では、[Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md) の使用をお勧めしています。 Azure でマネージド SQL Server DB を設定して実行するには、次のスクリプトを実行します。  スクリプトは必要に応じて変更してください。 *clientIP* は、開発用コンピューターの IP アドレスです。 スクリプトから出力されたサーバーの名前はメモしておいてください。

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> 開発用コンピューターが会社のファイアウォールにより保護されている場所に置かれている場合には、そのコンピューターの IP アドレスとインターネットに公開される IP アドレスとが異なることもあります。 ファイアウォール規則に使用する正しい IP アドレスがデータベースにあることを確認するには、[Azure portal](https://portal.azure.com) にアクセスして、[SQL データベース] セクションで目的のデータベースを探してください。 その名前をクリックし、[概要] セクションの [サーバー ファイアウォールの設定] をクリックします。 "クライアント IP アドレス" は、開発マシンの IP アドレスです。 "AllowClient" ルール内の IP アドレスと一致していることを確認してください。

## <a name="update-the-web-config"></a>Web 構成の更新

**FabrikamFiber.Web** プロジェクトに戻り、**web.config** ファイル内の接続文字列を更新し、コンテナー内の SQL Server を指定します。  前のスクリプトで作成したサーバー名の接続文字列の *Server* の部分を更新します。 "fab-fiber-751718376.database.windows.net" のようになっている必要があります。 次の XML では、更新する必要があるのは `connectionString` 属性だけです。`providerName` 属性と `name` 属性を変更する必要はありません。

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>ローカル デバッグには、ホストからアクセスできる範囲であれば好きな SQL Server を使用できます。 ただし、**localdb** は `container -> host` 通信をサポートしていません。 Web アプリケーションのリリース ビルドを作成するときに別の SQL データベースを使用する場合は、*web.release.config* ファイルに別の接続文字列を追加します。

## <a name="containerize-the-application"></a>アプリケーションのコンテナー格納

1. **FabrikamFiber.Web** プロジェクトを右クリックし、 **[追加]**  >  **[コンテナー オーケストレーター サポート]** の順に選択します。  コンテナー オーケストレーターとして **[Service Fabric]** を選択し、 **[OK]** をクリックします。

2. メッセージが表示されたら、 **[はい]** をクリックして Docker を Windows コンテナーに切り替えます。

   ソリューションに、新しい Service Fabric アプリケーション プロジェクト **FabrikamFiber.CallCenterApplication** が作成されます。  既存の **FabrikamFiber.Web** プロジェクトに Docker ファイルが追加されます。  **FabrikamFiber.Web** プロジェクトには、ほかにも **PackageRoot** ディレクトリが追加されます。このディレクトリには、新しい FabrikamFiber.Web サービスのサービス マニフェストと各種の設定が格納されています。

   これで、コンテナーを Service Fabric アプリケーションにビルドしてパッケージ化する準備ができました。 コンピューター上にコンテナー イメージをビルドすると、コンテナー レジストリにプッシュしたり、ホストにプルして実行したりできるようになります。

## <a name="run-the-containerized-application-locally"></a>コンテナーに格納されたアプリケーションをローカルで実行する

**F5** キーを押し、ローカルの Service Fabric 開発クラスターのコンテナーの中にあるアプリケーションを実行およびデバッグします。 'ServiceFabricAllowedUsers' グループに Visual Studio プロジェクト ディレクトリの読み取りおよび実行アクセス許可を付与するように求めるメッセージ ボックスが表示された場合は、 **[はい]** をクリックします。

F5 キーによる実行で次のような例外がスローされた場合は、Azure データベース ファイアウォールに正しい IP が追加されていません。

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Azure データベース ファイアウォールに適切な IP を追加するには、次のコマンドを実行します。

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

アプリケーションをローカルで実行できたので、Azure にデプロイする準備を始めましょう。  コンテナー レジストリにコンテナー イメージを格納する必要があります。  次のスクリプトを使って、[Azure コンテナー レジストリ](../container-registry/container-registry-intro.md)を作成します。 コンテナー レジストリ名は他の Azure サブスクリプションからも表示できるため、一意にする必要があります。
Azure にアプリケーションをデプロイする前に、このレジストリにコンテナー イメージをプッシュします。  Azure 内のクラスターにアプリケーションをデプロイすると、このレジストリからコンテナー イメージがプルされます。

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Azure に Service Fabric クラスターを作成する

Service Fabric アプリケーションは、ネットワークに接続された一連の物理マシンであるクラスター上で実行されます。  Azure にアプリケーションをデプロイする前に、Azure に Service Fabric クラスターを作成する必要があります。

次のようにすることができます。

* Visual Studio でテスト クラスターを作成する。 このオプションでは、お好きな構成を使用して、セキュリティで保護されたクラスターを Visual Studio で直接作成できます。
* [テンプレートからセキュリティで保護されたクラスターを作成する](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

このチュートリアルでは、Visual Studio からクラスターを作成します。テストのシナリオでは、こちらの方法が適しているからです。 別の方法でクラスターを作成する場合や、既存のクラスターを使用する場合には、接続エンドポイントをコピーして貼り付けるか、サブスクリプションからクラスターを選択します。

開始する前に、ソリューション エクスプローラーで [FabrikamFiber.Web]、[PackageRoot]、[ServiceManifest.xml] の順に開きます。 **[エンドポイント]** に記載されている Web フロントエンドのポートをメモします。

クラスターを作成する際は、次の手順に従います。

1. ソリューション エクスプローラーで **FabrikamFiber.CallCenterApplication** アプリケーション プロジェクトを右クリックし、 **[発行]** を選択します。
2. サブスクリプションにアクセスできるように、Azure アカウントを使用してサインインします。
3. **[接続のエンドポイント]** のドロップダウンの下にある **[新しいクラスターの作成]** オプションを選択します。
4. **[クラスターの作成]** ダイアログで、次のように設定を変更します。

    a. **[クラスター名]** フィールドでクラスターの名前を指定します。また、使用するサブスクリプションと場所を指定します。 クラスター リソース グループの名前をメモしておいてください。

    b. 省略可能:ノードの数を変更できます。 既定では、Service Fabric のシナリオをテストするのに最低限必要な 3 ノードになっています。

    c. **[証明書]** タブを選択します。このタブでは、クラスターの証明書をセキュリティで保護するために使用されるパスワードを入力します。 この証明書は、クラスターのセキュリティ保護に役立ちます。 また、証明書を保存したい場所にパスを変更することもできます。 アプリケーションをクラスターに発行するうえで必要な手順であるため、Visual Studio では証明書を自動でインポートすることもできます。

    >[!NOTE]
    >この証明書がインポートされているフォルダー パスをメモしておきます。 クラスター作成後の次の手順として、この証明書をインポートします。

    d. **[VM の詳細]** タブを選択します。クラスターを構成する仮想マシン (VM) に使用したいパスワードを指定します。 ユーザー名とパスワードは、VM へのリモート接続に使用できます。 また、VM マシン サイズを選択できるほか、必要に応じて VM イメージを変更できます。

    > [!IMPORTANT]
    > 実行中のコンテナーをサポートする SKU を選択します。 クラスター ノード上の Windows Server OS は、コンテナーの Windows Server OS と互換性を持っている必要があります。 詳細については、[Windows Server コンテナーの OS とホスト OS の互換性](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)に関するページを参照してください。 このチュートリアルでは、既定では Windows Server 2016 LTSC に基づいて Docker イメージを作成します。 このイメージに基づくコンテナーは、コンテナー搭載 Windows Server 2016 Datacenter で作成されたクラスター上で実行されます。 ただし、Windows Server の別のバージョンに基づいて、クラスターを作成するか、既存のクラスターを使用する場合は、コンテナーの基になる OS イメージを変更する必要があります。 **FabrikamFiber.Web** プロジェクトの **dockerfile** を開き、以前のバージョンの Windows Server に基づいている既存のすべての `FROM` ステートメントをコメントアウトし、[Windows Server Core DockerHub ページ](https://hub.docker.com/_/microsoft-windows-servercore)の目的のバージョンのタグに基づく `FROM` ステートメントを追加します。 Windows Server Core リリース、サポート タイムライン、およびバージョンの追加情報については、「[Windows Server のリリース情報](/windows-server/get-started/windows-server-release-info)」を参照してください。 

    e. **[詳細]** タブで、クラスターをデプロイする際にロード バランサーで開放するアプリケーション ポートを列挙します。 これは、クラスターの作成を始める前にメモしておいたポートです。 さらに、アプリケーション ログ ファイルのルーティングに使用される既存の Application Insights キーを追加することもできます。

    f. 設定の変更が完了したら、 **[作成]** ボタンを選択します。

5. 作成の完了には数分かかります。クラスターが完全に作成されると、出力ウィンドウにその旨が表示されます。

## <a name="install-the-imported-certificate"></a>インポートされた証明書をインストールする

クラスター作成手順の一部としてインポートされた証明書を **現在のユーザー** ストアの場所にインストールし、指定した秘密キーのパスワードを入力します。

インストールを確認するには、コントロール パネルで **[ユーザー証明書の管理]** を開き、 **[証明書 - 現在のユーザー]**  ->  **[個人]**  ->  **[証明書]** で、証明書がインストールされていることを確認します。 証明書は、 *[クラスター名]* . *[クラスターの場所]* .cloudapp.azure.com のようになります (例: *fabrikamfibercallcenter.southcentralus.cloudapp.azure.com*)。 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Azure で実行しているアプリケーションに SQL Database へのアクセスを許可する

ローカルで実行しているアプリケーションにアクセス権を付与するための SQL ファイアウォール規則は、既に作成しました。  次は、Azure で実行しているアプリケーションが SQL DB にアクセスできるようにする必要があります。  Service Fabric クラスターの[仮想ネットワーク サービス エンドポイント](../azure-sql/database/vnet-service-endpoint-rule-overview.md)を作成してから、そのエンドポイントに対して SQL DB へのアクセスを許可する規則を作成します。 クラスターを作成する際にメモしておいたクラスター リソース グループ変数を指定してください。

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションを展開する

これでアプリケーションの準備ができたので、Visual Studio から直接 Azure にあるクラスターにデプロイできるようになりました。  ソリューション エクスプローラーで **FabrikamFiber.CallCenterApplication** アプリケーション プロジェクトを右クリックし、 **[発行]** を選択します。  **[接続エンドポイント]** では、以前に作成したクラスターのエンドポイントを選択します。  **[Azure Container Registry]** では、以前に作成したコンテナー レジストリを選択します。  **[発行]** をクリックして、Azure にあるクラスターにアプリケーションをデプロイします。

![アプリケーションの発行][publish-app]

出力ウィンドウでデプロイの進行状況を確認します。 アプリケーションのデプロイが終わったらブラウザーを開き、クラスターのアドレスとアプリケーション ポートを入力します。 たとえば、「 `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/` 」のように入力します。

![azure.com で実行されている Fabrikam Fiber CallCenter アプリケーション ホーム ページのスクリーンショット。 このページには、サポート コールの一覧が含まれるダッシュボードが表示されます。][fabrikam-web-page-deployed]

ページの読み込みに失敗した場合や、証明書の入力を求められなかった場合は、エクスプローラーのパス (例: `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer`) を開いて、新しくインストールされた証明書を選択してください。

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Service Fabric クラスターを使用した継続的インテグレーションとデプロイ (CI/CD) の設定

Azure DevOps を使用して、Service Fabric クラスターへの CI/CD アプリケーションのデプロイを構成する方法については、「[チュートリアル: CI/CD を使用して Service Fabric クラスターへアプリケーションをデプロイする](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)」を参照してください。 このチュートリアルで説明されているプロセスは、この (FabrikamFiber) プロジェクトと同じですが、投票サンプルのダウンロードはスキップし、リポジトリ名として Voting の代わりに FabrikamFiber を指定してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

一連の作業が終わったら、作成したリソースをすべて削除してください。  最も簡単なのは、Service Fabric クラスター、Azure SQL DB、Azure コンテナー レジストリが含まれるリソース グループを削除するという方法です。

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * Visual Studio を使った既存のアプリケーションのコンテナー格納
> * Azure SQL Database でデータベースを作成する
> * Azure コンテナー レジストリの作成
> * Azure への Service Fabric アプリケーションのデプロイ

このチュートリアルの次の部分では、[CI/CD を使用して Service Fabric クラスターへコンテナー アプリケーションをデプロイする](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md)方法について説明します。

[link-fabrikam-github]: https://github.com/Azure-Samples/service-fabric-dotnet-containerize
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
