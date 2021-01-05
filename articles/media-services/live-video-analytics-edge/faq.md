---
title: Live Video Analytics on IoT Edge に関する FAQ - Azure
description: このトピックでは、Live Video Analytics on IoT Edge の FAQ に対する回答を示します。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401578"
---
# <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

このトピックでは、Live Video Analytics on IoT Edge の FAQ に対する回答を示します。

## <a name="general"></a>全般

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>グラフ トポロジ定義で使用できるシステム変数は何ですか。

|変数   |説明|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|UTC 時間でインスタンスを表します。通常は、日付時刻として表現されます (基本表現 yyyyMMddTHHmmssZ)。|
|System.PreciseDateTime|ミリ秒付きのISO8601 ファイルに準拠した形式の UTC 日付時刻インスタンスを表します (基本表現 yyyyMMddTHHmmss.fffZ)。|
|System.GraphTopologyName   |メディア グラフ トポロジを表します。グラフのブループリントを保持します。|
|System.GraphInstanceName|  メディア グラフ インスタンスを表します。パラメーター値を保持し、トポロジを参照します。|

## <a name="configuration-and-deployment"></a>構成とデプロイ

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>メディア Edge モジュールを Windows 10 デバイスに展開することはできますか。

はい。 [Windows 10 での Linux コンテナー](/virtualization/windowscontainers/deploy-containers/linux-containers)に関する記事を参照してください。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP カメラと RTSP 設定からのキャプチャ

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>ビデオ ストリームで送信するには、デバイスで特別な SDK を使用する必要がありますか。

いいえ。 Live Video Analytics on IoT Edge では、RTSP ビデオ ストリーミング プロトコル (ほとんどの IP カメラでサポートされています) を使用したメディアのキャプチャがサポートされています。

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>RTMP または Smooth (Media Services ライブ イベントなど) を使用して Live Video Analytics on IoT Edge にメディアをプッシュできますか。

* いいえ。 Live Video Analytics では、IP カメラからのビデオのキャプチャにのみ RTSP がサポートされます。
* TCP/HTTP 経由の RTSP ストリーミングをサポートするすべてのカメラが動作します。 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>グラフ インスタンスで RTSP ソース URL をリセットまたは更新できますか。

はい (グラフ インスタンスが非アクティブな状態の場合)。  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>テストおよび開発中に使用できる RTSP シミュレーターはありますか。

