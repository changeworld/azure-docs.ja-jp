---
title: 監視とログ記録 - Azure
description: この記事では、Live Video Analytics on IoT Edge での監視とログ記録の概要について説明します。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e81b1e98fb30bb8876c78c8c911585f5448db8f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730246"
---
# <a name="monitoring-and-logging"></a>監視およびログ記録

この記事では、リモート監視のために Live Video Analytics on IoT Edge モジュールからイベントを受け取る方法について説明します。 

また、モジュールによって生成されるログを制御する方法についても説明します。

## <a name="taxonomy-of-events"></a>イベントの分類

Live Video Analytics on IoT Edge によって出力されるイベントまたはテレメトリ データは、次の分類に従います。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="イベントの分類を示す図。":::

* 運用時:ユーザーの操作によって、または[メディア グラフ](media-graph-concept.md)の実行の間に生成されるイベント
   
   * 量: 少ないものと予想されます (1 分間に 2、3 回、またはそれ未満)
   * 例 :

      - 記録開始 (次の例を参照)
      - 記録停止
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 診断:パフォーマンスに関する問題の診断に役立つイベント

   * 量: 多い可能性があります (1 分間に数回)
   * 例 :
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 情報 (次の例を参照) 
      - 受信ビデオ フィードのギャップ

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics:ビデオ分析の一部として生成されるイベント

   * 量: 多い可能性があります (1 分間に数回以上)
   * 例 :
      
      - モーション検出 (次の例を参照) 
      - 推論結果

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

モジュールによって出力されたイベントは、[IoT Edge ハブ](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)に送信されます。 そこから他の宛先にルーティングすることができます。 

### <a name="timestamps-in-analytic-events"></a>分析イベントのタイムスタンプ

