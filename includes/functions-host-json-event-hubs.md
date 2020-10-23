---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8f3a58d3a7470867ab23249bbd645289e010ad89
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92223234"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|maxBatchSize|10|受信ループあたりで受信される最大イベント数。|
|prefetchCount|該当なし|基となる `EventProcessorHost` によって使用される既定のプリフェッチ カウント。 許容される最小値は 10 です。|
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。|

> [!NOTE]
> Azure Functions 2.x 以降の host.json のリファレンスについては、[Azure Functions の host.json のリファレンス](../articles/azure-functions/functions-host-json.md)に関する記事を参照してください。

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|maxBatchSize|64|受信ループあたりで受信される最大イベント数。|
|prefetchCount|該当なし|基となる `EventProcessorHost` によって使用される既定のプリフェッチ。| 
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。| 

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[Azure Functions 1.x の host.json のリファレンス](../articles/azure-functions/functions-host-json-v1.md)」を参照してください。