はい。 学習プロセスをサポートするためのクイック スタートとチュートリアルで使用できる [RTSP シミュレーター](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge モジュールがあります。 このモジュールは、ベストエフォートとして提供されており、常に使用できるとは限りません。 これは、数時間以上使用しないことを強くお勧めします。 運用環境のデプロイの計画を作成する前に、実際の RTSP ソースを使用したテストに投資する必要があります。

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Edge での IP カメラの ONVIF 検出をサポートしていますか。

いいえ。Edge 上のデバイスの ONVIF 検出はサポートされていません。

## <a name="streaming-and-playback"></a>ストリーミングと再生

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Edge から AMS に記録されたアセットは、HLS や DASH などの Media Services ストリーミング テクノロジを使用して再生できますか。

はい。 記録されたアセットは、Azure Media Services の他のアセットと同様にストリーミングできます。 コンテンツをストリーミングするには、ストリーミング エンドポイントを作成し、実行状態にする必要があります。 標準ストリーミング ロケーターの作成プロセスを使用すると、任意の機能を持つプレーヤー フレームワークにストリーミングするための HLS または DASH のマニフェストにアクセスできます。 発行 HLS または DASH のマニフェストの作成の詳細については、「[ダイナミック パッケージ](../latest/dynamic-packaging-overview.md)」を参照してください。

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>アーカイブされたアセットで Media Services の標準的なコンテンツ保護と DRM 機能を使用できますか。

はい。 標準の動的暗号化コンテンツ保護と DRM 機能はすべて、メディア グラフから記録されたアセットで使用できます。

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>記録されたアセットのコンテンツを表示するには、どのプレーヤーを使用できますか。

準拠している Apple HTTP ライブ ストリーミング (HLS) バージョン 3 またはバージョン 4 をサポートするすべての標準プレーヤーがサポートされています。 また、準拠している MPEG-DASH の再生が可能なプレーヤーもサポートされています。

テストに推奨されるプレーヤーは次のとおりです。

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple ネイティブ HTTP ライブ ストリーミング](https://developer.apple.com/streaming/)
* Edge、Chrome、または Safari に組み込まれている HTML5 ビデオ プレーヤー
* HLS または DASH の再生をサポートする商用プレーヤー

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>メディア グラフ アセットのストリーミングにはどのような制限がありますか。

Media Services は、メディアと娯楽、OTT、およびブロードキャストの各顧客に対してオンデマンドおよびライブ ストリーミングをサポートします。メディア グラフからライブまたは記録されたアセットをストリーミングするには、同じ大規模なインフラストラクチャとストリーミング エンドポイントを使用します。 つまり、Azure CDN、Verizon、または Akamai をすばやく簡単に有効にして、シナリオに応じて数人の視聴者から最大で数百万の視聴者にコンテンツを配信することができます。

コンテンツは、Apple HTTP ライブ ストリーミング (HLS) と MPEG-DASH の両方を使用して配信できます。

## <a name="design-your-ai-model"></a>AI モデルを設計する 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Docker コンテナーに複数の AI モデルがラップされています。 Live Video Analytics ではどのように使用すればよいですか。 

ソリューションは、Live Video Analytics との通信に推論サーバーで使用される通信プロトコルに応じて異なります。 これを行うには次のような方法があります。

#### <a name="http-protocol"></a>HTTP プロトコル:

* 1 つのコンテナー (単一の lvaExtension):  

   推論サーバーでは、1 つのポートを使用できますが、さまざまな AI モデルに対して異なるエンドポイントを使用できます。 たとえば、Python サンプルの場合、モデルごとに異なる `route` を次のように使用できます。 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   次に、Live Video Analytics のデプロイで、グラフをインスタンス化するときに、各インスタンスの推論サーバー URL を次のように設定します。 

   1 番目のインスタンス: 推論サーバー URL = `http://lvaExtension:44000/score/face_detection`<br/>
   2 番目のインスタンス: 推論サーバー URL = `http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > または、さまざまなポートで AI モデルを公開し、グラフをインスタンス化するときにそれらを呼び出すこともできます。  

* 複数のコンテナー: 

   各コンテナーは、異なる名前でデプロイされます。 現時点では、Live Video Analytics のドキュメント セットで、**lvaExtension** の名前の拡張機能をデプロイする方法を示しました。 2 つの異なるコンテナーを作成できるようになりました。 各コンテナーには、同じ HTTP インターフェイス (つまり同じ `/score` エンドポイント) があります。 これらの 2 つのコンテナーを別々の名前でデプロイし、両方が **異なるポート** でリッスンしていることを確認します。 

   たとえば、`lvaExtension1` という名前の 1 つのコンテナーでポート `44000` をリッスンし、`lvaExtension2` という名前のコンテナーでポート `44001` をリッスンしているとします。 

   Live Video Analytics トポロジでは、次のような異なる推論 URL を持つ 2 つのグラフをインスタンス化します。 

   1 番目のインスタンス: 推論サーバー URL = `http://lvaExtension1:44001/score`    
   2 番目のインスタンス: 推論サーバー URL = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>gRPC プロトコル: 

gRPC プロトコルの使用時に、gRPC サーバーで異なるポートを介して異なる AI モデルが公開されている場合、Live Video Analytics モジュール 1.0 が唯一の方法となります。 [この例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)では、すべての yolo モデルを公開している単一のポート 44000 があるとします。 理論的には、yolo gRPC サーバーは、一部のモデルを 44000 で、他のモデルを 45000 で公開するように書き換えることができます。 

Live Video Analytics モジュール 2.0 では、新しいプロパティが gRPC 拡張ノードに追加されます。 このプロパティは、**extensionConfiguration** と呼ばれます。これは、gRPC コントラクトの一部として使用できる省略可能な文字列です。 1 つの推論サーバーに複数の AI モデルがパッケージ化されている場合、AI モデルごとにノードを公開する必要はありません。 代わりに、グラフ インスタンスでは、拡張プロバイダー (あなた) は **extensionConfiguration** プロパティを使用して異なる AI モデルを選択する方法を定義でき、実行中には、Live Video Analytics によって推論サーバーにこの文字列が渡され、これを使用して目的の AI モデルが呼び出されます。 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>AI モデルに関連する gRPC サーバーを構築しており、複数のカメラ/グラフ インスタンスでの使用をサポートできるようにしたいと思います。 サーバーをどのように構築すればよいですか。 

 まず、サーバーで一度に複数の要求を処理できることを確認します。 または、サーバーが並列スレッドで動作することを確認します。 

たとえば、[Live Video Analytics gRPC サンプル](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)の 1 つには、既定の数の並列チャネルが設定されています。 参照: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

上記の gRPC サーバーのインスタンス化では、サーバーで、一度に 1 つのカメラあたり (つまり、グラフ トポロジ インスタンスあたり) に 3 つのチャネルのみを開くことができます。 4 つ以上のインスタンスをサーバーに接続しないようにしてください。 4 つ以上のチャネルを開こうとすると、既存のチャネルがドロップされるまで、要求は保留されます。  

上記の gRPC サーバー実装は、Python サンプルで使用されています。 開発者は、独自のサーバーを実装することも、上記の既定の実装では、ビデオ フィードの取得元として使用されるカメラの数に設定されたワーカー数を増加させることもできます。 

複数のカメラを設定して使用するために、開発者は、各インスタンスが同じまたは異なる推論サーバー (上の段落に記載されているサーバーなど) を指している複数のグラフ トポロジ インスタンスをインスタンス化できます。 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>推論意思決定を行う前に、上流から複数のフレームを受信できるようにしたいと考えています。 これを有効にするにはどうすればよいですか。 

現在の[既定のサンプル](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)は "ステートレス" モードで動作します。 これらのサンプルでは、前の呼び出しの状態および呼び出したユーザーも保持していません (つまり、複数のトポロジ インスタンスで同じ推論サーバーが呼び出される可能性があり、サーバーでは呼び出しているユーザーと呼び出し元ごとの状態を区別できません) 

#### <a name="http-protocol"></a>HTTP プロトコル

HTTP プロトコルを使用した場合: 

状態を保持するために、各呼び出し元 (グラフ トポロジ インスタンス) では、呼び出し元に固有の HTTP Query パラメーターを使用して推論サーバーを呼び出します。 たとえば、推論サーバーの URL アドレスは次のようになります  

1 番目のトポロジインスタンス = `http://lvaExtension:44000/score?id=1`<br/>
2 番目のトポロジインスタンス = `http://lvaExtension:44000/score?id=2`

… 

サーバー側のスコア ルートでは、呼び出しているユーザーが認識されます。 ID=1 の場合、その呼び出し元 (グラフ トポロジ インスタンス) の状態を個別に保持できます。 この場合、受信したビデオ フレームをバッファー (たとえば、配列、または DateTime キーを含むディクショナリ。値はフレームです) で保持でき、x フレームを受信した後に処理 (推論) するようにサーバーを定義することができます。 

#### <a name="grpc-protocol"></a>gRPC プロトコル 

gRPC プロトコルを使用する場合: 

gRPC 拡張機能では、各セッションは 1 つのカメラ フィード用であるため、ID を指定する必要はありません。 このため、extensionConfiguration プロパティを使用して、ビデオ フレームをバッファーに格納でき、x フレームを受信した後に処理 (推論) するようにサーバーを定義できるようになりました。 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>特定のコンテナーのすべての ProcessMediaStreams では、同じ AI モデルが実行されますか。 

いいえ。  

グラフ インスタンスでのエンド ユーザーからの開始/停止呼び出しによって、セッションが構成されるか、カメラの切断/再接続が行われる可能性があります。 カメラでビデオがストリーミングされている場合は、1 つのセッションを保持することを目標としています。 

* 2 つのカメラで処理用にビデオを送信すると、2 つのセッションが作成されます。 
* 2 つの gRPCExtension ノードを持つグラフに 1 つのカメラを移動させると、2 つのセッションが作成されます。 

各セッションは、Live Video Analytics と gRPC サーバー間の全二重接続であり、各セッションは異なるモデル/パイプラインを持つことができます。 

> [!NOTE]
> カメラが切断または再接続された場合、カメラが許容範囲の上限を超えて一定期間オフラインになると、Live Video Analytics は gRPC サーバーとの新しいセッションを開きます。 サーバーでこれらのセッション間の状態を追跡する必要はありません。 

Live Video Analytics では、グラフ インスタンス内の単一のカメラに対して複数の gRPC 拡張機能のサポートも追加されました。 これらの gRPC 拡張機能を使用して、AI 処理を順次または並列で実行したり、両方の組み合わせで実行したりすることができます。 

> [!NOTE]
> 複数の拡張機能を並行して実行すると、ハードウェア リソースに影響し、コンピューティングのニーズに合うハードウェアを選択する際には、この点に注意する必要があります。 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>同時 ProcessMediaStreams の最大数はいくつですか。 

Live Video Analytics で適用される上限はありません。  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>推論サーバーが CPU または GPU あるいはその他のハードウェア アクセラレータを使用する必要があるかどうかを判断するにはどうすればよいですか。 

これは、AI モデルがどの程度複雑に開発されているか、および開発者が CPU とハードウェア アクセラレータをどのように使用したいと考えているかに完全に依存しています。 AI モデルの開発時に、開発者は、モデルでどのアクションを実行するためにどのリソースを使用する必要があるかを指定できます。 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>処理後に境界ボックスを使用してイメージを格納するにはどのようにすればよいですか。 

現在、境界ボックスの座標は、推論メッセージとしてのみ提供しています。 開発者は、これらのメッセージを使用して、境界ボックスをビデオ フレームに重ね合わせることができるカスタム MJPEG ストリーマーを構築できます。  

## <a name="grpc-compatibility"></a>gRPC の互換性 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>メディア ストリーム記述子の必須フィールドを確認するにはどうすればよいですか。 

指定されていないフィールド値には、[gRPC によって指定された](https://developers.google.com/protocol-buffers/docs/proto3#default)既定値が与えられます。  

Live Video Analytics では、**proto3** バージョンのプロトコル バッファー言語を使用します。 Live Video Analytics コントラクトによって使用されるすべてのプロトコル バッファー データは、[ここで定義されている](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)プロトコル バッファー ファイル で使用できます。 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>最新のプロトコル バッファー ファイルを使用するにはどうすればよいですか。 

最新のプロトコル バッファー ファイルは、[ここで取得する](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)ことができます。 コントラクト ファイルは更新されるたびに、この場所に表示されます。 プロトコル ファイルを更新する直近の予定がないときにバージョンを知るには、ファイルの上部でパッケージ名を確認してください。 これを次の手順に置き換えます。 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

これらのファイルを更新すると、名前の末尾にある "v-value" が増加します。 

> [!NOTE]
> Live Video Analytics では proto3 バージョンの言語が使用されているため、フィールドは省略可能であり、下位および上位互換性があります。 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Live Video Analytics で使用できる gRPC の機能について教えてください。 どの機能が必須で、どの機能が省略可能ですか。 

protobuf コントラクトが満たされた場合は、サーバー側の gRPC 機能を使用できます。 

## <a name="monitoring-and-metrics"></a>監視とメトリック

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Event Grid を使用して、Edge のメディア グラフを監視できますか。

はい。 Prometheus メトリックを使用して、それらをイベント グリッドに公開できます。 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Azure Monitor を使用して、クラウドまたは Edge のメディア グラフの正常性、メトリック、およびパフォーマンスを表示できますか。

はい。 これはサポートされています。 [Azure Monitor メトリックの使用方法](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)に関するページをご覧ください。

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Media Services IoT Edge モジュールを簡単に監視できるツールはありますか。

Visual Studio Code では、LVAEdge モジュール エンドポイントを簡単に監視できる "Azure IoT Tools" 拡張機能がサポートされています。 このツールを使用すると、"イベント" の IoT Hub 組み込みエンドポイントの監視をすばやく開始し、Edge デバイスからクラウドにルーティングされる推論メッセージを確認できます。 

また、この拡張機能を使用して、LVAEdge モジュールのモジュール ツインを編集し、メディア グラフの設定を変更することもできます。

詳細については、[監視とログ記録](monitoring-logging.md)に関する記事をご覧ください。

## <a name="billing-and-availability"></a>課金と可用性

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Live Video Analytics on IoT Edge はどのように課金されますか。

詳細については、[価格](https://azure.microsoft.com/pricing/details/media-services/)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

[クイック スタート: はじめに - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
