---
title: Azure Video Analyzer Visual Studio Code 拡張機能
description: このクイックスタートでは、Azure Video Analyzer の Visual Studio Code 拡張機能の使用を開始する手順について説明します。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: e3d118f2651e7b680b85bbb41bb6ecc3d7106bba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745681"
---
# <a name="quickstart-azure-video-analyzer-visual-studio-code-extension"></a>クイックスタート: Azure Video Analyzer の Visual Studio Code 拡張機能

このクイックスタートの目的は、Video Analyzer の Visual Studio Code 拡張機能を設定して Video Analyzer エッジ モジュールに接続し、サンプル パイプライン トポロジをデプロイする方法を説明することです。  ここでは、概要クイックスタートと同じパイプラインを使用します。  

設定手順の完了後は、シミュレートされたライブ ビデオ ストリームをライブ パイプライン経由で実行し、そのストリーム内のモーションを検出してレポートできるようになります。 次の図は、そのパイプラインをグラフィカルに表したものです。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="モーションの検出":::
 
 ## <a name="prerequisites"></a>前提条件
 
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、無料の[アカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Video Analyzer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer)

* [Azure Video Analyzer の概要](./get-started-detect-motion-emit-events.md)に関するクイックスタートを完了していない場合は、[Azure リソースを設定](#set-up-azure-resources)してください。    

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="connect-the-azure-video-analyzer-visual-studio-code-extension-to-your-iot-hub"></a>Azure Video Analyzer の Visual Studio Code 拡張機能を IoT ハブに接続する

拡張機能をエッジ モジュールに接続するには、最初に自分の接続文字列を取得する必要があります。 そのためには、次の手順を実行します。

1.  [Azure portal](https://portal.azure.com) に移動し、自分の IoT ハブを選択します。
1.  [`Settings`] の左側にある [`Shared access policies`] を選択します。
1.  ポリシー名として `iothubowner` を選択します。
1.  右側のウィンドウで、`Primary connection string` をコピーします。

接続文字列を用意したら、次の手順に沿って拡張機能をエッジ モジュールに接続します。

1.  Visual Studio Code で、左側にある [`Azure Video Analyzer`] アイコンを選択します。
1.  [`Enter Connection String`] ボタンをクリックします。
1.  ポータルから取得した接続文字列を最上部に貼り付けます。
1.  デバイスを選択します (既定値は `avasample-iot-edge-device`)。
1.  Video Analyzer モジュールを選択します (既定値は `avaedge`)。

これで、自分の接続したデバイスが、基になるモジュールと共に左側に表示されます。  既定では、パイプライン トポロジはデプロイされていません。

## <a name="deploy-a-topology-and-live-pipeline"></a>トポロジとライブ パイプラインをデプロイする

パイプライン トポロジは、Video Analyzer で動作を定義するために使用する基本的な構成要素です。  パイプライン トポロジについては、[こちら](./pipeline.md)で詳しく確認できます。  このセクションでは、テンプレートのパイプライン トポロジをデプロイしてから、トポロジのインスタンス (ライブ パイプライン) を作成します。 ライブ パイプラインは実際のビデオ ストリームに接続されます。

1.  左側の `Modules` で `Pipeline topologies` を右クリックし、[`Create pipeline topology`] を選択します。
1.  上部にある [`Try sample topologies`]、[`Motion Detection`]、[`Publish motion events to IoT Hub`] の順に選択します。  メッセージが表示されたら、`Proceed` をクリックします。
1.  右上にある [`Save`] をクリックします。

これで、`MotionDetection` というラベルの付いたエントリが左側の `Pipeline topologies` リストに表示されます。  これがパイプライン トポロジです。一部のパラメーターは、ライブ パイプラインの作成時に指定できる変数として定義されています。  次に、ライブ パイプラインを作成します。

1.  左側の `Pipeline topologies` で `MotionDetection` を右クリックし、[`Create live pipeline`] を選択します。
1.  [`Live pipeline name`] に「`mdpipeline1`」と入力します。
1.  `Parameters` セクションで、次の操作を行います。
    - [rtspPassword] には「testpassword」と入力します。
    - [rtspUrl] には「rtsp://rtspsim:554/media/camera-300s.mkv」と入力します。
    - [rtspUserName] には「testuser」と入力します。
1.  右上にある [Save and activate]\(保存してアクティブにする\) をクリックします。

これにより、トポロジのデプロイが開始され、自分のエッジ デバイスでライブ パイプラインが稼働します。  概要クイックスタートで Azure IoT Hub 拡張機能をインストールしている場合、Azure IoT Hub の Visual Studio Code 拡張機能で組み込みのイベント エンドポイントを監視して、「[結果を観察する](./get-started-detect-motion-emit-events.md#observe-results)」セクションで説明されているとおりにそれを監視できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

* [Azure Video Analytics の Visual Studio Code 拡張機能](./visual-studio-code-extension.md)のさまざまな関数について学習します。
