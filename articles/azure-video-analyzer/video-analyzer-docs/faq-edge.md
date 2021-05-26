---
title: Azure Video Analyzer に関する FAQ - Azure
description: この記事では、Azure Video Analyzer についてよく寄せられる質問に回答します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 94f85cecdb8ee3d18ad7521d8157c01f1410c23c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387287"
---
# <a name="faq-about-azure-video-analyzer"></a>Azure Video Analyzer に関する FAQ

この記事では、Azure Video Analyzer についてよく寄せられる質問に回答します。

## <a name="general"></a>全般

**パイプライン トポロジ定義で使用できるシステム変数は何ですか。**

| 変数   |  説明  | 
| --- | --- | 
| System.Runtime.DateTime | UTC 時間でインスタンスを表します。通常は、次の形式の日付と時刻として表現されます:<br>*yyyyMMddTHHmmssZ* | 
| System.Runtime.PreciseDateTime | ミリ秒を含む ISO8601 ファイル準拠の形式で協定世界時 (UTC) の日時インスタンスを表します。形式は次のとおりです:<br>*yyyyMMddTHHmmss.fffZ* | 
| System.TopologyName    | パイプライン トポロジの名前を表します。 | 
| System.PipelineName | ライブ パイプラインの名前を表します。 | 

> [!Note] 
> System.Runtime.DateTime と System.Runtime.PreciseDateTime は、ビデオ シンク ノードで、Azure Video Analyzer ビデオ リソースの名前の一部として使用できません。 これらの変数は、ファイルに名前を付けるために FileSink ノードで使用できます。

**Video Analyzer のプライバシー ポリシーとは何ですか。**

Video Analyzer には、[Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)が適用されます。 プライバシーに関する声明では、Microsoft が処理する個人データ、Microsoft がそれをどのように処理し、何の目的でそれを処理するかについて説明しています。 プライバシーについて詳しくは、[Microsoft Trust Center](https://www.microsoft.com/trustcenter) を参照してください。

## <a name="configuration-and-deployment"></a>構成とデプロイ

**Edge モジュールを Windows 10 デバイスにデプロイできますか。**

はい。 詳細については、「[Windows 10 上の Linux コンテナー](/virtualization/windowscontainers/deploy-containers/linux-containers)」を参照してください。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP カメラと RTSP 設定からのキャプチャ

**ビデオ ストリームで送信するには、デバイスで特別な SDK を使用する必要がありますか。**

いいえ。Video Analyzer では、ビデオ ストリーミング用の RTSP (リアルタイム ストリーミング プロトコル) を使用したメディアのキャプチャがサポートされています。これは、ほとんどの IP カメラでサポートされています。

**RTSP 以外のプロトコルを使用して、Video Analyzer にメディアをプッシュできますか。**

いいえ。Video Analyzer では、IP カメラからビデオをキャプチャするために RTSP のみがサポートされています。 TCP/HTTP 経由の RTSP ストリーミングをサポートするすべてのカメラが動作します。 

**ライブ パイプラインで RTSP ソース URL をリセットまたは更新できますか。**

はい (ライブ パイプラインが "*非アクティブ*" 状態の場合)。  

**テストおよび開発中に使用できる RTSP シミュレーターはありますか。**

はい。[RTSP シミュレーター]()<!--add-valid-link.md)--><!-- https://github.com/Azure/video-analyzer/tree/main/utilities/rtspsim-live555 --> Edge モジュールを、学習プロセスをサポートするためのクイックスタートとチュートリアルで使用できます。 このモジュールは、ベストエフォートとして提供されており、常に使用できるとは限りません。 シミュレーターを数時間以上使用 "*しない*" ことを強くお勧めします。 運用環境のデプロイを計画する前に、実際の RTSP ソースを使用したテストに投資する必要があります。

## <a name="design-your-ai-model"></a>AI モデルを設計する 

**Docker コンテナーに複数の AI モデルがラップされています。Azure Video Analyzer でそれらをどのように使用すればよいですか。** 

