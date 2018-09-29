---
title: Azure IoT Hub への物理デバイス接続入門 |Microsoft Docs
description: 物理デバイスとボードを Azure IoT Hub に接続する方法を説明します。 デバイスは IoT Hub にテレメトリを送信でき、IoT Hub はデバイスを監視および管理することができます。
author: wesmc7777
manager: timlt
keywords: Azure IoT Hub チュートリアル
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: 5dcd588d525fe296348bd01252846bc498261018
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394745"
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Azure IoT Hub と物理デバイス入門チュートリアル

以下のチュートリアルでは、Azure IoT Hub と Device SDK について紹介します。 このチュートリアルでは、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、チュートリアルでは、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 次の表に示すチュートリアルでは、物理 IoT デバイスを作成する方法を説明します。

| IoT デバイス                       | プログラミング言語 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd][C][Pi_C]  |
| IoT DevKit                      | [VSCode の Arduino][DevKit]     |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
