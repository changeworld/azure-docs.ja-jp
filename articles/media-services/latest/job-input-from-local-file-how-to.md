---
title: ローカル ファイルから Azure Media Services ジョブの入力を作成する | Microsoft Docs
description: このトピックでは、ローカル ファイルからジョブの入力を作成する方法を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6ca2da7fc43e509bec357ae47ada3a2f57e79e78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-job-input-from-a-local-file"></a>ローカル ファイルからジョブの入力を作成する

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 入力ビデオは Media Service 資産として格納できます。この場合は、(ローカルまたは Azure Blob ストレージに格納されている) ファイルに基づいて入力資産を作成します。 このトピックでは、ローカル ファイルからジョブの入力を作成する方法を示します。 完全な例については、こちらの [github サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)を参照してください。

## <a name="net-sample"></a>.Net のサンプル

次のコードは、入力資産を作成してジョブの入力として使用する方法を示しています。 CreateInputAsset 関数は、次のアクションを行います。

* 資産を作成する 
* 書き込み可能な [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) を資産の[ストレージ内のコンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)に取得します。
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする

```csharp
private static Asset CreateInputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string fileToUpload)
{
    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

    // Use Media Services API to get back a response that contains
    // SAS URL for the Asset container into which to upload blobs.
    // That is where you would specify read-write permissions 
    // and the exparation time for the SAS URL.
    var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, new ListContainerSasInput()
    {
        Permissions = AssetContainerPermission.ReadWrite,
        ExpiryTime = DateTimeOffset.Now.AddHours(4)
    });

    var sasUri = new Uri(response.AssetContainerSasUrls.First());
    
    // Use Storage API to get a reference to the Asset container
    // that was created by calling Asset's CreateOrUpdate method.  
    CloudBlobContainer container = new CloudBlobContainer(sasUri);
    var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
    
    // Use Strorage API to upload the file into the container in storage.
    blob.UploadFromFile(fileToUpload);

    return asset;
}

private static Job SubmitJob(IAzureMediaServicesClient client, string transformName, string jobName, string outputAssetName)
{
    string inputAssetName = Guid.NewGuid().ToString() + "-input";
    JobInput jobInput = new JobInputAsset(assetName: inputAssetName);

    CreateInputAsset(client, inputAssetName, "ignite.mp4")

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    Job job = client.Jobs.CreateOrUpdate(
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

## <a name="next-steps"></a>次の手順

[HTTP(S) URL からジョブの入力を作成する](job-input-from-http-how-to.md)
