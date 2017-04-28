---
title: "タスク完了イベント - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 9dcc468b-e0a7-4b80-bec8-ffd466afdc8a
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: c42ccc5e443274d8e342b7f7f62293459185c354
ms.lasthandoff: 04/13/2017

---
# <a name="task-complete-event"></a>タスク完了イベント
タスク完了イベントのログ本文

## <a name="remarks"></a>解説
 タスクが完了すると、終了コードに関係なく、このイベントが発生します。 このイベントを使用して、タスクの期間、タスクが実行された場所、タスクが再試行されたかどうかを調べることができます。


 次の例は、タスク完了イベントの本文を示しています。

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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|要素名|型|メモ|
|------------------|----------|-----------|
|jobId|String|タスクを含むジョブの ID です。|
|id|String|タスクの ID です。|
|taskType|String|タスクのタイプです。 ジョブ マネージャー タスクを示す 'JobManager' と、ジョブ マネージャー タスクでないことを示す 'User' のいずれかです。 このイベントは、ジョブ準備タスク、ジョブ リリース タスクまたは開始タスクでは発生しません。|
|systemTaskVersion|Int32|これは、タスクの内部再試行カウンターです。 内部で、Batch サービスは一時的問題に対応するタスクを再試行できます。 これらの問題には、内部的なスケジュール エラーや、コンピューティング ノードを異常な状態から回復しようとする動作が含まれます。|
|[nodeInfo](#nodeInfo)|複合型|タスクが実行されたコンピューティング ノードに関する情報が含まれます。|
|[multiInstanceSettings](#multiInstanceSettings)|複合型|タスクが複数のコンピューティング ノードを必要とするマルチインスタンス タスクであることを指定します。  詳細は [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) をご覧ください。|
|[constraints](#constraints)|複合型|このタスクに適用される実行の制約。|
|[executionInfo](#executionInfo)|複合型|タスクの実行に関する情報が含まれます。|

###  <a name="nodeInfo"></a> nodeInfo

|要素名|型|メモ|
|------------------|----------|-----------|
|poolId|String|タスクが実行されたプールの ID。|
|nodeId|文字列|タスクが実行されたノードの ID。|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|要素名|型|メモ|
|------------------|----------|-----------|
|numberOfInstances|Int32|タスクに必要なコンピューティング ノードの数。|

###  <a name="constraints"></a> constraints

|要素名|型|メモ|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|タスクを再試行できる最大回数。 Batch サービスは、タスクの終了コードが 0 以外の場合、タスクを再試行します。<br /><br /> この値によって再試行の回数が限定されるので注意してください。 Batch サービスはタスクを 1 度再試行してから、この上限に達するまで再試行できます。 たとえば、最大再試行回数が 3 の場合、Batch がタスクを最大で 4 試行します (1 回の初回試行と 3 回の再試行)。<br /><br /> 最大再試行回数が 0 の場合、Batch サービスはタスクを再試行しません。<br /><br /> 最大再試行回数が -1 の場合、Batch サービスはタスクを無制限に再試行します。<br /><br /> 既定値は 0 (再試行なし) です。|

###  <a name="executionInfo"></a> executionInfo

|要素名|型|メモ|
|------------------|----------|-----------|
|startTime|DateTime|タスクの実行が開始した時刻です。 「実行中」は**実行している**状態に対応するため、タスクがリソース ファイルやアプリケーション パッケージを指定する場合、開始時刻はタスクがこれらのファイルやパッケージのダウンロードやデプロイを開始した時刻を反映します。  タスクが再起動または再実行された場合は、タスクが実行を開始したつい最近の時刻となります。|
|endTime|DateTime|タスクが完了した時刻です。|
|exitCode|Int32|タスクの終了コード。|
|retryCount|Int32|Batch サービスによりタスクが再試行された回数。 タスクは、0 以外の終了コードで終了すると、指定された MaxTaskRetryCount まで再試行されます。|
|requeueCount|Int32|Batch サービスによりタスクがユーザー要求の結果として再度キューに入れられる回数です。<br /><br /> ユーザーが (プールをサイズ変更したり圧縮することで) プールからノードを削除するときやジョブを無効にしているとき、ユーザーはノードで実行中のタスクを再実行のキューに再度入れるよう指定できます。 この回数は、タスクがこうした理由により何回キューに入れられたかを追跡します。|

