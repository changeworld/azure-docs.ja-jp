---
title: ジョブとタスクのエラーを確認する - Azure Batch | Microsoft Docs
description: 確認するエラーとジョブやタスクのトラブルシューティング
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 12/01/2019
ms.author: markscu
ms.openlocfilehash: c4e36d76bf85b9715a817dbeb7c690aa77f8d978
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851933"
---
# <a name="job-and-task-error-checking"></a>ジョブとタスクのエラーの確認

ジョブやタスクを追加する際に発生する可能性のあるエラーはさまざまです。 API、CLI、UI では発生したエラーがすぐに返されるので、これらの操作のエラーを検出するのは簡単です。  ただし、ジョブやタスクがスケジュールされ、実行される場合は後から発生する可能性のあるエラーがあります。

この記事では、ジョブとタスクが送信された後に発生する可能性のあるエラーについて取り上げます。 チェックして処理すべきエラーとその説明が記載されています。

## <a name="jobs"></a>[ジョブ]

ジョブは 1 つ以上のタスクをグループ化したもので、実行されるコマンド ラインは、実際にはタスクによって指定されています。

ジョブを追加する際は、次のパラメーターを指定できます。これらは、ジョブで発生するエラーに影響する場合があります。

- [ジョブの制約](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - ジョブの最大アクティブ時間または最大実行時間を設定するための `maxWallClockTime` プロパティを必要に応じて指定できます。 超過した場合は、ジョブの [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) で設定された `terminateReason` プロパティによって、ジョブが強制的に終了されます。
- [ジョブの準備タスク](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - 指定した場合、ジョブのタスクがノード上で初めて実行されたときにジョブ準備タスクが実行されます。 ジョブ準備タスクが失敗した場合、タスクが実行されず、ジョブは完了しません。
- [ジョブのリリース タスク](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - ジョブのリリース タスクを指定できるのは、ジョブの準備タスクが構成されている場合だけです。 ジョブが強制終了されるとき、ジョブの準備タスクが実行された各プール ノードでジョブのリリース タスクが実行されます。 ジョブのリリース タスクが失敗する可能性もありますが、その場合でもジョブは `completed` 状態に移行します。

### <a name="job-properties"></a>ジョブ プロパティ

エラーについては、次のジョブ プロパティをチェックする必要があります。

- "[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)":
  - ジョブの制約で指定された `maxWallClockTime` を超えたためジョブが強制終了されたことを示す値が、`terminateReason` プロパティに設定されることがあります。 また、ジョブの `onTaskFailure` プロパティが適切に設定されていない場合にタスクが失敗したことを示すように設定されることもあります。
  - スケジュール エラーが発生した場合は、[schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) プロパティが設定されます。
 
### <a name="job-preparation-tasks"></a>ジョブの準備タスク

ジョブの準備タスクがジョブに指定されている場合、そのタスクのインスタンスは、ジョブのタスクが初めてノード上で実行されるときに実行されます。 ジョブに対して構成されるジョブの準備タスクは、実行されるジョブの準備タスク インスタンスを (プール内のノード数を上限として) 複数含んだタスク テンプレートと考えることができます。

ジョブの準備タスクのインスタンスを確認して、エラーの有無を判断する必要があります。
- ジョブの準備タスクを実行すると、そのジョブの準備タスクをトリガーしたタスクの[状態](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)は `preparing` に移行します。ジョブの準備タスクが失敗した場合、トリガー元のタスクは `active` 状態に戻り、実行されません。  
- 実行されたジョブの準備タスクのすべてのインスタンスは、[List Preparation and Release Task Status](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API を使用してジョブから取得できます。 あらゆるタスクと同様、`failureInfo`、`exitCode`、`result` などのプロパティを備えた[実行情報](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)が提供されます。
- ジョブの準備タスクが失敗した場合、トリガー元のジョブ タスクは実行されません。また、ジョブは完了せず、停止しています。 スケジュール可能なタスクを含むジョブがほかになければ、プールは活用されない状態になる可能性があります。

### <a name="job-release-tasks"></a>ジョブのリリース タスク

ジョブのリリース タスクがジョブに指定されている場合、ジョブが強制終了されると、ジョブの準備タスクが実行された各プール ノードでジョブのリリース タスクのインスタンスが実行されます。  ジョブのリリース タスクのインスタンスを確認して、エラーの有無を判断する必要があります。
- 実行されているジョブのリリース タスクのすべてのインスタンスは、[List Preparation and Release Task Status](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API を使用してジョブから取得できます。 あらゆるタスクと同様、`failureInfo`、`exitCode`、`result` などのプロパティを備えた[実行情報](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation)が提供されます。
- ジョブのリリース タスクが 1 つ以上失敗すると、ジョブは強制終了され、`completed` 状態に移行します。

## <a name="tasks"></a>タスク

ジョブ タスクのエラーには、さまざまな理由が考えられます。

- タスクのコマンド ラインでエラーが発生すると、0 以外の終了コードが返される。
- タスクに `resourceFiles` が指定されているにもかかわらず、1 つ以上のファイルがダウンロードされなかったことを示すエラーが発生した。
- タスクに `outputFiles` が指定されているにもかかわらず、1 つ以上のファイルがアップロードされなかったことを示すエラーが発生した。
- タスクの[制約](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)の `maxWallClockTime` プロパティで指定された、タスクの経過時間を超えた。

いずれのケースも、次のプロパティで、エラーの有無やエラーに関する情報を確認する必要があります。
- タスクの [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) プロパティには、エラーに関する情報を提供するプロパティが複数含まれています。 なんらかの理由でタスクが失敗したかどうかは、[result](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) によって示され、その `exitCode` と `failureInfo` では、その失敗に関する詳細情報が提供されます。
- 成功したか失敗したかにかかわらず、タスクは常に `completed` [状態](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)に移行します。

タスクのエラーがジョブやタスクの依存関係に及ぼす影響を考慮する必要があります。  依存関係やジョブに対するアクションを構成するために、タスクに [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) プロパティを指定できます。
- 依存関係の場合、失敗したタスクに依存しているタスクがブロックされるか実行されるかは、[DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) によって制御されます。
- ジョブの場合、失敗したタスクが原因でジョブが無効になるか、強制終了されるか、未変更のまま据え置かれるかは、[JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) によって制御されます。

## <a name="next-steps"></a>次の手順

アプリケーションに包括的なエラー チェックが実装されていることを確認してください。これは、問題を迅速に検出して診断するためにきわめて重要となる場合があります。
