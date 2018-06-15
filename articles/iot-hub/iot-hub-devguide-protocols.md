---
title: Azure IoT Hub の通信プロトコルとポート | Microsoft Docs
description: 開発者ガイド - サポートされている device-to-cloud と cloud-to-device 通信の通信プロトコルと、開く必要があるポート番号について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 0fe3dd719877dac23410ff1ca00d559636a5ed60
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633004"
---
# <a name="reference---choose-a-communication-protocol"></a>リファレンス - 通信プロトコルの選択

IoT Hub によって、デバイスはデバイス側の通信に次のプロトコルを使うことができるようになります。

* [MQTT][lnk-mqtt]
* WebSocket 経由の MQTT
* [AMQP][lnk-amqp]
* AMQP over WebSocket
* HTTPS

これらのプロトコルがサポートしている具体的な IoT Hub 機能については、「[device-to-cloud 通信に関するガイダンス][lnk-d2c-guidance]」と「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」をご覧ください。

プロトコル選択に関する高度な推奨事項には次のようなものがあります。

| プロトコル | そのプロトコルを選択しなければならない場合 |
| --- | --- |
| MQTT <br> WebSocket 経由の MQTT |同じ TLS 接続で (それぞれがデバイス独自の資格情報を有する) 複数のデバイスに接続する必要がないすべてのデバイスで使用します。 |
| AMQP <br> WebSocket 経由の AMQP |デバイスを多重化する接続を活用するために、フィールド ゲートウェイとクラウド ゲートウェイで使用します。 |
| HTTPS |その他のプロトコルをサポートできないデバイスに使用します。 |

デバイス側の通信用プロトコルを選択する場合、次の点を考慮してください。

* **C2D のパターン**。 HTTPS には、サーバー プッシュを実装する効率的な方法がありません。 そのため、HTTPS を使うと、デバイスは IoT Hub に対して C2D メッセージのポーリングを行います。 この方法は、デバイスと IoT Hub の両方で非効率的です。 現在の HTTPS ガイドラインでは、各デバイスによるメッセージのポーリングの間隔は 25 分以上となっています。 MQTT と AMQP では、C2D メッセージを受信する場合のサーバー プッシュがサポートされています。 IoT Hub からデバイスへのメッセージも即座にプッシュできます。 配信の待機時間が問題となる場合は、MQTT または AMQP が使用に最適なプロトコルです。 頻繁に接続されないデバイスの場合は、HTTPS でも対応できます。
* **フィールド ゲートウェイ**。 MQTT と HTTPS を使う場合は、同じ TLS 接続で (それぞれがデバイス独自の資格情報を有する) 複数のデバイスに接続することはできません。 [フィールド ゲートウェイ シナリオ][lnk-azure-gateway-guidance]の場合、接続するデバイスごとにフィールド ゲートウェイと IoT Hub の間に TLS 接続が 1 つ必要となるため、次善のプロトコルになります。
* **リソースの少ないデバイス**。 MQTT および HTTPS ライブラリのフットプリントは、AMQP ライブラリの場合より小さくなります。 そのため、リソースが限られたデバイス (RAM が 1 MB 未満など) の場合、MQTT と HTTP/1 が実装可能な唯一のプロトコルとなります。
* **ネットワーク トラバーサル**。 標準の AMQP プロトコルはポート 5671 を使用し、MQTT はポート 8883 をリッスンします。 これらのポートを使うと、ネットワークが HTTPS 以外のプロトコルに限定されている場合、問題が発生する可能性があります。 このようなシナリオの場合は、MQTT over WebSockets、AMQP over WebSockets、または HTTPS を使ってください。
* **ペイロードのサイズ**。 MQTT と AMQP はバイナリ プロトコルであり、HTTPS よりもペイロードが小さくなります。

> [!WARNING]
> HTTPS を使う場合、各デバイスでは 25 分以上の間隔で C2D メッセージをポーリングする必要があります。 ただし、開発段階では、ポーリングの頻度を 25 分より短く設定することができます。

## <a name="port-numbers"></a>ポート番号

デバイスは、さまざまなプロトコルを使用して Azure の IoT Hub と通信できます。 通常、プロトコルの選択は、ソリューションの特定の要件によって左右されます。 次の表に、デバイスが特定のプロトコルを使用できるようにするために開く必要がある送信ポートを示します。

| プロトコル | ポート |
| --- | --- |
| MQTT |8883 |
| WebSocket 経由の MQTT |443 |
| AMQP |5671 |
| AMQP over WebSocket |443 |
| HTTPS |443 |

Azure リージョンに IoT Hub を作成すると、その IoT Hub は IoT Hub の存続期間中同じ IP アドレスを保持します。 ただし、サービス品質を維持するため、Microsoft によって IoT Hub が別のスケール ユニットに移動されると、新しい IP アドレスが割り当てられます。


## <a name="next-steps"></a>次の手順

IoT Hub による MQTT プロトコルの実装方法の詳細については、「[MQTT プロトコルを使用した IoT Hub との通信][lnk-mqtt-support]」をご覧ください。

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
