---
title: "プールのサイズ変更の開始イベント - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 8bd33e8b-6390-4a34-95dc-2e63d8bce941
caps.latest.revision: 6
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 5064d86063251aeed22688d8fac133c4be1a88f8
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-start-event"></a>プールのサイズ変更の開始イベント
プールのサイズ変更の開始イベント ログ本文

## <a name="remarks"></a>解説
 このイベントは、プールのサイズ変更が開始されたときに発生します。 プール サイズ変更は非同期イベントであるため、サイズ変更操作が完了するとプール サイズ変更の完了イベントが発生することが想定されます。

 次の例では、プールのサイズを 0 ノードから 2 ノードへ手動でサイズ変更する場合のプール サイズ変更開始イベントの本文を示します。

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|poolId|String|プールの ID。|
|nodeDeallocationOption|文字列|プールからノードが削除されるとき、プールのサイズが減少するかを指定します。<br /><br /> 次のいずれかの値になります。<br /><br /> **requeue** – 実行中のタスクを終了して、再度キューに入れます。 このタスクは、ジョブが有効になると再び実行されます。 タスクが終了するとすぐにノードを削除します。<br /><br /> **terminate** – 実行中のタスクを終了します。 タスクは再び実行されることがありません。 タスクが終了するとすぐにノードを削除します。<br /><br /> **taskcompletion** – 現在実行中のタスクを完了できるようにします。 待機中に新しいタスクをスケジュールしません。 すべてのタスクが完了するとノードを削除します。<br /><br /> **Retaineddata** - 現在実行中のタスクを完了できるようにしてから、すべてのタスク データ保有期間が終了するまで待機します。 待機中に新しいタスクをスケジュールしません。 すべてのタスク保有期間が終了したとき、ノードを削除します。<br /><br /> 既定値は再キューです。<br /><br /> プールのサイズが増加している場合、値は**無効**に設定されます。|
|currentDedicated|Int32|プールに現在割り当てられているコンピューティング ノードの数。|
|targetDedicated|Int32|プールに要求されているコンピューティング ノードの数。|
|enableAutoScale|ブール値|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|isAutoPool|ブール値|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|

