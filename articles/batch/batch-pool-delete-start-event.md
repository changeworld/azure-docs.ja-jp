---
title: "Azure Batch プール削除開始イベント | Microsoft Docs"
description: "Batch のプール削除開始イベントのリファレンスです。"
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8a5241dce422e5c826ab428da6d7bc93284a1cf
ms.lasthandoff: 04/22/2017

---

# <a name="pool-delete-start-event"></a>プール削除の開始イベント

 このイベントは、プールの削除操作が開始されたときに出力されます。 プールの削除は非同期イベントであるため、削除操作が完了したら、プール削除の完了イベントが出力されることを予測できます。

 次の例は、プール削除の開始イベントの本文を示しています。

```
{
    "id": "myPool1"
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|id|String|プールの ID。|
