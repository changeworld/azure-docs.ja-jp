---
title: IoT プラグ アンド プレイ プレビュー モデル検出を実装する | Microsoft Docs
description: ソリューション ビルダーとして、IoT プラグ アンド プレイ モデル検出をソリューションに実装する方法について説明します。
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337383"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>IoT プラグ アンド プレイ プレビュー モデル検出を IoT ソリューションに実装する

この記事では、ソリューション ビルダーが IoT プラグ アンド プレイ プレビュー モデル検出を IoT ソリューションに実装する方法について説明します。 モデル検出では、次の方法について記述します。

- IoT プラグ アンド プレイ デバイスで、モデル ID を登録します。
- IoT ソリューションを使用して、デバイスに実装されているインターフェイスを取得します。

IoT ソリューションには、大きく分けて 2 つのカテゴリがあります。

- "*専用の IoT ソリューション*" は、既知の IoT プラグ アンド プレイ デバイス モデルのセットと連携して機能します。

- "*モデル駆動型 IoT ソリューション*" は、任意の IoT プラグ アンド プレイ デバイスで使用できます。 モデル駆動型ソリューションのビルドはより複雑ですが、今後追加されるすべてのデバイスでソリューションが動作するという利点があります。

    モデル駆動型 IoT ソリューションをビルドするには、IoT プラグ アンド プレイ インターフェイス プリミティブ (テレメトリ、プロパティ、コマンド) に対してロジックを構築する必要があります。 ソリューションのロジックは、複数のテレメトリ、プロパティ、コマンドの機能を組み合わせることによってデバイスを表します。

この記事では、両方の種類のソリューションでモデル検出を実装する方法について説明します。

## <a name="model-discovery"></a>モデル検出

デバイスに実装されているモデルを検出するには、ソリューションでイベントベースの検出またはツインベースの検出を使用することで、モデル ID を取得します。

### <a name="event-based-discovery"></a>イベントベースの検出

IoT プラグ アンド プレイ デバイスから IoT Hub に接続すると、実装されているモデルが登録されます。 この登録により、[Digital Twin 変更イベント](concepts-digital-twin.md#digital-twin-change-events)通知が生成されます。 デジタル ツイン イベントのルーティングを有効にする方法については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。

このソリューションでは、次のスニペットに示すイベントを使用して、接続している IoT プラグ アンド プレイ デバイスについて把握し、そのモデル ID を取得することができます。

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

このイベントは、デバイス モデル ID が追加または更新されるとトリガーされます。

### <a name="twin-based-discovery"></a>ツインベースの検出

ソリューションで特定のデバイスの機能について把握するには、[Get Digital Twin](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) API を使用して情報を取得することができます。

次のデジタル ツイン スニペットでは、`$metadata.$model` に IoT プラグ アンド プレイ デバイスのモデル ID が含まれています。

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

次のスニペットに示すように、ソリューションで **Get Twin** を使用して、デバイス ツインからモデル ID を取得することもできます。

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>モデルの解決

ソリューションでは、モデルの解決を使用して、モデル ID からモデルを構成するインターフェイスにアクセスします。 

- ソリューションでは、これらのインターフェイスをファイルとしてローカル フォルダーに保存することを選択できます。 
- ソリューションには[モデル リポジトリ](concepts-model-repository.md)を使用できます。

## <a name="next-steps"></a>次のステップ

ここまでで IoT ソリューションのモデル検出について学びました。次は [Azure IoT プラットフォーム](overview-iot-plug-and-play.md)の詳細について学び、別の機能をソリューションに使用してみてください。

- [ソリューションからのデバイスの操作](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT エクスプローラー](howto-use-iot-explorer.md)
