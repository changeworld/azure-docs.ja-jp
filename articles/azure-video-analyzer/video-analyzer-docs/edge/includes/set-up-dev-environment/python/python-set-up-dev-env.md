---
author: russell-cooks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: 50bc0dc30b14685747e24afc7bda2964e8fd2d37
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861124"
---
### <a name="get-the-sample-code"></a>サンプル コードを入手する

1. [Ava python サンプル リポジトリ](https://github.com/Azure-Samples/video-analyzer-iot-edge-python)をクローンします。
1. Visual Studio Code を開始して、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、src/cloud-to-device-console-app フォルダーに移動し、**appsettings.json** という名前のファイルを作成します。 このファイルには、プログラムを実行するために必要な設定が格納されています。
1. 上記のセットアップ手順で作成したストレージ アカウントのファイル共有を参照し、"deployment-output" ファイル共有の下にある **appsettings.json** を見つけます。 そのファイルをクリックし、[ダウンロード] ボタンをクリックします。 コンテンツは新しいブラウザー タブで開きます。これは次のようになっています。

   ```json
   {
     "IoThubConnectionString": "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
     "deviceId": "avasample-iot-edge-device",
     "moduleId": "avaedge"
   }
   ```

   IoT Hub 接続文字列を使用すると、Visual Studio Code を使用して Azure IoT Hub 経由でエッジ モジュールにコマンドを送信できます。 上記の JSON を **src/cloud-to-device-console-app/appsettings.json** ファイルにコピーします。
1. 次に、src/edge フォルダーに移動して、 **.env** という名前のファイルを作成します。 このファイルには、Visual Studio Code がエッジ デバイスにモジュールをデプロイするために使用するプロパティが格納されています。
1. 上記のセットアップ手順で作成したストレージ アカウントのファイル共有を参照し、"deployment-output" ファイル共有の下にある **env.txt** を見つけます。 そのファイルをクリックし、[ダウンロード] ボタンをクリックします。 コンテンツは新しいブラウザー タブで開きます。これは次のようになっています。

   ```
        SUBSCRIPTION_ID="<Subscription ID>"
        RESOURCE_GROUP="<Resource Group>"
        AVA_PROVISIONING_TOKEN="<Provisioning token>"
        VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
        VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
        APPDATA_FOLDER_ON_DEVICE="/var/lib/videoAnalyzer"
        CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"
        CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

   この JSON を **env.txt** から **src/edge/.env** ファイルにコピーします。

### <a name="connect-to-the-iot-hub"></a>IoT ハブに接続します

1.  Visual Studio Code で、左下隅の **[Azure IoT Hub]** ペインの横にある **[その他のアクション]** アイコンを選択して、IoTHub 接続文字列を設定します。 src/cloud-to-device-console-app/appsettings.json ファイルからその文字列をコピーします。

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
1. 30 秒ほど経過したら、左下のセクションで Azure IoT Hub を更新します。 次のモジュールがデプロイされているエッジ デバイス `avasample-iot-edge-device` が表示されます。
    - Edge ハブ (モジュール名: **edgeHub**)
    - Edge エージェント (モジュール名: **edgeAgent**)
    - Video Analyzer (モジュール名: **avaedge**)
    - RTSP シミュレーター (モジュール名: **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>モジュールの監視の準備をする

このクイックスタートまたはチュートリアルを実行すると、イベントが IoT Hub に送信されます。 それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある **[Azure IoT Hub]** を探します。
1. **[デバイス]** ノードを展開します。
1. `avasample-iot-edge-device` を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
