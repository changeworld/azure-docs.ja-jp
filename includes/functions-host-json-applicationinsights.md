---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 7d8773cc12b50382f6f300987ec6ce504cd238af
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181743"
---
[Application Insights のサンプリング機能](../articles/azure-functions/functions-monitoring.md#configure-sampling)を制御します。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|isEnabled|true|サンプリングを有効または無効にします。| 
|maxTelemetryItemsPerSecond|5|サンプリングが開始されるしきい値。| 