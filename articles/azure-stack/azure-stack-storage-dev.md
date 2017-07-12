---
title: Get started with Azure Stack Storage development tools
description: Guidance to get started with Azure Stack Storage using development tools
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 06/22/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: fa75305733a0d2cf93620280a2ade0dfd2c69a17
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017

---

<a id="get-started-with-azure-stack-storage-development-tools" class="xliff"></a>

# Get started with Azure Stack Storage development tools 

Microsoft Azure Stack provides a set of the storage services including Blob, Table and, Queue.

This article provides quick guidance on how to get started with Azure Stack Storage using development tools. More detailed information and sample code can be found in the corresponding Azure Storage tutorials.

There are known differences between Azure Storage and Azure Stack Storage, including some specific requirements for each platform. For example, there are specific client libraries and specific endpoint suffix requirements for Azure Stack.

For more information, see [Azure Stack Storage: differences and considerations](azure-stack-acs-differences.md)

<a id="azure-client-libraries" class="xliff"></a>

## Azure client libraries
The Azure Stack Storage supported REST API version is 2015-04-05. It doesn’t have full parity with the latest version of Azure Storage REST API. So for the storage client libraries, you need to be aware of the version which is compatible with REST API 2015-04-05.


|Client Library|Azure Stack supported version|Link|Endpoint specification|
|---------|---------|---------|---------|
|.NET     |6.2.0|[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config file|
|Node.js     |1.1.0|[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Service instance declaration|
|Python     |0.30.0|[https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Service instance declaration|

<a id="endpoint-declaration" class="xliff"></a>

## Endpoint declaration
An Azure Stack endpoint includes two parts: the name of a region and the Azure Stack domain.
In the Azure Stack POC, the default endpoint is **local.azurestack.external**.
Contact your service administrator if you’re not sure about your endpoint.

<a id="examples" class="xliff"></a>

## Examples


<a id="net" class="xliff"></a>

#### .NET

For Azure Stack, the endpoint suffix is specified in the `app.config` file:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

<a id="nodejs" class="xliff"></a>

#### Node.js

For Azure Stack, the endpoint suffix is specified in the declaration instance:

```
var blobSvc = azure.createBlobService(‘'myaccount', ‘mykey’,
‘myaccount.blob.local.azurestack.external’);
```

<a id="python" class="xliff"></a>

#### Python

For Azure Stack, the endpoint suffix is specified in the declaration instance:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

<a id="get-started-with-blob-storage" class="xliff"></a>

## Get started with Blob storage

Refer to the following Azure Blob storage tutorials that are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Blob storage using .NET](../storage/storage-dotnet-how-to-use-blobs.md)
* [How to use Blob storage from Node.js](../storage/storage-nodejs-how-to-use-blob-storage.md)
* [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md)

<a id="get-started-with-queue-storage" class="xliff"></a>

## Get started with Queue storage

Refer to the following Azure Queue storage tutorials that are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Queue storage using .NET](../storage/storage-dotnet-how-to-use-queues.md)
* [How to use Queue storage from Node.js](../storage/storage-nodejs-how-to-use-queues.md)
* [How to use Queue storage from Python](../storage/storage-python-how-to-use-queue-storage.md)


<a id="get-started-with-table-storage" class="xliff"></a>

## Get started with Table storage

Refer to the following Azure Table storage tutorials that are applicable to Azure Stack. Note the specific endpoint suffix requirement for Azure Stack described in the previous [Examples](#examples) section.

* [Get started with Azure Table storage using .NET](../storage/storage-dotnet-how-to-use-tables.md)
* [How to use Azure Table storage from Node.js](../storage/storage-nodejs-how-to-use-table-storage.md)
* [How to use Table storage in Python](../storage/storage-python-how-to-use-table-storage.md)

<a id="next-steps" class="xliff"></a>

## Next steps

* [Introduction to Microsoft Azure Storage](../storage/storage-introduction.md)
