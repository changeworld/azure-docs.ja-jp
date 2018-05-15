---
title: Azure IoT Hub - クラウドへの IoT デバイス接続入門 |Microsoft Docs
description: IoT ボードとスタート キットを Azure IoT Hub に接続する方法について説明します。 デバイスは IoT Hub にテレメトリを送信でき、IoT Hub はデバイスを監視および管理することができます。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
keywords: Azure IoT Hub チュートリアル
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dafb8aca34a5a41f45f76d526aa3b8f3b1b792c4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>実際のデバイスでの Azure IoT Hub の使用

モノのインターネット (IoT) ソリューションを構築するには、Azure IoT Hub および Azure IoT device SDK を使用します。

* Azure IoT Hub は、クラウド内で完全に管理されたサービスで、IoT デバイスを安全に接続し、監視し、管理します。 Azure IoT Device SDK を使用して、IoT デバイスを実装します。
* IoT のより複雑なシナリオでは、IoT ゲートウェイを使用します。 レガシ デバイス、帯域幅のコスト、セキュリティ ポリシーおよびプライバシー ポリシー、エッジ データ処理などの要因を考慮する必要がある場合などです。 これらのシナリオでは、[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) を使用して、IoT Hub にデバイスを接続するゲートウェイを作成します。

## <a name="what-the-how-to-articles-cover"></a>ハウツー記事の内容

以下の記事では、Azure IoT Hub および Azure IoT device SDK について紹介します。 これらの記事では、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 記事で使用しているのは、実際の IoT デバイスです。

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
