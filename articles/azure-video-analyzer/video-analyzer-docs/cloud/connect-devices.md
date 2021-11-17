---
title: Azure Video Analyzer へのデバイスの接続
description: この記事では、Azure Video Analyzer にデバイスを接続する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70ad7876b56b12c6a9d6fd1654e12dc0a764aa00
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339974"
---
# <a name="connect-devices-to-azure-video-analyzer"></a>Azure Video Analyzer へのデバイスの接続

[!INCLUDE [header](includes/cloud-env.md)]

デバイスからビデオをキャプチャして記録するには、Azure Video Analyzer サービスに対して[RTSP](../terminology.md#rtsp)接続を確立する必要があります。 デバイスがファイアウォールの内側にある場合、そのような接続はブロックされるため、Azure からの受信接続を許可するルールを作成することは常に可能であるとは限りません。 このようなデバイスをサポートするには、[Azure IoT プラグアンドプレイ](../../../iot-develop/overview-iot-plug-and-play.md)デバイスの実装をビルドしてインストールします。これは、Video Analyzer から IoT Hub 経由で送信されたコマンドをリッスンし、サービスへのセキュリティで保護された WebSocket トンネルを開きます。 このようなトンネルが確立されると、Video Analyzer は RTSP サーバーに接続できるようになります。

## <a name="overview"></a>概要 

この記事では、Azure Video Analyzer がデバイスからビデオをキャプチャして記録できるようにする Azure IoT PnP デバイス実装の構築についての概要を説明します。 

アプリケーションは次の操作を行う必要があります。 

1. IoT デバイスとして実行する 
1. 特定のコマンド (`tunnelOpen`) を使用して[IoT PnP](../../../iot-develop/overview-iot-plug-and-play.md)インターフェイスを実装する 
1. このようなコマンドを受け取ると、次のようになります。 
   * 受信した引数を検証する 
   * 提供されたトークンを使用して指定された URL へのセキュリティで保護された Websocket 接続を開きます。
   * Websocket バイトをカメラの RTSP サーバー TCP 接続に転送する

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-devices/connect-devices.svg" alt-text="デバイスをクラウドに接続する":::

## <a name="run-as-an-iot-device"></a>IoT デバイスとして実行する 

Video Analyzer アプリケーションは、Video Analyzer の PnP プラグインとしてデプロイされます。 そのためには、[Azure IoT device SDK](../../../iot-develop/libraries-sdks.md#device-sdks) の 1 つを使用して、IoT PnP デバイスの実装を構築する必要があります。 IoT Hub に IoT デバイスを登録して、IoT Hub デバイス ID とデバイス接続文字列を取得します。

### <a name="iot-device-clientconfiguration"></a>IoT デバイス クライアントの構成

* PnP クエリをサポートするように OPTION_MODEL_ID を `“dtmi:azure:videoanalyzer:WebSocketTunneling;1”` に設定します。  
* デバイスでは、MQTT または MQTT over WebSockets プロトコルを使用して Azure IoT Hub に接続していることを確認します。 
    * IoT デバイスで構成されている場合に HTTPS プロキシ経由で IoT Hub に接続します。  
*  `tunnelOpen`  ダイレクトメソッドのコールバックを登録する 

## <a name="implement-the-iot-pnp-interface-for-video-analyzer"></a>Video Analyzer 用の IoT PnP インターフェイスを実装する

次の [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) モデルは、Video Analyzer に接続できるデバイスを示しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:azure:videoanalyzer:WebSocketTunneling;1",
  "@type": "Interface",
  "displayName": "Azure Video Analyzer Web Socket Tunneling",
  "description": "This interface enables media publishing to Azure Video Analyzer service from a RTSP compatible device which is located behind a firewall or NAT device.",
  "contents": [
    {
      "@type": "Command",
      "displayName": "Tunnel Open",
      "name": "tunnelOpen",
      "request": {
        "@type": "CommandPayload",
        "displayName": "Parameters",
        "name": "parameters",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "displayName": "Remote Endpoint",
              "description": "The remote endpoint for the web socket tunnel.",
              "name": "remoteEndpoint",
              "schema": "string"
            },
            {
              "displayName": "Remote Authorization Token",
              "description": "The bearer token for the web socket authentication.",
              "name": "remoteAuthorizationToken",
              "schema": "string"
            },
            {
              "displayName": "Local Port",
              "description": "The local port where web socket data should be tunneled to.",
              "name": "localPort",
              "schema": "integer"
            }
          ]
        }
      }
    }
  ]
}
```

IoT デバイスはダイレクト メソッド  `tunnelOpen` を登録します。ここでは、上記のように、要求の本文にパラメーター `remoteEndpoint`、`remoteAuthorizationToken`、および `localPort` が含まれます。

## <a name="implement-the-direct-method-tunnelopen"></a>ダイレクト メソッド `tunnelOpen` を実装する
`tunnelOpen` のダイレクトメソッドが Video Analyzer サービスによって呼び出された場合、アプリケーションでは次の操作を行う必要があります。

1. デバイスの利用可能な RTSP ポートを取得する
1. ダイレクト メソッドの呼び出しで指定された `localPort` の値と使用可能なポートを比較する
   * 一致が見つからない場合は **Badrequest** を返します (以下の「エラー応答」セクションを参照してください)
1. "(カメラの IP またはホスト名)" に対する TCP 接続 `localPort` を開きます。
   * 接続に失敗した場合は **Badrequest** を返します。
   * 注: ホスト名は通常 **localhos** です。
1. (デバイスで構成されている場合はプロキシを使用して) `remoteEndpoint` への web ソケット接続を開きます。
   * HTTP "Authorization" ヘッダーを "ベアラー (remoteAuthorizationToken)" として設定します。
   * ヘッダー "TunnelConnectionSource" を値 "Pnp デバイス" に設定します。
   * User-Agent を、実装を識別できる適切な値に設定します。 
      * たとえば、CPU のアーキテクチャ、OS、デバイスのモデルやメーカーを採用できます。
   * Web ソケット接続が成功した場合は 200 OK を返し、それ以外の場合は適切なエラーコードを返す
1. 応答を返す (ブロックしない)
1. IoT PnP デバイスの実装により、Websocket と RTSP サーバーの TCP 接続の間で双方向の TCP データの送信が開始されます。

Video Analyzer サービスはエラー発生時に `tunnelOpen` 要求を再試行します。そのため、アプリケーションで再試行する必要はありません。

### <a name="error-responses"></a>エラー応答
`tunnelOpen` 要求が失敗した場合、応答本文は次のようになります。

```
{
    "code": "<errorCode>", // Use HTTP status error codes
    "target": "<uri>", // The target URI experiencing the issue
    "message": "<Error message>",  // Short error message describing issue. Do not include end user identifiable information.
}
```
このようなエラー応答の例を次に示します。

* ローカル ポートを RTSP または RTSPS ポートとして使用することはできません {"code": "400", "target": "(カメラの IP またはホスト名): {localPort}", "message": "ローカルポートは使用できません"}
* タイムアウト/RTSP エンドポイントに接続できませんでした {"code": "400", "target": "(カメラの IP またはホスト名): {localPort}", "message": "エンドポイントに接続できませんでした"}
*   タイムアウト/WebSocket 接続試行のエラー応答 {"code": "{WebSocket 応答コード}", "target": "{remoteEndpoint}", "message": "{WebSocket エラー応答メッセージ}"}


## <a name="ingestion-to-video-analyzer"></a>Video Analyzer への取り込み
ビデオをキャプチャして Video Analyzer に記録するには、トンネリングが有効になっているパイプライン トポロジを作成する必要があります。 そのトポロジから、ライブ パイプラインを作成してアクティブ化する必要があります。 [このプロセスの手順については、こちらを参照してください。](use-remote-device-adapter.md#create-pipeline-topology-in-the-video-analyzer-service)

 
## <a name="example-implementation"></a>実装例
デバイスにアプリケーションを実装して、それを Video Analyzer に接続する場合は、videoanalyzerhelp@microsoft.com にお問い合わせください。

## <a name="see-also"></a>参照 

[IoT プラグ アンド プレイとは](../../../iot-develop/overview-iot-plug-and-play.md)
