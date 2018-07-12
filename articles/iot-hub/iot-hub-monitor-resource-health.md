---
title: Azure IoT Hub の正常性の監視 | Microsoft Docs
description: Azure Monitor と Azure Resource Health を使用して IoT Hub を監視し、問題を迅速に診断します
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 39171f7d7a7b27ec54f67b592e184e90134a1a52
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611373"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Azure IoT Hub の正常性を監視し、問題をすばやく診断する

Azure IoT Hub を実装する企業では、そのリソースに信頼性の高いパフォーマンスを期待します。 IoT Hub は、[Azure Monitor][lnk-AM] および [Azure Resource Health][lnk-ARH] と完全に統合され、操作を緊密に監視するのに役立ちます。 これらの 2 つのサービスは連携して、IoT ソリューションの実行を正常な状態に保つために必要なデータを提供します。 

Azure Monitor は、すべての Azure サービスの監視およびログの唯一のソースです。 Azure Monitor が生成する診断ログを Log Analytics、Event Hubs、または Azure Storage に送信して、カスタムの処理を実行できます。 Azure Monitor のメトリックと診断の設定により、リソースのパフォーマンスを把握できます。 この記事を読み進めると、IoT Hub で [Azure Monitor を使用する](#use-azure-monitor)方法を理解することができます。 

> [!IMPORTANT]
> Azure Monitor の診断ログを使用して IoT Hub サービスによって出力されるイベントは、信頼性も順序も保証されません。 一部のイベントが失われたり、順序どおりに配信されないことがあります。 さらに、診断ログはリアルタイムではなく、選択した出力先に記録されるまで数分かかる場合があります。

Azure Resource Health は、リソースが Azure の問題を影響を受けているときに診断を行ったりサポートを受けたりする場合に役立ちます。 パーソナライズされたダッシュボードには、IoT Hub の現在および過去の状態が表示されます。 この記事を読み進めると、IoT Hub で [Azure Resource Health を使用する](#use-azure-resource-health)方法を理解することができます。 

IoT Hub は、これらの 2 つのサービスとの統合に加えて、IoT リソースの状態を把握するための独自のメトリックも提供します。 詳細については、「[IoT Hub メトリックの理解][lnk-metrics]」を参照してください。

## <a name="use-azure-monitor"></a>Azure Monitor の使用

Azure Monitor は、リソース レベルの診断情報を提供します。これは、IoT Hub 内で実行される操作を監視できることを意味します。 

Azure Monitor の診断設定は、IoT Hub の操作の監視機能を置き換えます。 現在、操作の監視機能を使用している場合は、ワークフローを移行する必要があります。 詳細については、[操作の監視から診断設定への移行][lnk-migrate]に関するページを参照してください。

Azure Monitor が監視する特定のメトリックとイベントの詳細については、「[Azure Monitor のサポートされるメトリック][lnk-AM-metrics]」および「[Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ][lnk-AM-schemas]」を参照してください。

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>ログを理解する

Azure Monitor を使用すると、IoT Hub で発生するさまざまな操作を追跡できます。 各カテゴリには、そのカテゴリ内のイベントの報告方法を定義するスキーマがあります。 

#### <a name="connections"></a>接続

接続カテゴリは、エラーだけでなく、IoT Hub に対するデバイスの接続と切断イベントも追跡します。 このカテゴリの追跡は、許可されていない接続の試行を識別する場合、および接続状態が悪い領域内で接続が失われたタイミングを突き止める場合に便利です

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>クラウドからデバイスへのコマンド

C2D コマンド カテゴリでは、IoT Hub で発生し、かつクラウドからデバイスへのメッセージ パイプラインに関連しているエラーを追跡します。 このカテゴリには、クラウドからデバイスへのメッセージの送信時のエラー (許可されていない送信者など)、クラウドからデバイスへのメッセージの受信時のエラー (配信数が上限を超えているなど)、クラウドからデバイスへのメッセージ フィードバックの受信時のエラー (フィードバックの有効期限切れなど) が含まれます。 このカテゴリでは、クラウドからデバイスへのメッセージが正常に配信されてもクラウドからデバイスへのメッセージを適切に処理しないデバイスのエラーはキャッチしません。

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>デバイス ID の操作

デバイス ID の操作のカテゴリでは、IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。 このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

```json
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
```

#### <a name="routes"></a>ルート

メッセージ ルーティング カテゴリは、メッセージ ルート評価および IoT Hub によって認識されるエンドポイント正常性において発生するエラーを追跡します。 このカテゴリには、ルールが "未定義" と評価されたとき、IoT Hub がエンドポイントをデッドとしてマークしたとき、およびエンドポイントから受信したその他のすべてのエラーなどのイベントが含まれます。 このカテゴリには、メッセージ自体に関する具体的なエラー (デバイス調整エラーなど) は含まれません。このようなエラーは、"デバイス テレメトリ" カテゴリで報告されます。

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>デバイス テレメトリ

デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。 このカテゴリーには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。 このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできません。

```json
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
```

#### <a name="file-upload-operations"></a>ファイルのアップロード操作

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。 このカテゴリには、次のエラーが含まれます。

* SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)。
* デバイスによって報告されたアップロード エラー。
* IoT Hub 通知メッセージの作成中にストレージでファイルが見つからないときに発生するエラー。

このカテゴリでは、デバイスがファイルをストレージにアップロードしているときに直接発生したエラーをキャッチできません。

```json
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
```

#### <a name="cloud-to-device-twin-operations"></a>クラウドからデバイスへのツイン操作

クラウドからデバイスへのツイン操作カテゴリでは、デバイス ツイン上のサービスによって開始されたイベントを追跡します。 これらの操作には、ツインの取得、報告されたプロパティの更新、および必要なプロパティへのサブスクライブが含まれます

```json
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
```

#### <a name="device-to-cloud-twin-operations"></a>デバイスからクラウドへのツイン操作

デバイスからクラウドへのツイン操作カテゴリでは、デバイス ツイン上のデバイスによって開始されたイベントを追跡します。 これらの操作には、ツインの取得、タグの更新または置換、および必要なプロパティの更新または置換が含まれます。 

```json
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
```

#### <a name="twin-queries"></a>ツイン クエリ

ツイン クエリのカテゴリでは、クラウドで開始されたデバイス ツインのクエリ要求を報告します。 

```json
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
```

#### <a name="jobs-operations"></a>ジョブ操作

ジョブ操作カテゴリでは、デバイス ツインを更新するジョブ要求または複数のデバイスでダイレクト メソッドを呼び出すジョブ要求を報告します。 これらの要求は、クラウドで開始されます。 

```json
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
```

#### <a name="direct-methods"></a>ダイレクト メソッド

ダイレクト メソッド カテゴリでは、個々のデバイスに送信される要求/応答のインタラクションを追跡します。 これらの要求は、クラウドで開始されます。 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Azure Event Hubs からのログの読み取り

診断設定を使用してイベント ログを設定した後は、ログを読み取るアプリケーションを作成できます。これにより、ログの情報を基にしてアクションを実行できるようになります。 イベント ハブからログを取得するサンプル コードを次に示します。

```csharp
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
    static void Main(string[] args) 
    { 
        Console.WriteLine("Monitoring. Press Enter key to exit.\n"); 
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName); 
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
        CancellationTokenSource cts = new CancellationTokenSource(); 
        var tasks = new List<Task>(); 
        foreach (string partition in d2cPartitions) 
        { 
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token)); 
        } 
        Console.ReadLine(); 
        Console.WriteLine("Exiting..."); 
        cts.Cancel(); 
        Task.WaitAll(tasks.ToArray()); 
    } 
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct) 
    { 
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow); 
        while (true) 
        { 
            if (ct.IsCancellationRequested) 
            { 
                await eventHubReceiver.CloseAsync(); 
                break; 
            } 
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10)); 
            if (eventData != null) 
            { 
                string data = Encoding.UTF8.GetString(eventData.GetBytes()); 
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data); 
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Azure Resource Health の使用

Azure Resource Health を使用すると、IoT Hub が実行中かどうかを監視できます。 また、リージョンで発生した停電が IoT Hub の正常性に影響を与えているかどうかを知ることもできます。 Azure IoT Hub の正常性状態に関する詳細を理解するには、[Azure Monitor を使用する](#use-azure-monitor)ことをお勧めします。 

Azure IoT Hub では、リージョン レベルでの正常性が示されます。 リージョンで発生した停電の影響が IoT Hub に及んでいる場合、正常性状態は **[不明]** と表示されます。 Azure Resource Health が実行する特定の正常性チェックの詳細については、「[Azure Resource Health で利用できるリソースの種類と正常性チェック][lnk-ARH-checks]」を参照してください。

IoT Hub の正常性を確認するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Service Health]\(サービス正常性\)** > **[リソース正常性]** に移動します。
1. ドロップダウン ボックスから、サブスクリプションと **IoT Hub** を選択します。

正常性データを解釈する方法の詳細については、「[Azure Resource Health の概要][lnk-ARH]」を参照してください

## <a name="next-steps"></a>次の手順

- [IoT Hub メトリックの理解][lnk-metrics]
- [Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
