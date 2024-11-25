---
title: Windows 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する | Microsoft Docs
description: Windows 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する方法
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e73d987ad8029d1ee65af0f92173561230af599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589247"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Windows 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する
ポイント対サイト (P2S) VPN 接続を使用すると、ポート 445 を開くことなく、Azure の外部から SMB 経由で Azure ファイル共有をマウントできます。 ポイント対サイト VPN 接続は、Azure と個々のクライアントの間の VPN 接続です。 Azure Files で P2S VPN 接続を使用するには、接続したいクライアントごとに P2S VPN 接続を構成する必要があります。 オンプレミス ネットワークから Azure ファイル共有に接続する必要のある多数のクライアントが存在する場合は、クライアントごとのポイント対サイト接続の代わりにサイト間 (S2S) VPN 接続を使用できます。 詳細については、「[Azure Files で使用するサイト間 VPN を構成する](storage-files-configure-s2s-vpn.md)」を参照してください。

このハウツー記事を読み進める前に、Azure Files で使用可能なネットワーク オプションの完全な説明について[直接 Azure ファイル共有アクセスのネットワークに関する考慮事項](storage-files-networking-overview.md)に関するページを参照することを強くお勧めします。

この記事では、Azure ファイル共有をオンプレミスに直接マウントするために、Windows (Windows クライアントおよび Windows Server) 上でポイント対サイト VPN を構成する手順について詳細に説明します。 Azure File Sync のトラフィックを VPN 経由でルーティングすることを検討している場合は、[Azure File Sync のプロキシとファイアウォールの設定の構成](../file-sync/file-sync-firewall-and-proxy.md)に関するページを参照してください。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>前提条件
- 最新バージョンの Azure PowerShell モジュール。 Azure PowerShell をインストールする方法の詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照し、オペレーティング システムを選択してください。 Windows 上で Azure CLI を使用することは可能ですが、下の手順は Azure PowerShell 用に提供されています。

- オンプレミスにマウントする Azure ファイル共有。 ストレージ アカウント内にデプロイされた Azure ファイル共有は、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。 Azure ファイル共有とストレージ アカウントをデプロイする方法の詳細については、「[Azure ファイル共有を作成する](storage-how-to-create-file-share.md)」を参照してください。

- オンプレミスにマウントする Azure ファイル共有を含むストレージ アカウント用のプライベート エンドポイントを持つ仮想ネットワーク。 プライベート エンドポイントを作成する方法の詳細については、「[Azure Files ネットワーク エンドポイントの構成](storage-files-networking-endpoints.md?tabs=azure-powershell)」を参照してください。 

