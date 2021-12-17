---
title: リモートデバイスアダプターを使用してカメラをクラウドに接続します。
description: この記事では、リモートデバイスアダプターを使用してカメラをAzure Video Analyzerサービスに接続する方法について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74734444b33c6963097d4e23e3859aedbc9a91e7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563209"
---
# <a name="connect-cameras-to-the-cloud-using-a-remote-device-adapter"></a>リモートデバイスアダプターを使用してカメラをクラウドに接続します。

[!INCLUDE [header](includes/cloud-env.md)]

Azure Video Analyzer サービスを使用すると、クラウドに接続されている RTSPカメラからビデオをキャプチャして記録することができます。 このため、このようなカメラはインターネット経由でアクセスできる必要がありますが、多くの場合には許可されない可能性があります。 代わりに、Video Analyzer edge モジュールを、RTSP カメラと同じ (プライベート) ネットワーク上の軽量のエッジデバイスにデプロイし、エッジデバイスをインターネットに接続できます。 これでエッジ モジュールは、Video Analyzer サービスを *リモート デバイス* (カメラ) に接続できる *アダプター* として設定できます。 エッジ モジュールは、RTSP カメラと Video Analyzer サービス間のビデオ トラフィックの[透過的なゲートウェイ](../../../iot-edge/iot-edge-as-gateway.md)として機能します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="リモートデバイスアダプターを使用してカメラをクラウドに接続します":::

## <a name="pre-reading"></a>事前読み取り

