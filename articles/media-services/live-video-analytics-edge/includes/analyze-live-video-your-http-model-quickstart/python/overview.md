---
ms.openlocfilehash: 8124f31213de1bbb794968c616e6efb940ac36b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582423"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="シグナルの流れ":::

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../../../media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](../../../media-graph-concept.md#http-extension-processor)ノードにビデオ フレームを送信します。 

HTTP 拡張ノードは、プロキシの役割を果たします。 これは、`samplingOptions` フィールドを使用して設定された受信ビデオフ レームをサンプリングし、ビデオ フレームを指定された画像の種類に変換します。 次に、その画像を、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、REST 経由で転送します。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) モデルを使用して構築されています。 HTTP 拡張プロセッサ ノードは、検出結果を収集し、イベントを [IoT Hub シンク](../../../media-graph-concept.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub) に送信します。

このクイックスタートでは次の作業を行います。

1. メディア グラフを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。