---
title: "Azure Batch プール削除完了イベント | Microsoft Docs"
description: "Batch のプール削除完了イベントのリファレンスです。"
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
ms.openlocfilehash: 890f2ba7fda37060c56177868d6214d517d91831
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="pool-delete-complete-event"></a>プール削除の完了イベント

 このイベントは、プールの削除操作が完了したときに出力されます。

 次の例は、プール削除の完了イベントの本文を示しています。

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|id|String|プールの ID。|
|startTime|DateTime|プールの削除が開始された時刻。|
|endTime|DateTime|プールの削除が完了した時刻。|

## <a name="remarks"></a>解説
プールのサイズ変更操作の状態とエラー コードの詳細については、[アカウントからのプールの削除](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)を参照してください。