解決方法は、推論サーバーで Azure Video Analyzer との通信に使用される通信プロトコルによって異なります。 以下のセクションで各プロトコルのしくみについて説明します。

*HTTP プロトコルを使用する*:

* 単一コンテナー (*avaextension* という名前のモジュール):  

   推論サーバーでは、1 つのポートを使用できますが、さまざまな AI モデルに対して異なるエンドポイントを使用できます。 たとえば、Python サンプルの場合、次のようにモデルごとに異なる `routes` を使用できます。 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   次に、Video Analyzer のデプロイで、ライブ パイプラインをアクティブにするときに、次のように各ライブ パイプラインの推論サーバー URL を設定します。 

   1 番目のライブ パイプライン: 推論サーバー URL = `http://avaextension:44000/score/face_detection`<br/>
   2 番目のライブ パイプライン: 推論サーバー URL = `http://avaextension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > また、さまざまなポートで AI モデルを公開し、ライブ パイプラインをアクティブにするときにそれらを呼び出すことができます。  

* 複数のコンテナー: 

   各コンテナーは、異なる名前でデプロイされます。 クイックスタートとチュートリアルでは、*avaextension* という名前の拡張機能をデプロイする方法について説明しました。 現在は、同じ HTTP インターフェイス (つまり、同じ `/score` エンドポイント) を持つ 2 つの異なるコンテナーを開発できるようになりました。 これらの 2 つのコンテナーを別々の名前でデプロイし、確実に両方が "*異なるポート*" でリッスンするようにします。 

   たとえば、`avaextension1` という名前の 1 つのコンテナーでポート `44000` をリッスンし、`avaextension2` という名前の 2 つ目のコンテナーでポート `44001` をリッスンしているとします。 

   Video Analyzer トポロジで、次のような異なる推論 URL を持つ 2 つのライブ パイプラインをインスタンス化します。 

   1 番目のライブ パイプライン: 推論サーバー URL = `http://avaextension1:44001/score`    
   2 番目のライブ パイプライン: 推論サーバー URL = `http://avaextension2:44001/score`
   
*gRPC プロトコルを使用する*: 

* gRPC 拡張ノードには、プロパティ `extensionConfiguration` があります。これは、gRPC コントラクトの一部として使用できる省略可能な文字列です。 1 つの推論サーバーに複数の AI モデルがパッケージ化されている場合、AI モデルごとにノードを公開する必要はありません。 代わりに、ライブ パイプラインでは、拡張機能プロバイダーとして、`extensionConfiguration` プロパティを使用して、さまざまな AI モデルを選択する方法を定義できます。 実行中、この文字列が Video Analyzer から推論サーバーに渡されます。これにより、その文字列が使用され、目的の AI モデルを呼び出すことができます。 

**AI モデルに関連する gRPC サーバーを構築しています。複数のカメラまたはライブ パイプラインでの使用をサポートできるようにしたいと考えています。このサーバーはどのように構築すればよいですか。** 

 まず、サーバーが一度に複数の要求を処理できるように、または並列スレッドで動作できるようにします。 

たとえば、次の [Azure Video Analyzer gRPC のサンプル]()では、既定の数の並列チャネルが設定されています。<!--add-valid-link.md)--><!-- https://github.com/Azure/video-analyzer/tree/main/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/avaextension/server/server.py -->: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

上記の gRPC サーバーのインスタンス化では、カメラあたり (つまり、ライブ パイプラインあたり) 3 つのチャネルのみをサーバーで一度に開くことができます。 4 つ以上のインスタンスをサーバーに接続しないようにしてください。 4 つ以上のチャネルを開こうとすると、既存のチャネルがドロップされるまで、要求は保留されます。  

上記の gRPC サーバー実装は、Python サンプルで使用されています。 開発者は、独自のサーバーを実装することも、前の既定の実装を使用してワーカーの数を増やすこともできます。これは、ビデオ フィードに使用するカメラの数に設定します。 

