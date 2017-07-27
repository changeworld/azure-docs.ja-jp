---
title: "Azure IoT Hub - クラウドへの IoT デバイス接続入門 |Microsoft Docs"
description: "IoT デバイスを Azure IoT Hub に接続する方法について説明します。 デバイスは IoT Hub にテレメトリを送信し、Iot Hub はデバイスを監視および管理することができます。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT Hub チュートリアル"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: d3d74d44bdd7ce6b49ec58b70c7ddb8081e11f96
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT Hub 入門チュートリアル

モ ノのインターネット (IoT) ソリューションを構築するには、Azure IoT Hub および Azure IoT device SDK を使用します。

* Azure IoT Hub は、クラウド内で完全に管理されたサービスで、IoT デバイスを安全に接続し、監視し、管理します。 Azure IoT Device SDK を使用して、IoT デバイスを実装します。
* レガシ デバイス、帯域幅のコスト、セキュリティ ポリシーおよびプライバシー ポリシー、エッジ データ処理などの要因を考慮する必要がある複雑な IoT シナリオでは、IoT ゲートウェイを使用します。 これらのシナリオでは、Azure IoT Edge を使用して、IoT Hub にデバイスを接続するゲートウェイを作成します。

## <a name="what-the-tutorials-cover"></a>チュートリアルの内容

以下のチュートリアルでは、Azure IoT Hub と Device SDK について紹介します。 このチュートリアルでは、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、チュートリアルでは、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 チュートリアルでは、シミュレートされた IoT デバイスまたは実際の IoT デバイスのいずれかを選択できます。 また、ゲートウェイを使用して IoT hub にデバイスを接続できるようにする方法についても説明します。

## <a name="setup-your-device-connect-iot-device-or-gateway-to-azure-iot-hub"></a>デバイスのセットアップ: Azure IoT Hub への IoT デバイスまたはゲートウェイの接続

実際のデバイスまたはシミュレートされたデバイスを選択して、開始します。

| IoT デバイス                       | プログラミング言語 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd]、[C][Pi_C]、[Python][Pi_Py]           |
| Intel Edison                    | [Node.js][Ed_Nd][C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| PC のシミュレート デバイス          | [.NET][Sim_NET]、[Java][Sim_Jav]、[Node.js][Sim_Nd]、[Python][Sim_Pyth]              |
| オンライン デバイス シミュレーター         | [Raspberry Pi (Node.js)][Ol_Sim] |

また、IoT Edge ゲートウェイを使用すると、IoT hub にデバイスを接続できます。

| ゲートウェイ デバイス               | プログラミング言語 | プラットフォーム         |
|------------------------------|----------------------|------------------|
| Intel NUC (モデル: DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| シミュレートされたゲートウェイ            | C                    | [Linux][Sim_Lnx]、[Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-node-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md

