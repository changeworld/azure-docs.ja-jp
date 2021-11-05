---
title: Azure Video Analyzer のイベント ベースのビデオ記録 - Azure
description: イベントベースのビデオ録画 (EVR) とは、イベントによってトリガーされるときのビデオ記録のプロセスのことです。 問題のイベントは、ビデオ信号自体の処理 (モーションが検出された場合など) が原因で発生する可能性があります。または、独立したソース (ドアが開いているというドア センサー信号など) から発生する可能性があります。 この記事では、 EVR に関連するいくつかのユース ケースについて説明します。
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 721e86c43dc0268eaca3fb83b7b2784249f6010e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844565"
---
# <a name="event-based-video-recording"></a>イベントベースのビデオ記録  

イベントベースのビデオ記録 (EVR) とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 問題のイベントは、ビデオ信号自体の処理 (モーションが検出された場合など) が原因で発生する可能性があります。または、独立したソース (ドアが開いているというドア センサー信号など) から発生する可能性があります。 この記事では、 EVR に関連するいくつかのユース ケースについて説明します。

録画のタイムスタンプは UTC で格納されます。 録画されたビデオは、Video Analyzer のストリーミング機能を使用して再生できます。 詳細については、[ビデオ レコーディングの再生](playback-recordings-how-to.md)に関するページを参照してください。

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

* [パイプラインの概念](pipeline.md)
* [ビデオ記録の概念](video-recording.md) 

## <a name="overview"></a>概要 

Video Analyzer を使用すると、次の 2 つの方法で EVR を実行できます。
* 特定の RTSP 対応 IP カメラからクラウド内の特定のビデオ リソースへの入力を記録します。それぞれの新しいイベントは、そのビデオ リソースで使用可能なレコーディングに追加されます。
* MP4 ファイルを IoT Edge デバイスのローカル ストレージに分けて記録します。それぞれイベントによって新しい MP4 ファイルが作成されます。

この記事では、イベントベースのビデオ記録に関連するいくつかのユース ケースについて説明します。

### <a name="video-recording-triggered-by-motion-detection"></a>モーション検出によってトリガーされるビデオ レコーディング  

このユース ケースでは、ビデオ内でモーションが検出さた場合にのみビデオ クリップを記録でき、そのようなビデオ クリップが生成されたらアラートを受け取ることができます。 これは、重要なインフラストラクチャの保護を含む商用のセキュリティ シナリオに関連する場合があります。 EVR を使用すると、ストレージ コストを削減できます。

次の図は、このユース ケースに対応するパイプラインをグラフィカルに表したものです。 このようなパイプラインのパイプライン トポロジの JSON 表現については、[こちら](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-video-sink/topology.json)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/motion-detection.svg" alt-text="モーションが検出された場合のライブ ビデオのイベント ベースの記録。":::

この図では、カメラからのライブ ビデオ フィードが RTSP ソース ノードによってキャプチャされ、それが[モーション検出プロセッサ](pipeline.md#motion-detection-processor) ノードに配信されます。 ライブ ビデオでモーションが検出されると、モーション検出プロセッサ ノードによってイベントが生成され、それが[シグナル ゲート プロセッサ](pipeline.md#signal-gate-processor) ノードと IoT Hub メッセージ シンク ノードに送信されます。 後者のノードによってイベントが IoT Edge ハブに送信され、イベントはそこから他の送信先にルーティングされて、アラートがトリガーされます。 

モーション検出ノードからのイベントによってシグナル ゲート プロセッサ ノードがトリガーされ、開き、RTSP ソース ノードからのライブ ビデオ フィードが[ビデオ シンク ノード](pipeline.md#video-sink)に送信されます。 このような後続のモーション検出イベントがない場合、ゲートは構成された時間が経過すると閉じられます。 これにより、ビデオ リソースに追加される録画ビデオの期間が決まります。

### <a name="video-recording-based-on-events-from-other-sources"></a>他のソースからのイベントに基づくビデオ記録  

このユース ケースでは、別の IoT センサーからのシグナルを使用して、ビデオの記録をトリガーできます。 次の図は、このユース ケースに対応するパイプラインをグラフィカルに表したものです。 このようなパイプラインのパイプライン トポロジの JSON 表現については、[こちら](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/other-sources.svg" alt-text="外部ソースによって信号が送信された場合のライブ ビデオのイベント ベースの記録。":::

この図では、外部センサーによって IoT Edge ハブにイベントが送信されます。 その後イベントは、[IoT Hub メッセージ ソース](pipeline.md#iot-hub-message-source) ノード経由でシグナル ゲート プロセッサ ノードにルーティングされます。 シグナル ゲート プロセッサ ノードの動作は、前のユース ケースと同じです。ノードは、イベントによってトリガーされると開き、RTSP ソース ノードからファイル シンク ノードへライブ ビデオ フィードが流れるようになります。 ゲートが開くたび、新しい MP4 ファイルが IoT Edge デバイスのローカル ストレージに書き込まれます。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>外部推論モジュールに基づくビデオ記録 

このユース ケースでは、外部のロジック システムからの信号に基づいてビデオを記録できます。 このようなユース ケースの例としては、高速道路の交通状況のビデオ フィードでトラックが検出されたときにだけビデオを記録する場合があります。 次の図は、このユース ケースに対応するパイプラインをグラフィカルに表したものです。 このようなパイプラインのパイプライン トポロジの JSON 表現については、[こちら](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json)を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/external-inferencing-module.svg" alt-text="外部の推論モジュールによって信号が送信された場合のライブ ビデオのイベント ベースの記録。":::

この図では、RTSP ソース ノードによってカメラからのライブ ビデオ フィードがキャプチャされ、2 つのブランチに配信されています。1 つには[シグナル ゲート プロセッサ](pipeline.md#signal-gate-processor) ノードがあり、もう 1 つでは [HTTP 拡張](pipeline.md#http-extension-processor)ノードを使用して外部のロジック モジュールにデータが送信されています。 HTTP 拡張ノードを使用すると、パイプラインで REST を使用して外部の推論サービスに画像フレーム (JPEG、BMP、または PNG 形式) を送信できます。 このシグナル パスでは、通常、低フレーム レート (5 fps 未満) のみがサポートされます。 HTTP 拡張機能プロセッサ ノードを使用すると、外部推論モジュールに送信されるビデオのフレーム レートを下げることができます。

外部推論サービスからの結果は、HTTP 拡張ノードによって取得され、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに中継されます。そこでは、外部ロジック モジュールによって結果をさらに処理できます。 たとえば、推論サービスで車両を検出できる場合、ロジック モジュールではトラックなどの特定の車両を探すことができます。 ロジック モジュールでは、目的のものが検出されると、IoT Edge ハブ経由でパイプラインの IoT Hub メッセージ ソース ノードにイベントを送信することにより、シグナル ゲート プロセッサ ノードをトリガーできます。 シグナル ゲートからの出力は、ビデオ シンク ノードに進むことが示されます。 トラックが検出されるたび、ビデオがクラウドに記録されます (ビデオ リソースに追加されます)。

この例を拡張し、HTTP 拡張プロセッサ ノードの前にモーション検出プロセッサを使用します。 これにより、高速道路に長時間車両が存在しない場合がある夜間など、推論サービスの負荷が軽減されます。

## <a name="resiliency"></a>回復性
EVR にも適用される[回復性がある記録に関するメモ](continuous-video-recording.md#resilient-recording)を参照してください。

## <a name="next-steps"></a>次のステップ

* [チュートリアル: イベントベースのビデオ記録](record-event-based-live-video.md)
* [記録されたコンテンツの再生](playback-recordings-how-to.md)
