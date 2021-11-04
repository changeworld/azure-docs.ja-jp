---
title: エッジからのモーションの検出とイベントの生成 - Azure
description: このクイックスタートでは、Azure Video Analyzer を使用して、プログラムからダイレクト メソッドを呼び出すことで動きを検出し、イベントを生成する方法について説明します。
ms.topic: quickstart
ms.date: 11/04/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1682dc1bedc5fa6cbc076ad500c30e48ded7d5bf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554604"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>クイック スタート:モーションの検出とイベントの生成

[!INCLUDE [header](includes/edge-env.md)]

このクイックスタートでは、Azure Video Analyzer の基本的な操作手順について説明します。 IoT Edge デバイスとして Azure VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。 セットアップ手順の完了後、ビデオ パイプラインを通じて、シミュレートされたライブ ビデオ ストリームを実行できます。そのストリーム内のあらゆるモーションが検出およびレポートされます。 次の図は、そのパイプラインをグラフィカルに表したものです。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="モーションの検出":::

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>前提条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](./includes/common-includes/python-prerequisites.md)]
::: zone-end

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure にデプロイする](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

![モーション検出に基づく Azure Video Analyzer](./media/analyze-live-video/detect-motion.png)

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[モーション検出プロセッサ](../pipeline.md#motion-detection-processor) ノードにビデオ フレームを送信します。 モーション検出プロセッサ ノードを使用すると、ライブ ビデオのモーションを検出できます。 受信したビデオ フレームを調べ、ビデオに動きがあるかどうかを判断します。 モーションが検出されると、ビデオ フレームをパイプラインの次のノードに渡し、イベントを出力します。 最後に、生成されたすべてのイベントは IoT ハブ メッセージ シンクに送信され、そこで IoT Hub に公開されます。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
::: zone-end

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](./includes/detect-motion-emit-events-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](./includes/detect-motion-emit-events-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>サンプル ファイルを詳しく調べる

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](./includes/detect-motion-emit-events-quickstart/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](./includes/detect-motion-emit-events-quickstart/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>配置マニフェストを生成してデプロイする

::: zone pivot="programming-language-csharp"
[!INCLUDE [generate-deploy-deployment-manifest](./includes/detect-motion-emit-events-quickstart/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [generate-deploy-deployment-manifest](./includes/detect-motion-emit-events-quickstart/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="prepare-to-monitor-events"></a>イベントの監視の準備をする

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitor-events](./includes/detect-motion-emit-events-quickstart/csharp/prepare-monitor-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitor-events](./includes/detect-motion-emit-events-quickstart/python/prepare-monitor-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](./includes/detect-motion-emit-events-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](./includes/detect-motion-emit-events-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>結果を解釈する

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal で、ご利用のリソース グループに移動し、このクイックスタートを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

- [独自のモデルを使用したライブ ビデオの分析](analyze-live-video-use-your-model-http.md)に関するクイックスタートに従って、AI をライブ ビデオ フィードに適用します。
- 上級ユーザー向けのその他の課題を確認します。

  - RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。
  - Azure では、Linux VM を使用するのではなく、AMD64 または x64 Linux デバイスを使用します。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md?preserve-view=true&view=iotedge-2020-11)に関するページの手順に従います。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md?preserve-view=true&view=iotedge-2020-11)」の手順に従って、デバイスを Azure IoT Hub に登録します。
