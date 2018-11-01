---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134139"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 | 
|prefetchCount|該当なし|基になる MessageReceiver に使用される既定の PrefetchCount。| 
|autoRenewTimeout|00:05:00|メッセージ ロックが自動的に更新される最大間隔。| 
