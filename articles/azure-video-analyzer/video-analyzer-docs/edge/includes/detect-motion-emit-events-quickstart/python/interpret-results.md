---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: ac02502e1f4958ec0fa6e0a445d720e3c9a00351
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860304"
---
ライブ パイプラインを実行すると、モーション検出プロセッサ ノードから IoT Hub メッセージ シンク ノードを通過して IoT ハブに結果が渡されます。 Visual Studio Code の **[出力]** ウィンドウに表示されるメッセージには、**body** セクションと **applicationProperties** セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Azure Video Analyzer モジュールによって、アプリケーションのプロパティと本文の内容が定義されます。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

ライブ パイプラインがアクティブ化されると、RTSP ソース ノードによって、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続が試行されます。 接続に成功すると、次のイベントが出力されます。

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
  {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ava-sample-deployment/providers/Microsoft.Media/videoAnalyzers/avasample",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T00:58:58.602Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-message-source": "Telemetry",
    "messageId": "459c3255-7c86-4ff5-a1e5-7ce3fcb07627",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
```

前の出力は、次のような内容を表します。

- このメッセージは診断イベント **MediaSessionEstablished** です。 これは、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
- **sdp** セクションには、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="motiondetection-event"></a>MotionDetection イベント

モーションが検出されると、Video Analyzer モジュールから推論イベントが送信されます。 **type** は、これがモーション検出プロセッサからの結果であることを示す **motion** に設定されています。

このメッセージの例を次に示します。

```json
{
  "body": {
    "timestamp": 145818422564951,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.322176,
            "t": 0.574627,
            "w": 0.525,
            "h": 0.088889
          }
        }
      }
    ]
  },
  "properties": { … },
  "systemProperties": { … }
}
```

次の点に注意してください。

- **body** 値は、分析イベントに関するデータです。 このケースでは、推論イベントであるため、body には **timestamp** と **inferences** データが含まれています。
- **inferences** データは、**type** が **motion** であることを示しています。 ここには **motion** イベントに関する追加データが含まれています。
- **box** セクションには、移動する物体を囲む境界ボックスの座標が格納されます。 これらの値は、ビデオの幅と高さ (ピクセル単位) で正規化されます たとえば、元のピクセル座標を取得するには、水平ディメンションを 1920 で乗算し、垂直ディメンションを 1080 で乗算します。

  ```
  l - distance from left of image
  t - distance from top of image
  w - width of bounding box
  h - height of bounding box
  ```
