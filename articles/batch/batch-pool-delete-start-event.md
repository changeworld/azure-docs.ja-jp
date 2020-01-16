---
title: Azure Batch プール削除開始イベント | Microsoft Docs
description: Batch のプール削除開始イベントのリファレンスです。
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: d582e2b04d203484632a1781d1819f612de41fe7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026735"
---
# <a name="pool-delete-start-event"></a>プール削除の開始イベント

 このイベントは、プールの削除操作が開始されたときに出力されます。 プールの削除は非同期イベントであるため、削除操作が完了したら、プール削除の完了イベントが出力されることを予測できます。

 次の例は、プール削除の開始イベントの本文を示しています。

```
{
    "id": "myPool1"
}
```

|要素|種類|メモ|
|-------------|----------|-----------|
|`id`|String|プールの ID。|
