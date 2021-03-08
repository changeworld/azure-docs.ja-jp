---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "89570062"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="シグナルの流れ":::

上の図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../../../media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[モーション検出プロセッサ](../../../media-graph-concept.md#motion-detection-processor) ノードにビデオ フレームを送信します。 RTSP ソースは、同じビデオ フレームを[シグナル ゲート プロセッサ](../../../media-graph-concept.md#signal-gate-processor) ノードに送信します。このノードは、イベントによってトリガーされるまで閉じたままになっています。

モーション検出プロセッサは、ビデオでモーションを検出すると、イベントをシグナル ゲート プロセッサ ノードに送信してトリガーします。 ゲートは、構成されている期間開いて、[ファイル シンク](../../../media-graph-concept.md#file-sink) ノードにビデオ フレームを送信します。 このシンク ノードは、エッジ デバイスのローカル ファイル システムに、ビデオを MP4 ファイルとして記録します。 ファイルは、構成した場所に保存されます。

このクイックスタートでは次の作業を行います。

1. メディア グラフを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。
