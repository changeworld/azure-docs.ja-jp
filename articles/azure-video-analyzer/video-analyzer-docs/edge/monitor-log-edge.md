---
title: 監視とログ記録 - Azure
description: この記事では、Azure Video Analyzer での監視とログ記録の概要について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed86a5de4fd5d65ec3e5a3b8112cb96c95f01811
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399941"
---
# <a name="monitor-and-log-on-iot-edge"></a>IoT Edge の監視とログオン

![Edge アイコン](media/env-icon/edge.png)  
または、[サービスでの監視とログ](../cloud/monitor-log-cloud.md)に関する記事を参照してください。

---

この記事では、リモート監視のために Azure Video Analyzer on IoT Edge モジュールからイベントを受け取る方法について説明します。 

また、モジュールによって生成されるログを制御する方法についても説明します。

## <a name="taxonomy-of-events"></a>イベントの分類

IoT Edge の最上位に構築される Video Analyzer によって出力されるイベントまたはテレメトリ データは、次の分類に従います。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="イベントの分類を示す図。":::

* 運用時: ユーザーの操作によって、または[パイプライン](../pipeline.md)の実行中に生成されるイベント
  
   * 量: 少ないものと予想されます (1 分間に 2、3 回、またはそれ未満)
   * 例 :

      - アクティブ化されたライブのパイプライン
      
      - 非アクティブ化されたライブのパイプライン
      
      *サンプルの操作イベント*
      
      ```json
      {
         "body": {
             "outputType": "filePath",
             "outputLocation": "/var/media/Sample-1-fileSink/sampleFilesFromEVR-motion-fileSinkOutput-20210426T181911Z.mp4"
           },
           "properties": {
             "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
             "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
             "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
             "eventTime": "2021-04-26T18:19:13.298Z",
             "dataVersion": "1.0"
          },
      }
      ```
* 診断:パフォーマンスに関する問題の診断に役立つイベント

   * 量: 多い可能性があります (1 分間に数回)
   * 例 :
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 情報 (次の例を参照)       
      - カメラや AI 拡張機能などへの接続が失敗したときのエラー
      
    *サンプル診断イベント*
  
    ```json
    {
      "body": {
        "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
      },
      "applicationProperties": {
        "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
        "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
        "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
        "eventTime": "2021-04-26T18:15:13.298Z",
        "dataVersion": "1.0"
      }
    }
    ```
* 分析: ビデオ解析の結果として生成されたイベント

   * 量: 多い可能性があります (1 分間に数回以上)
   * 例 :
     
      - モーション検出 (次の例を参照) 
      
      - 推論結果
     
      *サンプル解析イベント*
        
      ```json
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
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
          "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/md",
          "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
          "eventTime": "2021-04-26T18:15:13.298Z",
          "dataVersion": "1.0"
        }
      }
      ```
        
