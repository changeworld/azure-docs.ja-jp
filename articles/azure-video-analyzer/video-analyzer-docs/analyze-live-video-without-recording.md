---
title: 記録を行わないライブ ビデオの分析 - Azure
description: パイプライン技術は、ライブ ビデオ ストリームから分析を単に抽出するためだけに使用できます。これをエッジやクラウドに記録する必要はありません。 この記事ではこの概念について説明します。
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6230c565e7f193264c0640a3a278b2812f20ad7d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847073"
---
# <a name="analyzing-live-videos-without-recording"></a>記録を行わないライブ ビデオの分析


## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事 

* [パイプラインの概念](pipeline.md)
* [パイプラインの拡張機能の概念](pipeline-extension.md)
* [イベントベースのビデオ記録の概念](event-based-video-recording-concept.md)

## <a name="overview"></a>概要  

パイプライン トポロジを使用して、ビデオの一部をファイルや資産に記録することなく、ライブ ビデオを分析することができます。 以下に示すパイプライン技術は、「[イベントベースのビデオ記録](event-based-video-recording-concept.md)」の記事のものと似ていますが、資産シンク ノードやファイル シンク ノードはありません。

> [!NOTE]
> 現在、ライブ ビデオは、エッジ モジュールでのみ分析でき、クラウドではできません。

### <a name="motion-detection"></a>モーション検出

以下に示すパイプライン トポロジは、[RTSP ソース](pipeline.md#rtsp-source) ノード、[モーション検出プロセッサ](pipeline.md#motion-detection-processor) ノード、および [IoT Hub メッセージ シンク](pipeline.md#iot-hub-message-sink) ノードで構成されています。[JSON 表現](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/motion-detection/topology.json)で使用されている設定を確認できます。 このトポロジを使用すると、受信ライブ ビデオ ストリームのモーションを検出し、IoT Hub メッセージ シンク ノードを介して他のアプリやサービスにモーション イベントを中継できます。 外部のアプリまたはサービスではアラートをトリガーしたり、適切な担当者に通知を送信したりすることができます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detection.svg" alt-text="ライブ ビデオでのモーションの検出":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>カスタム ビジョン モデルを使用するビデオの分析 

以下に示すパイプライン トポロジでは、別のモジュールにパッケージ化されたカスタム ビジョン モデルを使用してライブ ビデオ ストリームを分析できます。 [JSON 表現](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json)で使用される設定を確認できます。 推論サービスとして実行される IoT Edge モジュールにモデルをラップする[例](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions)は他にもあります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detected-frames.svg" alt-text="カスタム ビジョン モジュールを使用するライブ ビデオの分析":::

このパイプライン トポロジでは、RTSP ソースからのビデオ入力が [HTTP 拡張プロセッサ](pipeline.md#http-extension-processor) ノードに送信され、そこからイメージ フレーム (JPEG、BMP、または PNG 形式) が REST 経由で外部の推論サービスに送信されます。 外部の推論サービスからの結果は、HTTP 拡張ノードによって取得され、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに中継されます。 この種のパイプライン トポロジは、たとえば、交差点での車両の時系列分布の理解や、小売店でのコンシューマー トラフィック パターンの把握など、さまざまなシナリオ向けのソリューションを構築するために使用できます。

>[!TIP]
> HTTP 拡張プロセッサ ノード内のフレーム レートをダウンストリームに送信する前に、`samplingOptions` フィールドを使用して管理できます。

この例を拡張し、HTTP 拡張プロセッサ ノードの前にモーション検出プロセッサを使用します。 これにより、ビデオにモーション アクティビティがある場合にのみ使用されるため、推論サービスの負荷が軽減されます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/custom-model.svg" alt-text="モーションのあるフレームでのカスタム ビジョン モジュールを使用したライブ ビデオの分析":::

## <a name="next-steps"></a>次の手順

[クイック スタート:独自のモデルを使用してライブ ビデオを分析する - HTTP](analyze-live-video-use-your-model-http.md)
