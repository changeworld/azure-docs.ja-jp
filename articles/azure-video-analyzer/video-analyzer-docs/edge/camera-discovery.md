---
title: ローカル サブネットでの ONVIF 対応カメラの検出
description: ここでは、Video Analyzer エッジ モジュールを使用して、ローカル サブネットで ONVIF 対応カメラを検出する方法について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e0629294ba5d19315da096072fa804824649acc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564026"
---
# <a name="discovering-onvif-capable-cameras-in-the-local-subnet"></a>ローカル サブネットでの ONVIF 対応カメラの検出

[!INCLUDE [header](includes/edge-env.md)]

この攻略ガイドでは、Azure Video Analyzer エッジ モジュールを使用して、IoT Edge デバイスと同じサブネット上にある ONVIF 準拠のカメラを検出する方法について説明します。 Open Network Video Interface Forum (ONVIF) は、独立した IP ベースの物理デバイス (監視カメラなど) が、ネットワークに接続された追加のデバイスやソフトウェアと通信できるオープン スタンダードです。 ONVIF の詳細については、[ONVIF](https://www.onvif.org/about/mission/) の Web サイトを参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、以下を実行する必要があります。

- 有効な Azure サブスクリプションを持っている。
- [IoT 用の Azure CLI 拡張機能](https://github.com/Azure/azure-iot-cli-extension#installation)をインストールします。
- 次のいずれかを完了します。
  - [クイックスタート: Azure Video Analyzer で作業を開始する](get-started-detect-motion-emit-events.md)
  - [クイック スタート: Azure portal で Azure Video Analyzer の使用を開始する](get-started-detect-motion-emit-events-portal.md)
- IoT Edge デバイスに、Video Analyzer エッジ モジュール バージョン 1.1 (またはそれ以降) がデプロイされていること
- [Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/mt169373(v=ws.11)) 仮想マシンまたは [EFLOW](../../../iot-edge/how-to-install-iot-edge-on-windows.md?view=iotedge-2018-06&tabs=windowsadmincenter&preserve-view=true) を使用する場合、onvifDeviceDiscover ダイレクト メソッドの呼び出しを実行するには、[外部スイッチ](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)が必要です。 この要件は、ONVIF デバイス検出に使用される[マルチキャスト](https://en.wikipedia.org/wiki/Multicast)の呼び出しによるものです。

Video Analyzer エッジ モジュールの ONVIF 機能には、「[ONVIF 検出機能を有効にする](#enable-onvif-discovery-feature)」で説明されているように、特定のコンテナー作成オプションが必要です。 この ONVIF 検出機能では、ポート 3702 が使用可能である必要があります。
  
> [!NOTE]
> Video Analyzer エッジ モジュールのバージョン 1.1 が新しくデプロイされている場合は、「[ダイレクト メソッドの呼び出しを使用する](#use-direct-method-calls)」のセクションに進むことができます。 それ以外の場合は、次のセクションに従って、既存の Video Analyzer エッジ モジュールをアップグレードして、ONVIF 検出機能を有効にします。  

## <a name="check-the-version"></a>バージョンをチェックする

コマンド プロンプトで、次のコマンドを実行します。

```CLI
az account set --subscription <YOUR_SUBSCRIPTION_NAME>
az iot hub module-twin show -m <VIDEO_ANALYZER_IOT_EDGE_MODULE_NAME> -n <IOT_HUB_NAME> -d <IOT_EDGE_DEVICE_NAME> --query 'properties.reported.ProductInfo' -o tsv
```

上記のコマンドの結果が **Azure Video Analyzer:1.0.1** の場合は、次の手順を実行して、Video Analyzer エッジ モジュールをバージョン 1.1 に更新します。

1. Azure portal で、Video Analyzer エッジ モジュールのデプロイで使用される IoT Hub (上記の `<IOT_HUB_NAME>`) に移動します。
1. [自動デバイス管理] の **[IoT Edge]** をクリックし、Video Analyzer エッジ モジュールがデプロイされている IoT Edge デバイス (上記の `<IOT_EDGE_DEVICE_NAME>`) を選択します。
1. **[モジュールの設定]** をクリックし、 **[確認と作成]** をクリックします。
1. **[作成]** をクリックします。
1. 数分後に Video Analyzer エッジ モジュールが更新され、上記のコマンドをもう一度実行して確認できます。

### <a name="enable-onvif-discovery-feature"></a>ONVIF 検出機能を有効にする

次に、Video Analyzer エッジ モジュール用のコンテナー作成オプションにオプションを追加します。 これにより、ホスト ネットワークを介して通信して、同じサブネット上の ONVIF 対応カメラを検出することができます。

1. Azure portal で、IoT Hub に移動します。
1. [自動デバイス管理] の **[IoT Edge]** をクリックし、Video Analyzer エッジ モジュールがデプロイされている IoT Edge デバイスを選択します。
1. **[モジュールの設定]** をクリックし、Video Analyzer エッジ モジュールを選択します。
1. **[コンテナーの作成オプション]** タブに移動し、次の JSON エントリをそれぞれのセクションに追加します。

    - JSON エントリのボックスの最初の `{` の後に、次のように入力します。
  
        ```JSON
        "NetworkingConfig": {
        "EndpointsConfig": {
            "host": {}
            }
        },
        ```
    - `HostConfig` JSON オブジェクト:

        ```JSON
        "NetworkMode": "host",
        ```
1. 下部にある **[更新]** をクリックします。
1. **[確認と作成]** をクリックします。
1. **[作成]**

## <a name="use-direct-method-calls"></a>ダイレクト メソッドの呼び出しを使用する

Video Analyzer エッジ モジュールは、同じサブネット上のネットワーク接続カメラを検出するためのダイレクト メソッド呼び出しを提供します。 次の手順は、以下の `onvifDeviceDiscover` セクションと `onvifDeviceGet` セクションの両方に適用されます。

1. Azure portal で、IoT Hub に移動します。
1. [自動デバイス管理] の **[IoT Edge]** をクリックし、Video Analyzer エッジ モジュールがデプロイされている IoT Edge デバイスを選択します。
1. Video Analyzer エッジ モジュールをクリックし、上部にあるメニュー バーの **[ダイレクト メソッド]** をクリックします。

### <a name="onvifdevicediscover"></a>onvifDeviceDiscover

このダイレクト メソッドでは、Video Analyzer エッジ モジュールと同じサブネット上の検出可能なすべての ONVIF デバイスが一覧表示されます。


1. メソッド名に次のように入力します。

    ```JSON
    onvifDeviceDiscover
    ```
1. ペイロードで、次のように入力します。

    ```JSON
    {
        "@apiVersion":"1.1",
        "discoveryDuration":"PT8S"
    }
    ```

   検出期間は、Video Analyzer エッジ モジュールが ONVIF 検出可能デバイスからの応答の受信を待機する時間です。 これは、大規模な環境でこの値を調整するのに必要になることがあります。

1. 数秒以内に、次のような `result` が表示されます。

    ```JSON
    {
        "status": 200,
        "payload": {
            "value": [
                {
                    "serviceIdentifier": "{urn:uuid}",
                    "remoteIPAddress": "{IP_ADDRESS}",
                    "scopes": [
                        "onvif://www.onvif.org/type/Network_Video_Transmitter",
                        "onvif://www.onvif.org/name/{CAMERA_MANUFACTURE}",
                        "onvif://www.onvif.org/location/",
                        "onvif://www.onvif.org/hardware/{CAMERA_MODEL}",
                        "onvif://www.onvif.org/Profile/Streaming",
                        "onvif://www.onvif.org/Profile/G",
                        "onvif://www.onvif.org/Profile/T"
                    ],
                    "endpoints": [
                        "http://<IP_ADDRESS>/onvif/device_service",
                        "https://<IP_ADDRESS>/onvif/device_service"
                    ],
                    
                }
            ]
        }
    }
    ```
    リターン状態 200 は、ダイレクト メソッドの呼び出しが正常に処理されたことを示します。

### <a name="onvifdeviceget"></a>onvifDeviceGet

onvifDeviceGet 呼び出しは、セキュリティで保護されていないエンドポイントと TLS が有効なエンドポイントの両方をサポートします。 このダイレクト メソッド呼び出しは、特定の ONVIF デバイスに関する詳細情報を取得します。

# <a name="unsecuredendpoint"></a>[UnsecuredEndpoint](#tab/unsecuredendpoint)

> [!NOTE]
> ONVIF が有効なカメラで、セキュリティで保護されていないエンドポイントに onvifDeviceGet の呼び出しが行われた場合、Video Edge モジュール ID ツインの設定 `"allowUnsecuredEndpoints"` を `true` に設定する必要があります。 詳細については、[モジュール ツインのプロパティ](./module-twin-configuration-schema.md)に関する記事を参照してください。

1. メソッド名に次のように入力します。

    ```JSON
    onvifDeviceGet
    ```
1. ペイロードで、次のように入力します。

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "http://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }
    ```

# <a name="tlsendpoint"></a>[TlsEndpoint](#tab/tlsendpoint)

1. メソッド名に次のように入力します。

    ```JSON
    onvifDeviceGet
    ```
1. ペイロードで、次のように入力します。

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.TlsEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "https://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }

---

In the above payload:

- `url` is the IP address of the ONVIF device from which you wish to get additional details
- `username` is the user name that is used to authenticate with the device, which has permissions to the ONVIF features. Some devices require specific accounts to be created for this purpose.
- `password` is the password for that user name

    Within a few seconds you should see a result such as:

    ```JSON
    {
        "status": 200,
        "payload": {
            "hostname": {
                "fromDhcp": true,
                "hostname": "{NAME_OF_THE_ONVIF_DEVICE}"
            },
            "systemDateTime": {
                "type": "ntp",
                "time": "2021-09-28T03:05:05.000Z",
                "timeZone": "GMT"
            },
            "dns": {
                "fromDhcp": true,
                "ipv4Address": [
                    "{IP_ADDRESS}"
                ],
                "ipv6Address": []
            },
            "mediaProfiles": [
                {
                    "name": "{Profile1}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 3840,
                            "height": 2160
                        },
                        "rateControl": {
                            "bitRateLimit": 15600,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                },
                {
                    "name": "{Profile2}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 1280,
                            "height": 720
                        },
                        "rateControl": {
                            "bitRateLimit": 1900,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                }
            ]
        }
    }
    ```

### <a name="return-status-of-onvifdeviceget"></a>onvifDeviceGet のリターン状態

| Status | コード      | 意味/ソリューション                                                                                                                                                                                                            |
| ------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200    | Success   | ダイレクト メソッドの呼び出しが正常に完了しました。                                                                                                                                                                                |
| 400    | 正しくない要求  | 要求の形式が正しくありません   |
| 403    | Forbidden | 認証エラーのため、ダイレクト メソッドの呼び出しで、要求された情報を ONVIF デバイスから正常に取得できませんでした。 メッセージ本文のユーザー名およびパスワード、またはそのいずれかが正しいことを確認します。 |
| 500 | エラー | 原因不明のエラーが発生しました。  |
| 502    | 無効なゲートウェイ | アップストリーム サービスから無効な応答を受信しました。  |
| 504    | タイムアウト   | ONVIF デバイスの応答が受信される前に、ダイレクト メソッドの呼び出しが期限切れになりました。  |

## <a name="troubleshooting"></a>トラブルシューティング

ここでは、いくつかのトラブルシューティング手順について説明します。

- Azure portal の IoT Edge モジュール ダイレクト メソッド ブレードに、次のエラーが表示される場合: "An error prevented the operation from successfully completing. \(エラーが発生したため、操作が正常に完了しませんでした。\) The request failed with status code 504. \(要求に失敗しました。状態コードは 504 です。\)" (下図を参照):

    :::image type="content" source="./media/camera-discovery/five-zero-four-error.png" alt-text="504 エラーを示すスクリーンショット。":::

    上記のダイレクト メソッド呼び出しの `"discoveryDuration":"PT8S"` の設定が、`Connection Timeout` または `Method Timeout` の値よりも短いことを確認します。

    :::image type="content" source="./media/camera-discovery/five-zero-four-error-fix.png" alt-text="504 エラーの修正を示すスクリーンショット。":::

- ダイレクト メソッドの `Results` フィールドに "{"status":200,"payload":{"value":[]}} が表示される場合は、検出期間を長くすることが必要になることがあります。

    :::image type="content" source="./media/camera-discovery/result-status-two-hundred-null.png" alt-text="返されるメッセージが、ダイレクト メソッドの &quot;結果&quot; フィールドに表示されます":::

    `"discoveryDuration":"PTxS"` の時間の値 (x) をより大きな数値に調整します。 また、`Connection Timeout` 値と `Method Timeout` 値、またはそのいずれかを適宜調整します。
- `onvifDeviceGet` ダイレクト メソッド呼び出しでは、H.265 でエンコードされたメディア ストリームのメディア プロファイルは表示されません。
- ONVIF デバイスへの接続に使用されるユーザー アカウントに ONVIF カメラ機能へのアクセス許可が与えられていない場合は、リターン状態 403 を返す可能性があります。 一部の ONVIF 対応カメラでは、ONVIF デバイス情報を取得するためにユーザーを ONVIF セキュリティ設定に追加する必要があります。
- 現在、Video Analyzer エッジ モジュールで返せるのは、マルチキャスト経由で同じサブネット上で到達可能な、最大で 200 台の ONVIF 対応カメラです。 これには、ポート 3702 が使用可能であることも必要です。
- onvifDeviceGet が TLS エンドポイントで呼び出される場合は、エッジ デバイス上のホスト (/etc/hosts) ファイルに ONVIF デバイス名と IP アドレスを追加する必要があります。  TLS 暗号化に自己署名証明書を使用する場合は、証明書を IoT Edge デバイスの証明書ストアにも追加する必要があります。 
- onvifDeviceDiscover ダイレクト メソッドを EFLOW 仮想マシンから呼び出す場合は、次の手順を実行する必要があります。
  - EFLOW 仮想マシンに接続し、`sudo iptables -I INPUT -p udp -j ACCEPT` を実行します。

## <a name="next-steps"></a>次のステップ

- 検出された ONVIF デバイスを使用して、[ライブ ビデオを分析するためのクイックスタート](analyze-live-video-use-your-model-http.md)を試します。
