---
title: "タスク開始イベント - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 95f7762a-a715-4c83-907b-8aed004e69b1
caps.latest.revision: 3
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 81e27c1db2687c819aee15646c2c72e8765293c8
ms.lasthandoff: 04/13/2017

---
# <a name="task-start-event"></a>タスク開始イベント
タスク開始イベントログ本文

## <a name="remarks"></a>解説
 スケジューラがコンピューティング ノードでタスクの開始予定を設定すると、このイベントが生成されます。 タスクが再試行またはキューに再び追加されると、このイベントは同じタスクに再度生成されますが、これにより再試行回数とシステム タスクのバージョンは更新されますのでご注意ください。


 次の例は、タスク開始イベントの本文を示しています。

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|要素名|型|メモ|
|------------------|----------|-----------|
|jobId|文字列|タスクを含むジョブの ID です。|
|id|String|タスクの ID です。|
|taskType|文字列|タスクのタイプです。 ジョブ マネージャー タスクを示す 'JobManager' と、ジョブ マネージャー タスクでないことを示す 'User' のいずれかです。|
|systemTaskVersion|Int32|これは、タスクの内部再試行カウンターです。 Batch サービスは一時的問題に対応するタスクを内部で再試行できます。 これらの問題には、内部的なスケジュール エラーや、コンピューティング ノードを異常な状態から回復しようとする動作が含まれます。|
|[nodeInfo](#nodeInfo)|複合型|タスクが実行されたコンピューティング ノードに関する情報が含まれます。|
|[multiInstanceSettings](#multiInstanceSettings)|複合型|このタスクが複数のコンピューティング ノードを必要とする、複数インスタンス タスクであることを指定します。  詳細は [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) をご覧ください。|
|[制約](#constraints)|複合型|このタスクに適用される実行の制約。|
|[executionInfo](#executionInfo)|複合型|タスクの実行に関する情報が含まれます。|

###  <a name="nodeInfo"></a> nodeInfo

|要素名|型|メモ|
|------------------|----------|-----------|
|poolId|String|タスクが実行されたプールの ID。|
|nodeId|文字列|タスクが実行されたノードの ID。|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|要素名|型|メモ|
|------------------|----------|-----------|
|numberOfInstances|int|タスクに必要なコンピューティング ノードの数。|

###  <a name="constraints"></a>制約

|要素名|型|メモ|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|タスクを再試行できる最大回数。 Batch サービスは、終了コードが 0 以外の場合にタスクを再試行します。<br /><br /> この値によって再試行の回数が限定されますのでご注意ください。 Batch サービスはタスクを 1 回試行してから、上限に達するまで再試行できます。 たとえば、最大再試行回数が 3 の場合、Batch はタスクを最大 4 回試行します (初回試行 1 回と再試行 3 回)。<br /><br /> 最大再試行回数が 0 の場合、Batch サービスはタスクを再試行しません。<br /><br /> 最大再試行回数が -1 の場合、Batch サービスはタスクを無制限に再試行します。<br /><br /> 既定値は 0 (再試行なし) です。|

###  <a name="executionInfo"></a> executionInfo

|要素名|型|メモ|
|------------------|----------|-----------|
|retryCount|Int32|Batch サービスによりタスクが再試行された回数。 タスクは、0 以外のコードで終了すると、指定された MaxTaskRetryCount まで再試行されます。|