モジュールによって出力されたイベントは、[IoT Edge ハブ](../../../iot-edge/iot-edge-runtime.md#iot-edge-hub)に送信されます。 そこから他の宛先にルーティングすることができます。 

### <a name="events-and-video-playback"></a>イベントとビデオ再生

上記のとおり、ビデオ解析の一環として生成されたイベントには、`eventTime` が関連付けられています。 パイプライン トポロジの一部として[ライブ ビデオを記録した](../video-recording.md)場合は、記録したビデオ内のどこで特定のイベントが発生したかを見つけることができます。 [Video Analyzer プレーヤー ウィジェット](../player-widget.md)にビデオ記録を読み込み、そのコントロールを使用して、関心のある日時をシークできます。 パイプラインで AI モデルを使用して推論結果を生成する場合は、ビデオと共に推論データを記録する必要があります。 これにより、この[チュートリアル](record-stream-inference-data-with-video.md)で示すように、推論メタデータをビデオと共に再生できます。


## <a name="routing-events"></a>ルーティング イベント

次のモジュール ツイン プロパティを使用して、Video Analyzer モジュールによって発行された操作および診断のイベントをルーティングできます。 これらのプロパティについては、[モジュール ツインの JSON スキーマ](module-twin-configuration-schema.md)に記載されています。

- `diagnosticsEventsOutputName`:モジュールから診断イベントを取得するには、このプロパティを含めて、それに何らかの値を指定します。 モジュールによる診断イベントの発行を停止するには、これを省略するか、空のままにします。
  
- `operationalEventsOutputName`:モジュールから操作イベントを取得するには、このプロパティを含めて、それに何らかの値を指定します。 モジュールによる操作イベントの発行を止めるには、これを省略するか、空のままにします。

診断イベントと操作イベントは、モーション検出プロセッサや拡張プロセッサなどのノードによって自動的に生成されます。 IoT Edge ハブに送られるように、分析イベントをパイプライン内で IoT Hub メッセージ シンクにルーティングします。 

[前記のすべてのイベントのルーティング](../../../iot-edge/module-composition.md#declare-routes)は、配置マニフェストの `$edgeHub` モジュール ツインの `desired` プロパティを使用して制御できます。

```json
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.1",
     "routes": {
       "moduleToHub": "FROM /messages/modules/avaedge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

前の JSON で、`avaedge` は Video Analyzer モジュールの名前です。 ルーティング規則は、「[ルートの宣言](../../../iot-edge/module-composition.md#declare-routes)」で定義されているスキーマに従います。

> [!NOTE]
> 分析イベントが IoT Edge ハブに確実に渡るようにするには、モーション検出プロセッサ ノードや拡張機能プロセッサ ノードの下流に IoT Hub シンク ノードを配置する必要があります。

## <a name="event-schema"></a>イベント スキーマ

エッジ デバイス上で生成されたイベントは、エッジまたはクラウドで使用できます。 Video Analyzer によって生成されるイベントは、Azure IoT Hub によって確立された[ストリーミング メッセージング パターン](../../../iot-hub/iot-hub-devguide-messages-construct.md)に準拠しています。 パターンは、システム プロパティ、アプリケーション プロパティ、本文で構成されます。

### <a name="summary"></a>まとめ

IoT Hub によって観測されるすべてのイベントには、一連の共通プロパティがあります。

| プロパティ      | プロパティの種類       | データ型 | 説明                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `message-id`  | システム              | guid      | 一意のイベント ID。                                             |
| `topic`       | applicationProperty | string    | Azure Video Analyzer アカウントの Azure Resource Manager パス。 |
| `subject`     | applicationProperty | string    | イベントを出力するエンティティのサブパス。                    |
| `eventTime`   | applicationProperty | string    | イベントが生成された時刻。                                |
| `eventType`   | applicationProperty | string    | イベントの種類の識別子。 (次のセクションを参照。)          |
| `body`        | body                | object    | 特定のイベント データ。                                       |
| `dataVersion` | applicationProperty | string    | {Major}.{Minor}                                              |

### <a name="properties"></a>Properties

#### <a name="message-id"></a>message-id

イベントのグローバル一意識別子 (GUID)。

#### <a name="topic"></a>topic

イベントを生成した Video Analyzer エッジ モジュールを表します。 例:

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/videoAnalyzers/{accountName}`

#### <a name="subject"></a>subject

イベントを出力しているエンティティ。 例 :

`/edgeModules/avaedge/livePipelines/{livePipelineName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sources/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/processors/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sinks/{nodeName}`

`subject` プロパティを使用すると、イベントを生成したライブ パイプライン内のノードに汎用イベントをマップできます。 たとえば、RTSP のユーザー名またはパスワードが無効な場合、生成されるイベントは `/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource` ノードからの `Microsoft.VideoAnalyzer.Diagnostics.ProtocolError` になります。

#### <a name="eventtype"></a>eventType

イベントの種類は、このスキーマに従って名前空間に割り当てられます。

`Microsoft.VideoAnalyzer.{EventClass}.{EventType}`

イベントのクラスは、次のいずれかの種類になります。

| クラス名  | 説明                                                  |
| ----------- | ------------------------------------------------------------ |
| Analytics   | コンテンツ分析の一環として生成されるイベント。                |
| 診断 | 問題とパフォーマンスの診断に役立つイベント。 |
| 運用時 | リソース操作の一環として生成されるイベント。              |

例 :

* `Microsoft.VideoAnalyzer.Analytics.Inference`
* `Microsoft.VideoAnalyzer.Diagnostics.AuthorizationError`
* `Microsoft.VideoAnalyzer.Operational.RecordingStarted`

#### <a name="eventtime"></a>eventTime

イベント時間は ISO 8601 文字列で書式設定されます。 イベントが発生した日時を表します。

例:

`2021-04-26T18:15:13.298Z`

## <a name="metrics"></a>メトリック

これらのメトリックは、モジュールのポート 9600 で実行されている [Prometheus エンドポイント](https://prometheus.io/docs/instrumenting/exposition_formats/#text-based-format) を介して、Video Analyzer モジュールから報告されます。

| メトリックの名前                           | Type    | ラベル                                                                              | Description                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| va_active_live_pipelines              | ゲージ   | iothub, edge_device, module_name, pipeline_topology                                 | トポロジあたりのアクティブなライブ パイプラインの合計数。          |
| va_cumulative_latency_seconds_average | ゲージ   | iothub、edge_device、module_name、 pipeline_topology、 live_pipeline、ノード            | ビデオフレームがソースに受信されてから、最後の 1 分間にノードを離れるまでの平均待機時間。 フレームがない場合、これは報告されません。 |
| va_cumulative_latency_seconds_max     | ゲージ   | iothub、edge_device、module_name、 pipeline_topology、 live_pipeline、ノード            | ソースによりビデオフレームが受信されてから最後の 1 分間にノードを離れるまでの最大待機時間。 フレームがない場合、これは報告されません。 |
| va_data_dropped_total                 | カウンタ | iothub、edge_device、module_name、pipeline_topology、live_pipeline、node、 data_kind | ドロップされたデータ (イベント、メディアなど) のカウンター。      |
| va_received_bytes_total               | カウンタ | iothub、edge_device、module_name、 pipeline_topology、 live_pipeline、ノード            | ノードによって受信された合計バイト数。 RTSP ソースについてのみサポートされます。 |


> [!NOTE]
> Video Analyzer モジュール `avaedge` に名前を付けた場合は、GET 要求を `http://avaedge:9600/metrics` に送信することでメトリックにアクセスできます。 デプロイによっては、このポートが到達可能になるために、デバイスにマップする必要がある場合があります。

### <a name="azure-monitor-collection-via-telegraf"></a>Telegraf を使用した Azure Monitor コレクション 

Video Analyzer モジュールからメトリックを収集できるようにするには、次の手順に従います。 メトリックは、[Telegraf](https://github.com/influxdata/telegraf) によって収集され、[カスタムメトリック](../../../azure-monitor/essentials/metrics-custom-overview.md)として[Azure Monitor](../../../azure-monitor/overview.md)にアップロードされます。 Telegraf では、ログは [Prometheus](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/prometheus) プラグインによってスクラップされ、[azure_monitor](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/azure_monitor) プラグインによってアップロードされます。

エージェントは、Prometheus エンドポイントを公開する任意のモジュールからメトリックを収集するために使用できます。

1. 開発用コンピューターにフォルダーを作成し、そのフォルダーに移動します。
1. そのフォルダーに、次の構成を含む `telegraf.toml` ファイルを作成します。
    ```toml
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{AVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "{AVA_EDGE_MODULE_NAME}"
      region = "westus2"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > 必ず、.toml ファイル内の変数を置き換えてください。 変数は、中かっこ (`{}`) で示されます。 さらに、`region` の値を更新します。
1. 同じフォルダーに、次のコマンドを含む Dockerfile を作成します。
    ```docker
    FROM telegraf:1.15.3-alpine
    COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```
1. Docker CLI のコマンドを使用して Docker ファイルをビルドし、イメージを Azure コンテナー レジストリに発行します。
   
   Docker CLI を使用してコンテナー レジストリにプッシュする方法の詳細については、[Docker イメージのプッシュとプル](../../../container-registry/container-registry-get-started-docker-cli.md)に関する記事を参照してください。 Azure Container Registry の情報については、[こちらのドキュメント](../../../container-registry/index.yml)を参照してください。

   ```bash
   # Insert your own container image URL
   docker build . -t myregistry.azurecr.io/telegraf_agent
   docker push myregistry.azurecr.io/telegraf_agent
   ```

1. Azure Container Registry へのプッシュが完了したら、次のノードを配置マニフェスト ファイルに追加します。
    ```json
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_TELEGRAF_IMAGE}"
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
     >サービス プリンシパルには、**監視メトリック パブリッシャー** ロールを指定できます。 「 **[サービス プリンシパルを作成する](../../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** 」の手順に従って、サービス プリンシパルを作成し、ロールを割り当てます。
1. モジュールがデプロイされると、Azure Monitor で 1 つの名前空間の下にメトリックが表示されます。 メトリック名は、Prometheus によって出力されたものと一致します。 

   この場合は、Azure portal で IoT ハブに移動し、左側のペインで **[メトリック]** を選択します。 ここでメトリックが表示されます。 

### <a name="log-analytics-metrics-collection"></a>Log Analytics メトリックの収集

[Prometheus エンドポイント](https://prometheus.io/docs/practices/naming/)を [Log Analytics](../../../azure-monitor/logs/log-analytics-tutorial.md) と一緒に使用すると、CPUPercent、MemoryUsedPercent などのメトリックを生成して[それらを監視](../../../azure-monitor/essentials/metrics-supported.md)できます。   

> [!NOTE]
> 以下の構成で収集されるのは **メトリックのみ** です。ログは収集されません。 コレクター モジュールを拡張すれば、ログの収集とアップロードも行うことができます。

[![Log Analytics を使用したメトリックの収集を示す図。](./media/telemetry-schema/log-analytics.svg)](./media/telemetry-schema/log-analytics.svg#lightbox)

1. [メトリックの収集](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)方法を確認します。
1. Docker CLI のコマンドを使用して [Docker ファイル](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux)をビルドし、イメージを Azure コンテナー レジストリに発行します。
   
   Docker CLI を使用してコンテナー レジストリにプッシュする方法の詳細については、[Docker イメージのプッシュとプル](../../../container-registry/container-registry-get-started-docker-cli.md)に関する記事を参照してください。 Azure Container Registry のその他の情報については、[こちらのドキュメント](../../../container-registry/index.yml)を参照してください。
1. Azure Container Registry へのプッシュが完了すると、配置マニフェストに次の内容が挿入されます。
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://avaedge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > `edgeHub`、`edgeAgent`、`avaedge` の各モジュールは、配置マニフェスト ファイルに定義されているモジュールの名前です。 モジュールの名前が一致していることを確認してください。   

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
    | where module_name in ("avaedge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Kusto クエリを使用したメトリックを示す図。](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>ログ記録

他の IoT Edge モジュールと同様に、エッジ デバイスで[コンテナーのログを調べる](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues)こともできます。 ログに書き込まれる情報は、[次のモジュール ツイン](module-twin-configuration-schema.md) プロパティを使用して構成できます。

* `logLevel`

   * 指定できる値は、`Verbose`、`Information`、`Warning`、`Error`、および `None` です。
   * 既定値は `Information` です。 ログには、エラー、警告、情報のメッセージが含まれます。
   * 値を `Warning` に設定すると、ログにはエラーと警告のメッセージが含まれます。
   * 値を `Error` に設定すると、ログにはエラー メッセージのみが含まれます。
   * 値を `None` に設定すると、ログは生成されません。 これは推奨されません。
   * `Verbose` は、問題を診断するために Azure サポートとログを共有する必要がある場合にのみ使用してください。

* `logCategories`

   * `Application`、`Events`、`MediaPipeline` の 1 つ以上の値のコンマ区切りのリスト。
   * 既定値は `Application, Events` です。
   * `Application`:モジュールのスタートアップ メッセージ、環境エラー、ダイレクト メソッド呼び出しなど、モジュールからの概要情報です。
   * `Events`:この記事で既に説明したすべてのイベントです。
   * `MediaPipeline`:RTSP 対応カメラとの接続を確立するのが難しいなどの問題をトラブルシューティングするときに、分析情報が提供される可能性がある詳細なログです。
   
### <a name="generating-debug-logs"></a>デバッグ ログの生成
場合によっては、Azure サポートによる問題の解決を支援するため、上で説明したものより詳細なログを生成することが必要になる場合があります。 これらのログを生成するには:  

1. [Azure portal](https://portal.azure.com) にサインインし、IoT ハブに移動します。
1. 左側のペインで、 **[IoT Edge]** を選択します。
1. デバイスの一覧で、ターゲット デバイスの ID を選択します。
1. ペインの上部にある **[モジュールの設定]** を選択します。

   ![Azure portal の [モジュールの設定] ボタンのスクリーンショット。](media/troubleshoot/set-modules.png)

1. **[IoT Edge モジュール]** セクションで、 **[avaedge]** を探して選択します。
1. **[モジュール ID ツイン]** を選択します。 編集可能なペインが開きます。
1. **[desired key]\(必要なキー\)** の下に、次のキーと値のペアを追加します。

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > このコマンドにより、エッジ デバイスとコンテナーの間でログ フォルダーがバインドされます。 デバイス上の別の場所にあるログを収集するには、次のようにします。
   >
   > 1. **[バインド]** セクションでデバッグ ログの場所のバインドを作成し、 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** と **$DEBUG _LOG_LOCATION** を目的の場所 `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION` に置き換えます。
   > 2. 次のコマンドを使用します。 **$DEBUG _LOG_LOCATION** は、前の手順で使用した場所に置き換えます: `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. **[保存]** を選びます。

これにより、モジュールからデバイス ストレージ パス `/var/local/videoAnalyzer/debuglogs/` に、バイナリ形式のデバッグ ログが書き込まれます。 これらのログを Azure サポートと共有できます。  

**[モジュール ID ツイン]** の値を _null_ に設定すると、ログ収集を停止できます。 **[モジュール ID ツイン]** ページに戻り、次の内容をパラメーターとして更新します。

   `"DebugLogsDirectory": ""`

## <a name="faq"></a>よく寄せられる質問

質問がある場合は、[監視とメトリックに関する FAQ](faq.yml#monitoring-and-metrics) を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Video Analyzer のトラブルシューティング](troubleshoot.md)
