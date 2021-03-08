---
title: Azure Batch プール削除開始イベント
description: Batch のプール削除開始イベントのリファレンスです。 このイベントは、プールの削除操作が開始されたときに出力されます。
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803750"
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
