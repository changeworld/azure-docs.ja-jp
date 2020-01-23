---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279522"
---
[Application Insights のメトリックを計算する](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator)ときに集計される関数呼び出しの数を指定します。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|プロパティ |Default  | [説明] |
|---------|---------|---------| 
|batchSize|1000|集計する要求の最大数。| 
|flushTimeout|00:00:30|集計する最長期間。| 

2 つの制限のいずれかに最初に達した場合、関数呼び出しが集計されます。