複数のカメラを設定して使用するために、それぞれが同じまたは異なる推論サーバー (前の段落に記載されているサーバーなど) を指している複数のライブ パイプラインをインスタンス化できます。 

**推論意思決定を行う前に、複数のフレームを受信できるようにしたいと考えています。これはどのような方法で実現できますか。** 

現在の[既定のサンプル]()は、<!--add-valid-link.md)--><!--https://github.com/Azure/video-analyzer/tree/main/utilities/video-analysis--> "*ステートレス*" モードで動作します。 前の呼び出しの状態や、呼び出し元の ID は保持されません。 したがって、複数のライブ パイプラインが同じ推論サーバーを呼び出す可能性がある一方で、そのサーバーは、呼び出し元や各呼び出しの状態を識別できません。 

*HTTP プロトコルを使用する*:

状態を保持するために、各呼び出し元 (またはライブ パイプライン) により、呼び出し元に固有の HTTP クエリ パラメーターを使用して推論サーバーが呼び出されます。 たとえば、各ライブ パイプラインの推論サーバー URL アドレスは、次のようになります。  

1 番目のライブ パイプライン: `http://avaextension:44000/score?id=1`<br/>
2 番目のライブ パイプライン: `http://avaextension:44000/score?id=2`

… 

サーバー側では、`id` が呼び出し元を識別するのに役立ちます。 `id`=1 の場合、サーバーはそのライブ パイプラインの状態を個別に保持できます。 そのうえで、受信したビデオ フレームをバッファーで保持できます。 たとえば、配列、または DateTime キーを含むディクショナリを使用します。値はフレームです。 次に、*x* 個のフレームを受信した後に処理 (推論) するようにサーバーを定義できます。 

*gRPC プロトコルを使用する*: 

gRPC 拡張機能の場合、各セッションは単一のカメラ フィード用であるため、ID を指定する必要はありません。 extensionConfiguration プロパティを使用して、ビデオ フレームをバッファーに格納でき、*x* 個のフレームを受信した後に処理 (推論) するようにサーバーを定義できます。 

**特定のコンテナーのすべての ProcessMediaStreams で同じ AI モデルが実行されますか。** 

いいえ。 ライブ パイプラインでのエンド ユーザーからの開始または停止呼び出しによって、セッションが構成されるか、カメラの切断または再接続が行われる可能性があります。 カメラでビデオがストリーミングされている場合は、1 つのセッションを保持することを目標としています。 

* (2 つの異なるライブ パイプラインに) 処理のためにビデオを送信する 2 つのカメラによって、2 つのセッションが作成されます。 
* 2 つの gRPC 拡張ノードがあるライブ パイプラインに 1 つのカメラを移動させると、2 つのセッションが作成されます。 

各セッションは、Video Analyzer と gRPC サーバー間の全二重接続であり、異なるモデルを使用できます。 

> [!NOTE]
> カメラが許容範囲の上限を超えて一定期間オフラインになり、切断または再接続されると、Video Analyzer によって gRPC サーバーとの新しいセッションが開かれます。 サーバーでこれらのセッションにまたがって状態を追跡する必要はありません。 

また、Video Analyzer により、ライブ パイプライン内の単一のカメラに対する複数の gRPC 拡張機能のサポートも追加されます。 これらの gRPC 拡張機能を使用して、AI 処理を順次または並列で実行したり、両方の組み合わせで実行したりすることができます。 

> [!NOTE]
> 複数の拡張機能を並行して実行すると、ハードウェア リソースに影響します。 コンピューティングのニーズに合ったハードウェアを選択する際には、この点に留意してください。 

**同時 ProcessMediaStreams の最大数はいくつですか。** 

Video Analyzer では、この数に制限はありません。  

**推論サーバーで CPU または GPU あるいはその他のハードウェア アクセラレータを使用する必要があるかどうかを判断するには、どうすればよいですか。** 

