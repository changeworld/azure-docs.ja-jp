---
title: Azure IoT Hub - クラウドへの IoT デバイス接続入門 |Microsoft Docs
description: IoT ボードとスタート キットを Azure IoT Hub に接続する方法について説明します。 デバイスは IoT Hub にテレメトリを送信でき、IoT Hub はデバイスを監視および管理することができます。
author: dominicbetts
manager: timlt
keywords: Azure IoT Hub チュートリアル
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125194"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>実際のデバイスでの Azure IoT Hub の使用

これらのハウツー記事では、Azure IoT Hub と、実際のデバイスでのデバイス SDK の実行について説明します。

## <a name="set-up-your-device"></a>デバイスのセットアップ

IoT デバイスまたはゲートウェイを Azure IoT Hub に接続します。

| IoT デバイス                       | プログラミング言語 |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py]、[Node.js][Pi_Nd]、[C][Pi_C]  |
| IoT DevKit                       | [VSCode の Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd][C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| オンライン デバイス シミュレーター         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
