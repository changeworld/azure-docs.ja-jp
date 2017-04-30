---
title: "プールのサイズ変更の完了イベント - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: dfee89e3-510f-41a0-ace7-737527f40d20
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 623b6cae00ee0f3b0c5073b1a8bee68d2bf17b72
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-complete-event"></a>プールのサイズ変更の完了イベント
プールのサイズ変更の完了イベントのログ本文

## <a name="remarks"></a>解説
 このイベントは、プールのサイズ変更が完了または失敗したときに出力されます。

 次の例では、あるプールについてサイズを増加し、正常に完了した、プールのサイズ変更完了イベントの本文を示しています。

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|id|String|プールの ID。|
|nodeDeallocationOption|String|プールからノードが削除されるとき、プールのサイズが減少するかを指定します。<br /><br /> 次のいずれかの値になります。<br /><br /> **requeue** – 実行中のタスクを終了して、再度キューに入れます。 このタスクは、ジョブが有効になると再び実行されます。 タスクが終了するとすぐにノードを削除します。<br /><br /> **terminate** – 実行中のタスクを終了します。 タスクは再び実行されることがありません。 タスクが終了するとすぐにノードを削除します。<br /><br /> **taskcompletion** – 現在実行中のタスクが完了することを許可します。 待機中に新しいタスクをスケジュールしません。 すべてのタスクが完了したときにノードを削除します。<br /><br /> **Retaineddata** - 現在実行中のタスクが完了することを許可し、すべてのタスク データ保有期間が終了するまで待機します。 待機中に新しいタスクをスケジュールしません。 すべてのタスク保有期間が終了したとき、ノードを削除します。<br /><br /> 既定値は requeue です。<br /><br /> プールのサイズが増加している場合、値は**無効**に設定されます。|
|currentDedicated|Int32|プールに現在割り当てられているコンピューティング ノードの数。|
|targetDedicated|Int32|プールに要求されているコンピューティング ノードの数。|
|enableAutoScale|ブール値|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|isAutoPool|ブール値|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|
|startTime|DateTime|プールのサイズ変更が開始された時間。|
|endTime|DateTime|プールのサイズ変更が完了した時間。|
|resultCode|String|サイズ変更の結果。|
|resultMessage|String|サイズ変更のエラーには、結果の詳細が含まれています。<br /><br /> サイズ変更が正常に完了した場合、操作が成功したことが示されています。|
