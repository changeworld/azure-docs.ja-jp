---
title: Azure Stack Hub でチェックポイント ストアとして Blob Storage を使用する (プレビュー)
description: この記事では Azure Stack Hub (プレビュー) 上の Event Hubs でチェックポイント ストアとして Blob Storage を使用する方法について説明します。
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398756"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>チェックポイント ストアとして Blob Storage を使用する - Azure Stack Hub 上の Event Hubs (プレビュー)
Azure で一般公開されているものとは異なるバージョンの Storage Blob SDK をサポートする環境で、チェックポイント ストアとして Azure Blob Storage を使用している場合は、コードを使用して、Storage Service API バージョンをその環境でサポートされている特定のバージョンに変更する必要があります。 たとえば、[Azure Stack Hub バージョン 2002 上で Event Hubs](https://docs.microsoft.com/azure-stack/user/event-hubs-overview) を実行している場合、Storage Service で利用可能な最も高いバージョンは 2017-11-09 です。 この場合は、コードを使用して、対象にする Storage Service API のバージョンを 2017-11-09 にする必要があります。 特定の Storage API バージョンを対象にする方法の例については、GitHub の次のサンプルを参照してください。 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) または [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)、 
- Python - [同期](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py)、[非同期](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Azure Stack Hub 上の Event Hubs は現在[プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階であり、無料で提供されます。 

Azure Stack Hub でサポートされているバージョンを対象にせずに、Blob Storage をチェックポイント ストアとして使用する Event Hubs レシーバーを実行すると、次のエラー メッセージが表示されます。

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python におけるサンプルのエラー メッセージ
Python の場合、`azure.core.exceptions.HttpResponseError` のエラーは `EventHubConsumerClient.receive()` の `on_error(partition_context, error)` エラー ハンドラーに渡されます。 ただし、メソッド `receive()` は例外を発生させません。 `print(error)` は次の例外情報を出力します。

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

ロガーは、次のような 2 つの警告をログに記録します。

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>次のステップ

パーティション分割およびチェックポイント処理については、次の記事を参照してください。[アプリケーションの複数のインスタンス間でパーティション負荷のバランスを取る](event-processor-balance-partition-load.md)
