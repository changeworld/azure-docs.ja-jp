---
title: Azure IoT Hub への物理デバイス接続入門 |Microsoft Docs
description: 物理デバイスとボードを Azure IoT Hub に接続する方法を説明します。 デバイスは IoT Hub にテレメトリを送信でき、IoT Hub はデバイスを監視および管理することができます。
author: dominicbetts
manager: timlt
keywords: Azure IoT Hub チュートリアル
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: e7911c190ded59f758eff868add6440f5add6579
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633997"
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Azure IoT Hub と物理デバイス入門チュートリアル

以下のチュートリアルでは、Azure IoT Hub と Device SDK について紹介します。 このチュートリアルでは、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、チュートリアルでは、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 次の表に示すチュートリアルでは、物理 IoT デバイスを作成する方法を説明します。

| IoT デバイス                       | プログラミング言語 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Python][Pi_Py]、[Node.js][Pi_Nd]、[C][Pi_C]  |
| IoT DevKit                      | [VSCode の Arduino][DevKit]     |
| Intel Edison                    | [Node.js][Ed_Nd][C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

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
