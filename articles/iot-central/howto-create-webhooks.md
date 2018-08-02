---
title: Azure IoT Central でルールに対する Webhook を作成する | Microsoft Docs
description: Azure IoT Central でルールが作動したときに他のアプリケーションに自動的に通知する Webhook を作成します。
author: viv-liu
ms.author: viviali
ms.date: 07/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1e21076cafe21e6c0efcdf5a8146278eabd9ebc4
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227834"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central でルールに対する Webhook アクションを作成する

Webhook を使用すると、IoT Central アプリを他のアプリケーションやサービスに接続して、リモート監視や通知を行うことができます。 IoT Central アプリ内でルールがトリガーされるたびに、Webhook は、接続されている他のアプリケーションやサービスに対して自動的に通知を行います。 ルールがトリガーされるたびに、IoT Central アプリから他のアプリケーションの HTTP エンドポイントに POST 要求が送信されます。 ペイロードには、デバイスの詳細とルールのトリガーの詳細が含まれます。 

## <a name="how-to-set-up-the-webhook"></a>Webhook を設定する方法
この例では RequestBin に接続し、ルールが作動したときに、Webhook を使用して通知が届くようにします。 

1. [RequestBin](http://requestbin.net/) を開きます。 
1. 新しい RequestBin を作成し、**Bin URL** をコピーします。 
1. [テレメトリ ルール](howto-create-telemetry-rules.md)または[イベント ルール](howto-create-event-rules.md)を作成します。 ルールを保存し、新しいアクションを追加します。
![Webhook の作成画面](media/howto-create-webhooks/webhookcreate.png)
1. Webhook アクションを選択し、表示名を指定して、Bin URL をコールバック URL として貼り付けます。 
1. ルールを保存します。

ルールが作動すると、新しい要求が RequestBin に表示されます。

## <a name="payload"></a>ペイロード
ルールがトリガーされると、測定値、デバイス、ルール、アプリケーションの詳細に関する JSON ペイロードを含んだ HTTP POST 要求がコールバック URL に対して行われます。 テレメトリ ルールの場合、このペイロードは次のような内容になります。

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>既知の制限事項
現在、Webhook の登録/登録解除をプログラムから API を使って行う方法はありません。

この機能を改良する方法についてアイデアがありましたら、[UserVoice フォーラム](https://feedback.azure.com/forums/911455-azure-iot-central)でご提案ください。

## <a name="next-steps"></a>次の手順
Webhook を設定して使用する方法がわかったら、[Microsoft Flow でワークフローを構築する方法](howto-add-microsoft-flow.md)について詳しく調べることをお勧めします。
