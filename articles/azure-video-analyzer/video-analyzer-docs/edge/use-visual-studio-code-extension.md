---
title: Azure Video Analyzer の Visual Studio Code 拡張機能を使用する
description: この記事では、Azure Video Analyzer の Visual Studio Code 拡張機能の使用を開始する手順について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0784f69e40597bef3a08512e3bcbd01cf1343390
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552565"
---
# <a name="use-the-visual-studio-code-extension-for-azure-video-analyzer"></a>Azure Video Analyzer の Visual Studio Code 拡張機能を使用する

[!INCLUDE [header](includes/edge-env.md)]

この記事では、Azure Video Analyzer の Video Studio Code 拡張機能の使用を開始する手順について説明します。 IoT Hub 経由で Visual Studio Code 拡張機能を Video Analyzer Edge モジュールに接続し、[サンプルのパイプライン トポロジ](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink)をデプロイします。 その後、ビデオ リソースにビデオを継続的に記録する、シミュレートされたライブ ビデオ ストリームを、ライブ パイプライン経由で実行します。 次の図はパイプラインを表しています。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-continuous-video-recording/continuous-video-recording-overview.svg" alt-text="継続的なビデオ記録":::
 
 ## <a name="prerequisites"></a>前提条件
 
* [Visual Studio Code](https://code.visualstudio.com/) ([Azure Video Analyzer 拡張機能を含む](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer))
* 「[クイックスタート: Azure Video Analyzer で作業を開始する](./get-started-detect-motion-emit-events.md)」または「[連続録画と再生](./use-continuous-video-recording.md)」チュートリアルのいずれか

 > [!NOTE]
 > この記事の画像は、「[連続録画と再生](./use-continuous-video-recording.md)」チュートリアルに基づいています。    

## <a name="set-up-your-development-environment"></a>開発環境を設定する

### <a name="obtain-your-iot-hub-connection-string"></a>IoT ハブの接続文字列を取得する

Video Analyzer Edge モジュールへの呼び出しを行うには、まず、Visual Studio Code 拡張機能を IoT Hub に接続するために接続文字列が必要です。

1. Azure portal で、IoT Hub アカウントに移動します。
1. 左側のペインで **[共有アクセス ポリシー]** を見つけ、それを選択します。
1. **iothubowner** という名前のポリシーを選択します。
1. **[プライマリ接続文字列]** の値をコピーします。 これは `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX` のようになります。

### <a name="connect-the-visual-studio-code-extension-to-the-iot-hub"></a>Visual Studio Code 拡張機能を IoT Hub に接続する

IoT Hub 接続文字列を使用して、Visual Studio Code Video 拡張機能を Video Analyzer モジュールに接続します。 

1.  Visual Studio Code で、最も左側のアクティビティ バーから **[Azure Video Analyzer]** アイコンを選択します。
1.  Video Analyzer 拡張機能ペインで、 **[接続文字列の入力]** ボタンをクリックします。
1.  上部に、IoT Hub の接続文字列を貼り付けます。
1.  AVA がデプロイされているデバイスを選択します。 既定の名前は `avasample-iot-edge-device` です。
1.  Video Analyzer モジュールを選択します。 既定の名前は `avaedge` です。

![接続文字列の入力方法を示している Gif](./media/use-visual-studio-code-extension/enter-connection-string.gif)

これで Video Analyzer 拡張機能ペインに、接続されているデバイスと、そのすべてのモジュールが表示されるはずです。 モジュールの下に、パイプライン トポロジが一覧表示されます。 既定では、パイプライン トポロジはデプロイされていません。

## <a name="create-a-pipeline-topology"></a>パイプライン トポロジを作成する 

[パイプライン トポロジ](../pipeline.md)を使用すると、一連の相互接続されたノードを使用して、カスタム ニーズに合わせてどのようにライブ ビデオや録画ビデオの処理と分析を行う必要があるかを記述できます。 

1.  左側の **[モジュール]** で **[パイプライン トポロジ]** を右クリックし、 **[パイプライン トポロジの作成]** を選択します。
1.  上部にある **[サンプル トポロジを試す]** の **[継続的なビデオ記録]** で、 **[Azure Video Analyzer ビデオに記録する]** を選択します。 メッセージが表示されたら、 **[続行]** をクリックします。
1.  右上の **[保存]** をクリックします。

![トポロジを追加する方法を示している Gif](./media/use-visual-studio-code-extension/add-topology.gif)

ここで、左側の **[パイプライン トポロジ]** の一覧に、**CVRToVideoSink** というラベルが付いたエントリがあることに注目してください。 これがパイプライン トポロジで、一部のパラメーターは変数として定義されています。

## <a name="create-a-live-pipeline"></a>ライブ パイプラインを作成する

ライブ パイプラインは、パイプライン トポロジのインスタンスです。 パイプライン トポロジ内の変数には、ライブ パイプラインが作成されるときにデータが格納されます。

1.  左側の **[パイプライン トポロジ]** で **[CVRToVideoSink]** を右クリックし、 **[ライブ パイプラインの作成]** を選択します。
1.  **[インスタンス名]** には `livePipeline1` を入力します。
1. **[パラメーター]** セクションで、**rtspUrl** パラメーターの下に `rtsp://rtspsim:554/media/camera-300s.mkv` を入力します。
1.  右上にある **[保存してアクティブにする]** をクリックします。

![ライブ パイプラインを作成してアクティブにする方法を示している Gif](./media/use-visual-studio-code-extension/create-and-activate.gif)

これでライブ パイプラインがアクティブになったので、「[連続録画と再生](./use-continuous-video-recording.md#prepare-to-monitor-the-modules)」チュートリアルに示されているように、IoT Hub 拡張機能の **[組み込みイベント エンドポイントの監視の開始]** ボタンをクリックすると、運用イベントを表示できます。

## <a name="next-steps"></a>次のステップ

Visual Studio Code 拡張機能を使用して他のサンプル パイプライン トポロジの 1 つを読み込んで、各ノードのプロパティを表示します。 [Azure Video Analyzer の Visual Studio Code 拡張機能](../visual-studio-code-extension.md)のリファレンスに従って、変数をパラメーター化する方法と、モジュールを接続する方法について学習します。