この決定は、開発した AI モデルの複雑さ、および CPU とハードウェア アクセラレータをどのように使用したいかによって異なります。 AI モデルを開発する際、モデルでどのリソースを使用し、どのようなアクションを実行する必要があるかを指定できます。 

**推論サーバーによって生成された境界ボックスを表示するには、どのようにしますか。** 

推論結果は、ビデオ リソースのメディアに記録できます。 [ウィジェット]()を使用して、<!--add-valid-link.md)--><!-- pointer to widget md --> 推論データのオーバーレイを含むビデオを再生できます。

## <a name="grpc-compatibility"></a>gRPC の互換性 

**メディア ストリーム記述子の必須フィールドを確認するにはどうすればよいですか。** 

値を指定しないフィールドには、[gRPC によって指定された既定値](https://developers.google.com/protocol-buffers/docs/proto3#default)が与えられます。  

Video Analyzer では、プロトコル バッファー言語の *proto3* バージョンが使用されます。 Video Analyzer コントラクトによって使用されるすべてのプロトコル バッファー データは、[プロトコル バッファー ファイル]()で使用できます。<!--add-valid-link.md)--><!--https://github.com/Azure/azree-video-analyzer/tree/master/contracts/grpc-->. 

**最新のプロトコル バッファー ファイルを使用していることを確認するには、どうすればよいですか。** 

最新のプロトコル バッファー ファイルは、[コントラクト ファイルのサイト]()で入手できます。<!--add-valid-link.md)--><!--https://github.com/Azure/azree-video-analyzer/tree/master/contracts/grpc-->. コントラクト ファイルは更新されるたびに、この場所に置かれます。 プロトコル ファイルを更新する直近の予定はないため、バージョンを知るには、ファイル先頭のパッケージ名を確認してください。 これを次の手順に置き換えます。 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1
```

これらのファイルが更新されると、名前の末尾にある "v-value" が増分されます。 

> [!NOTE]
> Video Analyzer では proto3 バージョンの言語が使用されるため、フィールドは省略可能であり、バージョンには下位および上位互換性があります。 

**Video Analyzer では、どの gRPC 機能を使用できますか。また、どの機能が必須で、どの機能が省略可能ですか。** 

プロトコル バッファー (Protobuf) コントラクトが満たされている場合は、任意のサーバー側の gRPC 機能を使用できます。 

## <a name="monitoring-and-metrics"></a>監視とメトリック

**Azure Event Grid を使用して Edge のパイプラインを監視できますか。**

はい。 [Prometheus メトリック](monitor-log-edge.md#azure-monitor-collection-via-telegraf)を使用して、それらをイベント グリッドに公開できます。 

**Azure Monitor を使用して、クラウドまたは Edge のパイプラインの正常性、メトリック、パフォーマンスを表示できますか。**

はい、この方法はサポートされています。 詳細については、「[Azure Monitor メトリックの概要](../../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。

**Azure Video Analyzer IoT Edge モジュールを簡単に監視できるツールはありますか。**

Video Analyzer Edge モジュール エンドポイントを簡単に監視できる Azure IoT Tools 拡張機能が、Visual Studio Code でサポートされています。 このツールを使用すると、"イベント" の IoT Hub 組み込みエンドポイントの監視をすばやく開始し、Edge デバイスからクラウドにルート指定される推論メッセージを確認できます。 

また、この拡張機能を使用して、Video Analyzer Edge モジュールのモジュール ツインを編集し、パイプラインの設定を変更することもできます。

詳細については、[監視とログ記録](monitor-log-edge.md)に関する記事をご覧ください。

## <a name="billing-and-availability"></a>課金と可用性

**Azure Video Analyzer は、どのように課金されますか。**

課金の詳細については、[Video Analyzer の価格]()に関するページを参照してください。<!--add-valid-link.md)--><!--https://azure.microsoft.com/pricing/details/media-services/-->.

## <a name="next-steps"></a>次のステップ

[クイックスタート: Azure Video Analyzer で作業を開始する](get-started-detect-motion-emit-events.md)
