---
title: Azure Monitor でサポートされているメトリック (リソースの種類別)
description: Azure Monitor で使用できるメトリックの一覧 (リソースの種類別) です。
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 03/30/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 3219f8e61a0aa469775a972e6b240eb2069c2cd9
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929969"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor のサポートされるメトリック
Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 ここで示しているのは、Azure Monitor のメトリック パイプラインで現在利用できるメトリックの一覧です。 他のメトリックについては、ポータルや従来の API で使用できる場合があります。 以下の一覧には、統合された Azure Monitor メトリック パイプラインで使うことができるメトリックのみが含まれます。 これらのメトリックを照会してアクセスするには、[2018-01-01 バージョンの API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) を使ってください。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|平均|QPU。 範囲は S1 で 0 ～ 100、S2 で 0 ～ 200、S4 で 0 ～ 400|ServerResourceType|
|memory_metric|メモリ|バイト|平均|メモリ。 範囲は S1 で 0 ～ 25 GB、S2 で 0 ～ 50 GB、S4 で 0 ～ 100 GB|ServerResourceType|
|TotalConnectionRequests|合計接続要求数|Count|平均|合計接続要求数。 これらは到着した接続要求です。|ServerResourceType|
|SuccessfullConnectionsPerSec|成功した接続数 (秒単位)|CountPerSecond|平均|接続が正常に完了した割合。|ServerResourceType|
|TotalConnectionFailures|合計接続失敗数|Count|平均|失敗した接続試行数の合計。|ServerResourceType|
|CurrentUserSessions|現在のユーザー セッション|Count|平均|確立された現在のユーザー セッションの数。|ServerResourceType|
|QueryPoolBusyThreads|クエリ プール ビジー スレッド|Count|平均|クエリ スレッド プールのビジー スレッドの数。|ServerResourceType|
|CommandPoolJobQueueLength|コマンド プールのジョブ キューの長さ|Count|平均|コマンド スレッド プールのキュー内のジョブの数。|ServerResourceType|
|ProcessingPoolJobQueueLength|処理プール ジョブ キューの長さ|Count|平均|処理スレッド プールのキュー内の非 I/O ジョブの数。|ServerResourceType|
|CurrentConnections|接続: 現在の接続|Count|平均|現在確立されているクライアント接続の数。|ServerResourceType|
|CleanerCurrentPrice|メモリ: クリーナーの現在の価格|Count|平均|現在のメモリ価格 ($/バイト/時刻)。標準は 1,000 です。|ServerResourceType|
|CleanerMemoryShrinkable|メモリ: クリーナーの圧縮可能なメモリ|Bytes|平均|バックグラウンド クリーナーによる削除の対象となるメモリの量 (バイト単位)。|ServerResourceType|
|CleanerMemoryNonshrinkable|メモリ: クリーナーの圧縮不可能なメモリ|Bytes|平均|バックグラウンド クリーナーによる削除の対象とならないメモリの量 (バイト単位)。|ServerResourceType|
|MemoryUsage|メモリ: メモリ使用量|Bytes|平均|クリーナー メモリの価格の計算で使用されるサーバー プロセスのメモリ使用量。 Process\PrivateBytes カウンターの値に、メモリがマップされたデータのサイズを加えた値と等しくなります。xVelocity インメモリ分析エンジン (VertiPaq) によって xVelocity エンジンのメモリの制限を超えてマップされた (割り当てられた) メモリは無視されます。|ServerResourceType|
|MemoryLimitHard|メモリ: メモリの制限 - ハード|Bytes|平均|構成ファイルに指定されているハードのメモリの制限。|ServerResourceType|
|MemoryLimitHigh|メモリ: メモリの制限 - 高|Bytes|平均|構成ファイルに指定されているメモリの上限。|ServerResourceType|
|MemoryLimitLow|メモリ: メモリの制限 - 低|Bytes|平均|構成ファイルに指定されているメモリの下限。|ServerResourceType|
|MemoryLimitVertiPaq|メモリ: メモリの制限 - VertiPaq|Bytes|平均|構成ファイルに指定されているインメモリの制限。|ServerResourceType|
|Quota|メモリ: クォータ|Bytes|平均|現在のメモリ クォータ (バイト単位)。 メモリ クォータはメモリ付与またはメモリ予約とも呼ばれます。|ServerResourceType|
|QuotaBlocked|メモリ: ブロックされているクォータ|Count|平均|他のメモリ クォータが解放されるまでブロックされている現在のクォータ要求の数。|ServerResourceType|
|VertiPaqNonpaged|メモリ: VertiPaq 非ページ|Bytes|平均|メモリ内エンジン用にワーキング セットでロックされているメモリの量 (バイト単位)。|ServerResourceType|
|VertiPaqPaged|メモリ: VertiPaq ページ|Bytes|平均|メモリ内エンジン用に使用されているページ メモリの量 (バイト単位)。|ServerResourceType|
|RowsReadPerSec|処理: 1 秒あたりの読み取り行数|CountPerSecond|平均|すべてのリレーショナル データベースから読み取った行の比率。|ServerResourceType|
|RowsConvertedPerSec|処理: 1 秒あたりの変換行数|CountPerSecond|平均|処理中に変換された行の比率。|ServerResourceType|
|RowsWrittenPerSec|処理: 1 秒あたりの書き込み行数|CountPerSecond|平均|処理中に書き込まれた行の比率。|ServerResourceType|
|CommandPoolBusyThreads|スレッド: コマンド プールのビジー状態のスレッド|Count|平均|コマンド スレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|CommandPoolIdleThreads|スレッド: コマンド プールのアイドル状態のスレッド|Count|平均|コマンド スレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingBusyThreads|スレッド: 長時間解析を行っているビジー状態のスレッド|Count|平均|長時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|LongParsingIdleThreads|スレッド: 長時間解析を行っているアイドル状態のスレッド|Count|平均|長時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingJobQueueLength|スレッド: 長時間解析を行っているジョブのキューの長さ|Count|平均|長時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|スレッド: 処理中のプールのビジー状態の I/O ジョブのスレッド|Count|平均|処理中のスレッド プール内で I/O ジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|スレッド: 処理中のプールのビジー状態の I/O 以外のスレッド|Count|平均|処理中のスレッド プール内で I/O 以外のジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|スレッド: 処理中のプール I/O ジョブ キューの長さ|Count|平均|処理中のスレッド プールのキューに登録されている I/O ジョブの数。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|スレッド: 処理中のプールのアイドル状態の I/O ジョブのスレッド|Count|平均|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|スレッド: 処理中のプールのアイドル状態の I/O 以外のスレッド|Count|平均|I/O 以外のジョブ専用の、処理中のスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolIdleThreads|スレッド: クエリ プールのアイドル状態のスレッド|Count|平均|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolJobQueueLength|スレッド: クエリ プール ジョブ キューの長さ|Count|平均|クエリ スレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|ShortParsingBusyThreads|スレッド: 短時間解析を行っているビジー状態のスレッド|Count|平均|短時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|ShortParsingIdleThreads|スレッド: 短時間解析を行っているアイドル状態のスレッド|Count|平均|短時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|ShortParsingJobQueueLength|スレッド: 短時間解析を行っているジョブ キューの長さ|Count|平均|短時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|memory_thrashing_metric|メモリ スラッシング|Percent|平均|平均的なメモリ スラッシング。|ServerResourceType|
|mashup_engine_qpu_metric|M エンジン QPU|Count|平均|マッシュアップ エンジン プロセスによる QPU 使用率|ServerResourceType|
|mashup_engine_memory_metric|M エンジン メモリ|Bytes|平均|マッシュアップ エンジン プロセスによるメモリ使用率|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|TotalRequests|ゲートウェイ要求の合計|Count|合計|ゲートウェイ要求の数|Location、Hostname|
|SuccessfulRequests|成功したゲートウェイ要求|Count|合計|成功したゲートウェイ要求の数|Location、Hostname|
|UnauthorizedRequests|未承認ゲートウェイ要求|Count|合計|未承認ゲートウェイ要求の数|Location、Hostname|
|FailedRequests|失敗したゲートウェイ要求|Count|合計|ゲートウェイ要求における失敗の数|Location、Hostname|
|OtherRequests|その他のゲートウェイ要求|Count|合計|その他のゲートウェイ要求の数|Location、Hostname|
|時間|ゲートウェイ要求の全体の期間|ミリ秒|平均|ゲートウェイ要求の全体の期間 (ミリ秒単位)|Location、Hostname|
|容量|容量 (プレビュー)|Percent|平均|ApiManagement サービスの使用状況メトリック|Location|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|TotalJob|合計ジョブ数|Count|合計|ジョブの合計数|Runbook、Status|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count (専用コアの数)|Count|合計|Batch アカウントの専用コアの合計数|ディメンションなし|
|TotalNodeCount|Dedicated Node Count (専用ノードの数)|Count|合計|Batch アカウントの専用ノードの合計数|ディメンションなし|
|LowPriorityCoreCount|LowPriority Core Count (優先順位の低いコアの数)|Count|合計|Batch アカウントの優先順位の低いコアの合計数|ディメンションなし|
|TotalLowPriorityNodeCount|Low-Priority Node Count (優先順位の低いノードの数)|Count|合計|Batch アカウントの優先順位の低いノードの合計数|ディメンションなし|
|CreatingNodeCount|作成ノード数|Count|合計|作成されるノードの数|ディメンションなし|
|StartingNodeCount|開始ノード数|Count|合計|開始するノードの数|ディメンションなし|
|WaitingForStartTaskNodeCount|開始タスクの待機ノード数|Count|合計|開始タスクの完了を待機しているノードの数|ディメンションなし|
|StartTaskFailedNodeCount|開始タスクの失敗ノード数|Count|合計|開始タスクが失敗したノードの数|ディメンションなし|
|IdleNodeCount|アイドル状態のノード数|Count|合計|アイドル状態のノードの数|ディメンションなし|
|OfflineNodeCount|オフライン ノード数|Count|合計|オフライン ノードの数|ディメンションなし|
|RebootingNodeCount|再起動中のノード数|Count|合計|再起動中のノードの数|ディメンションなし|
|ReimagingNodeCount|再イメージ化中のノード数|Count|合計|再イメージ化中のノードの数|ディメンションなし|
|RunningNodeCount|実行中のノード数|Count|合計|実行中のノードの数|ディメンションなし|
|LeavingPoolNodeCount|プールから移動中のノード数|Count|合計|プールから移動中のノードの数|ディメンションなし|
|UnusableNodeCount|使用できないノード数|Count|合計|使用できないノードの数|ディメンションなし|
|PreemptedNodeCount|Preempted Node Count (割り込まれたノードの数)|Count|合計|割り込まれたノードの数|ディメンションなし|
|TaskStartEvent|タスク開始イベント|Count|合計|開始したタスクの合計数|ディメンションなし|
|TaskCompleteEvent|タスク完了イベント|Count|合計|完了したタスクの合計数|ディメンションなし|
|TaskFailEvent|タスク失敗イベント|Count|合計|障害状態で完了したタスクの合計数|ディメンションなし|
|PoolCreateEvent|プール作成イベント|Count|合計|作成されたプールの合計数|ディメンションなし|
|PoolResizeStartEvent|プールのサイズ変更の開始イベント|Count|合計|開始したプールのサイズ変更の合計数|ディメンションなし|
|PoolResizeCompleteEvent|プールのサイズ変更の完了イベント|Count|合計|完了したプールのサイズ変更の合計数|ディメンションなし|
|PoolDeleteStartEvent|プール削除の開始イベント|Count|合計|開始したプール削除の合計数|ディメンションなし|
|PoolDeleteCompleteEvent|プール削除の完了イベント|Count|合計|完了したプール削除の合計数|ディメンションなし|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|connectedclients|接続されているクライアント数|Count|最大値||ディメンションなし|
|totalcommandsprocessed|合計処理数|Count|合計||ディメンションなし|
|cachehits|キャッシュ ヒット数|Count|合計||ディメンションなし|
|cachemisses|キャッシュ ミス数|Count|合計||ディメンションなし|
|getcommands|取得数|Count|合計||ディメンションなし|
|setcommands|設定数|Count|合計||ディメンションなし|
|operationsPerSecond|1 秒あたりの操作回数|Count|合計||ディメンションなし|
|evictedkeys|削除されたキー数|Count|合計||ディメンションなし|
|totalkeys|合計キー数|Count|最大値||ディメンションなし|
|expiredkeys|期限切れキー数|Count|合計||ディメンションなし|
|usedmemory|メモリ使用量|Bytes|最大値||ディメンションなし|
|usedmemoryRss|RSS メモリ使用量|Bytes|最大値||ディメンションなし|
|serverLoad|サーバーの負荷|Percent|最大値||ディメンションなし|
|cacheWrite|キャッシュの書き込み|BytesPerSecond|最大値||ディメンションなし|
|cacheRead|キャッシュの読み取り|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime|CPU|Percent|最大値||ディメンションなし|
|connectedclients0|接続されているクライアント数 (シャード 0)|Count|最大値||ディメンションなし|
|totalcommandsprocessed0|合計処理数 (シャード 0)|Count|合計||ディメンションなし|
|cachehits0|キャッシュ ヒット数 (シャード 0)|Count|合計||ディメンションなし|
|cachemisses0|キャッシュ ミス数 (シャード 0)|Count|合計||ディメンションなし|
|getcommands0|取得数 (シャード 0)|Count|合計||ディメンションなし|
|setcommands0|設定数 (シャード 0)|Count|合計||ディメンションなし|
|operationsPerSecond0|1 秒あたりの操作回数 (シャード 0)|Count|合計||ディメンションなし|
|evictedkeys0|削除されたキー数 (シャード 0)|Count|合計||ディメンションなし|
|totalkeys0|合計キー数 (シャード 0)|Count|最大値||ディメンションなし|
|expiredkeys0|期限切れキー数 (シャード 0)|Count|合計||ディメンションなし|
|usedmemory0|メモリ使用量 (シャード 0)|Bytes|最大値||ディメンションなし|
|usedmemoryRss0|RSS メモリ使用量 (シャード 0)|Bytes|最大値||ディメンションなし|
|serverLoad0|サーバーの負荷 (シャード 0)|Percent|最大値||ディメンションなし|
|cacheWrite0|キャッシュの書き込み (シャード 0)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead0|キャッシュの読み取り (シャード 0)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime0|CPU (シャード 0)|Percent|最大値||ディメンションなし|
|connectedclients1|接続されているクライアント数 (シャード 1)|Count|最大値||ディメンションなし|
|totalcommandsprocessed1|合計処理数 (シャード 1)|Count|合計||ディメンションなし|
|cachehits1|キャッシュ ヒット数 (シャード 1)|Count|合計||ディメンションなし|
|cachemisses1|キャッシュ ミス数 (シャード 1)|Count|合計||ディメンションなし|
|getcommands1|取得数 (シャード 1)|Count|合計||ディメンションなし|
|setcommands1|設定数 (シャード 1)|Count|合計||ディメンションなし|
|operationsPerSecond1|1 秒あたりの操作回数 (シャード 1)|Count|合計||ディメンションなし|
|evictedkeys1|削除されたキー数 (シャード 1)|Count|合計||ディメンションなし|
|totalkeys1|合計キー数 (シャード 1)|Count|最大値||ディメンションなし|
|expiredkeys1|期限切れキー数 (シャード 1)|Count|合計||ディメンションなし|
|usedmemory1|メモリ使用量 (シャード 1)|Bytes|最大値||ディメンションなし|
|usedmemoryRss1|RSS メモリ使用量 (シャード 1)|Bytes|最大値||ディメンションなし|
|serverLoad1|サーバーの負荷 (シャード 1)|Percent|最大値||ディメンションなし|
|cacheWrite1|キャッシュの書き込み (シャード 1)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead1|キャッシュの読み取り (シャード 1)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime1|CPU (シャード 1)|Percent|最大値||ディメンションなし|
|connectedclients2|接続されているクライアント数 (シャード 2)|Count|最大値||ディメンションなし|
|totalcommandsprocessed2|合計処理数 (シャード 2)|Count|合計||ディメンションなし|
|cachehits2|キャッシュ ヒット数 (シャード 2)|Count|合計||ディメンションなし|
|cachemisses2|キャッシュ ミス数 (シャード 2)|Count|合計||ディメンションなし|
|getcommands2|取得数 (シャード 2)|Count|合計||ディメンションなし|
|setcommands2|設定数 (シャード 2)|Count|合計||ディメンションなし|
|operationsPerSecond2|1 秒あたりの操作回数 (シャード 2)|Count|合計||ディメンションなし|
|evictedkeys2|削除されたキー数 (シャード 2)|Count|合計||ディメンションなし|
|totalkeys2|合計キー数 (シャード 2)|Count|最大値||ディメンションなし|
|expiredkeys2|期限切れキー数 (シャード 2)|Count|合計||ディメンションなし|
|usedmemory2|メモリ使用量 (シャード 2)|Bytes|最大値||ディメンションなし|
|usedmemoryRss2|RSS メモリ使用量 (シャード 2)|Bytes|最大値||ディメンションなし|
|serverLoad2|サーバーの負荷 (シャード 2)|Percent|最大値||ディメンションなし|
|cacheWrite2|キャッシュの書き込み (シャード 2)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead2|キャッシュの読み取り (シャード 2)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime2|CPU (シャード 2)|Percent|最大値||ディメンションなし|
|connectedclients3|接続されているクライアント数 (シャード 3)|Count|最大値||ディメンションなし|
|totalcommandsprocessed3|合計処理数 (シャード 3)|Count|合計||ディメンションなし|
|cachehits3|キャッシュ ヒット数 (シャード 3)|Count|合計||ディメンションなし|
|cachemisses3|キャッシュ ミス数 (シャード 3)|Count|合計||ディメンションなし|
|getcommands3|取得数 (シャード 3)|Count|合計||ディメンションなし|
|setcommands3|設定数 (シャード 3)|Count|合計||ディメンションなし|
|operationsPerSecond3|1 秒あたりの操作回数 (シャード 3)|Count|合計||ディメンションなし|
|evictedkeys3|削除されたキー数 (シャード 3)|Count|合計||ディメンションなし|
|totalkeys3|合計キー数 (シャード 3)|Count|最大値||ディメンションなし|
|expiredkeys3|期限切れキー数 (シャード 3)|Count|合計||ディメンションなし|
|usedmemory3|メモリ使用量 (シャード 3)|Bytes|最大値||ディメンションなし|
|usedmemoryRss3|RSS メモリ使用量 (シャード 3)|Bytes|最大値||ディメンションなし|
|serverLoad3|サーバーの負荷 (シャード 3)|Percent|最大値||ディメンションなし|
|cacheWrite3|キャッシュの書き込み (シャード 3)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead3|キャッシュの読み取り (シャード 3)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime3|CPU (シャード 3)|Percent|最大値||ディメンションなし|
|connectedclients4|接続されているクライアント数 (シャード 4)|Count|最大値||ディメンションなし|
|totalcommandsprocessed4|合計処理数 (シャード 4)|Count|合計||ディメンションなし|
|cachehits4|キャッシュ ヒット数 (シャード 4)|Count|合計||ディメンションなし|
|cachemisses4|キャッシュ ミス数 (シャード 4)|Count|合計||ディメンションなし|
|getcommands4|取得数 (シャード 4)|Count|合計||ディメンションなし|
|setcommands4|設定数 (シャード 4)|Count|合計||ディメンションなし|
|operationsPerSecond4|1 秒あたりの操作回数 (シャード 4)|Count|合計||ディメンションなし|
|evictedkeys4|削除されたキー数 (シャード 4)|Count|合計||ディメンションなし|
|totalkeys4|合計キー数 (シャード 4)|Count|最大値||ディメンションなし|
|expiredkeys4|期限切れキー数 (シャード 4)|Count|合計||ディメンションなし|
|usedmemory4|メモリ使用量 (シャード 4)|Bytes|最大値||ディメンションなし|
|usedmemoryRss4|RSS メモリ使用量 (シャード 4)|Bytes|最大値||ディメンションなし|
|serverLoad4|サーバーの負荷 (シャード 4)|Percent|最大値||ディメンションなし|
|cacheWrite4|キャッシュの書き込み (シャード 4)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead4|キャッシュの読み取り (シャード 4)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime4|CPU (シャード 4)|Percent|最大値||ディメンションなし|
|connectedclients5|接続されているクライアント数 (シャード 5)|Count|最大値||ディメンションなし|
|totalcommandsprocessed5|合計処理数 (シャード 5)|Count|合計||ディメンションなし|
|cachehits5|キャッシュ ヒット数 (シャード 5)|Count|合計||ディメンションなし|
|cachemisses5|キャッシュ ミス数 (シャード 5)|Count|合計||ディメンションなし|
|getcommands5|取得数 (シャード 5)|Count|合計||ディメンションなし|
|setcommands5|設定数 (シャード 5)|Count|合計||ディメンションなし|
|operationsPerSecond5|1 秒あたりの操作回数 (シャード 5)|Count|合計||ディメンションなし|
|evictedkeys5|削除されたキー数 (シャード 5)|Count|合計||ディメンションなし|
|totalkeys5|合計キー数 (シャード 5)|Count|最大値||ディメンションなし|
|expiredkeys5|期限切れキー数 (シャード 5)|Count|合計||ディメンションなし|
|usedmemory5|メモリ使用量 (シャード 5)|Bytes|最大値||ディメンションなし|
|usedmemoryRss5|RSS メモリ使用量 (シャード 5)|Bytes|最大値||ディメンションなし|
|serverLoad5|サーバーの負荷 (シャード 5)|Percent|最大値||ディメンションなし|
|cacheWrite5|キャッシュの書き込み (シャード 5)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead5|キャッシュの読み取り (シャード 5)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime5|CPU (シャード 5)|Percent|最大値||ディメンションなし|
|connectedclients6|接続されているクライアント数 (シャード 6)|Count|最大値||ディメンションなし|
|totalcommandsprocessed6|合計処理数 (シャード 6)|Count|合計||ディメンションなし|
|cachehits6|キャッシュ ヒット数 (シャード 6)|Count|合計||ディメンションなし|
|cachemisses6|キャッシュ ミス数 (シャード 6)|Count|合計||ディメンションなし|
|getcommands6|取得数 (シャード 6)|Count|合計||ディメンションなし|
|setcommands6|設定数 (シャード 6)|Count|合計||ディメンションなし|
|operationsPerSecond6|1 秒あたりの操作回数 (シャード 6)|Count|合計||ディメンションなし|
|evictedkeys6|削除されたキー数 (シャード 6)|Count|合計||ディメンションなし|
|totalkeys6|合計キー数 (シャード 6)|Count|最大値||ディメンションなし|
|expiredkeys6|期限切れキー数 (シャード 6)|Count|合計||ディメンションなし|
|usedmemory6|メモリ使用量 (シャード 6)|Bytes|最大値||ディメンションなし|
|usedmemoryRss6|RSS メモリ使用量 (シャード 6)|Bytes|最大値||ディメンションなし|
|serverLoad6|サーバーの負荷 (シャード 6)|Percent|最大値||ディメンションなし|
|cacheWrite6|キャッシュの書き込み (シャード 6)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead6|キャッシュの読み取り (シャード 6)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime6|CPU (シャード 6)|Percent|最大値||ディメンションなし|
|connectedclients7|接続されているクライアント数 (シャード 7)|Count|最大値||ディメンションなし|
|totalcommandsprocessed7|合計処理数 (シャード 7)|Count|合計||ディメンションなし|
|cachehits7|キャッシュ ヒット数 (シャード 7)|Count|合計||ディメンションなし|
|cachemisses7|キャッシュ ミス数 (シャード 7)|Count|合計||ディメンションなし|
|getcommands7|取得数 (シャード 7)|Count|合計||ディメンションなし|
|setcommands7|設定数 (シャード 7)|Count|合計||ディメンションなし|
|operationsPerSecond7|1 秒あたりの操作回数 (シャード 7)|Count|合計||ディメンションなし|
|evictedkeys7|削除されたキー数 (シャード 7)|Count|合計||ディメンションなし|
|totalkeys7|合計キー数 (シャード 7)|Count|最大値||ディメンションなし|
|expiredkeys7|期限切れキー数 (シャード 7)|Count|合計||ディメンションなし|
|usedmemory7|メモリ使用量 (シャード 7)|Bytes|最大値||ディメンションなし|
|usedmemoryRss7|RSS メモリ使用量 (シャード 7)|Bytes|最大値||ディメンションなし|
|serverLoad7|サーバーの負荷 (シャード 7)|Percent|最大値||ディメンションなし|
|cacheWrite7|キャッシュの書き込み (シャード 7)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead7|キャッシュの読み取り (シャード 7)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime7|CPU (シャード 7)|Percent|最大値||ディメンションなし|
|connectedclients8|接続されているクライアント数 (シャード 8)|Count|最大値||ディメンションなし|
|totalcommandsprocessed8|合計処理数 (シャード 8)|Count|合計||ディメンションなし|
|cachehits8|キャッシュ ヒット数 (シャード 8)|Count|合計||ディメンションなし|
|cachemisses8|キャッシュ ミス数 (シャード 8)|Count|合計||ディメンションなし|
|getcommands8|取得数 (シャード 8)|Count|合計||ディメンションなし|
|setcommands8|設定数 (シャード 8)|Count|合計||ディメンションなし|
|operationsPerSecond8|1 秒あたりの操作回数 (シャード 8)|Count|合計||ディメンションなし|
|evictedkeys8|削除されたキー数 (シャード 8)|Count|合計||ディメンションなし|
|totalkeys8|合計キー数 (シャード 8)|Count|最大値||ディメンションなし|
|expiredkeys8|期限切れキー数 (シャード 8)|Count|合計||ディメンションなし|
|usedmemory8|メモリ使用量 (シャード 8)|Bytes|最大値||ディメンションなし|
|usedmemoryRss8|RSS メモリ使用量 (シャード 8)|Bytes|最大値||ディメンションなし|
|serverLoad8|サーバーの負荷 (シャード 8)|Percent|最大値||ディメンションなし|
|cacheWrite8|キャッシュの書き込み (シャード 8)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead8|キャッシュの読み取り (シャード 8)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime8|CPU (シャード 8)|Percent|最大値||ディメンションなし|
|connectedclients9|接続されているクライアント数 (シャード 9)|Count|最大値||ディメンションなし|
|totalcommandsprocessed9|合計処理数 (シャード 9)|Count|合計||ディメンションなし|
|cachehits9|キャッシュ ヒット数 (シャード 9)|Count|合計||ディメンションなし|
|cachemisses9|キャッシュ ミス数 (シャード 9)|Count|合計||ディメンションなし|
|getcommands9|取得数 (シャード 9)|Count|合計||ディメンションなし|
|setcommands9|設定数 (シャード 9)|カウント|合計||ディメンションなし|
|operationsPerSecond9|1 秒あたりの操作回数 (シャード 9)|Count|合計||ディメンションなし|
|evictedkeys9|削除されたキー数 (シャード 9)|Count|合計||ディメンションなし|
|totalkeys9|合計キー数 (シャード 9)|Count|最大値||ディメンションなし|
|expiredkeys9|期限切れキー数 (シャード 9)|Count|合計||ディメンションなし|
|usedmemory9|メモリ使用量 (シャード 9)|Bytes|最大値||ディメンションなし|
|usedmemoryRss9|RSS メモリ使用量 (シャード 9)|Bytes|最大値||ディメンションなし|
|serverLoad9|サーバーの負荷 (シャード 9)|Percent|最大値||ディメンションなし|
|cacheWrite9|キャッシュの書き込み (シャード 9)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead9|キャッシュの読み取り (シャード 9)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime9|CPU (シャード 9)|Percent|最大値||ディメンションなし|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|ディメンションなし|
|Disk Read Bytes/Sec|ディスクの読み取り|BytesPerSecond|平均|監視期間中にディスクから読み取られた平均バイト数。|ディメンションなし|
|Disk Write Bytes/Sec|ディスクの書き込み|BytesPerSecond|平均|監視期間中にディスクに書き込まれた平均バイト数。|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS。|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS。|ディメンションなし|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|ディメンションなし|
|Disk Read Bytes/Sec|ディスクの読み取り|BytesPerSecond|平均|監視期間中にディスクから読み取られた平均バイト数。|ディメンションなし|
|Disk Write Bytes/Sec|ディスクの書き込み|BytesPerSecond|平均|監視期間中にディスクに書き込まれた平均バイト数。|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS。|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS。|ディメンションなし|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|TotalCalls|合計呼び出し数|Count|合計|呼び出しの合計数。|ディメンションなし|
|SuccessfulCalls|成功した呼び出し|Count|合計|成功した呼び出しの数。|ディメンションなし|
|TotalErrors|合計エラー数|Count|合計|エラー応答 (HTTP 応答コード 4xx または 5xx) があった呼び出しの合計回数。|ディメンションなし|
|BlockedCalls|ブロックされた呼び出し|Count|合計|レートまたはクォータの制限を超えた呼び出しの回数。|ディメンションなし|
|ServerErrors|サーバー エラー|Count|合計|サービスの内部エラー (HTTP 応答コード 5xx) があった呼び出しの回数。|ディメンションなし|
|ClientErrors|クライアント エラー|Count|合計|クライアント側のエラー (HTTP 応答コード 4xx) があった呼び出しの回数。|ディメンションなし|
|DataIn|受信データ|Bytes|合計|受信データのサイズ (バイト)。|ディメンションなし|
|DataOut|送信データ|Bytes|合計|送信データのサイズ (バイト)。|ディメンションなし|
|Latency|Latency|MilliSeconds|平均|待機時間 (ミリ秒)。|ディメンションなし|
|CharactersTranslated|Characters Translated (変換文字数)|Count|合計|受信テキスト要求の合計文字数。|ディメンションなし|
|SpeechSessionDuration|Speech Session Duration (音声セッション継続時間)|Seconds|合計|音声セッションの合計継続時間 (秒)。|ディメンションなし|
|TotalTransactions|合計トランザクション|Count|合計|トランザクション数|ディメンションなし|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|ディメンションなし|
|Disk Read Bytes|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られた合計バイト数|ディメンションなし|
|Disk Write Bytes|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれた合計バイト数|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|ディメンションなし|
|CPU Credits Remaining|未使用の CPU クレジット|Count|平均|バーストに使用できるクレジットの合計|ディメンションなし|
|CPU Credits Consumed|使用済みの CPU クレジット|Count|平均|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られた合計バイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれた合計バイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中、1 つのディスクからの読み取り中に実行された合計 IOPS 数|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中、1 つのディスクへの書き込み中に実行された合計 IOPS 数|SlotId|
|Per Disk QD|データ ディスク QD (プレビュー)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られた合計バイト数/秒|ディメンションなし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれた合計バイト数/秒|ディメンションなし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中、1 つのディスクからの読み取り中に実行された合計 IOPS 数|ディメンションなし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中、1 つのディスクへの書き込み中に実行された合計 IOPS 数|ディメンションなし|
|OS Per Disk QD|OS ディスク QD (プレビュー)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|ディメンションなし|
|Disk Read Bytes|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られた合計バイト数|ディメンションなし|
|Disk Write Bytes|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれた合計バイト数|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|ディメンションなし|
|CPU Credits Remaining|未使用の CPU クレジット|Count|平均|バーストに使用できるクレジットの合計|ディメンションなし|
|CPU Credits Consumed|使用済みの CPU クレジット|Count|平均|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られた合計バイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれた合計バイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中、1 つのディスクからの読み取り中に実行された合計 IOPS 数|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中、1 つのディスクへの書き込み中に実行された合計 IOPS 数|SlotId|
|Per Disk QD|データ ディスク QD (プレビュー)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られた合計バイト数/秒|ディメンションなし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれた合計バイト数/秒|ディメンションなし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中、1 つのディスクからの読み取り中に実行された合計 IOPS 数|ディメンションなし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中、1 つのディスクへの書き込み中に実行された合計 IOPS 数|ディメンションなし|
|OS Per Disk QD|OS ディスク QD (プレビュー)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|ディメンションなし|
|Disk Read Bytes|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られた合計バイト数|ディメンションなし|
|Disk Write Bytes|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれた合計バイト数|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|ディメンションなし|
|CPU Credits Remaining|未使用の CPU クレジット|Count|平均|バーストに使用できるクレジットの合計|ディメンションなし|
|CPU Credits Consumed|使用済みの CPU クレジット|Count|平均|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られた合計バイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれた合計バイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中、1 つのディスクからの読み取り中に実行された合計 IOPS 数|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中、1 つのディスクへの書き込み中に実行された合計 IOPS 数|SlotId|
|Per Disk QD|データ ディスク QD (プレビュー)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られた合計バイト数/秒|ディメンションなし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれた合計バイト数/秒|ディメンションなし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中、1 つのディスクからの読み取り中に実行された合計 IOPS 数|ディメンションなし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中、1 つのディスクへの書き込み中に実行された合計 IOPS 数|ディメンションなし|
|OS Per Disk QD|OS ディスク QD (プレビュー)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|Count|平均|すべてのコアの CPU 使用率 (ミリコア単位)。|containerName|
|MemoryUsage|メモリ使用量|Bytes|平均|合計メモリ使用量 (バイト単位)。|containerName|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|マネージド クラスターで使用可能な CPU コアの合計数|Count|合計|マネージド クラスターで使用可能な CPU コアの合計数|ディメンションなし|
|kube_node_status_allocatable_memory_bytes|マネージド クラスターで使用可能なメモリの合計量|Bytes|合計|マネージド クラスターで使用可能なメモリの合計量|ディメンションなし|
|kube_pod_status_ready|準備完了状態のポッドの数|Count|合計|準備完了状態のポッドの数|namespace、pod|
|kube_node_status_condition|さまざまなノードの状態の条件|Count|合計|さまざまなノードの状態の条件|condition、status、node|
|kube_pod_status_phase|フェーズごとのポッドの数|Count|合計|フェーズごとのポッドの数|phase、namespace、pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls (Customer Insights API 呼び出し)|Count|合計||ディメンションなし|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines (マッピングのインポート操作に成功した行数)|Count|合計||ディメンションなし|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines (マッピングのインポート操作に失敗した行数)|Count|合計||ディメンションなし|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines (マッピングのインポート操作の合計行数)|Count|合計||ディメンションなし|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds (マッピングのインポート操作の実行時間 (秒))|Seconds|合計||ディメンションなし|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded (成功した送信プロファイルのエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed (失敗した送信プロファイルのエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration (送信プロファイルのエクスポート時間)|Seconds|合計||ディメンションなし|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded (成功した送信 KPI のエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed (失敗した送信 KPI のエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration (送信 KPI のエクスポート時間)|Seconds|合計||ディメンションなし|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started (送信 KPI のエクスポートの開始)|Seconds|合計||ディメンションなし|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count (送信 KPI レコードの数)|Seconds|合計||ディメンションなし|
|DCIOutboundProfileExportCount|Outbound Profile Export Count (送信プロファイルのエクスポートの数)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed (失敗した送信初期プロファイルのエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded (成功した送信初期プロファイルのエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed (失敗した送信初期 KPI のエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded (成功した送信初期 KPI のエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds (送信初期プロファイルのエクスポート時間 (秒))|Seconds|合計||ディメンションなし|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds (失敗した標準 KPI の Adla ジョブ (秒))|Seconds|合計||ディメンションなし|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds (標準 KPI の Adla ジョブのタイムアウト (秒))|Seconds|合計||ディメンションなし|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds (完了した標準 KPI の Adla ジョブ (秒))|Seconds|合計||ディメンションなし|
|ImportASAValuesFailed|Import ASA Values Failed Count (ASA 値のインポートの失敗数)|Count|合計||ディメンションなし|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count (ASA 値のインポートの成功数)|Count|合計||ディメンションなし|
|DCIProfilesCount|Profile Instance Count (プロファイル インスタンス数)|Count|Last (最後へ)||ディメンションなし|
|DCIInteractionsPerMonthCount|1 か月間のインタラクション数|Count|Last (最後へ)||ディメンションなし|
|DCIKpisCount|KPI Count (KPI の数)|Count|Last (最後へ)||ディメンションなし|
|DCISegmentsCount|Segment Count (セグメントの数)|Count|Last (最後へ)||ディメンションなし|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count (予測マッチの数)|Count|Last (最後へ)||ディメンションなし|
|DCIPredictionsCount|Prediction Count (予測の数)|Count|Last (最後へ)||ディメンションなし|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|FailedRuns|失敗した実行|Count|合計||pipelineName、activityName、windowEnd、windowStart|
|SuccessfulRuns|成功した実行|Count|合計||pipelineName、activityName、windowEnd、windowStart|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|PipelineFailedRuns|失敗したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|PipelineSucceededRuns|成功したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|ActivityFailedRuns|失敗したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|成功したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|TriggerFailedRuns|失敗したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|TriggerSucceededRuns|成功したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|IntegrationRuntimeCpuPercentage|統合実行時の CPU 使用率|Percent|平均||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableMemory|統合実行時に使用可能なメモリ|Bytes|平均||IntegrationRuntimeName、NodeName|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs (成功したジョブ)|Count|合計|成功したジョブの数。|ディメンションなし|
|JobEndedFailure|Failed Jobs (失敗したジョブ)|Count|合計|失敗したジョブの数。|ディメンションなし|
|JobEndedCancelled|Cancelled Jobs (取り消されたジョブ)|Count|合計|取り消されたジョブの数。|ディメンションなし|
|JobAUEndedSuccess|Successful AU Time (成功した AU 時間)|Seconds|合計|成功したジョブの AU 時間の合計。|ディメンションなし|
|JobAUEndedFailure|Failed AU Time (失敗した AU 時間)|Seconds|合計|失敗したジョブの AU 時間の合計。|ディメンションなし|
|JobAUEndedCancelled|Cancelled AU Time (取り消された AU 時間)|Seconds|合計|取り消されたジョブの AU 時間の合計。|ディメンションなし|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|TotalStorage|保存量の合計|Bytes|最大値|アカウントに保存されたデータの総量。|ディメンションなし|
|DataWritten|データ書き込み量|Bytes|合計|アカウントに書き込まれたデータの総量。|ディメンションなし|
|DataRead|データ読み取り量|Bytes|合計|アカウントから読み取られたデータの総量。|ディメンションなし|
|WriteRequests|書き込み要求|Count|合計|アカウントへのデータ書き込み要求の数。|ディメンションなし|
|ReadRequests|読み取り要求|Count|合計|アカウントへのデータ読み取り要求の数。|ディメンションなし|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|平均|CPU 使用率|ディメンションなし|
|memory_percent|メモリの割合|Percent|平均|メモリの割合|ディメンションなし|
|io_consumption_percent|IO の割合|Percent|平均|IO の割合|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|storage_limit|ストレージの制限|Bytes|平均|ストレージの制限|ディメンションなし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|平均|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|平均|サーバー ログ ストレージの使用量|ディメンションなし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|平均|サーバー ログ ストレージの上限|ディメンションなし|
|active_connections|アクティブな接続の合計|Count|平均|アクティブな接続の合計|ディメンションなし|
|connections_failed|失敗した接続の合計|Count|合計|失敗した接続の合計|ディメンションなし|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|平均|CPU 使用率|ディメンションなし|
|memory_percent|メモリの割合|Percent|平均|メモリの割合|ディメンションなし|
|io_consumption_percent|IO の割合|Percent|平均|IO の割合|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|storage_limit|ストレージの制限|Bytes|平均|ストレージの制限|ディメンションなし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|平均|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|平均|サーバー ログ ストレージの使用量|ディメンションなし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|平均|サーバー ログ ストレージの上限|ディメンションなし|
|active_connections|アクティブな接続の合計|Count|合計|アクティブな接続の合計|ディメンションなし|
|connections_failed|失敗した接続の合計|Count|合計|失敗した接続の合計|ディメンションなし|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|テレメトリ メッセージ送信試行|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|d2c.telemetry.ingress.success|送信済みテレメトリ メッセージ|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|c2d.commands.egress.complete.success|完了したコマンド|Count|合計|デバイスで正常に完了した Cloud to Device コマンドの数|ディメンションなし|
|c2d.commands.egress.abandon.success|中止したコマンド|Count|合計|デバイスで中止された Cloud to Device コマンドの数|ディメンションなし|
|c2d.commands.egress.reject.success|拒否されたコマンド|Count|合計|デバイスで拒否された Cloud to Device コマンドの数|ディメンションなし|
|devices.totalDevices|合計デバイス|Count|合計|IoT Hub に登録されたデバイスの数|ディメンションなし|
|devices.connectedDevices.allProtocol|接続されているデバイス|Count|合計|IoT Hub に接続されているデバイスの数|ディメンションなし|
|d2c.telemetry.egress.success|配信されたテレメトリ メッセージ|Count|合計|メッセージがエンドポイントに正常に書き込まれた回数 (合計)|ディメンションなし|
|d2c.telemetry.egress.dropped|破棄されたメッセージ|Count|合計|配信エンドポイントが停止したために破棄されたメッセージの数|ディメンションなし|
|d2c.telemetry.egress.orphaned|孤立したメッセージ|Count|合計|フォールバック ルートを含め、すべてのルートと一致しないメッセージの数|ディメンションなし|
|d2c.telemetry.egress.invalid|無効なメッセージ|Count|合計|エンドポイントとの互換性がないために配信されなかったメッセージの数|ディメンションなし|
|d2c.telemetry.egress.fallback|フォールバック条件に一致するメッセージ|Count|合計|フォールバック エンドポイントに書き込まれたメッセージの数|ディメンションなし|
|d2c.endpoints.egress.eventHubs|Event Hub エンドポイントに配信されたメッセージ|Count|合計|メッセージが Event Hub エンドポイントに正常に書き込まれた回数|ディメンションなし|
|d2c.endpoints.latency.eventHubs|イベント ハブ エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージとイベント ハブ エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|ディメンションなし|
|d2c.endpoints.egress.serviceBusQueues|Service Bus キュー エンドポイントに配信されたメッセージ|Count|合計|メッセージが Service Bus キュー エンドポイントに正常に書き込まれた回数|ディメンションなし|
|d2c.endpoints.latency.serviceBusQueues|Service Bus キュー エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージと Service Bus キュー エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|ディメンションなし|
|d2c.endpoints.egress.serviceBusTopics|Service Bus トピック エンドポイントに配信されたメッセージ|Count|合計|メッセージが Service Bus トピック エンドポイントに正常に書き込まれた回数|ディメンションなし|
|d2c.endpoints.latency.serviceBusTopics|Service Bus トピック エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージと Service Bus トピック エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|ディメンションなし|
|d2c.endpoints.egress.builtIn.events|組み込みエンドポイント (イベント/メッセージ) に配信されたメッセージ|Count|合計|メッセージが組み込みエンドポイント (イベント/メッセージ) に正常に書き込まれた回数|ディメンションなし|
|d2c.endpoints.latency.builtIn.events|組み込みエンドポイント (イベント/メッセージ) のメッセージ待機時間|ミリ秒|平均|IoT ハブの受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信メッセージの間の平均待機時間 (ミリ秒) |ディメンションなし|
|d2c.endpoints.egress.storage|ストレージ エンドポイントに配信されたメッセージ|Count|合計|メッセージがストレージ エンドポイントに正常に書き込まれた回数|ディメンションなし|
|d2c.endpoints.latency.storage|ストレージ エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージとストレージ エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|ディメンションなし|
|d2c.endpoints.egress.storage.bytes|ストレージに書き込まれたデータ|Bytes|合計|ストレージ エンドポイントに書き込まれたデータ量 (バイト)|ディメンションなし|
|d2c.endpoints.egress.storage.blobs|ストレージに書き込まれた BLOB|Count|合計|ストレージ エンドポイントに書き込まれた BLOB の数|ディメンションなし|
|d2c.twin.read.success|成功したデバイスからのツイン読み取り|Count|合計|デバイスが開始して成功したツイン読み取りの数。|ディメンションなし|
|d2c.twin.read.failure|失敗したデバイスからのツイン読み取り|Count|合計|デバイスが開始したツイン読み取りの失敗数。|ディメンションなし|
|d2c.twin.read.size|デバイスからのツイン読み取りの応答サイズ|Bytes|平均|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|d2c.twin.update.success|成功したデバイスからのツイン更新|Count|合計|デバイスが開始して成功したツイン更新の数。|ディメンションなし|
|d2c.twin.update.failure|失敗したデバイスからのツイン更新|Count|合計|デバイスが開始して失敗したツイン更新の数。|ディメンションなし|
|d2c.twin.update.size|デバイスからのツイン更新のサイズ|Bytes|平均|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.success|成功したダイレクト メソッドの呼び出し|Count|合計|成功したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.methods.failure|失敗したダイレクト メソッドの呼び出し|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.methods.requestSize|ダイレクト メソッドの呼び出しの要求サイズ|Bytes|平均|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.responseSize|ダイレクト メソッドの呼び出しの応答サイズ|Bytes|平均|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.read.success|成功したバック エンドからのツイン読み取り|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|ディメンションなし|
|c2d.twin.read.failure|失敗したバックエンドからのツイン読み取り|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|ディメンションなし|
|c2d.twin.read.size|バック エンドからのツイン読み取りの応答サイズ|Bytes|平均|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.update.success|成功したバックエンドからのツイン更新|Count|合計|バックエンドが開始して成功したツイン更新の数。|ディメンションなし|
|c2d.twin.update.failure|失敗したバック エンドからのツイン更新|Count|合計|バックエンドが開始して失敗したツイン更新の数。|ディメンションなし|
|c2d.twin.update.size|バックエンドからのツイン更新のサイズ|Bytes|平均|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|twinQueries.success|成功したツイン クエリ|Count|合計|成功したツイン クエリの数。|ディメンションなし|
|twinQueries.failure|失敗したツイン クエリ|Count|合計|失敗したツイン クエリの数。|ディメンションなし|
|twinQueries.resultSize|ツイン クエリの結果のサイズ|Bytes|平均|成功したツイン クエリの結果の平均、最小、および最大サイズ。|ディメンションなし|
|jobs.createTwinUpdateJob.success|成功したツイン更新ジョブの作成|Count|合計|正常に作成されたツイン更新ジョブの数。|ディメンションなし|
|jobs.createTwinUpdateJob.failure|失敗したツイン更新ジョブの作成|Count|合計|作成に失敗したツイン更新ジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.success|成功したメソッド呼び出しジョブの作成|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.failure|失敗したメソッド呼び出しジョブの作成|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.listJobs.success|成功したジョブ一覧の呼び出し|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.listJobs.failure|失敗したジョブ一覧の呼び出し|Count|合計|失敗したジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.cancelJob.success|成功したジョブの取り消し|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.cancelJob.failure|失敗したジョブの取り消し|Count|合計|失敗したジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.queryJobs.success|成功したジョブ クエリ|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|ディメンションなし|
|jobs.queryJobs.failure|失敗したジョブ クエリ|Count|合計|失敗したジョブ クエリの呼び出しの数。|ディメンションなし|
|jobs.completed|完了したジョブ|Count|合計|完了したジョブの数。|ディメンションなし|
|jobs.failed|失敗したジョブ|Count|合計|失敗したジョブの数。|ディメンションなし|
|d2c.telemetry.ingress.sendThrottle|調整エラーの数|Count|合計|デバイスのスループット調整による調整エラーの数|ディメンションなし|
|dailyMessageQuotaUsed|使用されているメッセージの合計数|Count|平均|現在使用されているメッセージの合計数。 これは累積値であり、毎日 00 時 00 分 (UTC) になるとゼロにリセットされます。|ディメンションなし|
|deviceDataUsage|合計 devicedata 使用状況|Count|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|ディメンションなし|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts (登録試行回数)|Count|合計|試行されたデバイス登録の回数|ProvisioningServiceName、IotHubName、Status|
|DeviceAssignments|Devices assigned (割り当て済みデバイス数)|Count|合計|IoT Hub に割り当てられているデバイスの数|ProvisioningServiceName、IotHubName|
|AttestationAttempts|Attestation attempts (構成証明試行回数)|Count|合計|試行されたデバイス構成証明の回数|ProvisioningServiceName、Status、Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|MetadataRequests|メタデータの要求数|Count|Count|メタデータの要求数。 Cosmos DB はメタデータ コレクションをアカウントごとに保持します。これにより、コレクションやデータベースなどとそれらの構成を無料で列挙できます。|GlobalDatabaseAccountName、DatabaseName、CollectionName、Region、StatusCode|
|MongoRequestCharge|Mongo 要求の料金|Count|合計|使用された Mongo 要求の単位数|GlobalDatabaseAccountName、DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequests|Mongo Requests (Mongo 要求数)|Count|Count|実行された Mongo 要求の数|GlobalDatabaseAccountName、DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|TotalRequestUnits|合計要求単位数|Count|合計|消費された要求の単位数|GlobalDatabaseAccountName、DatabaseName、CollectionName、Region、StatusCode|
|TotalRequests|要求の合計数|Count|Count|行われた要求の数|GlobalDatabaseAccountName、DatabaseName、CollectionName、Region、StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求 (プレビュー)|Count|合計|Microsoft.EventHub の成功した要求数。 (プレビュー)|EntityName|
|ServerErrors|サーバー エラー。 (プレビュー)|Count|合計|Microsoft.EventHub のサーバー エラー数。 (プレビュー)|EntityName|
|UserErrors|ユーザー エラー数。 (プレビュー)|Count|合計|Microsoft.EventHub のユーザー エラー数。 (プレビュー)|EntityName|
|QuotaExceededErrors|クォータ超過エラー数。 (プレビュー)|Count|合計|Microsoft.EventHub のクォータ超過エラー数。 (プレビュー)|EntityName|
|ThrottledRequests|スロットルされた要求数。 (プレビュー)|Count|合計|Microsoft.EventHub のスロットルされた要求数。 (プレビュー)|EntityName|
|IncomingRequests|受信要求 (プレビュー)|Count|合計|Microsoft.EventHub の受信要求数。 (プレビュー)|EntityName|
|IncomingMessages|受信メッセージ (プレビュー)|Count|合計|Microsoft.EventHub の受信メッセージ数。 (プレビュー)|EntityName|
|OutgoingMessages|送信メッセージ (プレビュー)|Count|合計|Microsoft.EventHub の送信メッセージ数。 (プレビュー)|EntityName|
|IncomingBytes|着信バイト数。 (プレビュー)|Bytes|合計|Microsoft.EventHub の受信バイト数。 (プレビュー)|EntityName|
|OutgoingBytes|発信バイト数。 (プレビュー)|Bytes|合計|Microsoft.EventHub の発信バイト数。 (プレビュー)|EntityName|
|ActiveConnections|ActiveConnections (プレビュー)|Count|平均|Microsoft.EventHub のアクティブな接続の合計数。 (プレビュー)|ディメンションなし|
|ConnectionsOpened|開かれている接続数。 (プレビュー)|Count|平均|Microsoft.EventHub の開かれている接続数。 (プレビュー)|EntityName|
|ConnectionsClosed|切断された接続数。 (プレビュー)|Count|平均|Microsoft.EventHub の切断された接続数。 (プレビュー)|EntityName|
|CaptureBacklog|バックログのキャプチャ。 (プレビュー)|Count|合計|Microsoft.EventHub のバックログをキャプチャします。 (プレビュー)|EntityName|
|CapturedMessages|キャプチャされたメッセージ数。 (プレビュー)|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。 (プレビュー)|EntityName|
|CapturedBytes|キャプチャされたバイト数。 (プレビュー)|Bytes|合計|Microsoft.EventHub のキャプチャされたバイト数。 (プレビュー)|EntityName|
|サイズ|サイズ (プレビュー)|Bytes|平均|EventHub のサイズ (バイト単位)。 (プレビュー)|EntityName|
|INREQS|受信要求|Count|合計|名前空間に対して受信した送信要求の合計数|ディメンションなし|
|SUCCREQ|成功した要求|Count|合計|名前空間の成功した要求の総数|ディメンションなし|
|FAILREQ|失敗した要求|Count|合計|名前空間の失敗した要求の総数|ディメンションなし|
|SVRBSY|サーバー ビジー エラー|Count|合計|名前空間のサーバー ビジー エラーの総数|ディメンションなし|
|INTERR|内部サーバー エラー|Count|合計|名前空間の内部サーバー エラーの総数|ディメンションなし|
|MISCERR|その他のエラー|Count|合計|名前空間の失敗した要求の総数|ディメンションなし|
|INMSGS|受信メッセージ (非推奨)|Count|合計|名前空間の受信メッセージの総数。 このメトリックは非推奨です。 代わりに、"受信メッセージ" メトリックを使ってください。|ディメンションなし|
|EHINMSGS|受信メッセージ|Count|合計|名前空間の受信メッセージの総数|ディメンションなし|
|OUTMSGS|送信メッセージ (非推奨)|Count|合計|名前空間の送信メッセージの総数。 このメトリックは非推奨です。 代わりに、"送信メッセージ" メトリックを使ってください。|ディメンションなし|
|EHOUTMSGS|送信メッセージ|Count|合計|名前空間の送信メッセージの総数|ディメンションなし|
|EHINMBS|受信バイト数 (非推奨)|Bytes|合計|名前空間の Event Hub 受信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"着信バイト数" メトリックを使ってください。|ディメンションなし|
|EHINBYTES|着信バイト数|Bytes|合計|名前空間のEvent Hub 受信メッセージのスループット|ディメンションなし|
|EHOUTMBS|送信バイト数 (非推奨)|Bytes|合計|名前空間の Event Hub 送信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"発信バイト数" メトリックを使ってください。|ディメンションなし|
|EHOUTBYTES|発信バイト数|Bytes|合計|名前空間の Event Hub 送信メッセージのスループット|ディメンションなし|
|EHABL|アーカイブ バックログ メッセージ|Count|合計|名前空間のバックログ内の Event Hub アーカイブ メッセージ|ディメンションなし|
|EHAMSGS|アーカイブ メッセージ|Count|合計|名前空間内の Event Hub アーカイブ メッセージ|ディメンションなし|
|EHAMBS|アーカイブ メッセージ スループット|Bytes|合計|名前空間内の HubEvent アーカイブ メッセージのスループット|ディメンションなし|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|GatewayRequests|ゲートウェイ要求数|Count|合計|ゲートウェイ要求の数|ClusterDnsName、HttpStatus|
|CategorizedGatewayRequests|カテゴリ別のゲートウェイ要求数|Count|合計|カテゴリ別のゲートウェイ要求の数 (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName、HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|ObservedMetricValue|実際のメトリック値|Count|平均|実行時に自動スケールによって計算された値|MetricTriggerSource|
|MetricThreshold|メトリックのしきい値|Count|平均|自動スケールの実行時に構成されていた自動スケールのしきい値。|MetricTriggerRule|
|ObservedCapacity|実際の容量|Count|平均|実行時に自動スケールに報告された容量。|ディメンションなし|
|ScaleActionsInitiated|スケール アクション開始|Count|合計|スケール操作の指示。|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|ServiceApiHit|サービス API ヒット数の合計|Count|Count|サービス API ヒット数の合計|ActivityType、ActivityName|
|ServiceApiLatency|サービス API の全体的な待機時間|ミリ秒|平均|サービス API 要求の全体的な待機時間|ActivityType、ActivityName、StatusCode|
|ServiceApiResult|サービス API 結果数の合計|Count|Count|サービス API 結果数の合計|ActivityType、ActivityName、StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Latency|Latency|ミリ秒|平均|API 呼び出しの期間|OperationName、OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|RunsStarted|開始した実行|Count|合計|開始したワークフロー実行の数。|ディメンションなし|
|RunsCompleted|完了した実行|Count|合計|完了したワークフロー実行の数。|ディメンションなし|
|RunsSucceeded|成功した実行|Count|合計|成功したワークフロー実行の数。|ディメンションなし|
|RunsFailed|失敗した実行|Count|合計|失敗したワークフロー実行の数。|ディメンションなし|
|RunsCancelled|キャンセルされた実行|Count|合計|キャンセルされたワークフロー実行の数。|ディメンションなし|
|RunLatency|実行の待機時間|Seconds|平均|完了したワークフロー実行の待機時間。|ディメンションなし|
|RunSuccessLatency|成功した実行の待機時間|Seconds|平均|成功したワークフロー実行の待機時間。|ディメンションなし|
|RunThrottledEvents|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|ディメンションなし|
|RunFailurePercentage|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|ディメンションなし|
|ActionsStarted|開始したアクション |Count|合計|開始したワークフロー アクションの数。|ディメンションなし|
|ActionsCompleted|完了したアクション |Count|合計|完了したワークフロー アクションの数。|ディメンションなし|
|ActionsSucceeded|成功したアクション |Count|合計|成功したワークフロー アクションの数。|ディメンションなし|
|ActionsFailed|失敗したアクション|Count|合計|失敗したワークフロー アクションの数。|ディメンションなし|
|ActionsSkipped|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|ディメンションなし|
|ActionLatency|アクションの待機時間 |Seconds|平均|完了したワークフロー アクションの待機時間。|ディメンションなし|
|ActionSuccessLatency|成功したアクションの待機時間 |Seconds|平均|成功したワークフロー アクションの待機時間。|ディメンションなし|
|ActionThrottledEvents|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|ディメンションなし|
|TriggersStarted|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|ディメンションなし|
|TriggersCompleted|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|ディメンションなし|
|TriggersSucceeded|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|ディメンションなし|
|TriggersFailed|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|ディメンションなし|
|TriggersSkipped|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|ディメンションなし|
|TriggersFired|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|ディメンションなし|
|TriggerLatency|トリガーの待機時間 |Seconds|平均|完了したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerFireLatency|起動されたトリガーの待機時間 |Seconds|平均|起動されたワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerSuccessLatency|成功したトリガーの待機時間 |Seconds|平均|成功したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerThrottledEvents|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|ディメンションなし|
|BillableActionExecutions|課金対象のアクションの実行|Count|合計|課金されるワークフロー アクションの実行数。|ディメンションなし|
|BillableTriggerExecutions|課金対象のトリガーの実行|Count|合計|課金されるワークフロー トリガーの実行数。|ディメンションなし|
|TotalBillableExecutions|課金対象の実行の合計数|Count|合計|課金されるワークフローの実行数。|ディメンションなし|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|VipAvailability|VIP 可用性|Count|平均|プローブの結果に基づく、VIP エンドポイントの可用性|VipAddress、VipPort|
|DipAvailability|DIP 可用性|Count|平均|プローブの結果に基づく、DIP エンドポイントの可用性|ProtocolType、DipPort、VipAddress、VipPort、DipAddress|
|ByteCount|Byte Count (バイト数)|Count|合計|期間内に送信された合計バイト数|VipAddress、VipPort、Direction|
|PacketCount|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|VipAddress、VipPort、Direction|
|SYNCount|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|VipAddress、VipPort、Direction|
|SnatConnectionCount|SNAT Connection Count (SNAT 接続数)|Count|合計|期間内に作成された新しい SNAT 接続の合計数|VipAddress、DipAddress、ConnectionState|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|QueryVolume|クエリ数|Count|合計|DNS ゾーンで処理されたクエリの数|ディメンションなし|
|RecordSetCount|レコード セット数|Count|最大値|DNS ゾーンのレコード セットの数|ディメンションなし|
|RecordSetCapacityUtilization|レコード セットの容量使用率|Percent|最大値|DNS ゾーンで使用されるレコード セットの容量の割合|ディメンションなし|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS 受信パケット数|CountPerSecond|最大値|DDoS 受信パケット数|ディメンションなし|
|PacketsDroppedDDoS|DDoS 受信パケット破棄数|CountPerSecond|最大値|DDoS 受信パケット破棄数|ディメンションなし|
|PacketsForwardedDDoS|DDoS 受信パケット転送数|CountPerSecond|最大値|DDoS 受信パケット転送数|ディメンションなし|
|TCPPacketsInDDoS|DDoS 受信 TCP パケット数|CountPerSecond|最大値|DDoS 受信 TCP パケット数|ディメンションなし|
|TCPPacketsDroppedDDoS|DDoS 受信 TCP パケット破棄数|CountPerSecond|最大値|DDoS 受信 TCP パケット破棄数|ディメンションなし|
|TCPPacketsForwardedDDoS|DDoS 受信 TCP パケット転送数|CountPerSecond|最大値|DDoS 受信 TCP パケット転送数|ディメンションなし|
|UDPPacketsInDDoS|DDoS 受信 UDP パケット数|CountPerSecond|最大値|DDoS 受信 UDP パケット数|ディメンションなし|
|UDPPacketsDroppedDDoS|DDoS 受信 UDP パケット破棄数|CountPerSecond|最大値|DDoS 受信 UDP パケット破棄数|ディメンションなし|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|CountPerSecond|最大値|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|ディメンションなし|
|BytesInDDoS|DDoS 受信バイト数|BytesPerSecond|最大値|DDoS 受信バイト数|ディメンションなし|
|BytesDroppedDDoS|DDoS 受信バイト破棄数|BytesPerSecond|最大値|DDoS 受信バイト破棄数|ディメンションなし|
|BytesForwardedDDoS|DDoS 受信バイト転送数|BytesPerSecond|最大値|DDoS 受信バイト転送数|ディメンションなし|
|TCPBytesInDDoS|DDoS 受信 TCP バイト数|BytesPerSecond|最大値|DDoS 受信 TCP バイト数|ディメンションなし|
|TCPBytesDroppedDDoS|DDoS 受信 TCP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 TCP バイト破棄数|ディメンションなし|
|TCPBytesForwardedDDoS|DDoS 受信 TCP バイト転送数|BytesPerSecond|最大値|DDoS 受信 TCP バイト転送数|ディメンションなし|
|UDPBytesInDDoS|DDoS 受信 UDP バイト数|BytesPerSecond|最大値|DDoS 受信 UDP バイト数|ディメンションなし|
|UDPBytesDroppedDDoS|DDoS 受信 UDP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 UDP バイト破棄数|ディメンションなし|
|UDPBytesForwardedDDoS|DDoS 受信 UDP バイト転送数|BytesPerSecond|最大値|DDoS 受信 UDP バイト転送数|ディメンションなし|
|IfUnderDDoSAttack|DDoS 攻撃中かどうか|Count|最大値|DDoS 攻撃中かどうか|ディメンションなし|
|DDoSTriggerTCPPackets|DDoS 軽減をトリガーする受信 TCP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 TCP パケット数|ディメンションなし|
|DDoSTriggerUDPPackets|DDoS 軽減をトリガーする受信 UDP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 UDP パケット数|ディメンションなし|
|DDoSTriggerSYNPackets|DDoS 軽減をトリガーする受信 SYN パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 SYN パケット数|ディメンションなし|
|VipAvailability|可用性|Count|平均|期間内の IP アドレスの平均可用性|ポート|
|ByteCount|Byte Count (バイト数)|Count|合計|期間内に送信された合計バイト数|Port、Direction|
|PacketCount|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|Port、Direction|
|SynCount|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|Port、Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Throughput|Throughput|BytesPerSecond|合計|Application Gateway が処理した 1 秒あたりのバイト数|ディメンションなし|
|UnhealthyHostCount|異常なホストの数|Count|平均|異常なバックエンド ホストの数。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常/異常なホストを表示できます。|BackendSettingsPool|
|HealthyHostCount|正常なホストの数|Count|平均|正常なバックエンド ホストの数。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常/異常なホストを表示できます。|BackendSettingsPool。 |
|TotalRequests|要求の合計数|Count|合計|Application Gateway が処理した成功した要求の数|BackendSettingsPool|
|FailedRequests|失敗した要求|Count|合計|Application Gateway が処理した失敗した要求の数|BackendSettingsPool|
|ResponseStatus|応答の状態|Count|合計|Application Gateway によって返された HTTP 応答の状態。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。|HttpStatusGroup|
|CurrentConnections|現在の接続数|Count|合計|Application Gateway で確立された現在の接続の数|ディメンションなし|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Tunnel Bandwidth (トンネル帯域幅)|BytesPerSecond|平均|1 秒あたりの平均トンネル帯域幅バイト数|ConnectionName、RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes (トンネル エグレス バイト数)|Bytes|合計|トンネルの送信バイト数|ConnectionName、RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes (トンネル イングレス バイト数)|Bytes|合計|トンネルの受信バイト数|ConnectionName、RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets (トンネル エグレス パケット数)|Count|合計|トンネルの送信パケット数|ConnectionName、RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets (トンネル イングレス パケット数)|Count|合計|トンネルの受信パケット数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの発信パケット破棄数|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの受信パケット破棄数|ConnectionName、RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure へのイングレス ビット数|ディメンションなし|
|BitsOutPerSecond|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure からのエグレス ビット数|ディメンションなし|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned (エンドポイント別の返されたクエリ数)|Count|合計|指定された期間内に Traffic Manager エンドポイントが返された回数|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint (エンドポイント別のエンドポイント状態)|Count|最大値|エンドポイントのプローブの状態が "有効" の場合は 1、それ以外の場合は 0。|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|ProbesFailedPercent|失敗したプローブの割合|Percent|平均|失敗した接続監視プローブの割合|ディメンションなし|
|AverageRoundtripMs|Avg.ラウンド トリップ時間 (ミリ秒)|MilliSeconds|平均|ソースと接続先の間で送信された接続監視プローブのネットワークの平均ラウンド トリップ時間 (ミリ秒)|ディメンションなし|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|registration.all|登録操作|Count|合計|成功した登録操作の数 (作成、更新、クエリ、および削除)。 |ディメンションなし|
|registration.create|登録作成操作|Count|合計|成功した登録作成の数。|ディメンションなし|
|registration.update|登録更新操作|Count|合計|成功した登録更新の数。|ディメンションなし|
|registration.get|登録読み取り操作|Count|合計|成功した登録クエリの数。|ディメンションなし|
|registration.delete|登録削除操作|Count|合計|成功した登録削除の数。|ディメンションなし|
|incoming|受信メッセージ|Count|合計|成功した送信 API 呼び出しの数。 |ディメンションなし|
|incoming.scheduled|予定されていたプッシュ通知の送信|Count|合計|予定されていたプッシュ通知の取り消し|ディメンションなし|
|incoming.scheduled.cancel|予定されていたプッシュ通知の取り消し|Count|合計|予定されていたプッシュ通知の取り消し|ディメンションなし|
|scheduled.pending|予定されていた通知の保留|Count|合計|予定されていた通知の保留|ディメンションなし|
|installation.all|インストール管理操作|Count|合計|インストール管理操作|ディメンションなし|
|installation.get|インストール操作の取得|Count|合計|インストール操作の取得|ディメンションなし|
|installation.upsert|インストール操作の作成または更新|Count|合計|インストール操作を作成または更新します|ディメンションなし|
|installation.patch|インストール操作のパッチ|Count|合計|インストール操作をパッチします|ディメンションなし|
|installation.delete|インストール操作の削除|Count|合計|インストール操作を削除します|ディメンションなし|
|outgoing.allpns.success|正常な通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.allpns.invalidpayload|ペイロード エラー|Count|合計|PNS が不良ペイロード エラーを返しために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.pnserror|外部通知システム エラー|Count|合計|PNS との通信に問題があったために失敗したプッシュの数 (認証の問題は除きます)。|ディメンションなし|
|outgoing.allpns.channelerror|チャネル エラー|Count|合計|チャネルが適切に調整されていないアプリまたは有効期限が切れたアプリに関連付けられていたために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.badorexpiredchannel|適切または期限切れのチャネル エラー|Count|合計|登録内のチャネル/トークン/登録 ID が期限切れまたは無効なために失敗したプッシュの数。|ディメンションなし|
|outgoing.wns.success|WNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.wns.invalidcredentials|WNS 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数  (Windows Live は資格情報を認識しません)。|ディメンションなし|
|outgoing.wns.badchannel|WNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (WNS ステータス: 404 - 見つかりません)。|ディメンションなし|
|outgoing.wns.expiredchannel|WNS 期限切れチャネル エラー|Count|合計|ChannelURI の有効期限が切れているために失敗したプッシュの数 (WNS ステータス: 410 - 削除)。|ディメンションなし|
|outgoing.wns.throttled|WNS 調整済み通知|Count|合計|WNS がこのアプリを調整しているために失敗したプッシュの数 (WNS ステータス: 406 - 受信不可)。|ディメンションなし|
|outgoing.wns.tokenproviderunreachable|WNS 承認エラー (到達不可)|Count|合計|Windows Live に到達できません。|ディメンションなし|
|outgoing.wns.invalidtoken|WNS 承認エラー (無効なトークン)|Count|合計|WNS に提供されたトークンは無効です (WNS ステータス: 401 - 許可されていません)。|ディメンションなし|
|outgoing.wns.wrongtoken|WNS 承認エラー (間違ったトークン)|Count|合計|WNS に提供されたトークンは有効ですが、別のアプリケーション用です (WNS ステータス: 403 - 禁止されています)。 これは、登録の ChannelURI が別のアプリケーションに関連付けられている場合に発生します。 クライアント アプリに関連付けられているアプリの資格情報が通知ハブ内にあることを確認します。|ディメンションなし|
|outgoing.wns.invalidnotificationformat|WNS 無効通知形式|Count|合計|通知形式が無効です (WNS ステータス: 400)。 WNS ではすべての無効なペイロードを拒否するわけではないことに注意してください。|ディメンションなし|
|outgoing.wns.invalidnotificationsize|WNS 無効通知サイズ エラー|Count|合計|通知ペイロードが大きすぎます (WNS ステータス: 413)。|ディメンションなし|
|outgoing.wns.channelthrottled|WNS 調整済みチャネル|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-NotificationStatus:channelThrottled)。|ディメンションなし|
|outgoing.wns.channeldisconnected|WNS チャネル切断|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-DeviceConnectionStatus: disconnected)。|ディメンションなし|
|outgoing.wns.dropped|WNS ドロップ通知|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected)。|ディメンションなし|
|outgoing.wns.pnserror|WNS エラー|Count|合計|WNS との通信エラーにより、通知が配信されません。|ディメンションなし|
|outgoing.wns.authenticationerror|WNS 認証エラー|Count|合計|資格情報無効または間違ったトークンによる Windows Live との通信エラーのため、通知が配信されません。|ディメンションなし|
|outgoing.apns.success|APNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.apns.invalidcredentials|APNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |ディメンションなし|
|outgoing.apns.badchannel|APNS 不良チャネル エラー|Count|合計|トークンが無効なために失敗したプッシュの数 (APNS 状態コード: 8)。|ディメンションなし|
|outgoing.apns.expiredchannel|APNS 期限切れチャネル エラー|Count|合計|APNS フィードバック チャネルによって無効化されたトークンの数。|ディメンションなし|
|outgoing.apns.invalidnotificationsize|APNS 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (APNS 状態コード: 7)。|ディメンションなし|
|outgoing.apns.pnserror|APNS エラー|Count|合計|APNS との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.gcm.success|GCM 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.gcm.invalidcredentials|GCM 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |ディメンションなし|
|outgoing.gcm.badchannel|GCM 不良チャネル エラー|Count|合計|登録内の registrationId が認識されなかったために失敗したプッシュの数 (GCM 結果: 無効な登録)。|ディメンションなし|
|outgoing.gcm.expiredchannel|GCM 期限切れチャネル エラー|Count|合計|登録内の registrationId が有効期限切れだったために失敗したプッシュの数 (GCM 結果: NotRegistered)。|ディメンションなし|
|outgoing.gcm.throttled|GCM 調整済み通知|Count|合計|GCM がこのアプリを調整したために失敗したプッシュの数 (GCM 状態コード: 501-599、または結果: 利用不可)。|ディメンションなし|
|outgoing.gcm.invalidnotificationformat|GCM 無効通知形式|Count|合計|ペイロードの形式が正しくなかったために失敗したプッシュの数 (GCM 結果: InvalidDataKey または InvalidTtl)。|ディメンションなし|
|outgoing.gcm.invalidnotificationsize|GCM 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (GCM 結果: MessageTooBig)。|ディメンションなし|
|outgoing.gcm.wrongchannel|GCM 誤チャネル エラー|Count|合計|登録内の registrationId が現在のアプリに関連付けられていないために失敗したプッシュの数 (GCM 結果: InvalidPackageName)。|ディメンションなし|
|outgoing.gcm.pnserror|GCM エラー|Count|合計|GCM との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.gcm.authenticationerror|GCM 認証エラー|Count|合計|PNS が提供された資格情報を受け入れなかったかったために失敗したプッシュの数。資格情報がブロックされているか、アプリでSenderId が正しく構成されていません (GCM 結果: MismatchedSenderId)。|ディメンションなし|
|outgoing.mpns.success|MPNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.mpns.invalidcredentials|MPNS 無効な資格情報|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |ディメンションなし|
|outgoing.mpns.badchannel|MPNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (MPNS ステータス: 404 - 見つかりません)。|ディメンションなし|
|outgoing.mpns.throttled|MPNS 調整済み通知|Count|合計|MPNS がこのアプリを調整しているために失敗したプッシュの数 (WNS MPNS: 406 - 受信不可)。|ディメンションなし|
|outgoing.mpns.invalidnotificationformat|MPNS 無効通知形式|Count|合計|通知のペイロードが長すぎたために失敗したプッシュの数。|ディメンションなし|
|outgoing.mpns.channeldisconnected|MPNS チャネル切断|Count|合計|登録の ChannelURI が切断されたために失敗したプッシュの数 (MPNS ステータス: 412 - 見つかりません)。|ディメンションなし|
|outgoing.mpns.dropped|MPNS ドロップ通知|Count|合計|MPNS によって破棄されたプッシュの数 (MPNS 応答ヘッダー: X-NotificationStatus: QueueFull または Suppressed)。|ディメンションなし|
|outgoing.mpns.pnserror|MPNS エラー|Count|合計|MPNS との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.mpns.authenticationerror|MPNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |ディメンションなし|
|notificationhub.pushes|All Outgoing Notifications (すべての送信通知)|Count|合計|Notification Hub のすべての送信通知|ディメンションなし|
|incoming.all.requests|すべての受信要求|Count|合計|Notification Hub の受信要求の合計数|ディメンションなし|
|incoming.all.failedrequests|失敗したすべての受信要求|Count|合計|Notification Hub の失敗した受信要求の合計数|ディメンションなし|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces
(パブリック プレビュー)

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
Average_% Free Inodes|% Free Inodes|Count|平均|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Free Space|% Free Space|Count|平均|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Used Inodes|% Used Inodes|Count|平均|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Used Space|% Used Space|Count|平均|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Disk Read Bytes/sec|Disk Read Bytes/sec |Count|平均|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Disk Reads/sec|Disk Reads/sec |Count|平均|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Disk Transfers/sec|Disk Transfers/sec|Count|平均|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Count|平均|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Disk Writes/sec|Disk Writes/sec|Count|平均|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Free Megabytes|Free Megabytes|Count|平均|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Count|平均|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Available Memory|% Available Memory|Count|平均|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Available Swap Space|% Available Swap Space|Count|平均|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Used Memory|% Used Memory|Count|平均|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Used Swap Space|% Used Swap Space|Count|平均|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Available MBytes Memory|Available MBytes Memory|Count|平均|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Available MBytes Swap|Available MBytes Swap|Count|平均|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Page Reads/sec|Page Reads/sec|Count|平均|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Page Writes/sec|Page Writes/sec|Count|平均|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Pages/sec|Pages/sec|Count|平均|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Used MBytes Swap Space|Available MBytes Memory|Count|平均|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Used Memory MBytes|Available MBytes Swap|Count|平均|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Bytes Transmitted|Total Bytes Transmitted|Count|平均|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Bytes Received|Total Bytes Received|Count|平均|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Bytes|Total Bytes|Count|平均|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Packets Transmitted|Total Packets Transmitted|Count|平均|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Packets Received|Total Packets Received|Count|平均|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Rx Errors|Total Rx Errors|Count|平均|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Tx Errors|Total Tx Errors|Count|平均|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Total Collisions|Total Collisions|Count|平均|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Avg。 Disk sec/Read|Avg.Disk sec/Read|Count|平均|Average_Avg。 Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Avg。 Disk sec/Transfer|Avg.Disk sec/Transfer|Count|平均|Average_Avg。 Disk sec/Transfer|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Avg。 Disk sec/Write|Avg.Disk sec/Write|Count|平均|Average_Avg。 Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Count|平均|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Pct Privileged Time|Pct Privileged Time|Count|平均|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Pct User Time|Pct User Time|Count|平均|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Used Memory kBytes|Used Memory kBytes|Count|平均|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Virtual Shared Memory|Virtual Shared Memory|Count|平均|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% DPC Time|% DPC Time|Count|平均|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Idle Time|% Idle Time|Count|平均|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Interrupt Time|% Interrupt Time|Count|平均|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% IO Wait Time|% IO Wait Time|Count|平均|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Nice Time|% Nice Time|Count|平均|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Privileged Time|% Privileged Time|Count|平均|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Processor Time|% Processor Time|Count|平均|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% User Time|% User Time|Count|平均|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Free Physical Memory|Free Physical Memory|Count|平均|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Free Space in Paging Files|Free Space in Paging Files|Count|平均|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Free Virtual Memory|Free Virtual Memory|Count|平均|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Processes|処理|Count|平均|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Size Stored In Paging Files|Size Stored In Paging Files|Count|平均|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Uptime|Uptime|Count|平均|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Users|ユーザー|Count|平均|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Current Disk Queue Length|現在のディスク キューの長さ|Count|平均|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Available MBytes|Available MBytes|Count|平均|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_% Committed Bytes In Use|% Committed Bytes In Use|Count|平均|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Bytes Received/sec|Bytes Received/sec|Count|平均|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Bytes Sent/sec|Bytes Sent/sec|Count|平均|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Average_Bytes Total/sec|Bytes Total/sec|Count|平均|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
Heartbeat|Heartbeat|Count|平均|Heartbeat|Computer、OSType、Version、SourceComputerId|
アップデート|アップデート|Count|平均|アップデート|Computer、Product、Classification、UpdateState、Optional、Approved|
Event|Event|Count|平均|Event|Source、EventLog、Computer、EventCategory、EventLevel、EventLevelName、EventID|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|QueryDuration|クエリ実行時間|ミリ秒|平均|最後の間隔における DAX クエリの実行時間|ディメンションなし|
|QueryPoolJobQueueLength|スレッド: クエリ プール ジョブ キューの長さ|Count|平均|クエリ スレッド プールのキューに登録されているジョブの数。|ディメンションなし|
|qpu_high_utilization_metric|QPU High Utilization (QPU の高使用率)|Count|合計|過去 1 分間の QPU の高使用率、QPU 使用率が高い場合は 1、それ以外の場合は 0|ディメンションなし|
|memory_metric|メモリ|バイト|平均|メモリ。 範囲は A1 で 0 から 3 GB、A2 で 0 から 5 GB、A3 で 0 から 10 GB、A4 で 0 から 25 GB、A5 で 0 から 50 GB、A6 で 0 から 100 GB|ディメンションなし|
|memory_thrashing_metric|メモリ スラッシング|Percent|平均|平均的なメモリ スラッシング。|ディメンションなし|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|合計|Microsoft.Relay の正常な ListenerConnections。|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|合計|Microsoft.Relay の ListenerConnections での ClientError。|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|合計|Microsoft.Relay の ListenerConnections での ServerError。|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|合計|Microsoft.Relay の正常な SenderConnections。|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|合計|Microsoft.Relay の SenderConnections での ClientError。|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|合計|Microsoft.Relay の SenderConnections での ServerError。|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|合計|Microsoft.Relay の ListenerConnections の合計。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|合計|Microsoft.Relay の SenderConnections 要求の合計。|EntityName|
|ActiveConnections|ActiveConnections|Count|合計|Microsoft.Relay の ActiveConnections の合計。|EntityName|
|ActiveListeners|ActiveListeners|Count|合計|Microsoft.Relay の ActiveListeners の合計。|EntityName|
|BytesTransferred|BytesTransferred|Count|合計|Microsoft.Relay の BytesTransferred の合計。|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|合計|Microsoft.Relay の ListenerDisconnects の合計。|EntityName|
|SenderDisconnects|SenderDisconnects|Count|合計|Microsoft.Relay の SenderDisconnects の合計。|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|SearchLatency|検索の待機時間|Seconds|平均|検索サービスの平均検索待機時間|ディメンションなし|
|SearchQueriesPerSecond|1 秒あたりの検索クエリ数|CountPerSecond|平均|検索サービスの 1 秒あたりの検索クエリ数|ディメンションなし|
|ThrottledSearchQueriesPercentage|スロットルされた検索クエリの割合|Percent|平均|検索サービスのスロットルされた検索クエリの割合|ディメンションなし|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求 (プレビュー)|Count|合計|名前空間の成功した要求の総数 (プレビュー)|EntityName、 |
|ServerErrors|サーバー エラー。 (プレビュー)|Count|合計|Microsoft.ServiceBus のサーバー エラー数。 (プレビュー)|EntityName、 |
|UserErrors|ユーザー エラー数。 (プレビュー)|Count|合計|Microsoft.ServiceBus のユーザー エラー数。 (プレビュー)|EntityName、 |
|ThrottledRequests|スロットルされた要求数。 (プレビュー)|Count|合計|Microsoft.ServiceBus のスロットルされた要求数。 (プレビュー)|EntityName、 |
|IncomingRequests|受信要求 (プレビュー)|Count|合計|Microsoft.ServiceBus の受信要求数。 (プレビュー)|EntityName|
|IncomingMessages|受信メッセージ (プレビュー)|Count|合計|Microsoft.ServiceBus の受信メッセージ数。 (プレビュー)|EntityName|
|OutgoingMessages|送信メッセージ (プレビュー)|Count|合計|Microsoft.ServiceBus の送信メッセージ数。 (プレビュー)|EntityName|
|ActiveConnections|ActiveConnections (プレビュー)|Count|合計|Microsoft.ServiceBus のアクティブな接続の合計数。 (プレビュー)|ディメンションなし|
|サイズ|サイズ (プレビュー)|Bytes|平均|キュー/トピックのサイズ (バイト単位)。 (プレビュー)|EntityName|
|メッセージ|キュー/トピック内のメッセージの数。 (プレビュー)|Count|平均|キュー/トピック内のメッセージの数。 (プレビュー)|EntityName|
|ActiveMessages|キュー/トピック内のアクティブなメッセージの数。 (プレビュー)|Count|平均|キュー/トピック内のアクティブなメッセージの数。 (プレビュー)|EntityName|
|CPUXNS|名前空間あたりの CPU 使用率|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック|ディメンションなし|
|WSXNS|名前空間あたりのメモリ サイズの使用量|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック|ディメンションなし|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count (接続数)|Count|最大値|ユーザー接続の量。|ディメンションなし|
|ConnectionCountPerSecond|Connection Count Per Second (1 秒あたりの接続数)|CountPerSecond|平均|1 秒あたりの平均接続数。|ディメンションなし|
|MessageCount|メッセージ数|Count|最大値|1 か月のメッセージの総量|ディメンションなし|
|MessageCountPerSecond|Message Count Per Second (1 秒あたりのメッセージ数)|CountPerSecond|平均|平均メッセージ数|ディメンションなし|
|MessageUsed|Message Used (メッセージの使用率)|Percent|最大値|1 か月に使用されたメッセージの割合|ディメンションなし|
|ConnectionUsed|Connection Used (接続の使用率)|Percent|最大値|使用された接続の割合|ディメンションなし|
|UserErrors|User Errors (ユーザー エラー)|Percent|最大値|ユーザー エラーの割合|ディメンションなし|
|SystemErrors|System Errors (システム エラー)|Percent|最大値|システム エラーの割合|ディメンションなし|
|SystemLoad|System Load (システムの負荷)|Percent|最大値|システムの負荷の割合|ディメンションなし|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|平均|CPU の割合|ディメンションなし|
|physical_data_read_percent|データ IO の割合|Percent|平均|データ IO の割合|ディメンションなし|
|log_write_percent|ログ IO の割合|Percent|平均|ログ IO の割合|ディメンションなし|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|ディメンションなし|
|storage|合計データベース サイズ|Bytes|最大値|合計データベース サイズ|ディメンションなし|
|connection_successful|成功した接続|Count|合計|成功した接続|ディメンションなし|
|connection_failed|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|blocked_by_firewall|ファイアウォールによってブロックされる|Count|合計|ファイアウォールによってブロックされる|ディメンションなし|
|deadlock|デッドロック|Count|合計|デッドロック|ディメンションなし|
|storage_percent|データベース サイズの割合|Percent|最大値|データベース サイズの割合|ディメンションなし|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|平均|インメモリ OLTP ストレージの割合|ディメンションなし|
|workers_percent|ワーカーの割合|Percent|平均|ワーカーの割合|ディメンションなし|
|sessions_percent|セッションの割合|Percent|平均|セッションの割合|ディメンションなし|
|dtu_limit|DTU の上限|Count|平均|DTU の上限|ディメンションなし|
|dtu_used|使用された DTU|Count|平均|使用された DTU|ディメンションなし|
|dwu_limit|DWU 上限|Count|最大値|DWU 上限|ディメンションなし|
|dwu_consumption_percent|DWU の割合|Percent|最大値|DWU の割合|ディメンションなし|
|dwu_used|使用済み DWU|Count|最大値|使用済み DWU|ディメンションなし|
|dw_cpu_percent|DW ノード レベルの CPU の割合|Percent|平均|DW ノード レベルの CPU の割合|DwLogicalNodeId|
|dw_physical_data_read_percent|DW ノード レベルのデータ IO の割合|Percent|平均|DW ノード レベルのデータ IO の割合|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|平均|CPU の割合|ディメンションなし|
|physical_data_read_percent|データ IO の割合|Percent|平均|データ IO の割合|ディメンションなし|
|log_write_percent|ログ IO の割合|Percent|平均|ログ IO の割合|ディメンションなし|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|workers_percent|ワーカーの割合|Percent|平均|ワーカーの割合|ディメンションなし|
|sessions_percent|セッションの割合|Percent|平均|セッションの割合|ディメンションなし|
|eDTU_limit|eDTU 制限|Count|平均|eDTU 制限|ディメンションなし|
|storage_limit|ストレージの制限|Bytes|平均|ストレージの制限|ディメンションなし|
|eDTU_used|使用済み eDTU|Count|平均|使用済み eDTU|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|平均|インメモリ OLTP ストレージの割合|ディメンションなし|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|ElasticPoolResourceId|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity (使用済み容量)|Bytes|平均|アカウントの使用済み容量|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName|
|Ingress|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName|
|Egress|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。|GeoType、ApiName|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity (BLOB 容量)|Bytes|合計|ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。|BlobType|
|BlobCount|Blob Count (BLOB 数)|Count|合計|ストレージ アカウントの Blob service 内の BLOB の数。|BlobType|
|ContainerCount|Blob Container Count (BLOB コンテナー数)|Count|平均|ストレージ アカウントの Blob service 内のコンテナーの数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName|
|Ingress|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName|
|Egress|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。|GeoType、ApiName|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity (テーブル容量)|Bytes|平均|ストレージ アカウントの Table service によって使われているストレージの量 (バイト単位)。|ディメンションなし|
|TableCount|Table Count (テーブル数)|Count|平均|ストレージ アカウントの Table service 内のテーブルの数。|ディメンションなし|
|TableEntityCount|Table Entity Count (テーブル エンティティ数)|Count|平均|ストレージ アカウントの Table service 内のテーブル エンティティの数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName|
|Ingress|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName|
|Egress|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。|GeoType、ApiName|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity (キュー容量)|Bytes|平均|ストレージ アカウントの Queue サービスによって使われているストレージの量 (バイト単位)。|ディメンションなし|
|QueueCount|Queue Count (キュー数)|Count|平均|ストレージ アカウントの Queue サービス内のキューの数。|ディメンションなし|
|QueueMessageCount|Queue Message Count (キュー メッセージ数)|Count|平均|ストレージ アカウントの Queue サービス内のキュー メッセージの概数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName|
|Ingress|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName|
|Egress|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。|GeoType、ApiName|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|FileCapacity|File Capacity (ファイル容量)|Bytes|平均|ストレージ アカウントの File サービスによって使われているストレージの量 (バイト単位)。|ディメンションなし|
|FileCount|File Count (ファイル数)|Count|平均|ストレージ アカウントの File サービス内のファイルの数。|ディメンションなし|
|FileShareCount|File Share Count (ファイル共有数)|Count|平均|ストレージ アカウントの File サービス内のファイル共有の数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName|
|Ingress|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName|
|Egress|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。|GeoType、ApiName|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|Percent|最大値|SU % 使用率|ディメンションなし|
|InputEvents|入力イベント|Count|合計|入力イベント|ディメンションなし|
|InputEventBytes|入力イベントのバイト数|Bytes|合計|入力イベントのバイト数|ディメンションなし|
|LateInputEvents|遅延入力イベント|Count|合計|遅延入力イベント|ディメンションなし|
|OutputEvents|出力イベント|Count|合計|出力イベント|ディメンションなし|
|ConversionErrors|データ変換エラー|Count|合計|データ変換エラー|ディメンションなし|
|Errors|実行時エラー|Count|合計|実行時エラー|ディメンションなし|
|DroppedOrAdjustedEvents|順不同のイベント|Count|合計|順不同のイベント|ディメンションなし|
|AMLCalloutRequests|関数の要求|Count|合計|関数の要求|ディメンションなし|
|AMLCalloutFailedRequests|失敗した関数の要求|Count|合計|失敗した関数の要求|ディメンションなし|
|AMLCalloutInputEvents|関数のイベント|Count|合計|関数のイベント|ディメンションなし|
|DeserializationError|逆シリアル化の入力エラー|Count|合計|逆シリアル化の入力エラー|ディメンションなし|
|EarlyInputEvents|適用時間が受信時間よりも早いイベント。|Count|合計|適用時間が受信時間よりも早いイベント。|ディメンションなし|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られたメッセージの数|ディメンションなし|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られた無効なメッセージの数|ディメンションなし|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|Bytes|合計|すべてのイベント ソースから読み取られたバイト数|ディメンションなし|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|Bytes|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|ディメンションなし|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|ディメンションなし|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|ディメンションなし|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|平均|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|ディメンションなし|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|イベント ソースから読み取られたメッセージの数|ディメンションなし|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|イベント ソースから読み取られた無効なメッセージの数|ディメンションなし|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|Bytes|合計|イベント ソースから読み取られたバイト数|ディメンションなし|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|Bytes|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|ディメンションなし|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|ディメンションなし|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|ディメンションなし|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|平均|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|ディメンションなし|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|インスタンス|
|DiskQueueLength|ディスク キューの長さ|Count|平均|ディスク キューの長さ|インスタンス|
|HttpQueueLength|HTTP キューの長さ|Count|平均|HTTP キューの長さ|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (関数を除く)

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|インスタンス|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|Bytes|平均|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|インスタンス|
|AppConnections|接続|Count|平均|接続|インスタンス|
|Handles|ハンドルの数|Count|平均|ハンドルの数|インスタンス|
|Threads|Thread Count|Count|平均|Thread Count|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|平均|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|平均|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|平均|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|平均|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (関数)

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|Bytes|平均|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|インスタンス|
|FunctionExecutionUnits|関数の実行単位|Count|合計|関数の実行単位|インスタンス|
|FunctionExecutionCount|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|平均|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|平均|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|平均|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|平均|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|インスタンス|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|Bytes|平均|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|インスタンス|
|FunctionExecutionUnits|関数の実行単位|Count|合計|関数の実行単位|インスタンス|
|FunctionExecutionCount|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|AppConnections|接続|Count|平均|接続|インスタンス|
|Handles|ハンドルの数|Count|平均|ハンドルの数|インスタンス|
|Threads|Thread Count|Count|平均|Thread Count|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|平均|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|平均|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|平均|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|平均|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|インスタンス|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|インスタンス|
|DiskQueueLength|ディスク キューの長さ|Count|平均|ディスク キューの長さ|インスタンス|
|HttpQueueLength|HTTP キューの長さ|Count|平均|HTTP キューの長さ|インスタンス|
|ActiveRequests|アクティブな要求数|Count|合計|アクティブな要求数|インスタンス|
|TotalFrontEnds|フロント エンドの合計|Count|平均|フロント エンドの合計|ディメンションなし|
|SmallAppServicePlanInstances|S App Service プランの worker の数|Count|平均|S App Service プランの worker の数|ディメンションなし|
|MediumAppServicePlanInstances|M App Service プランの worker の数|Count|平均|M App Service プランの worker の数|ディメンションなし|
|LargeAppServicePlanInstances|L App Service プランの worker の数|Count|平均|L App Service プランの worker の数|ディメンションなし|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|メトリック|メトリックの表示名|単位|集計の種類|説明|ディメンション|
|---|---|---|---|---|---|
|WorkersTotal|worker の合計数|Count|平均|worker の合計数|ディメンションなし|
|WorkersAvailable|利用可能な worker の数|Count|平均|利用可能な worker の数|ディメンションなし|
|WorkersUsed|使用済みの worker の数|Count|平均|使用済みの worker の数|ディメンションなし|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|インスタンス|

## <a name="next-steps"></a>次の手順
* [Azure Monitor のメトリックを確認します](monitoring-overview-metrics.md)
* [メトリックでアラートを作成します](insights-receive-alert-notifications.md)
* [メトリックをストレージ、Event Hub、または Log Analytics にエクスポートします](monitoring-overview-of-diagnostic-logs.md)
