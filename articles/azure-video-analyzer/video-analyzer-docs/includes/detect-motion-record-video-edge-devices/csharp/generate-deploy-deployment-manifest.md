---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 05/03/2021
ms.author: naiteeks
ms.openlocfilehash: c541518d88ace6b7412c2a2e288de4919befd35f
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387343"
---
エッジ デバイスにデプロイされるモジュールは、配置マニフェストによって定義されます。 また、これらのモジュールの構成設定も定義されます。

マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、これらの手順に従います。

1.  Visual Studio Code を開きます。
1.  **[Azure IoT Hub]** ペインの横にある **[その他のアクション]** アイコンを選択して、IoT Hub 接続文字列を設定します。 この文字列は、_src/cloud-to-device-console-app/appsettings.json_ ファイルからコピーすることができます。

    ![IOT 接続文字列を設定する](../../../media/vscode-common-screenshots/set-connection-string.png)

    > [!NOTE]
    > IoT ハブに使用する組み込みのエンドポイント情報を入力するよう求められる場合があります。 この情報を入手するには、Azure portal で IoT ハブに移動し、左側のナビゲーション ペインで **[組み込みのエンドポイント]** オプションを探します。 それをクリックし、 **[イベント ハブ互換エンドポイント]** セクションの **[イベント ハブ互換エンドポイント]** を探します。 ボックス内のテキストをコピーして使用します。 エンドポイントは次のようになります。 <br/>`Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>`

1.  **src/edge/deployment.template.json** を右クリックして、 **[Generate IoT Edge deployment manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。

    ![IoT Edge 配置マニフェストの生成](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    このアクションによって、_deployment.amd64.json_ という名前のマニフェスト ファイルが _src/edge/config_ フォルダーに作成されます。

1.  **src/edge/config/deployment.amd64.json** を右クリックして、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択し、エッジ デバイスの名前を選択します。

    ![単一デバイスのデプロイを作成する](../../../media/quickstarts/create-deployment-single-device.png)

1.  IoT Hub デバイスを選択するよう求められたら、ドロップダウン メニューから **avasample-iot-edge-device** を選択します。
1.  約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

    - Video Analyzer エッジ モジュール (モジュール名 `avaedge`)
    - リアルタイム ストリーミング プロトコル (RTSP) シミュレーター (モジュール名 `rtspsim`)