## <a name="collect-environment-information"></a>環境情報の収集
ポイント対サイト VPN を設定するには、まず、ガイドを通して使用する環境に関する情報を収集する必要があります。 ストレージ アカウント、仮想ネットワーク、またはプライベート エンドポイントをまだ作成していない場合は、「[前提条件](#prerequisites)」セクションを参照してください。

忘れずに、`<resource-group>`、`<vnet-name>`、`<subnet-name>` および `<storage-account-name>` を実際の環境の適切な値に置き換えてください。

```PowerShell
$resourceGroupName = "<resource-group-name>" 
$virtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"
$storageAccountName = "<storage-account-name>"

$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$subnetId = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq "StorageAccountSubnet" } | `
    Select-Object -ExpandProperty Id

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$privateEndpoint = Get-AzPrivateEndpoint | `
    Where-Object {
        $subnets = $_ | `
            Select-Object -ExpandProperty Subnet | `
            Where-Object { $_.Id -eq $subnetId }

        $connections = $_ | `
            Select-Object -ExpandProperty PrivateLinkServiceConnections | `
            Where-Object { $_.PrivateLinkServiceId -eq $storageAccount.Id }
        
        $null -ne $subnets -and $null -ne $connections
    } | `
    Select-Object -First 1
```

## <a name="create-root-certificate-for-vpn-authentication"></a>VPN 認証用のルート証明書の作成
オンプレミスの Windows マシンからの VPN 接続を認証して仮想ネットワークにアクセスできるようにするには、2 つの証明書を作成する必要があります。1 つは仮想マシン ゲートウェイに提供されるルート証明書、もう 1 つはルート証明書で署名されているクライアント証明書です。 次の PowerShell は、ルート証明書を作成します。クライアント証明書は、ゲートウェイからの情報を使用して Azure 仮想ネットワーク ゲートウェイを作成した後に作成されます。 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイをデプロイする
Azure 仮想ネットワーク ゲートウェイは、オンプレミスの Windows マシンの接続先となるサービスです。 このサービスをデプロイするには、2 つの基本的なコンポーネントが必要です。クライアントが世界中のどこにあってもそのクライアントへのゲートウェイを識別するパブリック IP と、クライアントの認証に使用される先ほど作成したルート証明書です。

`<desired-vpn-name-here>` は、これらのリソースに必要な名前に置き換えてください。

> [!Note]  
> Azure 仮想ネットワーク ゲートウェイのデプロイには、最大で 45 分かかる場合があります。 このリソースがデプロイされている間、この PowerShell スクリプトはデプロイの完了をブロックします。 これは予期されることです。

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>クライアント証明書の作成
クライアント証明書は、仮想ネットワーク ゲートウェイの URI を使用して作成されます。 この証明書は、先ほど作成したルート証明書で署名されています。

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>VPN クライアントを構成する
Azure 仮想ネットワーク ゲートウェイは、オンプレミスの Windows マシンで VPN 接続を初期化するために必要な構成ファイルを含むダウンロード可能なパッケージを作成します。 Windows 10/Windows Server 2016 以降の [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) 機能を使用して VPN 接続を構成します。 このパッケージには、レガシ Windows VPN クライアントを構成する実行可能パッケージも含まれます (必要な場合)。 このガイドでは、レガシ Windows VPN クライアントではなく、Always On VPN を使用します。Always On VPN クライアントは、自分のマシンに対する管理者権限を持っていないエンドユーザーでも、Azure VPN に接続したり切断したりできるからです。 

次のスクリプトでは、仮想ネットワーク ゲートウェイの認証に必要なクライアント証明書をインストールし、VPN パッケージをダウンロードしてインストールします。 `<computer1>`、`<computer2>` は、忘れずに希望するコンピューターに置き換えてください。 このスクリプトの `$sessions` 配列に PowerShell セッションを追加すると、必要な数のマシンで実行できます。 使用アカウントは、これらの各マシンの管理者である必要があります。 マシンのいずれかがスクリプトを実行しているローカル マシンである場合、昇格された PowerShell セッションからスクリプトを実行する必要があります。 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Azure ファイル共有をマウントする
ポイント対サイト VPN のセットアップが完了したので、これを使用して、PowerShell でセットアップしたコンピューターに Azure ファイル共有をマウントします。 次の例では、共有をマウントし、共有が実際にマウントされていることを証明するために共有のルート ディレクトリを一覧表示し、共有のマウントを解除します。 残念ながら、PowerShell リモート処理を介して共有を永続的にマウントすることはできません。 永続的にマウントする方法については、[Windows で Azure ファイル共有を使用する](storage-how-to-use-files-windows.md)方法に関する記事を参照してください。 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="rotate-vpn-root-certificate"></a>VPN ルート証明書のローテーション
有効期限または新しい要件のためにルート証明書をローテーションする必要がある場合は、仮想ネットワークゲートウェイを再デプロイしなくても、既存の仮想ネットワークゲートウェイに新しいルート証明書を追加できます。  次のサンプルスクリプトを使用してルート証明書を追加したら、[VPN クライアント証明書](#create-client-certificate)を再作成する必要があります。  

スクリプトを実行する前に、`<resource-group-name>`、`<desired-vpn-name-here>`、および `<new-root-cert-name>` を実際の値に置き換えます。

```PowerShell
#Creating the new Root Certificate
$ResourceGroupName = "<resource-group-name>"
$vpnName = "<desired-vpn-name-here>"
$NewRootCertName = "<new-root-cert-name>"

$rootcertname = "CN=$NewRootCertName"
$certLocation = "Cert:\CurrentUser\My"
$date = get-date -Format "MM_yyyy"
$vpnTemp = "C:\vpn-temp_$date\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}

#Fetching gateway details and adding the newly created Root Certificate.
$gateway = Get-AzVirtualNetworkGateway -Name $vpnName -ResourceGroupName $ResourceGroupName

Add-AzVpnClientRootCertificate `
    -PublicCertData $rootCertificate `
    -ResourceGroupName $ResourceGroupName `
    -VirtualNetworkGatewayName $gateway `
    -VpnClientRootCertificateName $NewRootCertName

```
## <a name="see-also"></a>関連項目
- [直接 Azure ファイル共有アクセスに関するネットワークの考慮事項](storage-files-networking-overview.md)
- [Linux 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する](storage-files-configure-p2s-vpn-linux.md)
- [Azure Files で使用するサイト間 (S2S) VPN を構成する](storage-files-configure-s2s-vpn.md)
