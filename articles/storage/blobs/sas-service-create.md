---
title: コンテナーまたは BLOB のサービス SAS を作成する
titleSuffix: Azure Storage
description: Azure Blob Storage クライアント ライブラリを使用して、コンテナーまたは BLOB のサービスの Shared Access Signature (SAS) を作成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e2674a3e9227f5cf5cf2a6ebff0a317f4377f3a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606171"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>コンテナーまたは BLOB のサービス SAS を作成する

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、ストレージ アカウント キーを使用して、BLOB ストレージ用の Azure Storage クライアント ライブラリでコンテナーまたは BLOB のサービス SAS を作成する方法について説明します。

## <a name="create-a-service-sas-for-a-blob-container"></a>BLOB コンテナーのサービス SAS を作成する

次のコード例では、コンテナーに SAS を作成します。 既存の保存されているアクセス ポリシーの名前が指定されている場合、そのポリシーは、SAS に関連付けられます。 保存されているアクセス ポリシーがない場合、コードは、アドホック SAS をコンテナーに作成します。

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

サービス SAS は、アカウント アクセス キーを使用して署名されます。 SAS の署名に使用される資格情報を作成するには、[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) クラスを使用します。 次に、新しい [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) オブジェクトを作成し、[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) を呼び出して SAS トークン文字列を取得します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

コンテナーのサービス SAS を作成するには、[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) メソッドを呼び出します。

```csharp
private static string GetContainerSasUri(CloudBlobContainer container,
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

サービス SAS は、アカウント アクセス キーを使用して署名されます。 SAS の署名に使用される資格情報を作成するには、[StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) クラスを使用します。 次に、[generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 関数を呼び出して、SAS トークン文字列を取得するために必要なパラメーターを指定します。

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_ContainerSAS":::

---

## <a name="create-a-service-sas-for-a-blob"></a>BLOB のサービス SAS を作成する

次のコード例では、BLOB に SAS を作成します。 既存の保存されているアクセス ポリシーの名前が指定されている場合、そのポリシーは、SAS に関連付けられます。 保存されているアクセス ポリシーがない場合、コードは、アドホック SAS を BLOB に作成します。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

サービス SAS は、アカウント アクセス キーを使用して署名されます。 SAS の署名に使用される資格情報を作成するには、[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) クラスを使用します。 次に、新しい [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) オブジェクトを作成し、[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) を呼び出して SAS トークン文字列を取得します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

BLOB のサービス SAS を作成するには、[CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) メソッドを呼び出します。

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

BLOB のサービス SAS を作成するには、[CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) メソッドを呼び出します。

BLOB のサービス SAS を作成するには、必要なパラメーターを指定して [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 関数を呼び出します。

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_BlobSAS":::

---

## <a name="create-a-service-sas-for-a-directory"></a>ディレクトリのサービス SAS を作成する

階層型名前空間が有効になっているストレージ アカウントでは、ディレクトリのサービス SAS を作成できます。 サービス SAS を作成するには、[Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) パッケージのバージョン 12.5.0 以降がインストールされていることを確認します。

次の例では、.NET 用の v12 クライアント ライブラリを使用して、ディレクトリのサービス SAS を作成する方法を示します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>次の手順

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)
- [サービス SAS を作成する](/rest/api/storageservices/create-service-sas)
