---
title: Azure IoT Hub の監視データのリファレンス
description: Azure IoT Hub を監視する際に必要となる重要なリファレンス資料
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 6c7fd0a310663b664d33027951ade885b83d458a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570958"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Azure IoT Hub の監視データのリファレンス

Azure IoT Hub の監視データの収集と分析の詳細については、「[Azure IoT Hub の監視](monitor-iot-hub.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションには、自動的に収集されるすべての Azure IoT Hub プラットフォーム メトリックが一覧表示されています。 IoT Hub メトリックのリソース プロバイダー名前空間は **Microsoft.Devices** であり、名前空間の種類は **IoTHubs** です。

次のサブセクションでは、IoT Hub プラットフォーム メトリックを一般カテゴリ別に分類し、Azure portal に表示される表示名で一覧表示しています。 各サブセクションに示すメトリックに関連する情報も提供します。

また、Azure Monitor ドキュメントの「[Microsoft.Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs)」には、すべての IoT Hub プラットフォーム メトリックがメトリック名で一覧表示された 1 つの表があります。 この表には、[サポートされる集計](#supported-aggregations)など、この記事に含まれている一部のメトリックの情報が提供されていないことに注意してください。

他の Azure サービスでサポートされているメトリックについては、「[Azure Monitor のサポートされるメトリック](../azure-monitor/essentials/metrics-supported.md)」を参照してください。

**このセクションのトピック**

- [Azure IoT Hub の監視データのリファレンス](#monitoring-azure-iot-hub-data-reference)
  - [メトリック](#metrics)
    - [サポートされる集計](#supported-aggregations)
    - [クラウドからデバイスへのコマンドのメトリック](#cloud-to-device-command-metrics)
    - [クラウドからデバイスへのダイレクト メソッドのメトリック](#cloud-to-device-direct-methods-metrics)
    - [クラウドからデバイスへのツイン操作のメトリック](#cloud-to-device-twin-operations-metrics)
    - [構成のメトリック](#configurations-metrics)
    - [1 日あたりのクォータのメトリック](#daily-quota-metrics)
    - [デバイス メトリック](#device-metrics)
    - [デバイス テレメトリのメトリック](#device-telemetry-metrics)
    - [デバイスからクラウドへのツイン操作のメトリック](#device-to-cloud-twin-operations-metrics)
    - [Event Grid のメトリック](#event-grid-metrics)
    - [ジョブのメトリック](#jobs-metrics)
    - [ルーティング メトリック](#routing-metrics)
    - [ツイン クエリのメトリック](#twin-query-metrics)
  - [メトリック ディメンション](#metric-dimensions)
  - [リソース ログ](#resource-logs)
    - [接続](#connections)
    - [デバイス テレメトリ](#device-telemetry)
    - [クラウドからデバイスへのコマンド](#cloud-to-device-commands)
    - [デバイス ID の操作](#device-identity-operations)
    - [ファイルのアップロード操作](#file-upload-operations)
    - [ルート](#routes)
    - [デバイスからクラウドへのツイン操作](#device-to-cloud-twin-operations)
    - [クラウドからデバイスへのツイン操作](#cloud-to-device-twin-operations)
    - [ツイン クエリ](#twin-queries)
    - [ジョブ操作](#jobs-operations)
    - [ダイレクト メソッド](#direct-methods)
    - [分散トレース (プレビュー)](#distributed-tracing-preview)
      - [IoT Hub D2C (device-to-cloud) のログ](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT Hub のイングレス ログ](#iot-hub-ingress-logs)
      - [IoT Hub のエグレス ログ](#iot-hub-egress-logs)
    - [構成](#configurations)
    - [デバイス ストリーム (プレビュー)](#device-streams-preview)
  - [Azure Monitor ログ テーブル](#azure-monitor-logs-tables)
  - [参照](#see-also)

### <a name="supported-aggregations"></a>サポートされる集計

各テーブルの **[集計の種類]** 列は、グラフやアラート用にメトリックが選択されているときに使用される既定の集計に対応しています。

   ![メトリックの集計を示すスクリーンショット](./media/monitor-iot-hub-reference/aggregation-type.png)

ほとんどのメトリックでは、すべての集計の種類が有効です。ただし、カウント メトリック ( **[ユニット]** 列の値が **[Count]** のもの) については、一部の集計のみが有効です。 カウント メトリックには、次の 2 種類があります。

* **シングルポイント** カウント メトリックの場合、測定されている操作が発生するたびに、IoT Hub により 1 つのデータ ポイント (基本的に 1) が登録されます。 次に、Azure Monitor によって、指定された粒度でこれらのデータ ポイントが合計されます。 **シングルポイント** メトリックの例としては、"*送信済みテレメトリ メッセージ*" や "*C2D メッセージ配信完了*" があります。 これらのメトリックでは、関連する集計の種類は合計 (Sum) のみです。 ポータルを使用すると、Minimum、Maximum、Average を選択できます。ただし、これらの値は常に 1 になります。

* **スナップショット** カウント メトリックの場合、測定されている操作が発生すると、IoT Hub によって合計数が登録されます。 現時点では、IoT Hub によって出力される **スナップショット** メトリックとしては、"*使用されているメッセージの合計数*"、"*デバイスの合計数 (プレビュー)* "、"*接続されているデバイス (プレビュー)* " の 3 つがあります。 これらのメトリックは、出力されるたびに "合計" 数量を示すので、指定された粒度でそれらを合計することは意味がありません。 Azure Monitor では、これらのメトリックに対する集計の種類として Minimum、Maximum、Average を選択するように制限されています。

### <a name="cloud-to-device-command-metrics"></a>クラウドからデバイスへのコマンドのメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|期限切れ C2D メッセージ (プレビュー)|C2DMessagesExpired|Count|合計|有効期限が切れた cloud-to-device メッセージの数|なし|
|C2D メッセージ配信完了|c2d.commands.egress.complete.success|Count|合計|デバイスで正常に完了した Cloud to Device メッセージ配信の数|なし|
|破棄した C2D メッセージ|c2d.commands.egress.abandon.success|Count|合計|デバイスで中止された Cloud to Device メッセージの数|なし|
|C2D メッセージ拒否数|c2d.commands.egress.reject.success|Count|合計|デバイスで拒否された Cloud to Device メッセージの数|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="cloud-to-device-direct-methods-metrics"></a>クラウドからデバイスへのダイレクト メソッドのメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|失敗したダイレクト メソッドの呼び出し|c2d.methods.failure|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|なし|
|ダイレクト メソッドの呼び出しの要求サイズ|c2d.methods.requestSize|バイト|Average|成功したすべてのダイレクト メソッド要求の数。|なし|
|ダイレクト メソッドの呼び出しの応答サイズ|c2d.methods.responseSize|バイト|Average|成功したすべてのダイレクト メソッド応答の数。|なし|
|成功したダイレクト メソッドの呼び出し|c2d.methods.success|Count|合計|成功したダイレクト メソッドの呼び出しの数。|なし|

**[ユニット]** 値が **Count** のメトリックでは、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="cloud-to-device-twin-operations-metrics"></a>クラウドからデバイスへのツイン操作のメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|失敗したバックエンドからのツイン読み取り|c2d.twin.read.failure|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|なし|
|失敗したバック エンドからのツイン更新|c2d.twin.update.failure|Count|合計|バックエンドが開始して失敗したツイン更新の数。|なし|
|バック エンドからのツイン読み取りの応答サイズ|c2d.twin.read.size|バイト|Average|バックエンドが開始して成功したツイン読み取りの数。|なし|
|バックエンドからのツイン更新のサイズ|c2d.twin.update.size|バイト|Average|バックエンドが開始して成功したすべてのツイン更新の合計サイズ。|なし|
|成功したバック エンドからのツイン読み取り|c2d.twin.read.success|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|なし|
|成功したバックエンドからのツイン更新|c2d.twin.update.success|Count|合計|バックエンドが開始して成功したツイン更新の数。|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="configurations-metrics"></a>構成のメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|構成メトリック|configuration|Count|合計|ターゲット デバイスのセットで、デバイスの構成と IoT Edge のデプロイに対して実行された CRUD 操作の数。 これには、これらの構成が理由のデバイス ツインまたはモジュール ツインを変更する操作の数も含まれます。|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="daily-quota-metrics"></a>1 日あたりのクォータのメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|デバイス データの合計使用量|deviceDataUsage|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|なし|
|デバイス データの合計使用量 (プレビュー)|deviceDataUsageV2|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|なし|
|使用されているメッセージの合計数|dailyMessageQuotaUsed|Count|Average|現在使用されているメッセージの合計数。 これは累積値であり、毎日 00 時 00 分 (UTC) になるとゼロにリセットされます。|なし|

"*使用されているメッセージの合計数*" では、Minimum、Maximum、Average の集計のみがサポートされています。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="device-metrics"></a>デバイス メトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|デバイスの総数 (非推奨)|devices.totalDevices|Count|合計|IoT Hub に登録されたデバイスの数|なし|
|接続されているデバイス (非推奨) |devices.connectedDevices.allProtocol|Count|合計|IoT Hub に接続されているデバイスの数|なし|
|デバイスの合計数 (プレビュー)|totalDeviceCount|Count|Average|IoT Hub に登録されたデバイスの数|なし|
|接続されているデバイス (プレビュー)|connectedDeviceCount|Count|Average|IoT Hub に接続されているデバイスの数|なし|

"*デバイスの総数 (非推奨)* " と "*接続されているデバイス (非推奨)* " では、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

"*デバイスの合計数 (プレビュー)* " と "*接続されているデバイス (プレビュー)* " では、Minimum、Maximum、Average の集計のみが有効です。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

"*接続されているデバイス (プレビュー)* " と "*デバイスの合計数 (プレビュー)* " は、診断設定を使用してエクスポートすることはできません。

### <a name="device-telemetry-metrics"></a>デバイス テレメトリのメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|調整エラーの数|d2c.telemetry.ingress.sendThrottle|Count|合計|デバイスのスループット調整による調整エラーの数|なし|
|テレメトリ メッセージ送信試行|d2c.telemetry.ingress.allProtocol|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|なし|
|送信済みテレメトリ メッセージ|d2c.telemetry.ingress.success|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="device-to-cloud-twin-operations-metrics"></a>デバイスからクラウドへのツイン操作のメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|失敗したデバイスからのツイン読み取り|d2c.twin.read.failure|Count|合計|デバイスが開始したツイン読み取りの失敗数。|なし|
|失敗したデバイスからのツイン更新|d2c.twin.update.failure|Count|合計|デバイスが開始して失敗したツイン更新の数。|なし|
|デバイスからのツイン読み取りの応答サイズ|d2c.twin.read.size|バイト|Average|デバイスが開始して成功したすべてのツイン読み取りの数。|なし|
|デバイスからのツイン更新のサイズ|d2c.twin.update.size|バイト|Average|デバイスが開始して成功したすべてのツイン更新の合計サイズ。|なし|
|成功したデバイスからのツイン読み取り|d2c.twin.read.success|Count|合計|デバイスが開始して成功したツイン読み取りの数。|なし|
|成功したデバイスからのツイン更新|d2c.twin.update.success|Count|合計|デバイスが開始して成功したツイン更新の数。|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="event-grid-metrics"></a>Event Grid のメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Event Grid の配信数 (プレビュー)|EventGridDeliveries|Count|合計|Event Grid に発行された IoT Hub イベントの数。 成功および失敗した要求の数には、Result ディメンションを使用します。 EventType ディメンションはイベントの種類 (https://aka.ms/ioteventgrid) ) を示します。|Result、<br/>EventType<br>"*詳細については、「[メトリック ディメンション](#metric-dimensions)」を参照してください。* "|
|Event Grid の待機時間 (プレビュー)|EventGridLatency|ミリ秒|Average|oT Hub イベントが生成されてから、そのイベントが Event Grid に発行されるまでの平均待機時間 (ミリ秒)。 この数は、すべてのイベントの種類の間の平均値です。 特定の種類のイベントの待機時間を確認するには、EventType ディメンションを使用します。|EventType<br>"*詳細については、「[メトリック ディメンション](#metric-dimensions)」を参照してください。* "|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="jobs-metrics"></a>ジョブのメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|完了したジョブ|jobs.completed|Count|合計|完了したジョブの数。|なし|
|失敗したジョブ一覧の呼び出し|jobs.listJobs.failure|Count|合計|失敗したジョブ一覧の呼び出しの数。|なし|
|失敗したメソッド呼び出しジョブの作成|jobs.createDirectMethodJob.failure|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|なし|
|失敗したツイン更新ジョブの作成|jobs.createTwinUpdateJob.failure|Count|合計|作成に失敗したツイン更新ジョブの数。|なし|
|失敗したジョブの取り消し|jobs.cancelJob.failure|Count|合計|失敗したジョブ取り消しの呼び出し。|なし|
|失敗したジョブ クエリ|jobs.queryJobs.failure|Count|合計|失敗したジョブ クエリの呼び出しの数。|なし|
|失敗したジョブ|jobs.failed|Count|合計|失敗したジョブの数。|なし|
|成功したジョブ一覧の呼び出し|jobs.listJobs.success|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|なし|
|成功したメソッド呼び出しジョブの作成|jobs.createDirectMethodJob.success|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|なし|
|成功したツイン更新ジョブの作成|jobs.createTwinUpdateJob.<br>success|Count|合計|正常に作成されたツイン更新ジョブの数。|なし|
|成功したジョブの取り消し|jobs.cancelJob.success|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|なし|
|成功したジョブ クエリ|jobs.queryJobs.success|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="routing-metrics"></a>ルーティング メトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
| Routing Delivery Attempts (ルーティングの配信試行) (プレビュー) |RoutingDeliveries | Count | 合計 |これはルーティングの配信に関するメトリックです。 ディメンションを使用して、特定のエンドポイントまたは特定のルーティング ソースに向けた配信の状態を特定します。| Result、<br>RoutingSource、<br>endpointType、<br>FailureReasonCategory、<br>EndpointName<br>"*詳細については、「[メトリック ディメンション](#metric-dimensions)」を参照してください。* " |
| Routing Delivery Data Size In Bytes (バイト単位でのルーティング配信のデータ サイズ) (プレビュー)|RoutingDataSizeInBytesDelivered| バイト | 合計 |IoT Hub によってカスタム エンドポイントと組み込みエンドポイントにルーティングされた合計バイト数。 ディメンションを使用して、特定のエンドポイントまたは特定のルーティング ソースに向けてルーティングされたデータ サイズを特定します。| RoutingSource、<br>EndpointType<br>EndpointName<br>"*詳細については、「[メトリック ディメンション](#metric-dimensions)」を参照してください。* "|
| Routing Latency (ルーティングの待機時間) (プレビュー) |RoutingDeliveryLatency| ミリ秒 | Average |これはルーティングの配信待機時間に関するメトリックです。 ディメンションを使用して、特定のエンドポイントまたは特定のルーティング ソースに向けた待機時間を特定します。| RoutingSource、<br>endpointType、<br>EndpointName<br>"*詳細については、「[メトリック ディメンション](#metric-dimensions)」を参照してください。* "|
|ルーティング: ストレージに配信された BLOB|d2c.endpoints.egress.storage.blobs|Count|合計|IoT Hub ルーティングで BLOB がストレージ エンドポイントに配信された回数。|なし|
|ルーティング: ストレージに配信されたデータ|d2c.endpoints.egress.storage.bytes|バイト|合計|IoT Hub ルーティングでストレージ エンドポイントに配信されたデータの量 (バイト)。|なし|
|ルーティング: Event Hub のメッセージの待機時間|d2c.endpoints.latency.eventHubs|ミリ秒|Average|IoT Hub への受信メッセージと、カスタム エンドポイント型の Event Hub への受信メッセージの間の平均待機時間 (ミリ秒)。 これには、組み込みエンドポイントへのメッセージ ルート (イベント) は含まれません。|なし|
|ルーティング: Service Bus キューのメッセージの待機時間|d2c.endpoints.latency.serviceBusQueues|ミリ秒|Average|IoT Hub への受信メッセージと、Service Bus キュー エンドポイントへの受信メッセージの間の平均待機時間 (ミリ秒)。|なし|
|ルーティング: Service Bus トピックのメッセージの待機時間|d2c.endpoints.latency.serviceBusTopics|ミリ秒|Average|IoT Hub への受信メッセージと、Service Bus トピック エンドポイントへの受信メッセージの間の平均待機時間 (ミリ秒)。|なし|
|ルーティング: メッセージ/イベントのメッセージの待機時間|d2c.endpoints.latency.builtIn.events|ミリ秒|Average|IoT Hub への受信メッセージと、組み込みエンドポイント (メッセージ/イベント) およびフォールバック ルートへの受信メッセージの間の平均待機時間 (ミリ秒)。|なし|
|ルーティング: ストレージのメッセージの待機時間|d2c.endpoints.latency.storage|ミリ秒|Average|IoT Hub への受信メッセージと、ストレージ エンドポイントへの受信メッセージの間の平均待機時間 (ミリ秒)。|なし|
|ルーティング: Event Hub に配信されたメッセージ|d2c.endpoints.egress.eventHubs|Count|合計|IoT Hub ルーティングにより、メッセージがカスタム エンドポイント型の Event Hub に正常に配信された回数。 これには、組み込みエンドポイントへのメッセージ ルート (イベント) は含まれません。|なし|
|ルーティング: Service Bus キューに配信されたメッセージ|d2c.endpoints.egress.serviceBusQueues|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus キュー エンドポイントに正常に配信された回数。|なし|
|ルーティング: Service Bus トピックに配信されたメッセージ|d2c.endpoints.egress.serviceBusTopics|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus トピック エンドポイントに正常に配信された回数。|なし|
|ルーティング: フォールバックのために配信されたメッセージ|d2c.telemetry.egress.fallback|Count|合計|IoT Hub ルーティングにより、フォールバック ルートに関連付けられているエンドポイントにメッセージが配信された回数。|なし|
|ルーティング: メッセージ/イベントに配信されたメッセージ|d2c.endpoints.egress.builtIn.events|Count|合計|IoT Hub ルーティングにより、メッセージが組み込みのエンドポイント (メッセージ/イベント) とフォールバック ルートに正常に配信された回数。|なし|
|ルーティング: ストレージに配信されたメッセージ|d2c.endpoints.egress.storage|Count|合計|IoT Hub ルーティングにより、メッセージがストレージ エンドポイントに正常に配信された回数。|なし|
|ルーティング: 配信されたテレメトリ メッセージ|d2c.telemetry.egress.success|Count|合計|IoT Hub ルーティングを使用して、メッセージがすべてのエンドポイントに正常に配信された回数。 メッセージが複数のエンドポイントにルーティングされている場合、この値は正常に配信されるたびに 1 つずつ増えます。 メッセージが同じエンドポイントに複数回配信されている場合、この値は正常に配信されるたびに 1 つずつ増えます。|なし|
|ルーティング: 破棄されたテレメトリ メッセージ |d2c.telemetry.egress.dropped|Count|合計|デッド エンドポイントであるため、メッセージが IoT Hub ルーティングで破棄された回数。 この値では、フォールバック ルートに配信されるメッセージはカウントされません。破棄されたメッセージはそこには配信されないためです。|なし|
|ルーティング: 互換性のないテレメトリ メッセージ|d2c.telemetry.egress.invalid|Count|合計|エンドポイントと互換性がないため、IoT Hub ルーティングでメッセージを配信できなかった回数。 Iot Hub がエンドポイントにメッセージを配信しようとしたときに一時的でないエラーで失敗する場合、そのメッセージはエンドポイントに適合していません。 無効なメッセージは再試行されません。 この値に再試行回数は含まれません。|なし|
|ルーティング: 孤立したテレメトリ メッセージ |d2c.telemetry.egress.orphaned|Count|合計|フォールバック ルートが無効にされたときにどのルーティング クエリにも一致しなかったため、IoT Hub ルーティングによってメッセージが孤立した回数。|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

### <a name="twin-query-metrics"></a>ツイン クエリのメトリック

|メトリックの表示名|メトリック|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|失敗したツイン クエリ|twinQueries.failure|Count|合計|失敗したツイン クエリの数。|なし|
|成功したツイン クエリ|twinQueries.success|Count|合計|成功したツイン クエリの数。|なし|
|ツイン クエリの結果のサイズ|twinQueries.resultSize|バイト|Average|成功したすべてのツイン クエリの結果の合計サイズ。|なし|

**[ユニット]** 値が **Count** のメトリックの場合、合計 (Sum) の集計のみが有効です。 Minimum、Maximum、Average の集計では、常に 1 が返されます。 詳細については、「[サポートされる集計](#supported-aggregations)」を参照してください。

## <a name="metric-dimensions"></a>メトリック ディメンション

Azure IoT Hub には、ルーティングと Event Grid の一部のメトリックに関連付けられた次のディメンションがあります。

|ディメンション名 | 説明|
|---|---|
||
|**EndpointName**| エンドポイント名。|
|**EndpointType**|次のいずれかになります: **eventHubs**、**serviceBusQueues**、**cosmosDB**、**serviceBusTopics**、 **builtin**、または **blobStorage**。|
|**EventType**| 次に示す Event Grid イベントの種類のいずれかになります: **Microsoft.Devices.DeviceCreated**、 **Microsoft.Devices.DeviceDeleted**、**Microsoft.Devices.DeviceConnected**、**Microsoft.Devices.DeviceDisconnected**、または **Microsoft.Devices.DeviceTelemetry**。 詳細については、「[イベントの種類](iot-hub-event-grid.md#event-types)」を参照してください。|
|**FailureReasonCategory**| 次のいずれかになります: **invalid**、**dropped**、**orphaned**、または **null**。|
|**結果**| **success** または **failure**。|
|**RoutingSource**| デバイス メッセージ<br>ツイン変更イベント<br>デバイス ライフサイクル イベント|

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

## <a name="resource-logs"></a>リソース ログ

このセクションには、Azure IoT Hub 用に収集されるすべてのリソース ログ カテゴリの種類とスキーマの一覧が表示されています。 すべての IoT Hub ログのリソース プロバイダーと種類は、[Microsoft.Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs) です。

**このセクションのトピック**

- [Azure IoT Hub の監視データのリファレンス](#monitoring-azure-iot-hub-data-reference)
  - [メトリック](#metrics)
    - [サポートされる集計](#supported-aggregations)
    - [クラウドからデバイスへのコマンドのメトリック](#cloud-to-device-command-metrics)
    - [クラウドからデバイスへのダイレクト メソッドのメトリック](#cloud-to-device-direct-methods-metrics)
    - [クラウドからデバイスへのツイン操作のメトリック](#cloud-to-device-twin-operations-metrics)
    - [構成のメトリック](#configurations-metrics)
    - [1 日あたりのクォータのメトリック](#daily-quota-metrics)
    - [デバイス メトリック](#device-metrics)
    - [デバイス テレメトリのメトリック](#device-telemetry-metrics)
    - [デバイスからクラウドへのツイン操作のメトリック](#device-to-cloud-twin-operations-metrics)
    - [Event Grid のメトリック](#event-grid-metrics)
    - [ジョブのメトリック](#jobs-metrics)
    - [ルーティング メトリック](#routing-metrics)
    - [ツイン クエリのメトリック](#twin-query-metrics)
  - [メトリック ディメンション](#metric-dimensions)
  - [リソース ログ](#resource-logs)
    - [接続](#connections)
    - [デバイス テレメトリ](#device-telemetry)
    - [クラウドからデバイスへのコマンド](#cloud-to-device-commands)
    - [デバイス ID の操作](#device-identity-operations)
    - [ファイルのアップロード操作](#file-upload-operations)
    - [ルート](#routes)
    - [デバイスからクラウドへのツイン操作](#device-to-cloud-twin-operations)
    - [クラウドからデバイスへのツイン操作](#cloud-to-device-twin-operations)
    - [ツイン クエリ](#twin-queries)
    - [ジョブ操作](#jobs-operations)
    - [ダイレクト メソッド](#direct-methods)
    - [分散トレース (プレビュー)](#distributed-tracing-preview)
      - [IoT Hub D2C (device-to-cloud) のログ](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT Hub のイングレス ログ](#iot-hub-ingress-logs)
      - [IoT Hub のエグレス ログ](#iot-hub-egress-logs)
    - [構成](#configurations)
    - [デバイス ストリーム (プレビュー)](#device-streams-preview)
  - [Azure Monitor ログ テーブル](#azure-monitor-logs-tables)
  - [参照](#see-also)

### <a name="connections"></a>接続

接続カテゴリは、エラーだけでなく、IoT Hub に対するデバイスの接続と切断イベントも追跡します。 このカテゴリは、承認されていない接続の試行を識別したり、デバイスへの接続を失ったときに警告したりするのに役立ちます。

> [!NOTE]
> デバイスの信頼できる接続状態については、「[デバイスのハートビート](iot-hub-devguide-identity-registry.md#device-heartbeat)」を参照してください。

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>デバイス テレメトリ

デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。 このカテゴリーには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。 このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできません。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>クラウドからデバイスへのコマンド

C2D コマンド カテゴリでは、IoT Hub で発生し、かつクラウドからデバイスへのメッセージ パイプラインに関連しているエラーを追跡します。 このカテゴリには、以下から発生するエラーが含まれます。

* 送信 cloud-to-device メッセージ (承認されていない送信者のエラーなど)、
* 受信 cloud-to-device メッセージ (配信数超過のエラーなど)、および
* 受信 cloud-to-device メッセージのフィードバック (フィードバックの有効期限切れのエラーなど)。

このカテゴリでは、cloud-to-device メッセージが正常に配信されたときのエラーはキャッチされませんが、デバイスによって不適切に処理されたときのエラーはキャッチされます。

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>デバイス ID の操作

デバイス ID の操作のカテゴリでは、IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。 このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>ファイルのアップロード操作

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。 このカテゴリには、次のエラーが含まれます。

* SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)。

* デバイスによって報告されたアップロード エラー。

* IoT Hub 通知メッセージの作成中にストレージでファイルが見つからないときに発生するエラー。

このカテゴリでは、デバイスがファイルをストレージにアップロードしているときに直接発生したエラーをキャッチできません。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>ルート

[メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md) カテゴリでは、メッセージ ルートの評価時に発生するエラーと、IoT Hub によって認識されるエンドポイントの正常性を追跡します。 このカテゴリには、以下のようなイベントが含まれます。

* "未定義" に評価されるルール、
* IoT Hub がエンドポイントをデッドとしてマークしている、または
* エンドポイントから受信したすべてのエラー。

このカテゴリには、メッセージ自体に関する具体的なエラー (デバイス調整エラーなど) は含まれません。このようなエラーは、"デバイス テレメトリ" カテゴリで報告されます。

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

ルーティング リソース ログの詳細は次のとおりです。

* [ルーティング リソース ログのエラー コードの一覧](troubleshoot-message-routing.md#diagnostics-error-codes)
* [ルーティング リソース ログの操作名の一覧](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>デバイスからクラウドへのツイン操作

デバイスからクラウドへのツイン操作カテゴリでは、デバイス ツイン上のデバイスによって開始されたイベントを追跡します。 これらの操作には、ツインの取得、報告されたプロパティの更新、および必要なプロパティへのサブスクライブが含まれます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>クラウドからデバイスへのツイン操作

クラウドからデバイスへのツイン操作カテゴリでは、デバイス ツイン上のサービスによって開始されたイベントを追跡します。 これらの操作には、ツインの取得、タグの更新または置換、および必要なプロパティの更新または置換が含まれます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>ツイン クエリ

ツイン クエリのカテゴリでは、クラウドで開始されたデバイス ツインのクエリ要求を報告します。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>ジョブ操作

ジョブ操作カテゴリでは、デバイス ツインを更新するジョブ要求または複数のデバイスでダイレクト メソッドを呼び出すジョブ要求を報告します。 これらの要求は、クラウドで開始されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>ダイレクト メソッド

ダイレクト メソッド カテゴリでは、個々のデバイスに送信される要求 - 応答のインタラクションを追跡します。 これらの要求は、クラウドで開始されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>分散トレース (プレビュー)

分散トレース カテゴリでは、トレース コンテキスト ヘッダーが含まれれるメッセージの関連付け ID が追跡されます。 これらのログを完全に有効にするには、[IoT Hub 分散トレース (プレビュー) を使用した IoT アプリケーションの エンド ツー エンドの分析と診断](iot-hub-distributed-tracing.md)に関する記事に従って、クライアント側のコードを更新する必要があります。

`correlationId` が [W3C トレース コンテキスト](https://github.com/w3c/trace-context)の提案に準拠していることに注意してください。これには、`trace-id` と `span-id` が含まれます。

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (device-to-cloud) のログ

IoT Hub では、有効なトレース プロパティを含むメッセージが IoT Hub に到着すると、このログが記録されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

ここで、IoT Hub のクロックがデバイスのクロックと同期していない可能性があり、経過時間を計算すると誤解を招く場合があるので、`durationMs` は計算されません。 `properties` セクションのタイムスタンプを使用するロジックを記述して、device-to-cloud 待機時間のスパイクをキャプチャすることをお勧めします。

| プロパティ | Type | 説明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | device-to-cloud メッセージのサイズ (バイト単位) |
| **deviceId** | ASCII の 7 ビットの英数字の文字列 | デバイスの ID |
| **callerLocalTimeUtc** | UTC タイムスタンプ | デバイスのローカル クロックによって報告されたメッセージの作成時刻 |
| **calleeLocalTimeUtc** | UTC タイムスタンプ | IoT Hub サービス側のクロックによって報告された、IoT Hub のゲートウェイへのメッセージの到着時刻 |

#### <a name="iot-hub-ingress-logs"></a>IoT Hub のイングレス ログ

有効なトレース プロパティを含むメッセージが内部または組み込みの Event Hub に書き込むと、IoT Hub でこのログが記録されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties` セクションでは、このログにはメッセージのイングレスに関する追加情報が含まれています

| プロパティ | Type | 説明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | true または false。IoT Hub でメッセージのルーティングが有効になっているかどうかを示します |
| **parentSpanId** | String | 親メッセージの [span-id](https://w3c.github.io/trace-context/#parent-id)。この場合は、D2C のメッセージ トレースです |

#### <a name="iot-hub-egress-logs"></a>IoT Hub のエグレス ログ

[ルーティング](iot-hub-devguide-messages-d2c.md)が有効になっていて、メッセージが[エンドポイント](iot-hub-devguide-endpoints.md)に書き込まれると、IoT Hub でこのログが記録されます。 ルーティングが有効でない場合、IoT Hub はこのログを記録しません。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties` セクションでは、このログにはメッセージのイングレスに関する追加情報が含まれています

| プロパティ | Type | 説明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | ルーティング エンドポイントの名前 |
| **endpointType** | String | ルーティング エンドポイントの種類 |
| **parentSpanId** | String | 親メッセージの [span-id](https://w3c.github.io/trace-context/#parent-id)。この場合は、IoT Hub のイングレス メッセージ トレースです |

### <a name="configurations"></a>構成

IoT Hub 構成ログでは、自動デバイス管理機能セットのイベントとエラーが追跡されます。

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>デバイス ストリーム (プレビュー)

デバイス ストリーム カテゴリでは、個々のデバイスに送信される要求 - 応答のインタラクションが追跡されます。

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル
<!-- REQUIRED. Please keep heading in this order -->

このセクションでは、Azure IoT Hub に関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログの Kusto テーブルを参照します。 これらのテーブルの一覧と、IoT Hub リソースの種類に関する詳細情報へのリンクについては、Azure Monitor ログ テーブル リファレンスの「[IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub)」を参照してください。

すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。

## <a name="see-also"></a>参照

* Azure IoT Hub の監視については、[Azure IoT Hub の監視](monitor-iot-hub.md)に関するページを参照してください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
