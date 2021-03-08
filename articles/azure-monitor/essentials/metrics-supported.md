---
title: Azure Monitor でサポートされているメトリック (リソースの種類別)
description: Azure Monitor で使用できるメトリックの一覧 (リソースの種類別) です。
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 02/06/2021
ms.author: robb
ms.openlocfilehash: 2437ab80a23ffc39c180bcdf72921fdf13768541
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033505"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor のサポートされるメトリック

> [!NOTE]
> この一覧は、主に自動生成されています。 GitHub を介してこの一覧に加えられた変更は、警告なしで上書きできます。 永続的な更新を行う方法の詳細については、この記事の作成者にお問い合わせください。

Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 

この記事は、Azure Monitor の統合メトリック パイプラインで現在利用できるすべてのプラットフォーム メトリック (つまり、自動的に集められた) の完全一覧になっています。 この記事の上部に記載された日付以降に変更または追加されたメトリックは、まだ下記に記載されていない可能性があります。 プログラムを使ってこのメトリックの一覧のクエリを実行し、アクセスするには、[2018-01-01 API バージョン](/rest/api/monitor/metricdefinitions)を使用してください。 この一覧にない他のメトリックは、ポータルまたは従来の API で使用できる場合があります。

メトリックは、リソース プロバイダーとリソースの種類別にまとめられます。 サービスとそれらに属するリソース プロバイダーおよび種類の一覧については、「[Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)」を参照してください。  

## <a name="exporting-platform-metrics-to-other-locations"></a>他の場所へのプラットフォーム メトリックのエクスポート

次の 2 つの方法のいずれかで、プラットフォーム メトリックを Azure Monitor パイプラインから他の場所にエクスポートできます。
1. [メトリック REST API](/rest/api/monitor/metrics/list) を使用します。
2. [診断設定](../essentials/diagnostic-settings.md)を使用して、プラットフォーム メトリックをルーティングします 
    - Azure Storage
    - Azure Monitor ログ (および Log Analytics)
    - イベント ハブ (対象が Microsoft 以外のシステムの場合) 

メトリックをルーティングする最も簡単な方法は診断設定を使用することですが、これには、次のような制限がいくつかあります。 

- **エクスポートできないものがある** - すべてのメトリックは、REST API を使用してエクスポートできますが、Azure Monitor バックエンドの複雑さにより、診断設定を使用してエクスポートできないものがあります。 以下の表の "*診断設定を使用したエクスポートが可能か*" の列には、この方法でエクスポートできるメトリックが一覧表示されています。  

- **多次元メトリック** - 診断設定を使用して、他の場所に多次元メトリックを送信することは現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。 *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。

## <a name="guest-os-and-host-os-metrics"></a>ゲスト OS とホスト OS のメトリック

> [!WARNING]
> Azure Virtual Machines、Service Fabric、Cloud Services で実行されるゲスト オペレーティング システム (ゲスト OS) のメトリックは、ここには一覧表示されて **いません**。 ゲスト OS メトリックは、ゲスト オペレーティング システムで実行される、あるいはそのシステムの一部として実行される 1 つまたは複数のエージェントを使用して収集する必要があります。  ゲスト OS メトリックには、ゲストの CPU 使用率またはメモリ使用量を追跡するパフォーマンス カウンターが含まれています。これらは、どちらも自動スケーリングまたはアラートに頻繁に使用されます。 
>
> **ホスト OS メトリックは利用可能であり、以下に一覧表示されています。** これらは同じではありません。 ホスト OS メトリックは、ゲスト OS セッションをホストしている Hyper-V セッションに関連しています。 

> [!TIP]
> ベスト プラクティスは、[Azure Diagnostics の拡張機能](../agents/diagnostics-extension-overview.md)を使用および構成し、プラットフォーム メトリックが格納されているのと同じ Azure Monitor メトリック データベースにゲスト OS パフォーマンス メトリックを送信することです。 この拡張機能により、[カスタム メトリック](../essentials/metrics-custom-overview.md) API を介してゲスト OS メトリックがルーティングされます。 その後、ゲスト OS メトリックをプラットフォーム メトリックのようにグラフにしたり、アラートを出す対象にしたり、それ以外の場面で利用したりすることができます。 代わりに、あるいはさらに、Log Analytics エージェントを使用して、Azure Monitor ログや Log Analytics にゲスト OS メトリックを送信することもできます。 そこで、非メトリック データと組み合わせて、これらのメトリックに対してクエリを実行できます。 

重要な追加情報については、[監視エージェントの概要](../agents/agents-overview.md)に関するページを参照してください。

## <a name="table-formatting"></a>表の書式設定

> [!IMPORTANT] 
> この最新の更新では、新しい列が追加され、メトリックがアルファベット順に並べ替えられています。 追加情報とは、ブラウザー ウィンドウの幅に応じて、以下の表の下部に水平スクロール バーが表示される場合があることを意味します。 情報が欠落していると思われる場合は、スクロール バーを使用して表の全体が表示されるようにしてください。

## <a name="microsoftaadiamazureadmetrics"></a>microsoft.aadiam/azureADMetrics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ThrottledRequests|いいえ|ThrottledRequests|Count|Average|azureADMetrics 型メトリック|ディメンションなし|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|はい|メモリ:クリーナーの現在の価格|Count|Average|現在のメモリ価格 ($/バイト/時刻)。標準は 1,000 です。|ServerResourceType|
|CleanerMemoryNonshrinkable|はい|メモリ:クリーナーの圧縮不可能なメモリ|バイト|Average|バックグラウンド クリーナーによる削除の対象とならないメモリの量 (バイト単位)。|ServerResourceType|
|CleanerMemoryShrinkable|はい|メモリ:クリーナーの圧縮可能なメモリ|バイト|Average|バックグラウンド クリーナーによる削除の対象となるメモリの量 (バイト単位)。|ServerResourceType|
|CommandPoolBusyThreads|はい|スレッド:コマンド プールのビジー状態のスレッド|Count|Average|コマンド スレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|CommandPoolIdleThreads|はい|スレッド:コマンド プールのアイドル状態のスレッド|Count|Average|コマンド スレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|CommandPoolJobQueueLength|はい|コマンド プールのジョブ キューの長さ|Count|Average|コマンド スレッド プールのキュー内のジョブの数。|ServerResourceType|
|CurrentConnections|はい|接続:現在の接続数|Count|Average|現在確立されているクライアント接続の数。|ServerResourceType|
|CurrentUserSessions|はい|現在のユーザー セッション|Count|Average|確立された現在のユーザー セッションの数。|ServerResourceType|
|LongParsingBusyThreads|はい|スレッド:長時間解析を行っているビジー状態のスレッド|Count|Average|長時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|LongParsingIdleThreads|はい|スレッド:長時間解析を行っているアイドル状態のスレッド|Count|Average|長時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingJobQueueLength|はい|スレッド:長時間解析を行っているジョブのキューの長さ|Count|Average|長時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|mashup_engine_memory_metric|はい|M エンジン メモリ|バイト|Average|マッシュアップ エンジン プロセスによるメモリ使用率|ServerResourceType|
|mashup_engine_private_bytes_metric|はい|M エンジン プライベート バイト|バイト|Average|マッシュアップ エンジンのプロセスによるプライベート バイト使用量です。|ServerResourceType|
|mashup_engine_qpu_metric|はい|M エンジン QPU|Count|Average|マッシュアップ エンジン プロセスによる QPU 使用率|ServerResourceType|
|mashup_engine_virtual_bytes_metric|はい|M エンジン仮想バイト|バイト|Average|マッシュアップ エンジンのプロセスによる仮想バイト使用量です。|ServerResourceType|
|memory_metric|はい|メモリ|バイト|Average|メモリ。 範囲は S1 で 0 ～ 25 GB、S2 で 0 ～ 50 GB、S4 で 0 ～ 100 GB|ServerResourceType|
|memory_thrashing_metric|はい|メモリ スラッシング|Percent|Average|平均的なメモリ スラッシング。|ServerResourceType|
|MemoryLimitHard|はい|メモリ:メモリの制限 - ハード|バイト|Average|構成ファイルに指定されているハードのメモリの制限。|ServerResourceType|
|MemoryLimitHigh|はい|メモリ:メモリの制限 - 高|バイト|Average|構成ファイルに指定されているメモリの上限。|ServerResourceType|
|MemoryLimitLow|はい|メモリ:メモリの制限 - 低|バイト|Average|構成ファイルに指定されているメモリの下限。|ServerResourceType|
|MemoryLimitVertiPaq|はい|メモリ:メモリの制限 - VertiPaq|バイト|Average|構成ファイルに指定されているインメモリの制限。|ServerResourceType|
|MemoryUsage|はい|メモリ:メモリ使用量|バイト|Average|クリーナー メモリの価格の計算で使用されるサーバー プロセスのメモリ使用量。 Process\PrivateBytes カウンターの値に、メモリがマップされたデータのサイズを加えた値と等しくなります。xVelocity インメモリ分析エンジン (VertiPaq) によって xVelocity エンジンのメモリの制限を超えてマップされた (割り当てられた) メモリは無視されます。|ServerResourceType|
|private_bytes_metric|はい|Private Bytes|バイト|Average|プライベート バイト。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|はい|スレッド:処理中のプールのビジー状態の I/O ジョブのスレッド|Count|Average|処理中のスレッド プール内で I/O ジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|はい|スレッド:処理中のプールのビジー状態の I/O 以外のスレッド|Count|Average|処理中のスレッド プール内で I/O 以外のジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|はい|スレッド:処理中のプールのアイドル状態の I/O ジョブのスレッド|Count|Average|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|はい|スレッド:処理中のプールのアイドル状態の I/O 以外のスレッド|Count|Average|I/O 以外のジョブ専用の、処理中のスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|はい|スレッド:処理中のプール I/O ジョブ キューの長さ|Count|Average|処理中のスレッド プールのキューに登録されている I/O ジョブの数。|ServerResourceType|
|ProcessingPoolJobQueueLength|はい|処理プール ジョブ キューの長さ|Count|Average|処理スレッド プールのキュー内の非 I/O ジョブの数。|ServerResourceType|
|qpu_metric|はい|QPU|Count|Average|QPU. 範囲は S1 で 0 ～ 100、S2 で 0 ～ 200、S4 で 0 ～ 400|ServerResourceType|
|QueryPoolBusyThreads|はい|クエリ プール ビジー スレッド|Count|Average|クエリ スレッド プールのビジー スレッドの数。|ServerResourceType|
|QueryPoolIdleThreads|はい|スレッド:クエリ プールのアイドル状態のスレッド|Count|Average|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolJobQueueLength|はい|スレッド:クエリ プール ジョブ キューの長さ|Count|Average|クエリ スレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|Quota|はい|メモリ:Quota|バイト|Average|現在のメモリ クォータ (バイト単位)。 メモリ クォータはメモリ付与またはメモリ予約とも呼ばれます。|ServerResourceType|
|QuotaBlocked|はい|メモリ:ブロックされているクォータ|Count|Average|他のメモリ クォータが解放されるまでブロックされている現在のクォータ要求の数。|ServerResourceType|
|RowsConvertedPerSec|はい|処理:1 秒あたりの変換行数|CountPerSecond|Average|処理中に変換された行の比率。|ServerResourceType|
|RowsReadPerSec|はい|処理:1 秒あたりの読み取り行数|CountPerSecond|Average|すべてのリレーショナル データベースから読み取った行の比率。|ServerResourceType|
|RowsWrittenPerSec|はい|処理:1 秒あたりの書き込み行数|CountPerSecond|Average|処理中に書き込まれた行の比率。|ServerResourceType|
|ShortParsingBusyThreads|はい|スレッド:短時間解析を行っているビジー状態のスレッド|Count|Average|短時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|ShortParsingIdleThreads|はい|スレッド:短時間解析を行っているアイドル状態のスレッド|Count|Average|短時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|ShortParsingJobQueueLength|はい|スレッド:短時間解析を行っているジョブ キューの長さ|Count|Average|短時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|SuccessfullConnectionsPerSec|はい|成功した接続数 (秒単位)|CountPerSecond|Average|接続が正常に完了した割合。|ServerResourceType|
|TotalConnectionFailures|はい|合計接続失敗数|Count|Average|失敗した接続試行数の合計。|ServerResourceType|
|TotalConnectionRequests|はい|合計接続要求数|Count|Average|合計接続要求数。 これらは到着した接続要求です。|ServerResourceType|
|VertiPaqNonpaged|はい|メモリ:VertiPaq 非ページ|バイト|Average|メモリ内エンジン用にワーキング セットでロックされているメモリの量 (バイト単位)。|ServerResourceType|
|VertiPaqPaged|はい|メモリ:VertiPaq ページ|バイト|Average|メモリ内エンジン用に使用されているページ メモリの量 (バイト単位)。|ServerResourceType|
|virtual_bytes_metric|はい|Virtual Bytes|バイト|Average|仮想バイト。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BackendDuration|はい|バックエンド要求の期間|ミリ秒|Average|バックエンド要求の期間 (ミリ秒)|Location、Hostname|
|容量|はい|容量|Percent|Average|ApiManagement サービスの使用状況メトリック|場所|
|Duration|はい|ゲートウェイ要求の全体の期間|ミリ秒|Average|ゲートウェイ要求の全体の期間 (ミリ秒単位)|Location、Hostname|
|EventHubDroppedEvents|はい|破棄された EventHub イベント数|Count|合計|キューのサイズ制限に達したためスキップされたイベントの数|場所|
|EventHubRejectedEvents|はい|拒否された EventHub イベント数|Count|合計|拒否された EventHub イベントの数 (誤った構成または未承認)|場所|
|EventHubSuccessfulEvents|はい|成功した EventHub イベント数|Count|合計|成功した EventHub イベントの数|場所|
|EventHubThrottledEvents|はい|調整された EventHub イベント数|Count|合計|調整された EventHub イベントの数|場所|
|EventHubTimedoutEvents|はい|タイムアウトした EventHub イベント数|Count|合計|タイムアウトした EventHub イベントの数|場所|
|EventHubTotalBytesSent|はい|EventHub イベントのサイズ|バイト|合計|EventHub イベントの合計サイズ (バイト単位)|場所|
|EventHubTotalEvents|はい|EventHub イベントの合計数|Count|合計|EventHub に送信されるイベントの数|場所|
|EventHubTotalFailedEvents|はい|失敗した EventHub イベント数|Count|合計|失敗した EventHub イベントの数|場所|
|FailedRequests|はい|失敗したゲートウェイ要求 (非推奨)|Count|合計|ゲートウェイ要求の失敗数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|NetworkConnectivity|はい|Network Connectivity Status of Resources (Preview) (リソースのネットワーク接続状態 (プレビュー))|Count|Average|API Management サービスからの依存リソースの種類のネットワーク接続状態|Location、ResourceType|
|OtherRequests|はい|その他のゲートウェイ要求 (非推奨)|Count|合計|その他のゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|Requests|はい|Requests|Count|合計|複数のディメンションを持つゲートウェイ要求メトリック|Location、Hostname、LastErrorReason、BackendResponseCode、GatewayResponseCode、BackendResponseCodeCategory、GatewayResponseCodeCategory|
|SuccessfulRequests|はい|成功したゲートウェイ要求 (非推奨)|Count|合計|成功したゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|TotalRequests|はい|ゲートウェイ要求の合計 (非推奨)|Count|合計|ゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|
|UnauthorizedRequests|はい|未承認ゲートウェイ要求 (非推奨)|Count|合計|未承認ゲートウェイ要求の数 - 代わりに、GatewayResponseCodeCategory ディメンションで多次元要求メトリックを使用します|Location、Hostname|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|はい|HttpIncomingRequestCount|Count|Count|受信 http 要求の合計数。|StatusCode、Authentication|
|HttpIncomingRequestDuration|はい|HttpIncomingRequestDuration|Count|Average|http 要求の待機時間。|StatusCode、Authentication|
|ThrottledHttpRequestCount|はい|ThrottledHttpRequestCount|Count|Count|調整された http 要求。|ディメンションなし|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active-timer-count|はい|active-timer-count|Count|Average|現在アクティブなタイマーの数|Deployment、AppName、Pod|
|alloc-rate|はい|alloc-rate|バイト|Average|マネージド ヒープで割り当てられたバイト数|Deployment、AppName、Pod|
|AppCpuUsage|はい|アプリの CPU 使用率 |Percent|Average|アプリの最近の CPU 使用率|Deployment、AppName、Pod|
|assembly-count|はい|assembly-count|Count|Average|読み込まれたアセンブリ数|Deployment、AppName、Pod|
|cpu-usage|はい|cpu-usage|Percent|Average|プロセスによって CPU が使用された時間 (%)|Deployment、AppName、Pod|
|current-requests|はい|current-requests|Count|Average|プロセスの有効期間中の処理中の要求の合計数|Deployment、AppName、Pod|
|exception-count|はい|exception-count|Count|合計|例外の数|Deployment、AppName、Pod|
|failed-requests|はい|failed-requests|Count|Average|プロセスの有効期間中の失敗した要求の合計数|Deployment、AppName、Pod|
|gc-heap-size|はい|gc-heap-size|Count|Average|GC によって報告された合計ヒープ サイズ (MB)|Deployment、AppName、Pod|
|gen-0-gc-count|はい|gen-0-gc-count|Count|Average|Gen 0 GC の数|Deployment、AppName、Pod|
|gen-0-size|はい|gen-0-size|バイト|Average|Gen 0 ヒープ サイズ|Deployment、AppName、Pod|
|gen-1-gc-count|はい|gen-1-gc-count|Count|Average|Gen 1 GC の数|Deployment、AppName、Pod|
|gen-1-size|はい|gen-1-size|バイト|Average|Gen 1 ヒープ サイズ|Deployment、AppName、Pod|
|gen-2-gc-count|はい|gen-2-gc-count|Count|Average|Gen 2 GC の数|Deployment、AppName、Pod|
|gen-2-size|はい|gen-2-size|バイト|Average|Gen 2 ヒープ サイズ|Deployment、AppName、Pod|
|jvm.gc.live.data.size|はい|jvm.gc.live.data.size|バイト|Average|完全な GC 後の古い世代のメモリ プールのサイズ|Deployment、AppName、Pod|
|jvm.gc.max.data.size|はい|jvm.gc.max.data.size|バイト|Average|古い世代のメモリ プールの最大サイズ|Deployment、AppName、Pod|
|jvm.gc.memory.allocated|はい|jvm.gc.memory.allocated|バイト|最大値|1つの GC の後から次の GC の前までの若い世代のメモリ プールのサイズ増加に合わせて増加|Deployment、AppName、Pod|
|jvm.gc.memory.promoted|はい|jvm.gc.memory.promoted|バイト|最大値|GC 前から後にかけた古い世代のメモリ プールのサイズ増加数|Deployment、AppName、Pod|
|jvm.gc.pause.total.count|はい|jvm.gc.pause.total.count|Count|合計|GC の一時停止回数|Deployment、AppName、Pod|
|jvm.gc.pause.total.time|はい|jvm.gc.pause.total.time|ミリ秒|合計|GC の一時停止合計時間|Deployment、AppName、Pod|
|jvm.memory.committed|はい|jvm.memory.committed|バイト|Average|JVM に割り当て済みのメモリ (バイト単位)|Deployment、AppName、Pod|
|jvm.memory.max|はい|jvm.memory.max|バイト|最大値|メモリ管理に使用できるメモリの最大量 (バイト単位)|Deployment、AppName、Pod|
|jvm.memory.used|はい|jvm.memory.used|バイト|Average|アプリのメモリ使用量 (バイト単位)|Deployment、AppName、Pod|
|loh-size|はい|loh-size|バイト|Average|LOH ヒープ サイズ|Deployment、AppName、Pod|
|monitor-lock-contention-count|はい|monitor-lock-contention-count|Count|Average|モニター ロックを取得しようとしたときに競合が発生した回数|Deployment、AppName、Pod|
|process.cpu.usage|はい|process.cpu.usage|Percent|Average|JVM プロセスの最近の CPU 使用率|Deployment、AppName、Pod|
|requests-per-second|はい|requests-rate|Count|Average|要求レート|Deployment、AppName、Pod|
|system.cpu.usage|はい|system.cpu.usage|Percent|Average|システム全体の最近の CPU 使用率|Deployment、AppName、Pod|
|threadpool-completed-items-count|はい|threadpool-completed-items-count|Count|Average|ThreadPool の完了した作業項目数|Deployment、AppName、Pod|
|threadpool-queue-length|はい|threadpool-queue-length|Count|Average|ThreadPool の作業項目のキューの長さ|Deployment、AppName、Pod|
|threadpool-thread-count|はい|threadpool-thread-count|Count|Average|ThreadPool スレッドの数|Deployment、AppName、Pod|
|time-in-gc|はい|time-in-gc|Percent|Average|最後の GC 以降の GC の時間 (%)|Deployment、AppName、Pod|
|tomcat.global.error|はい|tomcat.global.error|Count|合計|Tomcat グローバル エラー|Deployment、AppName、Pod|
|tomcat.global.received|はい|tomcat.global.received|バイト|合計|Tomcat 合計受信バイト|Deployment、AppName、Pod|
|tomcat.global.request.avg.time|はい|tomcat.global.request.avg.time|ミリ秒|Average|Tomcat 要求平均時間|Deployment、AppName、Pod|
|tomcat.global.request.max|はい|tomcat.global.request.max|ミリ秒|最大値|Tomcat 要求最大時間|Deployment、AppName、Pod|
|tomcat.global.request.total.count|はい|tomcat.global.request.total.count|Count|合計|Tomcat 要求合計数|Deployment、AppName、Pod|
|tomcat.global.request.total.time|はい|tomcat.global.request.total.time|ミリ秒|合計|Tomcat 要求合計時間|Deployment、AppName、Pod|
|tomcat.global.sent|はい|tomcat.global.sent|バイト|合計|Tomcat 合計送信バイト数|Deployment、AppName、Pod|
|tomcat.sessions.active.current|はい|tomcat.sessions.active.current|Count|合計|Tomcat セッションのアクティブ数|Deployment、AppName、Pod|
|tomcat.sessions.active.max|はい|tomcat.sessions.active.max|Count|合計|Tomcat セッション最大アクティブ数|Deployment、AppName、Pod|
|tomcat.sessions.alive.max|はい|tomcat.sessions.alive.max|ミリ秒|最大値|Tomcat セッション最大アライブ時間|Deployment、AppName、Pod|
|tomcat.sessions.created|はい|tomcat.sessions.created|Count|合計|Tomcat セッション作成数|Deployment、AppName、Pod|
|tomcat.sessions.expired|はい|tomcat.sessions.expired|Count|合計|Tomcat セッション期限切れ数|Deployment、AppName、Pod|
|tomcat.sessions.rejected|はい|tomcat.sessions.rejected|Count|合計|Tomcat セッション拒否数|Deployment、AppName、Pod|
|tomcat.threads.config.max|はい|tomcat.threads.config.max|Count|合計|Tomcat 構成の最大スレッド数|Deployment、AppName、Pod|
|tomcat.threads.current|はい|tomcat.threads.current|Count|合計|Tomcat 現在のスレッド数|Deployment、AppName、Pod|
|total-requests|はい|total-requests|Count|Average|プロセスの有効期間中の要求の合計数|Deployment、AppName、Pod|
|working-set|はい|working-set|Count|Average|プロセスによって使用されたワーキング セットの量 (MB)|Deployment、AppName、Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|TotalJob|はい|合計ジョブ数|Count|合計|ジョブの合計数|Runbook、Status|
|TotalUpdateDeploymentMachineRuns|はい|更新プログラムのデプロイ マシンの合計実行回数|Count|合計|ソフトウェア更新プログラムのデプロイの実行における、ソフトウェア更新プログラムのデプロイ マシンの実行回数の合計|SoftwareUpdateConfigurationName、Status、TargetComputer、SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|はい|更新プログラムのデプロイの合計実行回数|Count|合計|ソフトウェア更新プログラムのデプロイの実行回数の合計|SoftwareUpdateConfigurationName、Status|


## <a name="microsoftavsprivateclouds"></a>Microsoft.AVS/privateClouds

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CapacityLatest|はい|データストア ディスクの合計容量|バイト|Average|データストア内のディスクの合計容量|dsname|
|DiskUsedPercentage|はい| 使用済みのデータストア ディスクの割合|Percent|Average|データストアで使用済みの使用可能なディスクの割合|dsname|
|EffectiveCpuAverage|はい|Percentage CPU|Percent|Average|クラスター内の使用済みの CPU リソースの割合|clustername|
|EffectiveMemAverage|はい|平均有効メモリ|バイト|Average|クラスター内の使用可能なマシン メモリの合計容量|clustername|
|OverheadAverage|はい|平均メモリ オーバーヘッド|バイト|Average|仮想化インフラストラクチャによって消費されるホストの物理メモリ|clustername|
|TotalMbAverage|はい|平均メモリ合計|バイト|Average|クラスター内のメモリの合計|clustername|
|UsageAverage|はい|メモリの平均使用量|Percent|Average|構成されているメモリまたは使用可能なメモリの割合としてのメモリ使用量|clustername|
|UsedLatest|はい|使用済みのデータストア ディスク|バイト|Average|データストアで使用済みのディスクの合計容量|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CoreCount|いいえ|Dedicated Core Count (専用コアの数)|Count|合計|Batch アカウントの専用コアの合計数|ディメンションなし|
|CreatingNodeCount|いいえ|作成ノード数|Count|合計|作成されるノードの数|ディメンションなし|
|IdleNodeCount|いいえ|アイドル状態のノード数|Count|合計|アイドル状態のノードの数|ディメンションなし|
|JobDeleteCompleteEvent|はい|ジョブ削除の完了イベント|Count|合計|正常に削除されたジョブの合計数。|jobId|
|JobDeleteStartEvent|はい|ジョブ削除の開始イベント|Count|合計|削除を要求されたジョブの合計数。|jobId|
|JobDisableCompleteEvent|はい|ジョブ無効化の完了イベント|Count|合計|正常に無効化されたジョブの合計数。|jobId|
|JobDisableStartEvent|はい|ジョブ無効化の開始イベント|Count|合計|無効化を要求されたジョブの合計数。|jobId|
|JobStartEvent|はい|ジョブの開始イベント|Count|合計|正常に開始されたジョブの合計数。|jobId|
|JobTerminateCompleteEvent|はい|ジョブ終了の完了イベント|Count|合計|正常に終了されたジョブの合計数。|jobId|
|JobTerminateStartEvent|はい|ジョブ終了の開始イベント|Count|合計|終了を要求されたジョブの合計数。|jobId|
|LeavingPoolNodeCount|いいえ|プールから移動中のノード数|Count|合計|プールから移動中のノードの数|ディメンションなし|
|LowPriorityCoreCount|いいえ|LowPriority Core Count (優先順位の低いコアの数)|Count|合計|Batch アカウントの優先順位の低いコアの合計数|ディメンションなし|
|OfflineNodeCount|いいえ|オフライン ノード数|Count|合計|オフライン ノードの数|ディメンションなし|
|PoolCreateEvent|はい|プール作成イベント|Count|合計|作成されたプールの合計数|poolId|
|PoolDeleteCompleteEvent|はい|プール削除の完了イベント|Count|合計|完了したプール削除の合計数|poolId|
|PoolDeleteStartEvent|はい|プール削除の開始イベント|Count|合計|開始したプール削除の合計数|poolId|
|PoolResizeCompleteEvent|はい|プールのサイズ変更の完了イベント|Count|合計|完了したプールのサイズ変更の合計数|poolId|
|PoolResizeStartEvent|はい|プールのサイズ変更の開始イベント|Count|合計|開始したプールのサイズ変更の合計数|poolId|
|PreemptedNodeCount|いいえ|Preempted Node Count (割り込まれたノードの数)|Count|合計|割り込まれたノードの数|ディメンションなし|
|RebootingNodeCount|いいえ|再起動中のノード数|Count|合計|再起動中のノードの数|ディメンションなし|
|ReimagingNodeCount|いいえ|再イメージ化中のノード数|Count|合計|再イメージ化中のノードの数|ディメンションなし|
|RunningNodeCount|いいえ|実行中のノード数|Count|合計|実行中のノードの数|ディメンションなし|
|StartingNodeCount|いいえ|開始ノード数|Count|合計|開始するノードの数|ディメンションなし|
|StartTaskFailedNodeCount|いいえ|開始タスクの失敗ノード数|Count|合計|開始タスクが失敗したノードの数|ディメンションなし|
|TaskCompleteEvent|はい|タスク完了イベント|Count|合計|完了したタスクの合計数|poolId、jobId|
|TaskFailEvent|はい|タスク失敗イベント|Count|合計|障害状態で完了したタスクの合計数|poolId、jobId|
|TaskStartEvent|はい|タスク開始イベント|Count|合計|開始したタスクの合計数|poolId、jobId|
|TotalLowPriorityNodeCount|いいえ|Low-Priority Node Count (優先順位の低いノードの数)|Count|合計|Batch アカウントの優先順位の低いノードの合計数|ディメンションなし|
|TotalNodeCount|いいえ|Dedicated Node Count (専用ノードの数)|Count|合計|Batch アカウントの専用ノードの合計数|ディメンションなし|
|UnusableNodeCount|いいえ|使用できないノード数|Count|合計|使用できないノードの数|ディメンションなし|
|WaitingForStartTaskNodeCount|いいえ|開始タスクの待機ノード数|Count|合計|開始タスクの完了を待機しているノードの数|ディメンションなし|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|アクティブなコア|はい|アクティブなコア|Count|Average|アクティブなコアの数|Scenario、ClusterName|
|アクティブなノード|はい|アクティブなノード|Count|Average|実行中のノードの数|Scenario、ClusterName|
|アイドル状態のコア|はい|アイドル状態のコア|Count|Average|アイドル状態のコアの数|Scenario、ClusterName|
|アイドル状態のノード|はい|アイドル状態のノード|Count|Average|アイドル状態のノードの数|Scenario、ClusterName|
|完了したジョブ|はい|完了したジョブ|Count|合計|完了したジョブの数|Scenario、ClusterName、ResultType|
|送信されたジョブ|はい|送信されたジョブ|Count|合計|送信されたジョブの数|Scenario、ClusterName|
|終了中のコア|はい|終了中のコア|Count|Average|終了中のコアの数|Scenario、ClusterName|
|終了中のノード|はい|終了中のノード|Count|Average|終了中のノードの数|Scenario、ClusterName|
|割り込まれたコア|はい|割り込まれたコア|Count|Average|割り込まれたコアの数|Scenario、ClusterName|
|割り込まれたノード|はい|割り込まれたノード|Count|Average|割り込まれたノードの数|Scenario、ClusterName|
|クォータ使用率|はい|クォータ使用率|Count|Average|クォータ使用率 (%)|Scenario、ClusterName、VmFamilyName、VmPriority|
|コアの合計|はい|コアの合計|Count|Average|コアの合計数|Scenario、ClusterName|
|ノード総数|はい|ノード総数|Count|Average|ノードの合計数|Scenario、ClusterName|
|使用できないコア|はい|使用できないコア|Count|Average|使用できないコアの数|Scenario、ClusterName|
|使用できないノード|はい|使用できないノード|Count|Average|使用できないノードの数|Scenario、ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|はい|BroadcastProcessedCountDisplayName|Count|Average|処理されたトランザクションの数|Node、channel、type、status|
|ChaincodeExecuteTimeouts|はい|ChaincodeExecuteTimeoutsDisplayName|Count|Average|タイムアウトしたチェーンコード実行の数 (初期化または呼び出し)。|Node、chaincode|
|ChaincodeLaunchFailures|はい|ChaincodeLaunchFailuresDisplayName|Count|Average|失敗したチェーンコードの起動数。|Node、chaincode|
|ChaincodeLaunchTimeouts|はい|ChaincodeLaunchTimeoutsDisplayName|Count|Average|タイムアウトしたチェーンコードの起動数。|Node、chaincode|
|ChaincodeShimRequestsCompleted|はい|ChaincodeShimRequestsCompletedDisplayName|Count|Average|完了したチェーンコード shim 要求の数。|Node、type、channel、chaincode、success|
|ChaincodeShimRequestsReceived|はい|ChaincodeShimRequestsReceivedDisplayName|Count|Average|受信したチェーンコード shim 要求の数。|Node、type、channel、chaincode|
|ClusterCommEgressQueueCapacity|はい|ClusterCommEgressQueueCapacityDisplayName|Count|Average|送信キューの容量。|Node、host、msg_type、channel|
|ClusterCommEgressQueueLength|はい|ClusterCommEgressQueueLengthDisplayName|Count|Average|送信キューの長さ。|Node、host、msg_type、channel|
|ClusterCommEgressQueueWorkers|はい|ClusterCommEgressQueueWorkersDisplayName|Count|Average|送信キュー ワーカーの数。|Node、channel|
|ClusterCommEgressStreamCount|はい|ClusterCommEgressStreamCountDisplayName|Count|Average|他のノードへのストリームの数。|Node、channel|
|ClusterCommEgressTlsConnectionCount|はい|ClusterCommEgressTlsConnectionCountDisplayName|Count|Average|他のノードへの TLS 接続の数。|ノード|
|ClusterCommIngressStreamCount|はい|ClusterCommIngressStreamCountDisplayName|Count|Average|他のノードからのストリームの数。|ノード|
|ClusterCommMsgDroppedCount|はい|ClusterCommMsgDroppedCountDisplayName|Count|Average|ドロップされたメッセージの数。|Node、host、channel|
|ConnectionAccepted|はい|承認された接続|Count|合計|承認された接続|Node|
|ConnectionActive|はい|アクティブな接続|Count|Average|アクティブな接続|Node|
|ConnectionHandled|はい|処理された接続|Count|合計|処理された接続|Node|
|ConsensusEtcdraftActiveNodes|はい|ConsensusEtcdraftActiveNodesDisplayName|Count|Average|このチャネル内のアクティブなノードの数。|Node、channel|
|ConsensusEtcdraftClusterSize|はい|ConsensusEtcdraftClusterSizeDisplayName|Count|Average|このチャネル内のノードの数。|Node、channel|
|ConsensusEtcdraftCommittedBlockNumber|はい|ConsensusEtcdraftCommittedBlockNumberDisplayName|Count|Average|最後にコミットされたブロックのブロック番号|Node、channel|
|ConsensusEtcdraftConfigProposalsReceived|はい|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Count|Average|構成型のトランザクションに対して受信された提案の合計数。|Node、channel|
|ConsensusEtcdraftIsLeader|はい|ConsensusEtcdraftIsLeaderDisplayName|Count|Average|現在のノードのリーダーシップ ステータス。リーダーである場合は 1、それ以外の場合は 0。|Node、channel|
|ConsensusEtcdraftLeaderChanges|はい|ConsensusEtcdraftLeaderChangesDisplayName|Count|Average|プロセス開始以降のリーダーの変更の数。|Node、channel|
|ConsensusEtcdraftNormalProposalsReceived|はい|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Count|Average|標準型のトランザクションに対して受信された提案の合計数。|Node、channel|
|ConsensusEtcdraftProposalFailures|はい|ConsensusEtcdraftProposalFailuresDisplayName|Count|Average|提案に失敗した回数。|Node、channel|
|ConsensusEtcdraftSnapshotBlockNumber|はい|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Count|Average|最後のスナップショットのブロック番号。|Node、channel|
|ConsensusKafkaBatchSize|はい|ConsensusKafkaBatchSizeDisplayName|Count|Average|トピックに送信される平均バッチ サイズ (バイト単位)。|Node、topic|
|ConsensusKafkaCompressionRatio|はい|ConsensusKafkaCompressionRatioDisplayName|Count|Average|トピックの平均圧縮率 (%)。|Node、topic|
|ConsensusKafkaIncomingByteRate|はい|ConsensusKafkaIncomingByteRateDisplayName|Count|Average|1 秒間に読み取られたブローカーのバイト数。|Node、broker_id|
|ConsensusKafkaLastOffsetPersisted|はい|ConsensusKafkaLastOffsetPersistedDisplayName|Count|Average|最後にコミットされたブロックのブロック メタデータで指定されたオフセット。|Node、channel|
|ConsensusKafkaOutgoingByteRate|はい|ConsensusKafkaOutgoingByteRateDisplayName|Count|Average|1 秒間にブローカーに書き込まれたバイト数。|Node、broker_id|
|ConsensusKafkaRecordSendRate|はい|ConsensusKafkaRecordSendRateDisplayName|Count|Average|1 秒間にトピックに送信されたレコードの数。|Node、topic|
|ConsensusKafkaRecordsPerRequest|はい|ConsensusKafkaRecordsPerRequestDisplayName|Count|Average|トピックに対する要求ごとに送信される平均レコード数。|Node、topic|
|ConsensusKafkaRequestLatency|はい|ConsensusKafkaRequestLatencyDisplayName|Count|Average|ブローカーに対する平均要求待機時間 (ミリ秒単位)。|Node、broker_id|
|ConsensusKafkaRequestRate|はい|ConsensusKafkaRequestRateDisplayName|Count|Average|1 秒間にブローカーに送信された要求の数。|Node、broker_id|
|ConsensusKafkaRequestSize|はい|ConsensusKafkaRequestSizeDisplayName|Count|Average|ブローカーに対する平均要求サイズ (バイト単位)。|Node、broker_id|
|ConsensusKafkaResponseRate|はい|ConsensusKafkaResponseRateDisplayName|Count|Average|1 秒間にブローカーに送信された要求の数。|Node、broker_id|
|ConsensusKafkaResponseSize|はい|ConsensusKafkaResponseSizeDisplayName|Count|Average|ブローカーからの平均応答サイズ (バイト単位)。|Node、broker_id|
|CpuUsagePercentageInDouble|はい|CPU 使用率 (%)|Percent|最大値|CPU 使用率 (%)|Node|
|DeliverBlocksSent|はい|DeliverBlocksSentDisplayName|Count|Average|配信サービスによって送信されたブロックの数。|Node、channel、filtered、data_type|
|DeliverRequestsCompleted|はい|DeliverRequestsCompletedDisplayName|Count|Average|完了した配信要求の数。|Node、channel、filtered、data_type、success|
|DeliverRequestsReceived|はい|DeliverRequestsReceivedDisplayName|Count|Average|受信した配信要求の数。|Node、channel、filtered、data_type|
|DeliverStreamsClosed|はい|DeliverStreamsClosedDisplayName|Count|Average|配信サービスに対して閉じられている GRPC ストリームの数。|ノード|
|DeliverStreamsOpened|はい|DeliverStreamsOpenedDisplayName|Count|Average|配信サービスに対して開いている GRPC ストリームの数。|ノード|
|EndorserChaincodeInstantiationFailures|はい|EndorserChaincodeInstantiationFailuresDisplayName|Count|Average|失敗したチェーンコードのインスタンス化またはアップグレードの数。|Node、channel、chaincode|
|EndorserDuplicateTransactionFailures|はい|EndorserDuplicateTransactionFailuresDisplayName|Count|Average|トランザクション ID が重複しているために失敗した提案の数。|Node、channel、chaincode|
|EndorserEndorsementFailures|はい|EndorserEndorsementFailuresDisplayName|Count|Average|失敗した保証の数。|Node、channel、chaincode、chaincodeerror|
|EndorserProposalAclFailures|はい|EndorserProposalAclFailuresDisplayName|Count|Average|ACL チェックに失敗した提案の数。|Node、channel、chaincode|
|EndorserProposalSimulationFailures|はい|EndorserProposalSimulationFailuresDisplayName|Count|Average|失敗した提案のシミュレーションの数。|Node、channel、chaincode|
|EndorserProposalsReceived|はい|EndorserProposalsReceivedDisplayName|Count|Average|受信した提案の数。|ノード|
|EndorserProposalValidationFailures|はい|EndorserProposalValidationFailuresDisplayName|Count|Average|初期検証に失敗した提案の数。|ノード|
|EndorserSuccessfulProposals|はい|EndorserSuccessfulProposalsDisplayName|Count|Average|成功した提案の数。|ノード|
|FabricVersion|はい|FabricVersionDisplayName|Count|Average|ファブリックのアクティブなバージョン。|Node、version|
|GossipCommMessagesReceived|はい|GossipCommMessagesReceivedDisplayName|Count|Average|受信したメッセージの数。|ノード|
|GossipCommMessagesSent|はい|GossipCommMessagesSentDisplayName|Count|Average|送信されたメッセージの数。|ノード|
|GossipCommOverflowCount|はい|GossipCommOverflowCountDisplayName|Count|Average|発信キュー バッファー オーバーフローの数。|ノード|
|GossipLeaderElectionLeader|はい|GossipLeaderElectionLeaderDisplayName|Count|Average|ピアはリーダー (1) またはフォロワー (0) です|Node、channel|
|GossipMembershipTotalPeersKnown|はい|GossipMembershipTotalPeersKnownDisplayName|Count|Average|既知のピアの合計|Node、channel|
|GossipPayloadBufferSize|はい|GossipPayloadBufferSizeDisplayName|Count|Average|ペイロード バッファーのサイズ。|Node、channel|
|GossipStateHeight|はい|GossipStateHeightDisplayName|Count|Average|現在の台帳 (高さ)|Node、channel|
|GrpcCommConnClosed|はい|GrpcCommConnClosedDisplayName|Count|Average|閉じた gRPC 接続の数。 開いた接続から閉じた接続を引いたものがアクティブな接続の数です。|ノード|
|GrpcCommConnOpened|はい|GrpcCommConnOpenedDisplayName|Count|Average|開いた gRPC 接続の数。 開いた接続から閉じた接続を引いたものがアクティブな接続の数です。|ノード|
|GrpcServerStreamMessagesReceived|はい|GrpcServerStreamMessagesReceivedDisplayName|Count|Average|受信したストリーム メッセージの数。|Node、service、method|
|GrpcServerStreamMessagesSent|はい|GrpcServerStreamMessagesSentDisplayName|Count|Average|送信したストリーム メッセージの数。|Node、service、method|
|GrpcServerStreamRequestsCompleted|はい|GrpcServerStreamRequestsCompletedDisplayName|Count|Average|完了したストリーム要求の数。|Node、service、method、code|
|GrpcServerUnaryRequestsReceived|はい|GrpcServerUnaryRequestsReceivedDisplayName|Count|Average|受信した単項要求の数。|Node、service、method|
|IOReadBytes|はい|IO 読み取りバイト数|バイト|合計|IO 読み取りバイト数|Node|
|IOWriteBytes|はい|IO 書き込みバイト数|バイト|合計|IO 書き込みバイト数|Node|
|LedgerBlockchainHeight|はい|LedgerBlockchainHeightDisplayName|Count|Average|ブロック内のチェーンの高さ。|Node、channel|
|LedgerTransactionCount|はい|LedgerTransactionCountDisplayName|Count|Average|処理されたトランザクションの数|Node、channel、transaction_type、chaincode、validation_code|
|LoggingEntriesChecked|はい|LoggingEntriesCheckedDisplayName|Count|Average|アクティブなログ記録レベルに対してチェックされたログ エントリの数。|Node、level|
|LoggingEntriesWritten|はい|LoggingEntriesWrittenDisplayName|Count|Average|書き込まれたログ エントリの数。|Node、level|
|MemoryLimit|はい|メモリの制限|バイト|Average|メモリの制限|Node|
|MemoryUsage|はい|メモリ使用量|バイト|Average|メモリ使用量|Node|
|MemoryUsagePercentageInDouble|はい|メモリ使用率 (%)|Percent|Average|メモリ使用率 (%)|Node|
|PendingTransactions|はい|保留中のトランザクション|Count|Average|保留中のトランザクション|Node|
|ProcessedBlocks|はい|処理されたブロック|Count|合計|処理されたブロック|Node|
|ProcessedTransactions|はい|処理されたトランザクション|Count|合計|処理されたトランザクション|Node|
|QueuedTransactions|はい|キューに登録されたトランザクション|Count|Average|キューに登録されたトランザクション|Node|
|RequestHandled|はい|処理された要求|Count|合計|処理された要求|Node|
|StorageUsage|はい|ストレージの使用状況|バイト|Average|ストレージの使用状況|Node|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|RequestLatency|はい|要求の待機時間|ミリ秒|合計|サーバーが要求の処理に要した時間|Operation、Authentication、Protocol|
|RequestsTraffic|はい|要求トラフィック|Percent|Count|行われた要求の数|Operation、Authentication、Protocol、StatusCode、StatusCodeClass|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|allcachehits|はい|キャッシュ ヒット数 (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|allcachemisses|はい|キャッシュ ミス数 (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|allcacheRead|はい|キャッシュ読み取り (インスタンス ベース)|BytesPerSecond|最大値||ShardId、Port、Primary|
|allcacheWrite|はい|キャッシュ書き込み (インスタンス ベース)|BytesPerSecond|最大値||ShardId、Port、Primary|
|allconnectedclients|はい|接続されたクライアント (インスタンス ベース)|Count|最大値||ShardId、Port、Primary|
|allevictedkeys|はい|解放されたキー (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|allexpiredkeys|はい|有効期限が切れたキー (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|allgetcommands|はい|取得 (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|alloperationsPerSecond|はい|1 秒あたりの操作回数 (インスタンス ベース)|Count|最大値||ShardId、Port、Primary|
|allserverLoad|はい|サーバーの負荷 (インスタンス ベース)|Percent|最大値||ShardId、Port、Primary|
|allsetcommands|はい|設定 (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|alltotalcommandsprocessed|はい|操作の合計 (インスタンス ベース)|Count|合計||ShardId、Port、Primary|
|alltotalkeys|はい|キー合計 (インスタンス ベース)|Count|最大値||ShardId、Port、Primary|
|allusedmemory|はい|使用メモリ (インスタンス ベース)|バイト|最大値||ShardId、Port、Primary|
|allusedmemorypercentage|はい|使用メモリの割合 (インスタンス ベース)|Percent|最大値||ShardId、Port、Primary|
|allusedmemoryRss|はい|使用メモリ RSS (インスタンス ベース)|バイト|最大値||ShardId、Port、Primary|
|cachehits|はい|キャッシュ ヒット数|Count|合計||ShardId|
|cachehits0|はい|キャッシュ ヒット数 (シャード 0)|Count|合計||ディメンションなし|
|cachehits1|はい|キャッシュ ヒット数 (シャード 1)|Count|合計||ディメンションなし|
|cachehits2|はい|キャッシュ ヒット数 (シャード 2)|Count|合計||ディメンションなし|
|cachehits3|はい|キャッシュ ヒット数 (シャード 3)|Count|合計||ディメンションなし|
|cachehits4|はい|キャッシュ ヒット数 (シャード 4)|Count|合計||ディメンションなし|
|cachehits5|はい|キャッシュ ヒット数 (シャード 5)|Count|合計||ディメンションなし|
|cachehits6|はい|キャッシュ ヒット数 (シャード 6)|Count|合計||ディメンションなし|
|cachehits7|はい|キャッシュ ヒット数 (シャード 7)|Count|合計||ディメンションなし|
|cachehits8|はい|キャッシュ ヒット数 (シャード 8)|Count|合計||ディメンションなし|
|cachehits9|はい|キャッシュ ヒット数 (シャード 9)|Count|合計||ディメンションなし|
|cacheLatency|はい|キャッシュの待機時間 (マイクロ秒単位) (プレビュー)|Count|Average||ShardId|
|cachemisses|はい|キャッシュ ミス数|Count|合計||ShardId|
|cachemisses0|はい|キャッシュ ミス数 (シャード 0)|Count|合計||ディメンションなし|
|cachemisses1|はい|キャッシュ ミス数 (シャード 1)|Count|合計||ディメンションなし|
|cachemisses2|はい|キャッシュ ミス数 (シャード 2)|Count|合計||ディメンションなし|
|cachemisses3|はい|キャッシュ ミス数 (シャード 3)|Count|合計||ディメンションなし|
|cachemisses4|はい|キャッシュ ミス数 (シャード 4)|Count|合計||ディメンションなし|
|cachemisses5|はい|キャッシュ ミス数 (シャード 5)|Count|合計||ディメンションなし|
|cachemisses6|はい|キャッシュ ミス数 (シャード 6)|Count|合計||ディメンションなし|
|cachemisses7|はい|キャッシュ ミス数 (シャード 7)|Count|合計||ディメンションなし|
|cachemisses8|はい|キャッシュ ミス数 (シャード 8)|Count|合計||ディメンションなし|
|cachemisses9|はい|キャッシュ ミス数 (シャード 9)|Count|合計||ディメンションなし|
|cachemissrate|はい|キャッシュ ミス率|Percent|cachemissrate||ShardId|
|cacheRead|はい|キャッシュの読み取り|BytesPerSecond|最大値||ShardId|
|cacheRead0|はい|キャッシュの読み取り (シャード 0)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead1|はい|キャッシュの読み取り (シャード 1)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead2|はい|キャッシュの読み取り (シャード 2)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead3|はい|キャッシュの読み取り (シャード 3)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead4|はい|キャッシュの読み取り (シャード 4)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead5|はい|キャッシュの読み取り (シャード 5)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead6|はい|キャッシュの読み取り (シャード 6)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead7|はい|キャッシュの読み取り (シャード 7)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead8|はい|キャッシュの読み取り (シャード 8)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead9|はい|キャッシュの読み取り (シャード 9)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite|はい|キャッシュの書き込み|BytesPerSecond|最大値||ShardId|
|cacheWrite0|はい|キャッシュの書き込み (シャード 0)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite1|はい|キャッシュの書き込み (シャード 1)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite2|はい|キャッシュの書き込み (シャード 2)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite3|はい|キャッシュの書き込み (シャード 3)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite4|はい|キャッシュの書き込み (シャード 4)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite5|はい|キャッシュの書き込み (シャード 5)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite6|はい|キャッシュの書き込み (シャード 6)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite7|はい|キャッシュの書き込み (シャード 7)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite8|はい|キャッシュの書き込み (シャード 8)|BytesPerSecond|最大値||ディメンションなし|
|cacheWrite9|はい|キャッシュの書き込み (シャード 9)|BytesPerSecond|最大値||ディメンションなし|
|connectedclients|はい|接続されているクライアント数|Count|最大値||ShardId|
|connectedclients0|はい|接続されているクライアント数 (シャード 0)|Count|最大値||ディメンションなし|
|connectedclients1|はい|接続されているクライアント数 (シャード 1)|Count|最大値||ディメンションなし|
|connectedclients2|はい|接続されているクライアント数 (シャード 2)|Count|最大値||ディメンションなし|
|connectedclients3|はい|接続されているクライアント数 (シャード 3)|Count|最大値||ディメンションなし|
|connectedclients4|はい|接続されているクライアント数 (シャード 4)|Count|最大値||ディメンションなし|
|connectedclients5|はい|接続されているクライアント数 (シャード 5)|Count|最大値||ディメンションなし|
|connectedclients6|はい|接続されているクライアント数 (シャード 6)|Count|最大値||ディメンションなし|
|connectedclients7|はい|接続されているクライアント数 (シャード 7)|Count|最大値||ディメンションなし|
|connectedclients8|はい|接続されているクライアント数 (シャード 8)|Count|最大値||ディメンションなし|
|connectedclients9|はい|接続されているクライアント数 (シャード 9)|Count|最大値||ディメンションなし|
|エラー|はい|エラー|Count|最大値||ShardId、ErrorType|
|evictedkeys|はい|削除されたキー数|Count|合計||ShardId|
|evictedkeys0|はい|削除されたキー数 (シャード 0)|Count|合計||ディメンションなし|
|evictedkeys1|はい|削除されたキー数 (シャード 1)|Count|合計||ディメンションなし|
|evictedkeys2|はい|削除されたキー数 (シャード 2)|Count|合計||ディメンションなし|
|evictedkeys3|はい|削除されたキー数 (シャード 3)|Count|合計||ディメンションなし|
|evictedkeys4|はい|削除されたキー数 (シャード 4)|Count|合計||ディメンションなし|
|evictedkeys5|はい|削除されたキー数 (シャード 5)|Count|合計||ディメンションなし|
|evictedkeys6|はい|削除されたキー数 (シャード 6)|Count|合計||ディメンションなし|
|evictedkeys7|はい|削除されたキー数 (シャード 7)|Count|合計||ディメンションなし|
|evictedkeys8|はい|削除されたキー数 (シャード 8)|Count|合計||ディメンションなし|
|evictedkeys9|はい|削除されたキー数 (シャード 9)|Count|合計||ディメンションなし|
|expiredkeys|はい|期限切れキー数|Count|合計||ShardId|
|expiredkeys0|はい|期限切れキー数 (シャード 0)|Count|合計||ディメンションなし|
|expiredkeys1|はい|期限切れキー数 (シャード 1)|Count|合計||ディメンションなし|
|expiredkeys2|はい|期限切れキー数 (シャード 2)|Count|合計||ディメンションなし|
|expiredkeys3|はい|期限切れキー数 (シャード 3)|Count|合計||ディメンションなし|
|expiredkeys4|はい|期限切れキー数 (シャード 4)|Count|合計||ディメンションなし|
|expiredkeys5|はい|期限切れキー数 (シャード 5)|Count|合計||ディメンションなし|
|expiredkeys6|はい|期限切れキー数 (シャード 6)|Count|合計||ディメンションなし|
|expiredkeys7|はい|期限切れキー数 (シャード 7)|Count|合計||ディメンションなし|
|expiredkeys8|はい|期限切れキー数 (シャード 8)|Count|合計||ディメンションなし|
|expiredkeys9|はい|期限切れキー数 (シャード 9)|Count|合計||ディメンションなし|
|getcommands|はい|取得数|Count|合計||ShardId|
|getcommands0|はい|取得数 (シャード 0)|Count|合計||ディメンションなし|
|getcommands1|はい|取得数 (シャード 1)|Count|合計||ディメンションなし|
|getcommands2|はい|取得数 (シャード 2)|Count|合計||ディメンションなし|
|getcommands3|はい|取得数 (シャード 3)|Count|合計||ディメンションなし|
|getcommands4|はい|取得数 (シャード 4)|Count|合計||ディメンションなし|
|getcommands5|はい|取得数 (シャード 5)|Count|合計||ディメンションなし|
|getcommands6|はい|取得数 (シャード 6)|Count|合計||ディメンションなし|
|getcommands7|はい|取得数 (シャード 7)|Count|合計||ディメンションなし|
|getcommands8|はい|取得数 (シャード 8)|Count|合計||ディメンションなし|
|getcommands9|はい|取得数 (シャード 9)|Count|合計||ディメンションなし|
|operationsPerSecond|はい|1 秒あたりの操作回数|Count|最大値||ShardId|
|operationsPerSecond0|はい|1 秒あたりの操作回数 (シャード 0)|Count|最大値||ディメンションなし|
|operationsPerSecond1|はい|1 秒あたりの操作回数 (シャード 1)|Count|最大値||ディメンションなし|
|operationsPerSecond2|はい|1 秒あたりの操作回数 (シャード 2)|Count|最大値||ディメンションなし|
|operationsPerSecond3|はい|1 秒あたりの操作回数 (シャード 3)|Count|最大値||ディメンションなし|
|operationsPerSecond4|はい|1 秒あたりの操作回数 (シャード 4)|Count|最大値||ディメンションなし|
|operationsPerSecond5|はい|1 秒あたりの操作回数 (シャード 5)|Count|最大値||ディメンションなし|
|operationsPerSecond6|はい|1 秒あたりの操作回数 (シャード 6)|Count|最大値||ディメンションなし|
|operationsPerSecond7|はい|1 秒あたりの操作回数 (シャード 7)|Count|最大値||ディメンションなし|
|operationsPerSecond8|はい|1 秒あたりの操作回数 (シャード 8)|Count|最大値||ディメンションなし|
|operationsPerSecond9|はい|1 秒あたりの操作回数 (シャード 9)|Count|最大値||ディメンションなし|
|percentProcessorTime|はい|CPU|Percent|最大値||ShardId|
|percentProcessorTime0|はい|CPU (シャード 0)|Percent|最大値||ディメンションなし|
|percentProcessorTime1|はい|CPU (シャード 1)|Percent|最大値||ディメンションなし|
|percentProcessorTime2|はい|CPU (シャード 2)|Percent|最大値||ディメンションなし|
|percentProcessorTime3|はい|CPU (シャード 3)|Percent|最大値||ディメンションなし|
|percentProcessorTime4|はい|CPU (シャード 4)|Percent|最大値||ディメンションなし|
|percentProcessorTime5|はい|CPU (シャード 5)|Percent|最大値||ディメンションなし|
|percentProcessorTime6|はい|CPU (シャード 6)|Percent|最大値||ディメンションなし|
|percentProcessorTime7|はい|CPU (シャード 7)|Percent|最大値||ディメンションなし|
|percentProcessorTime8|はい|CPU (シャード 8)|Percent|最大値||ディメンションなし|
|percentProcessorTime9|はい|CPU (シャード 9)|Percent|最大値||ディメンションなし|
|serverLoad|はい|サーバーの負荷|Percent|最大値||ShardId|
|serverLoad0|はい|サーバーの負荷 (シャード 0)|Percent|最大値||ディメンションなし|
|serverLoad1|はい|サーバーの負荷 (シャード 1)|Percent|最大値||ディメンションなし|
|serverLoad2|はい|サーバーの負荷 (シャード 2)|Percent|最大値||ディメンションなし|
|serverLoad3|はい|サーバーの負荷 (シャード 3)|Percent|最大値||ディメンションなし|
|serverLoad4|はい|サーバーの負荷 (シャード 4)|Percent|最大値||ディメンションなし|
|serverLoad5|はい|サーバーの負荷 (シャード 5)|Percent|最大値||ディメンションなし|
|serverLoad6|はい|サーバーの負荷 (シャード 6)|Percent|最大値||ディメンションなし|
|serverLoad7|はい|サーバーの負荷 (シャード 7)|Percent|最大値||ディメンションなし|
|serverLoad8|はい|サーバーの負荷 (シャード 8)|Percent|最大値||ディメンションなし|
|serverLoad9|はい|サーバーの負荷 (シャード 9)|Percent|最大値||ディメンションなし|
|setcommands|はい|セット|Count|合計||ShardId|
|setcommands0|はい|設定数 (シャード 0)|Count|合計||ディメンションなし|
|setcommands1|はい|設定数 (シャード 1)|Count|合計||ディメンションなし|
|setcommands2|はい|設定数 (シャード 2)|Count|合計||ディメンションなし|
|setcommands3|はい|設定数 (シャード 3)|Count|合計||ディメンションなし|
|setcommands4|はい|設定数 (シャード 4)|Count|合計||ディメンションなし|
|setcommands5|はい|設定数 (シャード 5)|Count|合計||ディメンションなし|
|setcommands6|はい|設定数 (シャード 6)|Count|合計||ディメンションなし|
|setcommands7|はい|設定数 (シャード 7)|Count|合計||ディメンションなし|
|setcommands8|はい|設定数 (シャード 8)|Count|合計||ディメンションなし|
|setcommands9|はい|設定数 (シャード 9)|Count|合計||ディメンションなし|
|totalcommandsprocessed|はい|合計処理数|Count|合計||ShardId|
|totalcommandsprocessed0|はい|合計処理数 (シャード 0)|Count|合計||ディメンションなし|
|totalcommandsprocessed1|はい|合計処理数 (シャード 1)|Count|合計||ディメンションなし|
|totalcommandsprocessed2|はい|合計処理数 (シャード 2)|Count|合計||ディメンションなし|
|totalcommandsprocessed3|はい|合計処理数 (シャード 3)|Count|合計||ディメンションなし|
|totalcommandsprocessed4|はい|合計処理数 (シャード 4)|Count|合計||ディメンションなし|
|totalcommandsprocessed5|はい|合計処理数 (シャード 5)|Count|合計||ディメンションなし|
|totalcommandsprocessed6|はい|合計処理数 (シャード 6)|Count|合計||ディメンションなし|
|totalcommandsprocessed7|はい|合計処理数 (シャード 7)|Count|合計||ディメンションなし|
|totalcommandsprocessed8|はい|合計処理数 (シャード 8)|Count|合計||ディメンションなし|
|totalcommandsprocessed9|はい|合計処理数 (シャード 9)|Count|合計||ディメンションなし|
|totalkeys|はい|合計キー数|Count|最大値||ShardId|
|totalkeys0|はい|合計キー数 (シャード 0)|Count|最大値||ディメンションなし|
|totalkeys1|はい|合計キー数 (シャード 1)|Count|最大値||ディメンションなし|
|totalkeys2|はい|合計キー数 (シャード 2)|Count|最大値||ディメンションなし|
|totalkeys3|はい|合計キー数 (シャード 3)|Count|最大値||ディメンションなし|
|totalkeys4|はい|合計キー数 (シャード 4)|Count|最大値||ディメンションなし|
|totalkeys5|はい|合計キー数 (シャード 5)|Count|最大値||ディメンションなし|
|totalkeys6|はい|合計キー数 (シャード 6)|Count|最大値||ディメンションなし|
|totalkeys7|はい|合計キー数 (シャード 7)|Count|最大値||ディメンションなし|
|totalkeys8|はい|合計キー数 (シャード 8)|Count|最大値||ディメンションなし|
|totalkeys9|はい|合計キー数 (シャード 9)|Count|最大値||ディメンションなし|
|usedmemory|はい|メモリ使用量|バイト|最大値||ShardId|
|usedmemory0|はい|メモリ使用量 (シャード 0)|バイト|最大値||ディメンションなし|
|usedmemory1|はい|メモリ使用量 (シャード 1)|バイト|最大値||ディメンションなし|
|usedmemory2|はい|メモリ使用量 (シャード 2)|バイト|最大値||ディメンションなし|
|usedmemory3|はい|メモリ使用量 (シャード 3)|バイト|最大値||ディメンションなし|
|usedmemory4|はい|メモリ使用量 (シャード 4)|バイト|最大値||ディメンションなし|
|usedmemory5|はい|メモリ使用量 (シャード 5)|バイト|最大値||ディメンションなし|
|usedmemory6|はい|メモリ使用量 (シャード 6)|バイト|最大値||ディメンションなし|
|usedmemory7|はい|メモリ使用量 (シャード 7)|バイト|最大値||ディメンションなし|
|usedmemory8|はい|メモリ使用量 (シャード 8)|バイト|最大値||ディメンションなし|
|usedmemory9|はい|メモリ使用量 (シャード 9)|バイト|最大値||ディメンションなし|
|usedmemorypercentage|はい|使用されているメモリの割合|Percent|最大値||ShardId|
|usedmemoryRss|はい|RSS メモリ使用量|バイト|最大値||ShardId|
|usedmemoryRss0|はい|RSS メモリ使用量 (シャード 0)|バイト|最大値||ディメンションなし|
|usedmemoryRss1|はい|RSS メモリ使用量 (シャード 1)|バイト|最大値||ディメンションなし|
|usedmemoryRss2|はい|RSS メモリ使用量 (シャード 2)|バイト|最大値||ディメンションなし|
|usedmemoryRss3|はい|RSS メモリ使用量 (シャード 3)|バイト|最大値||ディメンションなし|
|usedmemoryRss4|はい|RSS メモリ使用量 (シャード 4)|バイト|最大値||ディメンションなし|
|usedmemoryRss5|はい|RSS メモリ使用量 (シャード 5)|バイト|最大値||ディメンションなし|
|usedmemoryRss6|はい|RSS メモリ使用量 (シャード 6)|バイト|最大値||ディメンションなし|
|usedmemoryRss7|はい|RSS メモリ使用量 (シャード 7)|バイト|最大値||ディメンションなし|
|usedmemoryRss8|はい|RSS メモリ使用量 (シャード 8)|バイト|最大値||ディメンションなし|
|usedmemoryRss9|はい|RSS メモリ使用量 (シャード 9)|バイト|最大値||ディメンションなし|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|cachehits|はい|キャッシュ ヒット数|Count|合計||ディメンションなし|
|cacheLatency|はい|キャッシュの待機時間 (マイクロ秒単位) (プレビュー)|Count|Average||InstanceId|
|cachemisses|はい|キャッシュ ミス数|Count|合計||InstanceId|
|cacheRead|はい|キャッシュの読み取り|BytesPerSecond|最大値||InstanceId|
|cacheWrite|はい|キャッシュの書き込み|BytesPerSecond|最大値||InstanceId|
|connectedclients|はい|接続されているクライアント数|Count|最大値||InstanceId|
|エラー|はい|エラー|Count|最大値||InstanceId、ErrorType|
|evictedkeys|はい|削除されたキー数|Count|合計||ディメンションなし|
|expiredkeys|はい|期限切れキー数|Count|合計||ディメンションなし|
|getcommands|はい|取得数|Count|合計||ディメンションなし|
|operationsPerSecond|はい|1 秒あたりの操作回数|Count|最大値||ディメンションなし|
|percentProcessorTime|はい|CPU|Percent|最大値||InstanceId|
|serverLoad|はい|サーバーの負荷|Percent|最大値||ディメンションなし|
|setcommands|はい|セット|Count|合計||ディメンションなし|
|totalcommandsprocessed|はい|合計処理数|Count|合計||ディメンションなし|
|totalkeys|はい|合計キー数|Count|最大値||ディメンションなし|
|usedmemory|はい|メモリ使用量|バイト|最大値||ディメンションなし|
|usedmemorypercentage|はい|使用されているメモリの割合|Percent|最大値||InstanceId|
|usedmemoryRss|はい|RSS メモリ使用量|バイト|最大値||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|はい|Web アプリケーション ファイアウォール要求の数|Count|合計|Web アプリケーション ファイアウォールによって処理されるクライアント要求の数|PolicyName、RuleName、Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ByteHitRatio|はい|バイト ヒット率|Percent|Average|これは、キャッシュから処理された合計バイト数と合計応答バイト数の比率です|エンドポイント|
|OriginHealthPercentage|はい|配信元の正常性の割合|Percent|Average|AFDX からバックエンドへの成功した正常性プローブの割合。|Origin、OriginPool|
|OriginLatency|はい|配信元の待機時間|MilliSeconds|Average|AFDX エッジによってバックエンドに要求が送信されてから、AFDX でバックエンドから最後の応答バイトが受信されるまでの算出時間。|Origin、Endpoint|
|OriginRequestCount|はい|配信元の要求数|Count|合計|AFDX から配信元に送信された要求の数。|HttpStatus、HttpStatusGroup、Origin、Endpoint|
|Percentage4XX|はい|4XX の割合|Percent|Average|応答の状態コードが 4XX であるすべてのクライアント要求の割合|Endpoint、ClientRegion、ClientCountry|
|Percentage5XX|はい|5XX の割合|Percent|Average|応答の状態コードが 5XX であるすべてのクライアント要求の割合|Endpoint、ClientRegion、ClientCountry|
|RequestCount|はい|要求数|Count|合計|HTTP/S プロキシによって処理されるクライアント要求の数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry、Endpoint|
|RequestSize|はい|要求サイズ|バイト|合計|AFDX にクライアントからの要求として送信されたバイト数。|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry、Endpoint|
|ResponseSize|はい|応答サイズ|バイト|合計|クライアントに HTTP/S プロキシからの応答として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry、Endpoint|
|TotalLatency|はい|合計待機時間|MilliSeconds|Average|HTTP/S プロキシによってクライアント要求が受信されてから、HTTP/S プロキシからの最後の応答バイトがクライアントで確認されるまでの算出時間|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry、Endpoint|
|WebApplicationFirewallRequestCount|はい|Web アプリケーション ファイアウォール要求の数|Count|合計|Web アプリケーション ファイアウォールによって処理されるクライアント要求の数|PolicyName、RuleName、Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|いいえ|ディスクの読み取り|BytesPerSecond|Average|監視期間中にディスクから読み取られた平均バイト数。|RoleInstanceId|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS。|RoleInstanceId|
|Disk Write Bytes/Sec|いいえ|ディスクの書き込み|BytesPerSecond|Average|監視期間中にディスクに書き込まれた平均バイト数。|RoleInstanceId|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS。|RoleInstanceId|
|Network In|はい|Network In|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|RoleInstanceId|
|Network Out|はい|Network Out|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|RoleInstanceId|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|いいえ|ディスクの読み取り|BytesPerSecond|Average|監視期間中にディスクから読み取られた平均バイト数。|ディメンションなし|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS。|ディメンションなし|
|Disk Write Bytes/Sec|いいえ|ディスクの書き込み|BytesPerSecond|Average|監視期間中にディスクに書き込まれた平均バイト数。|ディメンションなし|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS。|ディメンションなし|
|Network In|はい|Network In|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|ディメンションなし|
|Network Out|はい|Network Out|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|ディメンションなし|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|ディメンションなし|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|UsedCapacity|いいえ|Used capacity (使用済み容量)|バイト|Average|アカウントの使用済み容量|ディメンションなし|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|BlobCapacity|いいえ|Blob Capacity (BLOB 容量)|バイト|Average|ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。|BlobType、Tier|
|BlobCount|いいえ|Blob Count (BLOB 数)|Count|Average|ストレージ アカウントの Blob service 内の BLOB の数。|BlobType、Tier|
|ContainerCount|はい|Blob Container Count (BLOB コンテナー数)|Count|Average|ストレージ アカウントの Blob service 内のコンテナーの数。|ディメンションなし|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|IndexCapacity|いいえ|インデックスの容量|バイト|Average|ADLS Gen2 (階層構造) インデックスで使用されるストレージの容量 (バイト単位)。|ディメンションなし|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication、FileShare|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication、FileShare|
|FileCapacity|いいえ|File Capacity (ファイル容量)|バイト|Average|ストレージ アカウントの File サービスによって使われているストレージの量 (バイト単位)。|FileShare|
|FileCount|いいえ|File Count (ファイル数)|Count|Average|ストレージ アカウントの File サービス内のファイルの数。|FileShare|
|FileShareCount|いいえ|File Share Count (ファイル共有数)|Count|Average|ストレージ アカウントの File サービス内のファイル共有の数。|ディメンションなし|
|FileShareQuota|いいえ|ファイル共有のクォータ サイズ|バイト|Average|Azure Files サービスで使用できる記憶域の容量の上限 (バイト単位)。|FileShare|
|FileShareSnapshotCount|いいえ|ファイル共有のスナップショット数|Count|Average|ストレージ アカウントの Files サービス内の共有に存在するスナップショットの数。|FileShare|
|FileShareSnapshotSize|いいえ|ファイル共有のスナップショットのサイズ|バイト|Average|ストレージ アカウントの Files サービスのスナップショットによって使用される記憶域の容量 (バイト単位)。|FileShare|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication、FileShare|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication、FileShare|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication、FileShare|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication、FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|QueueCapacity|はい|Queue Capacity (キュー容量)|バイト|Average|ストレージ アカウントの Queue サービスによって使われているストレージの量 (バイト単位)。|ディメンションなし|
|QueueCount|はい|Queue Count (キュー数)|Count|Average|ストレージ アカウントの Queue サービス内のキューの数。|ディメンションなし|
|QueueMessageCount|はい|Queue Message Count (キュー メッセージ数)|Count|Average|ストレージ アカウントの Queue サービス内のキュー メッセージの概数。|ディメンションなし|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求のエンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|成功した要求の処理に Azure Storage が使った待機時間 (ミリ秒単位)。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|TableCapacity|はい|Table Capacity (テーブル容量)|バイト|Average|ストレージ アカウントの Table service によって使われているストレージの量 (バイト単位)。|ディメンションなし|
|TableCount|はい|Table Count (テーブル数)|Count|Average|ストレージ アカウントの Table service 内のテーブルの数。|ディメンションなし|
|TableEntityCount|はい|Table Entity Count (テーブル エンティティ数)|Count|Average|ストレージ アカウントの Table service 内のテーブル エンティティの数。|ディメンションなし|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BlockedCalls|はい|ブロックされた呼び出し|Count|合計|レートまたはクォータの制限を超えた呼び出しの回数。|ApiName、OperationName、Region|
|CharactersTrained|はい|トレーニングされた文字数|Count|合計|トレーニングされた文字の合計数。|ApiName、OperationName、Region|
|CharactersTranslated|はい|Characters Translated (変換文字数)|Count|合計|受信テキスト要求の合計文字数。|ApiName、OperationName、Region|
|ClientErrors|はい|クライアント エラー|Count|合計|クライアント側のエラー (HTTP 応答コード 4xx) があった呼び出しの回数。|ApiName、OperationName、Region|
|DataIn|はい|受信データ|バイト|合計|受信データのサイズ (バイト)。|ApiName、OperationName、Region|
|DataOut|はい|送信データ|バイト|合計|送信データのサイズ (バイト)。|ApiName、OperationName、Region|
|Latency|はい|Latency|MilliSeconds|Average|待機時間 (ミリ秒)。|ApiName、OperationName、Region|
|LearnedEvents|はい|学習したイベント|Count|合計|学習したイベントの数。|IsMatchBaseline、Mode、RunId|
|MatchedRewards|はい|一致した報酬|Count|合計| 一致した報酬の数。|IsMatchBaseline、Mode、RunId|
|ObservedRewards|はい|観察された報酬|Count|合計|観察された報酬の数。|IsMatchBaseline、Mode、RunId|
|ProcessedCharacters|はい|処理された文字|Count|合計|文字数。|ApiName、FeatureName、UsageChannel、Region|
|ProcessedTextRecords|はい|処理されたテキスト レコード|Count|合計|テキスト レコードの数。|ApiName、FeatureName、UsageChannel、Region|
|ServerErrors|はい|サーバー エラー|Count|合計|サービスの内部エラー (HTTP 応答コード 5xx) があった呼び出しの回数。|ApiName、OperationName、Region|
|SpeechSessionDuration|はい|Speech Session Duration (音声セッション継続時間)|Seconds|合計|音声セッションの合計継続時間 (秒)。|ApiName、OperationName、Region|
|SuccessfulCalls|はい|成功した呼び出し|Count|合計|成功した呼び出しの数。|ApiName、OperationName、Region|
|TotalCalls|はい|合計呼び出し数|Count|合計|呼び出しの合計数。|ApiName、OperationName、Region|
|TotalErrors|はい|合計エラー数|Count|合計|エラー応答 (HTTP 応答コード 4xx または 5xx) があった呼び出しの合計回数。|ApiName、OperationName、Region|
|TotalTokenCalls|はい|トークン呼び出しの合計|Count|合計|トークン呼び出しの合計回数。|ApiName、OperationName、Region|
|TotalTransactions|はい|合計トランザクション|Count|合計|トランザクションの合計数。|ディメンションなし|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|いいえ|認証 API 要求|Count|Count|通信サービスの認証エンドポイントに対するすべての要求の数。|Operation、StatusCode、StatusCodeClass|
|APIRequestChat|はい|チャット API 要求|Count|Count|通信サービスのチャット エンドポイントに対するすべての要求の数。|Operation、StatusCode、StatusCodeClass|
|APIRequestSMS|はい|SMS API 要求|Count|Count|通信サービスの SMS エンドポイントに対するすべての要求の数。|Operation、StatusCode、StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Disk Read Bytes|はい|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|RoleInstanceId、RoleId|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|RoleInstanceId、RoleId|
|Disk Write Bytes|はい|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|RoleInstanceId、RoleId|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|RoleInstanceId、RoleId|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|RoleInstanceId、RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Disk Read Bytes|はい|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|RoleInstanceId、RoleId|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|RoleInstanceId、RoleId|
|Disk Write Bytes|はい|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|RoleInstanceId、RoleId|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|RoleInstanceId、RoleId|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|RoleInstanceId、RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|複合ディスク読み取りバイト数/秒|いいえ|ディスク読み取りバイト数/秒 (プレビュー)|バイト|Average|監視期間中にディスクから読み取られたバイト数/秒。このメトリックはプレビュー段階であり、一般提供されるまでに変更される可能性があります||
|複合ディスク読み取り操作数/秒|いいえ|ディスク読み取り操作数/秒 (プレビュー)|バイト|Average|監視期間中にディスク上で実行された読み取り IO の数。このメトリックはプレビュー段階であり、一般提供されるまでに変更される可能性があります||
|複合ディスク書き込みバイト数/秒|いいえ|ディスク書き込みバイト数/秒 (プレビュー)|バイト|Average|監視期間中にディスクに書き込まれたバイト数/秒。このメトリックはプレビュー段階であり、一般提供されるまでに変更される可能性があります||
|複合ディスク書き込み操作数/秒|いいえ|ディスク書き込み操作数/秒 (プレビュー)|バイト|Average|監視期間中にディスク上で実行された書き込み IO の数。このメトリックはプレビュー段階であり、一般提供されるまでに変更される可能性があります||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|使用済みの CPU クレジット|はい|使用済みの CPU クレジット|Count|Average|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|未使用の CPU クレジット|はい|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|ディメンションなし|
|データ ディスク帯域幅の消費率|はい|データ ディスク帯域幅の消費率|Percent|Average|1 分あたりに消費されたデータ ディスク帯域幅の割合|LUN|
|[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)|はい|[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)|Percent|Average|1 分あたりに消費されたデータ ディスク I/O の割合|LUN|
|データ ディスクの最大バースト帯域幅|はい|データ ディスクの最大バースト帯域幅|Count|Average|バーストを使用してデータ ディスクで実現できる 1 秒あたりの最大バイト数|LUN|
|データ ディスクの最大バースト IOPS|はい|データ ディスクの最大バースト IOPS|Count|Average|バーストを使用してデータ ディスクで実現できる最大 IOPS|LUN|
|データ ディスクのキューの深さ|はい|データ ディスクのキューの深さ|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|LUN|
|データ ディスク読み取りバイト数/秒|はい|データ ディスク読み取りバイト数/秒|BytesPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN|
|データ ディスク読み取り操作数/秒|はい|データ ディスク読み取り操作数/秒|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|LUN|
|データ ディスクのターゲット帯域幅|はい|データ ディスクのターゲット帯域幅|Count|Average|バーストなしでデータ ディスクが実現できる 1 秒あたりのベースライン バイト数|LUN|
|データ ディスクのターゲット IOPS|はい|データ ディスクのターゲット IOPS|Count|Average|バーストなしでデータ ディスクが実現できるベースライン IOPS|LUN|
|データ ディスクの使用済みバースト BPS クレジットの割合|はい|データ ディスクの使用済みバースト BPS クレジットの割合|Percent|Average|これまでに使用されたデータ ディスクのバースト帯域幅クレジットの割合|LUN|
|データ ディスクの使用済みバースト IO クレジットの割合|はい|データ ディスクの使用済みバースト IO クレジットの割合|Percent|Average|これまでに使用されたデータ ディスクのバースト I/O クレジットの割合|LUN|
|データ ディスク書き込みバイト数/秒|はい|データ ディスク書き込みバイト数/秒|BytesPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN|
|データ ディスク書き込み操作数/秒|はい|データ ディスク書き込み操作数/秒|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|LUN|
|Disk Read Bytes|はい|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|ディメンションなし|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|ディメンションなし|
|Disk Write Bytes|はい|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|ディメンションなし|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|ディメンションなし|
|受信フロー数|はい|受信フロー数|Count|Average|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|ディメンションなし|
|受信フローの最大作成速度|はい|受信フローの最大作成速度|CountPerSecond|Average|受信フローの最大作成速度 (VM に送信されるトラフィック)|ディメンションなし|
|Network In|はい|課金対象のネットワーク受信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから受信した課金対象バイト数 (受信トラフィック) (非推奨)|ディメンションなし|
|受信ネットワーク合計|はい|受信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|ディメンションなし|
|Network Out|はい|課金対象のネットワーク送信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから発信した課金対象バイト数 (送信トラフィック) (非推奨)|ディメンションなし|
|送信ネットワーク合計|はい|送信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|ディメンションなし|
|OS ディスク帯域幅の消費率|はい|OS ディスク帯域幅の消費率|Percent|Average|1 分あたりに消費されたオペレーティング システム ディスク帯域幅の割合|LUN|
|[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)|はい|[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)|Percent|Average|1 分あたりに消費されたオペレーティング システム ディスク I/O の割合|LUN|
|OS ディスクの最大バースト帯域幅|はい|OS ディスクの最大バースト帯域幅|Count|Average|バーストを使用して OS ディスクで実現できる 1 秒あたりの最大バイト数|LUN|
|OS ディスクの最大バースト IOPS|はい|OS ディスクの最大バースト IOPS|Count|Average|バーストを使用して OS ディスクで実現できる最大 IOPS|LUN|
|OS ディスクのキューの深さ|はい|OS ディスクのキューの深さ|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|
|OS ディスク読み取りバイト数/秒|はい|OS ディスク読み取りバイト数/秒|BytesPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|ディメンションなし|
|OS ディスク読み取り操作数/秒|はい|OS ディスク読み取り操作数/秒|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|ディメンションなし|
|OS ディスクのターゲット帯域幅|はい|OS ディスクのターゲット帯域幅|Count|Average|バーストなしで OS ディスクが実現できる 1 秒あたりのベースライン バイト数|LUN|
|OS ディスクのターゲット IOPS|はい|OS ディスクのターゲット IOPS|Count|Average|バーストなしで OS ディスクが実現できるベースライン IOPS|LUN|
|OS ディスクの使用済みバースト BPS クレジットの割合|はい|OS ディスクの使用済みバースト BPS クレジットの割合|Percent|Average|これまでに使用された OS ディスクのバースト帯域幅クレジットの割合|LUN|
|OS ディスクの使用済みバースト IO クレジットの割合|はい|OS ディスクの使用済みバースト IO クレジットの割合|Percent|Average|これまでに使用された OS ディスクのバースト I/O クレジットの割合|LUN|
|OS ディスク書き込みバイト数/秒|はい|OS ディスク書き込みバイト数/秒|BytesPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|ディメンションなし|
|OS ディスク書き込み操作数/秒|はい|OS ディスク書き込み操作数/秒|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|ディメンションなし|
|送信フロー数|はい|送信フロー数|Count|Average|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|ディメンションなし|
|送信フローの最大作成速度|はい|送信フローの最大作成速度|CountPerSecond|Average|送信フローの最大作成速度 (VM から送信されるトラフィック)|ディメンションなし|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|ディメンションなし|
|Premium データ ディスク キャッシュ読み取りヒット|はい|Premium データ ディスク キャッシュ読み取りヒット|Percent|Average|Premium データ ディスク キャッシュ読み取りヒット|LUN|
|Premium データ ディスク キャッシュ読み取りミス|はい|Premium データ ディスク キャッシュ読み取りミス|Percent|Average|Premium データ ディスク キャッシュ読み取りミス|LUN|
|Premium OS ディスク キャッシュ読み取りヒット|はい|Premium OS ディスク キャッシュ読み取りヒット|Percent|Average|Premium OS ディスク キャッシュ読み取りヒット|ディメンションなし|
|Premium OS ディスク キャッシュ読み取りミス|はい|Premium OS ディスク キャッシュ読み取りミス|Percent|Average|Premium OS ディスク キャッシュ読み取りミス|ディメンションなし|
|VM のキャッシュされた帯域幅の消費率|はい|VM のキャッシュされた帯域幅の消費率|Percent|Average|VM によって消費されたキャッシュされたディスク帯域幅の割合|ディメンションなし|
|[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)|はい|[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)|Percent|Average|VM によって消費されたキャッシュされたディスク IOPS の割合|ディメンションなし|
|VM のキャッシュされていない帯域幅の消費率|はい|VM のキャッシュされていない帯域幅の消費率|Percent|Average|VM によって消費されたキャッシュされていないディスク帯域幅の割合|ディメンションなし|
|[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)|はい|[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)|Percent|Average|VM によって消費されたキャッシュされていないディスク IOPS の割合|ディメンションなし|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|使用済みの CPU クレジット|はい|使用済みの CPU クレジット|Count|Average|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|未使用の CPU クレジット|はい|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|ディメンションなし|
|データ ディスク帯域幅の消費率|はい|データ ディスク帯域幅の消費率|Percent|Average|1 分あたりに消費されたデータ ディスク帯域幅の割合|LUN、VMName|
|[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)|はい|[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)|Percent|Average|1 分あたりに消費されたデータ ディスク I/O の割合|LUN、VMName|
|データ ディスクの最大バースト帯域幅|はい|データ ディスクの最大バースト帯域幅|Count|Average|バーストを使用してデータ ディスクで実現できる 1 秒あたりの最大バイト数|LUN、VMName|
|データ ディスクの最大バースト IOPS|はい|データ ディスクの最大バースト IOPS|Count|Average|バーストを使用してデータ ディスクで実現できる最大 IOPS|LUN、VMName|
|データ ディスクのキューの深さ|はい|データ ディスクのキューの深さ|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|LUN、VMName|
|データ ディスク読み取りバイト数/秒|はい|データ ディスク読み取りバイト数/秒|BytesPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN、VMName|
|データ ディスク読み取り操作数/秒|はい|データ ディスク読み取り操作数/秒|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|LUN、VMName|
|データ ディスクのターゲット帯域幅|はい|データ ディスクのターゲット帯域幅|Count|Average|バーストなしでデータ ディスクが実現できる 1 秒あたりのベースライン バイト数|LUN、VMName|
|データ ディスクのターゲット IOPS|はい|データ ディスクのターゲット IOPS|Count|Average|バーストなしでデータ ディスクが実現できるベースライン IOPS|LUN、VMName|
|データ ディスクの使用済みバースト BPS クレジットの割合|はい|データ ディスクの使用済みバースト BPS クレジットの割合|Percent|Average|これまでに使用されたデータ ディスクのバースト帯域幅クレジットの割合|LUN、VMName|
|データ ディスクの使用済みバースト IO クレジットの割合|はい|データ ディスクの使用済みバースト IO クレジットの割合|Percent|Average|これまでに使用されたデータ ディスクのバースト I/O クレジットの割合|LUN、VMName|
|データ ディスク書き込みバイト数/秒|はい|データ ディスク書き込みバイト数/秒|BytesPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN、VMName|
|データ ディスク書き込み操作数/秒|はい|データ ディスク書き込み操作数/秒|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|LUN、VMName|
|Disk Read Bytes|はい|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|VMName|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|VMName|
|Disk Write Bytes|はい|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|VMName|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|VMName|
|受信フロー数|はい|受信フロー数|Count|Average|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|VMName|
|受信フローの最大作成速度|はい|受信フローの最大作成速度|CountPerSecond|Average|受信フローの最大作成速度 (VM に送信されるトラフィック)|VMName|
|Network In|はい|課金対象のネットワーク受信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから受信した課金対象バイト数 (受信トラフィック) (非推奨)|VMName|
|受信ネットワーク合計|はい|受信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|VMName|
|Network Out|はい|課金対象のネットワーク送信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから発信した課金対象バイト数 (送信トラフィック) (非推奨)|VMName|
|送信ネットワーク合計|はい|送信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|VMName|
|OS ディスク帯域幅の消費率|はい|OS ディスク帯域幅の消費率|Percent|Average|1 分あたりに消費されたオペレーティング システム ディスク帯域幅の割合|LUN、VMName|
|[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)|はい|[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)|Percent|Average|1 分あたりに消費されたオペレーティング システム ディスク I/O の割合|LUN、VMName|
|OS ディスクの最大バースト帯域幅|はい|OS ディスクの最大バースト帯域幅|Count|Average|バーストを使用して OS ディスクで実現できる 1 秒あたりの最大バイト数|LUN、VMName|
|OS ディスクの最大バースト IOPS|はい|OS ディスクの最大バースト IOPS|Count|Average|バーストを使用して OS ディスクで実現できる最大 IOPS|LUN、VMName|
|OS ディスクのキューの深さ|はい|OS ディスクのキューの深さ|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|VMName|
|OS ディスク読み取りバイト数/秒|はい|OS ディスク読み取りバイト数/秒|BytesPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|VMName|
|OS ディスク読み取り操作数/秒|はい|OS ディスク読み取り操作数/秒|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|VMName|
|OS ディスクのターゲット帯域幅|はい|OS ディスクのターゲット帯域幅|Count|Average|バーストなしで OS ディスクが実現できる 1 秒あたりのベースライン バイト数|LUN、VMName|
|OS ディスクのターゲット IOPS|はい|OS ディスクのターゲット IOPS|Count|Average|バーストなしで OS ディスクが実現できるベースライン IOPS|LUN、VMName|
|OS ディスクの使用済みバースト BPS クレジットの割合|はい|OS ディスクの使用済みバースト BPS クレジットの割合|Percent|Average|これまでに使用された OS ディスクのバースト帯域幅クレジットの割合|LUN、VMName|
|OS ディスクの使用済みバースト IO クレジットの割合|はい|OS ディスクの使用済みバースト IO クレジットの割合|Percent|Average|これまでに使用された OS ディスクのバースト I/O クレジットの割合|LUN、VMName|
|OS ディスク書き込みバイト数/秒|はい|OS ディスク書き込みバイト数/秒|BytesPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|VMName|
|OS ディスク書き込み操作数/秒|はい|OS ディスク書き込み操作数/秒|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|VMName|
|送信フロー数|はい|送信フロー数|Count|Average|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|VMName|
|送信フローの最大作成速度|はい|送信フローの最大作成速度|CountPerSecond|Average|送信フローの最大作成速度 (VM から送信されるトラフィック)|VMName|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|VMName|
|Premium データ ディスク キャッシュ読み取りヒット|はい|Premium データ ディスク キャッシュ読み取りヒット|Percent|Average|Premium データ ディスク キャッシュ読み取りヒット|LUN、VMName|
|Premium データ ディスク キャッシュ読み取りミス|はい|Premium データ ディスク キャッシュ読み取りミス|Percent|Average|Premium データ ディスク キャッシュ読み取りミス|LUN、VMName|
|Premium OS ディスク キャッシュ読み取りヒット|はい|Premium OS ディスク キャッシュ読み取りヒット|Percent|Average|Premium OS ディスク キャッシュ読み取りヒット|VMName|
|Premium OS ディスク キャッシュ読み取りミス|はい|Premium OS ディスク キャッシュ読み取りミス|Percent|Average|Premium OS ディスク キャッシュ読み取りミス|VMName|
|VM のキャッシュされた帯域幅の消費率|はい|VM のキャッシュされた帯域幅の消費率|Percent|Average|VM によって消費されたキャッシュされたディスク帯域幅の割合|VMName|
|[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)|はい|[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)|Percent|Average|VM によって消費されたキャッシュされたディスク IOPS の割合|VMName|
|VM のキャッシュされていない帯域幅の消費率|はい|VM のキャッシュされていない帯域幅の消費率|Percent|Average|VM によって消費されたキャッシュされていないディスク帯域幅の割合|VMName|
|[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)|はい|[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)|Percent|Average|VM によって消費されたキャッシュされていないディスク IOPS の割合|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|使用済みの CPU クレジット|はい|使用済みの CPU クレジット|Count|Average|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|未使用の CPU クレジット|はい|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|ディメンションなし|
|データ ディスク帯域幅の消費率|はい|データ ディスク帯域幅の消費率|Percent|Average|1 分あたりに消費されたデータ ディスク帯域幅の割合|LUN|
|[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)|はい|[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)|Percent|Average|1 分あたりに消費されたデータ ディスク I/O の割合|LUN|
|データ ディスクの最大バースト帯域幅|はい|データ ディスクの最大バースト帯域幅|Count|Average|バーストを使用してデータ ディスクで実現できる 1 秒あたりの最大バイト数|LUN|
|データ ディスクの最大バースト IOPS|はい|データ ディスクの最大バースト IOPS|Count|Average|バーストを使用してデータ ディスクで実現できる最大 IOPS|LUN|
|データ ディスクのキューの深さ|はい|データ ディスクのキューの深さ|Count|Average|データ ディスクのキューの深さ (またはキューの長さ)|LUN|
|データ ディスク読み取りバイト数/秒|はい|データ ディスク読み取りバイト数/秒|BytesPerSecond|Average|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN|
|データ ディスク読み取り操作数/秒|はい|データ ディスク読み取り操作数/秒|CountPerSecond|Average|監視期間中の 1 つのディスクからの読み取り IOPS|LUN|
|データ ディスクのターゲット帯域幅|はい|データ ディスクのターゲット帯域幅|Count|Average|バーストなしでデータ ディスクが実現できる 1 秒あたりのベースライン バイト数|LUN|
|データ ディスクのターゲット IOPS|はい|データ ディスクのターゲット IOPS|Count|Average|バーストなしでデータ ディスクが実現できるベースライン IOPS|LUN|
|データ ディスクの使用済みバースト BPS クレジットの割合|はい|データ ディスクの使用済みバースト BPS クレジットの割合|Percent|Average|これまでに使用されたデータ ディスクのバースト帯域幅クレジットの割合|LUN|
|データ ディスクの使用済みバースト IO クレジットの割合|はい|データ ディスクの使用済みバースト IO クレジットの割合|Percent|Average|これまでに使用されたデータ ディスクのバースト I/O クレジットの割合|LUN|
|データ ディスク書き込みバイト数/秒|はい|データ ディスク書き込みバイト数/秒|BytesPerSecond|Average|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN|
|データ ディスク書き込み操作数/秒|はい|データ ディスク書き込み操作数/秒|CountPerSecond|Average|監視期間中の 1 つのディスクからの書き込み IOPS|LUN|
|Disk Read Bytes|はい|Disk Read Bytes|バイト|合計|監視期間中にディスクから読み取られたバイト数|ディメンションなし|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|ディスク読み取り IOPS|ディメンションなし|
|Disk Write Bytes|はい|Disk Write Bytes|バイト|合計|監視期間中にディスクに書き込まれたバイト数|ディメンションなし|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|ディスク書き込み IOPS|ディメンションなし|
|受信フロー数|はい|受信フロー数|Count|Average|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|ディメンションなし|
|受信フローの最大作成速度|はい|受信フローの最大作成速度|CountPerSecond|Average|受信フローの最大作成速度 (VM に送信されるトラフィック)|ディメンションなし|
|Network In|はい|課金対象のネットワーク受信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから受信した課金対象バイト数 (受信トラフィック) (非推奨)|ディメンションなし|
|受信ネットワーク合計|はい|受信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|ディメンションなし|
|Network Out|はい|課金対象のネットワーク送信 (非推奨)|バイト|合計|仮想マシンがすべてのネットワーク インターフェイスから発信した課金対象バイト数 (送信トラフィック) (非推奨)|ディメンションなし|
|送信ネットワーク合計|はい|送信ネットワーク合計|バイト|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|ディメンションなし|
|OS ディスク帯域幅の消費率|はい|OS ディスク帯域幅の消費率|Percent|Average|1 分あたりに消費されたオペレーティング システム ディスク帯域幅の割合|LUN|
|[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)|はい|[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)|Percent|Average|1 分あたりに消費されたオペレーティング システム ディスク I/O の割合|LUN|
|OS ディスクの最大バースト帯域幅|はい|OS ディスクの最大バースト帯域幅|Count|Average|バーストを使用して OS ディスクで実現できる 1 秒あたりの最大バイト数|LUN|
|OS ディスクの最大バースト IOPS|はい|OS ディスクの最大バースト IOPS|Count|Average|バーストを使用して OS ディスクで実現できる最大 IOPS|LUN|
|OS ディスクのキューの深さ|はい|OS ディスクのキューの深さ|Count|Average|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|
|OS ディスク読み取りバイト数/秒|はい|OS ディスク読み取りバイト数/秒|BytesPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|ディメンションなし|
|OS ディスク読み取り操作数/秒|はい|OS ディスク読み取り操作数/秒|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|ディメンションなし|
|OS ディスクのターゲット帯域幅|はい|OS ディスクのターゲット帯域幅|Count|Average|バーストなしで OS ディスクが実現できる 1 秒あたりのベースライン バイト数|LUN|
|OS ディスクのターゲット IOPS|はい|OS ディスクのターゲット IOPS|Count|Average|バーストなしで OS ディスクが実現できるベースライン IOPS|LUN|
|OS ディスクの使用済みバースト BPS クレジットの割合|はい|OS ディスクの使用済みバースト BPS クレジットの割合|Percent|Average|これまでに使用された OS ディスクのバースト帯域幅クレジットの割合|LUN|
|OS ディスクの使用済みバースト IO クレジットの割合|はい|OS ディスクの使用済みバースト IO クレジットの割合|Percent|Average|これまでに使用された OS ディスクのバースト I/O クレジットの割合|LUN|
|OS ディスク書き込みバイト数/秒|はい|OS ディスク書き込みバイト数/秒|BytesPerSecond|Average|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|ディメンションなし|
|OS ディスク書き込み操作数/秒|はい|OS ディスク書き込み操作数/秒|CountPerSecond|Average|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|ディメンションなし|
|送信フロー数|はい|送信フロー数|Count|Average|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|ディメンションなし|
|送信フローの最大作成速度|はい|送信フローの最大作成速度|CountPerSecond|Average|送信フローの最大作成速度 (VM から送信されるトラフィック)|ディメンションなし|
|Percentage CPU|はい|Percentage CPU|Percent|Average|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|ディメンションなし|
|Premium データ ディスク キャッシュ読み取りヒット|はい|Premium データ ディスク キャッシュ読み取りヒット|Percent|Average|Premium データ ディスク キャッシュ読み取りヒット|LUN|
|Premium データ ディスク キャッシュ読み取りミス|はい|Premium データ ディスク キャッシュ読み取りミス|Percent|Average|Premium データ ディスク キャッシュ読み取りミス|LUN|
|Premium OS ディスク キャッシュ読み取りヒット|はい|Premium OS ディスク キャッシュ読み取りヒット|Percent|Average|Premium OS ディスク キャッシュ読み取りヒット|ディメンションなし|
|Premium OS ディスク キャッシュ読み取りミス|はい|Premium OS ディスク キャッシュ読み取りミス|Percent|Average|Premium OS ディスク キャッシュ読み取りミス|ディメンションなし|
|VM のキャッシュされた帯域幅の消費率|はい|VM のキャッシュされた帯域幅の消費率|Percent|Average|VM によって消費されたキャッシュされたディスク帯域幅の割合|ディメンションなし|
|[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)|はい|[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)|Percent|Average|VM によって消費されたキャッシュされたディスク IOPS の割合|ディメンションなし|
|VM のキャッシュされていない帯域幅の消費率|はい|VM のキャッシュされていない帯域幅の消費率|Percent|Average|VM によって消費されたキャッシュされていないディスク帯域幅の割合|ディメンションなし|
|[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)|はい|[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)|Percent|Average|VM によって消費されたキャッシュされていないディスク IOPS の割合|ディメンションなし|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CpuUsage|はい|CPU 使用率|Count|Average|すべてのコアの CPU 使用率 (ミリコア単位)。|containerName|
|MemoryUsage|はい|メモリ使用量|バイト|Average|合計メモリ使用量 (バイト単位)。|containerName|
|NetworkBytesReceivedPerSecond|はい|1 秒あたりの受信ネットワーク バイト数|バイト|Average|1 秒あたりの受信ネットワーク バイト数。|ディメンションなし|
|NetworkBytesTransmittedPerSecond|はい|1 秒あたりの送信ネットワーク バイト数|バイト|Average|1 秒あたりの送信ネットワーク バイト数。|ディメンションなし|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|はい|AgentPool の CPU 時間|Seconds|合計|AgentPool の CPU 時間 (秒)|ディメンションなし|
|RunDuration|はい|実行継続時間|ミリ秒|合計|実行継続時間 (ミリ秒単位)|ディメンションなし|
|SuccessfulPullCount|はい|成功したプルの数|Count|Average|成功したイメージ プルの数|ディメンションなし|
|SuccessfulPushCount|はい|成功したプッシュの数|Count|Average|成功したイメージ プッシュの数|ディメンションなし|
|TotalPullCount|はい|プルの合計数|Count|Average|イメージのプルの合計数|ディメンションなし|
|TotalPushCount|はい|合計のプッシュ数|Count|Average|イメージのプッシュの合計数|ディメンションなし|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|いいえ|配信要求|Count|Average|最後の 1 秒間に要求の種類ごとに apiserver で現在使用されている配信要求の最大数|requestKind|
|kube_node_status_allocatable_cpu_cores|いいえ|マネージド クラスターで使用可能な CPU コアの合計数|Count|Average|マネージド クラスターで使用可能な CPU コアの合計数||
|kube_node_status_allocatable_memory_bytes|いいえ|マネージド クラスターで使用可能なメモリの合計量|バイト|Average|マネージド クラスターで使用可能なメモリの合計量||
|kube_node_status_condition|いいえ|さまざまなノードの状態の条件|Count|Average|さまざまなノードの状態の条件|condition、status、status2、node|
|kube_pod_status_phase|いいえ|フェーズごとのポッドの数|Count|Average|フェーズごとのポッドの数|phase、namespace、pod|
|kube_pod_status_ready|いいえ|準備完了状態のポッドの数|Count|Average|準備完了状態のポッドの数|namespace、pod、condition|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|FailedRequests|はい|失敗した要求|Count|合計|カスタム リソース プロバイダーの使用可能なログを取得します|HttpMethod、CallPath、StatusCode|
|SuccessfullRequests|はい|成功した要求|Count|合計|カスタム プロバイダーによる正常に完了した要求|HttpMethod、CallPath、StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AvailableCapacity|はい|使用可能な容量|バイト|Average|レポート期間中の使用可能な容量 (バイト単位)。|ディメンションなし|
|BytesUploadedToCloud|はい|クラウドのアップロードされたバイト数 (デバイス)|バイト|Average|レポート期間中にデバイスから Azure にアップロードされるバイトの合計数。|ディメンションなし|
|BytesUploadedToCloudPerShare|はい|クラウドのアップロードされたバイト数 (共有)|バイト|Average|レポート期間中に共有から Azure にアップロードされるバイトの合計数。|共有|
|CloudReadThroughput|はい|クラウド ダウンロード スループット|BytesPerSecond|Average|レポート期間中の Azure へのクラウド ダウンロード スループット。|ディメンションなし|
|CloudReadThroughputPerShare|はい|クラウドのダウンロード スループット (共有)|BytesPerSecond|Average|レポート期間中の共有から Azure へのダウンロード スループット。|共有|
|CloudUploadThroughput|はい|クラウド アップロード スループット|BytesPerSecond|Average|レポート期間中の Azure へのクラウド アップロード スループット。|ディメンションなし|
|CloudUploadThroughputPerShare|はい|クラウドへのアップロード時のスループット (共有)|BytesPerSecond|Average|レポート期間中の共有から Azure へのアップロード スループット。|共有|
|HyperVMemoryUtilization|はい|Edge コンピューティング - メモリ使用量|Percent|Average|使用中の RAM の容量|InstanceName|
|HyperVVirtualProcessorUtilization|はい|Edge コンピューティング - CPU 使用率|Percent|Average|CPU 使用率 (パーセント)|InstanceName|
|NICReadThroughput|はい|読み取りスループット (ネットワーク)|BytesPerSecond|Average|ゲートウェイのすべてのボリュームに対するレポート期間におけるデバイスでのネットワーク インターフェイスの読み取りスループット。|InstanceName|
|NICWriteThroughput|はい|書き込みスループット (ネットワーク)|BytesPerSecond|Average|ゲートウェイのすべてのボリュームに対するレポート期間におけるデバイスでのネットワーク インターフェイスの書き込みスループット。|InstanceName|
|TotalCapacity|はい|合計容量|バイト|Average|合計容量|ディメンションなし|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DataAssetCount|はい|作成されたデータ資産|Count|最大値|作成されたデータ資産の数|DataAssetName|
|PipelineCount|はい|作成されたパイプライン|Count|最大値|作成されたパイプラインの数|PipelineName|
|ProposalCount|はい|作成された提案|Count|最大値|作成された提案の数|ProposalName|
|ScriptCount|はい|作成されたスクリプト|Count|最大値|作成されたスクリプトの数|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|FailedRuns|はい|失敗した実行|Count|合計||pipelineName、activityName|
|SuccessfulRuns|はい|成功した実行|Count|合計||pipelineName、activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|はい|キャンセルしたアクティビティの実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|ActivityFailedRuns|はい|失敗したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|はい|成功したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|FactorySizeInGbUnits|はい|合計の工場出荷時サイズ (GB 単位)|Count|最大値||ディメンションなし|
|IntegrationRuntimeAvailableMemory|はい|統合実行時に使用可能なメモリ|バイト|Average||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableNodeNumber|はい|統合ランタイムの使用可能なノード数|Count|Average||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|はい|統合ランタイム キューの期間|Seconds|Average||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|はい|統合実行時の CPU 使用率|Percent|Average||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeQueueLength|はい|統合ランタイム キューの長さ|Count|Average||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|はい|許可されている最大の工場出荷時サイズ (GB 単位)|Count|最大値||ディメンションなし|
|MaxAllowedResourceCount|はい|許可されているエンティティの最大数|Count|最大値||ディメンションなし|
|PipelineCancelledRuns|はい|キャンセルしたパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|PipelineElapsedTimeRuns|はい|経過時間パイプライン実行メトリック|Count|合計||RunId、Name|
|PipelineFailedRuns|はい|失敗したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|PipelineSucceededRuns|はい|成功したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|ResourceCount|はい|エンティティの合計数|Count|最大値||ディメンションなし|
|SSISIntegrationRuntimeStartCancel|はい|キャンセルされた SSIS 統合ランタイム開始メトリック|Count|合計||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|はい|失敗した SSIS 統合ランタイム開始メトリック|Count|合計||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|はい|成功した SSIS 統合ランタイム開始メトリック|Count|合計||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|はい|停止した SSIS 統合ランタイム停止メトリック|Count|合計||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|はい|成功した SSIS 統合ランタイム停止メトリック|Count|合計||IntegrationRuntimeName|
|SSISPackageExecutionCancel|はい|キャンセルされた SSIS パッケージ実行回数のメトリック|Count|合計||IntegrationRuntimeName|
|SSISPackageExecutionFailed|はい|失敗した SSIS パッケージ実行回数のメトリック|Count|合計||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|はい|成功した SSIS パッケージ実行回数のメトリック|Count|合計||IntegrationRuntimeName|
|TriggerCancelledRuns|はい|キャンセルしたトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|TriggerFailedRuns|はい|失敗したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|TriggerSucceededRuns|はい|成功したトリガー実行の回数メトリック|Count|合計||Name、FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|はい|Cancelled AU Time (取り消された AU 時間)|Seconds|合計|取り消されたジョブの AU 時間の合計。|ディメンションなし|
|JobAUEndedFailure|はい|Failed AU Time (失敗した AU 時間)|Seconds|合計|失敗したジョブの AU 時間の合計。|ディメンションなし|
|JobAUEndedSuccess|はい|Successful AU Time (成功した AU 時間)|Seconds|合計|成功したジョブの AU 時間の合計。|ディメンションなし|
|JobEndedCancelled|はい|Cancelled Jobs (取り消されたジョブ)|Count|合計|取り消されたジョブの数。|ディメンションなし|
|JobEndedFailure|はい|Failed Jobs (失敗したジョブ)|Count|合計|失敗したジョブの数。|ディメンションなし|
|JobEndedSuccess|はい|Successful Jobs (成功したジョブ)|Count|合計|成功したジョブの数。|ディメンションなし|
|JobStage|はい|Jobs in Stage (ステージのジョブ数)|Count|合計|各ステージでのジョブの数。|ディメンションなし|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DataRead|はい|データ読み取り量|バイト|合計|アカウントから読み取られたデータの総量。|ディメンションなし|
|DataWritten|はい|データ書き込み量|バイト|合計|アカウントに書き込まれたデータの総量。|ディメンションなし|
|ReadRequests|はい|読み取り要求|Count|合計|アカウントへのデータ読み取り要求の数。|ディメンションなし|
|TotalStorage|はい|保存量の合計|バイト|最大値|アカウントに保存されたデータの総量。|ディメンションなし|
|WriteRequests|はい|書き込み要求|Count|合計|アカウントへのデータ書き込み要求の数。|ディメンションなし|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|はい|Received Share Failed Snapshots (受信した共有の失敗したスナップショット)|Count|Count|アカウント内の受信した共有の失敗したスナップショットの数|ディメンションなし|
|FailedShareSynchronizations|はい|Sent Share Failed Snapshots (送信された共有の失敗したスナップショット)|Count|Count|アカウント内の送信された共有の失敗したスナップショットの数|ディメンションなし|
|ShareCount|はい|Sent Shares (送信された共有)|Count|最大値|アカウント内で送信された共有の数|ShareName|
|ShareSubscriptionCount|はい|Received Shares (受信した共有)|Count|最大値|アカウント内で受信した共有の数|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|はい|Received Share Succeeded Snapshots (受信した共有の成功したスナップショット)|Count|Count|アカウント内の受信した共有の成功したスナップショットの数|ディメンションなし|
|SucceededShareSynchronizations|はい|Sent Share Succeeded Snapshots (送信された共有の成功したスナップショット)|Count|Count|アカウント内の送信された共有の成功したスナップショットの数|ディメンションなし|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active_connections|はい|アクティブな接続|Count|Average|アクティブな接続|ディメンションなし|
|backup_storage_used|はい|使用されたバックアップ ストレージ|バイト|Average|使用されたバックアップ ストレージ|ディメンションなし|
|connections_failed|はい|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|cpu_percent|はい|CPU 使用率|Percent|Average|CPU 使用率|ディメンションなし|
|io_consumption_percent|はい|IO の割合|Percent|Average|IO の割合|ディメンションなし|
|memory_percent|はい|メモリの割合|Percent|Average|メモリの割合|ディメンションなし|
|network_bytes_egress|はい|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|はい|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|
|seconds_behind_master|はい|レプリケーションのラグ (秒単位)|Count|最大値|レプリケーションのラグ (秒単位)|ディメンションなし|
|serverlog_storage_limit|はい|サーバー ログ ストレージの上限|バイト|最大値|サーバー ログ ストレージの上限|ディメンションなし|
|serverlog_storage_percent|はい|サーバー ログ ストレージの割合|Percent|Average|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|はい|サーバー ログ ストレージの使用量|バイト|Average|サーバー ログ ストレージの使用量|ディメンションなし|
|storage_limit|はい|ストレージの制限|バイト|最大値|ストレージの制限|ディメンションなし|
|storage_percent|はい|ストレージの割合|Percent|Average|ストレージの割合|ディメンションなし|
|storage_used|はい|使用済みストレージ|バイト|Average|使用済みストレージ|ディメンションなし|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|aborted_connections|はい|中止された接続|Count|合計|中止された接続|ディメンションなし|
|active_connections|はい|アクティブな接続|Count|最大値|アクティブな接続|ディメンションなし|
|backup_storage_used|はい|使用済みバックアップ ストレージ|バイト|最大値|使用済みバックアップ ストレージ|ディメンションなし|
|cpu_percent|はい|ホストの CPU 使用率|Percent|最大値|ホストの CPU 使用率|ディメンションなし|
|io_consumption_percent|はい|IO の割合|Percent|最大値|IO の割合|ディメンションなし|
|memory_percent|はい|ホストのメモリ使用率|Percent|最大値|ホストのメモリ使用率|ディメンションなし|
|network_bytes_egress|はい|ホストのネットワーク送信|バイト|合計|ホストのネットワーク送信 (バイト単位)|ディメンションなし|
|network_bytes_ingress|はい|ホストのネットワーク受信|バイト|合計|ホストのネットワーク受信 (バイト単位)|ディメンションなし|
|クエリ|はい|クエリ|Count|合計|クエリ|ディメンションなし|
|replication_lag|はい|レプリケーションのラグ (秒単位)|Seconds|最大値|レプリケーションのラグ (秒単位)|ディメンションなし|
|storage_limit|はい|ストレージの制限|バイト|最大値|ストレージの制限|ディメンションなし|
|storage_percent|はい|ストレージの割合|Percent|最大値|ストレージの割合|ディメンションなし|
|storage_used|はい|使用されているストレージ|バイト|最大値|使用されているストレージ|ディメンションなし|
|total_connections|はい|合計接続数|Count|合計|合計接続数|ディメンションなし|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active_connections|はい|アクティブな接続|Count|Average|アクティブな接続|ディメンションなし|
|backup_storage_used|はい|使用されたバックアップ ストレージ|バイト|Average|使用されたバックアップ ストレージ|ディメンションなし|
|connections_failed|はい|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|cpu_percent|はい|CPU 使用率|Percent|Average|CPU 使用率|ディメンションなし|
|io_consumption_percent|はい|IO の割合|Percent|Average|IO の割合|ディメンションなし|
|memory_percent|はい|メモリの割合|Percent|Average|メモリの割合|ディメンションなし|
|network_bytes_egress|はい|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|はい|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|
|seconds_behind_master|はい|レプリケーションのラグ (秒単位)|Count|最大値|レプリケーションのラグ (秒単位)|ディメンションなし|
|serverlog_storage_limit|はい|サーバー ログ ストレージの上限|バイト|最大値|サーバー ログ ストレージの上限|ディメンションなし|
|serverlog_storage_percent|はい|サーバー ログ ストレージの割合|Percent|Average|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|はい|サーバー ログ ストレージの使用量|バイト|Average|サーバー ログ ストレージの使用量|ディメンションなし|
|storage_limit|はい|ストレージの制限|バイト|最大値|ストレージの制限|ディメンションなし|
|storage_percent|はい|ストレージの割合|Percent|Average|ストレージの割合|ディメンションなし|
|storage_used|はい|使用済みストレージ|バイト|Average|使用済みストレージ|ディメンションなし|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active_connections|はい|アクティブな接続|Count|Average|アクティブな接続|ディメンションなし|
|backup_storage_used|はい|使用済みバックアップ ストレージ|バイト|Average|使用済みバックアップ ストレージ|ディメンションなし|
|connections_failed|はい|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|connections_succeeded|はい|Succeeded Connections (成功した接続)|Count|合計|成功した接続|ディメンションなし|
|cpu_credits_consumed|はい|使用済みの CPU クレジット|Count|Average|データベース サーバーによって消費されたクレジットの合計数|ディメンションなし|
|cpu_credits_remaining|はい|未使用の CPU クレジット|Count|Average|バーストに使用できるクレジットの合計|ディメンションなし|
|cpu_percent|はい|CPU 使用率|Percent|Average|CPU 使用率|ディメンションなし|
|disk_queue_depth|はい|ディスクのキューの深さ|Count|Average|データ ディスクに対する未処理の I/O 操作の数|ディメンションなし|
|iops|はい|IOPS|Count|Average|1 秒あたりの IO 操作回数|ディメンションなし|
|maximum_used_transactionIDs|はい|Maximum Used Transaction IDs (使用されるトランザクション ID の最大数)|Count|Average|使用されるトランザクション ID の最大数|ディメンションなし|
|memory_percent|はい|メモリの割合|Percent|Average|メモリの割合|ディメンションなし|
|network_bytes_egress|はい|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|はい|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|
|read_iops|はい|読み取り IOPS|Count|Average|データ ディスク I/O の 1 秒あたりの読み取り操作の数|ディメンションなし|
|read_throughput|はい|読み取りスループット (バイト/秒)|Count|Average|監視期間中のデータ ディスクからの 1 秒あたりの読み取りバイト数|ディメンションなし|
|storage_free|はい|空き記憶域|バイト|Average|空き記憶域|ディメンションなし|
|storage_percent|はい|ストレージの割合|Percent|Average|ストレージの割合|ディメンションなし|
|storage_used|はい|使用済みストレージ|バイト|Average|使用済みストレージ|ディメンションなし|
|txlogs_storage_used|はい|使用されているトランザクション ログ ストレージ|バイト|Average|使用されているトランザクション ログ ストレージ|ディメンションなし|
|write_iops|はい|書き込み IOPS|Count|Average|データ ディスク I/O の 1 秒あたりの書き込み操作の数|ディメンションなし|
|write_throughput|はい|書き込みスループット (バイト/秒)|Count|Average|監視期間中のデータ ディスクへの 1 秒あたりの書き込みバイト数|ディメンションなし|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active_connections|はい|アクティブな接続|Count|Average|アクティブな接続|ディメンションなし|
|backup_storage_used|はい|使用済みバックアップ ストレージ|バイト|Average|使用済みバックアップ ストレージ|ディメンションなし|
|connections_failed|はい|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|cpu_percent|はい|CPU 使用率|Percent|Average|CPU 使用率|ディメンションなし|
|io_consumption_percent|はい|IO の割合|Percent|Average|IO の割合|ディメンションなし|
|memory_percent|はい|メモリの割合|Percent|Average|メモリの割合|ディメンションなし|
|network_bytes_egress|はい|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|はい|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|
|pg_replica_log_delay_in_bytes|はい|Max Lag Across Replicas (レプリカ間の最大ラグ)|バイト|最大値|最も遅れているレプリカのラグ (バイト単位)|ディメンションなし|
|pg_replica_log_delay_in_seconds|はい|Replica Lag (レプリカ ラグ)|Seconds|最大値|レプリカのラグ (秒単位)|ディメンションなし|
|serverlog_storage_limit|はい|サーバー ログ ストレージの上限|バイト|最大値|サーバー ログ ストレージの上限|ディメンションなし|
|serverlog_storage_percent|はい|サーバー ログ ストレージの割合|Percent|Average|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|はい|サーバー ログ ストレージの使用量|バイト|Average|サーバー ログ ストレージの使用量|ディメンションなし|
|storage_limit|はい|ストレージの制限|バイト|最大値|ストレージの制限|ディメンションなし|
|storage_percent|はい|ストレージの割合|Percent|Average|ストレージの割合|ディメンションなし|
|storage_used|はい|使用済みストレージ|バイト|Average|使用済みストレージ|ディメンションなし|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active_connections|はい|アクティブな接続|Count|Average|アクティブな接続|ディメンションなし|
|cpu_percent|はい|CPU 使用率|Percent|Average|CPU 使用率|ディメンションなし|
|iops|はい|IOPS|Count|Average|1 秒あたりの IO 操作回数|ディメンションなし|
|memory_percent|はい|メモリの割合|Percent|Average|メモリの割合|ディメンションなし|
|network_bytes_egress|はい|Network Out|バイト|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|はい|Network In|バイト|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|
|storage_percent|はい|ストレージの割合|Percent|Average|ストレージの割合|ディメンションなし|
|storage_used|はい|使用済みストレージ|バイト|Average|使用済みストレージ|ディメンションなし|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|はい|要求された使用率|Percent|Average|要求された使用率|ディメンションなし|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|はい|破棄した C2D メッセージ|Count|合計|デバイスで中止された Cloud to Device メッセージの数|ディメンションなし|
|c2d.commands.egress.complete.success|はい|C2D メッセージ配信完了|Count|合計|デバイスで正常に完了した Cloud to Device メッセージ配信の数|ディメンションなし|
|c2d.commands.egress.reject.success|はい|C2D メッセージ拒否数|Count|合計|デバイスで拒否された Cloud to Device メッセージの数|ディメンションなし|
|c2d.methods.failure|はい|失敗したダイレクト メソッドの呼び出し|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.methods.requestSize|はい|ダイレクト メソッドの呼び出しの要求サイズ|バイト|Average|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.responseSize|はい|ダイレクト メソッドの呼び出しの応答サイズ|バイト|Average|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.success|はい|成功したダイレクト メソッドの呼び出し|Count|合計|成功したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.twin.read.failure|はい|失敗したバックエンドからのツイン読み取り|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|ディメンションなし|
|c2d.twin.read.size|はい|バック エンドからのツイン読み取りの応答サイズ|バイト|Average|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.read.success|はい|成功したバック エンドからのツイン読み取り|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|ディメンションなし|
|c2d.twin.update.failure|はい|失敗したバック エンドからのツイン更新|Count|合計|バックエンドが開始して失敗したツイン更新の数。|ディメンションなし|
|c2d.twin.update.size|はい|バックエンドからのツイン更新のサイズ|バイト|Average|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.update.success|はい|成功したバックエンドからのツイン更新|Count|合計|バックエンドが開始して成功したツイン更新の数。|ディメンションなし|
|C2DMessagesExpired|はい|期限切れ C2D メッセージ|Count|合計|有効期限が切れた cloud-to-device メッセージの数|ディメンションなし|
|configuration|はい|構成メトリック|Count|合計|構成操作のメトリック|ディメンションなし|
|connectedDeviceCount|はい|接続されたデバイス|Count|Average|IoT Hub に接続されているデバイスの数|ディメンションなし|
|d2c.endpoints.egress.builtIn.events|はい|ルーティング: メッセージ/イベントに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが組み込みのエンドポイント (メッセージ/イベント) に正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.eventHubs|はい|ルーティング: Event Hub に配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Event Hub エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.serviceBusQueues|はい|ルーティング: Service Bus キューに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus キュー エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.serviceBusTopics|はい|ルーティング: Service Bus トピックに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus トピック エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.storage|はい|ルーティング: ストレージに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージがストレージ エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.storage.blobs|はい|ルーティング: ストレージに配信された BLOB|Count|合計|IoT Hub ルーティングで BLOB がストレージ エンドポイントに配信された回数。|ディメンションなし|
|d2c.endpoints.egress.storage.bytes|はい|ルーティング: ストレージに配信されたデータ|バイト|合計|IoT Hub ルーティングでストレージ エンドポイントに配信されたデータの量 (バイト)。|ディメンションなし|
|d2c.endpoints.latency.builtIn.events|はい|ルーティング: メッセージ/イベントのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.eventHubs|はい|ルーティング: Event Hub のメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Event Hub エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.serviceBusQueues|はい|ルーティング: Service Bus キューのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus キュー エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.serviceBusTopics|はい|ルーティング: Service Bus トピックのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus トピック エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.storage|はい|ルーティング: ストレージのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージとストレージ エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.telemetry.egress.dropped|はい|ルーティング: 破棄されたテレメトリ メッセージ |Count|合計|デッド エンドポイントであるため、メッセージが IoT Hub ルーティングで破棄された回数。 この値では、フォールバック ルートに配信されるメッセージはカウントされません。破棄されたメッセージはそこには配信されないためです。|ディメンションなし|
|d2c.telemetry.egress.fallback|はい|ルーティング: フォールバックのために配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、フォールバック ルートに関連付けられているエンドポイントにメッセージが配信された回数。|ディメンションなし|
|d2c.telemetry.egress.invalid|はい|ルーティング: 互換性のないテレメトリ メッセージ|Count|合計|エンドポイントと互換性がないため、IoT Hub ルーティングでメッセージを配信できなかった回数。 この値に再試行回数は含まれません。|ディメンションなし|
|d2c.telemetry.egress.orphaned|はい|ルーティング: 孤立したテレメトリ メッセージ |Count|合計|どのルーティング規則 (フォールバック規則を含む) にも一致しなかっため、メッセージが IoT Hub ルーティングによって孤立した回数。 |ディメンションなし|
|d2c.telemetry.egress.success|はい|ルーティング: 配信されたテレメトリ メッセージ|Count|合計|IoT Hub ルーティングを使用して、メッセージがすべてのエンドポイントに正常に配信された回数。 メッセージが複数のエンドポイントにルーティングされている場合、この値は正常に配信されるたびに 1 つずつ増えます。 メッセージが同じエンドポイントに複数回配信されている場合、この値は正常に配信されるたびに 1 つずつ増えます。|ディメンションなし|
|d2c.telemetry.ingress.allProtocol|はい|テレメトリ メッセージ送信試行|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|d2c.telemetry.ingress.sendThrottle|はい|調整エラーの数|Count|合計|デバイスのスループット調整による調整エラーの数|ディメンションなし|
|d2c.telemetry.ingress.success|はい|送信済みテレメトリ メッセージ|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|d2c.twin.read.failure|はい|失敗したデバイスからのツイン読み取り|Count|合計|デバイスが開始したツイン読み取りの失敗数。|ディメンションなし|
|d2c.twin.read.size|はい|デバイスからのツイン読み取りの応答サイズ|バイト|Average|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|d2c.twin.read.success|はい|成功したデバイスからのツイン読み取り|Count|合計|デバイスが開始して成功したツイン読み取りの数。|ディメンションなし|
|d2c.twin.update.failure|はい|失敗したデバイスからのツイン更新|Count|合計|デバイスが開始して失敗したツイン更新の数。|ディメンションなし|
|d2c.twin.update.size|はい|デバイスからのツイン更新のサイズ|バイト|Average|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|d2c.twin.update.success|はい|成功したデバイスからのツイン更新|Count|合計|デバイスが開始して成功したツイン更新の数。|ディメンションなし|
|dailyMessageQuotaUsed|はい|使用されているメッセージの合計数|Count|最大値|現在使用されているメッセージの合計数|ディメンションなし|
|deviceDataUsage|はい|デバイス データの合計使用量|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|ディメンションなし|
|deviceDataUsageV2|はい|デバイス データの合計使用量 (プレビュー)|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|ディメンションなし|
|devices.connectedDevices.allProtocol|はい|接続されているデバイス (非推奨) |Count|合計|IoT Hub に接続されているデバイスの数|ディメンションなし|
|devices.totalDevices|はい|デバイスの総数 (非推奨)|Count|合計|IoT Hub に登録されたデバイスの数|ディメンションなし|
|EventGridDeliveries|はい|Event Grid 配信|Count|合計|Event Grid に発行された IoT Hub イベントの数。 成功および失敗した要求の数には、Result ディメンションを使用します。 EventType ディメンションはイベントの種類 (https://aka.ms/ioteventgrid) ) を示します。|Result、EventType|
|EventGridLatency|はい|Event Grid の待機時間|ミリ秒|Average|oT Hub イベントが生成されてから、そのイベントが Event Grid に発行されるまでの平均待機時間 (ミリ秒)。 この数は、すべてのイベントの種類の間の平均値です。 特定の種類のイベントの待機時間を確認するには、EventType ディメンションを使用します。|EventType|
|jobs.cancelJob.failure|はい|失敗したジョブの取り消し|Count|合計|失敗したジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.cancelJob.success|はい|成功したジョブの取り消し|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.completed|はい|完了したジョブ|Count|合計|完了したジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.failure|はい|失敗したメソッド呼び出しジョブの作成|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.success|はい|成功したメソッド呼び出しジョブの作成|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.createTwinUpdateJob.failure|はい|失敗したツイン更新ジョブの作成|Count|合計|作成に失敗したツイン更新ジョブの数。|ディメンションなし|
|jobs.createTwinUpdateJob.success|はい|成功したツイン更新ジョブの作成|Count|合計|正常に作成されたツイン更新ジョブの数。|ディメンションなし|
|jobs.failed|はい|失敗したジョブ|Count|合計|失敗したジョブの数。|ディメンションなし|
|jobs.listJobs.failure|はい|失敗したジョブ一覧の呼び出し|Count|合計|失敗したジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.listJobs.success|はい|成功したジョブ一覧の呼び出し|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.queryJobs.failure|はい|失敗したジョブ クエリ|Count|合計|失敗したジョブ クエリの呼び出しの数。|ディメンションなし|
|jobs.queryJobs.success|はい|成功したジョブ クエリ|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|ディメンションなし|
|RoutingDataSizeInBytesDelivered|はい|Routing Delivery Message Size in Bytes (preview)\(バイト単位でのルーティング配信のメッセージ サイズ (プレビュー)\)|バイト|合計|IoT ハブによってエンドポイントに配信されるメッセージの合計サイズ (バイト単位)。 EndpointName と EndpointType ディメンションを使用して、さまざまなエンドポイントに配信されるメッセージのサイズ (バイト単位) を表示することができます。 メッセージが複数のエンドポイントに配信される場合、またはメッセージが同じエンドポイントに複数回配信される場合など、配信されるメッセージごとにメトリック値が増加します。|EndpointType、EndpointName、RoutingSource|
|RoutingDeliveries|はい|Routing Deliveries (preview) (ルーティング配信数 (プレビュー))|Count|合計|IoT Hub がルーティングを使用して、すべてのエンドポイントにメッセージを配信しようとした回数。 成功および失敗の試行回数を確認するには、Result ディメンションを使用します。 無効、ドロップ、孤立など、失敗の原因を確認するには、FailureReasonCategory ディメンションを使用します。 また、EndpointName と EndpointType ディメンションを使用して、さまざまなエンドポイントに配信されたメッセージの数を把握することもできます。 メッセージが複数のエンドポイントに配信されるかどうか、またはメッセージが同じエンドポイントに複数回配信されるかどうかなど、配信の試行ごとにメトリック値が 1 ずつ増加します。|EndpointType、EndpointName、FailureReasonCategory、Result、RoutingSource|
|RoutingDeliveryLatency|はい|Routing Delivery Latency (preview) (ルーティング配信の遅延 (プレビュー))|ミリ秒|Average|IoT Hub の受信メッセージとエンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。 EndpointName と EndpointType ディメンションを使用すると、さまざまなエンドポイントに対する待機時間を把握できます。|EndpointType、EndpointName、RoutingSource|
|tenantHub.requestedUsageRate|はい|要求された使用率|Percent|Average|要求された使用率|ディメンションなし|
|totalDeviceCount|はい|合計デバイス|Count|Average|IoT Hub に登録されたデバイスの数|ディメンションなし|
|twinQueries.failure|はい|失敗したツイン クエリ|Count|合計|失敗したツイン クエリの数。|ディメンションなし|
|twinQueries.resultSize|はい|ツイン クエリの結果のサイズ|バイト|Average|成功したツイン クエリの結果の平均、最小、および最大サイズ。|ディメンションなし|
|twinQueries.success|はい|成功したツイン クエリ|Count|合計|成功したツイン クエリの数。|ディメンションなし|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|はい|破棄した C2D メッセージ|Count|合計|デバイスで中止された Cloud to Device メッセージの数|ディメンションなし|
|c2d.commands.egress.complete.success|はい|C2D メッセージ配信完了|Count|合計|デバイスで正常に完了した Cloud to Device メッセージ配信の数|ディメンションなし|
|c2d.commands.egress.reject.success|はい|C2D メッセージ拒否数|Count|合計|デバイスで拒否された Cloud to Device メッセージの数|ディメンションなし|
|c2d.methods.failure|はい|失敗したダイレクト メソッドの呼び出し|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.methods.requestSize|はい|ダイレクト メソッドの呼び出しの要求サイズ|バイト|Average|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.responseSize|はい|ダイレクト メソッドの呼び出しの応答サイズ|バイト|Average|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.success|はい|成功したダイレクト メソッドの呼び出し|Count|合計|成功したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.twin.read.failure|はい|失敗したバックエンドからのツイン読み取り|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|ディメンションなし|
|c2d.twin.read.size|はい|バック エンドからのツイン読み取りの応答サイズ|バイト|Average|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.read.success|はい|成功したバック エンドからのツイン読み取り|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|ディメンションなし|
|c2d.twin.update.failure|はい|失敗したバック エンドからのツイン更新|Count|合計|バックエンドが開始して失敗したツイン更新の数。|ディメンションなし|
|c2d.twin.update.size|はい|バックエンドからのツイン更新のサイズ|バイト|Average|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.update.success|はい|成功したバックエンドからのツイン更新|Count|合計|バックエンドが開始して成功したツイン更新の数。|ディメンションなし|
|C2DMessagesExpired|はい|期限切れ C2D メッセージ|Count|合計|有効期限が切れた cloud-to-device メッセージの数|ディメンションなし|
|configuration|はい|構成メトリック|Count|合計|構成操作のメトリック|ディメンションなし|
|connectedDeviceCount|いいえ|接続されたデバイス|Count|Average|IoT Hub に接続されているデバイスの数|ディメンションなし|
|d2c.endpoints.egress.builtIn.events|はい|ルーティング: メッセージ/イベントに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが組み込みのエンドポイント (メッセージ/イベント) に正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.eventHubs|はい|ルーティング: Event Hub に配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Event Hub エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.serviceBusQueues|はい|ルーティング: Service Bus キューに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus キュー エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.serviceBusTopics|はい|ルーティング: Service Bus トピックに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus トピック エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.storage|はい|ルーティング: ストレージに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージがストレージ エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.egress.storage.blobs|はい|ルーティング: ストレージに配信された BLOB|Count|合計|IoT Hub ルーティングで BLOB がストレージ エンドポイントに配信された回数。|ディメンションなし|
|d2c.endpoints.egress.storage.bytes|はい|ルーティング: ストレージに配信されたデータ|バイト|合計|IoT Hub ルーティングでストレージ エンドポイントに配信されたデータの量 (バイト)。|ディメンションなし|
|d2c.endpoints.latency.builtIn.events|はい|ルーティング: メッセージ/イベントのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.eventHubs|はい|ルーティング: Event Hub のメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Event Hub エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.serviceBusQueues|はい|ルーティング: Service Bus キューのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus キュー エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.serviceBusTopics|はい|ルーティング: Service Bus トピックのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus トピック エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.latency.storage|はい|ルーティング: ストレージのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージとストレージ エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.telemetry.egress.dropped|はい|ルーティング: 破棄されたテレメトリ メッセージ |Count|合計|デッド エンドポイントであるため、メッセージが IoT Hub ルーティングで破棄された回数。 この値では、フォールバック ルートに配信されるメッセージはカウントされません。破棄されたメッセージはそこには配信されないためです。|ディメンションなし|
|d2c.telemetry.egress.fallback|はい|ルーティング: フォールバックのために配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、フォールバック ルートに関連付けられているエンドポイントにメッセージが配信された回数。|ディメンションなし|
|d2c.telemetry.egress.invalid|はい|ルーティング: 互換性のないテレメトリ メッセージ|Count|合計|エンドポイントと互換性がないため、IoT Hub ルーティングでメッセージを配信できなかった回数。 この値に再試行回数は含まれません。|ディメンションなし|
|d2c.telemetry.egress.orphaned|はい|ルーティング: 孤立したテレメトリ メッセージ |Count|合計|どのルーティング規則 (フォールバック規則を含む) にも一致しなかっため、メッセージが IoT Hub ルーティングによって孤立した回数。 |ディメンションなし|
|d2c.telemetry.egress.success|はい|ルーティング: 配信されたテレメトリ メッセージ|Count|合計|IoT Hub ルーティングを使用して、メッセージがすべてのエンドポイントに正常に配信された回数。 メッセージが複数のエンドポイントにルーティングされている場合、この値は正常に配信されるたびに 1 つずつ増えます。 メッセージが同じエンドポイントに複数回配信されている場合、この値は正常に配信されるたびに 1 つずつ増えます。|ディメンションなし|
|d2c.telemetry.ingress.allProtocol|はい|テレメトリ メッセージ送信試行|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|d2c.telemetry.ingress.sendThrottle|はい|調整エラーの数|Count|合計|デバイスのスループット調整による調整エラーの数|ディメンションなし|
|d2c.telemetry.ingress.success|はい|送信済みテレメトリ メッセージ|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|d2c.twin.read.failure|はい|失敗したデバイスからのツイン読み取り|Count|合計|デバイスが開始したツイン読み取りの失敗数。|ディメンションなし|
|d2c.twin.read.size|はい|デバイスからのツイン読み取りの応答サイズ|バイト|Average|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|d2c.twin.read.success|はい|成功したデバイスからのツイン読み取り|Count|合計|デバイスが開始して成功したツイン読み取りの数。|ディメンションなし|
|d2c.twin.update.failure|はい|失敗したデバイスからのツイン更新|Count|合計|デバイスが開始して失敗したツイン更新の数。|ディメンションなし|
|d2c.twin.update.size|はい|デバイスからのツイン更新のサイズ|バイト|Average|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|d2c.twin.update.success|はい|成功したデバイスからのツイン更新|Count|合計|デバイスが開始して成功したツイン更新の数。|ディメンションなし|
|dailyMessageQuotaUsed|はい|使用されているメッセージの合計数|Count|最大値|現在使用されているメッセージの合計数|ディメンションなし|
|deviceDataUsage|はい|デバイス データの合計使用量|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|ディメンションなし|
|deviceDataUsageV2|はい|デバイス データの合計使用量 (プレビュー)|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|ディメンションなし|
|devices.connectedDevices.allProtocol|はい|接続されているデバイス (非推奨) |Count|合計|IoT Hub に接続されているデバイスの数|ディメンションなし|
|devices.totalDevices|はい|デバイスの総数 (非推奨)|Count|合計|IoT Hub に登録されたデバイスの数|ディメンションなし|
|EventGridDeliveries|はい|Event Grid 配信|Count|合計|Event Grid に発行された IoT Hub イベントの数。 成功および失敗した要求の数には、Result ディメンションを使用します。 EventType ディメンションはイベントの種類 (https://aka.ms/ioteventgrid) ) を示します。|Result、EventType|
|EventGridLatency|はい|Event Grid の待機時間|ミリ秒|Average|oT Hub イベントが生成されてから、そのイベントが Event Grid に発行されるまでの平均待機時間 (ミリ秒)。 この数は、すべてのイベントの種類の間の平均値です。 特定の種類のイベントの待機時間を確認するには、EventType ディメンションを使用します。|EventType|
|jobs.cancelJob.failure|はい|失敗したジョブの取り消し|Count|合計|失敗したジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.cancelJob.success|はい|成功したジョブの取り消し|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.completed|はい|完了したジョブ|Count|合計|完了したジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.failure|はい|失敗したメソッド呼び出しジョブの作成|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.success|はい|成功したメソッド呼び出しジョブの作成|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.createTwinUpdateJob.failure|はい|失敗したツイン更新ジョブの作成|Count|合計|作成に失敗したツイン更新ジョブの数。|ディメンションなし|
|jobs.createTwinUpdateJob.success|はい|成功したツイン更新ジョブの作成|Count|合計|正常に作成されたツイン更新ジョブの数。|ディメンションなし|
|jobs.failed|はい|失敗したジョブ|Count|合計|失敗したジョブの数。|ディメンションなし|
|jobs.listJobs.failure|はい|失敗したジョブ一覧の呼び出し|Count|合計|失敗したジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.listJobs.success|はい|成功したジョブ一覧の呼び出し|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.queryJobs.failure|はい|失敗したジョブ クエリ|Count|合計|失敗したジョブ クエリの呼び出しの数。|ディメンションなし|
|jobs.queryJobs.success|はい|成功したジョブ クエリ|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|ディメンションなし|
|RoutingDataSizeInBytesDelivered|はい|Routing Delivery Message Size in Bytes (preview)\(バイト単位でのルーティング配信のメッセージ サイズ (プレビュー)\)|バイト|合計|IoT ハブによってエンドポイントに配信されるメッセージの合計サイズ (バイト単位)。 EndpointName と EndpointType ディメンションを使用して、さまざまなエンドポイントに配信されるメッセージのサイズ (バイト単位) を表示することができます。 メッセージが複数のエンドポイントに配信される場合、またはメッセージが同じエンドポイントに複数回配信される場合など、配信されるメッセージごとにメトリック値が増加します。|EndpointType、EndpointName、RoutingSource|
|RoutingDeliveries|はい|Routing Deliveries (preview) (ルーティング配信数 (プレビュー))|Count|合計|IoT Hub がルーティングを使用して、すべてのエンドポイントにメッセージを配信しようとした回数。 成功および失敗の試行回数を確認するには、Result ディメンションを使用します。 無効、ドロップ、孤立など、失敗の原因を確認するには、FailureReasonCategory ディメンションを使用します。 また、EndpointName と EndpointType ディメンションを使用して、さまざまなエンドポイントに配信されたメッセージの数を把握することもできます。 メッセージが複数のエンドポイントに配信されるかどうか、またはメッセージが同じエンドポイントに複数回配信されるかどうかなど、配信の試行ごとにメトリック値が 1 ずつ増加します。|EndpointType、EndpointName、FailureReasonCategory、Result、RoutingSource|
|RoutingDeliveryLatency|はい|Routing Delivery Latency (preview) (ルーティング配信の遅延 (プレビュー))|ミリ秒|Average|IoT Hub の受信メッセージとエンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。 EndpointName と EndpointType ディメンションを使用すると、さまざまなエンドポイントに対する待機時間を把握できます。|EndpointType、EndpointName、RoutingSource|
|totalDeviceCount|いいえ|合計デバイス|Count|Average|IoT Hub に登録されたデバイスの数|ディメンションなし|
|twinQueries.failure|はい|失敗したツイン クエリ|Count|合計|失敗したツイン クエリの数。|ディメンションなし|
|twinQueries.resultSize|はい|ツイン クエリの結果のサイズ|バイト|Average|成功したツイン クエリの結果の平均、最小、および最大サイズ。|ディメンションなし|
|twinQueries.success|はい|成功したツイン クエリ|Count|合計|成功したツイン クエリの数。|ディメンションなし|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AttestationAttempts|はい|Attestation attempts (構成証明試行回数)|Count|合計|試行されたデバイス構成証明の回数|ProvisioningServiceName、Status、Protocol|
|DeviceAssignments|はい|Devices assigned (割り当て済みデバイス数)|Count|合計|IoT Hub に割り当てられているデバイスの数|ProvisioningServiceName、IotHubName|
|RegistrationAttempts|はい|Registration attempts (登録試行回数)|Count|合計|試行されたデバイス登録の回数|ProvisioningServiceName、IotHubName、Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ApiRequests|はい|API 要求|Count|合計|Digital Twins の読み取り、書き込み、削除、およびクエリ操作に対して行われた API 要求の数。|Operation、Authentication、Protocol、StatusCode、StatusCodeClass、StatusText|
|ApiRequestsFailureRate|はい|API Requests Failure Rate (API 要求の失敗率)|Percent|Average|インスタンスに対してサービスが受信する API 要求のうち、Digital Twins の読み取り、書き込み、削除、クエリ操作に対して内部エラー (500) 応答コードを返す API 要求の割合。|Operation、Authentication、Protocol|
|ApiRequestsLatency|はい|API Requests Latency (API 要求の待機時間)|ミリ秒|Average|API 要求の応答時間。つまり、Azure Digital Twins が要求を受け取ってから、サービスが Digital Twins の読み取り、書き込み、削除、クエリ操作の成功/失敗の結果を送信するまでの時間です。|Operation、Authentication、Protocol、StatusCode、StatusCodeClass、StatusText|
|BillingApiOperations|はい|Billing API Operations (課金 API 操作)|Count|合計|Azure Digital Twins サービスに対して行われたすべての API 要求の数の課金メトリック。|MeterId|
|BillingMessagesProcessed|はい|Billing Messages Processed (処理されたメッセージの課金)|Count|合計|Azure Digital Twins から外部エンドポイントに送信されたメッセージ数の課金メトリック。|MeterId|
|BillingQueryUnits|はい|Billing Query Units (課金クエリ単位)|Count|合計|クエリを実行するために消費された、内部で計算されたサービス リソース使用量のメジャーであるクエリ単位の数。|MeterId|
|IngressEvents|はい|Ingress Events (イングレス イベント)|Count|合計|Azure Digital Twins の受信テレメトリ イベントの数。|結果|
|IngressEventsFailureRate|はい|Ingress Events Failure Rate (イングレス イベント エラー率)|Percent|Average|サービスが内部エラー (500) 応答コードを返す受信テレメトリ イベントの割合。|ディメンションなし|
|IngressEventsLatency|はい|Ingress Events Latency (イングレス イベント待ち時間)|ミリ秒|Average|イベントが到着してから、Azure Digital Twins がそのイベントを送出できる状態になるまでの時間。その時点でサービスから成功または失敗の結果が送信されます。|結果|
|ModelCount|はい|モデル数|Count|合計|Azure Digital Twins インスタンス内のモデルの合計数。 インスタンスごとに許可されるモデルの最大数に関するサービスの制限に近づいているかどうかを判断するには、このメトリックを使用します。|ディメンションなし|
|ルーティング|はい|Messages Routed (ルーティングされたメッセージ)|Count|合計|Event Hub、Service Bus、Event Grid など、エンドポイントの Azure サービスにルーティングされたメッセージの数。|EndpointType、Result|
|RoutingFailureRate|はい|Routing Failure Rate (ルーティングの失敗率)|Percent|Average|イベントが Azure Digital Twins からエンドポイントの Azure サービス (Event Hub、Service Bus、Event Grid など) にルーティングされるときにエラーが発生するイベントの割合。|EndpointType|
|RoutingLatency|はい|Routing Latency (ルーティングの待機時間)|ミリ秒|Average|イベントが Azure Digital Twins からルーティングされてから、Event Hub、Service Bus、Event Grid などのエンドポイントの Azure サービスにポストされるまでの経過時間。|EndpointType、Result|
|TwinCount|はい|ツインの数|Count|合計|Azure Digital Twins インスタンス内のツインの合計数。 インスタンスごとに許可されるツインの最大数に関するサービスの制限に近づいているかどうかを判断するには、このメトリックを使用します。|ディメンションなし|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AddRegion|はい|追加されたリージョン|Count|Count|追加されたリージョン|リージョン|
|AutoscaleMaxThroughput|いいえ|自動スケーリングの最大スループット|Count|最大値|自動スケーリングの最大スループット|DatabaseName、CollectionName|
|AvailableStorage|いいえ|(非推奨) 使用可能な記憶域|バイト|合計|[使用可能な記憶域] は、2023 年 9 月末に Azure Monitor から削除されます。 Cosmos DB コレクション ストレージのサイズは無制限になりました。 唯一の制限は、各論理パーティション キーのストレージ サイズが 20 GB であることです。 診断ログで PartitionKeyStatistics を有効にすると、上位のパーティション キーのストレージ消費量を知ることができます。 Cosmos DB ストレージ クォータの詳細については、こちらのドキュメント https://docs.microsoft.com/azure/cosmos-db/concepts-limits をご確認ください。 非推奨になった後、非推奨メトリックでまだ定義されている残りのアラート ルールは、非推奨となる日の後に自動的に無効になります。|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|いいえ|Cassandra 接続の終了|Count|合計|1 分単位の細分性で報告された、閉じられた Cassandra 接続の数|Region、ClosureReason|
|CassandraConnectorAvgReplicationLatency|いいえ|Cassandra Connector Average Replication Latency\(Cassandra コネクタの平均レプリケーション待機時間\)|MilliSeconds|Average|Cassandra コネクタの平均レプリケーション待機時間|ディメンションなし|
|CassandraConnectorReplicationHealthStatus|いいえ|Cassandra Connector Replication Health Status\(Cassandra コネクタ レプリケーションの正常性状態\)|Count|Count|Cassandra コネクタ レプリケーションの正常性の状態|NotStarted、ReplicationInProgress、Error|
|CassandraKeyspaceCreate|いいえ|作成された Cassandra キースペース|Count|Count|作成された Cassandra キースペース|ResourceName |
|CassandraKeyspaceDelete|いいえ|Cassandra Keyspace Deleted (削除された Cassandra キースペース)|Count|Count|削除された Cassandra キースペース|ResourceName |
|CassandraKeyspaceThroughputUpdate|いいえ|Cassandra Keyspace Throughput Updated (更新された Cassandra キースペースのスループット)|Count|Count|更新された Cassandra キースペースのスループット|ResourceName |
|CassandraKeyspaceUpdate|いいえ|Cassandra Keyspace Updated (更新された Cassandra キースペース)|Count|Count|更新された Cassandra キースペース|ResourceName |
|CassandraRequestCharges|いいえ|Cassandra 要求の料金|Count|合計|行われた Cassandra 要求で使用された RU|DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|いいえ|Cassandra 要求|Count|Count|行われた Cassandra 要求の数|DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|CassandraTableCreate|いいえ|作成された Cassandra テーブル|Count|Count|作成された Cassandra テーブル|ResourceName、ChildResourceName |
|CassandraTableDelete|いいえ|Cassandra Table Deleted (削除された Cassandra テーブル)|Count|Count|削除された Cassandra テーブル|ResourceName、ChildResourceName |
|CassandraTableThroughputUpdate|いいえ|Cassandra Table Throughput Updated (更新された Cassandra テーブルのスループット)|Count|Count|更新された Cassandra テーブルのスループット|ResourceName、ChildResourceName |
|CassandraTableUpdate|いいえ|Cassandra Table Updated (更新された Cassandra テーブル)|Count|Count|更新された Cassandra テーブル|ResourceName、ChildResourceName |
|CreateAccount|はい|作成されたアカウント|Count|Count|作成されたアカウント|ディメンションなし|
|DataUsage|いいえ|データ利用状況|バイト|合計|5 分単位の細分性で報告されたデータ使用量の合計|CollectionName、DatabaseName、Region|
|DeleteAccount|はい|削除されたアカウント|Count|Count|削除されたアカウント|ディメンションなし|
|DocumentCount|いいえ|ドキュメント数|Count|合計|5 分単位の細分性で報告されたドキュメントの合計数|CollectionName、DatabaseName、Region|
|DocumentQuota|いいえ|ドキュメントのクォータ|バイト|合計|5 分単位の細分性で報告されたストレージ クォータの合計|CollectionName、DatabaseName、Region|
|GremlinDatabaseCreate|いいえ|作成された Gremlin データベース|Count|Count|作成された Gremlin データベース|ResourceName |
|GremlinDatabaseDelete|いいえ|Gremlin Database Deleted (削除された Gremlin データベース)|Count|Count|削除された Gremlin データベース|ResourceName |
|GremlinDatabaseThroughputUpdate|いいえ|Gremlin Database Throughput Updated (更新された Gremlin データベースのスループット)|Count|Count|更新された Gremlin データベースのスループット|ResourceName |
|GremlinDatabaseUpdate|いいえ|Gremlin Database Updated (更新された Gremlin データベース)|Count|Count|更新された Gremlin データベース|ResourceName |
|GremlinGraphCreate|いいえ|作成された Gremlin グラフ|Count|Count|作成された Gremlin グラフ|ResourceName、ChildResourceName |
|GremlinGraphDelete|いいえ|Gremlin Graph Deleted (削除された Gremlin グラフ)|Count|Count|削除された Gremlin グラフ|ResourceName、ChildResourceName |
|GremlinGraphThroughputUpdate|いいえ|Gremlin Graph Throughput Updated (更新された Gremlin グラフのスループット)|Count|Count|更新された Gremlin グラフのスループット|ResourceName、ChildResourceName |
|GremlinGraphUpdate|いいえ|Gremlin Graph Updated (更新された Gremlin グラフ)|Count|Count|更新された Gremlin グラフ|ResourceName、ChildResourceName |
|IndexUsage|いいえ|インデックスの使用量|バイト|合計|5 分単位の細分性で報告されたインデックス使用量の合計|CollectionName、DatabaseName、Region|
|IntegratedCacheEvictedEntriesSize|いいえ|IntegratedCacheEvictedEntriesSize|バイト|Average|統合キャッシュから削除されたエントリのサイズ|CacheType、Region|
|IntegratedCacheHitRate|いいえ|IntegratedCacheHitRate|Percent|Average|統合キャッシュのキャッシュ ヒット率|CacheType、Region|
|IntegratedCacheSize|いいえ|IntegratedCacheSize|バイト|Average|専用ゲートウェイ要求の統合キャッシュのサイズ|CacheType、Region|
|IntegratedCacheTTLExpirationCount|いいえ|IntegratedCacheTTLExpirationCount|Count|Average|TTL の有効期限が原因で統合キャッシュから削除されたエントリの数|CacheType、Region|
|MetadataRequests|いいえ|メタデータの要求数|Count|Count|メタデータの要求数。 Cosmos DB はメタデータ コレクションをアカウントごとに保持します。これにより、コレクションやデータベースなどとそれらの構成を無料で列挙できます。|DatabaseName、CollectionName、Region、StatusCode、 |
|MongoCollectionCreate|いいえ|作成された Mongo コレクション|Count|Count|作成された Mongo コレクション|ResourceName、ChildResourceName |
|MongoCollectionDelete|いいえ|Mongo Collection Deleted (削除された Mongo コレクション)|Count|Count|削除された Mongo コレクション|ResourceName、ChildResourceName |
|MongoCollectionThroughputUpdate|いいえ|Mongo Collection Throughput Updated (更新された Mongo コレクションのスループット)|Count|Count|更新された Mongo コレクションのスループット|ResourceName、ChildResourceName |
|MongoCollectionUpdate|いいえ|Mongo Collection Updated (更新された Mongo コレクション)|Count|Count|更新された Mongo コレクション|ResourceName、ChildResourceName |
|MongoDatabaseDelete|いいえ|削除された Mongo データベース|Count|Count|削除された Mongo データベース|ResourceName |
|MongoDatabaseThroughputUpdate|いいえ|Mongo Database Throughput Updated (更新された Mongo データベースのスループット)|Count|Count|更新された Mongo データベースのスループット|ResourceName |
|MongoDBDatabaseCreate|いいえ|作成された Mongo データベース|Count|Count|作成された Mongo データベース|ResourceName |
|MongoDBDatabaseUpdate|いいえ|Mongo Database Updated (更新された Mongo データベース)|Count|Count|更新された Mongo データベース|ResourceName |
|MongoRequestCharge|はい|Mongo 要求の料金|Count|合計|使用された Mongo 要求の単位数|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequests|はい|Mongo Requests (Mongo 要求数)|Count|Count|実行された Mongo 要求の数|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequestsCount|いいえ|(非推奨) Mongo 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 要求の数|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsDelete|いいえ|(非推奨) Mongo Delete 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 削除要求の数|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsInsert|いいえ|(非推奨) Mongo Insert 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 挿入数|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsQuery|いいえ|(非推奨) Mongo Query 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo クエリ要求数|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsUpdate|いいえ|(非推奨) Mongo Update 要求のレート|CountPerSecond|Average|1 秒あたりの Mongo 更新要求数|DatabaseName、CollectionName、Region、ErrorCode|
|NormalizedRUConsumption|いいえ|Normalized RU Consumption (正規化された RU 消費量)|Percent|最大値|1 分あたりの最大 RU 消費率|CollectionName、DatabaseName、Region、PartitionKeyRangeId|
|ProvisionedThroughput|いいえ|プロビジョニングされたスループット|Count|最大値|プロビジョニングされたスループット|DatabaseName、CollectionName|
|RegionFailover|はい|リージョンのフェールオーバー|Count|Count|リージョンのフェールオーバー|ディメンションなし|
|RemoveRegion|はい|削除されたリージョン|Count|Count|削除されたリージョン|リージョン|
|ReplicationLatency|はい|P99 のレプリケーション待機時間|MilliSeconds|Average|geo 対応アカウントのソースおよびターゲット リージョン全体の P99 のレプリケーション待機時間|SourceRegion、TargetRegion|
|ServerSideLatency|いいえ|サーバー側の待機時間|MilliSeconds|Average|サーバー側の待機時間|DatabaseName、CollectionName、Region、ConnectionMode、OperationType、PublicAPIType|
|ServiceAvailability|いいえ|サービスの可用性|Percent|Average|アカウントは 1 時間、1 日、または 1 か月単位の細分性で可用性を要求します|ディメンションなし|
|SqlContainerCreate|いいえ|作成された SQL コンテナー|Count|Count|作成された SQL コンテナー|ResourceName、ChildResourceName |
|SqlContainerDelete|いいえ|Sql Container Deleted (削除された SQL コンテナー)|Count|Count|削除された SQL コンテナー|ResourceName、ChildResourceName |
|SqlContainerThroughputUpdate|いいえ|Sql Container Throughput Updated (更新された SQL コンテナーのスループット)|Count|Count|更新された SQL コンテナーのスループット|ResourceName、ChildResourceName |
|SqlContainerUpdate|いいえ|Sql Container Updated (更新された SQL コンテナー)|Count|Count|更新された SQL コンテナー|ResourceName、ChildResourceName |
|SqlDatabaseCreate|いいえ|作成された SQL Database|Count|Count|作成された SQL Database|ResourceName |
|SqlDatabaseDelete|いいえ|Sql Database Deleted (削除された SQL データベース)|Count|Count|削除された SQL データベース|ResourceName |
|SqlDatabaseThroughputUpdate|いいえ|Sql Database Throughput Updated (更新された SQL データベースのスループット)|Count|Count|更新された SQL データベースのスループット|ResourceName |
|SqlDatabaseUpdate|いいえ|Sql Database Updated (更新された SQL データベース)|Count|Count|更新された SQL データベース|ResourceName |
|TableTableCreate|いいえ|作成された AzureTable テーブル|Count|Count|作成された AzureTable テーブル|ResourceName |
|TableTableDelete|いいえ|AzureTable Table Deleted (削除された AzureTable テーブル)|Count|Count|削除された AzureTable テーブル|ResourceName |
|TableTableThroughputUpdate|いいえ|AzureTable Table Throughput Updated (更新された AzureTable テーブルのスループット)|Count|Count|更新された AzureTable テーブルのスループット|ResourceName |
|TableTableUpdate|いいえ|AzureTable Table Updated (更新された AzureTable テーブル)|Count|Count|更新された AzureTable テーブル|ResourceName |
|TotalRequests|はい|要求の合計数|Count|Count|行われた要求の数|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|TotalRequestUnits|はい|合計要求単位数|Count|合計|消費された要求の単位数|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|UpdateAccountKeys|はい|更新されたアカウント キー|Count|Count|更新されたアカウント キー|KeyType|
|UpdateAccountNetworkSettings|はい|更新されたアカウント ネットワーク設定|Count|Count|更新されたアカウント ネットワーク設定|ディメンションなし|
|UpdateAccountReplicationSettings|はい|更新されたアカウント レプリケーション設定|Count|Count|更新されたアカウント レプリケーション設定|ディメンションなし|
|UpdateDiagnosticsSettings|いいえ|Account Diagnostic Settings Updated (更新されたアカウント診断設定)|Count|Count|更新されたアカウント診断設定|DiagnosticSettingsName、ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|はい|高度なフィルターの評価|Count|合計|このトピックのイベント サブスクリプション全体で評価される高度なフィルターの合計数。|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|Topic、ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|トピック|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|ディメンションなし|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|ディメンションなし|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|ディメンションなし|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|はい|高度なフィルターの評価|Count|合計|このトピックのイベント サブスクリプション全体で評価される高度なフィルターの合計数。|EventSubscriptionName|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|はい|高度なフィルターの評価|Count|合計|このトピックのイベント サブスクリプション全体で評価される高度なフィルターの合計数。|EventSubscriptionName|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|はい|高度なフィルターの評価|Count|合計|このトピックのイベント サブスクリプション全体で評価される高度なフィルターの合計数。|EventSubscriptionName|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveConnections|いいえ|ActiveConnections|Count|Average|Microsoft.EventHub のアクティブな接続の合計数。||
|AvailableMemory|いいえ|使用可能なメモリ|Percent|最大値|メモリの合計に対する割合としてイベント ハブ クラスターで使用可能なメモリ。|Role|
|CaptureBacklog|いいえ|バックログのキャプチャ。|Count|合計|Microsoft.EventHub のバックログをキャプチャします。||
|CapturedBytes|いいえ|キャプチャされたバイト数。|バイト|合計|Microsoft.EventHub のキャプチャされたバイト数。||
|CapturedMessages|いいえ|キャプチャされたメッセージ数。|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。||
|ConnectionsClosed|いいえ|切断された接続数。|Count|Average|Microsoft.EventHub の切断された接続数。||
|ConnectionsOpened|いいえ|開かれている接続数。|Count|Average|Microsoft.EventHub の開かれている接続数。||
|CPU|いいえ|CPU|Percent|最大値|イベント ハブ クラスターの CPU 使用率 (%)|Role|
|IncomingBytes|はい|着信バイト数。|バイト|合計|Microsoft.EventHub の受信バイト数。||
|IncomingMessages|はい|受信メッセージ|Count|合計|Microsoft.EventHub の受信メッセージ数。||
|IncomingRequests|はい|受信要求|Count|合計|Microsoft.EventHub の受信要求数。||
|OutgoingBytes|はい|発信バイト数。|バイト|合計|Microsoft.EventHub の発信バイト数。||
|OutgoingMessages|はい|送信メッセージ|Count|合計|Microsoft.EventHub の送信メッセージ数。||
|QuotaExceededErrors|いいえ|クォータ超過エラー数。|Count|合計|Microsoft.EventHub のクォータ超過エラー数。|OperationResult|
|ServerErrors|いいえ|サーバー エラー。|Count|合計|Microsoft.EventHub のサーバー エラー数。|OperationResult|
|サイズ|いいえ|サイズ|バイト|Average|EventHub のサイズ (バイト単位)。|Role|
|SuccessfulRequests|いいえ|成功した要求|Count|合計|Microsoft.EventHub の成功した要求数。|OperationResult|
|ThrottledRequests|いいえ|スロットルされた要求数。|Count|合計|Microsoft.EventHub のスロットルされた要求数。|OperationResult|
|UserErrors|いいえ|ユーザー エラー数。|Count|合計|Microsoft.EventHub のユーザー エラー数。|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveConnections|いいえ|ActiveConnections|Count|Average|Microsoft.EventHub のアクティブな接続の合計数。||
|CaptureBacklog|いいえ|バックログのキャプチャ。|Count|合計|Microsoft.EventHub のバックログをキャプチャします。|EntityName|
|CapturedBytes|いいえ|キャプチャされたバイト数。|バイト|合計|Microsoft.EventHub のキャプチャされたバイト数。|EntityName|
|CapturedMessages|いいえ|キャプチャされたメッセージ数。|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。|EntityName|
|ConnectionsClosed|いいえ|切断された接続数。|Count|Average|Microsoft.EventHub の切断された接続数。|EntityName|
|ConnectionsOpened|いいえ|開かれている接続数。|Count|Average|Microsoft.EventHub の開かれている接続数。|EntityName|
|EHABL|はい|アーカイブ バックログ メッセージ数 (非推奨)|Count|合計|名前空間のバックログ内の Event Hub アーカイブ メッセージ数 (非推奨)||
|EHAMBS|はい|アーカイブ メッセージ スループット (非推奨)|バイト|合計|名前空間内の Event Hub アーカイブ メッセージ スループット (非推奨)||
|EHAMSGS|はい|アーカイブ メッセージ数 (非推奨)|Count|合計|名前空間内の Event Hub アーカイブ メッセージ数 (非推奨)||
|EHINBYTES|はい|受信バイト数 (非推奨)|バイト|合計|名前空間の Event Hub 受信メッセージのスループット (非推奨)||
|EHINMBS|はい|受信バイト数 (廃止) (非推奨)|バイト|合計|名前空間の Event Hub 受信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"受信バイト数" メトリックを使ってください (非推奨)||
|EHINMSGS|はい|受信メッセージ (非推奨)|Count|合計|名前空間の受信メッセージの合計数 (非推奨)||
|EHOUTBYTES|はい|送信バイト数 (非推奨)|バイト|合計|名前空間の Event Hub 送信メッセージのスループット (非推奨)||
|EHOUTMBS|はい|送信バイト数 (廃止) (非推奨)|バイト|合計|名前空間の Event Hub 送信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"発信バイト数" メトリックを使ってください (非推奨)||
|EHOUTMSGS|はい|送信メッセージ (非推奨)|Count|合計|名前空間の送信メッセージの合計数 (非推奨)||
|FAILREQ|はい|失敗した要求数 (非推奨)|Count|合計|名前空間の失敗した要求の合計数 (非推奨)||
|IncomingBytes|はい|着信バイト数。|バイト|合計|Microsoft.EventHub の受信バイト数。|EntityName|
|IncomingMessages|はい|受信メッセージ|Count|合計|Microsoft.EventHub の受信メッセージ数。|EntityName|
|IncomingRequests|はい|受信要求|Count|合計|Microsoft.EventHub の受信要求数。|EntityName|
|INMSGS|はい|受信メッセージ (廃止) (非推奨)|Count|合計|名前空間の受信メッセージの総数。 このメトリックは非推奨です。 代わりに、"受信メッセージ" メトリックを使ってください (非推奨)||
|INREQS|はい|受信要求数 (非推奨)|Count|合計|名前空間に対して受信した送信要求の合計数 (非推奨)||
|INTERR|はい|内部サーバー エラー数 (非推奨)|Count|合計|名前空間の内部サーバー エラーの合計数 (非推奨)||
|MISCERR|はい|その他のエラー数 (非推奨)|Count|合計|名前空間の失敗した要求の合計数 (非推奨)||
|OutgoingBytes|はい|発信バイト数。|バイト|合計|Microsoft.EventHub の発信バイト数。|EntityName|
|OutgoingMessages|はい|送信メッセージ|Count|合計|Microsoft.EventHub の送信メッセージ数。|EntityName|
|OUTMSGS|はい|送信メッセージ (廃止) (非推奨)|Count|合計|名前空間の送信メッセージの総数。 このメトリックは非推奨です。 代わりに、"送信メッセージ" メトリックを使ってください (非推奨)||
|QuotaExceededErrors|いいえ|クォータ超過エラー数。|Count|合計|Microsoft.EventHub のクォータ超過エラー数。|EntityName、OperationResult|
|ServerErrors|いいえ|サーバー エラー。|Count|合計|Microsoft.EventHub のサーバー エラー数。|EntityName、OperationResult|
|サイズ|いいえ|サイズ|バイト|Average|EventHub のサイズ (バイト単位)。|EntityName|
|SuccessfulRequests|いいえ|成功した要求|Count|合計|Microsoft.EventHub の成功した要求数。|EntityName、OperationResult|
|SUCCREQ|はい|成功した要求数 (非推奨)|Count|合計|名前空間の成功した要求の合計数 (非推奨)||
|SVRBSY|はい|サーバー ビジー エラー数 (非推奨)|Count|合計|名前空間のサーバー ビジー エラーの合計数 (非推奨)||
|ThrottledRequests|いいえ|スロットルされた要求数。|Count|合計|Microsoft.EventHub のスロットルされた要求数。|EntityName、OperationResult|
|UserErrors|いいえ|ユーザー エラー数。|Count|合計|Microsoft.EventHub のユーザー エラー数。|EntityName、OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|はい|カテゴリ別のゲートウェイ要求数|Count|合計|カテゴリ別のゲートウェイ要求の数 (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|はい|ゲートウェイ要求数|Count|合計|ゲートウェイ要求の数|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|はい|REST プロキシ コンシューマーの RequestThroughput|CountPerSecond|合計|Kafka REST プロキシに対するコンシューマー要求の数|Machine、Topic|
|KafkaRestProxy.ConsumerRequestTime.p95|はい|REST プロキシ コンシューマーの RequestLatency|ミリ秒|Average|Kafka REST プロキシ経由のコンシューマー要求でのメッセージ待機時間|Machine、Topic|
|KafkaRestProxy.MessagesIn.m1_delta|はい|REST プロキシ プロデューサーの MessageThroughput|CountPerSecond|合計|Kafka REST プロキシを介したプロデューサー メッセージの数|Machine、Topic|
|KafkaRestProxy.MessagesOut.m1_delta|はい|REST プロキシ コンシューマーの MessageThroughput|CountPerSecond|合計|Kafka REST プロキシを介したコンシューマー メッセージの数|Machine、Topic|
|KafkaRestProxy.OpenConnections|はい|REST プロキシの ConcurrentConnections|Count|合計|Kafka REST プロキシ経由の同時接続の数|Machine、Topic|
|KafkaRestProxy.ProducerRequest.m1_delta|はい|REST プロキシ プロデューサーの RequestThroughput|CountPerSecond|合計|Kafka REST プロキシに対するプロデューサー要求の数|Machine、Topic|
|KafkaRestProxy.ProducerRequestTime.p95|はい|REST プロキシ プロデューサーの RequestLatency|ミリ秒|Average|Kafka REST プロキシ経由のプロデューサー要求でのメッセージ待機時間|Machine、Topic|
|NumActiveWorkers|はい|アクティブなワーカーの数|Count|最大値|アクティブなワーカーの数|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|サービスの可用性率。|ディメンションなし|
|CosmosDbCollectionSize|はい|Cosmos DB コレクション サイズ|バイト|合計|バッキング Cosmos DB コレクションのサイズ (バイト単位)。|ディメンションなし|
|CosmosDbIndexSize|はい|Cosmos DB インデックスのサイズ|バイト|合計|バッキング Cosmos DB コレクションのインデックスのサイズ (バイト単位)。|ディメンションなし|
|CosmosDbRequestCharge|はい|Cosmos DB RU の使用状況|Count|合計|サービスのバッキング Cosmos DB に対する要求の RU の使用状況。|Operation、ResourceType|
|CosmosDbRequests|はい|サービス Cosmos DB 要求|Count|SUM|サービスのバッキング Cosmos DB に対して行われた要求の合計数。|Operation、ResourceType|
|CosmosDbThrottleRate|はい|サービス Cosmos DB スロットル率|Count|SUM|サービスのバッキング Cosmos DB からの 429 応答の合計数。|Operation、ResourceType|
|IoTConnectorDeviceEvent|はい|Number of Incoming Messages (受信メッセージの数)|Count|SUM|正規化の前に Azure IoT Connector for FHIR によって受信されたメッセージの合計数。|Operation、ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|はい|平均正規化ステージ待機時間|ミリ秒|Average|イベントのインジェスト時間から、イベントが正規化のために処理されるまでの平均時間。|Operation、ConnectorName|
|IoTConnectorMeasurement|はい|測定数|Count|SUM|Azure IoT Connector for FHIR の FHIR 変換ステージによって受信された、正規化された値の読み取りの数。|Operation、ConnectorName|
|IoTConnectorMeasurementGroup|はい|Number of Message Groups (メッセージ グループの数)|Count|SUM|FHIR 変換ステージによって生成される、種類、デバイス、患者、および構成された期間全体の測定値の一意のグループの総数。|Operation、ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|はい|Average Group Stage Latency (グループ ステージの平均待機時間)|ミリ秒|Average|IoT コネクタがデバイス データを受信してから、FHIR 変換ステージによってデータが処理されるまでの期間。|Operation、ConnectorName|
|IoTConnectorNormalizedEvent|はい|Number of Normalized Messages (正規化されたメッセージの数)|Count|SUM|Azure IoT Connector for FHIR の正規化ステージから出力された、マップされた正規化値の合計数。|Operation、ConnectorName|
|IoTConnectorTotalErrors|はい|Total Error Count|Count|SUM|Azure IoT Connector for FHIR によってログに記録されたエラーの合計数|Name、Operation、ErrorType、ErrorSeverity、ConnectorName|
|ServiceApiErrors|はい|サービス エラー|Count|SUM|サービスによって生成された内部サーバー エラーの合計数。|Protocol、Authentication、Operation、ResourceType、StatusCode、StatusCodeClass、StatusCodeText|
|ServiceApiLatency|はい|サービスの待機時間|ミリ秒|Average|サービスの応答待機時間。|Protocol、Authentication、Operation、ResourceType、StatusCode、StatusCodeClass、StatusCodeText|
|ServiceApiRequests|はい|Service Requests|Count|SUM|サービスによって受信された要求の合計数。|Protocol、Authentication、Operation、ResourceType、StatusCode、StatusCodeClass、StatusCodeText|
|TotalErrors|はい|合計エラー数|Count|SUM|サービスで発生した内部サーバー エラーの合計数。|Protocol、StatusCode、StatusCodeClass、StatusCodeText|
|TotalLatency|はい|合計待機時間|ミリ秒|Average|サービスの応答待機時間。|Protocol|
|TotalRequests|はい|要求の合計数|Count|SUM|サービスによって受信された要求の合計数。|Protocol|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|はい|平均 CPU 使用状況|Percent|Average|1 分間隔での仮想 CPU 使用率の平均割合。 仮想 CPU の合計数は、SKU 定義でユーザーが構成した値に基づいています。 SKU で定義されている RoleName に基づいて、さらにフィルターを適用できます。|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|はい|平均 CPU 使用状況|Percent|Average|1 分間隔での仮想 CPU 使用率の平均割合。 仮想 CPU の合計数は、SKU 定義でユーザーが構成した値に基づいています。 SKU で定義されている RoleName に基づいて、さらにフィルターを適用できます。|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|MetricThreshold|はい|メトリックのしきい値|Count|Average|自動スケールの実行時に構成されていた自動スケールのしきい値。|MetricTriggerRule|
|ObservedCapacity|はい|実際の容量|Count|Average|実行時に自動スケールに報告された容量。||
|ObservedMetricValue|はい|実際のメトリック値|Count|Average|実行時に自動スケールによって計算された値|MetricTriggerSource|
|ScaleActionsInitiated|はい|スケール アクション開始|Count|合計|スケール操作の指示。|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|はい|可用性|Percent|Average|正常に完了した可用性テストの割合|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|いいえ|可用性テスト|Count|Count|可用性テストの数|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|availabilityResults/duration|はい|可用性テスト継続時間|MilliSeconds|Average|可用性テスト継続時間|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|browserTimings/networkDuration|はい|ページ読み込みのネットワーク接続時間|MilliSeconds|Average|ユーザー要求からネットワーク接続までの時間。 DNS の参照とトランスポートの接続が含まれます。|ディメンションなし|
|browserTimings/processingDuration|はい|クライアントの処理時間|MilliSeconds|Average|ドキュメントの最終バイトを受信してから、DOM が読み込まれるまでの時間。 非同期要求がまだ処理されている可能性があります。|ディメンションなし|
|browserTimings/receiveDuration|はい|受信応答時間|MilliSeconds|Average|最初のバイトから最後のバイトまで、または切断までの時間。|ディメンションなし|
|browserTimings/sendDuration|はい|要求送信時間|MilliSeconds|Average|ネットワーク接続から、最初のバイトを受信するまでの時間。|ディメンションなし|
|browserTimings/totalDuration|はい|ブラウザーのページ読み込み時間|MilliSeconds|Average|ユーザーが要求を出してから DOM、スタイル シート、スクリプト、およびイメージが読み込まれるまでの時間。|ディメンションなし|
|dependencies/count|いいえ|依存関係呼び出し|Count|Count|外部リソースに対するアプリケーションからの呼び出しの数。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/synthetic、cloud/roleInstance、cloud/roleName|
|dependencies/duration|はい|依存関係の期間|MilliSeconds|Average|外部リソースに対するアプリケーションからの呼び出しの期間。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/synthetic、cloud/roleInstance、cloud/roleName|
|dependencies/failed|いいえ|依存関係呼び出しの失敗回数|Count|Count|外部リソースに対するアプリケーションからの失敗した依存関係呼び出しの数。|dependency/type、dependency/performanceBucket、dependency/target、dependency/resultCode、operation/synthetic、cloud/roleInstance、cloud/roleName|
|exceptions/browser|いいえ|ブラウザーの例外|Count|Count|ブラウザーでスローされた、キャッチされない例外の数。|cloud/roleName|
|exceptions/count|はい|例外|Count|Count|キャッチされていないすべての例外の合計数。|cloud/roleName、cloud/roleInstance、client/type|
|exceptions/server|いいえ|サーバーの例外|Count|Count|サーバー アプリケーションでスローされ、キャッチされていない例外の数。|cloud/roleName、cloud/roleInstance|
|pageViews/count|はい|ページ ビュー|Count|Count|ページ ビューの数。|operation/synthetic、cloud/roleName|
|pageViews/duration|はい|ページ ビューの読み込み時間|MilliSeconds|Average|ページ ビューの読み込み時間|operation/synthetic、cloud/roleName|
|performanceCounters/exceptionsPerSecond|はい|例外レート|CountPerSecond|Average|.NET 例外、および .NET 例外に変換されたアンマネージ例外を含む、Windows に報告された処理済み例外と未処理の例外の数。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|はい|使用可能なメモリ|バイト|Average|プロセスへの割り当てまたはシステムの使用にすぐに利用可能な物理メモリ。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|はい|プロセス CPU|Percent|Average|すべてのプロセス スレッドで命令を実行するためにプロセッサを使用した経過時間の割合。 これは 0 から 100 の間で変化することがあります。 このメトリックは、w3wp プロセスのみのパフォーマンスを示します。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|はい|プロセス IO 量|BytesPerSecond|Average|ファイル、ネットワーク、およびデバイスに対する読み書きで 1 秒あたりに処理される合計バイト数。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|はい|プロセッサ時間|Percent|Average|プロセッサによって非アイドル スレッドで費やされる時間の割合。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|はい|プロセスのプライベート バイト|バイト|Average|監視対象のアプリケーション プロセスに対して専用に割り当てられるメモリ。|cloud/roleInstance|
|performanceCounters/requestExecutionTime|はい|HTTP 要求の実行時間|MilliSeconds|Average|最新の要求の実行時間。|cloud/roleInstance|
|performanceCounters/requestsInQueue|はい|アプリケーション キュー内の HTTP 要求|Count|Average|アプリケーション要求キューの長さ。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|はい|HTTP 要求率|CountPerSecond|Average|ASP.NET からの 1 秒あたりのアプリケーションへのすべての要求のレート。|cloud/roleInstance|
|requests/count|いいえ|サーバー要求|Count|Count|完了した HTTP 要求の数。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/duration|はい|サーバーの応答時間|MilliSeconds|Average|HTTP 要求を受信してから、応答の送信を終了するまでの時間。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/failed|いいえ|失敗した要求|Count|Count|失敗としてマークされた HTTP 要求の数。 ほとんどの場合、応答コードが 400 以上で、かつ 401 に等しくない要求です。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、cloud/roleName|
|要求/率|いいえ|サーバー要求率|CountPerSecond|Average|1 秒あたりのサーバー要求数による率|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|traces/count|はい|トレース|Count|Count|トレース ドキュメント数|trace/severityLevel、operation/synthetic、cloud/roleName、cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|c2d.property.read.failure|はい|Failed Device Property Reads from IoT Central (IoT Central からのデバイス プロパティ読み取りの失敗数)|Count|合計|IoT Central から開始されて失敗した、すべてのプロパティ読み取りの数|ディメンションなし|
|c2d.property.read.success|はい|Successful Device Property Reads from IoT Central (IoT Central からのデバイス プロパティ読み取りの成功数)|Count|合計|IoT Central から開始されて成功した、すべてのプロパティ読み取りの数|ディメンションなし|
|c2d.property.update.failure|はい|Failed Device Property Updates from IoT Central (IoT Central からのデバイス プロパティ更新の失敗数)|Count|合計|IoT Central から開始されて失敗した、すべてのプロパティ更新の数|ディメンションなし|
|c2d.property.update.success|はい|Successful Device Property Updates from IoT Central (IoT Central からのデバイス プロパティ更新の成功数)|Count|合計|IoT Central から開始されて成功した、すべてのプロパティ更新の数|ディメンションなし|
|connectedDeviceCount|いいえ|Total Connected Devices (接続されたデバイスの総数)|Count|Average|IoT Central に接続されているデバイスの数|ディメンションなし|
|d2c.property.read.failure|はい|Failed Device Property Reads from Devices (デバイスからのデバイス プロパティ読み取りの失敗数)|Count|合計|デバイスから開始されて失敗した、すべてのプロパティ読み取りの数|ディメンションなし|
|d2c.property.read.success|はい|Successful Device Property Reads from Devices (デバイスからのデバイス プロパティ読み取りの成功数)|Count|合計|デバイスから開始されて成功した、すべてのプロパティ読み取りの数|ディメンションなし|
|d2c.property.update.failure|はい|Failed Device Property Updates from Devices (デバイスからのデバイス プロパティ更新の失敗数)|Count|合計|デバイスから開始されて失敗した、すべてのプロパティ更新の数|ディメンションなし|
|d2c.property.update.success|はい|Successful Device Property Updates from Devices (デバイスからのデバイス プロパティ更新の成功数)|Count|合計|デバイスから開始されて成功した、すべてのプロパティ更新の数|ディメンションなし|
|dataExport.error|はい|データ エクスポート エラー|Count|合計|データのエクスポートで発生したエラーの数|exportId、exportDisplayName、destinationId、destinationDisplayName|
|dataExport.messages.filtered|はい|フィルター処理されたデータ エクスポート メッセージ|Count|合計|データ エクスポートのフィルターによって渡されたメッセージの数|exportId、exportDisplayName、destinationId、destinationDisplayName|
|dataExport.messages.received|はい|受信したデータ エクスポート メッセージ|Count|合計|データ エクスポートのために受信されたメッセージの数 (フィルター処理と強化処理の前)|exportId、exportDisplayName、destinationId、destinationDisplayName|
|dataExport.messages.written|はい|書き込まれたデータ エクスポート メッセージ|Count|合計|宛先に書き込まれたメッセージの数|exportId、exportDisplayName、destinationId、destinationDisplayName|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ApiLatency|いいえ|ApiLatency|6|0|Microsoft.IoTSpaces に対して行われた API 要求の待機時間をミリ秒単位で計測します|ディメンションなし|
|FunctionExecutionLatency|いいえ|FunctionExecutionLatency|6|0|Microsoft.IoTSpaces のユーザー定義関数実行の待機時間をミリ秒単位で計測します|ディメンションなし|
|MessageEgressFailure|いいえ|MessageEgressFailure|2|3|「Microsoft.IoTSpaces のカウントで失敗したカウント イベントを測定する」に類似したローカライズされた文字列を検索します|ディメンションなし|
|MessageEgressLatency|いいえ|MessageEgressLatency|6|0|Microsoft.IoTSpaces のディスパッチャーから他のエンドポイントまでの待機時間をミリ秒単位で計測します|ディメンションなし|
|MessageEgressSuccess|いいえ|MessageEgressSuccess|2|3|「Microsoft.IoTSpaces のカウントで完了したカウント イベントを測定する」に類似したローカライズされた文字列を検索します|ディメンションなし|
|ProcessingLatency|いいえ|ProcessingLatency|6|0|Microsoft.IoTSpaces でのメッセージの取り込みからイベントのディスパッチまでの待機時間をミリ秒単位で計測します|ディメンションなし|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|いいえ|全体的なサービスの可用性|Percent|Average|サービス要求の可用性|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|ServiceApiHit|はい|サービス API ヒット数の合計|Count|Count|サービス API ヒット数の合計|ActivityType、ActivityName|
|ServiceApiLatency|いいえ|サービス API の全体的な待機時間|ミリ秒|Average|サービス API 要求の全体的な待機時間|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|ServiceApiResult|はい|サービス API 結果数の合計|Count|Count|サービス API 結果数の合計|ActivityType、ActivityName、StatusCode、StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|コンテナーの全体的な可用性|Percent|Average|コンテナー要求の可用性|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|SaturationShoebox|いいえ|コンテナーの全体的な飽和度|Percent|Average|使用されているコンテナーの容量|ActivityType、ActivityName、TransactionType|
|ServiceApiHit|はい|サービス API ヒット数の合計|Count|Count|サービス API ヒット数の合計|ActivityType、ActivityName|
|ServiceApiLatency|はい|サービス API の全体的な待機時間|ミリ秒|Average|サービス API 要求の全体的な待機時間|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|ServiceApiResult|はい|サービス API 結果数の合計|Count|Count|サービス API 結果数の合計|ActivityType、ActivityName、StatusCode、StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|はい|接続されたクラスター内の CPU コアの合計数|Count|合計|接続されたクラスター内の CPU コアの合計数||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BatchBlobCount|はい|Batch Blob Count\(バッチ BLOB 数\)|Count|Average|インジェスト用に集計されたバッチのデータ ソースの数。|データベース|
|BatchDuration|はい|バッチ期間|Seconds|Average|インジェスト フロー内の集計フェーズの期間。|データベース|
|BatchesProcessed|はい|Batches Processed\(処理されたバッチ\)|Count|合計|インジェスト用に集計されたバッチの数。 バッチ処理の種類: バッチが、バッチ処理ポリシーによって設定されたバッチ処理時間、データ サイズ、またはファイル数の上限に達したかどうか|Database、SealReason|
|BatchSize|はい|バッチ サイズ|バイト|Average|インジェスト用に集計されたバッチで、必要なデータ サイズが圧縮されていません。|データベース|
|BlobsDropped|はい|破棄された BLOB|Count|合計|コンポーネントによって永続的に拒否されている BLOB の数。|Database、ComponentType、ComponentName|
|BlobsProcessed|はい|処理された BLOB|Count|合計|コンポーネントによって処理された BLOB の数。|Database、ComponentType、ComponentName|
|BlobsReceived|はい|受信した BLOB|Count|合計|コンポーネントによって入力ストリームから受信した BLOB の数。|Database、ComponentType、ComponentName|
|CacheUtilization|はい|キャッシュ使用率|Percent|Average|クラスター スコープ内の使用率レベル|ディメンションなし|
|ContinuousExportMaxLatenessMinutes|はい|Continuous Export Max Lateness (連続エクスポートの最大遅延)|Count|最大値|クラスター内の連続エクスポート ジョブによって報告された遅延 (分単位)|ディメンションなし|
|ContinuousExportNumOfRecordsExported|はい|連続エクスポート - エクスポートされたレコードの数|Count|合計|エクスポート操作中に作成されたすべてのストレージ アーティファクトについてエクスポートされ実行されたレコードの数|ContinuousExportName、Database|
|ContinuousExportPendingCount|はい|保留中の連続エクスポートの数|Count|最大値|実行の準備ができている保留中の連続エクスポート ジョブの数|ディメンションなし|
|ContinuousExportResult|はい|連続エクスポートの結果|Count|Count|連続エクスポートが成功したか失敗したかを示します|ContinuousExportName、Result、Database|
|CPU|はい|CPU|Percent|Average|CPU 使用率レベル|ディメンションなし|
|DiscoveryLatency|はい|検出の待機時間|Seconds|Average|データ接続によって報告されます (存在する場合)。 メッセージがキューに入れられるか、イベントが作成されてから、データ接続によって検出されるまでの時間 (秒)。 この時間は、Azure Data Explorer の合計インジェスト期間には含まれません。|ComponentType、ComponentName|
|EventsDropped|はい|破棄されたイベント|Count|合計|データ接続によって永久に破棄されたイベントの数。 エラーの理由を含むインジェスト結果メトリックが送信されます。|ComponentType、ComponentName|
|EventsProcessed|はい|処理済みイベント|Count|合計|クラスターによって処理されたイベントの数|ComponentType、ComponentName|
|EventsProcessedForEventHubs|はい|(Event/IoT Hubs の) 処理されたイベント|Count|合計|Event/IoT Hub からのインジェスト時に、クラスターによって処理されたイベントの数|EventStatus|
|EventsReceived|はい|受信したイベント|Count|合計|データ接続によって受信されたイベントの数。|ComponentType、ComponentName|
|ExportUtilization|はい|エクスポート使用率|Percent|最大値|エクスポート使用率|ディメンションなし|
|IngestionLatencyInSeconds|はい|インジェストの待ち時間|Seconds|Average|クラスターでデータが受信された時点からクエリー用に準備できるまでの、取り込まれたデータの待ち時間。 インジェストの待ち時間の長さは、インジェストのシナリオに応じて異なります。|ディメンションなし|
|IngestionResult|はい|インジェストの結果|Count|合計|インジェスト操作の回数|IngestionResultDetails|
|IngestionUtilization|はい|インジェストの使用率|Percent|Average|クラスターでのインジェスト スロットの使用率|ディメンションなし|
|IngestionVolumeInMB|はい|インジェストの量|バイト|合計|クラスターに取り込まれたデータの全体的な量|データベース|
|InstanceCount|はい|Instance Count|Count|Average|インスタンスの総数|ディメンションなし|
|KeepAlive|はい|キープ アライブ|Count|Average|サニティ チェックでは、クラスターがクエリに応答していることが示されます|ディメンションなし|
|MaterializedViewAgeMinutes|はい|具体化されたビューの経過時間|Count|Average|具体化されたビューの経過時間 (分)|Database、MaterializedViewName|
|MaterializedViewDataLoss|はい|具体化されたビューのデータ損失|Count|最大値|具体化されたビューの潜在的なデータ損失の可能性を示します|Database、MaterializedViewName、Kind|
|MaterializedViewExtentsRebuild|はい|具体化されたビュー エクステントのリビルド|Count|Average|エクステントのリビルドの数|Database、MaterializedViewName|
|MaterializedViewHealth|はい|具体化されたビューの正常性|Count|Average|具体化されたビューの正常性 (正常の場合は 1、正常でない場合は 0)|Database、MaterializedViewName|
|MaterializedViewRecordsInDelta|はい|具体化されたビューのレコード (差分)|Count|Average|ビューの具体化されていない部分に含まれるレコードの数|Database、MaterializedViewName|
|MaterializedViewResult|はい|具体化されたビューの結果|Count|Average|具体化されたプロセスの結果|Database、MaterializedViewName、Result|
|QueryDuration|はい|クエリ実行時間|ミリ秒|Average|クエリの実行時間 (秒単位)|QueryStatus|
|QueryResult|いいえ|クエリの結果|Count|Count|クエリの合計数。|QueryStatus|
|QueueLength|はい|キューの長さ|Count|Average|コンポーネントのキュー内の保留中のメッセージの数。|ComponentType|
|QueueOldestMessage|はい|キューの最も古いメッセージ|Count|Average|キュー内の最も古いメッセージが挿入されてからの経過時間 (秒単位)。|ComponentType|
|ReceivedDataSizeBytes|はい|受信したデータ サイズ (バイト単位)|バイト|Average|データ接続によって受信されたデータのサイズ。 これは、データ ストリームのサイズ、または提供されている場合は生データのサイズです。|ComponentType、ComponentName|
|StageLatency|はい|ステージの待機時間|Seconds|Average|メッセージが検出されてから、処理のためにレポート コンポーネントによって受信されるまでの累積時間 (検出時間は、メッセージがインジェスト キューのためにキューに入れられたとき、またはデータ接続によって検出されたときに設定されます)。|Database、ComponentType|
|SteamingIngestRequestRate|はい|ストリーミング インジェストの要求率|Count|RateRequestsPerSecond|ストリーミング インジェストの要求率 (要求数/秒)|ディメンションなし|
|StreamingIngestDataRate|はい|ストリーミング インジェストのデータ速度|Count|Average|ストリーミング インジェストのデータ速度 (MB/秒)|ディメンションなし|
|StreamingIngestDuration|はい|ストリーミング インジェストの期間|ミリ秒|Average|ストリーミング インジェスト時間 (ミリ秒)|ディメンションなし|
|StreamingIngestResults|はい|ストリーミング インジェストの結果|Count|Average|ストリーミング インジェストの結果|結果|
|TotalNumberOfConcurrentQueries|はい|同時クエリの合計数|Count|最大値|同時クエリの合計数|ディメンションなし|
|TotalNumberOfExtents|はい|エクステントの合計数|Count|合計|データ エクステントの合計数|ディメンションなし|
|TotalNumberOfThrottledCommands|はい|スロットルされたコマンドの合計数|Count|合計|スロットルされたコマンドの合計数|CommandType|
|TotalNumberOfThrottledQueries|はい|スロットルされたクエリの合計数|Count|最大値|スロットルされたクエリの合計数|ディメンションなし|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActionLatency|はい|アクションの待機時間 |Seconds|Average|完了したワークフロー アクションの待機時間。|ディメンションなし|
|ActionsCompleted|はい|完了したアクション |Count|合計|完了したワークフロー アクションの数。|ディメンションなし|
|ActionsFailed|はい|失敗したアクション |Count|合計|失敗したワークフロー アクションの数。|ディメンションなし|
|ActionsSkipped|はい|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|ディメンションなし|
|ActionsStarted|はい|開始したアクション |Count|合計|開始したワークフロー アクションの数。|ディメンションなし|
|ActionsSucceeded|はい|成功したアクション |Count|合計|成功したワークフロー アクションの数。|ディメンションなし|
|ActionSuccessLatency|はい|成功したアクションの待機時間 |Seconds|Average|成功したワークフロー アクションの待機時間。|ディメンションなし|
|ActionThrottledEvents|はい|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|ディメンションなし|
|IntegrationServiceEnvironmentConnectorMemoryUsage|はい|統合サービス環境のコネクタ メモリ使用率|Percent|Average|統合サービス環境のコネクタ メモリ使用率。|ディメンションなし|
|IntegrationServiceEnvironmentConnectorProcessorUsage|はい|統合サービス環境のコネクタ プロセッサ使用率|Percent|Average|統合サービス環境のコネクタ プロセッサ使用率。|ディメンションなし|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|はい|統合サービス環境のワークフロー メモリ使用率|Percent|Average|統合サービス環境のワークフロー メモリ使用率。|ディメンションなし|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|はい|統合サービス環境のワークフロー プロセッサ使用率|Percent|Average|統合サービス環境のワークフロー プロセッサ使用率。|ディメンションなし|
|RunFailurePercentage|はい|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|ディメンションなし|
|RunLatency|はい|実行の待機時間|Seconds|Average|完了したワークフロー実行の待機時間。|ディメンションなし|
|RunsCancelled|はい|キャンセルされた実行|Count|合計|キャンセルされたワークフロー実行の数。|ディメンションなし|
|RunsCompleted|はい|完了した実行|Count|合計|完了したワークフロー実行の数。|ディメンションなし|
|RunsFailed|はい|失敗した実行|Count|合計|失敗したワークフロー実行の数。|ディメンションなし|
|RunsStarted|はい|開始した実行|Count|合計|開始したワークフロー実行の数。|ディメンションなし|
|RunsSucceeded|はい|成功した実行|Count|合計|成功したワークフロー実行の数。|ディメンションなし|
|RunStartThrottledEvents|はい|実行開始スロットル イベント数|Count|合計|ワークフロー実行開始スロットル イベントの数。|ディメンションなし|
|RunSuccessLatency|はい|成功した実行の待機時間|Seconds|Average|成功したワークフロー実行の待機時間。|ディメンションなし|
|RunThrottledEvents|はい|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|ディメンションなし|
|TriggerFireLatency|はい|起動されたトリガーの待機時間 |Seconds|Average|起動されたワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerLatency|はい|トリガーの待機時間 |Seconds|Average|完了したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggersCompleted|はい|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|ディメンションなし|
|TriggersFailed|はい|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|ディメンションなし|
|TriggersFired|はい|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|ディメンションなし|
|TriggersSkipped|はい|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|ディメンションなし|
|TriggersStarted|はい|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|ディメンションなし|
|TriggersSucceeded|はい|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|ディメンションなし|
|TriggerSuccessLatency|はい|成功したトリガーの待機時間 |Seconds|Average|成功したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerThrottledEvents|はい|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|ディメンションなし|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActionLatency|はい|アクションの待機時間 |Seconds|Average|完了したワークフロー アクションの待機時間。|ディメンションなし|
|ActionsCompleted|はい|完了したアクション |Count|合計|完了したワークフロー アクションの数。|ディメンションなし|
|ActionsFailed|はい|失敗したアクション |Count|合計|失敗したワークフロー アクションの数。|ディメンションなし|
|ActionsSkipped|はい|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|ディメンションなし|
|ActionsStarted|はい|開始したアクション |Count|合計|開始したワークフロー アクションの数。|ディメンションなし|
|ActionsSucceeded|はい|成功したアクション |Count|合計|成功したワークフロー アクションの数。|ディメンションなし|
|ActionSuccessLatency|はい|成功したアクションの待機時間 |Seconds|Average|成功したワークフロー アクションの待機時間。|ディメンションなし|
|ActionThrottledEvents|はい|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|ディメンションなし|
|BillableActionExecutions|はい|課金対象のアクションの実行|Count|合計|課金されるワークフロー アクションの実行数。|ディメンションなし|
|BillableTriggerExecutions|はい|課金対象のトリガーの実行|Count|合計|課金されるワークフロー トリガーの実行数。|ディメンションなし|
|BillingUsageNativeOperation|はい|ネイティブ操作実行の利用状況に応じた課金|Count|合計|請求対象となるネイティブ操作の実行回数。|ディメンションなし|
|BillingUsageStandardConnector|はい|Standard コネクタ実行の利用状況に応じた課金|Count|合計|請求対象となる Standard コネクタの実行回数。|ディメンションなし|
|BillingUsageStorageConsumption|はい|ストレージ使用実行の利用状況に応じた課金|Count|合計|請求対象となるストレージ使用の実行回数。|ディメンションなし|
|RunFailurePercentage|はい|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|ディメンションなし|
|RunLatency|はい|実行の待機時間|Seconds|Average|完了したワークフロー実行の待機時間。|ディメンションなし|
|RunsCancelled|はい|キャンセルされた実行|Count|合計|キャンセルされたワークフロー実行の数。|ディメンションなし|
|RunsCompleted|はい|完了した実行|Count|合計|完了したワークフロー実行の数。|ディメンションなし|
|RunsFailed|はい|失敗した実行|Count|合計|失敗したワークフロー実行の数。|ディメンションなし|
|RunsStarted|はい|開始した実行|Count|合計|開始したワークフロー実行の数。|ディメンションなし|
|RunsSucceeded|はい|成功した実行|Count|合計|成功したワークフロー実行の数。|ディメンションなし|
|RunStartThrottledEvents|はい|実行開始スロットル イベント数|Count|合計|ワークフロー実行開始スロットル イベントの数。|ディメンションなし|
|RunSuccessLatency|はい|成功した実行の待機時間|Seconds|Average|成功したワークフロー実行の待機時間。|ディメンションなし|
|RunThrottledEvents|はい|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|ディメンションなし|
|TotalBillableExecutions|はい|課金対象の実行の合計数|Count|合計|課金されるワークフローの実行数。|ディメンションなし|
|TriggerFireLatency|はい|起動されたトリガーの待機時間 |Seconds|Average|起動されたワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerLatency|はい|トリガーの待機時間 |Seconds|Average|完了したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggersCompleted|はい|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|ディメンションなし|
|TriggersFailed|はい|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|ディメンションなし|
|TriggersFired|はい|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|ディメンションなし|
|TriggersSkipped|はい|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|ディメンションなし|
|TriggersStarted|はい|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|ディメンションなし|
|TriggersSucceeded|はい|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|ディメンションなし|
|TriggerSuccessLatency|はい|成功したトリガーの待機時間 |Seconds|Average|成功したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerThrottledEvents|はい|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|ディメンションなし|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|アクティブなコア|はい|アクティブなコア|Count|Average|アクティブなコアの数|Scenario、ClusterName|
|アクティブなノード|はい|アクティブなノード|Count|Average|アクティブ ノードの数。 これらは、ジョブをアクティブに実行しているノードです。|Scenario、ClusterName|
|Cancel Requested Runs (キャンセルが要求された実行数)|はい|Cancel Requested Runs (キャンセルが要求された実行数)|Count|合計|このワークスペースに対してキャンセルが要求された実行の数。 実行のキャンセル要求が受信されたときに、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|Cancelled Runs (取り消された実行数)|はい|Cancelled Runs (取り消された実行数)|Count|合計|このワークスペースに対して取り消された実行の数。 実行が正常に取り消されたときに、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|完了した実行数|はい|完了した実行数|Count|合計|このワークスペースに対して正常に完了した実行の数。 実行が完了し、出力が収集されたときに、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|CpuUtilization|はい|CpuUtilization|Count|Average|CPU ノードの使用率 (%)。 使用率は 1 分間隔で報告されます。|Scenario、runId、NodeId、ClusterName|
|エラー|はい|エラー|Count|合計|このワークスペースの実行エラーの数。 実行時にエラーが発生するたびに、カウントが更新されます。|シナリオ|
|失敗した実行|はい|失敗した実行|Count|合計|このワークスペースに対して失敗した実行の数。 実行に失敗すると、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|Finalizing Runs (終了処理中の実行数)|はい|Finalizing Runs (終了処理中の実行数)|Count|合計|このワークスペースに対して終了処理状態になった実行の数。 実行は完了しているものの、出力の収集がまだ進行中の場合に、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|GpuMemoryUtilization|はい|GpuMemoryUtilization|Count|Average|GPU ノードのメモリ使用率 (%)。 使用率は 1 分間隔で報告されます。|Scenario、runId、NodeId、DeviceId、ClusterName|
|GpuUtilization|はい|GpuUtilization|Count|Average|GPU ノードの使用率 (%)。 使用率は 1 分間隔で報告されます。|Scenario、runId、NodeId、DeviceId、ClusterName|
|アイドル状態のコア|はい|アイドル状態のコア|Count|Average|アイドル状態のコアの数|Scenario、ClusterName|
|アイドル状態のノード|はい|アイドル状態のノード|Count|Average|アイドル状態のノードの数。 アイドル状態のノードは、ジョブを実行していないノードですが、使用可能な場合は新しいジョブを受け入れることができます。|Scenario、ClusterName|
|終了中のコア|はい|終了中のコア|Count|Average|終了中のコアの数|Scenario、ClusterName|
|終了中のノード|はい|終了中のノード|Count|Average|終了中のノードの数。 終了中のノードは、ジョブの処理を完了したばかりで、アイドル状態になるノードです。|Scenario、ClusterName|
|モデル デプロイ失敗|はい|モデル デプロイ失敗|Count|合計|このワークスペースで失敗したモデル デプロイの数|Scenario、StatusCode|
|モデル デプロイ開始|はい|モデル デプロイ開始|Count|合計|このワークスペースで開始されたモデル デプロイの数|シナリオ|
|モデル デプロイが成功しました|はい|モデル デプロイが成功しました|Count|合計|このワークスペースで成功したモデル デプロイの数|シナリオ|
|モデルの登録が失敗しました|はい|モデル登録失敗|Count|合計|このワークスペースで失敗したモデル登録の数|Scenario、StatusCode|
|モデル登録成功|はい|モデル登録成功|Count|合計|このワークスペースで成功したモデル登録の数|シナリオ|
|Not Responding Runs (応答していない実行数)|はい|Not Responding Runs (応答していない実行数)|Count|合計|このワークスペースに対して応答していない実行の数。 実行が応答していない状態になったときに、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|Not Started Runs (未開始の実行数)|はい|Not Started Runs (未開始の実行数)|Count|合計|このワークスペースに対して未開始状態の実行の数。 実行を作成するために要求が受信されたものの、実行情報がまだ設定されていない場合に、カウントが更新されます。 |Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|割り込まれたコア|はい|割り込まれたコア|Count|Average|割り込まれたコアの数|Scenario、ClusterName|
|割り込まれたノード|はい|割り込まれたノード|Count|Average|割り込まれたノードの数。 これらのノードは低優先度のノードであり、使用可能なノード プールから外されます。|Scenario、ClusterName|
|Preparing Runs (準備中の実行数)|はい|Preparing Runs (準備中の実行数)|Count|合計|このワークスペースに対して準備中の実行の数。 実行環境の準備中に実行が準備状態になると、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|Provisioning Runs (プロビジョニング中の実行数)|はい|Provisioning Runs (プロビジョニング中の実行数)|Count|合計|このワークスペースに対してプロビジョニング中の実行の数。 実行でのコンピューティング先の作成の待機中、またはプロビジョニング中に、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|Queued Runs (キューに入れられた実行数)|はい|Queued Runs (キューに入れられた実行数)|Count|合計|このワークスペースに対してキューに入れられた実行の数。 コンピューティング先で実行がキューに入れられたときに、カウントが更新されます。 必要なコンピューティング ノードの準備が整うまで待機しているときに発生する場合があります。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|クォータ使用率|はい|クォータ使用率|Count|Average|クォータ使用率 (%)|Scenario、ClusterName、VmFamilyName、VmPriority|
|開始した実行数|はい|開始した実行数|Count|合計|このワークスペースに対して実行されている実行の数。 必要なリソースに対して実行が開始されたときに、カウントが更新されます。|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|Starting Runs (開始中の実行数)|はい|Starting Runs (開始中の実行数)|Count|合計|このワークスペースに対して開始された実行の数。 実行の作成要求の後、および実行 ID などの実行情報が設定された後に、カウントが更新されます|Scenario、RunType、PublishedPipelineId、ComputeType、PipelineStepType、ExperimentName|
|コアの合計|はい|コアの合計|Count|Average|コアの合計数|Scenario、ClusterName|
|ノード総数|はい|ノード総数|Count|Average|ノードの合計数。 この合計には、アクティブ ノード、アイドル状態のノード、使用できないノード、割り込まれたノード、終了中のノードなどが含まれます|Scenario、ClusterName|
|使用できないコア|はい|使用できないコア|Count|Average|使用できないコアの数|Scenario、ClusterName|
|使用できないノード|はい|使用できないノード|Count|Average|使用できないノードの数。 使用できないノードは、いくつかの問題が解決されていないため、機能していません。 これらのノードは Azure によってリサイクルされます。|Scenario、ClusterName|
|警告|はい|警告|Count|合計|このワークスペースの実行警告の数。 実行時に警告が発生するたびに、カウントが更新されます。|シナリオ|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|API の可用性|ApiCategory、ApiName|
|使用法|いいえ|使用法|Count|Count|API 呼び出しの数|ApiCategory、ApiName、ResultType、ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AssetCount|はい|アセット数|Count|Average|現在のメディア サービス アカウントに既に作成されている資産の数|ディメンションなし|
|AssetQuota|はい|アセットのクォータ|Count|Average|現在のメディア サービス アカウントで許可されている資産の数|ディメンションなし|
|AssetQuotaUsedPercentage|はい|アセットのクォータの使用率|Percent|Average|現在のメディア サービス アカウントで使用されている資産の割合 (%)|ディメンションなし|
|ChannelsAndLiveEventsCount|はい|ライブ イベント数|Count|Average|現在のメディア サービス アカウントのライブ イベントの合計数|ディメンションなし|
|ContentKeyPolicyCount|はい|コンテンツ キー ポリシー数|Count|Average|現在のメディア サービス アカウントに既に作成されているコンテンツ キー ポリシーの数|ディメンションなし|
|ContentKeyPolicyQuota|はい|コンテンツ キー ポリシーのクォータ|Count|Average|現在のメディア サービス アカウントで許可されているコンテンツ キー ポリシーの数|ディメンションなし|
|ContentKeyPolicyQuotaUsedPercentage|はい|コンテンツ キー ポリシーのクォータの使用率|Percent|Average|現在のメディア サービス アカウントで使用されているコンテンツ キー ポリシーの割合 (%)|ディメンションなし|
|RunningChannelsAndLiveEventsCount|はい|実行中のライブ イベント数|Count|Average|現在のメディア サービス アカウントの実行中のライブ イベントの合計数|ディメンションなし|
|StreamingPolicyCount|はい|ストリーミング ポリシー数|Count|Average|現在のメディア サービス アカウントに既に作成されているストリーミング ポリシーの数|ディメンションなし|
|StreamingPolicyQuota|はい|ストリーミング ポリシーのクォータ|Count|Average|現在のメディア サービス アカウントで許可されているストリーミング ポリシーの数|ディメンションなし|
|StreamingPolicyQuotaUsedPercentage|はい|ストリーミング ポリシーのクォータの使用率|Percent|Average|現在のメディア サービス アカウントで使用されているストリーミング ポリシーの割合 (%)|ディメンションなし|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|IngestBitrate|はい|ライブ イベントの取り込みビットレート|BitsPerSecond|Average|ライブ イベントで取り込まれた受信ビットレート (ビット/秒)。|TrackName|
|IngestDriftValue|はい|ライブ イベントの取り込み誤差値|Seconds|最大値|取り込まれたコンテンツのタイムスタンプとシステム クロックの間の誤差。1 分あたりの秒数で計測されます。 0 以外の値は、取り込まれたコンテンツがシステム クロック時間よりも遅れて到着していることを示します。|TrackName|
|IngestLastTimestamp|はい|ライブ イベントの取り込みの最後のタイムスタンプ|ミリ秒|最大値|ライブ イベントで取り込まれた最後のタイムスタンプ。|TrackName|
|LiveOutputLastTimestamp|はい|最後の出力タイムスタンプ|ミリ秒|最大値|ライブ イベント出力のストレージに最後にアップロードされたフラグメントのタイムスタンプ。|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CPU|はい|CPU 使用率|Percent|Average|premium ストリーミング エンドポイントの CPU 使用率。 このデータは、standard ストリーミング エンドポイントでは使用できません。|VmId|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量 (バイト単位)。|OutputFormat|
|EgressBandwidth|いいえ|エグレス帯域幅|BitsPerSecond|Average|エグレス帯域幅 (ビット/秒)。|VmId|
|Requests|はい|Requests|Count|合計|ストリーミング エンドポイントへの要求。|OutputFormat、HttpStatusCode、ErrorCode|
|SuccessE2ELatency|はい|成功したエンド ツー エンドの待機時間|ミリ秒|Average|成功した要求の平均待機時間 (ミリ秒単位)。|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|はい|Active Rendering Sessions (アクティブなレンダリング セッション数)|Count|Average|アクティブなレンダリング セッションの合計数|SessionType、SDKVersion|
|AssetsConverted|はい|Assets Converted (変換された資産数)|Count|合計|変換された資産の合計数|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AnchorsCreated|はい|作成されたアンカー|Count|合計|作成されたアンカーの数|DeviceFamily、SDKVersion|
|AnchorsDeleted|はい|削除されたアンカー|Count|合計|削除されたアンカーの数|DeviceFamily、SDKVersion|
|AnchorsQueried|はい|クエリ対象のアンカー|Count|合計|クエリ対象の空間アンカーの数|DeviceFamily、SDKVersion|
|AnchorsUpdated|はい|更新されたアンカー|Count|合計|更新されたアンカーの数|DeviceFamily、SDKVersion|
|PosesFound|はい|検出されたポーズ|Count|合計|返されたポーズの数|DeviceFamily、SDKVersion|
|TotalDailyAnchors|はい|合計日次アンカー数|Count|Average|日単位のアンカーの合計数|DeviceFamily、SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|はい|Pool Allocated Size\(プールの割り当てサイズ\)|バイト|Average|このプールのプロビジョニングされたサイズ|ディメンションなし|
|VolumePoolAllocatedToVolumeThroughput|はい|プールの割り当てスループット|BytesPerSecond|Average|プールに属するすべてのボリュームのスループットの合計|ディメンションなし|
|VolumePoolAllocatedUsed|はい|ボリュームに割り当てられたプールのサイズ|バイト|Average|プールの使用済みの割り当てサイズ|ディメンションなし|
|VolumePoolProvisionedThroughput|はい|プールに対してプロビジョニングされたスループット|BytesPerSecond|Average|このプールのプロビジョニングされたスループット|ディメンションなし|
|VolumePoolTotalLogicalSize|はい|プールで消費されたサイズ|バイト|Average|プールに属するすべてのボリュームの論理サイズの合計|ディメンションなし|
|VolumePoolTotalSnapshotSize|はい|Total Snapshot size for the pool\(プールのスナップショット サイズの合計\)|バイト|Average|このプール内のすべてのボリュームのスナップショット サイズの合計|ディメンションなし|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AverageReadLatency|はい|読み取りの平均待機時間|MilliSeconds|Average|操作ごとの読み取りの平均待機時間 (ミリ秒単位)|ディメンションなし|
|AverageWriteLatency|はい|書き込みの平均待機時間|MilliSeconds|Average|操作ごとの書き込みの平均待機時間 (ミリ秒単位)|ディメンションなし|
|CbsVolumeBackupActive|はい|ボリューム バックアップが中断されているか|Count|Average|バックアップ ポリシーはボリュームに対して中断されていますか? 該当する場合は 1、それ以外の場合は 0 です。|ディメンションなし|
|CbsVolumeLogicalBackupBytes|はい|ボリューム バックアップのバイト数|バイト|Average|このボリュームに対してバックアップされた合計バイト数。|ディメンションなし|
|CbsVolumeOperationComplete|はい|ボリューム バックアップ操作が完了していますか|Count|Average|前回のボリュームのバックアップまたは復元操作は正常に完了しましたか? 該当する場合は 1、それ以外の場合は 0 です。|ディメンションなし|
|CbsVolumeOperationTransferredBytes|はい|ボリューム バックアップの最後に転送されたバイト数|バイト|Average|最後のバックアップまたは復元操作のために転送された合計バイト数。|ディメンションなし|
|CbsVolumeProtected|はい|ボリューム バックアップは有効か|Count|Average|ボリュームのバックアップは有効になっていますか? 該当する場合は 1、それ以外の場合は 0 です。|ディメンションなし|
|OtherThroughput|はい|その他のスループット|BytesPerSecond|Average|1 秒あたりのバイト単位のその他のスループット (つまり、読み取りや書き込みではない)|ディメンションなし|
|ReadIops|はい|読み取り IOPS|CountPerSecond|Average|1 秒あたりの読み取り入出力操作|ディメンションなし|
|ReadThroughput|はい|読み取りスループット|BytesPerSecond|Average|1 秒あたりの読み取りスループット (バイト単位)|ディメンションなし|
|TotalThroughput|はい|スループットの合計|BytesPerSecond|Average|1 秒あたりのすべてのスループットの合計 (バイト単位)|ディメンションなし|
|VolumeAllocatedSize|はい|ボリュームの割り当てサイズ|バイト|Average|ボリュームのプロビジョニングされたサイズ|ディメンションなし|
|VolumeConsumedSizePercentage|はい|ボリュームで消費されたサイズの割合|Percent|Average|スナップショットを含む、消費されたボリュームの割合。|ディメンションなし|
|VolumeLogicalSize|はい|ボリュームで消費されたサイズ|バイト|Average|ボリュームの論理サイズ (使用されているバイト)|ディメンションなし|
|VolumeSnapshotSize|はい|ボリューム スナップショットのサイズ|バイト|Average|ボリューム内のすべてのスナップショットのサイズ|ディメンションなし|
|WriteIops|はい|書き込み IOPS|CountPerSecond|Average|1 秒あたりの書き込み入出力操作|ディメンションなし|
|WriteThroughput|はい|書き込みスループット|BytesPerSecond|Average|1 秒あたりの書き込みスループット (バイト単位)|ディメンションなし|
|XregionReplicationHealthy|はい|Is volume replication status healthy\(ボリューム レプリケーションの状態は正常です\)|Count|Average|リレーションシップの状態 (1 または 0)。|ディメンションなし|
|XregionReplicationLagTime|はい|Volume replication lag time\(ボリューム レプリケーションのラグ タイム\)|Seconds|Average|ミラーのデータがソースより遅れている時間 (秒単位)。|ディメンションなし|
|XregionReplicationLastTransferDuration|はい|Volume replication last transfer duration\(ボリューム レプリケーションの最後の転送期間\)|Seconds|Average|最後の転送が完了するまでにかかった時間 (秒単位)。|ディメンションなし|
|XregionReplicationLastTransferSize|はい|Volume replication last transfer size\(ボリューム レプリケーションの最後の転送サイズ\)|バイト|Average|最後の転送の一部として転送されたバイトの合計数。|ディメンションなし|
|XregionReplicationRelationshipProgress|はい|Volume replication progress\(ボリューム レプリケーションの進行状況\)|バイト|Average|現在の転送操作で転送されたデータの総量。|ディメンションなし|
|XregionReplicationRelationshipTransferring|はい|Is volume replication transferring\(ボリューム レプリケーションは転送中です\)|Count|Average|ボリューム レプリケーションの状態が "転送中" かどうか。|ディメンションなし|
|XregionReplicationTotalTransferBytes|はい|Volume replication total transfer\(ボリューム レプリケーションの転送の合計\)|バイト|Average|リレーションシップに対して転送された累積バイト数。|ディメンションなし|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|いいえ|Application Gateway の合計時間|MilliSeconds|Average|要求が処理されその応答が送信されるのにかかった平均時間。 これは、Application Gateway が HTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの平均間隔として計算されます。 これには、通常、Application Gateway の処理時間、要求パケットと応答パケットがネットワーク経由で移動する時間、およびバックエンド サーバーが応答するまでの時間が含まれていることに注意することが重要です。|リスナー|
|AvgRequestCountPerHealthyHost|いいえ|正常なホストごとの 1 分あたりの要求|Count|Average|プール内の正常なバックエンド ホストごとの 1 分あたりの平均要求数|BackendSettingsPool|
|BackendConnectTime|いいえ|バックエンド接続時間|MilliSeconds|Average|バックエンド サーバーとの接続を確立するために費やされた時間|Listener、BackendServer、BackendPool、BackendHttpSetting|
|BackendFirstByteResponseTime|いいえ|バックエンド先頭バイト応答時間|MilliSeconds|Average|バックエンド サーバーへの接続の確立を開始してから応答ヘッダーの最初のバイトを受信するまでの時間間隔であり、バックエンド サーバーのおおよその処理時間です。|Listener、BackendServer、BackendPool、BackendHttpSetting|
|BackendLastByteResponseTime|いいえ|バックエンド最終バイト応答時間|MilliSeconds|Average|バックエンド サーバーへの接続の確立を開始してから応答本文の最後のバイトを受信するまでの時間間隔です。|Listener、BackendServer、BackendPool、BackendHttpSetting|
|BackendResponseStatus|はい|バックエンド応答の状態|Count|合計|バックエンド メンバーによって生成された HTTP 応答コードの数。 これには、Application Gateway によって生成された応答コードは含まれません。|BackendServer、BackendPool、BackendHttpSetting、HttpStatusGroup|
|BlockedCount|はい|Web アプリケーション ファイアウォールのブロックされた要求の規則配布|Count|合計|Web アプリケーション ファイアウォールのブロックされた要求の規則配布|RuleGroup、RuleId|
|BlockedReqCount|はい|Web アプリケーション ファイアウォールのブロックされた要求数|Count|合計|Web アプリケーション ファイアウォールのブロックされた要求数|ディメンションなし|
|BytesReceived|はい|受信バイト数|バイト|合計|クライアントから Application Gateway に送信されたバイトの合計数です|リスナー|
|BytesSent|はい|送信バイト数|バイト|合計|Application Gateway からクライアントに送信されたバイトの合計数です|リスナー|
|CapacityUnits|いいえ|現在の容量ユニット|Count|Average|使用された容量ユニット|ディメンションなし|
|ClientRtt|いいえ|クライアント RTT|MilliSeconds|Average|クライアントと Application Gateway の間の平均ラウンドトリップ時間。 このメトリックは、接続が確立され、受信確認が返されるまでにかかる時間を示します|リスナー|
|ComputeUnits|いいえ|現在のコンピューティング ユニット|Count|Average|消費されたコンピューティング ユニット|ディメンションなし|
|CpuUtilization|いいえ|CPU 使用率|Percent|Average|Application Gateway の現在の CPU 使用率|ディメンションなし|
|CurrentConnections|はい|現在の接続数|Count|合計|Application Gateway で確立された現在の接続の数|ディメンションなし|
|EstimatedBilledCapacityUnits|いいえ|推定課金対象容量ユニット|Count|Average|課金される推定容量ユニット|ディメンションなし|
|FailedRequests|はい|失敗した要求|Count|合計|Application Gateway が処理した失敗した要求の数|BackendSettingsPool|
|FixedBillableCapacityUnits|いいえ|固定請求可能容量ユニット|Count|Average|課金される最小容量ユニット|ディメンションなし|
|HealthyHostCount|はい|正常なホストの数|Count|Average|正常なバックエンド ホストの数|BackendSettingsPool|
|MatchedCount|はい|Web アプリケーション ファイアウォールの合計規則配布|Count|合計|受信トラフィックに対する Web アプリケーション ファイアウォールの合計規則配布|RuleGroup、RuleId|
|NewConnectionsPerSecond|いいえ|1 秒あたりの新しい接続数|CountPerSecond|Average|Application Gateway で確立された 1 秒あたりの新しい接続数|ディメンションなし|
|ResponseStatus|はい|応答の状態|Count|合計|Application Gateway によって返された HTTP 応答の状態|HttpStatusGroup|
|スループット|いいえ|スループット|BytesPerSecond|Average|Application Gateway が処理した 1 秒あたりのバイト数|ディメンションなし|
|TlsProtocol|はい|クライアント TLS プロトコル|Count|合計|Application Gateway との接続を確立したクライアントによって開始された TLS 要求と非 TLS 要求の数です。 TLS プロトコルの分布を表示するには、ディメンションの TLS プロトコルでフィルター処理します。|Listener、TlsProtocol|
|TotalRequests|はい|要求の合計数|Count|合計|Application Gateway が処理した成功した要求の数|BackendSettingsPool|
|UnhealthyHostCount|はい|異常なホストの数|Count|Average|異常なバックエンド ホストの数|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|はい|アプリケーション規則のヒット数|Count|合計|アプリケーション規則がヒットした回数|Status、Reason、Protocol|
|DataProcessed|はい|データ処理量|バイト|合計|ファイアウォールによって処理されたデータの総量|ディメンションなし|
|FirewallHealth|はい|ファイアウォールの正常性状態|Percent|Average|このファイアウォールの全体的な正常性を示します|状態、理由|
|NetworkRuleHit|はい|ネットワーク規則のヒット数|Count|合計|ネットワーク規則がヒットした回数|Status、Reason、Protocol|
|SNATPortUtilization|はい|SNAT ポート使用率|Percent|Average|現在使用されている送信 SNAT ポートの割合|Protocol|
|スループット|いいえ|スループット|BitsPerSecond|Average|このファイアウォールによって処理されたスループット|ディメンションなし|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|はい|BitsInPerSecond (受信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|ディメンションなし|
|BitsOutPerSecond (送信ビット数/秒)|はい|BitsOutPerSecond (送信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|ディメンションなし|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|QueryVolume|いいえ|クエリ数|Count|合計|DNS ゾーンで処理されたクエリの数|ディメンションなし|
|RecordSetCapacityUtilization|いいえ|レコード セットの容量使用率|Percent|最大値|DNS ゾーンで使用されるレコード セットの容量の割合|ディメンションなし|
|RecordSetCount|いいえ|レコード セット数|Count|最大値|DNS ゾーンのレコード セットの数|ディメンションなし|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ArpAvailability|はい|ARP の可用性|Percent|Average|MSEE からすべてのピアへの ARP の可用性。|PeeringType、Peer|
|BgpAvailability|はい|BGP の可用性|Percent|Average|MSEE からすべてのピアへの BGP の可用性。|PeeringType、Peer|
|BitsInPerSecond (受信ビット数/秒)|いいえ|BitsInPerSecond (受信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|PeeringType、DeviceRole|
|BitsOutPerSecond (送信ビット数/秒)|いいえ|BitsOutPerSecond (送信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|PeeringType、DeviceRole|
|GlobalReachBitsInPerSecond|いいえ|GlobalReachBitsInPerSecond|BitsPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|いいえ|GlobalReachBitsOutPerSecond|BitsPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|PeeredCircuitSKey|
|QosDropBitsInPerSecond|はい|DroppedInBitsPerSecond|BitsPerSecond|Average|1 秒あたりに破棄されたデータのイングレス ビット数|ディメンションなし|
|QosDropBitsOutPerSecond|はい|DroppedOutBitsPerSecond|BitsPerSecond|Average|1 秒あたりに破棄されたデータのエグレス ビット数|ディメンションなし|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|はい|BitsInPerSecond (受信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|ディメンションなし|
|BitsOutPerSecond (送信ビット数/秒)|はい|BitsOutPerSecond (送信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|ディメンションなし|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|いいえ|BitsInPerSecond (受信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|いいえ|BitsOutPerSecond (送信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|はい|ピアにアドバタイズされたルートの数 (プレビュー)|Count|最大値|ExpressRouteGateway によってピアにアドバタイズされたルートの数|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|はい|ピアから学習したルートの数 (プレビュー)|Count|最大値|ExpressRouteGateway によってピアから学習したルートの数|roleInstance|
|ExpressRouteGatewayCpuUtilization|はい|CPU 使用率 (プレビュー)|Count|Average|ExpressRoute ゲートウェイの CPU 使用率|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|いいえ|ルート変更の頻度 (プレビュー)|Count|合計|ExpressRoute ゲートウェイでのルート変更の頻度|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|いいえ|仮想ネットワーク内の VM の数 (プレビュー)|Count|最大値|仮想ネットワーク内の VM の数|ディメンションなし|
|ExpressRouteGatewayPacketsPerSecond|いいえ|1 秒あたりのパケット数 (プレビュー)|CountPerSecond|Average|ExpressRoute ゲートウェイのパケット数|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AdminState|はい|AdminState|Count|Average|ポートの管理者の状態|Link|
|LineProtocol|はい|LineProtocol|Count|Average|ポートの回線プロトコルの状態|Link|
|PortBitsInPerSecond|はい|BitsInPerSecond (受信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure へのイングレス ビット数|Link|
|PortBitsOutPerSecond|はい|BitsOutPerSecond (送信ビット数/秒)|BitsPerSecond|Average|1 秒あたりの Azure からのエグレス ビット数|Link|
|RxLightLevel|はい|RxLightLevel|Count|Average|DBM の Rx ライト レベル|Link、Lane|
|TxLightLevel|はい|TxLightLevel|Count|Average|DBM の Tx ライト レベル|Link、Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|はい|バックエンドの正常性の割合|Percent|Average|HTTP/S プロキシからバックエンドへの成功した正常性プローブの割合|Backend、BackendPool|
|BackendRequestCount|はい|バックエンド要求数|Count|合計|バックエンドに HTTP/S プロキシから送信された要求の数|HttpStatus、HttpStatusGroup、Backend|
|BackendRequestLatency|はい|バックエンド要求の待機時間|MilliSeconds|Average|HTTP/S プロキシによってバックエンドに要求が送信されてから、HTTP/S プロキシでバックエンドから最後の応答バイトが受信されるまでの算出時間|バックエンド|
|BillableResponseSize|はい|課金対象の応答サイズ|バイト|合計|HTTP/S プロキシからクライアントへの応答として送信された、課金対象のバイト数 (要求あたり最小 2 KB)。|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestCount|はい|要求数|Count|合計|HTTP/S プロキシによって処理されるクライアント要求の数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestSize|はい|要求サイズ|バイト|合計|HTTP/S プロキシにクライアントからの要求として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|ResponseSize|はい|応答サイズ|バイト|合計|クライアントに HTTP/S プロキシからの応答として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|TotalLatency|はい|合計待機時間|MilliSeconds|Average|HTTP/S プロキシによってクライアント要求が受信されてから、HTTP/S プロキシからの最後の応答バイトがクライアントで確認されるまでの算出時間|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|WebApplicationFirewallRequestCount|はい|Web アプリケーション ファイアウォール要求の数|Count|合計|Web アプリケーション ファイアウォールによって処理されるクライアント要求の数|PolicyName、RuleName、Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|いいえ|割り当てられた SNAT ポート|Count|Average|期間内に割り当てられた SNAT ポートの合計数|FrontendIPAddress、BackendIPAddress、ProtocolType、 |
|ByteCount|はい|Byte Count (バイト数)|バイト|合計|期間内に送信された合計バイト数|FrontendIPAddress、FrontendPort、Direction|
|DipAvailability|はい|正常性プローブの状態|Count|Average|期間あたりの Load Balancer 正常性プローブの状態平均|ProtocolType、BackendPort、FrontendIPAddress、FrontendPort、BackendIPAddress|
|PacketCount|はい|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|FrontendIPAddress、FrontendPort、Direction|
|SnatConnectionCount|はい|SNAT Connection Count (SNAT 接続数)|Count|合計|期間内に作成された新しい SNAT 接続の合計数|FrontendIPAddress、BackendIPAddress、ConnectionState|
|SYNCount|はい|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|FrontendIPAddress、FrontendPort、Direction|
|UsedSnatPorts|いいえ|使用された SNAT ポート|Count|Average|期間内に使用された SNAT ポートの合計数|FrontendIPAddress、BackendIPAddress、ProtocolType、 |
|VipAvailability|はい|データ パスの可用性|Count|Average|期間あたりの Load Balancer データ パスの可用性平均|FrontendIPAddress、FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ByteCount|はい|バイト|バイト|合計|期間内に送信された合計バイト数|Protocol、Direction|
|DatapathAvailability|はい|データパスの可用性 (プレビュー)|Count|Average|NAT ゲートウェイ データパスの可用性|ディメンションなし|
|PacketCount|はい|パケット|Count|合計|期間内に送信された合計パケット数|Protocol、Direction|
|PacketDropCount|はい|破棄されたパケット|Count|合計|破棄されたパケットの数|ディメンションなし|
|SNATConnectionCount|はい|SNAT Connection Count (SNAT 接続数)|Count|合計|同時アクティブ接続の合計数|Protocol、ConnectionState|
|TotalConnectionCount|はい|SNAT 接続数の合計|Count|合計|アクティブな SNAT 接続数の合計|Protocol|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BytesReceivedRate|はい|受信バイト数|バイト|合計|ネットワーク インターフェイスが受信したバイト数|ディメンションなし|
|BytesSentRate|はい|送信バイト数|バイト|合計|ネットワーク インターフェイスが送信したバイト数|ディメンションなし|
|PacketsReceivedRate|はい|受信したパケット数|Count|合計|ネットワーク インターフェイスが受信したパケット数|ディメンションなし|
|PacketsSentRate|はい|送信されたパケット数|Count|合計|ネットワーク インターフェイスが送信したパケット数|ディメンションなし|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|はい|Avg.ラウンドトリップ時間 (ms) (クラシック)|MilliSeconds|Average|ソースと接続先の間で送信された接続監視プローブのネットワークの平均ラウンド トリップ時間 (ミリ秒)|ディメンションなし|
|ChecksFailedPercent|はい|チェック失敗の割合|Percent|Average|失敗した接続監視チェックの割合|SourceAddress、SourceName、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName、SourceIP、DestinationIP、SourceSubnet、DestinationSubnet|
|ProbesFailedPercent|はい|失敗したプローブの割合 (クラシック)|Percent|Average|失敗した接続監視プローブの割合|ディメンションなし|
|RoundTripTimeMs|はい|ラウンドトリップ時間 (ミリ秒)|MilliSeconds|Average|接続監視チェックのラウンド トリップ時間 (ミリ秒)|SourceAddress、SourceName、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName、SourceIP、DestinationIP、SourceSubnet、DestinationSubnet|
|TestResult|はい|テスト結果|Count|Average|接続モニターのテスト結果|SourceAddress、SourceName、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName、TestResultCriterion、SourceIP、DestinationIP、SourceSubnet、DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|P2SBandwidth|はい|ゲートウェイの P2S 帯域幅|BytesPerSecond|Average|1 秒あたりのゲートウェイのポイント対サイト帯域幅の平均 (バイト単位)|ディメンションなし|
|P2SConnectionCount|はい|P2S 接続数|Count|合計|ゲートウェイのポイント対サイト接続の数|Protocol|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|QueryVolume|はい|クエリ数|Count|合計|プライベート DNS ゾーンで処理されたクエリの数|ディメンションなし|
|RecordSetCapacityUtilization|いいえ|レコード セットの容量使用率|Percent|最大値|プライベート DNS ゾーンで使用されるレコード セットの容量の割合|ディメンションなし|
|RecordSetCount|はい|レコード セット数|Count|最大値|プライベート DNS ゾーンのレコード セットの数|ディメンションなし|
|VirtualNetworkLinkCapacityUtilization|いいえ|Virtual Network Link Capacity Utilization (仮想ネットワーク リンク容量の使用率)|Percent|最大値|プライベート DNS ゾーンで使用される仮想ネットワーク リンクの容量の割合|ディメンションなし|
|VirtualNetworkLinkCount|はい|Virtual Network Link Count (仮想ネットワーク リンク数)|Count|最大値|プライベート DNS ゾーンにリンクされている仮想ネットワークの数|ディメンションなし|
|VirtualNetworkWithRegistrationCapacityUtilization|いいえ|Virtual Network Registration Link Capacity Utilization (仮想ネットワーク登録リンク容量の使用率)|Percent|最大値|プライベート DNS ゾーンで使用される自動登録容量を持つ仮想ネットワーク リンクの割合|ディメンションなし|
|VirtualNetworkWithRegistrationLinkCount|はい|Virtual Network Registration Link Count (仮想ネットワーク登録リンク数)|Count|最大値|自動登録が有効な状態でプライベート DNS ゾーンにリンクされる仮想ネットワークの数|ディメンションなし|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PEBytesIn|はい|入力バイト数|Count|合計|出力バイト数の合計|PrivateEndpointId|
|PEBytesOut|はい|出力バイト数|Count|合計|出力バイト数の合計|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PLSBytesIn|はい|入力バイト数|Count|合計|出力バイト数の合計|PrivateLinkServiceId|
|PLSBytesOut|はい|出力バイト数|Count|合計|出力バイト数の合計|PrivateLinkServiceId|
|PLSNatPortsUsage|はい|NAT ポートの使用状況|Percent|Average|NAT ポートの使用状況|PrivateLinkServiceId、PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ByteCount|はい|Byte Count (バイト数)|バイト|合計|期間内に送信された合計バイト数|Port、Direction|
|BytesDroppedDDoS|はい|DDoS 受信バイト破棄数|BytesPerSecond|最大値|DDoS 受信バイト破棄数|ディメンションなし|
|BytesForwardedDDoS|はい|DDoS 受信バイト転送数|BytesPerSecond|最大値|DDoS 受信バイト転送数|ディメンションなし|
|BytesInDDoS|はい|DDoS 受信バイト数|BytesPerSecond|最大値|DDoS 受信バイト数|ディメンションなし|
|DDoSTriggerSYNPackets|はい|DDoS 軽減をトリガーする受信 SYN パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 SYN パケット数|ディメンションなし|
|DDoSTriggerTCPPackets|はい|DDoS 軽減をトリガーする受信 TCP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 TCP パケット数|ディメンションなし|
|DDoSTriggerUDPPackets|はい|DDoS 軽減をトリガーする受信 UDP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 UDP パケット数|ディメンションなし|
|IfUnderDDoSAttack|はい|DDoS 攻撃中かどうか|Count|最大値|DDoS 攻撃中かどうか|ディメンションなし|
|PacketCount|はい|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|Port、Direction|
|PacketsDroppedDDoS|はい|DDoS 受信パケット破棄数|CountPerSecond|最大値|DDoS 受信パケット破棄数|ディメンションなし|
|PacketsForwardedDDoS|はい|DDoS 受信パケット転送数|CountPerSecond|最大値|DDoS 受信パケット転送数|ディメンションなし|
|PacketsInDDoS|はい|DDoS 受信パケット数|CountPerSecond|最大値|DDoS 受信パケット数|ディメンションなし|
|SynCount|はい|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|Port、Direction|
|TCPBytesDroppedDDoS|はい|DDoS 受信 TCP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 TCP バイト破棄数|ディメンションなし|
|TCPBytesForwardedDDoS|はい|DDoS 受信 TCP バイト転送数|BytesPerSecond|最大値|DDoS 受信 TCP バイト転送数|ディメンションなし|
|TCPBytesInDDoS|はい|DDoS 受信 TCP バイト数|BytesPerSecond|最大値|DDoS 受信 TCP バイト数|ディメンションなし|
|TCPPacketsDroppedDDoS|はい|DDoS 受信 TCP パケット破棄数|CountPerSecond|最大値|DDoS 受信 TCP パケット破棄数|ディメンションなし|
|TCPPacketsForwardedDDoS|はい|DDoS 受信 TCP パケット転送数|CountPerSecond|最大値|DDoS 受信 TCP パケット転送数|ディメンションなし|
|TCPPacketsInDDoS|はい|DDoS 受信 TCP パケット数|CountPerSecond|最大値|DDoS 受信 TCP パケット数|ディメンションなし|
|UDPBytesDroppedDDoS|はい|DDoS 受信 UDP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 UDP バイト破棄数|ディメンションなし|
|UDPBytesForwardedDDoS|はい|DDoS 受信 UDP バイト転送数|BytesPerSecond|最大値|DDoS 受信 UDP バイト転送数|ディメンションなし|
|UDPBytesInDDoS|はい|DDoS 受信 UDP バイト数|BytesPerSecond|最大値|DDoS 受信 UDP バイト数|ディメンションなし|
|UDPPacketsDroppedDDoS|はい|DDoS 受信 UDP パケット破棄数|CountPerSecond|最大値|DDoS 受信 UDP パケット破棄数|ディメンションなし|
|UDPPacketsForwardedDDoS|はい|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|CountPerSecond|最大値|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|ディメンションなし|
|UDPPacketsInDDoS|はい|DDoS 受信 UDP パケット数|CountPerSecond|最大値|DDoS 受信 UDP パケット数|ディメンションなし|
|VipAvailability|はい|データ パスの可用性|Count|Average|期間あたりの IP アドレス可用性平均|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|はい|Endpoint Status by Endpoint (エンドポイント別のエンドポイント状態)|Count|最大値|エンドポイントのプローブの状態が "有効" の場合は 1、それ以外の場合は 0。|EndpointName|
|QpsByEndpoint|はい|Queries by Endpoint Returned (エンドポイント別の返されたクエリ数)|Count|合計|指定された期間内に Traffic Manager エンドポイントが返された回数|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AverageBandwidth|はい|ゲートウェイの S2S 帯域幅|BytesPerSecond|Average|1 秒あたりのゲートウェイのサイト対サイト帯域幅の平均 (バイト単位)|ディメンションなし|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|はい|ピアにアドバタイズされたルートの数 (プレビュー)|Count|最大値|ExpressRouteGateway によってピアにアドバタイズされたルートの数|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|はい|ピアから学習したルートの数 (プレビュー)|Count|最大値|ExpressRouteGateway によってピアから学習したルートの数|roleInstance|
|ExpressRouteGatewayCpuUtilization|はい|CPU 使用率 (プレビュー)|Count|Average|ExpressRoute ゲートウェイの CPU 使用率|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|いいえ|ルート変更の頻度 (プレビュー)|Count|合計|ExpressRoute ゲートウェイでのルート変更の頻度|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|いいえ|仮想ネットワーク内の VM の数 (プレビュー)|Count|最大値|仮想ネットワーク内の VM の数|ディメンションなし|
|ExpressRouteGatewayPacketsPerSecond|いいえ|1 秒あたりのパケット数 (プレビュー)|CountPerSecond|Average|ExpressRoute ゲートウェイのパケット数|roleInstance|
|P2SBandwidth|はい|ゲートウェイの P2S 帯域幅|BytesPerSecond|Average|1 秒あたりのゲートウェイのポイント対サイト帯域幅の平均 (バイト単位)|ディメンションなし|
|P2SConnectionCount|はい|P2S 接続数|Count|最大値|ゲートウェイのポイント対サイト接続の数|Protocol|
|TunnelAverageBandwidth|はい|Tunnel Bandwidth (トンネル帯域幅)|BytesPerSecond|Average|1 秒あたりの平均トンネル帯域幅バイト数|ConnectionName、RemoteIP|
|TunnelEgressBytes|はい|Tunnel Egress Bytes (トンネル エグレス バイト数)|バイト|合計|トンネルの送信バイト数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|はい|Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの発信パケット破棄数|ConnectionName、RemoteIP|
|TunnelEgressPackets|はい|Tunnel Egress Packets (トンネル エグレス パケット数)|Count|合計|トンネルの送信パケット数|ConnectionName、RemoteIP|
|TunnelIngressBytes|はい|Tunnel Ingress Bytes (トンネル イングレス バイト数)|バイト|合計|トンネルの受信バイト数|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|はい|Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの受信パケット破棄数|ConnectionName、RemoteIP|
|TunnelIngressPackets|はい|Tunnel Ingress Packets (トンネル イングレス パケット数)|Count|合計|トンネルの受信パケット数|ConnectionName、RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|はい|VM への Ping のラウンド トリップ時間|MilliSeconds|Average|宛先 VM に送信された Ping のラウンド トリップ時間|SourceCustomerAddress、DestinationCustomerAddress|
|PingMeshProbesFailedPercent|はい|VM への Ping の失敗|Percent|Average|宛先 VM の送信された Ping の総数に対する失敗した Ping の数の割合 (%)|SourceCustomerAddress、DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PeeringAvailability|はい|BGP の可用性|Percent|Average|VirtualRouter とリモート ピア間の BGP の可用性|ピア|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AverageBandwidth|はい|ゲートウェイの S2S 帯域幅|BytesPerSecond|Average|1 秒あたりのゲートウェイのサイト対サイト帯域幅の平均 (バイト単位)|ディメンションなし|
|TunnelAverageBandwidth|はい|Tunnel Bandwidth (トンネル帯域幅)|BytesPerSecond|Average|1 秒あたりの平均トンネル帯域幅バイト数|ConnectionName、RemoteIP|
|TunnelEgressBytes|はい|Tunnel Egress Bytes (トンネル エグレス バイト数)|バイト|合計|トンネルの送信バイト数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|はい|Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの発信パケット破棄数|ConnectionName、RemoteIP|
|TunnelEgressPackets|はい|Tunnel Egress Packets (トンネル エグレス パケット数)|Count|合計|トンネルの送信パケット数|ConnectionName、RemoteIP|
|TunnelIngressBytes|はい|Tunnel Ingress Bytes (トンネル イングレス バイト数)|バイト|合計|トンネルの受信バイト数|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|はい|Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの受信パケット破棄数|ConnectionName、RemoteIP|
|TunnelIngressPackets|はい|Tunnel Ingress Packets (トンネル イングレス パケット数)|Count|合計|トンネルの受信パケット数|ConnectionName、RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|incoming|はい|受信メッセージ|Count|合計|成功した送信 API 呼び出しの数。 |ディメンションなし|
|incoming.all.failedrequests|はい|失敗したすべての受信要求|Count|合計|Notification Hub の失敗した受信要求の合計数|ディメンションなし|
|incoming.all.requests|はい|すべての受信要求|Count|合計|Notification Hub の受信要求の合計数|ディメンションなし|
|incoming.scheduled|はい|予定されていたプッシュ通知の送信|Count|合計|予定されていたプッシュ通知が取り消されました|ディメンションなし|
|incoming.scheduled.cancel|はい|予定されていたプッシュ通知が取り消されました|Count|合計|予定されていたプッシュ通知が取り消されました|ディメンションなし|
|installation.all|はい|インストール管理操作|Count|合計|インストール管理操作|ディメンションなし|
|installation.delete|はい|インストール操作の削除|Count|合計|インストール操作の削除|ディメンションなし|
|installation.get|はい|インストール操作の取得|Count|合計|インストール操作の取得|ディメンションなし|
|installation.patch|はい|インストール操作をパッチします|Count|合計|インストール操作をパッチします|ディメンションなし|
|installation.upsert|はい|インストール操作を作成または更新します|Count|合計|インストール操作を作成または更新します|ディメンションなし|
|notificationhub.pushes|はい|All Outgoing Notifications (すべての送信通知)|Count|合計|Notification Hub のすべての送信通知|ディメンションなし|
|outgoing.allpns.badorexpiredchannel|はい|適切または期限切れのチャネル エラー|Count|合計|登録内のチャネル/トークン/登録 ID が期限切れまたは無効なために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.channelerror|はい|チャネル エラー|Count|合計|チャネルが適切に調整されていないアプリまたは有効期限が切れたアプリに関連付けられていたために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.invalidpayload|はい|ペイロード エラー|Count|合計|PNS が不良ペイロード エラーを返しために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.pnserror|はい|外部通知システム エラー|Count|合計|PNS との通信に問題があったために失敗したプッシュの数 (認証の問題は除きます)。|ディメンションなし|
|outgoing.allpns.success|はい|正常な通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.apns.badchannel|はい|APNS 不良チャネル エラー|Count|合計|トークンが無効なために失敗したプッシュの数 (APNS 状態コード: 8)。|ディメンションなし|
|outgoing.apns.expiredchannel|はい|APNS 期限切れチャネル エラー|Count|合計|APNS フィードバック チャネルによって無効化されたトークンの数。|ディメンションなし|
|outgoing.apns.invalidcredentials|はい|APNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.apns.invalidnotificationsize|はい|APNS 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (APNS 状態コード: 7)。|ディメンションなし|
|outgoing.apns.pnserror|はい|APNS エラー|Count|合計|APNS との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.apns.success|はい|APNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.gcm.authenticationerror|はい|GCM 認証エラー|Count|合計|PNS が提供された資格情報を受け入れなかったために失敗したプッシュの数。資格情報がブロックされているか、アプリで SenderId が正しく構成されていません (GCM 結果: MismatchedSenderId)。|ディメンションなし|
|outgoing.gcm.badchannel|はい|GCM 不良チャネル エラー|Count|合計|登録内の registrationId が認識されなかったために失敗したプッシュの数 (GCM 結果: 無効な登録)。|ディメンションなし|
|outgoing.gcm.expiredchannel|はい|GCM 期限切れチャネル エラー|Count|合計|登録内の registrationId が有効期限切れだったために失敗したプッシュの数 (GCM 結果: NotRegistered)。|ディメンションなし|
|outgoing.gcm.invalidcredentials|はい|GCM 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.gcm.invalidnotificationformat|はい|GCM 無効通知形式|Count|合計|ペイロードの形式が正しくなかったために失敗したプッシュの数 (GCM 結果: InvalidDataKey または InvalidTtl)。|ディメンションなし|
|outgoing.gcm.invalidnotificationsize|はい|GCM 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (GCM 結果: MessageTooBig)。|ディメンションなし|
|outgoing.gcm.pnserror|はい|GCM エラー|Count|合計|GCM との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.gcm.success|はい|GCM 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.gcm.throttled|はい|GCM 調整済み通知|Count|合計|GCM によってこのアプリが調整されたために失敗したプッシュの数 (GCM 状態コード: 501-599、または結果: 利用不可)。|ディメンションなし|
|outgoing.gcm.wrongchannel|はい|GCM 誤チャネル エラー|Count|合計|登録内の registrationId が現在のアプリに関連付けられていないために失敗したプッシュの数 (GCM 結果: InvalidPackageName)。|ディメンションなし|
|outgoing.mpns.authenticationerror|はい|MPNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.mpns.badchannel|はい|MPNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (MPNS 状態: 404 - 見つかりません)。|ディメンションなし|
|outgoing.mpns.channeldisconnected|はい|MPNS チャネル切断|Count|合計|登録の ChannelURI が切断されたために失敗したプッシュの数 (MPNS 状態: 412 - 見つかりません)。|ディメンションなし|
|outgoing.mpns.dropped|はい|MPNS ドロップ通知|Count|合計|MPNS によって破棄されたプッシュの数 (MPNS 応答ヘッダー: X-NotificationStatus: QueueFull または Suppressed)。|ディメンションなし|
|outgoing.mpns.invalidcredentials|はい|MPNS 無効な資格情報|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.mpns.invalidnotificationformat|はい|MPNS 無効通知形式|Count|合計|通知のペイロードが長すぎたために失敗したプッシュの数。|ディメンションなし|
|outgoing.mpns.pnserror|はい|MPNS エラー|Count|合計|MPNS との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.mpns.success|はい|MPNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.mpns.throttled|はい|MPNS 調整済み通知|Count|合計|MPNS によってこのアプリが調整されているために失敗したプッシュの数 (WNS MPNS: 406 - 受信不可)。|ディメンションなし|
|outgoing.wns.authenticationerror|はい|WNS 認証エラー|Count|合計|資格情報無効または間違ったトークンによる Windows Live との通信エラーのため、通知が配信されません。|ディメンションなし|
|outgoing.wns.badchannel|はい|WNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (WNS 状態: 404 - 見つかりません)。|ディメンションなし|
|outgoing.wns.channeldisconnected|はい|WNS チャネル切断|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-DeviceConnectionStatus: disconnected)。|ディメンションなし|
|outgoing.wns.channelthrottled|はい|WNS 調整済みチャネル|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-NotificationStatus:channelThrottled)。|ディメンションなし|
|outgoing.wns.dropped|はい|WNS ドロップ通知|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected)。|ディメンションなし|
|outgoing.wns.expiredchannel|はい|WNS 期限切れチャネル エラー|Count|合計|ChannelURI の有効期限が切れているために失敗したプッシュの数 (WNS 状態: 410 - 削除)。|ディメンションなし|
|outgoing.wns.invalidcredentials|はい|WNS 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 (Windows Live は資格情報を認識しません)。|ディメンションなし|
|outgoing.wns.invalidnotificationformat|はい|WNS 無効通知形式|Count|合計|通知の形式が無効です (WNS 状態: 400)。 WNS ではすべての無効なペイロードを拒否するわけではないことに注意してください。|ディメンションなし|
|outgoing.wns.invalidnotificationsize|はい|WNS 無効通知サイズ エラー|Count|合計|通知ペイロードが大きすぎます (WNS 状態: 413)。|ディメンションなし|
|outgoing.wns.invalidtoken|はい|WNS 承認エラー (無効なトークン)|Count|合計|WNS に提供されたトークンは無効です (WNS 状態: 401 - 許可されていません)。|ディメンションなし|
|outgoing.wns.pnserror|はい|WNS エラー|Count|合計|WNS との通信エラーにより、通知が配信されません。|ディメンションなし|
|outgoing.wns.success|はい|WNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.wns.throttled|はい|WNS 調整済み通知|Count|合計|WNS によってこのアプリが調整されているために失敗したプッシュの数 (WNS 状態: 406 - 受信不可)。|ディメンションなし|
|outgoing.wns.tokenproviderunreachable|はい|WNS 承認エラー (到達不可)|Count|合計|Windows Live に到達できません。|ディメンションなし|
|outgoing.wns.wrongtoken|はい|WNS 承認エラー (間違ったトークン)|Count|合計|WNS に提供されたトークンは有効ですが、別のアプリケーション用です (WNS 状態: 403 - 禁止されています)。 これは、登録の ChannelURI が別のアプリケーションに関連付けられている場合に発生します。 クライアント アプリに関連付けられているアプリの資格情報が通知ハブ内にあることを確認します。|ディメンションなし|
|registration.all|はい|登録操作|Count|合計|成功した登録操作の数 (作成、更新、クエリ、および削除)。 |ディメンションなし|
|registration.create|はい|登録作成操作|Count|合計|成功した登録作成の数。|ディメンションなし|
|registration.delete|はい|登録削除操作|Count|合計|成功した登録削除の数。|ディメンションなし|
|registration.get|はい|登録読み取り操作|Count|合計|成功した登録クエリの数。|ディメンションなし|
|registration.update|はい|登録更新操作|Count|合計|成功した登録更新の数。|ディメンションなし|
|scheduled.pending|はい|予定されていた通知の保留|Count|合計|予定されていた通知の保留|ディメンションなし|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Average_% Available Memory|はい|% Available Memory|Count|Average|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Swap Space|はい|% Available Swap Space|Count|Average|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Committed Bytes In Use|はい|% Committed Bytes In Use|Count|Average|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% DPC Time|はい|% DPC Time|Count|Average|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Inodes|はい|% Free Inodes|Count|Average|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|はい|% Free Space|Count|Average|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Idle Time|はい|% Idle Time|Count|Average|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Interrupt Time|はい|% Interrupt Time|Count|Average|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% IO Wait Time|はい|% IO Wait Time|Count|Average|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Nice Time|はい|% Nice Time|Count|Average|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Privileged Time|はい|% Privileged Time|Count|Average|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|はい|% Processor Time|Count|Average|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Inodes|はい|% Used Inodes|Count|Average|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Memory|はい|% Used Memory|Count|Average|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Space|はい|% Used Space|Count|Average|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Swap Space|はい|% Used Swap Space|Count|Average|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% User Time|はい|% User Time|Count|Average|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes|はい|Available MBytes|Count|Average|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Memory|はい|Available MBytes Memory|Count|Average|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Swap|はい|Available MBytes Swap|Count|Average|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Read|はい|Avg.Disk sec/Read|Count|Average|Average_Avg. Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Transfer|はい|Avg.Disk sec/Transfer|Count|Average|Average_Avg. Disk sec/Transfer|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Write|はい|Avg.Disk sec/Write|Count|Average|Average_Avg. Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Received/sec|はい|Bytes Received/sec|Count|Average|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Sent/sec|はい|Bytes Sent/sec|Count|Average|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Total/sec|はい|Bytes Total/sec|Count|Average|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Current Disk Queue Length|はい|Current Disk Queue Length|Count|Average|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Read Bytes/sec|はい|Disk Read Bytes/sec|Count|Average|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|はい|Disk Reads/sec|Count|Average|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|はい|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Write Bytes/sec|はい|Disk Write Bytes/sec|Count|Average|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|はい|Disk Writes/sec|Count|Average|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|はい|Free Megabytes|Count|Average|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Physical Memory|はい|Free Physical Memory|Count|Average|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Space in Paging Files|はい|Free Space in Paging Files|Count|Average|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Virtual Memory|はい|Free Virtual Memory|Count|Average|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Logical Disk Bytes/sec|はい|Logical Disk Bytes/sec|Count|Average|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Reads/sec|はい|Page Reads/sec|Count|Average|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Writes/sec|はい|Page Writes/sec|Count|Average|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pages/sec|はい|Pages/sec|Count|Average|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct Privileged Time|はい|Pct Privileged Time|Count|Average|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct User Time|はい|Pct User Time|Count|Average|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Physical Disk Bytes/sec|はい|Physical Disk Bytes/sec|Count|Average|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processes|はい|処理|Count|Average|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processor Queue Length|はい|プロセッサ キューの長さ|Count|Average|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Size Stored In Paging Files|はい|Size Stored In Paging Files|Count|Average|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes|はい|Total Bytes|Count|Average|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Received|はい|Total Bytes Received|Count|Average|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Transmitted|はい|Total Bytes Transmitted|Count|Average|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Collisions|はい|Total Collisions|Count|Average|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Received|はい|Total Packets Received|Count|Average|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Transmitted|はい|Total Packets Transmitted|Count|Average|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Rx Errors|はい|Total Rx Errors|Count|Average|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Tx Errors|はい|Total Tx Errors|Count|Average|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Uptime|はい|Uptime|Count|Average|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used MBytes Swap Space|はい|Used MBytes Swap Space|Count|Average|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory kBytes|はい|Used Memory kBytes|Count|Average|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory MBytes|はい|Used Memory MBytes|Count|Average|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Users|はい|ユーザー|Count|Average|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Virtual Shared Memory|はい|Virtual Shared Memory|Count|Average|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Event|はい|Event|Count|Average|Event|Source、EventLog、Computer、EventCategory、EventLevel、EventLevelName、EventID|
|Heartbeat|はい|Heartbeat|Count|合計|Heartbeat|Computer、OSType、Version、SourceComputerId|
|更新|はい|更新|Count|Average|更新|Computer、Product、Classification、UpdateState、Optional、Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|EgressTrafficRate|はい|エグレス トラフィック速度|BitsPerSecond|Average|エグレス トラフィック速度 (ビット/秒)|ConnectionId、SessionIp、TrafficClass|
|IngressTrafficRate|はい|イグレス トラフィック速度|BitsPerSecond|Average|イグレス トラフィック速度 (ビット/秒)|ConnectionId、SessionIp、TrafficClass|
|SessionAvailability|はい|セッションの可用性|Count|Average|ピアリング セッションの可用性|ConnectionId、SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PrefixLatency|はい|プレフィックスの待機時間|ミリ秒|Average|プレフィックスの待機時間の中央値|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|memory_metric|はい|メモリ (Gen1)|バイト|Average|メモリ。 範囲: 0-3 GB (A1)、0-5 GB (A2)、0-10 GB (A3)、0-25 GB (A4)、0-50 GB (A5)、0-100 GB (A6)。 Power BI Embedded Generation 1 のリソースに対してのみサポートされます。|ディメンションなし|
|memory_thrashing_metric|はい|メモリ スラッシング (データセット) (Gen1)|Percent|Average|平均的なメモリ スラッシング。 Power BI Embedded Generation 1 のリソースに対してのみサポートされます。|ディメンションなし|
|qpu_high_utilization_metric|はい|高い QPU 使用率 (Gen1)|Count|合計|過去 1 分間の高い QPU 使用率。QPU 使用率が高い場合は 1、それ以外の場合は 0。 Power BI Embedded Generation 1 のリソースに対してのみサポートされます。|ディメンションなし|
|QueryDuration|はい|クエリ実行時間 (データセット) (Gen1)|ミリ秒|Average|最後の間隔での DAX クエリ実行時間。 Power BI Embedded Generation 1 のリソースに対してのみサポートされます。|ディメンションなし|
|QueryPoolJobQueueLength|はい|クエリ プールのジョブ キューの長さ (データセット) (Gen1)|Count|Average|クエリ スレッド プールのキューに登録されているジョブの数。 Power BI Embedded Generation 1 のリソースに対してのみサポートされます。|ディメンションなし|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ScanCancelled|はい|Scan Cancelled (キャンセルされたスキャン)|Count|合計|取り消されたスキャンの数を示します。|ResourceId|
|ScanCompleted|はい|Scan Completed (完了したスキャン)|Count|合計|正常に完了したスキャンの数を示します。|ResourceId|
|ScanFailed|はい|Scan Failed (失敗したスキャン)|Count|合計|失敗したスキャンの数を示します。|ResourceId|
|ScanTimeTaken|はい|Scan time taken (スキャンの所要時間)|Seconds|合計|スキャンの合計時間を示します (秒単位)。|ResourceId|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ScanCancelled|はい|Scan Cancelled (キャンセルされたスキャン)|Count|合計|取り消されたスキャンの数を示します。||
|ScanCompleted|はい|Scan Completed (完了したスキャン)|Count|合計|正常に完了したスキャンの数を示します。||
|ScanFailed|はい|Scan Failed (失敗したスキャン)|Count|合計|失敗したスキャンの数を示します。||
|ScanTimeTaken|はい|Scan time taken (スキャンの所要時間)|Seconds|合計|スキャンの合計時間を示します (秒単位)。||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveConnections|いいえ|ActiveConnections|Count|合計|Microsoft.Relay の ActiveConnections の合計。|EntityName|
|ActiveListeners|いいえ|ActiveListeners|Count|合計|Microsoft.Relay の ActiveListeners の合計。|EntityName|
|BytesTransferred|はい|BytesTransferred|バイト|合計|Microsoft.Relay の BytesTransferred の合計。|EntityName|
|ListenerConnections-ClientError|いいえ|ListenerConnections-ClientError|Count|合計|Microsoft.Relay の ListenerConnections での ClientError.|EntityName、OperationResult|
|ListenerConnections-ServerError|いいえ|ListenerConnections-ServerError|Count|合計|Microsoft.Relay の ListenerConnections での ServerError.|EntityName、OperationResult|
|ListenerConnections-Success|いいえ|ListenerConnections-Success|Count|合計|Microsoft.Relay の正常な ListenerConnections.|EntityName、OperationResult|
|ListenerConnections-TotalRequests|いいえ|ListenerConnections-TotalRequests|Count|合計|Microsoft.Relay の ListenerConnections の合計。|EntityName|
|ListenerDisconnects|いいえ|ListenerDisconnects|Count|合計|Microsoft.Relay の ListenerDisconnects の合計。|EntityName|
|SenderConnections-ClientError|いいえ|SenderConnections-ClientError|Count|合計|Microsoft.Relay の SenderConnections での ClientError.|EntityName、OperationResult|
|SenderConnections-ServerError|いいえ|SenderConnections-ServerError|Count|合計|Microsoft.Relay の SenderConnections での ServerError.|EntityName、OperationResult|
|SenderConnections-Success|いいえ|SenderConnections-Success|Count|合計|Microsoft.Relay の正常な SenderConnections.|EntityName、OperationResult|
|SenderConnections-TotalRequests|いいえ|SenderConnections-TotalRequests|Count|合計|Microsoft.Relay の SenderConnections 要求の合計。|EntityName|
|SenderDisconnects|いいえ|SenderDisconnects|Count|合計|Microsoft.Relay の SenderDisconnects の合計。|EntityName|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Latency|はい|HTTP 受信要求の待機時間データ|Count|Average|HTTP 受信要求の待機時間データ|Method、Namespace、RequestRegion、ResourceType、Microsoft.SubscriptionId|
|トラフィック|はい|トラフィック|Count|Average|HTTP トラフィック|RequestRegion、StatusCode、StatusCodeClass、ResourceType、Namespace、Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|SearchLatency|はい|検索の待機時間|Seconds|Average|検索サービスの平均検索待機時間|ディメンションなし|
|SearchQueriesPerSecond|はい|1 秒あたりの検索クエリ数|CountPerSecond|Average|検索サービスの 1 秒あたりの検索クエリ数|ディメンションなし|
|ThrottledSearchQueriesPercentage|はい|スロットルされた検索クエリの割合|Percent|Average|検索サービスのスロットルされた検索クエリの割合|ディメンションなし|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveConnections|いいえ|ActiveConnections|Count|合計|Microsoft.ServiceBus のアクティブな接続の合計数。||
|ActiveMessages|いいえ|キュー/トピック内のアクティブなメッセージの数。|Count|Average|キュー/トピック内のアクティブなメッセージの数。|EntityName|
|ConnectionsClosed|いいえ|切断された接続数。|Count|Average|Microsoft.ServiceBus の切断された接続数。|EntityName|
|ConnectionsOpened|いいえ|開かれている接続数。|Count|Average|Microsoft.ServiceBus の開かれている接続数。|EntityName|
|CPUXNS|いいえ|CPU (非推奨)|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック。 このメトリックは非推奨です。 代わりに CPU メトリック (NamespaceCpuUsage) を使用してください。|[レプリカ]|
|DeadletteredMessages|いいえ|キュー/トピック内の配信不能メッセージの数。|Count|Average|キュー/トピック内の配信不能メッセージの数。|EntityName|
|IncomingMessages|はい|受信メッセージ|Count|合計|Microsoft.ServiceBus の受信メッセージ数。|EntityName|
|IncomingRequests|はい|受信要求|Count|合計|Microsoft.ServiceBus の受信要求数。|EntityName|
|メッセージ|いいえ|キュー/トピック内のメッセージの数。|Count|Average|キュー/トピック内のメッセージの数。|EntityName|
|NamespaceCpuUsage|いいえ|CPU|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック。|[レプリカ]|
|NamespaceMemoryUsage|いいえ|メモリ使用量|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック。|[レプリカ]|
|OutgoingMessages|はい|送信メッセージ|Count|合計|Microsoft.ServiceBus の送信メッセージ数。|EntityName|
|ScheduledMessages|いいえ|キュー/トピック内のスケジュール済みメッセージの数。|Count|Average|キュー/トピック内のスケジュール済みメッセージの数。|EntityName|
|ServerErrors|いいえ|サーバー エラー。|Count|合計|Microsoft.ServiceBus のサーバー エラー数。|EntityName、OperationResult|
|サイズ|いいえ|サイズ|バイト|Average|キュー/トピックのサイズ (バイト単位)。|EntityName|
|SuccessfulRequests|いいえ|成功した要求|Count|合計|名前空間の成功した要求の総数|EntityName、OperationResult|
|ThrottledRequests|いいえ|スロットルされた要求数。|Count|合計|Microsoft.ServiceBus のスロットルされた要求数。|EntityName、OperationResult|
|UserErrors|いいえ|ユーザー エラー数。|Count|合計|Microsoft.ServiceBus のユーザー エラー数。|EntityName、OperationResult|
|WSXNS|いいえ|メモリ使用量 (非推奨)|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック。 このメトリックは非推奨です。 代わりにメモリ使用量 (NamespaceMemoryUsage) メトリックを使用してください。|[レプリカ]|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActualCpu|いいえ|ActualCpu|Count|Average|実際の CPU 使用率 (ミリコア単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|いいえ|ActualMemory|バイト|Average|実際のメモリ使用量 (MB 単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedCpu|いいえ|AllocatedCpu|Count|Average|このコンテナーに割り当てられた CPU (ミリコア単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|いいえ|AllocatedMemory|バイト|Average|このコンテナーに割り当てられたメモリ (MB 単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|いいえ|ApplicationStatus|Count|Average|Service Fabric Mesh アプリケーションの状態|ApplicationName、Status|
|ContainerStatus|いいえ|ContainerStatus|Count|Average|Service Fabric Mesh アプリケーションのコンテナーの状態|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|CpuUtilization|いいえ|CpuUtilization|Percent|Average|AllocatedCpu の割合としてのこのコンテナーの CPU の 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|いいえ|MemoryUtilization|Percent|Average|AllocatedCpu の割合としてのこのコンテナーの CPU の 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|RestartCount|いいえ|RestartCount|Count|Average|Service Fabric Mesh アプリケーションのコンテナーの再起動数|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|
|ServiceReplicaStatus|いいえ|ServiceReplicaStatus|Count|Average|Service Fabric Mesh アプリケーションのサービス レプリカの正常性状態|ApplicationName、Status、ServiceName、ServiceReplicaName|
|ServiceStatus|いいえ|ServiceStatus|Count|Average|Service Fabric Mesh アプリケーションのサービスの正常性状態|ApplicationName、Status、ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ConnectionCount|はい|Connection Count (接続数)|Count|最大値|ユーザー接続の量。|エンドポイント|
|InboundTraffic|はい|受信トラフィック|バイト|合計|サービスの受信トラフィック|ディメンションなし|
|MessageCount|はい|メッセージ数|Count|合計|メッセージの総量。|ディメンションなし|
|OutboundTraffic|はい|送信トラフィック|バイト|合計|サービスの送信トラフィック|ディメンションなし|
|SystemErrors|はい|System Errors (システム エラー)|Percent|最大値|システム エラーの割合|ディメンションなし|
|UserErrors|はい|User Errors (ユーザー エラー)|Percent|最大値|ユーザー エラーの割合|ディメンションなし|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|avg_cpu_percent|はい|平均 CPU の割合|Percent|Average|平均 CPU の割合|ディメンションなし|
|io_bytes_read|はい|読み取り IO バイト|バイト|Average|読み取り IO バイト|ディメンションなし|
|io_bytes_written|はい|書き込み IO バイト|バイト|Average|書き込み IO バイト|ディメンションなし|
|io_requests|はい|IO 要求数|Count|Average|IO 要求数|ディメンションなし|
|reserved_storage_mb|はい|予約済みストレージ スペース|Count|Average|予約済みストレージ スペース|ディメンションなし|
|storage_space_used_mb|はい|使用済みストレージ スペース|Count|Average|使用済みストレージ スペース|ディメンションなし|
|virtual_core_count|はい|仮想コア数|Count|Average|仮想コア数|ディメンションなし|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|active_queries|はい|アクティブなクエリ|Count|合計|すべてのワークロード グループにおけるアクティブなクエリの数。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|allocated_data_storage|はい|割り当て済みのデータ領域|バイト|Average|割り当て済みのデータ ストレージ。 データ ウェアハウスには適用されません。|ディメンションなし|
|app_cpu_billed|はい|課金されるアプリ CPU|Count|合計|課金されるアプリ CPU。 サーバーレス データベースに適用されます。|ディメンションなし|
|app_cpu_percent|はい|アプリ CPU の割合|Percent|Average|アプリ CPU の割合。 サーバーレス データベースに適用されます。|ディメンションなし|
|app_memory_percent|はい|アプリ メモリの割合|Percent|Average|メモリの割合 (%)。 サーバーレス データベースに適用されます。|ディメンションなし|
|base_blob_size_bytes|はい|Base blob storage size (ベース Blob ストレージ サイズ)|バイト|最大値|ベース Blob ストレージ サイズ。 ハイパースケール データベースに適用されます。|ディメンションなし|
|blocked_by_firewall|はい|ファイアウォールによってブロックされる|Count|合計|ファイアウォールによってブロックされる|ディメンションなし|
|cache_hit_percent|はい|キャッシュ ヒットの割合|Percent|最大値|キャッシュ ヒットの割合。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|cache_used_percent|はい|使用されたキャッシュの割合|Percent|最大値|使用されたキャッシュの割合。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|connection_failed|はい|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|connection_successful|はい|成功した接続|Count|合計|成功した接続|ディメンションなし|
|cpu_limit|はい|CPU 制限|Count|Average|CPU 制限。 仮想コア ベースのデータベースに適用されます。|ディメンションなし|
|cpu_percent|はい|CPU の割合|Percent|Average|CPU の割合|ディメンションなし|
|cpu_used|はい|使用された CPU|Count|Average|使用された CPU。 仮想コア ベースのデータベースに適用されます。|ディメンションなし|
|deadlock|はい|デッドロック|Count|合計|デッドロック。 データ ウェアハウスには適用されません。|ディメンションなし|
|diff_backup_size_bytes|はい|差分バックアップ ストレージ サイズ|バイト|最大値|累積的な差分バックアップ ストレージ サイズ。 仮想コア ベースのデータベースに適用されます。 ハイパースケール データベースには適用されません。|ディメンションなし|
|dtu_consumption_percent|はい|DTU の割合|Percent|Average|DTU の割合。 DTU ベースのデータベースに適用されます。|ディメンションなし|
|dtu_limit|はい|DTU の上限|Count|Average|DTU の上限。 DTU ベースのデータベースに適用されます。|ディメンションなし|
|dtu_used|はい|使用された DTU|Count|Average|使用された DTU。 DTU ベースのデータベースに適用されます。|ディメンションなし|
|dwu_consumption_percent|はい|DWU の割合|Percent|最大値|DWU の割合。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|dwu_limit|はい|DWU 上限|Count|最大値|DWU 上限。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|dwu_used|はい|使用済み DWU|Count|最大値|使用された DWU。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|full_backup_size_bytes|はい|完全バックアップ ストレージ サイズ|バイト|最大値|累積的な完全バックアップ ストレージ サイズ。 仮想コア ベースのデータベースに適用されます。 ハイパースケール データベースには適用されません。|ディメンションなし|
|local_tempdb_usage_percent|はい|ローカル tempdb の割合|Percent|Average|ローカル tempdb の割合。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|log_backup_size_bytes|はい|ログ バックアップ ストレージ サイズ|バイト|最大値|累積的なログ バックアップ ストレージ サイズ。 仮想コア ベースおよびハイパースケール データベースに適用されます。|ディメンションなし|
|log_write_percent|はい|ログ IO の割合|Percent|Average|ログ IO の割合。 データ ウェアハウスには適用されません。|ディメンションなし|
|memory_usage_percent|はい|メモリの割合|Percent|最大値|メモリの割合。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|physical_data_read_percent|はい|データ IO の割合|Percent|Average|データ IO の割合|ディメンションなし|
|queued_queries|はい|キューに置かれたクエリ|Count|合計|すべてのワークロード グループにおけるキューに置かれたクエリの数。 データ ウェアハウスにのみ適用されます。|ディメンションなし|
|sessions_percent|はい|セッションの割合|Percent|Average|セッションの割合。 データ ウェアハウスには適用されません。|ディメンションなし|
|snapshot_backup_size_bytes|はい|Snapshot backup storage size (スナップショット バックアップ ストレージ サイズ)|バイト|最大値|累積的なスナップショット バックアップ ストレージ サイズ。 ハイパースケール データベースに適用されます。|ディメンションなし|
|sqlserver_process_core_percent|はい|SQL Server プロセス コアの割合|Percent|最大値|SQL DB プロセスに対する CPU 使用率 (%)。 データ ウェアハウスには適用されません。|ディメンションなし|
|sqlserver_process_memory_percent|はい|SQL Server プロセス メモリの割合|Percent|最大値|SQL DB プロセスに対するメモリ使用率 (%)。 データ ウェアハウスには適用されません。|ディメンションなし|
|storage|はい|使用済みのデータ領域|バイト|最大値|使用済みのデータ領域。 データ ウェアハウスには適用されません。|ディメンションなし|
|storage_percent|はい|使用済みのデータ領域の割合|Percent|最大値|使用済みのデータ領域の割合。 データ ウェアハウスまたはハイパースケール データベースには適用されません。|ディメンションなし|
|tempdb_data_size|はい|Tempdb データ ファイル サイズ (KB)|Count|最大値|tempdb データ ファイルで使用されている領域 (キロバイト単位) データ ウェアハウスには適用されません。|ディメンションなし|
|tempdb_log_size|はい|Tempdb ログ ファイル サイズ (KB)|Count|最大値|tempdb トランザクション ログ ファイルで使用されている領域 (キロバイト単位) データ ウェアハウスには適用されません。|ディメンションなし|
|tempdb_log_used_percent|はい|Tempdb ログ使用率|Percent|最大値|tempdb トランザクション ログ ファイルで使用されている領域の割合 データ ウェアハウスには適用されません。|ディメンションなし|
|wlg_active_queries|はい|Workload group active queries (ワークロード グループのアクティブなクエリ)|Count|合計|ワークロード グループ内のアクティブなクエリ数。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|wlg_active_queries_timeouts|はい|Workload group query timeouts (ワークロード グループのクエリのタイムアウト)|Count|合計|ワークロード グループに対するタイムアウトしたクエリの数。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|wlg_allocation_relative_to_system_percent|はい|Workload group allocation by system percent (システム割合別のワークロード グループの割り当て)|Percent|最大値|ワークロード グループあたりのシステム全体に対するリソースの割り当て比率。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|はい|Workload group allocation by cap resource percent (上限リソース割合別のワークロード グループの割り当て)|Percent|最大値|ワークロード グループごとの指定された上限リソースに対するリソースの割り当て比率。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|wlg_effective_cap_resource_percent|はい|Effective cap resource percent (有効な上限リソース割合)|Percent|最大値|他のワークロード グループに割り当てられた有効な最小リソース割合が考慮された、ワークロード グループに許容されるリソースの割合に対するハード制限。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|wlg_effective_min_resource_percent|はい|Effective min resource percent (有効な最小リソース割合)|Percent|最大値|サービス レベルの最小値が考慮された、ワークロード グループ用に予約および分離されるリソースの最小割合。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|wlg_queued_queries|はい|Workload group queued queries (ワークロード グループのキューに登録されたクエリ)|Count|合計|ワークロード グループ内のキューに入れられたクエリ数。 データ ウェアハウスにのみ適用されます。|WorkloadGroupName、IsUserDefined|
|workers_percent|はい|ワーカーの割合|Percent|Average|ワーカーの割合。 データ ウェアハウスには適用されません。|ディメンションなし|
|xtp_storage_percent|はい|インメモリ OLTP ストレージの割合|Percent|Average|インメモリ OLTP ストレージの割合。 データ ウェアハウスには適用されません。|ディメンションなし|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|allocated_data_storage|はい|割り当て済みのデータ領域|バイト|Average|割り当て済みのデータ領域|ディメンションなし|
|allocated_data_storage_percent|はい|割り当て済みのデータ領域の割合|Percent|最大値|割り当て済みのデータ領域の割合|ディメンションなし|
|cpu_limit|はい|CPU 制限|Count|Average|CPU 制限。 仮想コア ベースのエラスティック プールに適用されます。|ディメンションなし|
|cpu_percent|はい|CPU の割合|Percent|Average|CPU の割合|ディメンションなし|
|cpu_used|はい|使用された CPU|Count|Average|使用された CPU。 仮想コア ベースのエラスティック プールに適用されます。|ディメンションなし|
|database_allocated_data_storage|いいえ|割り当て済みのデータ領域|バイト|Average|割り当て済みのデータ領域|DatabaseResourceId|
|database_cpu_limit|いいえ|CPU 制限|Count|Average|CPU 制限|DatabaseResourceId|
|database_cpu_percent|いいえ|CPU の割合|Percent|Average|CPU の割合|DatabaseResourceId|
|database_cpu_used|いいえ|使用された CPU|Count|Average|使用された CPU|DatabaseResourceId|
|database_dtu_consumption_percent|いいえ|DTU の割合|Percent|Average|DTU の割合|DatabaseResourceId|
|database_eDTU_used|いいえ|使用済み eDTU|Count|Average|使用済み eDTU|DatabaseResourceId|
|database_log_write_percent|いいえ|ログ IO の割合|Percent|Average|ログ IO の割合|DatabaseResourceId|
|database_physical_data_read_percent|いいえ|データ IO の割合|Percent|Average|データ IO の割合|DatabaseResourceId|
|database_sessions_percent|いいえ|セッションの割合|Percent|Average|セッションの割合|DatabaseResourceId|
|database_storage_used|いいえ|使用済みのデータ領域|バイト|Average|使用済みのデータ領域|DatabaseResourceId|
|database_workers_percent|いいえ|ワーカーの割合|Percent|Average|ワーカーの割合|DatabaseResourceId|
|dtu_consumption_percent|はい|DTU の割合|Percent|Average|DTU の割合。 DTU ベースのエラスティック プールに適用されます。|ディメンションなし|
|eDTU_limit|はい|eDTU 制限|Count|Average|eDTU 制限。 DTU ベースのエラスティック プールに適用されます。|ディメンションなし|
|eDTU_used|はい|使用済み eDTU|Count|Average|使用された eDTU。 DTU ベースのエラスティック プールに適用されます。|ディメンションなし|
|log_write_percent|はい|ログ IO の割合|Percent|Average|ログ IO の割合|ディメンションなし|
|physical_data_read_percent|はい|データ IO の割合|Percent|Average|データ IO の割合|ディメンションなし|
|sessions_percent|はい|セッションの割合|Percent|Average|セッションの割合|ディメンションなし|
|sqlserver_process_core_percent|はい|SQL Server プロセス コアの割合|Percent|最大値|SQL DB プロセスに対する CPU 使用率 (%)。 エラスティック プールに適用されます。|ディメンションなし|
|sqlserver_process_memory_percent|はい|SQL Server プロセス メモリの割合|Percent|最大値|SQL DB プロセスに対するメモリ使用率 (%)。 エラスティック プールに適用されます。|ディメンションなし|
|storage_limit|はい|データの最大サイズ|バイト|Average|データの最大サイズ|ディメンションなし|
|storage_percent|はい|使用済みのデータ領域の割合|Percent|Average|使用済みのデータ領域の割合|ディメンションなし|
|storage_used|はい|使用済みのデータ領域|バイト|Average|使用済みのデータ領域|ディメンションなし|
|tempdb_data_size|はい|Tempdb データ ファイル サイズ (KB)|Count|最大値|tempdb データ ファイルで使用されている領域 (キロバイト単位)|ディメンションなし|
|tempdb_log_size|はい|Tempdb ログ ファイル サイズ (KB)|Count|最大値|tempdb トランザクション ログ ファイルで使用されている領域 (キロバイト単位)|ディメンションなし|
|tempdb_log_used_percent|はい|Tempdb ログ使用率|Percent|最大値|tempdb トランザクション ログ ファイルで使用されている領域の割合|ディメンションなし|
|workers_percent|はい|ワーカーの割合|Percent|Average|ワーカーの割合|ディメンションなし|
|xtp_storage_percent|はい|インメモリ OLTP ストレージの割合|Percent|Average|インメモリ OLTP ストレージの割合|ディメンションなし|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量。 この数値には、Azure Storage から外部クライアントへのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|UsedCapacity|はい|Used capacity (使用済み容量)|バイト|Average|ストレージ アカウントによって使用されているストレージの量。 Standard ストレージ アカウントについては、Blob、Table、File、および Queue で使用される容量の合計です。 Premium ストレージ アカウントと BLOB ストレージ アカウントについては、BlobCapacity または FileCapacity と同じです。|ディメンションなし|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|BlobCapacity|いいえ|Blob Capacity (BLOB 容量)|バイト|Average|ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。|BlobType、Tier|
|BlobCount|いいえ|Blob Count (BLOB 数)|Count|Average|ストレージ アカウントに格納されている BLOB オブジェクトの数。|BlobType、Tier|
|BlobProvisionedSize|いいえ|BLOB でプロビジョニングされたサイズ|バイト|Average|ストレージ アカウントの Blob service でプロビジョニングされたストレージの総量 (バイト単位)。|BlobType、Tier|
|ContainerCount|はい|Blob Container Count (BLOB コンテナー数)|Count|Average|ストレージ アカウントのコンテナーの数。|ディメンションなし|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量。 この数値には、Azure Storage から外部クライアントへのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|IndexCapacity|いいえ|インデックスの容量|バイト|Average|Azure Data Lake Storage Gen2 階層構造インデックスで使用されるストレージの容量。|ディメンションなし|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication、FileShare|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量。 この数値には、Azure Storage から外部クライアントへのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication、FileShare|
|FileCapacity|いいえ|File Capacity (ファイル容量)|バイト|Average|ストレージ アカウントによって使用されている File ストレージの量。|FileShare|
|FileCount|いいえ|File Count (ファイル数)|Count|Average|ストレージ アカウントのファイルの数。|FileShare|
|FileShareCapacityQuota|いいえ|ファイル共有の容量クォータ|バイト|Average|Azure Files サービスで使用できる記憶域の容量の上限 (バイト単位)。|FileShare|
|FileShareCount|いいえ|File Share Count (ファイル共有数)|Count|Average|ストレージ アカウントのファイル共有の数。|ディメンションなし|
|FileShareProvisionedIOPS|いいえ|ファイル共有のプロビジョニングされた IOPS|バイト|Average|Premium ファイル ストレージ アカウントにある Premium ファイル共有のためにプロビジョニングされた IOPS のベースライン数。 この数値は、共有容量のプロビジョニングされたサイズ (クォータ) に基づいて計算されます。|FileShare|
|FileShareSnapshotCount|いいえ|ファイル共有のスナップショット数|Count|Average|ストレージ アカウントの Files サービス内の共有に存在するスナップショットの数。|FileShare|
|FileShareSnapshotSize|いいえ|ファイル共有のスナップショットのサイズ|バイト|Average|ストレージ アカウントの Files サービスのスナップショットによって使用される記憶域の容量 (バイト単位)。|FileShare|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication、FileShare|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication、FileShare|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication、FileShare|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication、FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量。 この数値には、Azure Storage から外部クライアントへのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|QueueCapacity|はい|Queue Capacity (キュー容量)|バイト|Average|ストレージ アカウントによって使用されている Queue ストレージの量。|ディメンションなし|
|QueueCount|はい|Queue Count (キュー数)|Count|Average|ストレージ アカウントのキューの数。|ディメンションなし|
|QueueMessageCount|はい|Queue Message Count (キュー メッセージ数)|Count|Average|ストレージ アカウントの期限が切れていないキュー メッセージの数。|ディメンションなし|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|可用性|はい|可用性|Percent|Average|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|
|エグレス|はい|エグレス|バイト|合計|エグレス データの量。 この数値には、Azure Storage から外部クライアントへのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|イングレス|はい|イングレス|バイト|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|はい|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|Average|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|SuccessServerLatency|はい|Success Server Latency (成功サーバー待機時間)|ミリ秒|Average|Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|TableCapacity|はい|Table Capacity (テーブル容量)|バイト|Average|ストレージ アカウントによって使用されている Table Storage の量。|ディメンションなし|
|TableCount|はい|Table Count (テーブル数)|Count|Average|ストレージ アカウントのテーブルの数。|ディメンションなし|
|TableEntityCount|はい|Table Entity Count (テーブル エンティティ数)|Count|Average|ストレージ アカウントのテーブル エンティティの数。|ディメンションなし|
|トランザクション|はい|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ClientIOPS|はい|クライアント IOPS の合計|Count|Average|キャッシュによって処理された、クライアント ファイル操作の速度。|ディメンションなし|
|ClientLatency|はい|クライアントの平均待機時間|ミリ秒|Average|キャッシュに対するクライアント ファイル操作の平均待機時間。|ディメンションなし|
|ClientLockIOPS|はい|クライアント ロック IOPS|CountPerSecond|Average|1 秒あたりのクライアント ファイルのロック操作数。|ディメンションなし|
|ClientMetadataReadIOPS|はい|クライアント メタデータの読み取り IOPS|CountPerSecond|Average|永続的な状態を変更しない、キャッシュに送信されるクライアント ファイル操作 (データ読み取りを除く) の速度。|ディメンションなし|
|ClientMetadataWriteIOPS|はい|クライアント メタデータの書き込み IOPS|CountPerSecond|Average|永続的な状態を変更する、キャッシュに送信されるクライアント ファイル操作 (データ書き込みを除く) の速度。|ディメンションなし|
|ClientReadIOPS|はい|クライアントの読み取り IOPS|CountPerSecond|Average|1 秒あたりのクライアントの読み取り操作数。|ディメンションなし|
|ClientReadThroughput|はい|キャッシュ読み取りの平均スループット|BytesPerSecond|Average|クライアントの読み取りデータ転送速度。|ディメンションなし|
|ClientWriteIOPS|はい|クライアントの書き込み IOPS|CountPerSecond|Average|1 秒あたりのクライアントの書き込み操作数。|ディメンションなし|
|ClientWriteThroughput|はい|キャッシュ書き込みの平均スループット|BytesPerSecond|Average|クライアントの書き込みデータ転送速度。|ディメンションなし|
|StorageTargetAsyncWriteThroughput|はい|StorageTarget の非同期書き込みスループット|BytesPerSecond|Average|キャッシュから特定の StorageTarget にデータが非同期で書き込まれる速度。 これらは、クライアントがブロックされない便宜的な書き込みです。|StorageTarget|
|StorageTargetFillThroughput|はい|StorageTarget のフィル スループット|BytesPerSecond|Average|キャッシュ ミスを処理するためにキャッシュが StorageTarget からデータを読み取る速度。|StorageTarget|
|StorageTargetHealth|はい|ストレージ ターゲットの正常性|Count|Average|キャッシュとストレージ ターゲット間の接続性テストの結果を示すブール値。|ディメンションなし|
|StorageTargetIOPS|はい|StorageTarget IOPS の合計|Count|Average|キャッシュが特定の StorageTarget に送信するすべてのファイル操作の速度。|StorageTarget|
|StorageTargetLatency|はい|StorageTarget の待機時間|ミリ秒|Average|キャッシュが特定の StorageTarget に送信するすべてのファイル操作の平均ラウンド トリップ待機時間。|StorageTarget|
|StorageTargetMetadataReadIOPS|はい|StorageTarget のメタデータ読み取り IOPS|CountPerSecond|Average|キャッシュから特定の StorageTarget に送信される、永続的な状態を変更しないファイル操作 (読み取り操作は除く) の速度。|StorageTarget|
|StorageTargetMetadataWriteIOPS|はい|StorageTarget のメタデータ書き込み IOPS|CountPerSecond|Average|キャッシュから特定の StorageTarget に送信される、永続的な状態の変更があるファイル操作 (書き込み操作は除く) の速度。|StorageTarget|
|StorageTargetReadAheadThroughput|はい|StorageTarget の先読みスループット|BytesPerSecond|Average|好都合なタイミングを生かして、StorageTarget からキャッシュに適宜データを読み込む速度。|StorageTarget|
|StorageTargetReadIOPS|はい|StorageTarget の読み取り IOPS|CountPerSecond|Average|キャッシュから特定の StorageTarget に送信されるファイル読み取り操作の速度。|StorageTarget|
|StorageTargetSyncWriteThroughput|はい|StorageTarget の同期書き込みスループット|BytesPerSecond|Average|キャッシュから特定の StorageTarget にデータが同期書き込みされる速度。 これらの書き込みは、クライアントがブロックする原因となります。|StorageTarget|
|StorageTargetTotalReadThroughput|はい|StorageTarget の合計読み取りスループット|BytesPerSecond|Average|特定の StorageTarget からキャッシュにデータが読み取られる合計速度。|StorageTarget|
|StorageTargetTotalWriteThroughput|はい|StorageTarget の合計書き込みスループット|BytesPerSecond|Average|キャッシュから特定の StorageTarget にデータが書き込まれる合計速度。|StorageTarget|
|StorageTargetWriteIOPS|はい|StorageTarget の書き込み IOPS|Count|Average|キャッシュから特定の StorageTarget に送信されるファイル書き込み操作の速度。|StorageTarget|
|Uptime|はい|Uptime|Count|Average|キャッシュと監視システム間の接続性テストの結果を示すブール値。|ディメンションなし|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|はい|同期セッションの結果|Count|Average|サーバー エンドポイントがクラウド エンドポイントとの同期セッションを正常に完了するたびに 1 の値をログ記録するメトリック|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncBatchTransferredFileBytes|はい|同期したバイト数|バイト|合計|同期セッションに転送されたファイルの合計サイズ|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|はい|アプリケーションによるクラウドを使った階層化の呼び戻しサイズ|バイト|合計|アプリケーションによって呼び戻されたデータのサイズ|SyncGroupName、ServerName、ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|はい|クラウドを使った階層化の呼び戻しサイズ|バイト|合計|呼び戻されたデータのサイズ|SyncGroupName、ServerName|
|StorageSyncRecallIOTotalSizeBytes|はい|クラウドを使った階層化の呼び戻し|バイト|合計|サーバーに呼び戻されたデータの合計サイズ|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|はい|クラウドを使った階層化の呼び戻しスループット|BytesPerSecond|Average|データの呼び戻しスループットのサイズ|SyncGroupName、ServerName|
|StorageSyncServerHeartbeat|はい|サーバーのオンライン状態|Count|最大値|クラウド エンドポイントとのハートビートを正常に記録するたびに 1 の値をログ記録するメトリック|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|はい|同期されたファイル数|Count|合計|同期したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|はい|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ServerHeartbeat|はい|サーバーのオンライン状態|Count|最大値|クラウド エンドポイントとのハートビートを正常に記録するたびに 1 の値をログ記録するメトリック|ServerResourceId、ServerName|
|ServerRecallIOTotalSizeBytes|はい|クラウドを使った階層化の呼び戻し|バイト|合計|サーバーに呼び戻されたデータの合計サイズ|ServerResourceId、ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|はい|同期したバイト数|バイト|合計|同期セッションに転送されたファイルの合計サイズ|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|はい|同期されたファイル数|Count|合計|同期したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|はい|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|はい|同期したバイト数|バイト|合計|同期セッションに転送されたファイルの合計サイズ|ServerEndpointName、SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|はい|同期されたファイル数|Count|合計|同期したファイルの数|ServerEndpointName、SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|はい|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|ServerEndpointName、SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|はい|失敗した関数の要求|Count|合計|失敗した関数の要求|LogicalName、PartitionId|
|AMLCalloutInputEvents|はい|関数のイベント|Count|合計|関数のイベント|LogicalName、PartitionId|
|AMLCalloutRequests|はい|関数の要求|Count|合計|関数の要求|LogicalName、PartitionId|
|ConversionErrors|はい|データ変換エラー|Count|合計|データ変換エラー|LogicalName、PartitionId|
|DeserializationError|はい|逆シリアル化の入力エラー|Count|合計|逆シリアル化の入力エラー|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|はい|順不同のイベント|Count|合計|順不同のイベント|LogicalName、PartitionId|
|EarlyInputEvents|はい|初期入力イベント|Count|合計|初期入力イベント|LogicalName、PartitionId|
|エラー|はい|実行時エラー|Count|合計|実行時エラー|LogicalName、PartitionId|
|InputEventBytes|はい|入力イベントのバイト数|バイト|合計|入力イベントのバイト数|LogicalName、PartitionId|
|InputEvents|はい|入力イベント|Count|合計|入力イベント|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|はい|バックログされた入力イベント|Count|最大値|バックログされた入力イベント|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|はい|受信した入力ソース|Count|合計|受信した入力ソース|LogicalName、PartitionId|
|LateInputEvents|はい|遅延入力イベント|Count|合計|遅延入力イベント|LogicalName、PartitionId|
|OutputEvents|はい|出力イベント|Count|合計|出力イベント|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|はい|透かしの遅延|Seconds|最大値|透かしの遅延|LogicalName、PartitionId|
|ResourceUtilization|はい|SU % 使用率|Percent|最大値|SU % 使用率|LogicalName、PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse ワークスペース

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|いいえ|処理済みのデータ (バイト)|バイト|合計|クエリによって処理されたデータの量|ディメンションなし|
|BuiltinSqlPoolLoginAttempts|いいえ|Login attempts (ログインの試行回数)|Count|合計|成功または失敗したログイン試行回数|結果|
|BuiltinSqlPoolRequestsEnded|いいえ|終了した要求|Count|合計|成功、失敗、または取り消された要求の数|結果|
|IntegrationActivityRunsEnded|いいえ|Activity runs ended (終了したアクティビティ実行数)|Count|合計|成功、失敗、または取り消された統合アクティビティの数|Result、FailureType、Activity、ActivityType、Pipeline|
|IntegrationPipelineRunsEnded|いいえ|Pipeline runs ended (終了したパイプライン実行数)|Count|合計|成功、失敗、または取り消された統合パイプライン実行の数|Result、FailureType、Pipeline|
|IntegrationTriggerRunsEnded|いいえ|終了したトリガーの実行|Count|合計|成功、失敗、または取り消された統合トリガーの数|Result、FailureType、Trigger|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|いいえ|割り当てられた仮想コア|Count|最大値|Apache Spark プールに割り当てられた仮想コア|SubmitterId|
|BigDataPoolAllocatedMemory|いいえ|割り当てられたメモリ (GB)|Count|最大値|Apache Spark プールに割り当てられたメモリ (GB)|SubmitterId|
|BigDataPoolApplicationsActive|いいえ|Active Apache Spark applications (アクティブな Apache Spark アプリケーション)|Count|最大値|アクティブな Apache Spark プール アプリケーションの合計数|JobState|
|BigDataPoolApplicationsEnded|いいえ|Ended Apache Spark applications (終了した Apache Spark アプリケーション)|Count|合計|終了した Apache Spark プール アプリケーションの数|JobType、JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveQueries|いいえ|アクティブなクエリ|Count|合計|アクティブなクエリ。 フィルター処理も分割も適用しないでこのメトリックを使用すると、システム上で実行されているすべてのアクティブなクエリが表示されます|IsUserDefined|
|AdaptiveCacheHitPercent|いいえ|Adaptive cache hit percentage (アダプティブ キャッシュ ヒット率)|Percent|最大値|ワークロードによるアダプティブ キャッシュの使用状況を測定します。 このメトリックをキャッシュ ヒット率メトリックと一緒に使用して、追加容量に合わせてスケーリングするか、キャッシュをハイドレートするためにワークロードを再実行するかを決定します|ディメンションなし|
|AdaptiveCacheUsedPercent|いいえ|Adaptive cache used percentage (アダプティブ キャッシュ使用率)|Percent|最大値|ワークロードによるアダプティブ キャッシュの使用状況を測定します。 このメトリックをキャッシュ使用率メトリックと一緒に使用して、追加容量に合わせてスケーリングするか、キャッシュをハイドレートするためにワークロードを再実行するかを決定します|ディメンションなし|
|接続|はい|接続|Count|合計|SQL プールに対するログインの合計数|結果|
|ConnectionsBlockedByFirewall|いいえ|ファイアウォールによってブロックされる接続数|Count|合計|ファイアウォール規則によってブロックされる接続の数。 SQL プールのアクセス制御ポリシーを再確認し、数が多い場合はこれらの接続を監視します|ディメンションなし|
|CPUPercent|いいえ|DWU used percentage (使用された CPU の割合)|Percent|最大値|SQL プール内のすべてのノードでの CPU 使用率|ディメンションなし|
|DWULimit|いいえ|DWU 上限|Count|最大値|SQL プールのサービス レベル目標|ディメンションなし|
|DWUUsed|いいえ|使用済み DWU|Count|最大値|SQL プール全体の使用状況の概要表示を表します。 DWU の制限 * DWU の割合で測定されます|ディメンションなし|
|DWUUsedPercent|いいえ|DWU used percentage (使用された DWU の割合)|Percent|最大値|SQL プール全体の使用状況の概要表示を表します。 CPU の割合とデータ IO の割合の最大値を取得することにより測定します|ディメンションなし|
|LocalTempDBUsedPercent|いいえ|Local tempdb used percentage (ローカル tempdb 使用率)|Percent|最大値|すべてのコンピューティング ノードでの、ローカル tempdb の使用率 - 値は 5 分ごとに出力されます|ディメンションなし|
|MemoryUsedPercent|いいえ|Memory used percentage (メモリ使用率)|Percent|最大値|SQL プール内のすべてのノードでのメモリ使用率|ディメンションなし|
|QueuedQueries|いいえ|キューに置かれたクエリ|Count|合計|同時実行数の上限に達した後にキューに登録された要求の累積数|IsUserDefined|
|WLGActiveQueries|いいえ|Workload group active queries (ワークロード グループのアクティブなクエリ)|Count|合計|ワークロード グループ内のアクティブなクエリ数。 フィルター処理も分割も適用しないでこのメトリックを使用すると、システム上で実行されているすべてのアクティブなクエリが表示されます|IsUserDefined、WorkloadGroup|
|WLGActiveQueriesTimeouts|いいえ|Workload group query timeouts (ワークロード グループのクエリのタイムアウト)|Count|合計|タイムアウトしたワークロード グループのクエリ。このメトリックでクエリのタイムアウトがレポートされるのは、クエリの実行が開始された場合だけです (ロックまたはリソースの待機による待ち時間は含まれません)|IsUserDefined、WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|いいえ|Workload group allocation by max resource percent (最大リソース割合別のワークロード グループの割り当て)|Percent|最大値|ワークロード グループあたりの "Effective cap resource percent (有効な上限リソース割合)" に対するリソースの割り当て比率を表示します。 このメトリックでは、ワークロード グループの有効使用率が示されます|IsUserDefined、WorkloadGroup|
|WLGAllocationBySystemPercent|いいえ|Workload group allocation by system percent (システム割合別のワークロード グループの割り当て)|Percent|最大値|システム全体に対するリソース割り当て比率|IsUserDefined、WorkloadGroup|
|WLGEffectiveCapResourcePercent|いいえ|Effective cap resource percent (有効な上限リソース割合)|Percent|最大値|ワークロードグループの有効な上限リソースの割合。 min_percentage_resource > 0 のワークロード グループが他にある場合、effective_cap_percentage_resource はそれに比例して低くなります|IsUserDefined、WorkloadGroup|
|WLGEffectiveMinResourcePercent|いいえ|Effective min resource percent (有効な最小リソース割合)|Percent|最大値|有効な最小リソース割合の設定では、サービス レベルとワークロード グループの設定が考慮されます。 有効な min_percentage_resource は、低いサービス レベルで高い値に調整できます|IsUserDefined、WorkloadGroup|
|Effective min resource percent|いいえ|Workload group queued queries (ワークロード グループのキューに登録されたクエリ)|Count|合計|同時実行数の上限に達した後にキューに登録された要求の累積数|IsUserDefined、WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|はい|Ingress Received Bytes (受信バイトの受信)|バイト|合計|すべてのイベント ソースから読み取られたバイト数|ディメンションなし|
|IngressReceivedInvalidMessages|はい|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られた無効なメッセージの数|ディメンションなし|
|IngressReceivedMessages|はい|Ingress Received Messages (受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られたメッセージの数|ディメンションなし|
|IngressReceivedMessagesCountLag|はい|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|Average|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|ディメンションなし|
|IngressReceivedMessagesTimeLag|はい|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|ディメンションなし|
|IngressStoredBytes|はい|Ingress Stored Bytes (保存済みバイトの受信)|バイト|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|ディメンションなし|
|IngressStoredEvents|はい|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|ディメンションなし|
|WarmStorageMaxProperties|はい|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|ディメンションなし|
|WarmStorageUsedProperties|はい|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|ディメンションなし|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|はい|Ingress Received Bytes (受信バイトの受信)|バイト|合計|イベント ソースから読み取られたバイト数|ディメンションなし|
|IngressReceivedInvalidMessages|はい|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|イベント ソースから読み取られた無効なメッセージの数|ディメンションなし|
|IngressReceivedMessages|はい|Ingress Received Messages (受信メッセージの受信)|Count|合計|イベント ソースから読み取られたメッセージの数|ディメンションなし|
|IngressReceivedMessagesCountLag|はい|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|Average|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|ディメンションなし|
|IngressReceivedMessagesTimeLag|はい|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|ディメンションなし|
|IngressStoredBytes|はい|Ingress Stored Bytes (保存済みバイトの受信)|バイト|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|ディメンションなし|
|IngressStoredEvents|はい|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|ディメンションなし|
|WarmStorageMaxProperties|はい|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|ディメンションなし|
|WarmStorageUsedProperties|はい|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|ディメンションなし|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|Disk Read Bytes|はい|Disk Read Bytes|バイト|合計|サンプル期間にわたる読み取り操作による合計ディスク スループット。|ディメンションなし|
|Disk Read Operations/Sec|はい|Disk Read Operations/Sec|CountPerSecond|Average|以前のサンプル期間内の IO 読み取り操作の平均数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|Disk Write Bytes|はい|Disk Write Bytes|バイト|合計|サンプル期間にわたる書き込み操作による合計ディスク スループット。|ディメンションなし|
|Disk Write Operations/Sec|はい|Disk Write Operations/Sec|CountPerSecond|Average|以前のサンプル期間内の IO 書き込み操作の平均数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|DiskReadBytesPerSecond|はい|Disk Read Bytes/Sec|BytesPerSecond|Average|サンプル期間にわたる読み取り操作による平均ディスク スループット。|ディメンションなし|
|DiskReadLatency|はい|ディスク読み取り待機時間|ミリ秒|Average|合計読み取り待機時間。 デバイスとカーネルの読み取り待機時間の合計。|ディメンションなし|
|DiskReadOperations|はい|ディスク読み取り操作数|Count|合計|以前のサンプル期間内の IO 読み取り操作数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|DiskWriteBytesPerSecond|はい|Disk Write Bytes/Sec|BytesPerSecond|Average|サンプル期間にわたる書き込み操作による平均ディスク スループット。|ディメンションなし|
|DiskWriteLatency|はい|ディスク書き込み待機時間|ミリ秒|Average|合計書き込み待機時間。 デバイスとカーネルの書き込み待機時間の合計。|ディメンションなし|
|DiskWriteOperations|はい|Disk Write Operations|Count|合計|以前のサンプル期間内の IO 書き込み操作数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|MemoryActive|はい|アクティブなメモリ|バイト|Average|過去の小さな時間枠で VM によって使用されたメモリの容量。 これは、VM が現在必要としているメモリの容量の「真」の数値です。 ゲストのパフォーマンスに影響を与えずに、追加の未使用メモリをスワップ アウトするか、バルーニングできます。|ディメンションなし|
|MemoryGranted|はい|許可されているメモリ量|バイト|Average|ホストによって VM に許可されたメモリの容量。 VMkernel がメモリを必要とした場合、一度アクセスされ、許可されたメモリがスワップ アウトされるか、バルーニングされるまで、メモリはホストに与えられません。|ディメンションなし|
|MemoryUsed|はい|メモリ使用量|バイト|Average|VM によって使用されているマシンのメモリの容量。|ディメンションなし|
|Network In|はい|Network In|バイト|合計|受信トラフィックの合計ネットワーク スループット。|ディメンションなし|
|Network Out|はい|Network Out|バイト|合計|送信トラフィックの合計ネットワーク スループット。|ディメンションなし|
|NetworkInBytesPerSecond|はい|受信ネットワークのバイト数/秒|BytesPerSecond|Average|受信トラフィックの平均ネットワーク スループット。|ディメンションなし|
|NetworkOutBytesPerSecond|はい|送信ネットワークのバイト数/秒|BytesPerSecond|Average|送信トラフィックの平均ネットワーク スループット。|ディメンションなし|
|Percentage CPU|はい|Percentage CPU|Percent|Average|ノードの CPU 使用率。 この値は、100% でシステム上のすべてのプロセッサ コアを表します。 たとえば、4 コア システムの 50% を使用する双方向 VM は、2 つのコアを完全に使用します。|ディメンションなし|
|PercentageCpuReady|はい|CPU Ready の割合|ミリ秒|合計|準備時間は、過去の更新間隔で CPU が利用可能になるまで待機するのに費やされた時間です。|ディメンションなし|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|ActiveRequests|はい|アクティブな要求数 (非推奨)|Count|合計|アクティブな要求数|インスタンス|
|AverageResponseTime|はい|平均応答時間 (非推奨)|Seconds|Average|フロント エンドが要求に応答するのに要した平均時間 (秒単位)。|インスタンス|
|BytesReceived|はい|受信データ|バイト|合計|すべてのフロント エンドで使用された受信帯域幅の平均値 (MiB)。|インスタンス|
|BytesSent|はい|送信データ|バイト|合計|すべてのフロント エンドで使用された受信帯域幅の平均値 (MiB)。|インスタンス|
|CpuPercentage|はい|CPU の割合|Percent|Average|フロント エンドの全インスタンスで使用された平均 CPU。|インスタンス|
|DiskQueueLength|はい|ディスク キューの長さ|Count|Average|ストレージのキューに登録された読み取り要求と書き込み要求の平均数。 ディスク キューが長いときは、過剰なディスク I/O のためにアプリの速度が低下している可能性があることを示しています。|インスタンス|
|Http101|はい|Http 101|Count|合計|HTTP 状態コード 101 が結果として返された要求の数。|インスタンス|
|Http2xx|はい|HTTP 2xx|Count|合計|200 以上 300 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http3xx|はい|HTTP 3xx|Count|合計|300 以上 400 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http401|はい|HTTP 401|Count|合計|HTTP 401 状態コードが結果として返された要求の数。|インスタンス|
|Http403|はい|HTTP 403|Count|合計|HTTP 403 状態コードが結果として返された要求の数。|インスタンス|
|Http404|はい|HTTP 404|Count|合計|HTTP 404 状態コードが結果として返された要求の数。|インスタンス|
|Http406|はい|HTTP 406|Count|合計|HTTP 406 状態コードが結果として返された要求の数。|インスタンス|
|Http4xx|はい|HTTP 4xx|Count|合計|400 以上 500 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http5xx|はい|HTTP サーバー エラー|Count|合計|500 以上 600 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|HttpQueueLength|はい|HTTP キューの長さ|Count|Average|処理される前にキューで待つ必要があった HTTP 要求の平均数。 HTTP キューが長いこと、または HTTP キューの長さが増加していることは、プランに大きな負荷がかかっているしるしです。|インスタンス|
|HttpResponseTime|はい|応答時間|Seconds|Average|フロント エンドが要求に応答するのに要した時間 (秒単位)。|インスタンス|
|LargeAppServicePlanInstances|はい|L App Service プランの worker の数|Count|Average|L App Service プランの worker の数|ディメンションなし|
|MediumAppServicePlanInstances|はい|M App Service プランの worker の数|Count|Average|M App Service プランの worker の数|ディメンションなし|
|MemoryPercentage|はい|メモリの割合|Percent|Average|フロント エンドの全インスタンスで使用された平均メモリ|インスタンス|
|Requests|はい|Requests|Count|合計|結果として返された HTTP 状態コードを問わない、要求の合計数。|インスタンス|
|SmallAppServicePlanInstances|はい|S App Service プランの worker の数|Count|Average|S App Service プランの worker の数|ディメンションなし|
|TotalFrontEnds|はい|フロント エンドの合計|Count|Average|フロント エンドの合計|ディメンションなし|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|CpuPercentage|はい|CPU の割合|Percent|Average|ワーカー プールの全インスタンスで使用された平均 CPU。|インスタンス|
|MemoryPercentage|はい|メモリの割合|Percent|Average|ワーカー プールの全インスタンスで使用された平均メモリ。|インスタンス|
|WorkersAvailable|はい|利用可能な worker の数|Count|Average|利用可能な worker の数|ディメンションなし|
|WorkersTotal|はい|worker の合計数|Count|Average|worker の合計数|ディメンションなし|
|WorkersUsed|はい|使用済みの worker の数|Count|Average|使用済みの worker の数|ディメンションなし|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BytesReceived|はい|受信データ|バイト|合計|プランの全インスタンスで使用された平均受信帯域幅。|インスタンス|
|BytesSent|はい|送信データ|バイト|合計|プランの全インスタンスで使用された平均送信帯域幅。|インスタンス|
|CpuPercentage|はい|CPU の割合|Percent|Average|プランの全インスタンスで使用された平均 CPU。|インスタンス|
|DiskQueueLength|はい|ディスク キューの長さ|Count|Average|ストレージのキューに登録された読み取り要求と書き込み要求の平均数。 ディスク キューが長いときは、過剰なディスク I/O のためにアプリの速度が低下している可能性があることを示しています。|インスタンス|
|HttpQueueLength|はい|HTTP キューの長さ|Count|Average|処理される前にキューで待つ必要があった HTTP 要求の平均数。 HTTP キューが長いこと、または HTTP キューの長さが増加していることは、プランに大きな負荷がかかっているしるしです。|インスタンス|
|MemoryPercentage|はい|メモリの割合|Percent|Average|プランの全インスタンスで使用された平均メモリ。|インスタンス|
|SocketInboundAll|はい|SocketInboundAll|Count|Average|SocketInboundAll|インスタンス|
|SocketLoopback|はい|SocketLoopback|Count|Average|SocketLoopback|インスタンス|
|SocketOutboundAll|はい|SocketOutboundAll|Count|Average|SocketOutboundAll|インスタンス|
|SocketOutboundEstablished|はい|SocketOutboundEstablished|Count|Average|SocketOutboundEstablished|インスタンス|
|SocketOutboundTimeWait|はい|SocketOutboundTimeWait|Count|Average|SocketOutboundTimeWait|インスタンス|
|TcpCloseWait|はい|TCP 終了を待機中|Count|Average|TCP 終了を待機中|インスタンス|
|TcpClosing|はい|TCP 終了|Count|Average|TCP 終了|インスタンス|
|TcpEstablished|はい|TCP 確立済み|Count|Average|TCP 確立済み|インスタンス|
|TcpFinWait1|はい|TCP FIN の待機 1|Count|Average|TCP FIN の待機 1|インスタンス|
|TcpFinWait2|はい|TCP FIN の待機 2|Count|Average|TCP FIN の待機 2|インスタンス|
|TcpLastAck|はい|TCP 最後の ACK|Count|Average|TCP 最後の ACK|インスタンス|
|TcpSynReceived|はい|TCP SYN 受信済み|Count|Average|TCP SYN 受信済み|インスタンス|
|TcpSynSent|はい|TCP SYN 送信済み|Count|Average|TCP SYN 送信済み|インスタンス|
|TcpTimeWait|はい|TCP 待機中|Count|Average|TCP 待機中|インスタンス|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AppConnections|はい|接続|Count|Average|サンドボックス内に存在するバインドされたソケットの数 (w3wp.exe とその子プロセス)。 バインドされたソケットは、bind()/connect() API の呼び出しによって作成され、対象のソケットが CloseHandle()/closesocket() で閉じられるまで残っています。|インスタンス|
|AverageMemoryWorkingSet|はい|平均メモリ ワーキング セット|バイト|Average|アプリで使用された平均メモリ量 (メガバイト (MiB))。|インスタンス|
|AverageResponseTime|はい|平均応答時間 (非推奨)|Seconds|Average|アプリが要求に応答するのに要した平均時間 (秒単位)。|インスタンス|
|BytesReceived|はい|受信データ|バイト|合計|アプリで消費された受信帯域幅の量 (MiB)。|インスタンス|
|BytesSent|はい|送信データ|バイト|合計|アプリで消費された送信帯域幅の量 (MiB)。|インスタンス|
|CpuTime|はい|CPU 時間|Seconds|合計|アプリで消費された CPU の量 (秒)。 このメトリックの詳細については、 https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (「CPU 時間と CPU の割合」) を参照してください Azure Functions には適用されません。|インスタンス|
|CurrentAssemblies|はい|現在のアセンブリ|Count|Average|このアプリケーション内のすべての AppDomain で読み込まれたアセンブリの現在の数。|インスタンス|
|FileSystemUsage|はい|ファイル システムの使用量|バイト|Average|アプリによって使用されたファイル システム クォータの割合。|ディメンションなし|
|FunctionExecutionCount|はい|関数の実行回数|Count|合計|関数の実行回数。 Azure Functions にのみ存在します。|インスタンス|
|FunctionExecutionUnits|はい|関数の実行単位|Count|合計|関数の実行単位。 Azure Functions にのみ存在します。|インスタンス|
|Gen0Collections|はい|Gen 0 ガベージ コレクション|Count|合計|アプリ プロセスが開始されてからジェネレーション 0 オブジェクトがガベージ コレクションされた回数。 上位のジェネレーションの GC には、下位のジェネレーションの GC がすべて含まれます。|インスタンス|
|Gen1Collections|はい|Gen 1 ガベージ コレクション|Count|合計|アプリ プロセスが開始されてからジェネレーション 1 オブジェクトがガベージ コレクションされた回数。 上位のジェネレーションの GC には、下位のジェネレーションの GC がすべて含まれます。|インスタンス|
|Gen2Collections|はい|Gen 2 ガベージ コレクション|Count|合計|アプリ プロセスが開始されてからジェネレーション 2 オブジェクトがガベージ コレクションされた回数。|インスタンス|
|ハンドル|はい|ハンドルの数|Count|Average|アプリ プロセスによって現在開かれているハンドルの合計数。|インスタンス|
|HealthCheckStatus|はい|正常性チェックの状態|Count|Average|正常性チェックの状態|インスタンス|
|Http101|はい|Http 101|Count|合計|HTTP 状態コード 101 が結果として返された要求の数。|インスタンス|
|Http2xx|はい|HTTP 2xx|Count|合計|200 以上 300 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http3xx|はい|HTTP 3xx|Count|合計|300 以上 400 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http401|はい|HTTP 401|Count|合計|HTTP 401 状態コードが結果として返された要求の数。|インスタンス|
|Http403|はい|HTTP 403|Count|合計|HTTP 403 状態コードが結果として返された要求の数。|インスタンス|
|Http404|はい|HTTP 404|Count|合計|HTTP 404 状態コードが結果として返された要求の数。|インスタンス|
|Http406|はい|HTTP 406|Count|合計|HTTP 406 状態コードが結果として返された要求の数。|インスタンス|
|Http4xx|はい|HTTP 4xx|Count|合計|400 以上 500 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http5xx|はい|HTTP サーバー エラー|Count|合計|500 以上 600 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|HttpResponseTime|はい|応答時間|Seconds|Average|アプリが要求に応答するのに要した時間 (秒単位)。|インスタンス|
|IoOtherBytesPerSecond|はい|IO その他のバイト/秒|BytesPerSecond|合計|アプリ プロセスがデータを含まない I/O 操作 (制御操作など) にバイトを発行している速度。|インスタンス|
|IoOtherOperationsPerSecond|はい|IO その他の操作/秒|BytesPerSecond|合計|アプリ プロセスが読み取りまたは書き込み操作ではない I/O 操作を発行している速度。|インスタンス|
|IoReadBytesPerSecond|はい|IO 読み取りバイト/秒|BytesPerSecond|合計|アプリ プロセスが I/O 操作からバイトを読み取っている速度。|インスタンス|
|IoReadOperationsPerSecond|はい|IO 読み取り操作/秒|BytesPerSecond|合計|アプリ プロセスが読み取り I/O 操作を発行している速度。|インスタンス|
|IoWriteBytesPerSecond|はい|IO 書き込みバイト/秒|BytesPerSecond|合計|アプリ プロセスが I/O 操作にバイトを書き込んでいる速度。|インスタンス|
|IoWriteOperationsPerSecond|はい|IO 書き込み操作/秒|BytesPerSecond|合計|アプリ プロセスが書き込み I/O 操作を発行している速度。|インスタンス|
|MemoryWorkingSet|はい|メモリ ワーキング セット|バイト|Average|アプリで使用されている現在のメモリ量 (MiB)。|インスタンス|
|PrivateBytes|はい|Private Bytes|バイト|Average|プライベート バイトは、アプリ プロセスによって割り当てられた、他のプロセスとは共有できないメモリの現在のサイズ (バイト単位) です。|インスタンス|
|Requests|はい|Requests|Count|合計|結果として返された HTTP 状態コードを問わない、要求の合計数。|インスタンス|
|RequestsInApplicationQueue|はい|アプリケーション キュー内の要求数|Count|Average|アプリケーション要求キュー内の要求の数。|インスタンス|
|Threads|はい|Thread Count|Count|Average|アプリ プロセス内で現在アクティブなスレッドの数。|インスタンス|
|TotalAppDomains|はい|アプリ ドメイン合計数|Count|Average|現在このアプリケーションに読み込まれている AppDomain の数。|インスタンス|
|TotalAppDomainsUnloaded|はい|アンロード済みのアプリ ドメイン合計数|Count|Average|アプリケーションの開始以降にアンロードされた AppDomain の合計数。|インスタンス|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|AppConnections|はい|接続|Count|Average|サンドボックス内に存在するバインドされたソケットの数 (w3wp.exe とその子プロセス)。 バインドされたソケットは、bind()/connect() API の呼び出しによって作成され、対象のソケットが CloseHandle()/closesocket() で閉じられるまで残っています。|インスタンス|
|AverageMemoryWorkingSet|はい|平均メモリ ワーキング セット|バイト|Average|アプリで使用された平均メモリ量 (メガバイト (MiB))。|インスタンス|
|AverageResponseTime|はい|平均応答時間 (非推奨)|Seconds|Average|アプリが要求に応答するのに要した平均時間 (秒単位)。|インスタンス|
|BytesReceived|はい|受信データ|バイト|合計|アプリで消費された受信帯域幅の量 (MiB)。|インスタンス|
|BytesSent|はい|送信データ|バイト|合計|アプリで消費された送信帯域幅の量 (MiB)。|インスタンス|
|CpuTime|はい|CPU 時間|Seconds|合計|アプリで消費された CPU の量 (秒)。 このメトリックの詳細については、 https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (「CPU 時間と CPU の割合」) を参照してください|インスタンス|
|CurrentAssemblies|はい|現在のアセンブリ|Count|Average|このアプリケーション内のすべての AppDomain で読み込まれたアセンブリの現在の数。|インスタンス|
|FileSystemUsage|はい|ファイル システムの使用量|バイト|Average|アプリによって使用されたファイル システム クォータの割合。|ディメンションなし|
|FunctionExecutionCount|はい|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|FunctionExecutionUnits|はい|関数の実行単位|Count|合計|関数の実行単位|インスタンス|
|Gen0Collections|はい|Gen 0 ガベージ コレクション|Count|合計|アプリ プロセスが開始されてからジェネレーション 0 オブジェクトがガベージ コレクションされた回数。 上位のジェネレーションの GC には、下位のジェネレーションの GC がすべて含まれます。|インスタンス|
|Gen1Collections|はい|Gen 1 ガベージ コレクション|Count|合計|アプリ プロセスが開始されてからジェネレーション 1 オブジェクトがガベージ コレクションされた回数。 上位のジェネレーションの GC には、下位のジェネレーションの GC がすべて含まれます。|インスタンス|
|Gen2Collections|はい|Gen 2 ガベージ コレクション|Count|合計|アプリ プロセスが開始されてからジェネレーション 2 オブジェクトがガベージ コレクションされた回数。|インスタンス|
|ハンドル|はい|ハンドルの数|Count|Average|アプリ プロセスによって現在開かれているハンドルの合計数。|インスタンス|
|HealthCheckStatus|はい|正常性チェックの状態|Count|Average|正常性チェックの状態|インスタンス|
|Http101|はい|Http 101|Count|合計|HTTP 状態コード 101 が結果として返された要求の数。|インスタンス|
|Http2xx|はい|HTTP 2xx|Count|合計|200 以上 300 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http3xx|はい|HTTP 3xx|Count|合計|300 以上 400 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http401|はい|HTTP 401|Count|合計|HTTP 401 状態コードが結果として返された要求の数。|インスタンス|
|Http403|はい|HTTP 403|Count|合計|HTTP 403 状態コードが結果として返された要求の数。|インスタンス|
|Http404|はい|HTTP 404|Count|合計|HTTP 404 状態コードが結果として返された要求の数。|インスタンス|
|Http406|はい|HTTP 406|Count|合計|HTTP 406 状態コードが結果として返された要求の数。|インスタンス|
|Http4xx|はい|HTTP 4xx|Count|合計|400 以上 500 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|Http5xx|はい|HTTP サーバー エラー|Count|合計|500 以上 600 未満の HTTP 状態コードが結果として返された要求の数。|インスタンス|
|HttpResponseTime|はい|応答時間|Seconds|Average|アプリが要求に応答するのに要した時間 (秒単位)。|インスタンス|
|IoOtherBytesPerSecond|はい|IO その他のバイト/秒|BytesPerSecond|合計|アプリ プロセスがデータを含まない I/O 操作 (制御操作など) にバイトを発行している速度。|インスタンス|
|IoOtherOperationsPerSecond|はい|IO その他の操作/秒|BytesPerSecond|合計|アプリ プロセスが読み取りまたは書き込み操作ではない I/O 操作を発行している速度。|インスタンス|
|IoReadBytesPerSecond|はい|IO 読み取りバイト/秒|BytesPerSecond|合計|アプリ プロセスが I/O 操作からバイトを読み取っている速度。|インスタンス|
|IoReadOperationsPerSecond|はい|IO 読み取り操作/秒|BytesPerSecond|合計|アプリ プロセスが読み取り I/O 操作を発行している速度。|インスタンス|
|IoWriteBytesPerSecond|はい|IO 書き込みバイト/秒|BytesPerSecond|合計|アプリ プロセスが I/O 操作にバイトを書き込んでいる速度。|インスタンス|
|IoWriteOperationsPerSecond|はい|IO 書き込み操作/秒|BytesPerSecond|合計|アプリ プロセスが書き込み I/O 操作を発行している速度。|インスタンス|
|MemoryWorkingSet|はい|メモリ ワーキング セット|バイト|Average|アプリで使用されている現在のメモリ量 (MiB)。|インスタンス|
|PrivateBytes|はい|Private Bytes|バイト|Average|プライベート バイトは、アプリ プロセスによって割り当てられた、他のプロセスとは共有できないメモリの現在のサイズ (バイト単位) です。|インスタンス|
|Requests|はい|Requests|Count|合計|結果として返された HTTP 状態コードを問わない、要求の合計数。|インスタンス|
|RequestsInApplicationQueue|はい|アプリケーション キュー内の要求数|Count|Average|アプリケーション要求キュー内の要求の数。|インスタンス|
|Threads|はい|Thread Count|Count|Average|アプリ プロセス内で現在アクティブなスレッドの数。|インスタンス|
|TotalAppDomains|はい|アプリ ドメイン合計数|Count|Average|現在このアプリケーションに読み込まれている AppDomain の数。|インスタンス|
|TotalAppDomainsUnloaded|はい|アンロード済みのアプリ ドメイン合計数|Count|Average|アプリケーションの開始以降にアンロードされた AppDomain の合計数。|インスタンス|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|BytesSent|はい|送信データ|バイト|合計|BytesSent|インスタンス|
|FunctionErrors|はい|FunctionErrors|Count|合計|FunctionErrors|インスタンス|
|FunctionHits|はい|FunctionHits|Count|合計|FunctionHits|インスタンス|
|SiteErrors|はい|SiteErrors|Count|合計|SiteErrors|インスタンス|
|SiteHits|はい|SiteHits|Count|合計|SiteHits|インスタンス|


## <a name="next-steps"></a>次のステップ

- [Azure Monitor のメトリックを確認します](../data-platform.md)
- [メトリックでアラートを作成します](../alerts/alerts-overview.md)
- [メトリックをストレージ、Event Hub、または Log Analytics にエクスポートします](../essentials/platform-logs-overview.md)
