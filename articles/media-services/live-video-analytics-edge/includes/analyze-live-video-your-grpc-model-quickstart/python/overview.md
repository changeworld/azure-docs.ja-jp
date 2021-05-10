---
ms.openlocfilehash: 8d18b2e72a2a9e787f69d6adaeae8ebc3ca162b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582751"
---
![概要](../../../media/quickstarts/gRPC-extension.svg)

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../../../media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[モーション検出プロセッサ](../../../media-graph-concept.md#motion-detection-processor) ノードにビデオ フレームを送信します。 このプロセッサはモーションを検出し、検出時にビデオ フレームを [gRPC 拡張プロセッサ](../../../media-graph-concept.md#grpc-extension-processor) ノードにプッシュします。

gRPC 拡張ノードはプロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、[共有メモリ](https://en.wikipedia.org/wiki/Shared_memory)を介して gRPC エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、画像を gRPC 経由で転送します。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) モデルを使用して構築されています。 gRPC 拡張プロセッサ ノードは検出結果を収集し、イベントを [IoT Hub シンク](../../../media-graph-concept.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub) に送信します。

このクイックスタートでは次の作業を行います。

1. メディア グラフを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。