* [カメラをクラウドに 接続します](connect-cameras-to-cloud.md)
* [クイックスタート: Azure portal の Video Analyzer ライブ パイプラインの概要](get-started-livepipelines-portal.md)

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure Video Analyzer を IoT Edge デバイスにデプロイする](../edge/deploy-iot-edge-device.md)
* [IoT Hub は、Video Analyzerアカウントにアタッチする必要があります](../create-video-analyzer-account.md#post-deployment-steps)
* [RTSP カメラ](../quotas-limitations.md#supported-cameras)
  * カメラがエッジデバイスと同じネットワーク上にあることを確認します。
  * ビデオを最大帯域幅 (kBpsまたはキロビット/秒) で送信するようにカメラを構成できることを確認します。

## <a name="overview"></a>概要
リモートデバイスアダプターを使用してカメラを VideoAnalyzer サービスに接続するには、次のことを行う必要があります。

1. IoT Hubで RTSP カメラを表すIoTデバイスを作成します。
1. 上のデバイスの透過的ゲートウェイとして機能するデバイスアダプターを、Video Analyzer edgeモジュールに作成します。
1. ビデオをキャプチャしてカメラから録画するために、Video Analyzer サービスでライブパイプラインを作成するときに、IoT デバイスとデバイスアダプターを使用します。



## <a name="create-an-iot-device"></a>IoTデバイスを作成します。

Video Analyzer サービスに接続する必要がある各 RTSPカメラを表す IoT デバイスを作成します。 Azure Portal で次の操作を行います。

1. IoT Hubに移動します。
1. **デバイスの管理** の下にある **デバイス** を選択します。
1. **+ デバイスの追加** を選択します。 
1. 一意の文字列を使用して **デバイスID** を入力してください (例: building404-camera1)
1. **認証の種類** は **対称キー** である必要があります。
1. その他のプロパティはすべて既定値のままにすることができます。
1. **保存** を選択して IoT デバイスを作成します。
1. IoT デバイスを選択し、 **プライマリキー** または **セカンダリキー** を記録します。これは、以下で必要になります。

## <a name="create-a-remote-device-adapter"></a>リモートデバイスアダプターを作成します。

Video Analyzer edge モジュールがカメラと Video Analyzerサービス間のビデオの透過的なゲートウェイとして機能するようにするには、各カメラ用のリモートデバイスアダプタを作成する必要があります。 次の値を必要とする [**remoteDeviceAdapterSet** ダイレクトメソッド](../edge/direct-methods.md)を呼び出します。 

* IoTデバイスのデバイス ID
* IoT デバイスのプライマリ キー
* カメラの IPアドレス

Azure Portal で次の操作を行います。

1. IoT Hubに移動します。
1. **[デバイスの自動管理]** の下の **[IoT Edge]** ウィンドウを選択します。
1. Video Analyzer Edge モジュールが配置されている IoT Edge デバイス (**ava-sample-device** など) を選択します。
1. モジュールの下に、Video Analyzer edge モジュール(**avaedge** など) を選択します。
1. **</>ダイレクトメソッド** を選択します。 
1. メソッド名として **remoteDeviceAdapterSet** を入力します。
1. **ペイロード** について次のように入力します。

```
 {
   "@apiVersion" : "1.1",
   "name": "<name of remote device adapter such as remoteDeviceAdapterCamera1>",
   "properties": {
     "target": {
       "host": "<Camera's IP address>"
      },
     "iotHubDeviceConnection": {
      "deviceId": "<IoT Hub Device ID>",
      "credentials": {
        "@type": "#Microsoft.VideoAnalyzer.SymmetricKeyCredentials",
        "key": "<Primary or Secondary Key>"
       }
     }
   }
 }
 
```

成功した場合は、状態コード201の応答が返されます。

設定されているすべてのリモート デバイス アダプターを一覧表示するには、次のペイロードを使用して **remoteDeviceAdapterList** ダイレクト メソッドを呼び出します。
```
 {
   "@apiVersion" : "1.1"
 }
```


## <a name="create-pipeline-topology-in-the-video-analyzer-service"></a>Video Analyzer サービスでパイプライントポロジを作成します。

ファイアウォールの背後にあるカメラから取り込むクラウドパイプライントポロジを作成する場合は、パイプライントポロジの RTSP ソースノードでトンネリングを有効にする必要があります。 このような [パイプライントポロジ](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cloud-record-camera-behind-firewall)の例を参照してください。  


前の手順で作成した IoT デバイスに基づく次の値は、RTSP ソース ノードでトンネリングを有効にするために必要です。

* IoT Hub 名
* IoT Hub のデバイス ID

```
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "url": "${rtspUrlParameter}",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUsernameParameter}",
                        "password": "${rtspPasswordParameter}"
                    },
                    "tunnel": { 
                        "@type": "#Microsoft.VideoAnalyzer.SecureIotDeviceRemoteTunnel",
                        "iotHubName" : "<IoT Hub Name>",
                        "deviceId": "${ioTHubDeviceIdParameter}"
                    }
                }
            }
``` 

次のことを確認します。
* `Transport` が `tcp`
* `Endpoint` が `UnsecuredEndpoint`
* `Tunnel` が `SecureIotDeviceRemoteTunnel`

[このクイックスタート](get-started-livepipelines-portal.md#deploy-a-live-pipeline)では、Azure portal でパイプライン トポロジとライブ パイプラインを作成する手順について概説します。 サンプルトポロジを使用します`Live capture, record, and stream from RTSP camera behind firewall` 

## <a name="create-and-activate-a-live-pipeline"></a>ライブパイプラインを作成してアクティブ化します。

ライブパイプラインを作成するときに、RTSP URL、RTSP ユーザー名、RTSP パスワード、IoT Hub デバイス IDを定義する必要があります。 サンプルペイロードは次のとおりです。

```
   {
    "name": "record-from-building404-camera1",
    "properties": {
        "topologyName": "record-camera-behind-firewall",
        "description": "Capture, record and stream video from building404-camera1 via a remote device adapter",
        "bitrateKbps": 1500,
        "parameters": [
            {
                "name": "rtspUrlParameter",
                "value": "<RTSP URL for building404-camera1 such as rtsp://localhost:554/media/video>"
            },
            {
                "name": "rtspUsernameParameter",
                "value": "<User name for building404-camera1>"
            },
            {
                "name": "rtspPasswordParameter",
                "value": "<Password for building404-camera1>"
            },
            {
                "name": "ioTHubDeviceIdParameter",
                "value": "<IoT Hub Device ID such as building404-camera1>"
            },
            {
                "name": "videoName",
                "value": "video-from-building404-camera1"
            }
          ]
       }
   }
```
RTSP URL の IP アドレスは、**localhost** である必要があります。 `bitrateKbps`値が RTSP カメラからのビデオの最大ビットレート設定と一致していることを確認します。

ライブパイプラインを作成したら、パイプラインをアクティブ化して、Video Analyzer video リソースへの記録を開始できます。 前の手順で説明した[クイックスタート](get-started-livepipelines-portal.md#deploy-a-live-pipeline)では、Azure portal でライブパイプラインをアクティブ化する方法についても説明します。


### <a name="playback-recorded-video-in-the-azure-portal"></a>Azure portal に記録されたビデオの再生。

1. ライブパイプラインをアクティブ化すると、ビデオリソースは Azure portal の VideoAnalyzer アカウントの **ビデオ** ウィンドウで使用できるようになります。 パイプラインがアクティブで録画中のため、その状態は、**Is in use** と示されます。
1. ライブパイプラインで定義されたビデオリソースを選択して、ビデオを表示します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="クラウドのライブパイプラインによってキャプチャされたライブビデオのスクリーンショット。":::

ビデオの再生中にエラーが発生した場合は、 [このトラブルシューティングガイド](troubleshoot.md#unable-to-play-video-after-activating-live-pipeline)の手順に従ってください。

[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>次の手順

Video Analyzer アカウントにビデオが存在するので、 [このチュートリアル](export-portion-of-video-as-mp4.md)を使用して、この記録されたビデオのクリップを MP4 形式にエクスポートできます。
