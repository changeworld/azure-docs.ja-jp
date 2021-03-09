---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167656"
---
[Application Insights のメトリックを計算する](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator)ときに集計される関数呼び出しの数を指定します。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|プロパティ |Default  | 説明 |
|---------|---------|---------| 
|batchSize|1000|集計する要求の最大数。| 
|flushTimeout|00:00:30|集計する最長期間。| 

2 つの制限のいずれかに最初に達した場合、関数呼び出しが集計されます。