前に示したように、ビデオ分析の一部として生成されるイベントには、タイムスタンプが関連付けられています。 グラフ トポロジの一部として[ライブ ビデオを記録した](video-recording-concept.md)場合は、これらのタイムスタンプを使用して、記録したビデオ内のどこで特定のイベントが発生したかを見つけることができます。 次に示すのは、[Azure Media Service 資産](terminology.md#asset)に記録されたビデオのタイムラインに分析イベントのタイムスタンプをマップする方法に関するガイドラインです。

まず、`eventTime` 値を抽出します。 [時間範囲フィルター](playback-recordings-how-to.md#time-range-filters)でこの値を使用して、記録から適切な部分を取得します。 たとえば、`eventTime` の 30 秒前に開始し、その 30 秒後に終了するビデオを取得できます。 前の例では、`eventTime` が 2020-05-12T23:33:09.381Z の場合、`eventTime` の前後 30 秒間に対する HLS マニフェストの要求は次のようになります。

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

上記の URL からは、メディア プレイリストの URL が含まれる[マスター プレイリスト](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)が返されます。 メディア プレイリストには、次のようなエントリが含まれます。

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
上記のエントリでは、`timestamp` の値が `143039375031270` のときに開始するビデオ フラグメントを使用できることが示されています。 分析イベントの `timestamp` の値によって使用されるタイムスケールは、メディア プレイリストと同じです。 それを使用して関連するビデオ フラグメントを識別し、正しいフレームにシークできます。

詳細については、HLS の[フレームが正確なシークに関する記事](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS)を参照してください。

## <a name="controlling-events"></a>イベントの制御

次のモジュール ツイン プロパティを使用して、Live Video Analytics on IoT Edge モジュールによって発行された操作および診断イベントを制御できます。 これらのプロパティについては、[モジュール ツインの JSON スキーマ](module-twin-configuration-schema.md)に記載されています。

- `diagnosticsEventsOutputName`:モジュールから診断イベントを取得するには、このプロパティを含めて、それに何らかの値を指定します。 モジュールによる診断イベントの発行を停止するには、これを省略するか、空のままにします。
   
- `operationalEventsOutputName`:モジュールから操作イベントを取得するには、このプロパティを含めて、それに何らかの値を指定します。 モジュールによる操作イベントの発行を止めるには、これを省略するか、空のままにします。
   
分析イベントは、モーション検出プロセッサや HTTP 拡張プロセッサのようなノードによって生成されます。 それらを IoT Edge ハブに送信するには、IoT ハブ シンクを使用します。 

[前記のすべてのイベントのルーティング](../../iot-edge/module-composition.md#declare-routes)は、配置マニフェストの `$edgeHub` モジュール ツインの `desired` プロパティを使用して制御できます。

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

前の JSON で、`lvaEdge` は Live Video Analytics on IoT Edge モジュールの名前です。 ルーティング規則は、「[ルートの宣言](../../iot-edge/module-composition.md#declare-routes)」で定義されているスキーマに従います。

> [!NOTE]
> 分析イベントが IoT Edge ハブに確実に渡るようにするには、モーション検出プロセッサ ノードや HTTP 拡張機能プロセッサ ノードの下流に IoT ハブ シンク ノードを配置する必要があります。

## <a name="event-schema"></a>イベント スキーマ

イベントはエッジ デバイス上で生成され、エッジまたはクラウドで使用できます。 Live Video Analytics on IoT Edge によって生成されるイベントは、Azure IoT Hub によって確立された[ストリーミング メッセージング パターン](../../iot-hub/iot-hub-devguide-messages-construct.md)に準拠しています。 パターンは、システム プロパティ、アプリケーション プロパティ、本文で構成されます。

### <a name="summary"></a>まとめ

IoT Hub によって観測されるすべてのイベントには、一連の共通プロパティがあります。

|プロパティ   |プロパティの種類| データ型   |説明|
|---|---|---|---|
|`message-id`   |システム |guid|  一意のイベント ID。|
|`topic`|   applicationProperty |string|    Azure Media Services アカウントの Azure Resource Manager パス。|
|`subject`| applicationProperty |string|    イベントを出力するエンティティのサブパス。|
|`eventTime`|   applicationProperty|    string| イベントが生成された時刻。|
|`eventType`|   applicationProperty |string|    イベントの種類の識別子。 (次のセクションを参照。)|
|`body`|body    |object|    特定のイベント データ。|
|`dataVersion`  |applicationProperty|   string  |{Major}.{Minor}|

### <a name="properties"></a>Properties

#### <a name="message-id"></a>message-id

イベントのグローバル一意識別子 (GUID)。

#### <a name="topic"></a>topic

グラフに関連付けられている Azure Media Services アカウントを表します。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

イベントを出力しているエンティティ:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject` プロパティを使用すると、汎用イベントを生成モジュールにマップできます。 たとえば、RTSP のユーザー名またはパスワードが無効な場合、生成されるイベントは `/graphInstances/myGraph/sources/myRtspSource` ノードの `Microsoft.Media.Graph.Diagnostics.ProtocolError` になります。

#### <a name="event-types"></a>イベントの種類

イベントの種類は、このスキーマに従って名前空間に割り当てられます。

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>イベント クラス

|クラス名|説明|
|---|---|
|Analytics  |コンテンツ分析の一環として生成されるイベント。|
|診断    |問題とパフォーマンスの診断に役立つイベント。|
|運用時    |リソース操作の一環として生成されるイベント。|

イベントの種類は、各イベント クラスに固有です。

例 :

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>イベント時間

イベント時間は ISO 8601 文字列で書式設定されます。 イベントが発生した日時を表します。

### <a name="azure-monitor-collection-via-telegraf"></a>Telegraf を使用した Azure Monitor コレクション

これらのメトリックは Live Video Analytics on IoT Edge モジュールから報告されます。  

|メトリックの名前|Type|Label|説明|
|-----------|----|-----|-----------|
|lva_active_graph_instances|ゲージ|iothub、edge_device、module_name、graph_topology|トポロジあたりのアクティブなグラフの合計数。|
|lva_received_bytes_total|カウンタ|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node|ノードによって受信された合計バイト数。 RTSP ソースについてのみサポートされます。|
|lva_data_dropped_total|カウンタ|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node、data_kind|ドロップされたデータ (イベント、メディアなど) のカウンター。|

> [!NOTE]
> [Prometheus エンドポイント](https://prometheus.io/docs/practices/naming/)は、コンテナーのポート 9600 で公開されています。 Live Video Analytics on IoT Edge モジュールに "lvaEdge" という名前を付けた場合、 http://lvaEdge:9600/metrics に GET 要求を送信することによってメトリックにアクセスできます。   

IoT Edge モジュールでの Live Video Analytics からメトリックを収集できるようにするには、次の手順に従います。

1. 開発用コンピューターにフォルダーを作成し、そのフォルダーに移動します。

1. そのフォルダーに、次の構成を含む `telegraf.toml` ファイルを作成します。
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > 必ず、.toml ファイル内の変数を置き換えてください。 変数は、中かっこ (`{}`) で示されます。

1. 同じフォルダーに、次のコマンドを含む Dockerfile を作成します。
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Docker CLI のコマンドを使用して Docker ファイルをビルドし、イメージを Azure コンテナー レジストリに発行します。
    
   Docker CLI を使用してコンテナー レジストリにプッシュする方法の詳細については、[Docker イメージのプッシュとプル](../../container-registry/container-registry-get-started-docker-cli.md)に関する記事を参照してください。 Azure Container Registry のその他の情報については、[こちらのドキュメント](../../container-registry/index.yml)を参照してください。


1. Azure Container Registry へのプッシュが完了したら、次のノードを配置マニフェスト ファイルに追加します。
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > マニフェスト ファイル内の変数は必ず置き換えてください。 変数は、中かっこ (`{}`) で示されます。


   Azure Monitor の[認証はサービス プリンシパルを使用して行う](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)ことができます。
        
   Azure Monitor Telegraf プラグインからは、[複数の認証方法](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)が公開されています。 

  1. サービス プリンシパル認証を使用するには、次の環境変数を設定します。  
     `AZURE_TENANT_ID`:認証を行うテナントを指定します。  
     `AZURE_CLIENT_ID`:使用するアプリ クライアント ID を指定します。  
     `AZURE_CLIENT_SECRET`:使用するアプリ シークレットを指定します。  
     
     >[!TIP]
     > サービス プリンシパルには、**監視メトリック パブリッシャー** ロールを指定できます。 「 **[サービス プリンシパルを作成する](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** 」の手順に従って、サービス プリンシパルを作成し、ロールを割り当てます。

1. モジュールがデプロイされると、Azure Monitor で 1 つの名前空間の下にメトリックが表示されます。 メトリック名は、Prometheus によって出力されたものと一致します。 

   この場合は、Azure portal で IoT ハブに移動し、左側のペインで **[メトリック]** を選択します。 ここでメトリックが表示されます。

### <a name="log-analytics-metrics-collection"></a>Log Analytics メトリックの収集
[Prometheus エンドポイント](https://prometheus.io/docs/practices/naming/)を [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-analytics-tutorial) と一緒に使用すると、CPUPercent、MemoryUsedPercent などのメトリックを生成して[それらを監視](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)できます。   

> [!NOTE]
> 以下の構成で収集されるのは **メトリックのみ** です。ログは収集されません。 コレクター モジュールを拡張すれば、ログの収集とアップロードも行うことができます。

[ ![Log Analytics を使用したメトリックの収集を示す図。](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. [メトリックの収集](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)方法を確認します。
1. Docker CLI のコマンドを使用して [Docker ファイル](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux)をビルドし、イメージを Azure コンテナー レジストリに発行します。
    
   Docker CLI を使用してコンテナー レジストリにプッシュする方法の詳細については、[Docker イメージのプッシュとプル](../../container-registry/container-registry-get-started-docker-cli.md)に関する記事を参照してください。 Azure Container Registry のその他の情報については、[こちらのドキュメント](../../container-registry/index.yml)を参照してください。

1. Azure Container Registry へのプッシュが完了すると、配置マニフェストに次の内容が挿入されます。
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > `edgeHub`、`edgeAgent`、`lvaEdge` の各モジュールは、配置マニフェスト ファイルに定義されているモジュールの名前です。 モジュールの名前が一致していることを確認してください。   

    `LogAnalyticsWorkspaceId` と `LogAnalyticsSharedKey` の値は、次の手順に従って取得できます。
    1. Azure portal にアクセスします
    1. Log Analytics ワークスペースを検索します
    1. Log Analytics ワークスペースが見つかったら、左側のナビゲーション ペインの `Agents management` オプションに移動します。
    1. 使用できるワークスペース ID とシークレット キーが表示されます。

1. 次に、左側のナビゲーション ペインにある [`Workbooks`] タブをクリックしてブックを作成します。
1. Kusto クエリ言語を使用すると、次のようにクエリを記述し、IoT Edge のモジュールで使用される CPU 使用率を取得できます。
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Kusto クエリを使用したメトリックを示す図。](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>ログ記録

他の IoT Edge モジュールと同様に、エッジ デバイスで[コンテナーのログを調べる](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)こともできます。 ログに書き込まれる情報は、[次のモジュール ツイン](module-twin-configuration-schema.md) プロパティを使用して構成できます。

* `logLevel`

   * 指定できる値は、`Verbose`、`Information`、`Warning`、`Error`、および `None` です。
   * 既定値は `Information` です。 ログには、エラー、警告、情報のメッセージが含まれます。
   * 値を `Warning` に設定すると、ログにはエラーと警告のメッセージが含まれます。
   * 値を `Error` に設定すると、ログにはエラー メッセージのみが含まれます。
   * 値を `None` に設定すると、ログは生成されません。 (この構成はお勧めしません。)
   * `Verbose` は、問題を診断するために Azure サポートとログを共有する必要がある場合にのみ使用してください。

* `logCategories`

   * `Application`、`Events`、`MediaPipeline` の 1 つ以上の値のコンマ区切りのリスト。
   * 既定値は `Application, Events` です。
   * `Application`:モジュールのスタートアップ メッセージ、環境エラー、ダイレクト メソッド呼び出しなど、モジュールからの概要情報です。
   * `Events`:この記事で既に説明したすべてのイベントです。
   * `MediaPipeline`:RTSP 対応カメラとの接続を確立するのが難しいなどの問題をトラブルシューティングするときに、分析情報が提供される可能性がある詳細なログです。
   
### <a name="generating-debug-logs"></a>デバッグ ログの生成

場合によっては、Azure サポートによる問題の解決を支援するため、上で説明したものより詳細なログを生成することが必要になる場合があります。 これらのログを生成するには:

1. `createOptions` を使用して、[モジュール ストレージをデバイス ストレージにリンク](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage)します。 クイックスタートから[配置マニフェスト テンプレート](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)を見ると、次のようなコードがあります。

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   このコードにより、Edge モジュールはデバイス ストレージ パス `/var/local/mediaservices/` にログを書き込むことができます。 

 1. モジュールに次の `desired` プロパティを追加します。

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

これにより、モジュールからデバイス ストレージ パス `/var/local/mediaservices/debuglogs/` に、バイナリ形式のデバッグ ログが書き込まれます。 これらのログを Azure サポートと共有できます。

## <a name="faq"></a>よく寄せられる質問

質問がある場合は、[監視とメトリックに関する FAQ](faq.md#monitoring-and-metrics) を参照してください。

## <a name="next-steps"></a>次のステップ

[継続的なビデオ記録](continuous-video-recording-tutorial.md)
