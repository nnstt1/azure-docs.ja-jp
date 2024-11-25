---
title: Linux VM の Azure Compute Gallery で Azure Image Builder を使用する
description: Azure Image Builder と Azure Compute Gallery で Linux VM イメージを作成します。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: c6a8017f241fe1636b6f9c922c167bb5e2d4ecde
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471461"
---
# <a name="create-a-linux-image-and-distribute-it-to-an-azure-compute-gallery-by-using-azure-cli"></a>Azure CLI を使用して Linux イメージを作成し Azure Compute Gallery に配布する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

この記事では、Azure Image Builder と Azure CLI を使用して [Azure Compute Gallery](../shared-image-galleries.md) (旧称 Shared Image Gallery) でイメージ バージョンを作成し、そのイメージをグローバルに配布する方法について説明します。 この操作は、[Azure PowerShell](../windows/image-builder-gallery.md) を使用して行うこともできます。


サンプルの .json テンプレートを使用して、イメージを構成します。 使用する .json ファイルは、[helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json) です。 

イメージを Azure Compute Gallery に配布するため、テンプレートでは `distribute` セクションの値として [sharedImage](image-builder-json.md#distribute-sharedimage) が使われています。


## <a name="register-the-features"></a>機能の登録
Azure Image Builder を使用するには、新しい機能を登録する必要があります。

登録を確認します。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages -o json | grep registrationState
az provider show -n Microsoft.KeyVault -o json | grep registrationState
az provider show -n Microsoft.Compute -o json | grep registrationState
az provider show -n Microsoft.Storage -o json | grep registrationState
az provider show -n Microsoft.Network -o json | grep registrationState
```

登録されていない場合、次のコマンドを実行します。

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```

## <a name="set-variables-and-permissions"></a>変数とアクセス許可の設定 

いくつかの情報を繰り返し使用するので、その情報を格納するいくつかの変数を作成します。

Image Builder では、ソース マネージド イメージと同じリソース グループ内にのみ、カスタム イメージを作成できます。 ソース マネージド イメージと同じリソース グループになるように、この例のリソース グループ名を更新します。

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the Azure Compute Gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

サブスクリプション ID の変数を作成します。

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```

リソース グループを作成します。

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>ユーザー割り当て ID を作成し、リソース グループにアクセス許可を設定する
Image Builder は、指定された[ユーザー ID](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) を使用して、Azure Compute Gallery (SIG) にイメージを挿入します。 この例では、イメージの SIG への配布を実行するための粒度の細かいアクションを持つ Azure ロール定義を作成します。 このロール定義はその後、ユーザー ID に割り当てられます。

```bash
# create user assigned identity for image builder to access the storage account where the script is located
identityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $identityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/Azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>イメージ定義およびギャラリーの作成

Azure Compute Gallery で Image Builder を使用するには、既存のギャラリーとイメージ定義が必要です。 Image Builder では、ギャラリーとイメージ定義は自動的には作成されません。

使用するギャラリーとイメージ定義がまだない場合は、最初に作成します。 まず、ギャラリーを作成します。

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

次に、イメージ定義を作成します。

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>.json のダウンロードと構成

.Json テンプレートをダウンロードし、変数を使用して構成します。

```azurecli-interactive
curl https://raw.githubusercontent.com/Azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>イメージ バージョンの作成

次の部分では、ギャラリー内でイメージ バージョンを作成します。 

Azure Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

イメージのビルドを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

イメージを作成し、両方のリージョンにレプリケートするまでにしばらくかかることがあります。 この部分が終了するまで待ってから、VM の作成に進みます。


## <a name="create-the-vm"></a>VM の作成

Azure Image Builder で作成されたイメージ バージョンから VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM に SSH 接続します。

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

SSH 接続が確立されるとすぐに、イメージが *当日のメッセージ* でカスタマイズされたことがわかります。

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ここで同じイメージの新しいバージョンを作成するように、イメージ バージョンを再カスタマイズしてみる場合、次の手順をスキップして、[Azure Image Builder を使用した別のイメージ バージョンの作成](image-builder-gallery-update-image-version.md)に関するページに進みます。


これにより、他のすべてのリソース ファイルとともに、作成されたイメージが削除されます。 リソースを削除する前に、このデプロイを終了していることを確認します。

ギャラリー リソースを削除する場合、それらの作成に使用したイメージ定義を削除する前に、すべてのイメージ バージョンを削除する必要があります。 ギャラリーを削除するには、最初にギャラリー内のすべてのイメージ定義を削除していることが必要です。

Image Builder テンプレートを削除します。

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

アクセス許可の割り当て、ロール、ID の削除
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Image Builder で作成されたイメージ バージョン (これは常に `0.` で始まります) を取得し、続いてイメージ バージョンを削除します。

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


イメージ定義を削除します。

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

ギャラリーを削除します。

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

リソース グループを削除します。

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>次のステップ

[Azure Compute Gallery の詳細](../shared-image-galleries.md)を確認してください。
