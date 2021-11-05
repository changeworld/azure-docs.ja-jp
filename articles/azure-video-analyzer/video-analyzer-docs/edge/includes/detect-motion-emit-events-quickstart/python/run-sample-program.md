---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4964c543ae38234bdc119397154c1f2cd818957
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860564"
---
サンプル コードを実行するには、これらの手順に従います。

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/extension-settings.png" alt-text="拡張機能の設定":::

1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/verbose-message.png" alt-text="詳細メッセージの表示":::

1. Visual Studio Code で、_src/cloud-to-device-console-app/operations.json_ に移動します。
1. `pipelineTopologySet` ノードで、次の値が表示されていることを確認します。

   ```
   "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-detection/topology.json"
   ```

1. `livePipelineSet` および `livePipelineDelete` のノードで、**topologyName** の値が、パイプライン トポロジの **name** プロパティの値と一致していることを確認します。

   `"topologyName" : "MotionDetection"`

1. * VS Code で、`TERMINAL` ウィンドウに移動する
   * cd コマンドを使用して /video-analyzer-iot-edge-python-main/src/cloud-to-device-console-app ディレクトリに移動します。
   * "Python main.py" を実行すると、`TERMINAL` ウィンドウにメッセージが出力されます
1. _operations.json_ ファイルは、`pipelineTopologyList` および `livePipelineList` の呼び出しから始まります。 前回のクイックスタートを終了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されます。

   ```
   -----------------------  Request: pipelineTopologyList  --------------------------------------------------

   {
   "@apiVersion": "1.1"
   }

   ---------------  Response: pipelineTopologyList - Status: 200  ---------------

   {
     "value": []
   }

   --------------------------------------------------------------------------

   ```

   **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。

   - 前の pipelineTopologyUrlを使用する `pipelineTopologySet` の呼び出し。
   - 次の本文を使用する `livePipelineSet` の呼び出し。

   ```json
   {
     "@apiVersion": "1.1",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "MotionDetection",
       "description": "Sample pipeline description",
       "parameters": [
         {
           "name": "rtspUrl",
           "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
         },
         {
           "name": "rtspUserName",
           "value": "testuser"
         },
         {
           "name": "rtspPassword",
           "value": "testpassword"
         }
       ]
     }
   }
   ```

   - ライブ パイプラインとビデオのフローを開始する `livePipelineActivate` の呼び出し。
   - ライブ パイプラインが実行状態であることを示す `livePipelineList` の 2 回目の呼び出し。

1. **[ターミナル]** ウィンドウの出力は `Press Enter to continue` で一時停止します。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の **[出力]** ウィンドウに切り替えます。 Azure Video Analyzer モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. ライブ パイプラインは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 ライブ パイプラインを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。

   次の一連の呼び出しによって、リソースがクリーンアップされます。

   - `livePipelineDeactivate` の呼び出しによって、パイプラインが非アクティブ化されます。
   - `livePipelineDelete` の呼び出しによって、パイプラインが削除されます。
   - `pipelineTopologyDelete` の呼び出しによって、トポロジが削除されます。
   - `pipelineTopologyList` の最後の呼び出しによって、リストが空であることが示されます。
