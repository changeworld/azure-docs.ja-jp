---
title: Azure Batch プール削除開始イベント
description: Batch のプール削除開始イベントのリファレンスです。 このイベントは、プールの削除操作が開始されたときに出力されます。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723937"
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
