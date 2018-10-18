---
title: バッチプールとノードエラーのチェック
description: エラーを確認してプールとノードを作成するときに回避する方法
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435832"
---
# <a name="checking-for-pool-and-node-errors"></a>プールとノードのエラーのチェック

Batch プールを作成し管理する場合、すぐに行われる操作もあれば、即座には行われない、バックグラウンドで実行される非同期操作もあり、完了するまでに時間がかかる場合があります。

API、CLI、UI で直ちにエラーが返されるので、すぐに実行される操作のエラーを検出するのは簡単です。

この記事では、プールとプールのノードに対して実行できるバック グラウンド操作をについて説明します - エラーを検出する方法、およびエラーの回避方法を具体的に示しています。

## <a name="pool-errors"></a>プールのエラー

### <a name="resize-timeout-or-failure"></a>タイムアウトまたはエラーのサイズ変更

新しいプール作成している場合、または既存のプールのサイズ変更を行っている場合、ノードのターゲット数は指定されます。  操作はすぐに完了しますが、新しいノードの実際の割り当て、または既存のノードの削除はバック グラウンドで行われ、数分かかる可能性があります。  [作成](https://docs.microsoft.com/rest/api/batchservice/pool/add)または[サイズ変更](https://docs.microsoft.com/rest/api/batchservice/pool/resize)API はサイズ変更のタイムアウトが指定されます- サイズ変更のタイムアウト期間中にノードの目標数を取得できない場合、プールを安定した状態にし、サイズ変更エラーが発生したまま操作が停止します。

最後の評価のために[ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror)プロパティが報告したサイズ変更タイムアウトで、発生したエラーを1つかそれ以上リストにします。

サイズ変更タイムアウトの一般的な原因は次のとおりです。
- サイズ変更タイムアウトが短すぎる
  - 既定値のタイムアウトは15 分です。通常、プールのノードを割り当てる、または削除するには十分な時間です。
  - プールの作成またはサイズ変更時に多数のノード (Marketplace イメージから 1000 以上のノード、またはカスタムのイメージから 300 を超えるノード) を割り当てるときはタイムアウトを 30 分にすることをお勧めします。
- 不十分なコア クォータ
  - バッチアカウントには、すべてのプールにわたり割り当てられるコアの数へのクォータがあります。  そのクォータに達すると、バッチはノードを割り当てられません。  さらにノードを割り当てられるように、コア クォータを[増やすことができます](https://docs.microsoft.com/azure/batch/batch-quota-limit)。
- [プールが仮想ネットワーク内にある](https://docs.microsoft.com/azure/batch/batch-virtual-network)場合の不足しているサブネット IP アドレス
  - 仮想ネットワーク サブネットは、要求されたプールノードすべてに割り当てるため、割り当てられていない IP アドレスを十分持っている必要があります。さもなければ、ノードは作成されません。
- [プールが仮想ネットワーク](https://docs.microsoft.com/azure/batch/batch-virtual-network)の時の不十分なリソース
  - ロード バランサー、パブリック IP、NSG などのリソースは、Batch アカウントを作成するために使用されるサブスクリプションに作成されます。  これらのリソースへのサブスクリプションのクォータは十分である必要があります。
- 大規模なプールのカスタム VM イメージを使用している
  - カスタム イメージを使用する大規模なプールは、割り当てに時間がかかり、タイムアウトのサイズ変更が発生します。  [特定のアーティクル](https://docs.microsoft.com/azure/batch/batch-custom-images)で提供される制限と構成の推奨事項。 

### <a name="auto-scale-failures"></a>自動スケール エラー

明示的にプール作成またはサイズ変更内でプールのためのノードの目標数を設定するのではなく、プール内のノードの数は自動的にスケールできます。  [プールの自動スケール数式が作成され](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)、これは、プールのノードの目標数を設定する構成可能な一定の間隔で評価されます。  次の種類の問題が発生する可能性があり、検出されました。

- 自動スケールの評価は失敗することがあります。
- 結果のサイズ変更操作は失敗してタイムアウトになることがあります。
- サイズ変更操作またはタイムアウトになり、不適切なノードの目標値を導く、自動スケールの数式に問題がある可能性があります。

最後の自動スケール評価に関する情報は[autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun)プロパティ使用して取得でき、評価の時間、値、評価の結果、および評価を実行している間のエラーを報告します。

[プールのサイズ変更完了イベント](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)により、すべての評価に関する情報が自動的にキャプチャされます。

### <a name="delete"></a>削除

プール内のノードがあると仮定した場合、プールは最初に削除されたノードで操作の結果を削除し、そのあとにプール オブジェクトが続きます。  プールのノードを削除するまでに数分かかります。

[プールの状態](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)は、削除プロセス中に 'deleting' に設定されます。  プールの削除時間がかかりすぎている場合、呼び出しアプリケーションが 'state' と 'stateTransitionTime' プロパティを使用して検出します。

## <a name="pool-compute-node-errors"></a>プールのコンピューティング ノードエラー

ノードをプールに正常に割り当てることができますが、さまざまな問題がノードの異常につながり、役に立ちません。  ノードがプールに割り当てられると、料金が発生してしまうので、使用できないノードに対する支払いを回避するために問題を検出することが重要です。

### <a name="start-task-failure"></a>タスクの開始に失敗

省略可能な[タスクの開始](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)はプールに対して指定できます。  すべてのタスクと同様、ストレージからダウンロードするためにコマンドラインとリソース ファイルを指定できます。  タスクの開始は、プールに指定されますが、各ノードで実行されます ‐ 各ノードが開始したら、タスクの開始を実行します。  [タスクの開始](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)でさらにプロパティ 'waitForSuccess' は、バッチが、ノードにタスクがスケジュールされる前に、タスクの開始が正常に完了するのを待つべきかどうかを指定します。

タスクの開始が失敗し、タスクの開始構成が正常に完了するまで待機するよう指定された場合は、ノードは使用できなくなり、料金が発生します。

タスクの開始の失敗は[結果](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult)と[startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) ノードプロパティ最上位レベルの[failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) プロパティを使って検出できます。

失敗したタスクの開始は、'starttaskfailed' が 'true' に設定されている場合にのみ、ノード[状態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)'waitForSuccess' につながります。

すべてのタスクと同様、タスクの開始の失敗には多くの原因があります。  トラブルシューティングについては、stdout、stderr、さらにタスク固有のログ ファイルをチェックする必要があります。

### <a name="application-package-download-failure"></a>アプリケーション パッケージのダウンロード エラー

ノードが開始した後でタスクがスケジュールされる前ならば、各ノードにダウンロードされている指定パッケージ ファイルで、1 つまたは複数のアプリケーション パッケージをプールに指定することができます。  例えば、別の場所にファイルをコピーする、またはセットアップを実行するために、アプリケーション パッケージを組み合わせてタスクの開始コマンドラインを使用することは一般的です。

ダウンロードやアプリケーション パッケージの圧縮解除に失敗したノードは、ノード[エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)プロパティが報告します。  ノードの状態は '使用できない' に設定されます。

### <a name="node-in-unusable-state"></a>ノードは使用できない状態

[ノード状態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)設定できる '使用できない' にはさまざまな理由があります。  '使用できない' タスクは、ノードにタスクをスケジュールすることはできませんが、それでもノードに料金が発生します。

バッチは、使用できないノードの回復を常に試みますが、原因によっては、回復の可能性がある場合とない場合があります。

原因が特定された場合はノード[エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)プロパティにより報告されます。

'使用できない' ノードの原因の他の例:

- カスタム イメージが無効です。たとえば、正しく準備されません。
- インフラストラクチャのエラーまたは低レベルのアップグレードは、原因となるVMを移動させることへとつながります；Batch はノードを回復します。

### <a name="node-agent-log-files"></a>ノード エージェント ログ ファイル

プール ノードの問題についてサポートに連絡する必要がある場合、各プール ノードで実行される Batch エージェント プロセスからログ ファイルを取得できます。  Azure portal、Batch エクスプ ローラー、または[API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs)で、ノードのログ ファイルをアップロードできます。  コストを節約するためにノードやプールを削除できるので、ログ ファイルのアップロードと保存は非常に役に立ちます。

## <a name="next-steps"></a>次の手順

問題がすぐに検出され特定されるよう、特に非同期操作に関して、アプリケーションが包括的なエラー チェックを行ったことを確認してください。
