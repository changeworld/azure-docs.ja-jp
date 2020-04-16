---
title: 診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック
description: Azure Monitor で使用できるメトリックの一覧 (リソースの種類別) です。
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422106"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック

Azure Monitor には、構成なしの[プラットフォーム メトリック](data-platform-metrics.md)が既定で用意されています。 これにより、複数の方法を使用してプラットフォーム メトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 Azure Monitor の統合メトリック パイプラインで現在利用可能なプラットフォーム メトリックの完全な一覧については、[サポートされるメトリック](metrics-supported.md)に関するページを参照してください。 これらのメトリックを照会してアクセスするには、[2018-01-01 バージョンの API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) を使ってください。 他のメトリックについては、ポータルや従来の API で使用できる場合があります。

次の 2 つの方法のいずれかで、プラットフォーム メトリックを Azure Monitor パイプラインから他の場所にエクスポートできます。
1. [診断設定](diagnostic-settings.md)を使用して、Log Analytics、Event Hubs または Azure Storage に送信します。
2. [メトリック REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list) を使用します。

Azure Monitor バックエンドの複雑さにより、すべてのメトリックが診断設定を使用してエクスポート可能であるとは限りません。 次の表に、診断設定を使用したエクスポートが可能か不可能かに関する一覧を示します。

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Null 値と 0 値の動作を変更する 
 
診断設定を使用してエクスポートできるプラットフォーム メトリックについて、Azure Monitor が '0' を 'Null' と解釈するメトリックがいくつかあります。 これにより、実際の '0' (リソースによって生成されたもの) と解釈された '0' (Null) との間で混同が発生していました。 近日中に実施する変更により、診断設定を使用してエクスポートされるプラットフォーム メトリックでは、基になるリソースによって実際に出力されたものでない限り、'0' がエクスポートされなくなります。 この変更は、2020 年 4 月 1 日に予定されていましたが、新型コロナウイルスによる優先順位の変更のため、遅れが生じていたものです。 

注意:

1.  リソース グループまたは特定のリソースを削除すると、影響を受けるリソースのメトリック データは、診断設定のエクスポート先に送信されなくなります。 つまり、Event Hubs、ストレージ アカウント、Log Analytics ワークスペースに表示されなくなります。
2.  この改善は、すべてのパブリック クラウドとプライベート クラウドで利用できるようになります。
3.  この変更は、次のいずれのエクスペリエンスの動作にも影響しません。 
   - 診断設定を使用してエクスポートされるプラットフォーム リソース ログ
   - メトリックス エクスプローラーのメトリック グラフ
   - プラットフォーム メトリックに関するアラート
 
## <a name="metrics-exportable-table"></a>メトリック エクスポート可能テーブル 

表には次の列が含まれています。 
- 診断設定を使用したエクスポートが可能か? 
- NULL または0 による影響 
- ResourceType 
- メトリック 
- MetricDisplayName
- ユニット 
- AggregationType


> [!NOTE]
> 以下の表には、下部に水平スクロール バーが表示されることがあります。 見つからない情報がある場合は、スクロール バーを左端から右端まで動かして確認してください。  


