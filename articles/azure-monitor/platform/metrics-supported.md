---
title: Azure Monitor でサポートされているメトリック (リソースの種類別)
description: Azure Monitor で使用できるメトリックの一覧 (リソースの種類別) です。
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e67cd8c85d9ba85e32ef7aece402f97ab24243e5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963446"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor のサポートされるメトリック

Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 ここで示しているのは、Azure Monitor のメトリック パイプラインで現在利用できるメトリックの一覧です。 他のメトリックについては、ポータルや従来の API で使用できる場合があります。 以下の一覧には、統合された Azure Monitor メトリック パイプラインで使うことができるメトリックのみが含まれます。 メトリックは名前空間別にまとめられます。 サービスとそれらに属する名前空間の一覧については、「[Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)」を参照してください。 プログラムを使ってこれらのメトリックのクエリを実行し、アクセスするには、[2018-01-01 API バージョン](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)を使用してください。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
> 診断設定を使用してエクスポート可能なプラットフォーム メトリックの一覧については、[この記事](metrics-supported-export-diagnostic-settings.md)を参照してください。



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. 範囲は S1 で 0 ～ 100、S2 で 0 ～ 200、S4 で 0 ～ 400|ServerResourceType|
|memory_metric|メモリ|バイト|Average|メモリ。 範囲は S1 で 0 ～ 25 GB、S2 で 0 ～ 50 GB、S4 で 0 ～ 100 GB|ServerResourceType|
|private_bytes_metric|Private Bytes|バイト|Average|プライベート バイト。|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes|バイト|Average|仮想バイト。|ServerResourceType|
|TotalConnectionRequests|合計接続要求数|Count|Average|合計接続要求数。 これらは到着した接続要求です。|ServerResourceType|
|SuccessfullConnectionsPerSec|成功した接続数 (秒単位)|CountPerSecond|Average|接続が正常に完了した割合。|ServerResourceType|
|TotalConnectionFailures|合計接続失敗数|Count|Average|失敗した接続試行数の合計。|ServerResourceType|
|CurrentUserSessions|現在のユーザー セッション|Count|Average|確立された現在のユーザー セッションの数。|ServerResourceType|
|QueryPoolBusyThreads|クエリ プール ビジー スレッド|Count|Average|クエリ スレッド プールのビジー スレッドの数。|ServerResourceType|
|CommandPoolJobQueueLength|コマンド プールのジョブ キューの長さ|Count|Average|コマンド スレッド プールのキュー内のジョブの数。|ServerResourceType|
|ProcessingPoolJobQueueLength|処理プール ジョブ キューの長さ|Count|Average|処理スレッド プールのキュー内の非 I/O ジョブの数。|ServerResourceType|
|CurrentConnections|接続:現在の接続数|Count|Average|現在確立されているクライアント接続の数。|ServerResourceType|
|CleanerCurrentPrice|メモリ:クリーナーの現在の価格|Count|Average|現在のメモリ価格 ($/バイト/時刻)。標準は 1,000 です。|ServerResourceType|
|CleanerMemoryShrinkable|メモリ:クリーナーの圧縮可能なメモリ|バイト|Average|バックグラウンド クリーナーによる削除の対象となるメモリの量 (バイト単位)。|ServerResourceType|
|CleanerMemoryNonshrinkable|メモリ:クリーナーの圧縮不可能なメモリ|バイト|Average|バックグラウンド クリーナーによる削除の対象とならないメモリの量 (バイト単位)。|ServerResourceType|
|MemoryUsage|メモリ:メモリ使用量|バイト|Average|クリーナー メモリの価格の計算で使用されるサーバー プロセスのメモリ使用量。 Process\PrivateBytes カウンターの値に、メモリがマップされたデータのサイズを加えた値と等しくなります。xVelocity インメモリ分析エンジン (VertiPaq) によって xVelocity エンジンのメモリの制限を超えてマップされた (割り当てられた) メモリは無視されます。|ServerResourceType|
|MemoryLimitHard|メモリ:メモリの制限 - ハード|バイト|Average|構成ファイルに指定されているハードのメモリの制限。|ServerResourceType|
|MemoryLimitHigh|メモリ:メモリの制限 - 高|バイト|Average|構成ファイルに指定されているメモリの上限。|ServerResourceType|
|MemoryLimitLow|メモリ:メモリの制限 - 低|バイト|Average|構成ファイルに指定されているメモリの下限。|ServerResourceType|
|MemoryLimitVertiPaq|メモリ:メモリの制限 - VertiPaq|バイト|Average|構成ファイルに指定されているインメモリの制限。|ServerResourceType|
|Quota|メモリ:Quota|バイト|Average|現在のメモリ クォータ (バイト単位)。 メモリ クォータはメモリ付与またはメモリ予約とも呼ばれます。|ServerResourceType|
|QuotaBlocked|メモリ:ブロックされているクォータ|Count|Average|他のメモリ クォータが解放されるまでブロックされている現在のクォータ要求の数。|ServerResourceType|
|VertiPaqNonpaged|メモリ:VertiPaq 非ページ|バイト|Average|メモリ内エンジン用にワーキング セットでロックされているメモリの量 (バイト単位)。|ServerResourceType|
|VertiPaqPaged|メモリ:VertiPaq ページ|バイト|Average|メモリ内エンジン用に使用されているページ メモリの量 (バイト単位)。|ServerResourceType|
|RowsReadPerSec|処理:1 秒あたりの読み取り行数|CountPerSecond|Average|すべてのリレーショナル データベースから読み取った行の比率。|ServerResourceType|
|RowsConvertedPerSec|処理:1 秒あたりの変換行数|CountPerSecond|Average|処理中に変換された行の比率。|ServerResourceType|
|RowsWrittenPerSec|処理:1 秒あたりの書き込み行数|CountPerSecond|Average|処理中に書き込まれた行の比率。|ServerResourceType|
|CommandPoolBusyThreads|スレッド:コマンド プールのビジー状態のスレッド|Count|Average|コマンド スレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|CommandPoolIdleThreads|スレッド:コマンド プールのアイドル状態のスレッド|Count|Average|コマンド スレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingBusyThreads|スレッド:長時間解析を行っているビジー状態のスレッド|Count|Average|長時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|LongParsingIdleThreads|スレッド:長時間解析を行っているアイドル状態のスレッド|Count|Average|長時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingJobQueueLength|スレッド:長時間解析を行っているジョブのキューの長さ|Count|Average|長時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|スレッド:処理中のプールのビジー状態の I/O ジョブのスレッド|Count|Average|処理中のスレッド プール内で I/O ジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|スレッド:処理中のプールのビジー状態の I/O 以外のスレッド|Count|Average|処理中のスレッド プール内で I/O 以外のジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|スレッド:処理中のプール I/O ジョブ キューの長さ|Count|Average|処理中のスレッド プールのキューに登録されている I/O ジョブの数。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|スレッド:処理中のプールのアイドル状態の I/O ジョブのスレッド|Count|Average|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|スレッド:処理中のプールのアイドル状態の I/O 以外のスレッド|Count|Average|I/O 以外のジョブ専用の、処理中のスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolIdleThreads|スレッド:クエリ プールのアイドル状態のスレッド|Count|Average|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolJobQueueLength|スレッド:クエリ プール ジョブ キューの長さ|Count|Average|クエリ スレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|ShortParsingBusyThreads|スレッド:短時間解析を行っているビジー状態のスレッド|Count|Average|短時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|ShortParsingIdleThreads|スレッド:短時間解析を行っているアイドル状態のスレッド|Count|Average|短時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|ShortParsingJobQueueLength|スレッド:短時間解析を行っているジョブ キューの長さ|Count|Average|短時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|memory_thrashing_metric|メモリ スラッシング|Percent|Average|平均的なメモリ スラッシング。|ServerResourceType|
|mashup_engine_qpu_metric|M エンジン QPU|Count|Average|マッシュアップ エンジン プロセスによる QPU 使用率|ServerResourceType|
|mashup_engine_memory_metric|M エンジン メモリ|バイト|Average|マッシュアップ エンジン プロセスによるメモリ使用率|ServerResourceType|
|mashup_engine_private_bytes_metric|M エンジン プライベート バイト|バイト|Average|マッシュアップ エンジンのプロセスによるプライベート バイト使用量です。|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M エンジン仮想バイト|バイト|Average|マッシュアップ エンジンのプロセスによる仮想バイト使用量です。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|ゲートウェイ要求の合計 (非推奨)|Count|合計|ゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|SuccessfulRequests|成功したゲートウェイ要求 (非推奨)|Count|合計|成功したゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|UnauthorizedRequests|未承認ゲートウェイ要求 (非推奨)|Count|合計|未承認ゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|FailedRequests|失敗したゲートウェイ要求 (非推奨)|Count|合計|ゲートウェイ要求の失敗数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|OtherRequests|その他のゲートウェイ要求 (非推奨)|Count|合計|その他のゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|Duration|ゲートウェイ要求の全体の期間|ミリ秒|Average|ゲートウェイ要求の全体の期間 (ミリ秒単位)|Location、Hostname|
|BackendDuration|バックエンド要求の期間|ミリ秒|Average|バックエンド要求の期間 (ミリ秒)|Location、Hostname|
|容量|容量|Percent|Average|ApiManagement サービスの使用状況メトリック|Location|
|EventHubTotalEvents|EventHub イベントの合計数|Count|合計|EventHub に送信されるイベントの数|Location|
|EventHubSuccessfulEvents|成功した EventHub イベント数|Count|合計|成功した EventHub イベントの数|Location|
|EventHubTotalFailedEvents|失敗した EventHub イベント数|Count|合計|失敗した EventHub イベントの数|Location|
|EventHubRejectedEvents|拒否された EventHub イベント数|Count|合計|拒否された EventHub イベントの数 (誤った構成または未承認)|Location|
|EventHubThrottledEvents|調整された EventHub イベント数|Count|合計|調整された EventHub イベントの数|Location|
|EventHubTimedoutEvents|タイムアウトした EventHub イベント数|Count|合計|タイムアウトした EventHub イベントの数|Location|
|EventHubDroppedEvents|破棄された EventHub イベント数|Count|合計|キューのサイズ制限に達したためスキップされたイベントの数|Location|
|EventHubTotalBytesSent|EventHub イベントのサイズ|バイト|合計|EventHub イベントの合計サイズ (バイト単位)|Location|
|Requests|Requests|Count|合計|複数のディメンションを持つゲートウェイ要求メトリック|Location、Hostname、LastErrorReason、BackendResponseCode、GatewayResponseCode、BackendResponseCodeCategory、GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|受信 http 要求の合計数。|なし|
|FailedHttpRequestCount|FailedHttpRequestCount|Count|Count|失敗した http 要求。|なし|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Count|Average|http 要求の待機時間。|なし|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|システム CPU 使用率 (%)|Percent|Average|システム全体の最近の CPU 使用率|AppName、Pod|
|AppCpuUsagePercentage|アプリの CPU 使用率 (%)|Percent|Average|アプリ JVM の CPU 使用率 (%)|AppName、Pod|
|AppMemoryCommitted|割り当て済みアプリ メモリ|バイト|Average|JVM に割り当て済みのメモリ (バイト単位)|AppName、Pod|
|AppMemoryUsed|使用済みアプリ メモリ|バイト|Average|アプリのメモリ使用量 (バイト単位)|AppName、Pod|
|AppMemoryMax|最大のアプリ メモリ|バイト|最大値|メモリ管理に使用できるメモリの最大量 (バイト単位)|AppName、Pod|
|MaxOldGenMemoryPoolBytes|使用可能な古い世代データの最大サイズ|バイト|Average|古い世代のメモリ プールの最大サイズ|AppName、Pod|
|OldGenMemoryPoolBytes|古い世代のデータのサイズ|バイト|Average|完全な GC 後の古い世代のメモリ プールのサイズ|AppName、Pod|
|OldGenPromotedBytes|古い世代データのサイズに昇格|バイト|最大値|GC 前から後にかけた古い世代のメモリ プールのサイズ増加数|AppName、Pod|
|YoungGenPromotedBytes|若い世代データのサイズに昇格|バイト|最大値|1つの GC の後から次の GC の前までの若い世代のメモリ プールのサイズ増加に合わせて増加|AppName、Pod|
|GCPauseTotalCount|GC の一時停止回数|Count|合計|GC の一時停止回数|AppName、Pod|
|GCPauseTotalTime|GC の一時停止合計時間|ミリ秒|合計|GC の一時停止合計時間|AppName、Pod|
|TomcatSentBytes|Tomcat 合計送信バイト数|バイト|合計|Tomcat 合計送信バイト数|AppName、Pod|
|TomcatReceivedBytes|Tomcat 合計受信バイト|バイト|合計|Tomcat 合計受信バイト|AppName、Pod|
|TomcatRequestTotalTime|Tomcat 要求合計時間|ミリ秒|合計|Tomcat 要求合計時間|AppName、Pod|
|TomcatRequestTotalCount|Tomcat 要求合計数|Count|合計|Tomcat 要求合計数|AppName、Pod|
|TomcatResponseAvgTime|Tomcat 要求平均時間|ミリ秒|Average|Tomcat 要求平均時間|AppName、Pod|
|TomcatRequestMaxTime|Tomcat 要求最大時間|ミリ秒|最大値|Tomcat 要求最大時間|AppName、Pod|
|TomcatErrorCount|Tomcat グローバル エラー|Count|合計|Tomcat グローバル エラー|AppName、Pod|
|TomcatSessionActiveMaxCount|Tomcat セッション最大アクティブ数|Count|合計|Tomcat セッション最大アクティブ数|AppName、Pod|
|TomcatSessionAliveMaxTime|Tomcat セッション最大アライブ時間|ミリ秒|最大値|Tomcat セッション最大アライブ時間|AppName、Pod|
|TomcatSessionActiveCurrentCount|Tomcat セッション アライブ数|Count|合計|Tomcat セッション アライブ数|AppName、Pod|
|TomcatSessionCreatedCount|Tomcat セッション作成数|Count|合計|Tomcat セッション作成数|AppName、Pod|
|TomcatSessionExpiredCount|Tomcat セッション期限切れ数|Count|合計|Tomcat セッション期限切れ数|AppName、Pod|
|TomcatSessionRejectedCount|Tomcat セッション拒否数|Count|合計|Tomcat セッション拒否数|AppName、Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalJob|合計ジョブ数|Count|合計|ジョブの合計数|Runbook、Status|
|TotalUpdateDeploymentRuns|更新プログラムのデプロイの合計実行回数|Count|合計|ソフトウェア更新プログラムのデプロイの実行回数の合計|SoftwareUpdateConfigurationName、Status|
|TotalUpdateDeploymentMachineRuns|更新プログラムのデプロイ マシンの合計実行回数|Count|合計|ソフトウェア更新プログラムのデプロイの実行における、ソフトウェア更新プログラムのデプロイ マシンの実行回数の合計|SoftwareUpdateConfigurationName、Status、TargetComputer、SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count (専用コアの数)|Count|合計|Batch アカウントの専用コアの合計数|なし|
|TotalNodeCount|Dedicated Node Count (専用ノードの数)|Count|合計|Batch アカウントの専用ノードの合計数|なし|
|LowPriorityCoreCount|LowPriority Core Count (優先順位の低いコアの数)|Count|合計|Batch アカウントの優先順位の低いコアの合計数|なし|
|TotalLowPriorityNodeCount|Low-Priority Node Count (優先順位の低いノードの数)|Count|合計|Batch アカウントの優先順位の低いノードの合計数|なし|
|CreatingNodeCount|作成ノード数|Count|合計|作成されるノードの数|なし|
|StartingNodeCount|開始ノード数|Count|合計|開始するノードの数|なし|
|WaitingForStartTaskNodeCount|開始タスクの待機ノード数|Count|合計|開始タスクの完了を待機しているノードの数|なし|
|StartTaskFailedNodeCount|開始タスクの失敗ノード数|Count|合計|開始タスクが失敗したノードの数|なし|
|IdleNodeCount|アイドル状態のノード数|Count|合計|アイドル状態のノードの数|なし|
|OfflineNodeCount|オフライン ノード数|Count|合計|オフライン ノードの数|なし|
|RebootingNodeCount|再起動中のノード数|Count|合計|再起動中のノードの数|なし|
|ReimagingNodeCount|再イメージ化中のノード数|Count|合計|再イメージ化中のノードの数|なし|
|RunningNodeCount|実行中のノード数|Count|合計|実行中のノードの数|なし|
|LeavingPoolNodeCount|プールから移動中のノード数|Count|合計|プールから移動中のノードの数|なし|
|UnusableNodeCount|使用できないノード数|Count|合計|使用できないノードの数|なし|
|PreemptedNodeCount|Preempted Node Count (割り込まれたノードの数)|Count|合計|割り込まれたノードの数|なし|
|TaskStartEvent|タスク開始イベント|Count|合計|開始したタスクの合計数|なし|
|TaskCompleteEvent|タスク完了イベント|Count|合計|完了したタスクの合計数|なし|
|TaskFailEvent|タスク失敗イベント|Count|合計|障害状態で完了したタスクの合計数|なし|
|PoolCreateEvent|プール作成イベント|Count|合計|作成されたプールの合計数|なし|
|PoolResizeStartEvent|プールのサイズ変更の開始イベント|Count|合計|開始したプールのサイズ変更の合計数|なし|
|PoolResizeCompleteEvent|プールのサイズ変更の完了イベント|Count|合計|完了したプールのサイズ変更の合計数|なし|
|PoolDeleteStartEvent|プール削除の開始イベント|Count|合計|開始したプール削除の合計数|なし|
|PoolDeleteCompleteEvent|プール削除の完了イベント|Count|合計|完了したプール削除の合計数|なし|
|JobDeleteCompleteEvent|ジョブ削除の完了イベント|Count|合計|正常に削除されたジョブの合計数。|なし|
|JobDeleteStartEvent|ジョブ削除の開始イベント|Count|合計|削除を要求されたジョブの合計数。|なし|
|JobDisableCompleteEvent|ジョブ無効化の完了イベント|Count|合計|正常に無効化されたジョブの合計数。|なし|
|JobDisableStartEvent|ジョブ無効化の開始イベント|Count|合計|無効化を要求されたジョブの合計数。|なし|
|JobStartEvent|ジョブの開始イベント|Count|合計|正常に開始されたジョブの合計数。|なし|
|JobTerminateCompleteEvent|ジョブ終了の完了イベント|Count|合計|正常に終了されたジョブの合計数。|なし|
|JobTerminateStartEvent|ジョブ終了の開始イベント|Count|合計|終了を要求されたジョブの合計数。|なし|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|送信されたジョブ|送信されたジョブ|Count|合計|送信されたジョブの数|Scenario、ClusterName|
|ジョブが完了しました|完了したジョブ|Count|合計|完了したジョブの数|Scenario、ClusterName、ResultType|
|ノード総数|ノード総数|Count|Average|ノードの合計数|Scenario、ClusterName|
|アクティブなノード|アクティブなノード|Count|Average|実行中のノードの数|Scenario、ClusterName|
|アイドル状態のノード|アイドル状態のノード|Count|Average|アイドル状態のノードの数|Scenario、ClusterName|
|使用できないノード|使用できないノード|Count|Average|使用できないノードの数|Scenario、ClusterName|
|割り込まれたノード|割り込まれたノード|Count|Average|割り込まれたノードの数|Scenario、ClusterName|
|終了中のノード|終了中のノード|Count|Average|終了中のノードの数|Scenario、ClusterName|
|コアの合計|コアの合計|Count|Average|コアの合計数|Scenario、ClusterName|
|アクティブなコア|アクティブなコア|Count|Average|アクティブなコアの数|Scenario、ClusterName|
|アイドル状態のコア|アイドル状態のコア|Count|Average|アイドル状態のコアの数|Scenario、ClusterName|
|使用できないコア|使用できないコア|Count|Average|使用できないコアの数|Scenario、ClusterName|
|割り込まれたコア|割り込まれたコア|Count|Average|割り込まれたコアの数|Scenario、ClusterName|
|終了中のコア|終了中のコア|Count|Average|終了中のコアの数|Scenario、ClusterName|
|クォータ使用率|クォータ使用率|Count|Average|クォータ使用率 (%)|Scenario、ClusterName、VmFamilyName、VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU 使用率 (%)|Percent|最大値|CPU 使用率 (%)|Node|
|MemoryUsage|メモリ使用量|バイト|Average|メモリ使用量|Node|
|MemoryLimit|メモリの制限|バイト|Average|メモリの制限|Node|
|MemoryUsagePercentageInDouble|メモリ使用率 (%)|Percent|Average|メモリ使用率 (%)|Node|
|StorageUsage|ストレージの使用状況|バイト|Average|ストレージの使用状況|Node|
|IOReadBytes|IO 読み取りバイト数|バイト|合計|IO 読み取りバイト数|Node|
|IOWriteBytes|IO 書き込みバイト数|バイト|合計|IO 書き込みバイト数|Node|
|ConnectionAccepted|承認された接続|Count|合計|承認された接続|Node|
|ConnectionHandled|処理された接続|Count|合計|処理された接続|Node|
|ConnectionActive|アクティブな接続|Count|Average|アクティブな接続|Node|
|RequestHandled|処理された要求|Count|合計|処理された要求|Node|
|ProcessedBlocks|処理されたブロック|Count|合計|処理されたブロック|Node|
|ProcessedTransactions|処理されたトランザクション|Count|合計|処理されたトランザクション|Node|
|PendingTransactions|保留中のトランザクション|Count|Average|保留中のトランザクション|Node|
|QueuedTransactions|キューに登録されたトランザクション|Count|Average|キューに登録されたトランザクション|Node|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|connectedclients|接続されているクライアント数|Count|最大値||ShardId|
|totalcommandsprocessed|合計処理数|Count|合計||ShardId|
|cachehits|キャッシュ ヒット数|Count|合計||ShardId|
|cachemisses|キャッシュ ミス数|Count|合計||ShardId|
|cachemissrate|キャッシュ ミス率|Percent|cachemissrate||ShardId|
|getcommands|取得数|Count|合計||ShardId|
|setcommands|セット|Count|合計||ShardId|
|operationsPerSecond|1 秒あたりの操作回数|Count|最大値||ShardId|
|evictedkeys|削除されたキー数|Count|合計||ShardId|
|totalkeys|合計キー数|Count|最大値||ShardId|
|expiredkeys|期限切れキー数|Count|合計||ShardId|
|usedmemory|メモリ使用量|バイト|最大値||ShardId|
|usedmemorypercentage|使用されているメモリの割合|Percent|最大値||ShardId|
|usedmemoryRss|RSS メモリ使用量|バイト|最大値||ShardId|
|serverLoad|サーバーの負荷|Percent|最大値||ShardId|
|cacheWrite|キャッシュの書き込み|BytesPerSecond|最大値||ShardId|
|cacheRead|キャッシュの読み取り|BytesPerSecond|最大値||ShardId|
|percentProcessorTime|CPU|Percent|最大値||ShardId|
|cacheLatency|キャッシュの待機時間 (マイクロ秒単位) (プレビュー)|Count|Average||ShardId|
|エラー|エラー|Count|最大値||ShardId、ErrorType|
|connectedclients0|接続されているクライアント数 (シャード 0)|Count|最大値||なし|
|totalcommandsprocessed0|合計処理数 (シャード 0)|Count|合計||なし|
|cachehits0|キャッシュ ヒット数 (シャード 0)|Count|合計||なし|
|cachemisses0|キャッシュ ミス数 (シャード 0)|Count|合計||なし|
|getcommands0|取得数 (シャード 0)|Count|合計||なし|
|setcommands0|設定数 (シャード 0)|Count|合計||なし|
|operationsPerSecond0|1 秒あたりの操作回数 (シャード 0)|Count|最大値||なし|
|evictedkeys0|削除されたキー数 (シャード 0)|Count|合計||なし|
|totalkeys0|合計キー数 (シャード 0)|Count|最大値||なし|
|expiredkeys0|期限切れキー数 (シャード 0)|Count|合計||なし|
|usedmemory0|メモリ使用量 (シャード 0)|バイト|最大値||なし|
|usedmemoryRss0|RSS メモリ使用量 (シャード 0)|バイト|最大値||なし|
|serverLoad0|サーバーの負荷 (シャード 0)|Percent|最大値||なし|
|cacheWrite0|キャッシュの書き込み (シャード 0)|BytesPerSecond|最大値||なし|
|cacheRead0|キャッシュの読み取り (シャード 0)|BytesPerSecond|最大値||なし|
|percentProcessorTime0|CPU (シャード 0)|Percent|最大値||なし|
|connectedclients1|接続されているクライアント数 (シャード 1)|Count|最大値||なし|
|totalcommandsprocessed1|合計処理数 (シャード 1)|Count|合計||なし|
|cachehits1|キャッシュ ヒット数 (シャード 1)|Count|合計||なし|
|cachemisses1|キャッシュ ミス数 (シャード 1)|Count|合計||なし|
|getcommands1|取得数 (シャード 1)|Count|合計||なし|
|setcommands1|設定数 (シャード 1)|Count|合計||なし|
|operationsPerSecond1|1 秒あたりの操作回数 (シャード 1)|Count|最大値||なし|
|evictedkeys1|削除されたキー数 (シャード 1)|Count|合計||なし|
|totalkeys1|合計キー数 (シャード 1)|Count|最大値||なし|
|expiredkeys1|期限切れキー数 (シャード 1)|Count|合計||なし|
|usedmemory1|メモリ使用量 (シャード 1)|バイト|最大値||なし|
|usedmemoryRss1|RSS メモリ使用量 (シャード 1)|バイト|最大値||なし|
|serverLoad1|サーバーの負荷 (シャード 1)|Percent|最大値||なし|
|cacheWrite1|キャッシュの書き込み (シャード 1)|BytesPerSecond|最大値||なし|
|cacheRead1|キャッシュの読み取り (シャード 1)|BytesPerSecond|最大値||なし|
|percentProcessorTime1|CPU (シャード 1)|Percent|最大値||なし|
|connectedclients2|接続されているクライアント数 (シャード 2)|Count|最大値||なし|
|totalcommandsprocessed2|合計処理数 (シャード 2)|Count|合計||なし|
|cachehits2|キャッシュ ヒット数 (シャード 2)|Count|合計||なし|
|cachemisses2|キャッシュ ミス数 (シャード 2)|Count|合計||なし|
|getcommands2|取得数 (シャード 2)|Count|合計||なし|
|setcommands2|設定数 (シャード 2)|Count|合計||なし|
|operationsPerSecond2|1 秒あたりの操作回数 (シャード 2)|Count|最大値||なし|
|evictedkeys2|削除されたキー数 (シャード 2)|Count|合計||なし|
|totalkeys2|合計キー数 (シャード 2)|Count|最大値||なし|
|expiredkeys2|期限切れキー数 (シャード 2)|Count|合計||なし|
|usedmemory2|メモリ使用量 (シャード 2)|バイト|最大値||なし|
|usedmemoryRss2|RSS メモリ使用量 (シャード 2)|バイト|最大値||なし|
|serverLoad2|サーバーの負荷 (シャード 2)|Percent|最大値||なし|
|cacheWrite2|キャッシュの書き込み (シャード 2)|BytesPerSecond|最大値||なし|
|cacheRead2|キャッシュの読み取り (シャード 2)|BytesPerSecond|最大値||なし|
|percentProcessorTime2|CPU (シャード 2)|Percent|最大値||なし|
|connectedclients3|接続されているクライアント数 (シャード 3)|Count|最大値||なし|
|totalcommandsprocessed3|合計処理数 (シャード 3)|Count|合計||なし|
|cachehits3|キャッシュ ヒット数 (シャード 3)|Count|合計||なし|
|cachemisses3|キャッシュ ミス数 (シャード 3)|Count|合計||なし|
|getcommands3|取得数 (シャード 3)|Count|合計||なし|
|setcommands3|設定数 (シャード 3)|Count|合計||なし|
|operationsPerSecond3|1 秒あたりの操作回数 (シャード 3)|Count|最大値||なし|
|evictedkeys3|削除されたキー数 (シャード 3)|Count|合計||なし|
|totalkeys3|合計キー数 (シャード 3)|Count|最大値||なし|
|expiredkeys3|期限切れキー数 (シャード 3)|Count|合計||なし|
|usedmemory3|メモリ使用量 (シャード 3)|バイト|最大値||なし|
|usedmemoryRss3|RSS メモリ使用量 (シャード 3)|バイト|最大値||なし|
|serverLoad3|サーバーの負荷 (シャード 3)|Percent|最大値||なし|
|cacheWrite3|キャッシュの書き込み (シャード 3)|BytesPerSecond|最大値||なし|
|cacheRead3|キャッシュの読み取り (シャード 3)|BytesPerSecond|最大値||なし|
|percentProcessorTime3|CPU (シャード 3)|Percent|最大値||なし|
|connectedclients4|接続されているクライアント数 (シャード 4)|Count|最大値||なし|
|totalcommandsprocessed4|合計処理数 (シャード 4)|Count|合計||なし|
|cachehits4|キャッシュ ヒット数 (シャード 4)|Count|合計||なし|
|cachemisses4|キャッシュ ミス数 (シャード 4)|Count|合計||なし|
|getcommands4|取得数 (シャード 4)|Count|合計||なし|
|setcommands4|設定数 (シャード 4)|Count|合計||なし|
|operationsPerSecond4|1 秒あたりの操作回数 (シャード 4)|Count|最大値||なし|
|evictedkeys4|削除されたキー数 (シャード 4)|Count|合計||なし|
|totalkeys4|合計キー数 (シャード 4)|Count|最大値||なし|
|expiredkeys4|期限切れキー数 (シャード 4)|Count|合計||なし|
|usedmemory4|メモリ使用量 (シャード 4)|バイト|最大値||なし|
|usedmemoryRss4|RSS メモリ使用量 (シャード 4)|バイト|最大値||なし|
|serverLoad4|サーバーの負荷 (シャード 4)|Percent|最大値||なし|
|cacheWrite4|キャッシュの書き込み (シャード 4)|BytesPerSecond|最大値||なし|
|cacheRead4|キャッシュの読み取り (シャード 4)|BytesPerSecond|最大値||なし|
|percentProcessorTime4|CPU (シャード 4)|Percent|最大値||なし|
|connectedclients5|接続されているクライアント数 (シャード 5)|Count|最大値||なし|
|totalcommandsprocessed5|合計処理数 (シャード 5)|Count|合計||なし|
|cachehits5|キャッシュ ヒット数 (シャード 5)|Count|合計||なし|
|cachemisses5|キャッシュ ミス数 (シャード 5)|Count|合計||なし|
|getcommands5|取得数 (シャード 5)|Count|合計||なし|
|setcommands5|設定数 (シャード 5)|Count|合計||なし|
|operationsPerSecond5|1 秒あたりの操作回数 (シャード 5)|Count|最大値||なし|
|evictedkeys5|削除されたキー数 (シャード 5)|Count|合計||なし|
|totalkeys5|合計キー数 (シャード 5)|Count|最大値||なし|
|expiredkeys5|期限切れキー数 (シャード 5)|Count|合計||なし|
|usedmemory5|メモリ使用量 (シャード 5)|バイト|最大値||なし|
|usedmemoryRss5|RSS メモリ使用量 (シャード 5)|バイト|最大値||なし|
|serverLoad5|サーバーの負荷 (シャード 5)|Percent|最大値||なし|
|cacheWrite5|キャッシュの書き込み (シャード 5)|BytesPerSecond|最大値||なし|
|cacheRead5|キャッシュの読み取り (シャード 5)|BytesPerSecond|最大値||なし|
|percentProcessorTime5|CPU (シャード 5)|Percent|最大値||なし|
|connectedclients6|接続されているクライアント数 (シャード 6)|Count|最大値||なし|
|totalcommandsprocessed6|合計処理数 (シャード 6)|Count|合計||なし|
|cachehits6|キャッシュ ヒット数 (シャード 6)|Count|合計||なし|
|cachemisses6|キャッシュ ミス数 (シャード 6)|Count|合計||なし|
|getcommands6|取得数 (シャード 6)|Count|合計||なし|
|setcommands6|設定数 (シャード 6)|Count|合計||なし|
|operationsPerSecond6|1 秒あたりの操作回数 (シャード 6)|Count|最大値||なし|
|evictedkeys6|削除されたキー数 (シャード 6)|Count|合計||なし|
|totalkeys6|合計キー数 (シャード 6)|Count|最大値||なし|
|expiredkeys6|期限切れキー数 (シャード 6)|Count|合計||なし|
|usedmemory6|メモリ使用量 (シャード 6)|バイト|最大値||なし|
|usedmemoryRss6|RSS メモリ使用量 (シャード 6)|バイト|最大値||なし|
|serverLoad6|サーバーの負荷 (シャード 6)|Percent|最大値||なし|
|cacheWrite6|キャッシュの書き込み (シャード 6)|BytesPerSecond|最大値||なし|
|cacheRead6|キャッシュの読み取り (シャード 6)|BytesPerSecond|最大値||なし|
|percentProcessorTime6|CPU (シャード 6)|Percent|最大値||なし|
|connectedclients7|接続されているクライアント数 (シャード 7)|Count|最大値||なし|
|totalcommandsprocessed7|合計処理数 (シャード 7)|Count|合計||なし|
|cachehits7|キャッシュ ヒット数 (シャード 7)|Count|合計||なし|
|cachemisses7|キャッシュ ミス数 (シャード 7)|Count|合計||なし|
|getcommands7|取得数 (シャード 7)|Count|合計||なし|
|setcommands7|設定数 (シャード 7)|Count|合計||なし|
|operationsPerSecond7|1 秒あたりの操作回数 (シャード 7)|Count|最大値||なし|
|evictedkeys7|削除されたキー数 (シャード 7)|Count|合計||なし|
|totalkeys7|合計キー数 (シャード 7)|Count|最大値||なし|
|expiredkeys7|期限切れキー数 (シャード 7)|Count|合計||なし|
|usedmemory7|メモリ使用量 (シャード 7)|バイト|最大値||なし|
|usedmemoryRss7|RSS メモリ使用量 (シャード 7)|バイト|最大値||なし|
|serverLoad7|サーバーの負荷 (シャード 7)|Percent|最大値||なし|
|cacheWrite7|キャッシュの書き込み (シャード 7)|BytesPerSecond|最大値||なし|
|cacheRead7|キャッシュの読み取り (シャード 7)|BytesPerSecond|最大値||なし|
|percentProcessorTime7|CPU (シャード 7)|Percent|最大値||なし|
|connectedclients8|接続されているクライアント数 (シャード 8)|Count|最大値||なし|
|totalcommandsprocessed8|合計処理数 (シャード 8)|Count|合計||なし|
|cachehits8|キャッシュ ヒット数 (シャード 8)|Count|合計||なし|
|cachemisses8|キャッシュ ミス数 (シャード 8)|Count|合計||なし|
|getcommands8|取得数 (シャード 8)|Count|合計||なし|
|setcommands8|設定数 (シャード 8)|Count|合計||なし|
|operationsPerSecond8|1 秒あたりの操作回数 (シャード 8)|Count|最大値||なし|
|evictedkeys8|削除されたキー数 (シャード 8)|Count|合計||なし|
|totalkeys8|合計キー数 (シャード 8)|Count|最大値||なし|
|expiredkeys8|期限切れキー数 (シャード 8)|Count|合計||なし|
|usedmemory8|メモリ使用量 (シャード 8)|バイト|最大値||なし|
|usedmemoryRss8|RSS メモリ使用量 (シャード 8)|バイト|最大値||なし|
|serverLoad8|サーバーの負荷 (シャード 8)|Percent|最大値||なし|
|cacheWrite8|キャッシュの書き込み (シャード 8)|BytesPerSecond|最大値||なし|
|cacheRead8|キャッシュの読み取り (シャード 8)|BytesPerSecond|最大値||なし|
|percentProcessorTime8|CPU (シャード 8)|Percent|最大値||なし|
|connectedclients9|接続されているクライアント数 (シャード 9)|Count|最大値||なし|
|totalcommandsprocessed9|合計処理数 (シャード 9)|Count|合計||なし|
|cachehits9|キャッシュ ヒット数 (シャード 9)|Count|合計||なし|
|cachemisses9|キャッシュ ミス数 (シャード 9)|Count|合計||なし|
|getcommands9|取得数 (シャード 9)|Count|合計||なし|
|setcommands9|設定数 (シャード 9)|Count|合計||なし|
|operationsPerSecond9|1 秒あたりの操作回数 (シャード 9)|Count|最大値||なし|
|evictedkeys9|削除されたキー数 (シャード 9)|Count|合計||なし|
|totalkeys9|合計キー数 (シャード 9)|Count|最大値||なし|
|expiredkeys9|期限切れキー数 (シャード 9)|Count|合計||なし|
|usedmemory9|メモリ使用量 (シャード 9)|バイト|最大値||なし|
|usedmemoryRss9|RSS メモリ使用量 (シャード 9)|バイト|最大値||なし|
|serverLoad9|サーバーの負荷 (シャード 9)|Percent|最大値||なし|
|cacheWrite9|キャッシュの書き込み (シャード 9)|BytesPerSecond|最大値||なし|
|cacheRead9|キャッシュの読み取り (シャード 9)|BytesPerSecond|最大値||なし|
|percentProcessorTime9|CPU (シャード 9)|Percent|最大値||なし|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web アプリケーション ファイアウォール要求の数|Count|合計|Web アプリケーション ファイアウォールによって処理されるクライアント要求の数|PolicyName、RuleName、Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|なし|
|Network In|Network In|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|なし|
|Network Out|Network Out|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|なし|
|Disk Read Bytes/Sec|ディスクの読み取り|BytesPerSecond|Average|監視期間中にディスクから読み取られた平均バイト数。|なし|
|Disk Write Bytes/Sec|ディスクの書き込み|BytesPerSecond|Average|監視期間中にディスクに書き込まれた平均バイト数。|なし|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS。|なし|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS。|なし|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|RoleInstanceId|
|Network In|Network In|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|RoleInstanceId|
|Network Out|Network Out|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|RoleInstanceId|
|Disk Read Bytes/Sec|ディスクの読み取り|BytesPerSecond|Average|監視期間中にディスクから読み取られた平均バイト数。|RoleInstanceId|
|Disk Write Bytes/Sec|ディスクの書き込み|BytesPerSecond|Average|監視期間中にディスクに書き込まれた平均バイト数。|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS。|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS。|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity (使用済み容量)|バイト|Average|アカウントの使用済み容量|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity (BLOB 容量)|バイト|Average|ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。|BlobType、Tier|
|BlobCount|Blob Count (BLOB 数)|Count|Average|ストレージ アカウントの Blob service 内の BLOB の数。|BlobType、Tier|
|ContainerCount|Blob Container Count (BLOB コンテナー数)|Count|Average|ストレージ アカウントの Blob service 内のコンテナーの数。|なし|
|IndexCapacity|インデックスの容量|バイト|Average|ADLS Gen2 (階層構造) インデックスで使用されるストレージの容量 (バイト単位)。|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity (テーブル容量)|バイト|Average|ストレージ アカウントの Table service によって使われているストレージの量 (バイト単位)。|なし|
|TableCount|Table Count (テーブル数)|Count|Average|ストレージ アカウントの Table service 内のテーブルの数。|なし|
|TableEntityCount|Table Entity Count (テーブル エンティティ数)|Count|Average|ストレージ アカウントの Table service 内のテーブル エンティティの数。|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|File Capacity (ファイル容量)|バイト|Average|ストレージ アカウントの File サービスによって使われているストレージの量 (バイト単位)。|FileShare|
|FileCount|File Count (ファイル数)|Count|Average|ストレージ アカウントの File サービス内のファイルの数。|FileShare|
|FileShareCount|File Share Count (ファイル共有数)|Count|Average|ストレージ アカウントの File サービス内のファイル共有の数。|なし|
|FileShareSnapshotCount|ファイル共有のスナップショット数|Count|Average|ストレージ アカウントの Files サービス内の共有に存在するスナップショットの数。|FileShare|
|FileShareSnapshotSize|ファイル共有のスナップショットのサイズ|バイト|Average|ストレージ アカウントの Files サービスのスナップショットによって使用される記憶域の容量 (バイト単位)。|FileShare|
|FileShareQuota|ファイル共有のクォータ サイズ|バイト|Average|Azure Files サービスで使用できる記憶域の容量の上限 (バイト単位)。|FileShare|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication、FileShare|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication、FileShare|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication、FileShare|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication、FileShare|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication、FileShare|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication、FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity (キュー容量)|バイト|Average|ストレージ アカウントの Queue サービスによって使われているストレージの量 (バイト単位)。|なし|
|QueueCount|Queue Count (キュー数)|Count|Average|ストレージ アカウントの Queue サービス内のキューの数。|なし|
|QueueMessageCount|Queue Message Count (キュー メッセージ数)|Count|Average|ストレージ アカウントの Queue サービス内のキュー メッセージの概数。|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|合計呼び出し数|Count|合計|呼び出しの合計数。|ApiName、OperationName、Region|
|SuccessfulCalls|成功した呼び出し|Count|合計|成功した呼び出しの数。|ApiName、OperationName、Region|
|TotalErrors|合計エラー数|Count|合計|エラー応答 (HTTP 応答コード 4xx または 5xx) があった呼び出しの合計回数。|ApiName、OperationName、Region|
|BlockedCalls|ブロックされた呼び出し|Count|合計|レートまたはクォータの制限を超えた呼び出しの回数。|ApiName、OperationName、Region|
|ServerErrors|サーバー エラー|Count|合計|サービスの内部エラー (HTTP 応答コード 5xx) があった呼び出しの回数。|ApiName、OperationName、Region|
|ClientErrors|クライアント エラー|Count|合計|クライアント側のエラー (HTTP 応答コード 4xx) があった呼び出しの回数。|ApiName、OperationName、Region|
|DataIn|受信データ|バイト|合計|受信データのサイズ (バイト)。|ApiName、OperationName、Region|
|DataOut|送信データ|バイト|合計|送信データのサイズ (バイト)。|ApiName、OperationName、Region|
|Latency|Latency|MilliSeconds|Average|待機時間 (ミリ秒)。|ApiName、OperationName、Region|
|CharactersTranslated|Characters Translated (変換文字数)|Count|合計|受信テキスト要求の合計文字数。|ApiName、OperationName、Region|
|CharactersTrained|トレーニングされた文字数|Count|合計|トレーニングされた文字の合計数。|ApiName、OperationName、Region|
|SpeechSessionDuration|Speech Session Duration (音声セッション継続時間)|Seconds|合計|音声セッションの合計継続時間 (秒)。|ApiName、OperationName、Region|
|TotalTransactions|合計トランザクション|Count|合計|トランザクションの合計数。|なし|
|TotalTokenCalls|トークン呼び出しの合計|Count|合計|トークン呼び出しの合計回数。|ApiName、OperationName、Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|なし|
|Network In|課金対象のネットワーク受信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから受信した課金対象バイト数 (受信トラフィック) (非推奨)|なし|
|Network Out|課金対象のネットワーク送信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから発信した課金対象バイト数 (送信トラフィック) (非推奨)|なし|
|Disk Read Bytes|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|なし|
|Disk Write Bytes|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|なし|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|なし|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|なし|
|未使用の CPU クレジット|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|なし|
|使用済みの CPU クレジット|使用済みの CPU クレジット|Count|Average|仮想マシンによって消費されたクレジットの合計数|なし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|SlotId|
|Per Disk QD|データ ディスク QD (非推奨)|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|なし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|なし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|なし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|なし|
|OS Per Disk QD|OS ディスク QD (非推奨)|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|なし|
|データ ディスク読み取りバイト数/秒|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN|
|データ ディスク書き込みバイト数/秒|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN|
|データ ディスク読み取り操作数/秒|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|LUN|
|データ ディスク書き込み操作数/秒|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|LUN|
|データ ディスクのキューの深さ|データ ディスクのキューの深さ (プレビュー)|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|LUN|
|OS ディスク読み取りバイト数/秒|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|なし|
|OS ディスク書き込みバイト数/秒|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|なし|
|OS ディスク読み取り操作数/秒|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|なし|
|OS ディスク書き込み操作数/秒|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|なし|
|OS ディスクのキューの深さ|OS ディスクのキューの深さ (プレビュー)|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|なし|
|受信フロー数|受信フロー数|Count|Average|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|なし|
|送信フロー数|送信フロー数|Count|Average|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|なし|
|受信フローの最大作成速度|受信フローの最大作成速度 (プレビュー)|CountPerSecond|Average|受信フローの最大作成速度 (VM に送信されるトラフィック)|なし|
|送信フローの最大作成速度|送信フローの最大作成速度 (プレビュー)|CountPerSecond|Average|送信フローの最大作成速度 (VM から送信されるトラフィック)|なし|
|Premium データ ディスク キャッシュ読み取りヒット|Premium データ ディスク キャッシュ読み取りヒット (プレビュー)|Percent|Average|Premium データ ディスク キャッシュ読み取りヒット|LUN|
|Premium データ ディスク キャッシュ読み取りミス|Premium データ ディスク キャッシュ読み取りミス (プレビュー)|Percent|Average|Premium データ ディスク キャッシュ読み取りミス|LUN|
|Premium OS ディスク キャッシュ読み取りヒット|Premium OS ディスク キャッシュ読み取りヒット (プレビュー)|Percent|Average|Premium OS ディスク キャッシュ読み取りヒット|なし|
|Premium OS ディスク キャッシュ読み取りミス|Premium OS ディスク キャッシュ読み取りミス (プレビュー)|Percent|Average|Premium OS ディスク キャッシュ読み取りミス|なし|
|受信ネットワーク合計|受信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|なし|
|送信ネットワーク合計|送信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|なし|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|VMName|
|Network In|課金対象のネットワーク受信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから受信した課金対象バイト数 (受信トラフィック) (非推奨)|VMName|
|Network Out|課金対象のネットワーク送信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから発信した課金対象バイト数 (送信トラフィック) (非推奨)|VMName|
|Disk Read Bytes|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|VMName|
|Disk Write Bytes|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|VMName|
|未使用の CPU クレジット|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|なし|
|使用済みの CPU クレジット|使用済みの CPU クレジット|Count|Average|仮想マシンによって消費されたクレジットの合計数|なし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|SlotId|
|Per Disk QD|データ ディスク QD (非推奨)|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|なし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|なし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|なし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|なし|
|OS Per Disk QD|OS ディスク QD (非推奨)|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|なし|
|データ ディスク読み取りバイト数/秒|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN、VMName|
|データ ディスク書き込みバイト数/秒|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN、VMName|
|データ ディスク読み取り操作数/秒|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|LUN、VMName|
|データ ディスク書き込み操作数/秒|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|LUN、VMName|
|データ ディスクのキューの深さ|データ ディスクのキューの深さ (プレビュー)|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|LUN、VMName|
|OS ディスク読み取りバイト数/秒|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|VMName|
|OS ディスク書き込みバイト数/秒|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|VMName|
|OS ディスク読み取り操作数/秒|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|VMName|
|OS ディスク書き込み操作数/秒|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|VMName|
|OS ディスクのキューの深さ|OS ディスクのキューの深さ (プレビュー)|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|VMName|
|受信フロー数|受信フロー数|Count|Average|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|VMName|
|送信フロー数|送信フロー数|Count|Average|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|VMName|
|受信フローの最大作成速度|受信フローの最大作成速度 (プレビュー)|CountPerSecond|Average|受信フローの最大作成速度 (VM に送信されるトラフィック)|VMName|
|送信フローの最大作成速度|送信フローの最大作成速度 (プレビュー)|CountPerSecond|Average|送信フローの最大作成速度 (VM から送信されるトラフィック)|VMName|
|Premium データ ディスク キャッシュ読み取りヒット|Premium データ ディスク キャッシュ読み取りヒット (プレビュー)|Percent|Average|Premium データ ディスク キャッシュ読み取りヒット|LUN、VMName|
|Premium データ ディスク キャッシュ読み取りミス|Premium データ ディスク キャッシュ読み取りミス (プレビュー)|Percent|Average|Premium データ ディスク キャッシュ読み取りミス|LUN、VMName|
|Premium OS ディスク キャッシュ読み取りヒット|Premium OS ディスク キャッシュ読み取りヒット (プレビュー)|Percent|Average|Premium OS ディスク キャッシュ読み取りヒット|VMName|
|Premium OS ディスク キャッシュ読み取りミス|Premium OS ディスク キャッシュ読み取りミス (プレビュー)|Percent|Average|Premium OS ディスク キャッシュ読み取りミス|VMName|
|受信ネットワーク合計|受信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|VMName|
|送信ネットワーク合計|送信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|なし|
|Network In|課金対象のネットワーク受信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから受信した課金対象バイト数 (受信トラフィック) (非推奨)|なし|
|Network Out|課金対象のネットワーク送信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから発信した課金対象バイト数 (送信トラフィック) (非推奨)|なし|
|Disk Read Bytes|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|なし|
|Disk Write Bytes|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|なし|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|なし|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|なし|
|未使用の CPU クレジット|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|なし|
|使用済みの CPU クレジット|使用済みの CPU クレジット|Count|Average|仮想マシンによって消費されたクレジットの合計数|なし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|SlotId|
|Per Disk QD|データ ディスク QD (非推奨)|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|なし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|なし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|なし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|なし|
|OS Per Disk QD|OS ディスク QD (非推奨)|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|なし|
|データ ディスク読み取りバイト数/秒|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN|
|データ ディスク書き込みバイト数/秒|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN|
|データ ディスク読み取り操作数/秒|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|LUN|
|データ ディスク書き込み操作数/秒|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|LUN|
|データ ディスクのキューの深さ|データ ディスクのキューの深さ (プレビュー)|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|LUN|
|OS ディスク読み取りバイト数/秒|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|なし|
|OS ディスク書き込みバイト数/秒|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|なし|
|OS ディスク読み取り操作数/秒|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|なし|
|OS ディスク書き込み操作数/秒|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|なし|
|OS ディスクのキューの深さ|OS ディスクのキューの深さ (プレビュー)|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|なし|
|受信フロー数|受信フロー数|Count|Average|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|なし|
|送信フロー数|送信フロー数|Count|Average|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|なし|
|受信フローの最大作成速度|受信フローの最大作成速度 (プレビュー)|CountPerSecond|Average|受信フローの最大作成速度 (VM に送信されるトラフィック)|なし|
|送信フローの最大作成速度|送信フローの最大作成速度 (プレビュー)|CountPerSecond|Average|送信フローの最大作成速度 (VM から送信されるトラフィック)|なし|
|Premium データ ディスク キャッシュ読み取りヒット|Premium データ ディスク キャッシュ読み取りヒット (プレビュー)|Percent|Average|Premium データ ディスク キャッシュ読み取りヒット|LUN|
|Premium データ ディスク キャッシュ読み取りミス|Premium データ ディスク キャッシュ読み取りミス (プレビュー)|Percent|Average|Premium データ ディスク キャッシュ読み取りミス|LUN|
|Premium OS ディスク キャッシュ読み取りヒット|Premium OS ディスク キャッシュ読み取りヒット (プレビュー)|Percent|Average|Premium OS ディスク キャッシュ読み取りヒット|なし|
|Premium OS ディスク キャッシュ読み取りミス|Premium OS ディスク キャッシュ読み取りミス (プレビュー)|Percent|Average|Premium OS ディスク キャッシュ読み取りミス|なし|
|受信ネットワーク合計|受信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|なし|
|送信ネットワーク合計|送信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|なし|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|Count|Average|すべてのコアの CPU 使用率 (ミリコア単位)。|containerName|
|MemoryUsage|メモリ使用量|バイト|Average|合計メモリ使用量 (バイト単位)。|containerName|
|NetworkBytesReceivedPerSecond|1 秒あたりの受信ネットワーク バイト数|バイト|Average|1 秒あたりの受信ネットワーク バイト数。|なし|
|NetworkBytesTransmittedPerSecond|1 秒あたりの送信ネットワーク バイト数|バイト|Average|1 秒あたりの送信ネットワーク バイト数。|なし|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalPullCount|プルの合計数|Count|Average|イメージのプルの合計数|なし|
|SuccessfulPullCount|成功したプルの数|Count|Average|成功したイメージ プルの数|なし|
|TotalPushCount|合計のプッシュ数|Count|Average|イメージのプッシュの合計数|なし|
|SuccessfulPushCount|成功したプッシュの数|Count|Average|成功したイメージ プッシュの数|なし|
|RunDuration|実行継続時間|ミリ秒|合計|実行継続時間 (ミリ秒単位)|なし|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|マネージド クラスターで使用可能な CPU コアの合計数|Count|Average|マネージド クラスターで使用可能な CPU コアの合計数|なし|
|kube_node_status_allocatable_memory_bytes|マネージド クラスターで使用可能なメモリの合計量|バイト|Average|マネージド クラスターで使用可能なメモリの合計量|なし|
|kube_pod_status_ready|準備完了状態のポッドの数|Count|Average|準備完了状態のポッドの数|namespace、pod|
|kube_node_status_condition|さまざまなノードの状態の条件|Count|Average|さまざまなノードの状態の条件|condition、status、status2、node|
|kube_pod_status_phase|フェーズごとのポッドの数|Count|Average|フェーズごとのポッドの数|phase、namespace、pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfullRequests|成功した要求|Count|合計|カスタム プロバイダーによる正常に完了した要求|HttpMethod、CallPath、StatusCode|
|FailedRequests|失敗した要求|Count|合計|カスタム リソース プロバイダーの使用可能なログを取得します|HttpMethod、CallPath、StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|NICReadThroughput|読み取りスループット (ネットワーク)|BytesPerSecond|Average|ゲートウェイのすべてのボリュームに対するレポート期間におけるデバイスでのネットワーク インターフェイスの読み取りスループット。|InstanceName|
|NICWriteThroughput|書き込みスループット (ネットワーク)|BytesPerSecond|Average|ゲートウェイのすべてのボリュームに対するレポート期間におけるデバイスでのネットワーク インターフェイスの書き込みスループット。|InstanceName|
|CloudReadThroughputPerShare|クラウドのダウンロード スループット (共有)|BytesPerSecond|Average|レポート期間中の共有から Azure へのダウンロード スループット。|共有|
|CloudUploadThroughputPerShare|クラウドへのアップロード時のスループット (共有)|BytesPerSecond|Average|レポート期間中の共有から Azure へのアップロード スループット。|共有|
|BytesUploadedToCloudPerShare|クラウドのアップロードされたバイト数 (共有)|バイト|Average|レポート期間中に共有から Azure にアップロードされるバイトの合計数。|共有|
|TotalCapacity|合計容量|バイト|Average|合計容量|なし|
|AvailableCapacity|使用可能な容量|バイト|Average|レポート期間中の使用可能な容量 (バイト単位)。|なし|
|CloudUploadThroughput|クラウド アップロード スループット|BytesPerSecond|Average|レポート期間中の Azure へのクラウド アップロード スループット。|なし|
|CloudReadThroughput|クラウド ダウンロード スループット|BytesPerSecond|Average|レポート期間中の Azure へのクラウド ダウンロード スループット。|なし|
|BytesUploadedToCloud|クラウドのアップロードされたバイト数 (デバイス)|バイト|Average|レポート期間中にデバイスから Azure にアップロードされるバイトの合計数。|なし|
|HyperVVirtualProcessorUtilization|Edge コンピューティング - CPU 使用率|Percent|Average|CPU 使用率 (パーセント)|InstanceName|
|HyperVMemoryUtilization|Edge コンピューティング - メモリ使用量|Percent|Average|使用中の RAM の容量|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|FailedRuns|失敗した実行|Count|合計||pipelineName、activityName|
|SuccessfulRuns|成功した実行|Count|合計||pipelineName、activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PipelineFailedRuns|失敗したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|PipelineSucceededRuns|成功したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|PipelineCancelledRuns|キャンセルしたパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|ActivityFailedRuns|失敗したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|成功したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|ActivityCancelledRuns|キャンセルしたアクティビティの実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|TriggerFailedRuns|失敗したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|TriggerSucceededRuns|成功したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|TriggerCancelledRuns|キャンセルしたトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|IntegrationRuntimeCpuPercentage|統合実行時の CPU 使用率|Percent|Average||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableMemory|統合実行時に使用可能なメモリ|バイト|Average||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|統合ランタイム キューの期間|Seconds|Average||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|統合ランタイム キューの長さ|Count|Average||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|統合ランタイムの使用可能なノード数|Count|Average||IntegrationRuntimeName|
|MaxAllowedResourceCount|許可されているエンティティの最大数|Count|最大値||なし|
|MaxAllowedFactorySizeInGbUnits|許可されている最大の工場出荷時サイズ (GB 単位)|Count|最大値||なし|
|ResourceCount|エンティティの合計数|Count|最大値||なし|
|FactorySizeInGbUnits|合計の工場出荷時サイズ (GB 単位)|Count|最大値||なし|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs (成功したジョブ)|Count|合計|成功したジョブの数。|なし|
|JobEndedFailure|Failed Jobs (失敗したジョブ)|Count|合計|失敗したジョブの数。|なし|
|JobEndedCancelled|Cancelled Jobs (取り消されたジョブ)|Count|合計|取り消されたジョブの数。|なし|
|JobAUEndedSuccess|Successful AU Time (成功した AU 時間)|Seconds|合計|成功したジョブの AU 時間の合計。|なし|
|JobAUEndedFailure|Failed AU Time (失敗した AU 時間)|Seconds|合計|失敗したジョブの AU 時間の合計。|なし|
|JobAUEndedCancelled|Cancelled AU Time (取り消された AU 時間)|Seconds|合計|取り消されたジョブの AU 時間の合計。|なし|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|保存量の合計|バイト|最大値|アカウントに保存されたデータの総量。|なし|
|DataWritten|データ書き込み量|バイト|合計|アカウントに書き込まれたデータの総量。|なし|
|DataRead|データ読み取り量|バイト|合計|アカウントから読み取られたデータの総量。|なし|
|WriteRequests|書き込み要求|Count|合計|アカウントへのデータ書き込み要求の数。|なし|
|ReadRequests|読み取り要求|Count|合計|アカウントへのデータ読み取り要求の数。|なし|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|Average|CPU 使用率|なし|
|memory_percent|メモリの割合|Percent|Average|メモリの割合|なし|
|io_consumption_percent|IO の割合|Percent|Average|IO の割合|なし|
|storage_percent|ストレージの割合|Percent|Average|ストレージの割合|なし|
|storage_used|使用済みストレージ|バイト|Average|使用済みストレージ|なし|
|storage_limit|ストレージの制限|バイト|最大値|ストレージの制限|なし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|Average|サーバー ログ ストレージの割合|なし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|バイト|Average|サーバー ログ ストレージの使用量|なし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|バイト|Average|サーバー ログ ストレージの上限|なし|
|active_connections|アクティブな接続|Count|Average|アクティブな接続|なし|
|connections_failed|失敗した接続|Count|合計|失敗した接続|なし|
|seconds_behind_master|レプリケーションのラグ (秒単位)|Count|最大値|レプリケーションのラグ (秒単位)|なし|
|backup_storage_used|使用されたバックアップ ストレージ|バイト|Average|使用されたバックアップ ストレージ|なし|
|network_bytes_egress|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|なし|
|network_bytes_ingress|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|なし|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|Average|CPU 使用率|なし|
|memory_percent|メモリの割合|Percent|Average|メモリの割合|なし|
|io_consumption_percent|IO の割合|Percent|Average|IO の割合|なし|
|storage_percent|ストレージの割合|Percent|Average|ストレージの割合|なし|
|storage_used|使用済みストレージ|バイト|Average|使用済みストレージ|なし|
|storage_limit|ストレージの制限|バイト|最大値|ストレージの制限|なし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|Average|サーバー ログ ストレージの割合|なし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|バイト|Average|サーバー ログ ストレージの使用量|なし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|バイト|最大値|サーバー ログ ストレージの上限|なし|
|active_connections|アクティブな接続|Count|Average|アクティブな接続|なし|
|connections_failed|失敗した接続|Count|合計|失敗した接続|なし|
|seconds_behind_master|レプリケーションのラグ (秒単位)|Count|最大値|レプリケーションのラグ (秒単位)|なし|
|backup_storage_used|使用されたバックアップ ストレージ|バイト|Average|使用されたバックアップ ストレージ|なし|
|network_bytes_egress|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|なし|
|network_bytes_ingress|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|なし|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|Average|CPU 使用率|なし|
|memory_percent|メモリの割合|Percent|Average|メモリの割合|なし|
|io_consumption_percent|IO の割合|Percent|Average|IO の割合|なし|
|storage_percent|ストレージの割合|Percent|Average|ストレージの割合|なし|
|storage_used|使用済みストレージ|バイト|Average|使用済みストレージ|なし|
|storage_limit|ストレージの制限|バイト|最大値|ストレージの制限|なし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|Average|サーバー ログ ストレージの割合|なし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|バイト|Average|サーバー ログ ストレージの使用量|なし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|バイト|最大値|サーバー ログ ストレージの上限|なし|
|active_connections|アクティブな接続|Count|Average|アクティブな接続|なし|
|connections_failed|失敗した接続|Count|合計|失敗した接続|なし|
|backup_storage_used|使用されたバックアップ ストレージ|バイト|Average|使用されたバックアップ ストレージ|なし|
|network_bytes_egress|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|なし|
|network_bytes_ingress|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|なし|
|pg_replica_log_delay_in_seconds|Replica Lag (レプリカ ラグ)|Seconds|最大値|レプリカのラグ (秒単位)|なし|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (レプリカ間の最大ラグ)|バイト|最大値|最も遅れているレプリカのラグ (バイト単位)|なし|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|Average|CPU 使用率|なし|
|memory_percent|メモリの割合|Percent|Average|メモリの割合|なし|
|iops|IOPS|Count|Average|1 秒あたりの IO 操作回数|なし|
|storage_percent|ストレージの割合|Percent|Average|ストレージの割合|なし|
|storage_used|使用済みストレージ|バイト|Average|使用済みストレージ|なし|
|active_connections|アクティブな接続|Count|Average|アクティブな接続|なし|
|network_bytes_egress|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|なし|
|network_bytes_ingress|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|なし|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|テレメトリ メッセージ送信試行|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|なし|
|d2c.telemetry.ingress.success|送信済みテレメトリ メッセージ|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|なし|
|c2d.commands.egress.complete.success|C2D メッセージ配信完了|Count|合計|デバイスで正常に完了した Cloud to Device メッセージ配信の数|なし|
|c2d.commands.egress.abandon.success|破棄した C2D メッセージ|Count|合計|デバイスで中止された Cloud to Device メッセージの数|なし|
|c2d.commands.egress.reject.success|C2D メッセージ拒否数|Count|合計|デバイスで拒否された Cloud to Device メッセージの数|なし|
|C2DMessagesExpired|期限切れ C2D メッセージ (プレビュー)|Count|合計|有効期限が切れた cloud-to-device メッセージの数|なし|
|devices.totalDevices|デバイスの総数 (非推奨)|Count|合計|IoT Hub に登録されたデバイスの数|なし|
|devices.connectedDevices.allProtocol|接続されているデバイス (非推奨) |Count|合計|IoT Hub に接続されているデバイスの数|なし|
|d2c.telemetry.egress.success|ルーティング: 配信されたテレメトリ メッセージ|Count|合計|IoT Hub ルーティングを使用して、メッセージがすべてのエンドポイントに正常に配信された回数。 メッセージが複数のエンドポイントにルーティングされている場合、この値は正常に配信されるたびに 1 つずつ増えます。 メッセージが同じエンドポイントに複数回配信されている場合、この値は正常に配信されるたびに 1 つずつ増えます。|なし|
|d2c.telemetry.egress.dropped|ルーティング: 破棄されたテレメトリ メッセージ |Count|合計|デッド エンドポイントであるため、メッセージが IoT Hub ルーティングで破棄された回数。 この値では、フォールバック ルートに配信されるメッセージはカウントされません。破棄されたメッセージはそこには配信されないためです。|なし|
|d2c.telemetry.egress.orphaned|ルーティング: 孤立したテレメトリ メッセージ |Count|合計|どのルーティング規則 (フォールバック規則を含む) にも一致しなかっため、メッセージが IoT Hub ルーティングによって孤立した回数。 |なし|
|d2c.telemetry.egress.invalid|ルーティング: 互換性のないテレメトリ メッセージ|Count|合計|エンドポイントと互換性がないため、IoT Hub ルーティングでメッセージを配信できなかった回数。 この値に再試行回数は含まれません。|なし|
|d2c.telemetry.egress.fallback|ルーティング: フォールバックのために配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、フォールバック ルートに関連付けられているエンドポイントにメッセージが配信された回数。|なし|
|d2c.endpoints.egress.eventHubs|ルーティング: Event Hub に配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Event Hub エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.eventHubs|ルーティング: Event Hub のメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Event Hub エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.serviceBusQueues|ルーティング: Service Bus キューに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus キュー エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.serviceBusQueues|ルーティング: Service Bus キューのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus キュー エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.serviceBusTopics|ルーティング: Service Bus トピックに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus トピック エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.serviceBusTopics|ルーティング: Service Bus トピックのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus トピック エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.builtIn.events|ルーティング: メッセージ/イベントに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが組み込みのエンドポイント (メッセージ/イベント) に正常に配信された回数。|なし|
|d2c.endpoints.latency.builtIn.events|ルーティング: メッセージ/イベントのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.storage|ルーティング: ストレージに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージがストレージ エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.storage|ルーティング: ストレージのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージとストレージ エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.storage.bytes|ルーティング: ストレージに配信されたデータ|バイト|合計|IoT Hub ルーティングでストレージ エンドポイントに配信されたデータの量 (バイト)。|なし|
|d2c.endpoints.egress.storage.blobs|ルーティング: ストレージに配信された BLOB|Count|合計|IoT Hub ルーティングで BLOB がストレージ エンドポイントに配信された回数。|なし|
|EventGridDeliveries|Event Grid 配信 (プレビュー)|Count|合計|Event Grid に発行された IoT Hub イベントの数。 成功および失敗した要求の数には、Result ディメンションを使用します。 EventType ディメンションはイベントの種類 (https://aka.ms/ioteventgrid) ) を示します。|ResourceId、Result、EventType|
|EventGridLatency|Event Grid の待機時間 (プレビュー)|ミリ秒|Average|oT Hub イベントが生成されてから、そのイベントが Event Grid に発行されるまでの平均待機時間 (ミリ秒)。 この数は、すべてのイベントの種類の間の平均値です。 特定の種類のイベントの待機時間を確認するには、EventType ディメンションを使用します。|ResourceId、EventType|
|d2c.twin.read.success|成功したデバイスからのツイン読み取り|Count|合計|デバイスが開始して成功したツイン読み取りの数。|なし|
|d2c.twin.read.failure|失敗したデバイスからのツイン読み取り|Count|合計|デバイスが開始したツイン読み取りの失敗数。|なし|
|d2c.twin.read.size|デバイスからのツイン読み取りの応答サイズ|バイト|Average|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|なし|
|d2c.twin.update.success|成功したデバイスからのツイン更新|Count|合計|デバイスが開始して成功したツイン更新の数。|なし|
|d2c.twin.update.failure|失敗したデバイスからのツイン更新|Count|合計|デバイスが開始して失敗したツイン更新の数。|なし|
|d2c.twin.update.size|デバイスからのツイン更新のサイズ|バイト|Average|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|なし|
|c2d.methods.success|成功したダイレクト メソッドの呼び出し|Count|合計|成功したダイレクト メソッドの呼び出しの数。|なし|
|c2d.methods.failure|失敗したダイレクト メソッドの呼び出し|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|なし|
|c2d.methods.requestSize|ダイレクト メソッドの呼び出しの要求サイズ|バイト|Average|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|なし|
|c2d.methods.responseSize|ダイレクト メソッドの呼び出しの応答サイズ|バイト|Average|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|なし|
|c2d.twin.read.success|成功したバック エンドからのツイン読み取り|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|なし|
|c2d.twin.read.failure|失敗したバックエンドからのツイン読み取り|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|なし|
|c2d.twin.read.size|バック エンドからのツイン読み取りの応答サイズ|バイト|Average|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|なし|
|c2d.twin.update.success|成功したバックエンドからのツイン更新|Count|合計|バックエンドが開始して成功したツイン更新の数。|なし|
|c2d.twin.update.failure|失敗したバック エンドからのツイン更新|Count|合計|バックエンドが開始して失敗したツイン更新の数。|なし|
|c2d.twin.update.size|バックエンドからのツイン更新のサイズ|バイト|Average|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|なし|
|twinQueries.success|成功したツイン クエリ|Count|合計|成功したツイン クエリの数。|なし|
|twinQueries.failure|失敗したツイン クエリ|Count|合計|失敗したツイン クエリの数。|なし|
|twinQueries.resultSize|ツイン クエリの結果のサイズ|バイト|Average|成功したツイン クエリの結果の平均、最小、および最大サイズ。|なし|
|jobs.createTwinUpdateJob.success|成功したツイン更新ジョブの作成|Count|合計|正常に作成されたツイン更新ジョブの数。|なし|
|jobs.createTwinUpdateJob.failure|失敗したツイン更新ジョブの作成|Count|合計|作成に失敗したツイン更新ジョブの数。|なし|
|jobs.createDirectMethodJob.success|成功したメソッド呼び出しジョブの作成|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|なし|
|jobs.createDirectMethodJob.failure|失敗したメソッド呼び出しジョブの作成|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|なし|
|jobs.listJobs.success|成功したジョブ一覧の呼び出し|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|なし|
|jobs.listJobs.failure|失敗したジョブ一覧の呼び出し|Count|合計|失敗したジョブ一覧の呼び出しの数。|なし|
|jobs.cancelJob.success|成功したジョブの取り消し|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|なし|
|jobs.cancelJob.failure|失敗したジョブの取り消し|Count|合計|失敗したジョブ取り消しの呼び出し。|なし|
|jobs.queryJobs.success|成功したジョブ クエリ|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|なし|
|jobs.queryJobs.failure|失敗したジョブ クエリ|Count|合計|失敗したジョブ クエリの呼び出しの数。|なし|
|jobs.completed|完了したジョブ|Count|合計|完了したジョブの数。|なし|
|jobs.failed|失敗したジョブ|Count|合計|失敗したジョブの数。|なし|
|d2c.telemetry.ingress.sendThrottle|調整エラーの数|Count|合計|デバイスのスループット調整による調整エラーの数|なし|
|dailyMessageQuotaUsed|使用されているメッセージの合計数|Count|Average|現在使用されているメッセージの合計数|なし|
|deviceDataUsage|デバイス データの合計使用量|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|なし|
|deviceDataUsageV2|デバイス データの合計使用量 (プレビュー)|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|なし|
|totalDeviceCount|デバイスの合計数 (プレビュー)|Count|Average|IoT Hub に登録されたデバイスの数|なし|
|connectedDeviceCount|接続されているデバイス (プレビュー)|Count|Average|IoT Hub に接続されているデバイスの数|なし|
|configuration|構成メトリック|Count|合計|構成操作のメトリック|なし|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts (登録試行回数)|Count|合計|試行されたデバイス登録の回数|ProvisioningServiceName、IotHubName、Status|
|DeviceAssignments|Devices assigned (割り当て済みデバイス数)|Count|合計|IoT Hub に割り当てられているデバイスの数|ProvisioningServiceName、IotHubName|
|AttestationAttempts|Attestation attempts (構成証明試行回数)|Count|合計|試行されたデバイス構成証明の回数|ProvisioningServiceName、Status、Protocol|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AddRegion|追加されたリージョン|Count|Count|追加されたリージョン|リージョン|
|AvailableStorage|使用可能なストレージ|バイト|合計|5 分単位の細分性で報告された使用可能なストレージの合計|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|Cassandra 接続の終了|Count|合計|1 分単位の細分性で報告された、閉じられた Cassandra 接続の数|APIType、Region、ClosureReason|
|CassandraRequestCharges|Cassandra 要求の料金|Count|合計|行われた Cassandra 要求で使用された RU|APIType、DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|Cassandra 要求|Count|Count|行われた Cassandra 要求の数|APIType、DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|CreateAccount|作成されたアカウント|Count|Count|作成されたアカウント|なし|
|DataUsage|データ利用状況|バイト|合計|5 分単位の細分性で報告されたデータ使用量の合計|CollectionName、DatabaseName、Region|
|DeleteAccount|削除されたアカウント|Count|Count|削除されたアカウント|なし|
|DocumentCount|ドキュメント数|Count|合計|5 分単位の細分性で報告されたドキュメントの合計数|CollectionName、DatabaseName、Region|
|DocumentQuota|ドキュメントのクォータ|バイト|合計|5 分単位の細分性で報告されたストレージ クォータの合計|CollectionName、DatabaseName、Region|
|IndexUsage|インデックスの使用量|バイト|合計|5 分単位の細分性で報告されたインデックス使用量の合計|CollectionName、DatabaseName、Region|
|MetadataRequests|メタデータの要求数|Count|Count|メタデータの要求数。 Cosmos DB はメタデータ コレクションをアカウントごとに保持します。これにより、コレクションやデータベースなどとそれらの構成を無料で列挙できます。|DatabaseName、CollectionName、Region、StatusCode、Role|
|MongoRequestCharge|Mongo 要求の料金|Count|合計|使用された Mongo 要求の単位数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequests|Mongo Requests (Mongo 要求数)|Count|Count|実行された Mongo 要求の数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsCount|Mongo 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 要求の数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsDelete|Mongo Delete 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 削除要求の数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsInsert|Mongo Insert 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 挿入数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsQuery|Mongo Query 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo クエリ要求数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsUpdate|Mongo 更新要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 更新要求数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|ProvisionedThroughput|プロビジョニングされたスループット|Count|最大値|プロビジョニングされたスループット|DatabaseName、CollectionName|
|RegionFailover|リージョンのフェールオーバー|Count|Count|リージョンのフェールオーバー|なし|
|RemoveRegion|削除されたリージョン|Count|Count|削除されたリージョン|リージョン|
|ReplicationLatency|P99 のレプリケーション待機時間|MilliSeconds|Average|geo 対応アカウントのソースおよびターゲット リージョン全体の P99 のレプリケーション待機時間|SourceRegion、TargetRegion|
|ServiceAvailability|サービスの可用性|Percent|Average|アカウントは 1 時間、1 日、または 1 か月単位の細分性で可用性を要求します|なし|
|TotalRequestUnits|合計要求単位数|Count|合計|消費された要求の単位数|DatabaseName、CollectionName、Region、StatusCode、OperationType|
|TotalRequests|要求の合計数|Count|Count|行われた要求の数|DatabaseName、CollectionName、Region、StatusCode、OperationType|
|UpdateAccountKeys|更新されたアカウント キー|Count|Count|更新されたアカウント キー|KeyType|
|UpdateAccountNetworkSettings|更新されたアカウント ネットワーク設定|Count|Count|更新されたアカウント ネットワーク設定|なし|
|UpdateAccountReplicationSettings|更新されたアカウント レプリケーション設定|Count|Count|更新されたアカウント レプリケーション設定|なし|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TransactionCount|トランザクション数|Count|Count|トランザクションの合計数|TransactionCount|
|SuccessCount|成功数|Count|Count|成功したトランザクション数|SuccessCount|
|FailureCount|失敗数|Count|Count|失敗したトランザクション数|FailureCount|
|SuccessLatency|成功の待機時間|MilliSeconds|Average|成功したトランザクションの待機時間|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|トピック|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|Topic、ErrorType、Error|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|なし|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|なし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|なし|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|なし|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|なし|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|なし|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|なし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|なし|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求|Count|合計|Microsoft.EventHub の成功した要求数。|EntityName、OperationResult|
|ServerErrors|サーバー エラー。|Count|合計|Microsoft.EventHub のサーバー エラー数。|EntityName、OperationResult|
|UserErrors|ユーザー エラー数。|Count|合計|Microsoft.EventHub のユーザー エラー数。|EntityName、OperationResult|
|QuotaExceededErrors|クォータ超過エラー数。|Count|合計|Microsoft.EventHub のクォータ超過エラー数。|EntityName、OperationResult|
|ThrottledRequests|スロットルされた要求数。|Count|合計|Microsoft.EventHub のスロットルされた要求数。|EntityName、OperationResult|
|IncomingRequests|受信要求|Count|合計|Microsoft.EventHub の受信要求数。|EntityName|
|IncomingMessages|受信メッセージ|Count|合計|Microsoft.EventHub の受信メッセージ数。|EntityName|
|OutgoingMessages|送信メッセージ|Count|合計|Microsoft.EventHub の送信メッセージ数。|EntityName|
|IncomingBytes|着信バイト数。|バイト|合計|Microsoft.EventHub の受信バイト数。|EntityName|
|OutgoingBytes|発信バイト数。|バイト|合計|Microsoft.EventHub の発信バイト数。|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Microsoft.EventHub のアクティブな接続の合計数。|なし|
|ConnectionsOpened|開かれている接続数。|Count|Average|Microsoft.EventHub の開かれている接続数。|EntityName|
|ConnectionsClosed|切断された接続数。|Count|Average|Microsoft.EventHub の切断された接続数。|EntityName|
|CaptureBacklog|バックログのキャプチャ。|Count|合計|Microsoft.EventHub のバックログをキャプチャします。|EntityName|
|CapturedMessages|キャプチャされたメッセージ数。|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。|EntityName|
|CapturedBytes|キャプチャされたバイト数。|バイト|合計|Microsoft.EventHub のキャプチャされたバイト数。|EntityName|
|Size|Size|バイト|Average|EventHub のサイズ (バイト単位)。|EntityName|
|INREQS|受信要求数 (非推奨)|Count|合計|名前空間に対して受信した送信要求の合計数 (非推奨)|なし|
|SUCCREQ|成功した要求数 (非推奨)|Count|合計|名前空間の成功した要求の合計数 (非推奨)|なし|
|FAILREQ|失敗した要求数 (非推奨)|Count|合計|名前空間の失敗した要求の合計数 (非推奨)|なし|
|SVRBSY|サーバー ビジー エラー数 (非推奨)|Count|合計|名前空間のサーバー ビジー エラーの合計数 (非推奨)|なし|
|INTERR|内部サーバー エラー数 (非推奨)|Count|合計|名前空間の内部サーバー エラーの合計数 (非推奨)|なし|
|MISCERR|その他のエラー数 (非推奨)|Count|合計|名前空間の失敗した要求の合計数 (非推奨)|なし|
|INMSGS|受信メッセージ (廃止) (非推奨)|Count|合計|名前空間の受信メッセージの総数。 このメトリックは非推奨です。 代わりに、"受信メッセージ" メトリックを使ってください (非推奨)|なし|
|EHINMSGS|受信メッセージ (非推奨)|Count|合計|名前空間の受信メッセージの合計数 (非推奨)|なし|
|OUTMSGS|送信メッセージ (廃止) (非推奨)|Count|合計|名前空間の送信メッセージの総数。 このメトリックは非推奨です。 代わりに、"送信メッセージ" メトリックを使ってください (非推奨)|なし|
|EHOUTMSGS|送信メッセージ (非推奨)|Count|合計|名前空間の送信メッセージの合計数 (非推奨)|なし|
|EHINMBS|受信バイト数 (廃止) (非推奨)|バイト|合計|名前空間の Event Hub 受信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"受信バイト数" メトリックを使ってください (非推奨)|なし|
|EHINBYTES|受信バイト数 (非推奨)|バイト|合計|名前空間の Event Hub 受信メッセージのスループット (非推奨)|なし|
|EHOUTMBS|送信バイト数 (廃止) (非推奨)|バイト|合計|名前空間の Event Hub 送信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"発信バイト数" メトリックを使ってください (非推奨)|なし|
|EHOUTBYTES|送信バイト数 (非推奨)|バイト|合計|名前空間の Event Hub 送信メッセージのスループット (非推奨)|なし|
|EHABL|アーカイブ バックログ メッセージ数 (非推奨)|Count|合計|名前空間のバックログ内の Event Hub アーカイブ メッセージ数 (非推奨)|なし|
|EHAMSGS|アーカイブ メッセージ数 (非推奨)|Count|合計|名前空間内の Event Hub アーカイブ メッセージ数 (非推奨)|なし|
|EHAMBS|アーカイブ メッセージ スループット (非推奨)|バイト|合計|名前空間内の Event Hub アーカイブ メッセージ スループット (非推奨)|なし|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求|Count|合計|Microsoft.EventHub の成功した要求数。|OperationResult|
|ServerErrors|サーバー エラー。|Count|合計|Microsoft.EventHub のサーバー エラー数。|OperationResult|
|UserErrors|ユーザー エラー数。|Count|合計|Microsoft.EventHub のユーザー エラー数。|OperationResult|
|QuotaExceededErrors|クォータ超過エラー数。|Count|合計|Microsoft.EventHub のクォータ超過エラー数。|OperationResult|
|ThrottledRequests|スロットルされた要求数。|Count|合計|Microsoft.EventHub のスロットルされた要求数。|OperationResult|
|IncomingRequests|受信要求|Count|合計|Microsoft.EventHub の受信要求数。|なし|
|IncomingMessages|受信メッセージ|Count|合計|Microsoft.EventHub の受信メッセージ数。|なし|
|OutgoingMessages|送信メッセージ|Count|合計|Microsoft.EventHub の送信メッセージ数。|なし|
|IncomingBytes|着信バイト数。|バイト|合計|Microsoft.EventHub の受信バイト数。|なし|
|OutgoingBytes|発信バイト数。|バイト|合計|Microsoft.EventHub の発信バイト数。|なし|
|ActiveConnections|ActiveConnections|Count|Average|Microsoft.EventHub のアクティブな接続の合計数。|なし|
|ConnectionsOpened|開かれている接続数。|Count|Average|Microsoft.EventHub の開かれている接続数。|なし|
|ConnectionsClosed|切断された接続数。|Count|Average|Microsoft.EventHub の切断された接続数。|なし|
|CaptureBacklog|バックログのキャプチャ。|Count|合計|Microsoft.EventHub のバックログをキャプチャします。|なし|
|CapturedMessages|キャプチャされたメッセージ数。|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。|なし|
|CapturedBytes|キャプチャされたバイト数。|バイト|合計|Microsoft.EventHub のキャプチャされたバイト数。|なし|
|CPU|CPU|Percent|最大値|イベント ハブ クラスターの CPU 使用率 (%)|Role|
|AvailableMemory|使用可能なメモリ|Percent|最大値|メモリの合計に対する割合としてイベント ハブ クラスターで使用可能なメモリ。|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|GatewayRequests|ゲートウェイ要求数|Count|合計|ゲートウェイ要求の数|HttpStatus|
|CategorizedGatewayRequests|カテゴリ別のゲートウェイ要求数|Count|合計|カテゴリ別のゲートウェイ要求の数 (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|アクティブなワーカーの数|Count|最大値|アクティブなワーカーの数|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|実際のメトリック値|Count|Average|実行時に自動スケールによって計算された値|MetricTriggerSource|
|MetricThreshold|メトリックのしきい値|Count|Average|自動スケールの実行時に構成されていた自動スケールのしきい値。|MetricTriggerRule|
|ObservedCapacity|実際の容量|Count|Average|実行時に自動スケールに報告された容量。|なし|
|ScaleActionsInitiated|スケール アクション開始|Count|合計|スケール操作の指示。|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|可用性|Percent|Average|正常に完了した可用性テストの割合|availabilityResult/name、availabilityResult/location|
|availabilityResults/count|可用性テスト|Count|Count|可用性テストの数|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|availabilityResults/duration|可用性テスト継続時間|MilliSeconds|Average|可用性テスト継続時間|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|browserTimings/networkDuration|ページ読み込みのネットワーク接続時間|MilliSeconds|Average|ユーザー要求からネットワーク接続までの時間。 DNS の参照とトランスポートの接続が含まれます。|なし|
|browserTimings/processingDuration|クライアントの処理時間|MilliSeconds|Average|ドキュメントの最終バイトを受信してから、DOM が読み込まれるまでの時間。 非同期要求がまだ処理されている可能性があります。|なし|
|browserTimings/receiveDuration|受信応答時間|MilliSeconds|Average|最初のバイトから最後のバイトまで、または切断までの時間。|なし|
|browserTimings/sendDuration|要求送信時間|MilliSeconds|Average|ネットワーク接続から、最初のバイトを受信するまでの時間。|なし|
|browserTimings/totalDuration|ブラウザーのページ読み込み時間|MilliSeconds|Average|ユーザーが要求を出してから DOM、スタイル シート、スクリプト、およびイメージが読み込まれるまでの時間。|なし|
|dependencies/count|依存関係呼び出し|Count|Count|外部リソースに対するアプリケーションからの呼び出しの数。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、operation/synthetic、cloud/roleInstance、cloud/roleName|
|dependencies/duration|依存関係の期間|MilliSeconds|Average|外部リソースに対するアプリケーションからの呼び出しの期間。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、operation/synthetic、cloud/roleInstance、cloud/roleName|
|dependencies/failed|依存関係呼び出しの失敗回数|Count|Count|外部リソースに対するアプリケーションからの失敗した依存関係呼び出しの数。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、operation/synthetic、cloud/roleInstance、cloud/roleName|
|pageViews/count|ページ ビュー|Count|Count|ページ ビューの数。|operation/synthetic、cloud/roleName|
|pageViews/duration|ページ ビューの読み込み時間|MilliSeconds|Average|ページ ビューの読み込み時間|operation/synthetic、cloud/roleName|
|performanceCounters/requestExecutionTime|HTTP 要求の実行時間|MilliSeconds|Average|最新の要求の実行時間。|cloud/roleInstance|
|performanceCounters/requestsInQueue|アプリケーション キュー内の HTTP 要求|Count|Average|アプリケーション要求キューの長さ。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 要求率|CountPerSecond|Average|ASP.NET からの 1 秒あたりのアプリケーションへのすべての要求のレート。|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|例外レート|CountPerSecond|Average|.NET 例外、および .NET 例外に変換されたアンマネージ例外を含む、Windows に報告された処理済み例外と未処理の例外の数。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|プロセス IO 量|BytesPerSecond|Average|ファイル、ネットワーク、およびデバイスに対する読み書きで 1 秒あたりに処理される合計バイト数。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|プロセス CPU|Percent|Average|すべてのプロセス スレッドで命令を実行するためにプロセッサを使用した経過時間の割合。 これは 0 から 100 の間で変化することがあります。 このメトリックは、w3wp プロセスのみのパフォーマンスを示します。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|プロセッサ時間|Percent|Average|プロセッサによって非アイドル スレッドで費やされる時間の割合。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|使用可能なメモリ|バイト|Average|プロセスへの割り当てまたはシステムの使用にすぐに利用可能な物理メモリ。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|プロセスのプライベート バイト|バイト|Average|監視対象のアプリケーション プロセスに対して専用に割り当てられるメモリ。|cloud/roleInstance|
|requests/duration|サーバーの応答時間|MilliSeconds|Average|HTTP 要求を受信してから、応答の送信を終了するまでの時間。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/count|サーバー要求|Count|Count|完了した HTTP 要求の数。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/failed|失敗した要求|Count|Count|失敗としてマークされた HTTP 要求の数。 ほとんどの場合、応答コードが 400 以上で、かつ 401 に等しくない要求です。|request/performanceBucket、request/resultCode、request/success,operation/synthetic、cloud/roleInstance、cloud/roleName|
|要求/率|サーバー要求率|CountPerSecond|Average|1 秒あたりのサーバー要求数による率|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|exceptions/count|例外|Count|Count|キャッチされていないすべての例外の合計数。|cloud/roleName、cloud/roleInstance、client/type|
|exceptions/browser|ブラウザーの例外|Count|Count|ブラウザーでスローされた、キャッチされない例外の数。|client/isServer、cloud/roleName|
|exceptions/server|サーバーの例外|Count|Count|サーバー アプリケーションでスローされ、キャッチされていない例外の数。|client/isServer、cloud/roleName、cloud/roleInstance|
|traces/count|トレース|Count|Count|トレース ドキュメント数|trace/severityLevel、operation/synthetic、cloud/roleName、cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|サービス API ヒット数の合計|Count|Count|サービス API ヒット数の合計|ActivityType、ActivityName|
|ServiceApiLatency|サービス API の全体的な待機時間|ミリ秒|Average|サービス API 要求の全体的な待機時間|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|ServiceApiResult|サービス API 結果数の合計|Count|Count|サービス API 結果数の合計|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|SaturationShoebox|コンテナーの全体的な飽和度|Percent|Average|使用されているコンテナーの容量|ActivityType、ActivityName、TransactionType|
|可用性|コンテナーの全体的な可用性|Percent|Average|コンテナー要求の可用性|ActivityType、ActivityName、StatusCode、StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CacheUtilization|キャッシュ使用率|Percent|Average|クラスター スコープ内の使用率レベル|なし|
|QueryDuration|クエリ実行時間|ミリ秒|Average|クエリの実行時間 (秒単位)|QueryStatus|
|IngestionUtilization|インジェストの使用率|Percent|Average|クラスターでのインジェスト スロットの使用率|なし|
|KeepAlive|キープ アライブ|Count|Average|サニティ チェックでは、クラスターがクエリに応答していることが示されます|なし|
|IngestionVolumeInMB|インジェストの量 (MB 単位)|Count|合計|クラスターに取り込まれたデータの全体的な量 (MB 単位)|データベース|
|IngestionLatencyInSeconds|インジェストの待機時間 (秒単位)|Seconds|Average|ソース (メッセージが EventHub 内にある場合など) からクラスターへのインジェスト時間 (秒単位)|なし|
|EventsProcessedForEventHubs|(Event/IoT Hubs の) 処理されたイベント|Count|合計|Event/IoT Hub からのインジェスト時に、クラスターによって処理されたイベントの数|EventStatus|
|IngestionResult|インジェストの結果|Count|Count|インジェスト操作の回数|IngestionResultDetails|
|CPU|CPU|Percent|Average|CPU 使用率レベル|なし|
|ContinuousExportNumOfRecordsExported|連続エクスポート - エクスポートされたレコードの数|Count|合計|エクスポート操作中に作成されたすべてのストレージ アーティファクトについてエクスポートされ実行されたレコードの数|なし|
|ExportUtilization|エクスポート使用率|Percent|最大値|エクスポート使用率|なし|
|ContinuousExportPendingCount|保留中の連続エクスポートの数|Count|最大値|実行の準備ができている保留中の連続エクスポート ジョブの数|なし|
|ContinuousExportMaxLatenessMinutes|連続エクスポートの最大遅延分数|Count|最大値|保留中で実行準備ができているすべての連続エクスポートの最大待機時間 (分)|なし|
|ContinuousExportResult|連続エクスポートの結果|Count|Count|連続エクスポートが成功したか失敗したかを示します|ContinuousExportName、Result、Database|
|StreamingIngestDuration|ストリーミング インジェストの期間|ミリ秒|Average|ストリーミング インジェスト時間 (ミリ秒)|なし|
|StreamingIngestDataRate|ストリーミング インジェストのデータ速度|Count|Average|ストリーミング インジェストのデータ速度 (MB/秒)|なし|
|SteamingIngestRequestRate|ストリーミング インジェストの要求率|Count|RateRequestsPerSecond|ストリーミング インジェストの要求率 (要求数/秒)|なし|
|StreamingIngestResults|ストリーミング インジェストの結果|Count|Average|ストリーミング インジェストの結果|結果|
|TotalNumberOfConcurrentQueries|同時クエリの合計数|Count|合計|同時クエリの合計数|なし|
|TotalNumberOfThrottledQueries|スロットルされたクエリの合計数|Count|合計|スロットルされたクエリの合計数|なし|
|TotalNumberOfThrottledCommands|スロットルされたコマンドの合計数|Count|合計|スロットルされたコマンドの合計数|CommandType|
|TotalNumberOfExtents|エクステントの合計数|Count|合計|データ エクステントの合計数|なし|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|開始した実行|Count|合計|開始したワークフロー実行の数。|なし|
|RunsCompleted|完了した実行|Count|合計|完了したワークフロー実行の数。|なし|
|RunsSucceeded|成功した実行|Count|合計|成功したワークフロー実行の数。|なし|
|RunsFailed|失敗した実行|Count|合計|失敗したワークフロー実行の数。|なし|
|RunsCancelled|キャンセルされた実行|Count|合計|キャンセルされたワークフロー実行の数。|なし|
|RunLatency|実行の待機時間|Seconds|Average|完了したワークフロー実行の待機時間。|なし|
|RunSuccessLatency|成功した実行の待機時間|Seconds|Average|成功したワークフロー実行の待機時間。|なし|
|RunThrottledEvents|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|なし|
|RunStartThrottledEvents|実行開始スロットル イベント数|Count|合計|ワークフロー実行開始スロットル イベントの数。|なし|
|RunFailurePercentage|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|なし|
|ActionsStarted|開始したアクション |Count|合計|開始したワークフロー アクションの数。|なし|
|ActionsCompleted|完了したアクション |Count|合計|完了したワークフロー アクションの数。|なし|
|ActionsSucceeded|成功したアクション |Count|合計|成功したワークフロー アクションの数。|なし|
|ActionsFailed|失敗したアクション |Count|合計|失敗したワークフロー アクションの数。|なし|
|ActionsSkipped|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|なし|
|ActionLatency|アクションの待機時間 |Seconds|Average|完了したワークフロー アクションの待機時間。|なし|
|ActionSuccessLatency|成功したアクションの待機時間 |Seconds|Average|成功したワークフロー アクションの待機時間。|なし|
|ActionThrottledEvents|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|なし|
|TriggersStarted|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|なし|
|TriggersCompleted|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|なし|
|TriggersSucceeded|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|なし|
|TriggersFailed|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|なし|
|TriggersSkipped|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|なし|
|TriggersFired|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|なし|
|TriggerLatency|トリガーの待機時間 |Seconds|Average|完了したワークフロー トリガーの待機時間。|なし|
|TriggerFireLatency|起動されたトリガーの待機時間 |Seconds|Average|起動されたワークフロー トリガーの待機時間。|なし|
|TriggerSuccessLatency|成功したトリガーの待機時間 |Seconds|Average|成功したワークフロー トリガーの待機時間。|なし|
|TriggerThrottledEvents|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|なし|
|BillableActionExecutions|課金対象のアクションの実行|Count|合計|課金されるワークフロー アクションの実行数。|なし|
|BillableTriggerExecutions|課金対象のトリガーの実行|Count|合計|課金されるワークフロー トリガーの実行数。|なし|
|TotalBillableExecutions|課金対象の実行の合計数|Count|合計|課金されるワークフローの実行数。|なし|
|BillingUsageNativeOperation|ネイティブ操作実行の利用状況に応じた課金|Count|合計|請求対象となるネイティブ操作の実行回数。|なし|
|BillingUsageStandardConnector|Standard コネクタ実行の利用状況に応じた課金|Count|合計|請求対象となる Standard コネクタの実行回数。|なし|
|BillingUsageStorageConsumption|ストレージ使用実行の利用状況に応じた課金|Count|合計|請求対象となるストレージ使用の実行回数。|なし|
|BillingUsageNativeOperation|ネイティブ操作実行の利用状況に応じた課金|Count|合計|請求対象となるネイティブ操作の実行回数。|なし|
|BillingUsageStandardConnector|Standard コネクタ実行の利用状況に応じた課金|Count|合計|請求対象となる Standard コネクタの実行回数。|なし|
|BillingUsageStorageConsumption|ストレージ使用実行の利用状況に応じた課金|Count|合計|請求対象となるストレージ使用の実行回数。|なし|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|開始した実行|Count|合計|開始したワークフロー実行の数。|なし|
|RunsCompleted|完了した実行|Count|合計|完了したワークフロー実行の数。|なし|
|RunsSucceeded|成功した実行|Count|合計|成功したワークフロー実行の数。|なし|
|RunsFailed|失敗した実行|Count|合計|失敗したワークフロー実行の数。|なし|
|RunsCancelled|キャンセルされた実行|Count|合計|キャンセルされたワークフロー実行の数。|なし|
|RunLatency|実行の待機時間|Seconds|Average|完了したワークフロー実行の待機時間。|なし|
|RunSuccessLatency|成功した実行の待機時間|Seconds|Average|成功したワークフロー実行の待機時間。|なし|
|RunThrottledEvents|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|なし|
|RunStartThrottledEvents|実行開始スロットル イベント数|Count|合計|ワークフロー実行開始スロットル イベントの数。|なし|
|RunFailurePercentage|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|なし|
|ActionsStarted|開始したアクション |Count|合計|開始したワークフロー アクションの数。|なし|
|ActionsCompleted|完了したアクション |Count|合計|完了したワークフロー アクションの数。|なし|
|ActionsSucceeded|成功したアクション |Count|合計|成功したワークフロー アクションの数。|なし|
|ActionsFailed|失敗したアクション |Count|合計|失敗したワークフロー アクションの数。|なし|
|ActionsSkipped|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|なし|
|ActionLatency|アクションの待機時間 |Seconds|Average|完了したワークフロー アクションの待機時間。|なし|
|ActionSuccessLatency|成功したアクションの待機時間 |Seconds|Average|成功したワークフロー アクションの待機時間。|なし|
|ActionThrottledEvents|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|なし|
|TriggersStarted|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|なし|
|TriggersCompleted|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|なし|
|TriggersSucceeded|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|なし|
|TriggersFailed|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|なし|
|TriggersSkipped|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|なし|
|TriggersFired|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|なし|
|TriggerLatency|トリガーの待機時間 |Seconds|Average|完了したワークフロー トリガーの待機時間。|なし|
|TriggerFireLatency|起動されたトリガーの待機時間 |Seconds|Average|起動されたワークフロー トリガーの待機時間。|なし|
|TriggerSuccessLatency|成功したトリガーの待機時間 |Seconds|Average|成功したワークフロー トリガーの待機時間。|なし|
|TriggerThrottledEvents|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|なし|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|統合サービス環境のワークフロー プロセッサ使用率|Percent|Average|統合サービス環境のワークフロー プロセッサ使用率。|なし|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|統合サービス環境のワークフロー メモリ使用率|Percent|Average|統合サービス環境のワークフロー メモリ使用率。|なし|
|IntegrationServiceEnvironmentConnectorProcessorUsage|統合サービス環境のコネクタ プロセッサ使用率|Percent|Average|統合サービス環境のコネクタ プロセッサ使用率。|なし|
|IntegrationServiceEnvironmentConnectorMemoryUsage|統合サービス環境のコネクタ メモリ使用率|Percent|Average|統合サービス環境のコネクタ メモリ使用率。|なし|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|完了した実行数|完了した実行数|Count|合計|このワークスペースに対して正常に完了した実行の数|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType|
|開始した実行数|開始した実行数|Count|合計|このワークスペースに対して開始された実行数|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType|
|失敗した実行|失敗した実行|Count|合計|このワークスペースに対して失敗した実行数|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType|
|モデルの登録が成功しました|モデル登録成功|Count|合計|このワークスペースで成功したモデル登録の数|シナリオ|
|モデルの登録が失敗しました|モデル登録失敗|Count|合計|このワークスペースで失敗したモデル登録の数|Scenario、StatusCode|
|開始されたモデル デプロイ|モデル デプロイ開始|Count|合計|このワークスペースで開始されたモデル デプロイの数|シナリオ|
|モデル デプロイが成功しました|モデル デプロイが成功しました|Count|合計|このワークスペースで成功したモデル デプロイの数|シナリオ|
|モデル デプロイ失敗|モデル デプロイ失敗|Count|合計|このワークスペースで失敗したモデル デプロイの数|Scenario、StatusCode|
|ノード総数|ノード総数|Count|Average|ノードの合計数。 この合計には、アクティブ ノード、アイドル状態のノード、使用できないノード、割り込まれたノード、終了中のノードなどが含まれます|Scenario、ClusterName|
|アクティブなノード|アクティブなノード|Count|Average|アクティブ ノードの数。 これらは、ジョブをアクティブに実行しているノードです。|Scenario、ClusterName|
|アイドル状態のノード|アイドル状態のノード|Count|Average|アイドル状態のノードの数。 アイドル状態のノードは、ジョブを実行していないノードですが、使用可能な場合は新しいジョブを受け入れることができます。|Scenario、ClusterName|
|使用できないノード|使用できないノード|Count|Average|使用できないノードの数。 使用できないノードは、いくつかの問題が解決されていないため、機能していません。 これらのノードは Azure によってリサイクルされます。|Scenario、ClusterName|
|割り込まれたノード|割り込まれたノード|Count|Average|割り込まれたノードの数。 これらのノードは低優先度のノードであり、使用可能なノード プールから外されます。|Scenario、ClusterName|
|終了中のノード|終了中のノード|Count|Average|終了中のノードの数。 終了中のノードは、ジョブの処理を完了したばかりで、アイドル状態になるノードです。|Scenario、ClusterName|
|コアの合計|コアの合計|Count|Average|コアの合計数|Scenario、ClusterName|
|アクティブなコア|アクティブなコア|Count|Average|アクティブなコアの数|Scenario、ClusterName|
|アイドル状態のコア|アイドル状態のコア|Count|Average|アイドル状態のコアの数|Scenario、ClusterName|
|使用できないコア|使用できないコア|Count|Average|使用できないコアの数|Scenario、ClusterName|
|割り込まれたコア|割り込まれたコア|Count|Average|割り込まれたコアの数|Scenario、ClusterName|
|終了中のコア|終了中のコア|Count|Average|終了中のコアの数|Scenario、ClusterName|
|クォータ使用率|クォータ使用率|Count|Average|クォータ使用率 (%)|Scenario、ClusterName、VmFamilyName、VmPriority|
|CpuUtilization|CpuUtilization|Count|Average|CPU (プレビュー)|Scenario、runId、NodeId、CreatedTime|
|GpuUtilization|GpuUtilization|Count|Average|GPU (プレビュー)|Scenario、runId、NodeId、CreatedTime、DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|使用法|使用法|Count|Count|API 呼び出しの数|ApiCategory、ApiName、ResultType、ResponseCode|
|可用性|可用性|Percent|Average|API の可用性|ApiCategory、ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。|OutputFormat|
|SuccessE2ELatency|成功したエンド ツー エンドの待機時間|ミリ秒|Average|成功した要求の平均待機時間 (ミリ秒単位)。|OutputFormat|
|Requests|Requests|Count|合計|ストリーミング エンドポイントへの要求。|OutputFormat、HttpStatusCode、ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AssetQuota|アセットのクォータ|Count|Average|現在のメディア サービス アカウントで許可されている資産の数|なし|
|AssetCount|アセット数|Count|Average|現在のメディア サービス アカウントに既に作成されている資産の数|なし|
|AssetQuotaUsedPercentage|アセットのクォータの使用率|Percent|Average|現在のメディア サービス アカウントで使用されている資産の割合 (%)|なし|
|ContentKeyPolicyQuota|コンテンツ キー ポリシーのクォータ|Count|Average|現在のメディア サービス アカウントで許可されているコンテンツ キー ポリシーの数|なし|
|ContentKeyPolicyCount|コンテンツ キー ポリシー数|Count|Average|現在のメディア サービス アカウントに既に作成されているコンテンツ キー ポリシーの数|なし|
|ContentKeyPolicyQuotaUsedPercentage|コンテンツ キー ポリシーのクォータの使用率|Percent|Average|現在のメディア サービス アカウントで使用されているコンテンツ キー ポリシーの割合 (%)|なし|
|StreamingPolicyQuota|ストリーミング ポリシーのクォータ|Count|Average|現在のメディア サービス アカウントで許可されているストリーミング ポリシーの数|なし|
|StreamingPolicyCount|ストリーミング ポリシー数|Count|Average|現在のメディア サービス アカウントに既に作成されているストリーミング ポリシーの数|なし|
|StreamingPolicyQuotaUsedPercentage|ストリーミング ポリシーのクォータの使用率|Percent|Average|現在のメディア サービス アカウントで使用されているストリーミング ポリシーの割合 (%)|なし|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AverageReadLatency|読み取りの平均待機時間|MilliSeconds|Average|操作ごとの読み取りの平均待機時間 (ミリ秒単位)|なし|
|AverageWriteLatency|書き込みの平均待機時間|MilliSeconds|Average|操作ごとの書き込みの平均待機時間 (ミリ秒単位)|なし|
|VolumeLogicalSize|ボリュームの論理サイズ|バイト|Average|ボリュームの論理サイズ (使用されているバイト)|なし|
|VolumeSnapshotSize|ボリューム スナップショットのサイズ|バイト|Average|ボリューム内のすべてのスナップショットのサイズ|なし|
|ReadIops|読み取り IOPS|CountPerSecond|Average|1 秒あたりの読み取り入出力操作|なし|
|WriteIops|書き込み IOPS|CountPerSecond|Average|1 秒あたりの書き込み入出力操作|なし|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|ボリューム プールの使用済みの割り当て|バイト|Average|プールの使用済みの割り当てサイズ|なし|
|VolumePoolTotalLogicalSize|ボリューム プールの論理サイズの合計|バイト|Average|プールに属するすべてのボリュームの論理サイズの合計|なし|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BytesSentRate|送信バイト数|バイト|合計|ネットワーク インターフェイスが送信したバイト数|なし|
|BytesReceivedRate|受信バイト数|バイト|合計|ネットワーク インターフェイスが受信したバイト数|なし|
|PacketsSentRate|送信されたパケット数|Count|合計|ネットワーク インターフェイスが送信したパケット数|なし|
|PacketsReceivedRate|受信したパケット数|Count|合計|ネットワーク インターフェイスが受信したパケット数|なし|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|データ パスの可用性|Count|Average|期間あたりの Load Balancer データ パスの可用性平均|FrontendIPAddress、FrontendPort|
|DipAvailability|正常性プローブの状態|Count|Average|期間あたりの Load Balancer 正常性プローブの状態平均|ProtocolType、BackendPort、FrontendIPAddress、FrontendPort、BackendIPAddress|
|ByteCount|Byte Count (バイト数)|Count|合計|期間内に送信された合計バイト数|FrontendIPAddress、FrontendPort、Direction|
|PacketCount|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|FrontendIPAddress、FrontendPort、Direction|
|SYNCount|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|FrontendIPAddress、FrontendPort、Direction|
|SnatConnectionCount|SNAT Connection Count (SNAT 接続数)|Count|合計|期間内に作成された新しい SNAT 接続の合計数|FrontendIPAddress、BackendIPAddress、ConnectionState|
|AllocatedSnatPorts|割り当てられた SNAT ポート (プレビュー)|Count|合計|期間内に割り当てられた SNAT ポートの合計数|FrontendIPAddress、BackendIPAddress、ProtocolType、IsAwaitingRemoval|
|UsedSnatPorts|使用された SNAT ポート (プレビュー)|Count|合計|期間内に使用された SNAT ポートの合計数|FrontendIPAddress、BackendIPAddress、ProtocolType、IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueryVolume|クエリ数|Count|合計|DNS ゾーンで処理されたクエリの数|なし|
|RecordSetCount|レコード セット数|Count|最大値|DNS ゾーンのレコード セットの数|なし|
|RecordSetCapacityUtilization|レコード セットの容量使用率|Percent|最大値|DNS ゾーンで使用されるレコード セットの容量の割合|なし|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS 受信パケット数|CountPerSecond|最大値|DDoS 受信パケット数|なし|
|PacketsDroppedDDoS|DDoS 受信パケット破棄数|CountPerSecond|最大値|DDoS 受信パケット破棄数|なし|
|PacketsForwardedDDoS|DDoS 受信パケット転送数|CountPerSecond|最大値|DDoS 受信パケット転送数|なし|
|TCPPacketsInDDoS|DDoS 受信 TCP パケット数|CountPerSecond|最大値|DDoS 受信 TCP パケット数|なし|
|TCPPacketsDroppedDDoS|DDoS 受信 TCP パケット破棄数|CountPerSecond|最大値|DDoS 受信 TCP パケット破棄数|なし|
|TCPPacketsForwardedDDoS|DDoS 受信 TCP パケット転送数|CountPerSecond|最大値|DDoS 受信 TCP パケット転送数|なし|
|UDPPacketsInDDoS|DDoS 受信 UDP パケット数|CountPerSecond|最大値|DDoS 受信 UDP パケット数|なし|
|UDPPacketsDroppedDDoS|DDoS 受信 UDP パケット破棄数|CountPerSecond|最大値|DDoS 受信 UDP パケット破棄数|なし|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|CountPerSecond|最大値|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|なし|
|BytesInDDoS|DDoS 受信バイト数|BytesPerSecond|最大値|DDoS 受信バイト数|なし|
|BytesDroppedDDoS|DDoS 受信バイト破棄数|BytesPerSecond|最大値|DDoS 受信バイト破棄数|なし|
|BytesForwardedDDoS|DDoS 受信バイト転送数|BytesPerSecond|最大値|DDoS 受信バイト転送数|なし|
|TCPBytesInDDoS|DDoS 受信 TCP バイト数|BytesPerSecond|最大値|DDoS 受信 TCP バイト数|なし|
|TCPBytesDroppedDDoS|DDoS 受信 TCP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 TCP バイト破棄数|なし|
|TCPBytesForwardedDDoS|DDoS 受信 TCP バイト転送数|BytesPerSecond|最大値|DDoS 受信 TCP バイト転送数|なし|
|UDPBytesInDDoS|DDoS 受信 UDP バイト数|BytesPerSecond|最大値|DDoS 受信 UDP バイト数|なし|
|UDPBytesDroppedDDoS|DDoS 受信 UDP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 UDP バイト破棄数|なし|
|UDPBytesForwardedDDoS|DDoS 受信 UDP バイト転送数|BytesPerSecond|最大値|DDoS 受信 UDP バイト転送数|なし|
|IfUnderDDoSAttack|DDoS 攻撃中かどうか|Count|最大値|DDoS 攻撃中かどうか|なし|
|DDoSTriggerTCPPackets|DDoS 軽減をトリガーする受信 TCP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 TCP パケット数|なし|
|DDoSTriggerUDPPackets|DDoS 軽減をトリガーする受信 UDP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 UDP パケット数|なし|
|DDoSTriggerSYNPackets|DDoS 軽減をトリガーする受信 SYN パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 SYN パケット数|なし|
|VipAvailability|データ パスの可用性|Count|Average|期間あたりの IP アドレス可用性平均|Port|
|ByteCount|Byte Count (バイト数)|Count|合計|期間内に送信された合計バイト数|Port、Direction|
|PacketCount|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|Port、Direction|
|SynCount|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|Port、Direction|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|VM への Ping のラウンド トリップ時間|MilliSeconds|Average|宛先 VM に送信された Ping のラウンド トリップ時間|SourceCustomerAddress、DestinationCustomerAddress|
|PingMeshProbesFailedPercent|VM への Ping の失敗|Percent|Average|宛先 VM の送信された Ping の総数に対する失敗した Ping の数の割合 (%)|SourceCustomerAddress、DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ApplicationRuleHit|アプリケーション規則のヒット数|Count|合計|アプリケーション規則がヒットした回数|Status、Reason、Protocol|
|NetworkRuleHit|ネットワーク規則のヒット数|Count|合計|ネットワーク規則がヒットした回数|Status、Reason、Protocol|
|FirewallHealth|ファイアウォールの正常性状態|Percent|Average|ファイアウォールの正常性状態|Status、Reason|
|DataProcessed|データ処理量|バイト|合計|ファイアウォールによって処理済みデータの合計量|なし|
|SNATPortUtilization|SNAT ポート使用率|Percent|Average|SNAT ポート使用率|なし|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|スループット|スループット|BytesPerSecond|Average|Application Gateway が処理した 1 秒あたりのバイト数|なし|
|UnhealthyHostCount|異常なホストの数|Count|Average|異常なバックエンド ホストの数|BackendSettingsPool|
|HealthyHostCount|正常なホストの数|Count|Average|正常なバックエンド ホストの数|BackendSettingsPool|
|TotalRequests|要求の合計数|Count|合計|Application Gateway が処理した成功した要求の数|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|正常なホストごとの 1 分あたりの要求|Count|Average|プール内の正常なバックエンド ホストごとの 1 分あたりの平均要求数|BackendSettingsPool|
|FailedRequests|失敗した要求|Count|合計|Application Gateway が処理した失敗した要求の数|BackendSettingsPool|
|ResponseStatus|応答の状態|Count|合計|Application Gateway によって返された HTTP 応答の状態|HttpStatusGroup|
|CurrentConnections|現在の接続数|Count|合計|Application Gateway で確立された現在の接続の数|なし|
|CpuUtilization|CPU 使用率|Percent|Average|Application Gateway の現在の CPU 使用率|なし|
|CapacityUnits|現在の容量ユニット|Count|Average|使用された容量ユニット|なし|
|ComputeUnits|現在のコンピューティング ユニット|Count|Average|消費されたコンピューティング ユニット|なし|
|BackendResponseStatus|バックエンド応答の状態|Count|合計|バックエンド メンバーによって生成された HTTP 応答コードの数。 これには、Application Gateway によって生成された応答コードは含まれません。|BackendServer、BackendPool、BackendHttpSetting、HttpStatusGroup|
|TlsProtocol|クライアント TLS プロトコル|Count|合計|Application Gateway との接続を確立したクライアントによって開始された TLS 要求と非 TLS 要求の数です。 TLS プロトコルの分布を表示するには、ディメンションの TLS プロトコルでフィルター処理します。|Listener、TlsProtocol|
|BytesSent|送信バイト数|バイト|合計|Application Gateway からクライアントに送信されたバイトの合計数です|リスナー|
|BytesReceived|受信バイト数|バイト|合計|クライアントから Application Gateway に送信されたバイトの合計数です|リスナー|
|ClientRtt|クライアント RTT|MilliSeconds|Average|クライアントと Application Gateway の間の平均ラウンドトリップ時間。 このメトリックは、接続が確立され、受信確認が返されるまでにかかる時間を示します|リスナー|
|ApplicationGatewayTotalTime|Application Gateway の合計時間|MilliSeconds|Average|要求が処理されその応答が送信されるのにかかった平均時間。 これは、Application Gateway が HTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの平均間隔として計算されます。 これには、通常、Application Gateway の処理時間、要求パケットと応答パケットがネットワーク経由で移動する時間、およびバックエンド サーバーが応答するまでの時間が含まれていることに注意することが重要です。|リスナー|
|BackendConnectTime|バックエンド接続時間|MilliSeconds|Average|バックエンド サーバーとの接続を確立するために費やされた時間|Listener、BackendServer、BackendPool、BackendHttpSetting|
|BackendFirstByteResponseTime|バックエンド先頭バイト応答時間|MilliSeconds|Average|バックエンド サーバーへの接続の確立を開始してから応答ヘッダーの最初のバイトを受信するまでの時間間隔であり、バックエンド サーバーのおおよその処理時間です。|Listener、BackendServer、BackendPool、BackendHttpSetting|
|BackendLastByteResponseTime|バックエンド最終バイト応答時間|MilliSeconds|Average|バックエンド サーバーへの接続の確立を開始してから応答本文の最後のバイトを受信するまでの時間間隔です。|Listener、BackendServer、BackendPool、BackendHttpSetting|
|MatchedCount|Web アプリケーション ファイアウォールの合計規則配布|Count|合計|受信トラフィックに対する Web アプリケーション ファイアウォールの合計規則配布|RuleGroup、RuleId|
|BlockedCount|Web アプリケーション ファイアウォールのブロックされた要求の規則配布|Count|合計|Web アプリケーション ファイアウォールのブロックされた要求の規則配布|RuleGroup、RuleId|
|BlockedReqCount|Web アプリケーション ファイアウォールのブロックされた要求数|Count|合計|Web アプリケーション ファイアウォールのブロックされた要求数|なし|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AverageBandwidth|ゲートウェイの S2S 帯域幅|BytesPerSecond|Average|1 秒あたりのゲートウェイのサイト対サイト帯域幅の平均 (バイト単位)|なし|
|P2SBandwidth|ゲートウェイの P2S 帯域幅|BytesPerSecond|Average|1 秒あたりのゲートウェイのポイント対サイト帯域幅の平均 (バイト単位)|なし|
|P2SConnectionCount|P2S 接続数|Count|最大値|ゲートウェイのポイント対サイト接続の数|Protocol|
|TunnelAverageBandwidth|Tunnel Bandwidth (トンネル帯域幅)|BytesPerSecond|Average|1 秒あたりの平均トンネル帯域幅バイト数|ConnectionName、RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes (トンネル エグレス バイト数)|バイト|合計|トンネルの送信バイト数|ConnectionName、RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes (トンネル イングレス バイト数)|バイト|合計|トンネルの受信バイト数|ConnectionName、RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets (トンネル エグレス パケット数)|Count|合計|トンネルの送信パケット数|ConnectionName、RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets (トンネル イングレス パケット数)|Count|合計|トンネルの受信パケット数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの発信パケット破棄数|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの受信パケット破棄数|ConnectionName、RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Average|DBM の Rx ライト レベル|Link、Lane|
|TxLightLevel|TxLightLevel|Count|Average|DBM の Tx ライト レベル|Link、Lane|
|AdminState|AdminState|Count|Average|ポートの管理者の状態|Link|
|LineProtocol|LineProtocol|Count|Average|ポートの回線プロトコルの状態|Link|
|PortBitsInPerSecond|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|Link|
|PortBitsOutPerSecond|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|Link|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|PeeringType|
|BitsOutPerSecond (送信ビット数/秒)|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|PeeredCircuitSKey|
|BgpAvailability|BGP の可用性|Percent|Average|MSEE からすべてのピアへの BGP の可用性。|PeeringType、Peer|
|ArpAvailability|ARP の可用性|Percent|Average|MSEE からすべてのピアへの ARP の可用性。|PeeringType、Peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Average|1 秒あたりに破棄されたデータのイングレス ビット数|なし|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Average|1 秒あたりに破棄されたデータのエグレス ビット数|なし|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|なし|
|BitsOutPerSecond (送信ビット数/秒)|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|なし|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|なし|
|BitsOutPerSecond (送信ビット数/秒)|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|なし|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned (エンドポイント別の返されたクエリ数)|Count|合計|指定された期間内に Traffic Manager エンドポイントが返された回数|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint (エンドポイント別のエンドポイント状態)|Count|最大値|エンドポイントのプローブの状態が "有効" の場合は 1、それ以外の場合は 0。|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ProbesFailedPercent|失敗したプローブの割合|Percent|Average|失敗した接続監視プローブの割合|なし|
|AverageRoundtripMs|Avg.ラウンド トリップ時間 (ミリ秒)|MilliSeconds|Average|ソースと接続先の間で送信された接続監視プローブのネットワークの平均ラウンド トリップ時間 (ミリ秒)|なし|
|ChecksFailedPercent|チェック失敗の割合 (プレビュー)|Percent|Average|失敗した接続監視チェックの割合|SourceAddress、SourceName、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName|
|RoundTripTimeMs|ラウンド トリップ時間 (ミリ秒) (プレビュー)|MilliSeconds|Average|接続監視チェックのラウンド トリップ時間 (ミリ秒)|SourceAddress、SourceName、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RequestCount|要求数|Count|合計|HTTP/S プロキシによって処理されるクライアント要求の数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestSize|要求サイズ|バイト|合計|HTTP/S プロキシにクライアントからの要求として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|ResponseSize|応答サイズ|バイト|合計|クライアントに HTTP/S プロキシからの応答として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BillableResponseSize|課金対象の応答サイズ|バイト|合計|HTTP/S プロキシからクライアントへの応答として送信された、課金対象のバイト数 (要求あたり最小 2 KB)。|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BackendRequestCount|バックエンド要求数|Count|合計|バックエンドに HTTP/S プロキシから送信された要求の数|HttpStatus、HttpStatusGroup、Backend|
|BackendRequestLatency|バックエンド要求の待機時間|MilliSeconds|Average|HTTP/S プロキシによってバックエンドに要求が送信されてから、HTTP/S プロキシでバックエンドから最後の応答バイトが受信されるまでの算出時間|バックエンド|
|TotalLatency|合計待機時間|MilliSeconds|Average|HTTP/S プロキシによってクライアント要求が受信されてから、HTTP/S プロキシからの最後の応答バイトがクライアントで確認されるまでの算出時間|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BackendHealthPercentage|バックエンドの正常性の割合|Percent|Average|HTTP/S プロキシからバックエンドへの成功した正常性プローブの割合|Backend、BackendPool|
|WebApplicationFirewallRequestCount|Web アプリケーション ファイアウォール要求の数|Count|合計|Web アプリケーション ファイアウォールによって処理されるクライアント要求の数|PolicyName、RuleName、Action|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|registration.all|登録操作|Count|合計|成功した登録操作の数 (作成、更新、クエリ、および削除)。 |なし|
|registration.create|登録作成操作|Count|合計|成功した登録作成の数。|なし|
|registration.update|登録更新操作|Count|合計|成功した登録更新の数。|なし|
|registration.get|登録読み取り操作|Count|合計|成功した登録クエリの数。|なし|
|registration.delete|登録削除操作|Count|合計|成功した登録削除の数。|なし|
|incoming|受信メッセージ|Count|合計|成功した送信 API 呼び出しの数。 |なし|
|incoming.scheduled|予定されていたプッシュ通知の送信|Count|合計|予定されていたプッシュ通知が取り消されました|なし|
|incoming.scheduled.cancel|予定されていたプッシュ通知が取り消されました|Count|合計|予定されていたプッシュ通知が取り消されました|なし|
|scheduled.pending|予定されていた通知の保留|Count|合計|予定されていた通知の保留|なし|
|installation.all|インストール管理操作|Count|合計|インストール管理操作|なし|
|installation.get|インストール操作の取得|Count|合計|インストール操作の取得|なし|
|installation.upsert|インストール操作を作成または更新します|Count|合計|インストール操作を作成または更新します|なし|
|installation.patch|インストール操作をパッチします|Count|合計|インストール操作をパッチします|なし|
|installation.delete|インストール操作の削除|Count|合計|インストール操作の削除|なし|
|outgoing.allpns.success|正常な通知|Count|合計|成功した通知の数。|なし|
|outgoing.allpns.invalidpayload|ペイロード エラー|Count|合計|PNS が不良ペイロード エラーを返しために失敗したプッシュの数。|なし|
|outgoing.allpns.pnserror|外部通知システム エラー|Count|合計|PNS との通信に問題があったために失敗したプッシュの数 (認証の問題は除きます)。|なし|
|outgoing.allpns.channelerror|チャネル エラー|Count|合計|チャネルが適切に調整されていないアプリまたは有効期限が切れたアプリに関連付けられていたために失敗したプッシュの数。|なし|
|outgoing.allpns.badorexpiredchannel|適切または期限切れのチャネル エラー|Count|合計|登録内のチャネル/トークン/登録 ID が期限切れまたは無効なために失敗したプッシュの数。|なし|
|outgoing.wns.success|WNS 正常通知|Count|合計|成功した通知の数。|なし|
|outgoing.wns.invalidcredentials|WNS 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 (Windows Live は資格情報を認識しません)。|なし|
|outgoing.wns.badchannel|WNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (WNS 状態: 404 - 見つかりません)。|なし|
|outgoing.wns.expiredchannel|WNS 期限切れチャネル エラー|Count|合計|ChannelURI の有効期限が切れているために失敗したプッシュの数 (WNS 状態: 410 - 削除)。|なし|
|outgoing.wns.throttled|WNS 調整済み通知|Count|合計|WNS によってこのアプリが調整されているために失敗したプッシュの数 (WNS 状態: 406 - 受信不可)。|なし|
|outgoing.wns.tokenproviderunreachable|WNS 承認エラー (到達不可)|Count|合計|Windows Live に到達できません。|なし|
|outgoing.wns.invalidtoken|WNS 承認エラー (無効なトークン)|Count|合計|WNS に提供されたトークンは無効です (WNS 状態: 401 - 許可されていません)。|なし|
|outgoing.wns.wrongtoken|WNS 承認エラー (間違ったトークン)|Count|合計|WNS に提供されたトークンは有効ですが、別のアプリケーション用です (WNS 状態: 403 - 禁止されています)。 これは、登録の ChannelURI が別のアプリケーションに関連付けられている場合に発生します。 クライアント アプリに関連付けられているアプリの資格情報が通知ハブ内にあることを確認します。|なし|
|outgoing.wns.invalidnotificationformat|WNS 無効通知形式|Count|合計|通知の形式が無効です (WNS 状態: 400)。 WNS ではすべての無効なペイロードを拒否するわけではないことに注意してください。|なし|
|outgoing.wns.invalidnotificationsize|WNS 無効通知サイズ エラー|Count|合計|通知ペイロードが大きすぎます (WNS 状態: 413)。|なし|
|outgoing.wns.channelthrottled|WNS 調整済みチャネル|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-NotificationStatus:channelThrottled)。|なし|
|outgoing.wns.channeldisconnected|WNS チャネル切断|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-DeviceConnectionStatus: disconnected)。|なし|
|outgoing.wns.dropped|WNS ドロップ通知|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected)。|なし|
|outgoing.wns.pnserror|WNS エラー|Count|合計|WNS との通信エラーにより、通知が配信されません。|なし|
|outgoing.wns.authenticationerror|WNS 認証エラー|Count|合計|資格情報無効または間違ったトークンによる Windows Live との通信エラーのため、通知が配信されません。|なし|
|outgoing.apns.success|APNS 正常通知|Count|合計|成功した通知の数。|なし|
|outgoing.apns.invalidcredentials|APNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|なし|
|outgoing.apns.badchannel|APNS 不良チャネル エラー|Count|合計|トークンが無効なために失敗したプッシュの数 (APNS 状態コード: 8)。|なし|
|outgoing.apns.expiredchannel|APNS 期限切れチャネル エラー|Count|合計|APNS フィードバック チャネルによって無効化されたトークンの数。|なし|
|outgoing.apns.invalidnotificationsize|APNS 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (APNS 状態コード: 7)。|なし|
|outgoing.apns.pnserror|APNS エラー|Count|合計|APNS との通信エラーにより失敗したプッシュの数。|なし|
|outgoing.gcm.success|GCM 正常通知|Count|合計|成功した通知の数。|なし|
|outgoing.gcm.invalidcredentials|GCM 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|なし|
|outgoing.gcm.badchannel|GCM 不良チャネル エラー|Count|合計|登録内の registrationId が認識されなかったために失敗したプッシュの数 (GCM 結果: 無効な登録)。|なし|
|outgoing.gcm.expiredchannel|GCM 期限切れチャネル エラー|Count|合計|登録内の registrationId が有効期限切れだったために失敗したプッシュの数 (GCM 結果: NotRegistered)。|なし|
|outgoing.gcm.throttled|GCM 調整済み通知|Count|合計|GCM によってこのアプリが調整されたために失敗したプッシュの数 (GCM 状態コード: 501-599、または結果: 利用不可)。|なし|
|outgoing.gcm.invalidnotificationformat|GCM 無効通知形式|Count|合計|ペイロードの形式が正しくなかったために失敗したプッシュの数 (GCM 結果: InvalidDataKey または InvalidTtl)。|なし|
|outgoing.gcm.invalidnotificationsize|GCM 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (GCM 結果: MessageTooBig)。|なし|
|outgoing.gcm.wrongchannel|GCM 誤チャネル エラー|Count|合計|登録内の registrationId が現在のアプリに関連付けられていないために失敗したプッシュの数 (GCM 結果: InvalidPackageName)。|なし|
|outgoing.gcm.pnserror|GCM エラー|Count|合計|GCM との通信エラーにより失敗したプッシュの数。|なし|
|outgoing.gcm.authenticationerror|GCM 認証エラー|Count|合計|PNS が提供された資格情報を受け入れなかったために失敗したプッシュの数。資格情報がブロックされているか、アプリで SenderId が正しく構成されていません (GCM 結果: MismatchedSenderId)。|なし|
|outgoing.mpns.success|MPNS 正常通知|Count|合計|成功した通知の数。|なし|
|outgoing.mpns.invalidcredentials|MPNS 無効な資格情報|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|なし|
|outgoing.mpns.badchannel|MPNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (MPNS 状態: 404 - 見つかりません)。|なし|
|outgoing.mpns.throttled|MPNS 調整済み通知|Count|合計|MPNS によってこのアプリが調整されているために失敗したプッシュの数 (WNS MPNS: 406 - 受信不可)。|なし|
|outgoing.mpns.invalidnotificationformat|MPNS 無効通知形式|Count|合計|通知のペイロードが長すぎたために失敗したプッシュの数。|なし|
|outgoing.mpns.channeldisconnected|MPNS チャネル切断|Count|合計|登録の ChannelURI が切断されたために失敗したプッシュの数 (MPNS 状態: 412 - 見つかりません)。|なし|
|outgoing.mpns.dropped|MPNS ドロップ通知|Count|合計|MPNS によって破棄されたプッシュの数 (MPNS 応答ヘッダー: X-NotificationStatus: QueueFull または Suppressed)。|なし|
|outgoing.mpns.pnserror|MPNS エラー|Count|合計|MPNS との通信エラーにより失敗したプッシュの数。|なし|
|outgoing.mpns.authenticationerror|MPNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|なし|
|notificationhub.pushes|All Outgoing Notifications (すべての送信通知)|Count|合計|Notification Hub のすべての送信通知|なし|
|incoming.all.requests|すべての受信要求|Count|合計|Notification Hub の受信要求の合計数|なし|
|incoming.all.failedrequests|失敗したすべての受信要求|Count|合計|Notification Hub の失敗した受信要求の合計数|なし|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Count|Average|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|% Free Space|Count|Average|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Inodes|% Used Inodes|Count|Average|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Space|% Used Space|Count|Average|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Count|Average|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Count|Average|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Count|Average|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Count|Average|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|Free Megabytes|Count|Average|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Count|Average|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Memory|% Available Memory|Count|Average|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Count|Average|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Memory|% Used Memory|Count|Average|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Count|Average|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Count|Average|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Count|Average|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Count|Average|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Count|Average|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pages/sec|Pages/sec|Count|Average|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Count|Average|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Count|Average|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Count|Average|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Count|Average|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes|Total Bytes|Count|Average|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Count|Average|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Received|Total Packets Received|Count|Average|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Count|Average|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Count|Average|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Collisions|Total Collisions|Count|Average|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Read|Avg.Disk sec/Read|Count|Average|Average_Avg. Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg.Disk sec/Transfer|Count|Average|Average_Avg. Disk sec/Transfer|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Write|Avg.Disk sec/Write|Count|Average|Average_Avg. Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Count|Average|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Count|Average|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct User Time|Pct User Time|Count|Average|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Count|Average|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Count|Average|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% DPC Time|% DPC Time|Count|Average|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Idle Time|% Idle Time|Count|Average|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Count|Average|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Count|Average|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Nice Time|% Nice Time|Count|Average|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Privileged Time|% Privileged Time|Count|Average|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|% Processor Time|Count|Average|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% User Time|% User Time|Count|Average|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Count|Average|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Count|Average|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Count|Average|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processes|処理|Count|Average|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Count|Average|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Uptime|Uptime|Count|Average|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Users|ユーザー|Count|Average|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Read|Avg.Disk sec/Read|Count|Average|Average_Avg. Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Write|Avg.Disk sec/Write|Count|Average|Average_Avg. Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Count|Average|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Count|Average|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Count|Average|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|Free Megabytes|Count|Average|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|% Free Space|Count|Average|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes|Available MBytes|Count|Average|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Count|Average|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Count|Average|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Count|Average|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Count|Average|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|% Processor Time|Count|Average|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processor Queue Length|プロセッサ キューの長さ|Count|Average|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Heartbeat|Heartbeat|Count|合計|Heartbeat|Computer、OSType、Version、SourceComputerId|
|更新|更新|Count|Average|更新|Computer、Product、Classification、UpdateState、Optional、Approved|
|Event|Event|Count|Average|Event|Source、EventLog、Computer、EventCategory、EventLevel、EventLevelName、EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PrefixLatency|プレフィックスの待機時間|ミリ秒|Average|プレフィックスの待機時間の中央値|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SessionAvailabilityV4|セッション可用性 V4|Percent|Average|V4 セッションの可用性|ConnectionId|
|SessionAvailabilityV6|セッションの可用性 V6|Percent|Average|V6 セッションの可用性|ConnectionId|
|IngressTrafficRate|イグレス トラフィック速度|BitsPerSecond|Average|イグレス トラフィック速度 (ビット/秒)|ConnectionId|
|EgressTrafficRate|エグレス トラフィック速度|BitsPerSecond|Average|エグレス トラフィック速度 (ビット/秒)|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|クエリ実行時間|ミリ秒|Average|最後の間隔における DAX クエリの実行時間|ディメンションなし|
|QueryPoolJobQueueLength|スレッド:クエリ プール ジョブ キューの長さ|Count|Average|クエリ スレッド プールのキューに登録されているジョブの数。|ディメンションなし|
|qpu_high_utilization_metric|QPU High Utilization (QPU の高使用率)|Count|合計|過去 1 分間の QPU の高使用率、QPU 使用率が高い場合は 1、それ以外の場合は 0|ディメンションなし|
|memory_metric|メモリ|バイト|Average|メモリ。 範囲は A1 で 0 から 3 GB、A2 で 0 から 5 GB、A3 で 0 から 10 GB、A4 で 0 から 25 GB、A5 で 0 から 50 GB、A6 で 0 から 100 GB|ディメンションなし|
|memory_thrashing_metric|メモリ スラッシング|Percent|Average|平均的なメモリ スラッシング。|ディメンションなし|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|合計|Microsoft.Relay の正常な ListenerConnections.|EntityName、OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|合計|Microsoft.Relay の ListenerConnections での ClientError.|EntityName、OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|合計|Microsoft.Relay の ListenerConnections での ServerError.|EntityName、OperationResult|
|SenderConnections-Success|SenderConnections-Success|Count|合計|Microsoft.Relay の正常な SenderConnections.|EntityName、OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|合計|Microsoft.Relay の SenderConnections での ClientError.|EntityName、OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|合計|Microsoft.Relay の SenderConnections での ServerError.|EntityName、OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|合計|Microsoft.Relay の ListenerConnections の合計。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|合計|Microsoft.Relay の SenderConnections 要求の合計。|EntityName|
|ActiveConnections|ActiveConnections|Count|合計|Microsoft.Relay の ActiveConnections の合計。|EntityName|
|ActiveListeners|ActiveListeners|Count|合計|Microsoft.Relay の ActiveListeners の合計。|EntityName|
|BytesTransferred|BytesTransferred|Count|合計|Microsoft.Relay の BytesTransferred の合計。|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|合計|Microsoft.Relay の ListenerDisconnects の合計。|EntityName|
|SenderDisconnects|SenderDisconnects|Count|合計|Microsoft.Relay の SenderDisconnects の合計。|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|検索の待機時間|Seconds|Average|検索サービスの平均検索待機時間|なし|
|SearchQueriesPerSecond|1 秒あたりの検索クエリ数|CountPerSecond|Average|検索サービスの 1 秒あたりの検索クエリ数|なし|
|ThrottledSearchQueriesPercentage|スロットルされた検索クエリの割合|Percent|Average|検索サービスのスロットルされた検索クエリの割合|なし|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求|Count|合計|名前空間の成功した要求の総数|EntityName、OperationResult|
|ServerErrors|サーバー エラー。|Count|合計|Microsoft.ServiceBus のサーバー エラー数。|EntityName、OperationResult|
|UserErrors|ユーザー エラー数。|Count|合計|Microsoft.ServiceBus のユーザー エラー数。|EntityName、OperationResult|
|ThrottledRequests|スロットルされた要求数。|Count|合計|Microsoft.ServiceBus のスロットルされた要求数。|EntityName、OperationResult|
|IncomingRequests|受信要求|Count|合計|Microsoft.ServiceBus の受信要求数。|EntityName|
|IncomingMessages|受信メッセージ|Count|合計|Microsoft.ServiceBus の受信メッセージ数。|EntityName|
|OutgoingMessages|送信メッセージ|Count|合計|Microsoft.ServiceBus の送信メッセージ数。|EntityName|
|ActiveConnections|ActiveConnections|Count|合計|Microsoft.ServiceBus のアクティブな接続の合計数。|なし|
|ConnectionsOpened|開かれている接続数。|Count|Average|Microsoft.ServiceBus の開かれている接続数。|EntityName|
|ConnectionsClosed|切断された接続数。|Count|Average|Microsoft.ServiceBus の切断された接続数。|EntityName|
|Size|Size|バイト|Average|キュー/トピックのサイズ (バイト単位)。|EntityName|
|メッセージ|キュー/トピック内のメッセージの数。|Count|Average|キュー/トピック内のメッセージの数。|EntityName|
|ActiveMessages|キュー/トピック内のアクティブなメッセージの数。|Count|Average|キュー/トピック内のアクティブなメッセージの数。|EntityName|
|DeadletteredMessages|キュー/トピック内の配信不能メッセージの数。|Count|Average|キュー/トピック内の配信不能メッセージの数。|EntityName|
|ScheduledMessages|キュー/トピック内のスケジュール済みメッセージの数。|Count|Average|キュー/トピック内のスケジュール済みメッセージの数。|EntityName|
|NamespaceCpuUsage|CPU|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック。|[レプリカ]|
|NamespaceMemoryUsage|メモリ使用量|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック。|[レプリカ]|
|CPUXNS|CPU (非推奨)|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック。 このメトリックは非推奨です。 代わりに CPU メトリック (NamespaceCpuUsage) を使用してください。|[レプリカ]|
|WSXNS|メモリ使用量 (非推奨)|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック。 このメトリックは非推奨です。 代わりにメモリ使用量 (NamespaceMemoryUsage) メトリックを使用してください。|[レプリカ]|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|このコンテナーに割り当てられた CPU (ミリコア単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|バイト|Average|このコンテナーに割り当てられたメモリ (MB 単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|実際の CPU 使用率 (ミリコア単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|ActualMemory|バイト|Average|実際のメモリ使用量 (MB 単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|AllocatedCpu の割合としてのこのコンテナーの CPU の 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|AllocatedCpu の割合としてのこのコンテナーの CPU の 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Service Fabric Mesh アプリケーションの状態|ApplicationName、Status|
|ServiceStatus|ServiceStatus|Count|Average|Service Fabric Mesh アプリケーションのサービスの正常性状態|ApplicationName、Status、ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Service Fabric Mesh アプリケーションのサービス レプリカの正常性状態|ApplicationName、Status、ServiceName、ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Service Fabric Mesh アプリケーションのコンテナーの状態|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|RestartCount|RestartCount|Count|Average|Service Fabric Mesh アプリケーションのコンテナーの再起動数|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count (接続数)|Count|最大値|ユーザー接続の量。|エンドポイント|
|MessageCount|メッセージ数|Count|合計|メッセージの総量。|なし|
|InboundTraffic|受信トラフィック|バイト|合計|サービスの受信トラフィック|なし|
|OutboundTraffic|送信トラフィック|バイト|合計|サービスの送信トラフィック|なし|
|UserErrors|User Errors (ユーザー エラー)|Percent|最大値|ユーザー エラーの割合|なし|
|SystemErrors|System Errors (システム エラー)|Percent|最大値|システム エラーの割合|なし|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|Average|CPU の割合|なし|
|physical_data_read_percent|データ IO の割合|Percent|Average|データ IO の割合|なし|
|log_write_percent|ログ IO の割合|Percent|Average|ログ IO の割合。 データ ウェアハウスには適用されません。|なし|
|dtu_consumption_percent|DTU の割合|Percent|Average|DTU の割合。 DTU ベースのデータベースに適用されます。|なし|
|storage|使用済みのデータ領域|バイト|最大値|使用済みのデータ領域。 データ ウェアハウスには適用されません。|なし|
|connection_successful|成功した接続|Count|合計|成功した接続|なし|
|connection_failed|失敗した接続|Count|合計|失敗した接続|なし|
|blocked_by_firewall|ファイアウォールによってブロックされる|Count|合計|ファイアウォールによってブロックされる|なし|
|deadlock|デッドロック|Count|合計|デッドロック。 データ ウェアハウスには適用されません。|なし|
|storage_percent|使用済みのデータ領域の割合|Percent|最大値|使用済みのデータ領域の割合。 データ ウェアハウスまたはハイパースケール データベースには適用されません。|なし|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|Average|インメモリ OLTP ストレージの割合。 データ ウェアハウスには適用されません。|なし|
|workers_percent|ワーカーの割合|Percent|Average|ワーカーの割合。 データ ウェアハウスには適用されません。|なし|
|sessions_percent|セッションの割合|Percent|Average|セッションの割合。 データ ウェアハウスには適用されません。|なし|
|dtu_limit|DTU の上限|Count|Average|DTU の上限。 DTU ベースのデータベースに適用されます。|なし|
|dtu_used|使用された DTU|Count|Average|使用された DTU。 DTU ベースのデータベースに適用されます。|なし|
|cpu_limit|CPU 制限|Count|Average|CPU 制限。 仮想コア ベースのデータベースに適用されます。|なし|
|cpu_used|使用された CPU|Count|Average|使用された CPU。 仮想コア ベースのデータベースに適用されます。|なし|
|dwu_limit|DWU 上限|Count|最大値|DWU 上限。 データ ウェアハウスにのみ適用されます。|なし|
|dwu_consumption_percent|DWU の割合|Percent|最大値|DWU の割合。 データ ウェアハウスにのみ適用されます。|なし|
|dwu_used|使用済み DWU|Count|最大値|使用された DWU。 データ ウェアハウスにのみ適用されます。|なし|
|cache_hit_percent|キャッシュ ヒットの割合|Percent|最大値|キャッシュ ヒットの割合。 データ ウェアハウスにのみ適用されます。|なし|
|cache_used_percent|使用されたキャッシュの割合|Percent|最大値|使用されたキャッシュの割合。 データ ウェアハウスにのみ適用されます。|なし|
|sqlserver_process_core_percent|SQL Server プロセス コアの割合|Percent|最大値|オペレーティング システムによって測定された SQL Server プロセスの CPU 使用率 (%)。 現在、サーバーレス データベースでのみ使用できます。|なし|
|sqlserver_process_memory_percent|SQL Server プロセス メモリの割合|Percent|最大値|オペレーティング システムによって測定された SQL Server プロセスのメモリ使用率 (%)。 現在、サーバーレス データベースでのみ使用できます。|なし|
|tempdb_data_size|Tempdb データ ファイル サイズ (KB)|Count|最大値|Tempdb データ ファイル サイズ (KB)。 データ ウェアハウスには適用されません。|なし|
|tempdb_log_size|Tempdb ログ ファイル サイズ (KB)|Count|最大値|Tempdb ログ ファイル サイズ (KB)。 データ ウェアハウスには適用されません。|なし|
|tempdb_log_used_percent|Tempdb ログ使用率|Percent|最大値|Tempdb ログ使用率。 データ ウェアハウスには適用されません。|なし|
|local_tempdb_usage_percent|ローカル tempdb の割合|Percent|Average|ローカル tempdb の割合。 データ ウェアハウスにのみ適用されます。|なし|
|app_cpu_billed|課金されるアプリ CPU|Count|合計|課金されるアプリ CPU。 サーバーレス データベースに適用されます。|なし|
|app_cpu_percent|アプリ CPU の割合|Percent|Average|アプリ CPU の割合。 サーバーレス データベースに適用されます。|なし|
|app_memory_percent|アプリ メモリの割合|Percent|Average|メモリの割合 (%)。 サーバーレス データベースに適用されます。|なし|
|allocated_data_storage|割り当て済みのデータ領域|バイト|Average|割り当て済みのデータ ストレージ。 データ ウェアハウスには適用されません。|なし|
|memory_usage_percent|メモリの割合|Percent|最大値|メモリの割合。 データ ウェアハウスにのみ適用されます。|なし|
|full_backup_size_bytes|完全バックアップ ストレージ サイズ|バイト|最大値|累積的な完全バックアップ ストレージ サイズ。 仮想コア ベースのデータベースに適用されます。 ハイパースケール データベースには適用されません。|なし|
|diff_backup_size_bytes|差分バックアップ ストレージ サイズ|バイト|最大値|累積的な差分バックアップ ストレージ サイズ。 仮想コア ベースのデータベースに適用されます。 ハイパースケール データベースには適用されません。|なし|
|log_backup_size_bytes|ログ バックアップ ストレージ サイズ|バイト|最大値|累積的なログ バックアップ ストレージ サイズ。 仮想コア ベースのデータベースに適用されます。 ハイパースケール データベースには適用されません。|なし|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|Average|CPU の割合|なし|
|database_cpu_percent|CPU の割合|Percent|Average|CPU の割合|DatabaseResourceId|
|physical_data_read_percent|データ IO の割合|Percent|Average|データ IO の割合|なし|
|database_physical_data_read_percent|データ IO の割合|Percent|Average|データ IO の割合|DatabaseResourceId|
|log_write_percent|ログ IO の割合|Percent|Average|ログ IO の割合|なし|
|database_log_write_percent|ログ IO の割合|Percent|Average|ログ IO の割合|DatabaseResourceId|
|dtu_consumption_percent|DTU の割合|Percent|Average|DTU の割合。 DTU ベースのエラスティック プールに適用されます。|なし|
|database_dtu_consumption_percent|DTU の割合|Percent|Average|DTU の割合|DatabaseResourceId|
|storage_percent|使用済みのデータ領域の割合|Percent|Average|使用済みのデータ領域の割合|なし|
|workers_percent|ワーカーの割合|Percent|Average|ワーカーの割合|なし|
|database_workers_percent|ワーカーの割合|Percent|Average|ワーカーの割合|DatabaseResourceId|
|sessions_percent|セッションの割合|Percent|Average|セッションの割合|なし|
|database_sessions_percent|セッションの割合|Percent|Average|セッションの割合|DatabaseResourceId|
|eDTU_limit|eDTU 制限|Count|Average|eDTU 制限。 DTU ベースのエラスティック プールに適用されます。|なし|
|storage_limit|データの最大サイズ|バイト|Average|データの最大サイズ|なし|
|eDTU_used|使用済み eDTU|Count|Average|使用された eDTU。 DTU ベースのエラスティック プールに適用されます。|なし|
|database_eDTU_used|使用済み eDTU|Count|Average|使用済み eDTU|DatabaseResourceId|
|storage_used|使用済みのデータ領域|バイト|Average|使用済みのデータ領域|なし|
|database_storage_used|使用済みのデータ領域|バイト|Average|使用済みのデータ領域|DatabaseResourceId|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|Average|インメモリ OLTP ストレージの割合|なし|
|cpu_limit|CPU 制限|Count|Average|CPU 制限。 仮想コア ベースのエラスティック プールに適用されます。|なし|
|database_cpu_limit|CPU 制限|Count|Average|CPU 制限|DatabaseResourceId|
|cpu_used|使用された CPU|Count|Average|使用された CPU。 仮想コア ベースのエラスティック プールに適用されます。|なし|
|database_cpu_used|使用された CPU|Count|Average|使用された CPU|DatabaseResourceId|
|sqlserver_process_core_percent|SQL Server プロセス コアの割合|Percent|最大値|SQL DB プロセスに対する CPU 使用率 (%)。 エラスティック プールに適用されます。|なし|
|sqlserver_process_memory_percent|SQL Server プロセス メモリの割合|Percent|最大値|SQL DB プロセスに対するメモリ使用率 (%)。 エラスティック プールに適用されます。|なし|
|tempdb_data_size|Tempdb データ ファイル サイズ (KB)|Count|最大値|Tempdb データ ファイル サイズ (KB)|なし|
|tempdb_log_size|Tempdb ログ ファイル サイズ (KB)|Count|最大値|Tempdb ログ ファイル サイズ (KB)|なし|
|tempdb_log_used_percent|Tempdb ログ使用率|Percent|最大値|Tempdb ログ使用率|なし|
|allocated_data_storage|割り当て済みのデータ領域|バイト|Average|割り当て済みのデータ領域|なし|
|database_allocated_data_storage|割り当て済みのデータ領域|バイト|Average|割り当て済みのデータ領域|DatabaseResourceId|
|allocated_data_storage_percent|割り当て済みのデータ領域の割合|Percent|最大値|割り当て済みのデータ領域の割合|なし|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU の割合|Percent|Average|DTU の割合|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU の割合|Percent|Average|DTU の割合|DatabaseResourceId、ElasticPoolResourceId|
|storage_used|使用済みのデータ領域|バイト|Average|使用済みのデータ領域|ElasticPoolResourceId|
|database_storage_used|使用済みのデータ領域|バイト|Average|使用済みのデータ領域|DatabaseResourceId、ElasticPoolResourceId|
|dtu_used|使用された DTU|Count|Average|使用された DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|virtual_core_count|仮想コア数|Count|Average|仮想コア数|なし|
|avg_cpu_percent|平均 CPU の割合|Percent|Average|平均 CPU の割合|なし|
|reserved_storage_mb|予約済みストレージ スペース|Count|Average|予約済みストレージ スペース|なし|
|storage_space_used_mb|使用済みストレージ スペース|Count|Average|使用済みストレージ スペース|なし|
|io_requests|IO 要求数|Count|Average|IO 要求数|なし|
|io_bytes_read|読み取り IO バイト|バイト|Average|読み取り IO バイト|なし|
|io_bytes_written|書き込み IO バイト|バイト|Average|書き込み IO バイト|なし|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity (使用済み容量)|バイト|Average|アカウントの使用済み容量|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity (BLOB 容量)|バイト|Average|ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。|BlobType、Tier|
|BlobCount|Blob Count (BLOB 数)|Count|Average|ストレージ アカウントの Blob service 内の BLOB の数。|BlobType、Tier|
|ContainerCount|Blob Container Count (BLOB コンテナー数)|Count|Average|ストレージ アカウントの Blob service 内のコンテナーの数。|なし|
|IndexCapacity|インデックスの容量|バイト|Average|ADLS Gen2 (階層構造) インデックスで使用されるストレージの容量 (バイト単位)。|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity (テーブル容量)|バイト|Average|ストレージ アカウントの Table service によって使われているストレージの量 (バイト単位)。|なし|
|TableCount|Table Count (テーブル数)|Count|Average|ストレージ アカウントの Table service 内のテーブルの数。|なし|
|TableEntityCount|Table Entity Count (テーブル エンティティ数)|Count|Average|ストレージ アカウントの Table service 内のテーブル エンティティの数。|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|File Capacity (ファイル容量)|バイト|Average|ストレージ アカウントの File サービスによって使われているストレージの量 (バイト単位)。|FileShare|
|FileCount|File Count (ファイル数)|Count|Average|ストレージ アカウントの File サービス内のファイルの数。|FileShare|
|FileShareCount|File Share Count (ファイル共有数)|Count|Average|ストレージ アカウントの File サービス内のファイル共有の数。|なし|
|FileShareSnapshotCount|ファイル共有のスナップショット数|Count|Average|ストレージ アカウントの Files サービス内の共有に存在するスナップショットの数。|FileShare|
|FileShareSnapshotSize|ファイル共有のスナップショットのサイズ|バイト|Average|ストレージ アカウントの Files サービスのスナップショットによって使用される記憶域の容量 (バイト単位)。|FileShare|
|FileShareQuota|ファイル共有のクォータ サイズ|バイト|Average|Azure Files サービスで使用できる記憶域の容量の上限 (バイト単位)。|FileShare|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication、FileShare|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication、FileShare|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication、FileShare|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication、FileShare|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication、FileShare|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication、FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity (キュー容量)|バイト|Average|ストレージ アカウントの Queue サービスによって使われているストレージの量 (バイト単位)。|なし|
|QueueCount|Queue Count (キュー数)|Count|Average|ストレージ アカウントの Queue サービス内のキューの数。|なし|
|QueueMessageCount|Queue Message Count (キュー メッセージ数)|Count|Average|ストレージ アカウントの Queue サービス内のキュー メッセージの概数。|なし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ClientIOPS|クライアント IOPS の合計|Count|Average|キャッシュによって処理された、クライアント ファイル操作の速度。|なし|
|ClientLatency|クライアントの平均待機時間|ミリ秒|Average|ストレージ キャッシュに対するクライアント ファイル操作の平均待機時間。|なし|
|ClientReadIOPS|クライアントの読み取り IOPS|CountPerSecond|Average|1 秒あたりのクライアントの読み取り操作数。|なし|
|ClientReadThroughput|キャッシュ読み取りの平均スループット|BytesPerSecond|Average|クライアントの読み取りデータ転送速度。|なし|
|ClientWriteIOPS|クライアントの書き込み IOPS|CountPerSecond|Average|1 秒あたりのクライアントの書き込み操作数。|なし|
|ClientWriteThroughput|キャッシュ書き込みの平均スループット|BytesPerSecond|Average|クライアントの書き込みデータ転送速度。|なし|
|ClientMetadataReadIOPS|クライアント メタデータの読み取り IOPS|CountPerSecond|Average|永続的な状態を変更しない、キャッシュに送信されるクライアント ファイル操作 (データ読み取りを除く) の速度。|なし|
|ClientMetadataWriteIOPS|クライアント メタデータの書き込み IOPS|CountPerSecond|Average|永続的な状態を変更する、キャッシュに送信されるクライアント ファイル操作 (データ書き込みを除く) の速度。|なし|
|ClientLockIOPS|クライアント ロック IOPS|CountPerSecond|Average|1 秒あたりのクライアント ファイルのロック操作数。|なし|
|StorageTargetHealth|ストレージ ターゲットの正常性|Count|Average|キャッシュとストレージ ターゲット間の接続性テストの結果を示すブール値。|なし|
|Uptime|Uptime|Count|Average|キャッシュと監視システム間の接続性テストの結果を示すブール値。|なし|
|StorageTargetIOPS|StorageTarget IOPS の合計|Count|Average|キャッシュが特定の StorageTarget に送信するすべてのファイル操作の速度。|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget の書き込み IOPS|Count|Average|キャッシュから特定の StorageTarget に送信されるファイル書き込み操作の速度。|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget の非同期書き込みスループット|BytesPerSecond|Average|キャッシュから特定の StorageTarget にデータが非同期で書き込まれる速度。 これらは、クライアントがブロックされない便宜的な書き込みです。|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget の同期書き込みスループット|BytesPerSecond|Average|キャッシュから特定の StorageTarget にデータが同期書き込みされる速度。 これらの書き込みは、クライアントがブロックする原因となります。|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget の合計書き込みスループット|BytesPerSecond|Average|キャッシュから特定の StorageTarget にデータが書き込まれる合計速度。|StorageTarget|
|StorageTargetLatency|StorageTarget の待機時間|ミリ秒|Average|キャッシュが特定の StorageTarget に送信するすべてのファイル操作の平均ラウンド トリップ待機時間。|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget のメタデータ読み取り IOPS|CountPerSecond|Average|キャッシュから特定の StorageTarget に送信される、永続的な状態を変更しないファイル操作 (読み取り操作は除く) の速度。|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget のメタデータ書き込み IOPS|CountPerSecond|Average|キャッシュから特定の StorageTarget に送信される、永続的な状態の変更があるファイル操作 (書き込み操作は除く) の速度。|StorageTarget|
|StorageTargetReadIOPS|StorageTarget の読み取り IOPS|CountPerSecond|Average|キャッシュから特定の StorageTarget に送信されるファイル読み取り操作の速度。|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget の先読みスループット|BytesPerSecond|Average|好都合なタイミングを生かして、StorageTarget からキャッシュに適宜データを読み込む速度。|StorageTarget|
|StorageTargetFillThroughput|StorageTarget のフィル スループット|BytesPerSecond|Average|キャッシュ ミスを処理するためにキャッシュが StorageTarget からデータを読み取る速度。|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget の合計読み取りスループット|BytesPerSecond|Average|特定の StorageTarget からキャッシュにデータが読み取られる合計速度。|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ServerSyncSessionResult|同期セッションの結果|Count|Average|サーバー エンドポイントがクラウド エンドポイントとの同期セッションを正常に完了するたびに 1 の値をログ記録するメトリック|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|同期されたファイル数|Count|合計|同期したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncBatchTransferredFileBytes|同期したバイト数|バイト|合計|同期セッションに転送されたファイルの合計サイズ|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncServerHeartbeat|サーバーのオンライン状態|Count|最大値|クラウド エンドポイントとのハートビートを正常に記録するたびに 1 の値をログ記録するメトリック|ServerName|
|StorageSyncRecallIOTotalSizeBytes|クラウドを使った階層化の呼び戻し|バイト|合計|サーバーに呼び戻されたデータの合計サイズ|ServerName|
|StorageSyncRecalledTotalNetworkBytes|クラウドを使った階層化の呼び戻しサイズ|バイト|合計|呼び戻されたデータのサイズ|SyncGroupName、ServerName|
|StorageSyncRecallThroughputBytesPerSecond|クラウドを使った階層化の呼び戻しスループット|BytesPerSecond|Average|データの呼び戻しスループットのサイズ|SyncGroupName、ServerName|
|StorageSyncRecalledNetworkBytesByApplication|アプリケーションによるクラウドを使った階層化の呼び戻しサイズ|バイト|合計|アプリケーションによって呼び戻されたデータのサイズ|SyncGroupName、ServerName、ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|同期されたファイル数|Count|合計|同期したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupBatchTransferredFileBytes|同期したバイト数|バイト|合計|同期セッションに転送されたファイルの合計サイズ|SyncGroupName、ServerEndpointName、SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|同期されたファイル数|Count|合計|同期したファイルの数|ServerEndpointName、SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|ServerEndpointName、SyncDirection|
|ServerEndpointBatchTransferredFileBytes|同期したバイト数|バイト|合計|同期セッションに転送されたファイルの合計サイズ|ServerEndpointName、SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ServerHeartbeat|サーバーのオンライン状態|Count|最大値|クラウド エンドポイントとのハートビートを正常に記録するたびに 1 の値をログ記録するメトリック|ServerResourceId、ServerName|
|ServerRecallIOTotalSizeBytes|クラウドを使った階層化の呼び戻し|バイト|合計|サーバーに呼び戻されたデータの合計サイズ|ServerResourceId、ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|Percent|最大値|SU % 使用率|LogicalName、PartitionId|
|InputEvents|入力イベント|Count|合計|入力イベント|LogicalName、PartitionId|
|InputEventBytes|入力イベントのバイト数|バイト|合計|入力イベントのバイト数|LogicalName、PartitionId|
|LateInputEvents|遅延入力イベント|Count|合計|遅延入力イベント|LogicalName、PartitionId|
|OutputEvents|出力イベント|Count|合計|出力イベント|LogicalName、PartitionId|
|ConversionErrors|データ変換エラー|Count|合計|データ変換エラー|LogicalName、PartitionId|
|エラー|実行時エラー|Count|合計|実行時エラー|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|順不同のイベント|Count|合計|順不同のイベント|LogicalName、PartitionId|
|AMLCalloutRequests|関数の要求|Count|合計|関数の要求|LogicalName、PartitionId|
|AMLCalloutFailedRequests|失敗した関数の要求|Count|合計|失敗した関数の要求|LogicalName、PartitionId|
|AMLCalloutInputEvents|関数のイベント|Count|合計|関数のイベント|LogicalName、PartitionId|
|DeserializationError|逆シリアル化の入力エラー|Count|合計|逆シリアル化の入力エラー|LogicalName、PartitionId|
|EarlyInputEvents|初期入力イベント|Count|合計|初期入力イベント|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|透かしの遅延|Seconds|最大値|透かしの遅延|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|バックログされた入力イベント|Count|最大値|バックログされた入力イベント|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|受信した入力ソース|Count|合計|受信した入力ソース|LogicalName、PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られたメッセージの数|なし|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られた無効なメッセージの数|なし|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|バイト|合計|すべてのイベント ソースから読み取られたバイト数|なし|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|バイト|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|なし|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|なし|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|なし|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|Average|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|なし|
|WarmStorageMaxProperties|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|なし|
|WarmStorageUsedProperties|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|なし|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|イベント ソースから読み取られたメッセージの数|なし|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|イベント ソースから読み取られた無効なメッセージの数|なし|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|バイト|合計|イベント ソースから読み取られたバイト数|なし|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|バイト|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|なし|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|なし|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|なし|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|Average|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|なし|
|WarmStorageMaxProperties|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|なし|
|WarmStorageUsedProperties|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|なし|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Average|サンプル期間にわたる読み取り操作による平均ディスク スループット。|なし|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Average|サンプル期間にわたる書き込み操作による平均ディスク スループット。|なし|
|Disk Read Bytes|Disk Read Bytes|バイト|合計|サンプル期間にわたる読み取り操作による合計ディスク スループット。|なし|
|Disk Write Bytes|Disk Write Bytes|バイト|合計|サンプル期間にわたる書き込み操作による合計ディスク スループット。|なし|
|DiskReadOperations|ディスク読み取り操作数|Count|合計|以前のサンプル期間内の IO 読み取り操作数。 これらの操作はサイズが変動する場合があることに注意してください。|なし|
|DiskWriteOperations|Disk Write Operations|Count|合計|以前のサンプル期間内の IO 書き込み操作数。 これらの操作はサイズが変動する場合があることに注意してください。|なし|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|以前のサンプル期間内の IO 読み取り操作の平均数。 これらの操作はサイズが変動する場合があることに注意してください。|なし|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|以前のサンプル期間内の IO 書き込み操作の平均数。 これらの操作はサイズが変動する場合があることに注意してください。|なし|
|DiskReadLatency|ディスク読み取り待機時間|ミリ秒|Average|合計読み取り待機時間。 デバイスとカーネルの読み取り待機時間の合計。|なし|
|DiskWriteLatency|ディスク書き込み待機時間|ミリ秒|Average|合計書き込み待機時間。 デバイスとカーネルの書き込み待機時間の合計。|なし|
|NetworkInBytesPerSecond|受信ネットワークのバイト数/秒|BytesPerSecond|Average|受信トラフィックの平均ネットワーク スループット。|なし|
|NetworkOutBytesPerSecond|送信ネットワークのバイト数/秒|BytesPerSecond|Average|送信トラフィックの平均ネットワーク スループット。|なし|
|Network In|Network In|バイト|合計|受信トラフィックの合計ネットワーク スループット。|なし|
|Network Out|Network Out|バイト|合計|送信トラフィックの合計ネットワーク スループット。|なし|
|MemoryUsed|メモリ使用量|バイト|Average|VM によって使用されているマシンのメモリの容量。|なし|
|MemoryGranted|許可されているメモリ量|バイト|Average|ホストによって VM に許可されたメモリの容量。 VMkernel がメモリを必要とした場合、一度アクセスされ、許可されたメモリがスワップ アウトされるか、バルーニングされるまで、メモリはホストに与えられません。|なし|
|MemoryActive|アクティブなメモリ|バイト|Average|過去の小さな時間枠で VM によって使用されたメモリの容量。 これは、VM が現在必要としているメモリの容量の「真」の数値です。 ゲストのパフォーマンスに影響を与えずに、追加の未使用メモリをスワップ アウトするか、バルーニングできます。|なし|
|Percentage CPU|Percentage CPU|Percent|Average|ノードの CPU 使用率。 この値は、100% でシステム上のすべてのプロセッサ コアを表します。 たとえば、4 コア システムの 50% を使用する双方向 VM は、2 つのコアを完全に使用します。|なし|
|PercentageCpuReady|CPU Ready の割合|ミリ秒|合計|準備時間は、過去の更新間隔で CPU が利用可能になるまで待機するのに費やされた時間です。|なし|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|CPU の割合|Percent|Average|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|Average|メモリの割合|インスタンス|
|DiskQueueLength|ディスク キューの長さ|Count|Average|ディスク キューの長さ|インスタンス|
|HttpQueueLength|HTTP キューの長さ|Count|Average|HTTP キューの長さ|インスタンス|
|BytesReceived|受信データ|バイト|合計|受信データ|インスタンス|
|BytesSent|送信データ|バイト|合計|送信データ|インスタンス|
|TcpSynSent|TCP SYN 送信済み|Count|Average|TCP SYN 送信済み|インスタンス|
|TcpSynReceived|TCP SYN 受信済み|Count|Average|TCP SYN 受信済み|インスタンス|
|TcpEstablished|TCP 確立済み|Count|Average|TCP 確立済み|インスタンス|
|TcpFinWait1|TCP FIN の待機 1|Count|Average|TCP FIN の待機 1|インスタンス|
|TcpFinWait2|TCP FIN の待機 2|Count|Average|TCP FIN の待機 2|インスタンス|
|TcpClosing|TCP 終了|Count|Average|TCP 終了|インスタンス|
|TcpCloseWait|TCP 終了を待機中|Count|Average|TCP 終了を待機中|インスタンス|
|TcpLastAck|TCP 最後の ACK|Count|Average|TCP 最後の ACK|インスタンス|
|TcpTimeWait|TCP 待機中|Count|Average|TCP 待機中|インスタンス|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (関数を除く)

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|インスタンス|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|バイト|合計|受信データ|インスタンス|
|BytesSent|送信データ|バイト|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|バイト|Average|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|バイト|Average|平均メモリ ワーキング セット|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|Average|平均応答時間|インスタンス|
|AppConnections|接続|Count|Average|接続|インスタンス|
|ハンドル|ハンドルの数|Count|Average|ハンドルの数|インスタンス|
|Threads|Thread Count|Count|Average|Thread Count|インスタンス|
|PrivateBytes|Private Bytes|バイト|Average|Private Bytes|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|Average|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|Average|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|Average|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|Average|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|
|HealthCheckStatus|正常性チェックの状態|Count|Average|正常性チェックの状態|インスタンス|
|FileSystemUsage|ファイル システムの使用量|バイト|Average|ファイル システムの使用量|なし|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (関数)

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|受信データ|バイト|合計|受信データ|インスタンス|
|BytesSent|送信データ|バイト|合計|送信データ|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|バイト|Average|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|バイト|Average|平均メモリ ワーキング セット|インスタンス|
|FunctionExecutionUnits|関数の実行単位|MB/ミリ秒|合計|[関数の実行単位](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|インスタンス|
|FunctionExecutionCount|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|PrivateBytes|Private Bytes|バイト|Average|Private Bytes|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|Average|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|Average|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|Average|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|Average|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|
|HealthCheckStatus|正常性チェックの状態|Count|Average|正常性チェックの状態|インスタンス|
|FileSystemUsage|ファイル システムの使用量|バイト|Average|ファイル システムの使用量|なし|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|インスタンス|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|バイト|合計|受信データ|インスタンス|
|BytesSent|送信データ|バイト|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|バイト|Average|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|バイト|Average|平均メモリ ワーキング セット|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|Average|平均応答時間|インスタンス|
|HttpResponseTime|応答時間|Seconds|Average|応答時間|インスタンス|
|FunctionExecutionUnits|関数の実行単位|Count|合計|関数の実行単位|インスタンス|
|FunctionExecutionCount|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|AppConnections|接続|Count|Average|接続|インスタンス|
|ハンドル|ハンドルの数|Count|Average|ハンドルの数|インスタンス|
|Threads|Thread Count|Count|Average|Thread Count|インスタンス|
|PrivateBytes|Private Bytes|バイト|Average|Private Bytes|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|Average|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|Average|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|Average|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|Average|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|
|HealthCheckStatus|正常性チェックの状態|Count|Average|正常性チェックの状態|インスタンス|
|FileSystemUsage|ファイル システムの使用量|バイト|Average|ファイル システムの使用量|なし|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|バイト|合計|受信データ|インスタンス|
|BytesSent|送信データ|バイト|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|Average|平均応答時間|インスタンス|
|CpuPercentage|CPU の割合|Percent|Average|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|Average|メモリの割合|インスタンス|
|DiskQueueLength|ディスク キューの長さ|Count|Average|ディスク キューの長さ|インスタンス|
|HttpQueueLength|HTTP キューの長さ|Count|Average|HTTP キューの長さ|インスタンス|
|ActiveRequests|アクティブな要求数|Count|合計|アクティブな要求数|インスタンス|
|TotalFrontEnds|フロント エンドの合計|Count|Average|フロント エンドの合計|なし|
|SmallAppServicePlanInstances|S App Service プランの worker の数|Count|Average|S App Service プランの worker の数|なし|
|MediumAppServicePlanInstances|M App Service プランの worker の数|Count|Average|M App Service プランの worker の数|なし|
|LargeAppServicePlanInstances|L App Service プランの worker の数|Count|Average|L App Service プランの worker の数|なし|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|worker の合計数|Count|Average|worker の合計数|なし|
|WorkersAvailable|利用可能な worker の数|Count|Average|利用可能な worker の数|なし|
|WorkersUsed|使用済みの worker の数|Count|Average|使用済みの worker の数|なし|
|CpuPercentage|CPU の割合|Percent|Average|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|Average|メモリの割合|インスタンス|
## <a name="next-steps"></a>次のステップ
* [Azure Monitor のメトリックを確認します](data-platform.md)
* [メトリックでアラートを作成します](alerts-overview.md)
* [メトリックをストレージ、Event Hub、または Log Analytics にエクスポートします](platform-logs-overview.md)
