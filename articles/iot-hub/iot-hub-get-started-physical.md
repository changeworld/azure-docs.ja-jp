---
title: "Azure IoT Hub への物理デバイス接続入門 |Microsoft Docs"
description: "物理デバイスとボードを Azure IoT Hub に接続する方法を説明します。 デバイスは IoT Hub にテレメトリを送信でき、IoT Hub はデバイスを監視および管理することができます。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT Hub チュートリアル"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: dce80d060538adc061149f686b71992a09d190da
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Azure IoT Hub と物理デバイス入門チュートリアル

以下のチュートリアルでは、Azure IoT Hub と Device SDK について紹介します。 このチュートリアルでは、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、チュートリアルでは、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 次の表に示すチュートリアルでは、物理 IoT デバイスを作成する方法を説明します。

| IoT デバイス                       | プログラミング言語 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd][C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd][C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

また、IoT Edge ゲートウェイを使用すると、IoT hub にデバイスを接続できます。

| ゲートウェイ デバイス               | プログラミング言語 | プラットフォーム         |
|------------------------------|----------------------|------------------|
| Intel NUC (モデル: DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