| 診断設定を使用したエクスポートが可能か?  | NULL 出力済み |  ResourceType  |  メトリック  |  MetricDisplayName  |  ユニット  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  メモリ:クリーナーの現在の価格  |  Count  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  メモリ:クリーナーの圧縮不可能なメモリ  |  バイト  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  メモリ:クリーナーの圧縮可能なメモリ  |  バイト  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  スレッド:コマンド プールのビジー状態のスレッド  |  Count  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  スレッド:コマンド プールのアイドル状態のスレッド  |  Count  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  コマンド プールのジョブ キューの長さ  |  Count  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  接続:現在の接続数  |  Count  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  現在のユーザー セッション  |  Count  |  Average | 
| ****はい****  | いいえ |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  スレッド:長時間解析を行っているビジー状態のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  スレッド:長時間解析を行っているアイドル状態のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  スレッド:長時間解析を行っているジョブのキューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  M エンジン メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M エンジン プライベート バイト  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M エンジン QPU  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M エンジン仮想バイト  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  memory_metric  |  メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  メモリ スラッシング  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  メモリ:メモリの制限 - ハード  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  メモリ:メモリの制限 - 高  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  メモリ:メモリの制限 - 低  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  メモリ:メモリの制限 - VertiPaq  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  メモリ:メモリ使用量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Private Bytes  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  スレッド:処理中のプールのビジー状態の I/O ジョブのスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  スレッド:処理中のプールのビジー状態の I/O 以外のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  スレッド:処理中のプールのアイドル状態の I/O ジョブのスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  スレッド:処理中のプールのアイドル状態の I/O 以外のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  スレッド:処理中のプール I/O ジョブ キューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  処理プール ジョブ キューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  クエリ プール ビジー スレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  スレッド:クエリ プールのアイドル状態のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  スレッド:クエリ プール ジョブ キューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  Quota  |  メモリ:Quota  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  メモリ:ブロックされているクォータ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  処理:1 秒あたりの変換行数  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  処理:1 秒あたりの読み取り行数  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  処理:1 秒あたりの書き込み行数  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  スレッド:短時間解析を行っているビジー状態のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  スレッド:短時間解析を行っているアイドル状態のスレッド  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  スレッド:短時間解析を行っているジョブ キューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  成功した接続数 (秒単位)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  合計接続失敗数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  合計接続要求数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  メモリ:VertiPaq 非ページ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  メモリ:VertiPaq ページ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Virtual Bytes  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  BackendDuration  |  バックエンド要求の期間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ApiManagement/service  |  容量  |  容量  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  Duration  |  ゲートウェイ要求の全体の期間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  破棄された EventHub イベント数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  拒否された EventHub イベント数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  成功した EventHub イベント数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  調整された EventHub イベント数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  タイムアウトした EventHub イベント数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  EventHub イベントのサイズ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  EventHub イベントの合計数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  失敗した EventHub イベント数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  FailedRequests  |  失敗したゲートウェイ要求 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  OtherRequests  |  その他のゲートウェイ要求 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  成功したゲートウェイ要求 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  TotalRequests  |  ゲートウェイ要求の合計 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  未承認ゲートウェイ要求 (非推奨)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  アプリの CPU 使用率 (%)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  AppMemoryCommitted  |  割り当て済みアプリ メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  AppMemoryMax  |  最大のアプリ メモリ  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  AppMemoryUsed  |  使用済みアプリ メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  GCPauseTotalCount  |  GC の一時停止回数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTime  |  GC の一時停止合計時間  |  ミリ秒  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  使用可能な古い世代データの最大サイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  古い世代のデータのサイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  古い世代データのサイズに昇格  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  SystemCpuUsagePercentage  |  システム CPU 使用率 (%)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Tomcat グローバル エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Tomcat 合計受信バイト  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Tomcat 要求最大時間  |  ミリ秒  |  最大値 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Tomcat 要求合計数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Tomcat 要求合計時間  |  ミリ秒  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Tomcat 要求平均時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Tomcat 合計送信バイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Tomcat セッション アライブ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Tomcat セッション最大アクティブ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Tomcat セッション最大アライブ時間  |  ミリ秒  |  最大値 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Tomcat セッション作成数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSessionExpiredCount  |  Tomcat セッション期限切れ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  TomcatSessionRejectedCount  |  Tomcat セッション拒否数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes  |  若い世代データのサイズに昇格  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Automation/automationAccounts  |  TotalJob  |  合計ジョブ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  更新プログラムのデプロイ マシンの合計実行回数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  更新プログラムのデプロイの合計実行回数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Dedicated Core Count (専用コアの数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  作成ノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  アイドル状態のノード数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  ジョブ削除の完了イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  ジョブ削除の開始イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  ジョブ無効化の完了イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  ジョブ無効化の開始イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  ジョブの開始イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  ジョブ終了の完了イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  ジョブ終了の開始イベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  プールから移動中のノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority Core Count (優先順位の低いコアの数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  オフライン ノード数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  プール作成イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  プール削除の完了イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  プール削除の開始イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  プールのサイズ変更の完了イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  プールのサイズ変更の開始イベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Preempted Node Count (割り込まれたノードの数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  再起動中のノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  再イメージ化中のノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  実行中のノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  開始ノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  開始タスクの失敗ノード数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  タスク完了イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  タスク失敗イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  タスク開始イベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Low-Priority Node Count (優先順位の低いノードの数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Dedicated Node Count (専用ノードの数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  使用できないノード数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  開始タスクの待機ノード数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  アクティブなコア  |  アクティブなコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  アクティブなノード  |  アクティブなノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  アイドル状態のコア  |  アイドル状態のコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  アイドル状態のノード  |  アイドル状態のノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  完了したジョブ  |  完了したジョブ  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  送信済みジョブ  |  送信されたジョブ  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  終了中のコア  |  終了中のコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  終了中のノード  |  終了中のノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  割り込まれたコア  |  割り込まれたコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  割り込まれたノード  |  割り込まれたノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  クォータ使用率  |  クォータ使用率  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  コア総数  |  コアの合計  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  ノード総数  |  ノード総数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  使用できないコア  |  使用できないコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.BatchAI/workspaces  |  使用できないノード  |  使用できないノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  承認された接続  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  アクティブな接続  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  処理された接続  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  CPU 使用率 (%)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  IO 読み取りバイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  IO 書き込みバイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  メモリの制限  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  メモリ使用量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  メモリ使用率 (%)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  保留中のトランザクション  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  処理されたブロック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  処理されたトランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  キューに登録されたトランザクション  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  処理された要求  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  ストレージの使用状況  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits  |  キャッシュ ヒット数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits0  |  キャッシュ ヒット数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits1  |  キャッシュ ヒット数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits2  |  キャッシュ ヒット数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits3  |  キャッシュ ヒット数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits4  |  キャッシュ ヒット数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits5  |  キャッシュ ヒット数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits6  |  キャッシュ ヒット数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits7  |  キャッシュ ヒット数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits8  |  キャッシュ ヒット数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachehits9  |  キャッシュ ヒット数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheLatency  |  キャッシュの待機時間 (マイクロ秒単位) (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses  |  キャッシュ ミス数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses0  |  キャッシュ ミス数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses1  |  キャッシュ ミス数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses2  |  キャッシュ ミス数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses3  |  キャッシュ ミス数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses4  |  キャッシュ ミス数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses5  |  キャッシュ ミス数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses6  |  キャッシュ ミス数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses7  |  キャッシュ ミス数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses8  |  キャッシュ ミス数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cachemisses9  |  キャッシュ ミス数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead  |  キャッシュの読み取り  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead0  |  キャッシュの読み取り (シャード 0)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead1  |  キャッシュの読み取り (シャード 1)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead2  |  キャッシュの読み取り (シャード 2)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead3  |  キャッシュの読み取り (シャード 3)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead4  |  キャッシュの読み取り (シャード 4)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead5  |  キャッシュの読み取り (シャード 5)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead6  |  キャッシュの読み取り (シャード 6)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead7  |  キャッシュの読み取り (シャード 7)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead8  |  キャッシュの読み取り (シャード 8)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheRead9  |  キャッシュの読み取り (シャード 9)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite  |  キャッシュの書き込み  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite0  |  キャッシュの書き込み (シャード 0)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite1  |  キャッシュの書き込み (シャード 1)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite2  |  キャッシュの書き込み (シャード 2)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite3  |  キャッシュの書き込み (シャード 3)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite4  |  キャッシュの書き込み (シャード 4)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite5  |  キャッシュの書き込み (シャード 5)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite6  |  キャッシュの書き込み (シャード 6)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite7  |  キャッシュの書き込み (シャード 7)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite8  |  キャッシュの書き込み (シャード 8)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  cacheWrite9  |  キャッシュの書き込み (シャード 9)  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients  |  接続されているクライアント数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients0  |  接続されているクライアント数 (シャード 0)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients1  |  接続されているクライアント数 (シャード 1)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients2  |  接続されているクライアント数 (シャード 2)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients3  |  接続されているクライアント数 (シャード 3)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients4  |  接続されているクライアント数 (シャード 4)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients5  |  接続されているクライアント数 (シャード 5)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients6  |  接続されているクライアント数 (シャード 6)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients7  |  接続されているクライアント数 (シャード 7)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients8  |  接続されているクライアント数 (シャード 8)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  connectedclients9  |  接続されているクライアント数 (シャード 9)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  エラー  |  エラー  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys  |  削除されたキー数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys0  |  削除されたキー数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys1  |  削除されたキー数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys2  |  削除されたキー数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys3  |  削除されたキー数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys4  |  削除されたキー数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys5  |  削除されたキー数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys6  |  削除されたキー数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys7  |  削除されたキー数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys8  |  削除されたキー数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  evictedkeys9  |  削除されたキー数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys  |  期限切れキー数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys0  |  期限切れキー数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys1  |  期限切れキー数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys2  |  期限切れキー数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys3  |  期限切れキー数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys4  |  期限切れキー数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys5  |  期限切れキー数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys6  |  期限切れキー数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys7  |  期限切れキー数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys8  |  期限切れキー数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  expiredkeys9  |  期限切れキー数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands  |  取得数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands0  |  取得数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands1  |  取得数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands2  |  取得数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands3  |  取得数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands4  |  取得数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands5  |  取得数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands6  |  取得数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands7  |  取得数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands8  |  取得数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  getcommands9  |  取得数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond  |  1 秒あたりの操作回数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond0  |  1 秒あたりの操作回数 (シャード 0)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond1  |  1 秒あたりの操作回数 (シャード 1)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond2  |  1 秒あたりの操作回数 (シャード 2)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond3  |  1 秒あたりの操作回数 (シャード 3)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond4  |  1 秒あたりの操作回数 (シャード 4)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond5  |  1 秒あたりの操作回数 (シャード 5)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond6  |  1 秒あたりの操作回数 (シャード 6)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond7  |  1 秒あたりの操作回数 (シャード 7)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond8  |  1 秒あたりの操作回数 (シャード 8)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  operationsPerSecond9  |  1 秒あたりの操作回数 (シャード 9)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (シャード 0)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (シャード 1)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (シャード 2)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (シャード 3)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (シャード 4)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (シャード 5)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (シャード 6)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (シャード 7)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (シャード 8)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (シャード 9)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad  |  サーバーの負荷  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad0  |  サーバーの負荷 (シャード 0)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad1  |  サーバーの負荷 (シャード 1)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad2  |  サーバーの負荷 (シャード 2)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad3  |  サーバーの負荷 (シャード 3)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad4  |  サーバーの負荷 (シャード 4)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad5  |  サーバーの負荷 (シャード 5)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad6  |  サーバーの負荷 (シャード 6)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad7  |  サーバーの負荷 (シャード 7)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad8  |  サーバーの負荷 (シャード 8)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  serverLoad9  |  サーバーの負荷 (シャード 9)  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands  |  セット  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands0  |  設定数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands1  |  設定数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands2  |  設定数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands3  |  設定数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands4  |  設定数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands5  |  設定数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands6  |  設定数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands7  |  設定数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands8  |  設定数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  setcommands9  |  設定数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  合計処理数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  合計処理数 (シャード 0)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  合計処理数 (シャード 1)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  合計処理数 (シャード 2)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  合計処理数 (シャード 3)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  合計処理数 (シャード 4)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  合計処理数 (シャード 5)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  合計処理数 (シャード 6)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  合計処理数 (シャード 7)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  合計処理数 (シャード 8)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  合計処理数 (シャード 9)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys  |  合計キー数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys0  |  合計キー数 (シャード 0)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys1  |  合計キー数 (シャード 1)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys2  |  合計キー数 (シャード 2)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys3  |  合計キー数 (シャード 3)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys4  |  合計キー数 (シャード 4)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys5  |  合計キー数 (シャード 5)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys6  |  合計キー数 (シャード 6)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys7  |  合計キー数 (シャード 7)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys8  |  合計キー数 (シャード 8)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  totalkeys9  |  合計キー数 (シャード 9)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory  |  メモリ使用量  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory0  |  メモリ使用量 (シャード 0)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory1  |  メモリ使用量 (シャード 1)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory2  |  メモリ使用量 (シャード 2)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory3  |  メモリ使用量 (シャード 3)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory4  |  メモリ使用量 (シャード 4)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory5  |  メモリ使用量 (シャード 5)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory6  |  メモリ使用量 (シャード 6)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory7  |  メモリ使用量 (シャード 7)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory8  |  メモリ使用量 (シャード 8)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemory9  |  メモリ使用量 (シャード 9)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemorypercentage  |  使用されているメモリの割合  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss  |  RSS メモリ使用量  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss0  |  RSS メモリ使用量 (シャード 0)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss1  |  RSS メモリ使用量 (シャード 1)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss2  |  RSS メモリ使用量 (シャード 2)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss3  |  RSS メモリ使用量 (シャード 3)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss4  |  RSS メモリ使用量 (シャード 4)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss5  |  RSS メモリ使用量 (シャード 5)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss6  |  RSS メモリ使用量 (シャード 6)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss7  |  RSS メモリ使用量 (シャード 7)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss8  |  RSS メモリ使用量 (シャード 8)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Cache/redis  |  usedmemoryRss9  |  RSS メモリ使用量 (シャード 9)  |  バイト  |  最大値 | 
| いいえ  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  ディスクの読み取り  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  ディスクの書き込み  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Network In  |  Network In  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Network Out  |  Network Out  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Bytes/Sec  |  ディスクの読み取り  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Bytes/Sec  |  ディスクの書き込み  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Network In  |  Network In  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Network Out  |  Network Out  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicCompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Used capacity (使用済み容量)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  可用性  |  可用性  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Blob Capacity (BLOB 容量)  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Blob Count (BLOB 数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Blob Container Count (BLOB コンテナー数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  インデックスの容量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  File Capacity (ファイル容量)  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCount  |  File Count (ファイル数)  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  File Share Count (ファイル共有数)  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  ファイル共有のクォータ サイズ  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  ファイル共有のスナップショット数  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  ファイル共有のスナップショットのサイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Queue Capacity (キュー容量)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Queue Count (キュー数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Queue Message Count (キュー メッセージ数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Table Capacity (テーブル容量)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Table Count (テーブル数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Table Entity Count (テーブル エンティティ数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  ブロックされた呼び出し  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  トレーニングされた文字数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Characters Translated (変換文字数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  クライアント エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  DataIn  |  受信データ  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  DataOut  |  送信データ  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  Latency  |  Latency  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  サーバー エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Speech Session Duration (音声セッション継続時間)  |  Seconds  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  成功した呼び出し  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  合計呼び出し数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  合計エラー数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  トークン呼び出しの合計  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  合計トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  使用済みの CPU クレジット  |  使用済みの CPU クレジット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  未使用の CPU クレジット  |  未使用の CPU クレジット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  データ ディスクのキューの深さ  |  データ ディスクのキューの深さ (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  データ ディスク読み取りバイト数/秒  |  データ ディスク読み取りバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  データ ディスク読み取り操作数/秒  |  データ ディスク読み取り操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  データ ディスク書き込みバイト数/秒  |  データ ディスク書き込みバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  データ ディスク書き込み操作数/秒  |  データ ディスク書き込み操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  受信フロー数  |  受信フロー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  受信フローの最大作成速度  |  受信フローの最大作成速度 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Network In  |  課金対象のネットワーク受信 (非推奨)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  受信ネットワーク合計  |  受信ネットワーク合計  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Network Out  |  課金対象のネットワーク送信 (非推奨)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  送信ネットワーク合計  |  送信ネットワーク合計  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS ディスクのキューの深さ  |  OS ディスクのキューの深さ (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS ディスク読み取りバイト数/秒  |  OS ディスク読み取りバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS ディスク読み取り操作数/秒  |  OS ディスク読み取り操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS ディスク書き込みバイト数/秒  |  OS ディスク書き込みバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS ディスク書き込み操作数/秒  |  OS ディスク書き込み操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS Per Disk QD  |  OS ディスク QD (非推奨)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS Per Disk Read Bytes/sec  |  OS ディスク読み取りバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS Per Disk Read Operations/Sec  |  OS ディスク読み取り操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS Per Disk Write Bytes/sec  |  OS ディスク書き込みバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  OS Per Disk Write Operations/Sec  |  OS ディスク書き込み操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  送信フロー数  |  送信フロー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  送信フローの最大作成速度  |  送信フローの最大作成速度 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Per Disk QD  |  データ ディスク QD (非推奨)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Per Disk Read Bytes/sec  |  データ ディスク読み取りバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Per Disk Read Operations/Sec  |  データ ディスク読み取り操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Per Disk Write Bytes/sec  |  データ ディスク書き込みバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Per Disk Write Operations/Sec  |  データ ディスク書き込み操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Premium データ ディスク キャッシュ読み取りヒット  |  Premium データ ディスク キャッシュ読み取りヒット (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Premium データ ディスク キャッシュ読み取りミス  |  Premium データ ディスク キャッシュ読み取りミス (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Premium OS ディスク キャッシュ読み取りヒット  |  Premium OS ディスク キャッシュ読み取りヒット (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachines  |  Premium OS ディスク キャッシュ読み取りミス  |  Premium OS ディスク キャッシュ読み取りミス (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  使用済みの CPU クレジット  |  使用済みの CPU クレジット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  未使用の CPU クレジット  |  未使用の CPU クレジット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  データ ディスクのキューの深さ  |  データ ディスクのキューの深さ (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  データ ディスク読み取りバイト数/秒  |  データ ディスク読み取りバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  データ ディスク読み取り操作数/秒  |  データ ディスク読み取り操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  データ ディスク書き込みバイト数/秒  |  データ ディスク書き込みバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  データ ディスク書き込み操作数/秒  |  データ ディスク書き込み操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Read Bytes  |  Disk Read Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Write Bytes  |  Disk Write Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  受信フロー数  |  受信フロー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  受信フローの最大作成速度  |  受信フローの最大作成速度 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Network In  |  課金対象のネットワーク受信 (非推奨)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  受信ネットワーク合計  |  受信ネットワーク合計  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Network Out  |  課金対象のネットワーク送信 (非推奨)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  送信ネットワーク合計  |  送信ネットワーク合計  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS ディスクのキューの深さ  |  OS ディスクのキューの深さ (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS ディスク読み取りバイト数/秒  |  OS ディスク読み取りバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS ディスク読み取り操作数/秒  |  OS ディスク読み取り操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS ディスク書き込みバイト数/秒  |  OS ディスク書き込みバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS ディスク書き込み操作数/秒  |  OS ディスク書き込み操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk QD  |  OS ディスク QD (非推奨)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk Read Bytes/sec  |  OS ディスク読み取りバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk Read Operations/Sec  |  OS ディスク読み取り操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk Write Bytes/sec  |  OS ディスク書き込みバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk Write Operations/Sec  |  OS ディスク書き込み操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  送信フロー数  |  送信フロー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  送信フローの最大作成速度  |  送信フローの最大作成速度 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk QD  |  データ ディスク QD (非推奨)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk Read Bytes/sec  |  データ ディスク読み取りバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk Read Operations/Sec  |  データ ディスク読み取り操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk Write Bytes/sec  |  データ ディスク書き込みバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk Write Operations/Sec  |  データ ディスク書き込み操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Percentage CPU  |  Percentage CPU  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Premium データ ディスク キャッシュ読み取りヒット  |  Premium データ ディスク キャッシュ読み取りヒット (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Premium データ ディスク キャッシュ読み取りミス  |  Premium データ ディスク キャッシュ読み取りミス (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS ディスク キャッシュ読み取りヒット  |  Premium OS ディスク キャッシュ読み取りヒット (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS ディスク キャッシュ読み取りミス  |  Premium OS ディスク キャッシュ読み取りミス (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  使用済みの CPU クレジット  |  使用済みの CPU クレジット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  未使用の CPU クレジット  |  未使用の CPU クレジット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  データ ディスクのキューの深さ  |  データ ディスクのキューの深さ (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  データ ディスク読み取りバイト数/秒  |  データ ディスク読み取りバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  データ ディスク読み取り操作数/秒  |  データ ディスク読み取り操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  データ ディスク書き込みバイト数/秒  |  データ ディスク書き込みバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  データ ディスク書き込み操作数/秒  |  データ ディスク書き込み操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  受信フロー数  |  受信フロー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  受信フローの最大作成速度  |  受信フローの最大作成速度 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Network In  |  課金対象のネットワーク受信 (非推奨)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  受信ネットワーク合計  |  受信ネットワーク合計  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Network Out  |  課金対象のネットワーク送信 (非推奨)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  送信ネットワーク合計  |  送信ネットワーク合計  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS ディスクのキューの深さ  |  OS ディスクのキューの深さ (プレビュー)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS ディスク読み取りバイト数/秒  |  OS ディスク読み取りバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS ディスク読み取り操作数/秒  |  OS ディスク読み取り操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS ディスク書き込みバイト数/秒  |  OS ディスク書き込みバイト数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS ディスク書き込み操作数/秒  |  OS ディスク書き込み操作数/秒 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per Disk QD  |  OS ディスク QD (非推奨)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per Disk Read Bytes/sec  |  OS ディスク読み取りバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per Disk Read Operations/Sec  |  OS ディスク読み取り操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per Disk Write Bytes/sec  |  OS ディスク書き込みバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per Disk Write Operations/Sec  |  OS ディスク書き込み操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  送信フロー数  |  送信フロー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  送信フローの最大作成速度  |  送信フローの最大作成速度 (プレビュー)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk QD  |  データ ディスク QD (非推奨)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk Read Bytes/sec  |  データ ディスク読み取りバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk Read Operations/Sec  |  データ ディスク読み取り操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk Write Bytes/sec  |  データ ディスク書き込みバイト数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk Write Operations/Sec  |  データ ディスク書き込み操作数/秒 (非推奨)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium データ ディスク キャッシュ読み取りヒット  |  Premium データ ディスク キャッシュ読み取りヒット (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium データ ディスク キャッシュ読み取りミス  |  Premium データ ディスク キャッシュ読み取りミス (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS ディスク キャッシュ読み取りヒット  |  Premium OS ディスク キャッシュ読み取りヒット (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS ディスク キャッシュ読み取りミス  |  Premium OS ディスク キャッシュ読み取りミス (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  CPU 使用率  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  メモリ使用量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  1 秒あたりの受信ネットワーク バイト数  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  1 秒あたりの送信ネットワーク バイト数  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  実行継続時間  |  ミリ秒  |  合計 | 
| **はい**  | いいえ |  Microsoft.ContainerRegistry/registries  |  SuccessfulPullCount  |  成功したプルの数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerRegistry/registries  |  SuccessfulPushCount  |  成功したプッシュの数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  プルの合計数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  合計のプッシュ数  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  マネージド クラスターで使用可能な CPU コアの合計数  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  マネージド クラスターで使用可能なメモリの合計量  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  さまざまなノードの状態の条件  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  フェーズごとのポッドの数  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  準備完了状態のポッドの数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  AvailableCapacity  |  使用可能な容量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  クラウドのアップロードされたバイト数 (デバイス)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  クラウドのアップロードされたバイト数 (共有)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  クラウド ダウンロード スループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  クラウドのダウンロード スループット (共有)  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  クラウド アップロード スループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  クラウドへのアップロード時のスループット (共有)  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Edge コンピューティング - メモリ使用量  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Edge コンピューティング - CPU 使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  読み取りスループット (ネットワーク)  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  書き込みスループット (ネットワーク)  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  合計容量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  失敗した実行  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  成功した実行  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  ActivityCancelledRuns  |  キャンセルしたアクティビティの実行回数のメトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  失敗したアクティビティ実行回数のメトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  成功したアクティビティ実行回数のメトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  FactorySizeInGbUnits  |  合計の工場出荷時サイズ (GB 単位)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  統合実行時に使用可能なメモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAverageTaskPickupDelay  |  統合ランタイム キューの期間  |  Seconds  |  Average | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  統合実行時の CPU 使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  IntegrationRuntimeQueueLength  |  統合ランタイム キューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  MaxAllowedFactorySizeInGbUnits  |  許可されている最大の工場出荷時サイズ (GB 単位)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  MaxAllowedResourceCount  |  許可されているエンティティの最大数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  PipelineCancelledRuns  |  キャンセルしたパイプライン実行回数のメトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  失敗したパイプライン実行回数のメトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  成功したパイプライン実行回数のメトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  ResourceCount  |  エンティティの合計数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  TriggerCancelledRuns  |  キャンセルしたトリガー実行の回数メトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  失敗したトリガー実行の回数メトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  成功したトリガー実行の回数メトリック  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Cancelled AU Time (取り消された AU 時間)  |  Seconds  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Failed AU Time (失敗した AU 時間)  |  Seconds  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Successful AU Time (成功した AU 時間)  |  Seconds  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Cancelled Jobs (取り消されたジョブ)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Failed Jobs (失敗したジョブ)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Successful Jobs (成功したジョブ)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeStore/accounts  |  DataRead  |  データ読み取り量  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  データ書き込み量  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  読み取り要求  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  保存量の合計  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  書き込み要求  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  アクティブな接続  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  使用されたバックアップ ストレージ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  失敗した接続  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU 使用率  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  IO の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Network Out  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Network In  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  レプリケーションのラグ (秒単位)  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  サーバー ログ ストレージの上限  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  サーバー ログ ストレージの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  サーバー ログ ストレージの使用量  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  ストレージの制限  |  バイト  |  最大値 | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  ストレージの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  使用済みストレージ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  active_connections  |  アクティブな接続  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  使用されたバックアップ ストレージ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  失敗した接続  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  CPU 使用率  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  IO の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Network Out  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Network In  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  レプリケーションのラグ (秒単位)  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  サーバー ログ ストレージの上限  |  バイト  |  最大値 | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  サーバー ログ ストレージの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  サーバー ログ ストレージの使用量  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  ストレージの制限  |  バイト  |  最大値 | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  ストレージの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DBforMySQL/servers  |  storage_used  |  使用済みストレージ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  アクティブな接続  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  使用されたバックアップ ストレージ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  失敗した接続  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU 使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  IO の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Network Out  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Network In  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Max Lag Across Replicas (レプリカ間の最大ラグ)  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Replica Lag (レプリカ ラグ)  |  Seconds  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  サーバー ログ ストレージの上限  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  サーバー ログ ストレージの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  サーバー ログ ストレージの使用量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  ストレージの制限  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  ストレージの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  使用済みストレージ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  アクティブな接続  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU 使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  IOPS  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Network Out  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Network In  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  ストレージの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  使用済みストレージ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.nodes  |  Digital Twins ノード テレメトリのプレースホルダー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  破棄した C2D メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  C2D メッセージ配信完了  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  C2D メッセージ拒否数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  失敗したダイレクト メソッドの呼び出し  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  ダイレクト メソッドの呼び出しの要求サイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  ダイレクト メソッドの呼び出しの応答サイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  成功したダイレクト メソッドの呼び出し  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  失敗したバックエンドからのツイン読み取り  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  バック エンドからのツイン読み取りの応答サイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  成功したバック エンドからのツイン読み取り  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  失敗したバック エンドからのツイン更新  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  バックエンドからのツイン更新のサイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  成功したバックエンドからのツイン更新  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  期限切れ C2D メッセージ (プレビュー)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  configuration  |  構成メトリック  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  接続されているデバイス (プレビュー)  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  ルーティング: メッセージ/イベントに配信されたメッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  ルーティング: Event Hub に配信されたメッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  ルーティング: Service Bus キューに配信されたメッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  ルーティング: Service Bus トピックに配信されたメッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  ルーティング: ストレージに配信されたメッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  ルーティング: ストレージに配信された BLOB  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  ルーティング: ストレージに配信されたデータ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  ルーティング: メッセージ/イベントのメッセージの待機時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  ルーティング: Event Hub のメッセージの待機時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  ルーティング: Service Bus キューのメッセージの待機時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  ルーティング: Service Bus トピックのメッセージの待機時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  ルーティング: ストレージのメッセージの待機時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  ルーティング: 破棄されたテレメトリ メッセージ   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  ルーティング: フォールバックのために配信されたメッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  ルーティング: 互換性のないテレメトリ メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  ルーティング: 孤立したテレメトリ メッセージ   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  ルーティング: 配信されたテレメトリ メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  テレメトリ メッセージ送信試行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  調整エラーの数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  送信済みテレメトリ メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  失敗したデバイスからのツイン読み取り  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  デバイスからのツイン読み取りの応答サイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  成功したデバイスからのツイン読み取り  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  失敗したデバイスからのツイン更新  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  デバイスからのツイン更新のサイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  成功したデバイスからのツイン更新  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  使用されているメッセージの合計数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  デバイス データの合計使用量  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  デバイス データの合計使用量 (プレビュー)  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  接続されているデバイス (非推奨)   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  デバイスの総数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Event Grid 配信 (プレビュー)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Event Grid の待機時間 (プレビュー)  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  失敗したジョブの取り消し  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  成功したジョブの取り消し  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  完了したジョブ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  失敗したメソッド呼び出しジョブの作成  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  成功したメソッド呼び出しジョブの作成  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  失敗したツイン更新ジョブの作成  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  成功したツイン更新ジョブの作成  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  失敗したジョブ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  失敗したジョブ一覧の呼び出し  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  成功したジョブ一覧の呼び出し  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  失敗したジョブ クエリ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  成功したジョブ クエリ  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  デバイスの合計数 (プレビュー)  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  失敗したツイン クエリ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  ツイン クエリの結果のサイズ  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  成功したツイン クエリ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Attestation attempts (構成証明試行回数)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Devices assigned (割り当て済みデバイス数)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Registration attempts (登録試行回数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  使用可能なストレージ  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra 接続の終了  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra 要求の料金  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra 要求  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  データ利用状況  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  ドキュメント数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  ドキュメントのクォータ  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  インデックスの使用量  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  メタデータの要求数  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Mongo 要求の料金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo Requests (Mongo 要求数)  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Mongo 要求のレート  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Mongo Delete 要求のレート  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Mongo Insert 要求のレート  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Mongo Query 要求のレート  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Mongo 更新要求のレート  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  プロビジョニングされたスループット  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  P99 のレプリケーション待機時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  サービスの可用性  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  要求の合計数  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  合計要求単位数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EnterpriseKnowledgeGraph/services  |  FailureCount  |  失敗数  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessCount  |  成功数  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessLatency  |  成功の待機時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.EnterpriseKnowledgeGraph/services  |  TransactionCount  |  トランザクション数  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  DeadLetteredCount  |  配信不能イベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventGrid/domains  |  DeliveryAttemptFailCount  |  配信できなかったイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  DeliverySuccessCount  |  配信されたイベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventGrid/domains  |  DestinationProcessingDurationInMs  |  宛先処理継続時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  DroppedEventCount  |  削除されたイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  MatchedEventCount  |  一致するイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  PublishFailCount  |  発行失敗イベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  PublishSuccessCount  |  発行されたイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/domains  |  PublishSuccessLatencyInMs  |  成功した発行の待機時間  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  配信不能イベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  配信できなかったイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  配信されたイベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  宛先処理継続時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  削除されたイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  一致するイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  発行失敗イベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  発行されたイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  成功した発行の待機時間  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  不一致のイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  発行失敗イベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  発行されたイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/topics  |  PublishSuccessLatencyInMs  |  成功した発行の待機時間  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  不一致のイベント  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  AvailableMemory  |  使用可能なメモリ  |  Percent  |  最大値 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  バックログのキャプチャ。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  CapturedBytes  |  キャプチャされたバイト数。  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  CapturedMessages  |  キャプチャされたメッセージ数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  切断された接続数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  開かれている接続数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  着信バイト数。  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  受信メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  受信要求  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  発信バイト数。  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  送信メッセージ  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  クォータ超過エラー数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  ServerErrors  |  サーバー エラー。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  成功した要求  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  スロットルされた要求数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/clusters  |  UserErrors  |  ユーザー エラー数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  バックログのキャプチャ。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  キャプチャされたバイト数。  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  キャプチャされたメッセージ数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  切断された接続数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  開かれている接続数。  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHABL  |  アーカイブ バックログ メッセージ数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  アーカイブ メッセージ スループット (非推奨)  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  アーカイブ メッセージ数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  受信バイト数 (非推奨)  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  受信バイト数 (廃止) (非推奨)  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  受信メッセージ (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  送信バイト数 (非推奨)  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  送信バイト数 (廃止) (非推奨)  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  送信メッセージ (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  失敗した要求数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  着信バイト数。  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  受信メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  受信要求  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  INMSGS  |  受信メッセージ (廃止) (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  INREQS  |  受信要求数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  INTERR  |  内部サーバー エラー数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  MISCERR  |  その他のエラー数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  発信バイト数。  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  送信メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  送信メッセージ (廃止) (非推奨)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  クォータ超過エラー数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  ServerErrors  |  サーバー エラー。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  サイズ  |  サイズ  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  成功した要求  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  成功した要求数 (非推奨)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  サーバー ビジー エラー数 (非推奨)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  スロットルされた要求数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.EventHub/namespaces  |  UserErrors  |  ユーザー エラー数。  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  カテゴリ別のゲートウェイ要求数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  ゲートウェイ要求数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  アクティブなワーカーの数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.HDInsight/clusters  |  ScalingRequests  |  スケール要求  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  メトリックのしきい値  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  実際の容量  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  実際のメトリック値  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  スケール アクション開始  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  availabilityResults/availabilityPercentage  |  可用性  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  availabilityResults/count  |  可用性テスト  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  availabilityResults/duration  |  可用性テスト継続時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  ページ読み込みのネットワーク接続時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  クライアントの処理時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  受信応答時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  要求送信時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  ブラウザーのページ読み込み時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  dependencies/count  |  依存関係呼び出し  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  dependencies/duration  |  依存関係の期間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  dependencies/failed  |  依存関係呼び出しの失敗回数  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  exceptions/browser  |  ブラウザーの例外  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.Insights/Components  |  exceptions/count  |  例外  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  exceptions/server  |  サーバーの例外  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.Insights/Components  |  pageViews/count  |  ページ ビュー  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  pageViews/duration  |  ページ ビューの読み込み時間  |  MilliSeconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  例外レート  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  使用可能なメモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  プロセス CPU  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  プロセス IO 量  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  プロセッサ時間  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  プロセスのプライベート バイト  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  HTTP 要求の実行時間  |  MilliSeconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  アプリケーション キュー内の HTTP 要求  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  HTTP 要求率  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  requests/count  |  サーバー要求  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.Insights/Components  |  requests/duration  |  サーバーの応答時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  requests/failed  |  失敗した要求  |  Count  |  Count | 
| いいえ  | いいえ |  Microsoft.Insights/Components  |  要求/率  |  サーバー要求率  |  CountPerSecond  |  Average | 
| **はい**  | **はい** |  Microsoft.Insights/Components  |  traces/count  |  トレース  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  サービス API ヒット数の合計  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  サービス API の全体的な待機時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  サービス API 結果数の合計  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  CacheUtilization  |  キャッシュ使用率  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  連続エクスポートの最大遅延分数  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  連続エクスポート - エクスポートされたレコードの数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  ContinuousExportPendingCount  |  保留中の連続エクスポートの数  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  ContinuousExportResult  |  連続エクスポートの結果  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  (Event/IoT Hubs の) 処理されたイベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  ExportUtilization  |  エクスポート使用率  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  インジェストの待機時間 (秒単位)  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  インジェストの結果  |  Count  |  Count | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  IngestionUtilization  |  インジェストの使用率  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  インジェストの量 (MB 単位)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  キープ アライブ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  クエリ実行時間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  ストリーミング インジェストの要求率  |  Count  |  RateRequestsPerSecond | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  ストリーミング インジェストのデータ速度  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  ストリーミング インジェストの期間  |  ミリ秒  |  Average | 
| **はい**  | **はい** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  ストリーミング インジェストの結果  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  アクションの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  完了したアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  失敗したアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  スキップされたアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  開始したアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  成功したアクション   |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  成功したアクションの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  アクション スロットル イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  統合サービス環境のコネクタ メモリ使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  統合サービス環境のコネクタ プロセッサ使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  統合サービス環境のワークフロー メモリ使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  統合サービス環境のワークフロー プロセッサ使用率  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  実行エラーの割合  |  Percent  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  実行の待機時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  取り消された実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  完了した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  失敗した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  開始した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  成功した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  実行開始スロットル イベント数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  成功した実行の待機時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  実行スロットル イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  起動されたトリガーの待機時間   |  Seconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  トリガーの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  完了したトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  失敗したトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  起動されたトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  スキップされたトリガー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  開始したトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  成功したトリガー   |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  成功したトリガーの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  トリガー スロットル イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  ActionLatency  |  アクションの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  完了したアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  ActionsFailed  |  失敗したアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  スキップされたアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  ActionsStarted  |  開始したアクション   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  成功したアクション   |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  成功したアクションの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  アクション スロットル イベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  課金対象のアクションの実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  課金対象のトリガーの実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  ネイティブ操作実行の利用状況に応じた課金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  ネイティブ操作実行の利用状況に応じた課金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Standard コネクタ実行の利用状況に応じた課金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Standard コネクタ実行の利用状況に応じた課金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  ストレージ使用実行の利用状況に応じた課金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  ストレージ使用実行の利用状況に応じた課金  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  実行エラーの割合  |  Percent  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  RunLatency  |  実行の待機時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunsCancelled  |  取り消された実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunsCompleted  |  完了した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunsFailed  |  失敗した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunsStarted  |  開始した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  成功した実行  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  実行開始スロットル イベント数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  成功した実行の待機時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  実行スロットル イベント  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  課金対象の実行の合計数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  起動されたトリガーの待機時間   |  Seconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  TriggerLatency  |  トリガーの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  完了したトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggersFailed  |  失敗したトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggersFired  |  起動されたトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  スキップされたトリガー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggersStarted  |  開始したトリガー   |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  成功したトリガー   |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  成功したトリガーの待機時間   |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  トリガー スロットル イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  アクティブなコア  |  アクティブなコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  アクティブなノード  |  アクティブなノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  完了した実行数  |  完了した実行数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  失敗した実行  |  失敗した実行  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  アイドル状態のコア  |  アイドル状態のコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  アイドル状態のノード  |  アイドル状態のノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  終了中のコア  |  終了中のコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  終了中のノード  |  終了中のノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  モデル デプロイ失敗  |  モデル デプロイ失敗  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  モデル デプロイ開始  |  モデル デプロイ開始  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  モデル デプロイ成功  |  モデル デプロイが成功しました  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  モデル登録失敗  |  モデル登録失敗  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  モデル登録成功  |  モデル登録成功  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  割り込まれたコア  |  割り込まれたコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  割り込まれたノード  |  割り込まれたノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  クォータ使用率  |  クォータ使用率  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  開始した実行数  |  開始した実行数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  コア総数  |  コアの合計  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  ノード総数  |  ノード総数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  使用できないコア  |  使用できないコア  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.MachineLearningServices/workspaces  |  使用できないノード  |  使用できないノード  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Maps/accounts  |  可用性  |  可用性  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Maps/accounts  |  使用法  |  使用法  |  Count  |  Count | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  AssetCount  |  アセット数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  AssetQuota  |  アセットのクォータ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  アセットのクォータの使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  コンテンツ キー ポリシー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  コンテンツ キー ポリシーのクォータ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  コンテンツ キー ポリシーのクォータの使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  ストリーミング ポリシー数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  ストリーミング ポリシーのクォータ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  ストリーミング ポリシーのクォータの使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices/streamingEndpoints  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices/streamingEndpoints  |  Requests  |  Requests  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  成功したエンド ツー エンドの待機時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  GC の一時停止回数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  GC の一時停止合計時間  |  ミリ秒  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  使用可能な古い世代データの最大サイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  古い世代のデータのサイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  古い世代データのサイズに昇格  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  サービス CPU 使用率 (%)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  割り当て済みサービス メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  最大のサービス メモリ  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  使用済みサービス メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  システム CPU 使用率 (%)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Tomcat グローバル エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat 合計受信バイト  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat 要求最大時間  |  ミリ秒  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Tomcat 要求合計数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tomcat 要求合計時間  |  ミリ秒  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tomcat 要求平均時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Tomcat 合計送信バイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat セッション アライブ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat セッション最大アクティブ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat セッション最大アライブ時間  |  ミリ秒  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Tomcat セッション作成数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Tomcat セッション期限切れ数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Tomcat セッション拒否数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  若い世代データのサイズに昇格  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  ボリューム プールの使用済みの割り当て  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  ボリューム プールの論理サイズの合計  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  読み取りの平均待機時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  書き込みの平均待機時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  読み取り IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  ボリュームの論理サイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  ボリューム スナップショットのサイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  書き込み IOPS  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Application Gateway の合計時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  正常なホストごとの 1 分あたりの要求  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  バックエンド接続時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  バックエンド先頭バイト応答時間  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  バックエンド最終バイト応答時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  バックエンド応答の状態  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Web アプリケーション ファイアウォールのブロックされた要求の規則配布  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Web アプリケーション ファイアウォールのブロックされた要求数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  BytesReceived  |  受信バイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  BytesSent  |  送信バイト数  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  現在の容量ユニット  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  ClientRtt  |  クライアント RTT  |  MilliSeconds  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  現在のコンピューティング ユニット  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  現在の接続数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  FailedRequests  |  失敗した要求  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  正常なホストの数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Web アプリケーション ファイアウォールの合計規則配布  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  応答の状態  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Network/applicationGateways  |  スループット  |  スループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  クライアント TLS プロトコル  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  TotalRequests  |  要求の合計数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  異常なホストの数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  アプリケーション規則のヒット数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/azurefirewalls  |  DataProcessed  |  データ処理量  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  ファイアウォールの正常性状態  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  ネットワーク規則のヒット数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  SNAT ポート使用率  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/connections  |  BitsInPerSecond (受信ビット数/秒)  |  BitsInPerSecond (受信ビット数/秒)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/connections  |  BitsOutPerSecond (送信ビット数/秒)  |  BitsOutPerSecond (送信ビット数/秒)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/dnszones  |  QueryVolume  |  クエリ数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  レコード セットの容量使用率  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/dnszones  |  RecordSetCount  |  レコード セット数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  ARP の可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  BGP の可用性  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond (受信ビット数/秒)  |  BitsInPerSecond (受信ビット数/秒)  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond (送信ビット数/秒)  |  BitsOutPerSecond (送信ビット数/秒)  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond (受信ビット数/秒)  |  BitsInPerSecond (受信ビット数/秒)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond (送信ビット数/秒)  |  BitsOutPerSecond (送信ビット数/秒)  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond (受信ビット数/秒)  |  CountPerSecond  |  Average | 
| いいえ  | いいえ |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond (送信ビット数/秒)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRoutePorts  |  AdminState  |  AdminState  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond (受信ビット数/秒)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond (送信ビット数/秒)  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  バックエンドの正常性の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  バックエンド要求数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  バックエンド要求の待機時間  |  MilliSeconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  課金対象の応答サイズ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  RequestCount  |  要求数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  RequestSize  |  要求サイズ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  ResponseSize  |  応答サイズ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  TotalLatency  |  合計待機時間  |  MilliSeconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Web アプリケーション ファイアウォール要求の数  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  割り当てられた SNAT ポート (プレビュー)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/loadBalancers  |  ByteCount  |  Byte Count (バイト数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/loadBalancers  |  DipAvailability  |  正常性プローブの状態  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/loadBalancers  |  PacketCount  |  Packet Count (パケット数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  SNAT Connection Count (SNAT 接続数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/loadBalancers  |  SYNCount  |  SYN Count (SYN 数)  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  使用された SNAT ポート (プレビュー)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/loadBalancers  |  VipAvailability  |  データ パスの可用性  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  受信バイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  送信バイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  受信したパケット数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  送信されたパケット数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Avg.ラウンド トリップ時間 (ミリ秒)  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  チェック失敗の割合 (プレビュー)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  失敗したプローブの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  ラウンド トリップ時間 (ミリ秒) (プレビュー)  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Byte Count (バイト数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  DDoS 受信バイト破棄数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  DDoS 受信バイト転送数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  DDoS 受信バイト数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  DDoS 軽減をトリガーする受信 SYN パケット数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  DDoS 軽減をトリガーする受信 TCP パケット数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  DDoS 軽減をトリガーする受信 UDP パケット数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  DDoS 攻撃中かどうか  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Packet Count (パケット数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  DDoS 受信パケット破棄数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  DDoS 受信パケット転送数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  DDoS 受信パケット数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  SynCount  |  SYN Count (SYN 数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  DDoS 受信 TCP バイト破棄数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  DDoS 受信 TCP バイト転送数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  DDoS 受信 TCP バイト数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  DDoS 受信 TCP パケット破棄数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  DDoS 受信 TCP パケット転送数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  DDoS 受信 TCP パケット数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  DDoS 受信 UDP バイト破棄数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  DDoS 受信 UDP バイト転送数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  DDoS 受信 UDP バイト数  |  BytesPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  DDoS 受信 UDP パケット破棄数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  DDoS 受信 UDP パケット数  |  CountPerSecond  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  データ パスの可用性  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Endpoint Status by Endpoint (エンドポイント別のエンドポイント状態)  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Queries by Endpoint Returned (エンドポイント別の返されたクエリ数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  ゲートウェイの S2S 帯域幅  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  ゲートウェイの P2S 帯域幅  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S 接続数  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Tunnel Bandwidth (トンネル帯域幅)  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Tunnel Egress Bytes (トンネル エグレス バイト数)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Tunnel Egress Packets (トンネル エグレス パケット数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Tunnel Ingress Bytes (トンネル イングレス バイト数)  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Tunnel Ingress Packets (トンネル イングレス パケット数)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  VM への Ping のラウンド トリップ時間  |  MilliSeconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  VM への Ping の失敗  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  受信メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  失敗したすべての受信要求  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  すべての受信要求  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  予定されていたプッシュ通知の送信  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  予定されていたプッシュ通知が取り消されました  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  インストール管理操作  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  インストール操作の削除  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  インストール操作の取得  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  インストール操作をパッチします  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  インストール操作を作成または更新します  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  All Outgoing Notifications (すべての送信通知)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  適切または期限切れのチャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  チャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  ペイロード エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  外部通知システム エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  正常な通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  APNS 不良チャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  APNS 期限切れチャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  APNS 認証エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  APNS 無効通知サイズ エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  APNS エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  APNS 正常通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  GCM 認証エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM 不良チャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  GCM 期限切れチャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM 承認エラー (無効な資格情報)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  GCM 無効通知形式  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  GCM 無効通知サイズ エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  GCM エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  GCM 正常通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  GCM 調整済み通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  GCM 誤チャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  MPNS 認証エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  MPNS 不良チャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  MPNS チャネル切断  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS ドロップ通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  MPNS 無効な資格情報  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  MPNS 無効通知形式  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  MPNS エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS 正常通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  MPNS 調整済み通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  WNS 認証エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS 不良チャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  WNS チャネル切断  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS 調整済みチャネル  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS ドロップ通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS 期限切れチャネル エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  WNS 承認エラー (無効な資格情報)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  WNS 無効通知形式  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  WNS 無効通知サイズ エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  WNS 承認エラー (無効なトークン)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  WNS エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  WNS 正常通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  WNS 調整済み通知  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  WNS 承認エラー (到達不可)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS 承認エラー (間違ったトークン)  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  登録操作  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  登録作成操作  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  登録削除操作  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  登録読み取り操作  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  登録更新操作  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  予定されていた通知の保留  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  % Available Memory  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  % Available Swap Space  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  % Committed Bytes In Use  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  % DPC Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  % Free Inodes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % Free Space  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % Free Space  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  % Idle Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  % Interrupt Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  % IO Wait Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  % Nice Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  % Privileged Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Processor Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Processor Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  % Used Inodes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  % Used Memory  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  % Used Space  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  % Used Swap Space  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  % User Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Available MBytes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Available MBytes Memory  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Available MBytes Swap  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Read  |  Avg.Disk sec/Read  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Read  |  Avg.Disk sec/Read  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Transfer  |  Avg.Disk sec/Transfer  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Write  |  Avg.Disk sec/Write  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Write  |  Avg.Disk sec/Write  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Bytes Received/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Bytes Sent/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Bytes Total/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Current Disk Queue Length  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Disk Read Bytes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Disk Reads/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Disk Reads/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Disk Transfers/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Disk Transfers/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |  Disk Write Bytes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Disk Writes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Disk Writes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Free Megabytes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Free Megabytes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Free Physical Memory  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Free Space in Paging Files  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Free Virtual Memory  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Logical Disk Bytes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Page Reads/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Page Writes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Pages/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Pct Privileged Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Pct User Time  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Physical Disk Bytes/sec  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  処理  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  プロセッサ キューの長さ  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Size Stored In Paging Files  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Total Bytes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Total Bytes Received  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Total Bytes Transmitted  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Total Collisions  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Total Packets Received  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Total Packets Transmitted  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Total Rx Errors  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Total Tx Errors  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Uptime  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Used MBytes Swap Space  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  Used Memory kBytes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Used Memory MBytes  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  ユーザー  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Virtual Shared Memory  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  Event  |  Event  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.OperationalInsights/workspaces  |  Heartbeat  |  Heartbeat  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.OperationalInsights/workspaces  |  更新  |  更新  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  メモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  メモリ スラッシング (データセット)  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  QPU High Utilization (QPU の高使用率)  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  クエリ実行時間 (データセット)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  クエリ プール ジョブ キューの長さ (データセット)  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Search/searchServices  |  SearchLatency  |  検索の待機時間  |  Seconds  |  Average | 
| **はい**  | いいえ |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  1 秒あたりの検索クエリ数  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  スロットルされた検索クエリの割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  キュー/トピック内のアクティブなメッセージの数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  切断された接続数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  開かれている接続数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU (非推奨)  |  Percent  |  最大値 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  キュー/トピック内の配信不能メッセージの数。  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  受信メッセージ  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  受信要求  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  メッセージ  |  キュー/トピック内のメッセージの数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  CPU  |  Percent  |  最大値 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  メモリ使用量  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  送信メッセージ  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  キュー/トピック内のスケジュール済みメッセージの数。  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  サーバー エラー。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  サイズ  |  サイズ  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  成功した要求  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  スロットルされた要求数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  ユーザー エラー数。  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  メモリ使用量 (非推奨)  |  Percent  |  最大値 | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  ContainerStatus  |  ContainerStatus  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.ServiceFabricMesh/applications  |  ServiceStatus  |  ServiceStatus  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Connection Count (接続数)  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  受信トラフィック  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  メッセージ数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  送信トラフィック  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  System Errors (システム エラー)  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  User Errors (ユーザー エラー)  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  平均 CPU の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  読み取り IO バイト  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  書き込み IO バイト  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/managedInstances  |  io_requests  |  IO 要求数  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  予約済みストレージ スペース  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  使用済みストレージ スペース  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  仮想コア数  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU の割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers  |  database_storage_used  |  使用済みのデータ領域  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers  |  dtu_used  |  使用された DTU  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers  |  storage_used  |  使用済みのデータ領域  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  割り当て済みのデータ領域  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  課金されるアプリ CPU  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  アプリ CPU の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  アプリ メモリの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  ファイアウォールによってブロックされる  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  キャッシュ ヒットの割合  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  使用されたキャッシュの割合  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  connection_failed  |  失敗した接続  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  connection_successful  |  成功した接続  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU 制限  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  cpu_used  |  使用された CPU  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  deadlock  |  デッドロック  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU の上限  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  dtu_used  |  使用された DTU  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU の割合  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  dwu_limit  |  DWU 上限  |  Count  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  dwu_used  |  使用済み DWU  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  ローカル tempdb の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  ログ IO の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  メモリの割合  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  データ IO の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  セッションの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  storage  |  使用済みのデータ領域  |  バイト  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  storage_percent  |  使用済みのデータ領域の割合  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Tempdb データ ファイル サイズ (KB)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tempdb ログ ファイル サイズ (KB)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Tempdb ログ使用率  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  workers_percent  |  ワーカーの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  インメモリ OLTP ストレージの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  割り当て済みのデータ領域  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  割り当て済みのデータ領域の割合  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  CPU 制限  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  CPU の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  使用された CPU  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  割り当て済みのデータ領域  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  CPU 制限  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  CPU の割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  使用された CPU  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU の割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  使用済み eDTU  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  ログ IO の割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  データ IO の割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  セッションの割合  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  使用済みのデータ領域  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  ワーカーの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU 制限  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  使用済み eDTU  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  ログ IO の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  データ IO の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  セッションの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  データの最大サイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  使用済みのデータ領域の割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  使用済みのデータ領域  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Tempdb データ ファイル サイズ (KB)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tempdb ログ ファイル サイズ (KB)  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Tempdb ログ使用率  |  Percent  |  最大値 | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  ワーカーの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  インメモリ OLTP ストレージの割合  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Used capacity (使用済み容量)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  可用性  |  可用性  |  Percent  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Blob Capacity (BLOB 容量)  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Blob Count (BLOB 数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Blob Container Count (BLOB コンテナー数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  インデックスの容量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/blobServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  File Capacity (ファイル容量)  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  File Count (ファイル数)  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  File Share Count (ファイル共有数)  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  ファイル共有のクォータ サイズ  |  バイト  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  ファイル共有のスナップショット数  |  Count  |  Average | 
| いいえ  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  ファイル共有のスナップショットのサイズ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/fileServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Queue Capacity (キュー容量)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Queue Count (キュー数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Queue Message Count (キュー メッセージ数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/queueServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  可用性  |  可用性  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  エグレス  |  エグレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  イングレス  |  イングレス  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Success E2E Latency (成功 E2E 待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Success Server Latency (成功サーバー待機時間)  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Table Capacity (テーブル容量)  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Table Count (テーブル数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Table Entity Count (テーブル エンティティ数)  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.Storage/storageAccounts/tableServices  |  トランザクション  |  トランザクション  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientIOPS  |  クライアント IOPS の合計  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientLatency  |  クライアントの平均待機時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  クライアント ロック IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS  |  クライアント メタデータの読み取り IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  クライアント メタデータの書き込み IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  クライアントの読み取り IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  キャッシュ読み取りの平均スループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  クライアントの書き込み IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  キャッシュ書き込みの平均スループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  StorageTarget の非同期書き込みスループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  StorageTarget のフィル スループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  ストレージ ターゲットの正常性  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  StorageTarget IOPS の合計  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetLatency  |  StorageTarget の待機時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  StorageTarget のメタデータ読み取り IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  StorageTarget のメタデータ書き込み IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  StorageTarget の先読みスループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetReadIOPS  |  StorageTarget の読み取り IOPS  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  StorageTarget の同期書き込みスループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  StorageTarget の合計読み取りスループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  StorageTarget の合計書き込みスループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  StorageTarget の書き込み IOPS  |  Count  |  Average | 
| **はい**  | いいえ |  Microsoft.StorageCache/caches  |  Uptime  |  Uptime  |  Count  |  Average | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  同期セッションの結果  |  Count  |  Average | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  同期したバイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  アプリケーションによるクラウドを使った階層化の呼び戻しサイズ  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  クラウドを使った階層化の呼び戻しサイズ  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  クラウドを使った階層化の呼び戻し  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  クラウドを使った階層化の呼び戻しスループット  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  サーバーのオンライン状態  |  Count  |  最大値 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  同期されたファイル数  |  Count  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  ファイルが同期していない  |  Count  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  サーバーのオンライン状態  |  Count  |  最大値 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  クラウドを使った階層化の呼び戻し  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  同期したバイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  同期されたファイル数  |  Count  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  ファイルが同期していない  |  Count  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  同期したバイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  同期されたファイル数  |  Count  |  合計 | 
| **はい**  | いいえ |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  ファイルが同期していない  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  失敗した関数の要求  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  関数のイベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  関数の要求  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  データ変換エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  逆シリアル化の入力エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  順不同のイベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  初期入力イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  エラー  |  実行時エラー  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  入力イベントのバイト数  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  入力イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  バックログされた入力イベント  |  Count  |  最大値 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  受信した入力ソース  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  遅延入力イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  出力イベント  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  透かしの遅延  |  Seconds  |  最大値 | 
| **はい**  | いいえ |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  SU % 使用率  |  Percent  |  最大値 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Disk Read Bytes/Sec  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  ディスク読み取り待機時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  ディスク読み取り操作数  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Disk Write Bytes/Sec  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  ディスク書き込み待機時間  |  ミリ秒  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Disk Write Operations  |  Count  |  合計 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  アクティブなメモリ  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  許可されているメモリ量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  メモリ使用量  |  バイト  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Network In  |  Network In  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Network Out  |  Network Out  |  バイト  |  合計 | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  受信ネットワークのバイト数/秒  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  送信ネットワークのバイト数/秒  |  BytesPerSecond  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percent  |  Average | 
| **はい**  | いいえ |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  CPU Ready の割合  |  ミリ秒  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  アクティブな要求数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  平均応答時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  受信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  送信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  CPU の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  ディスク キューの長さ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  HTTP 2xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  HTTP 3xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  HTTP 406  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  HTTP 4xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  HTTP サーバー エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  HTTP キューの長さ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  L App Service プランの worker の数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  M App Service プランの worker の数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Requests  |  Requests  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  S App Service プランの worker の数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  フロント エンドの合計  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  CPU の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  利用可能な worker の数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  worker の合計数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  使用済みの worker の数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  BytesReceived  |  受信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  BytesSent  |  送信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  CPU の割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  ディスク キューの長さ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  HTTP キューの長さ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  メモリの割合  |  Percent  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP 終了を待機中  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpClosing  |  TCP 終了  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP 確立済み  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP FIN の待機 1  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP FIN の待機 2  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP 最後の ACK  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP SYN 受信済み  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP SYN 送信済み  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  TCP 待機中  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  AppConnections  |  接続  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  平均メモリ ワーキング セット  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  AverageResponseTime  |  平均応答時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  BytesReceived  |  受信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  BytesSent  |  送信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  CpuTime  |  CPU 時間  |  Seconds  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  CurrentAssemblies  |  現在のアセンブリ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  関数の実行回数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  関数の実行単位  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Gen0Collections  |  Gen 0 ガベージ コレクション  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Gen1Collections  |  Gen 1 ガベージ コレクション  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Gen2Collections  |  Gen 2 ガベージ コレクション  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  ハンドル  |  ハンドルの数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  HealthCheckStatus  |  正常性チェックの状態  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http2xx  |  HTTP 2xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http3xx  |  HTTP 3xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http401  |  HTTP 401  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http403  |  HTTP 403  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http404  |  HTTP 404  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http406  |  HTTP 406  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http4xx  |  HTTP 4xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Http5xx  |  HTTP サーバー エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  HttpResponseTime  |  応答時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  IO その他のバイト/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  IO その他の操作/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  IO 読み取りバイト/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  IO 読み取り操作/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  IO 書き込みバイト/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  IO 書き込み操作/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  メモリ ワーキング セット  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  PrivateBytes  |  Private Bytes  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Requests  |  Requests  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  アプリケーション キュー内の要求数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  Threads  |  Thread Count  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  TotalAppDomains  |  アプリ ドメイン合計数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  アンロード済みのアプリ ドメイン合計数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  AppConnections  |  接続  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  平均メモリ ワーキング セット  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  平均応答時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  BytesReceived  |  受信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  BytesSent  |  送信データ  |  バイト  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  CpuTime  |  CPU 時間  |  Seconds  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  現在のアセンブリ  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  関数の実行回数  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  関数の実行単位  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Gen 0 ガベージ コレクション  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Gen 1 ガベージ コレクション  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Gen 2 ガベージ コレクション  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  ハンドル  |  ハンドルの数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  正常性チェックの状態  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http2xx  |  HTTP 2xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http3xx  |  HTTP 3xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http401  |  HTTP 401  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http403  |  HTTP 403  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http404  |  HTTP 404  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http406  |  HTTP 406  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http4xx  |  HTTP 4xx  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Http5xx  |  HTTP サーバー エラー  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  応答時間  |  Seconds  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  IO その他のバイト/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  IO その他の操作/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  IO 読み取りバイト/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  IO 読み取り操作/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  IO 書き込みバイト/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  IO 書き込み操作/秒  |  BytesPerSecond  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  メモリ ワーキング セット  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Private Bytes  |  バイト  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Requests  |  Requests  |  Count  |  合計 | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  アプリケーション キュー内の要求数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  Threads  |  Thread Count  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  アプリ ドメイン合計数  |  Count  |  Average | 
| **はい**  | **はい** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  アンロード済みのアプリ ドメイン合計数  |  Count  |  Average | 
