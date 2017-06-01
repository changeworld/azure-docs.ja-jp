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
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 64b09448149b7e1efd8b76663a27b6c7ec1eb3c7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT Hub 入門チュートリアル

モ ノのインターネット (IoT) ソリューションを構築するには、Azure IoT Hub および Azure IoT device SDK を使用します。

* Azure IoT Hub は、クラウド内で完全に管理されたサービスで、IoT デバイスを安全に接続し、監視し、管理します。 Azure IoT Device SDK を使用して、IoT デバイスを実装します。
* レガシ デバイス、帯域幅のコスト、セキュリティ ポリシーおよびプライバシー ポリシー、エッジ データ処理などの要因を考慮する必要がある複雑な IoT シナリオでは、IoT ゲートウェイを使用します。 これらのシナリオでは、Azure IoT Edge を使用して、IoT Hub にデバイスを接続するゲートウェイを作成します。

## <a name="what-the-tutorials-cover"></a>チュートリアルの内容

以下のチュートリアルでは、Azure IoT Hub と Device SDK について紹介します。 このチュートリアルでは、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、チュートリアルでは、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 チュートリアルでは、シミュレートされた IoT デバイスまたは実際の IoT デバイスのいずれかを選択できます。 また、ゲートウェイを使用して IoT hub にデバイスを接続できるようにする方法についても説明します。

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>デバイスのセットアップ シナリオ: Azure IoT Hub への IoT デバイスまたはゲートウェイの接続

実際のデバイスまたはシミュレートされたデバイスを選択して、開始します。

| IoT デバイス                       | プログラミング言語 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd][C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd][C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| シミュレートされたデバイス                | [.NET][Sim_NET]、[Java][Sim_Jav]、[Node.js][Sim_Nd]、[Python][Sim_Pyth]              |

また、IoT Edge ゲートウェイを使用すると、IoT hub にデバイスを接続できます。

| ゲートウェイ デバイス               | プログラミング言語 | プラットフォーム         |
|------------------------------|----------------------|------------------|
| Intel NUC (モデル: DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| シミュレートされたゲートウェイ            | C                    | [Linux][Sim_Lnx]、[Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>拡張 IoT シナリオ: その他の Azure サービスおよびツールの使用

IoT Hub にデバイスを接続すると、他の Azure ツールおよびサービスを使用する追加のシナリオを検証できます。

| シナリオ                                    | Azure サービスまたは Azure ツール              |
|---------------------------------------------|------------------------------------|
| [IoT Hub メッセージの管理][Mg_IoT_Hub_Msg]                    | iothub-explorer ツール               |
| [IoT デバイスの管理][Mg_IoT_Dv]               | iothub-explorer ツール               |
| [Azure Storage への IoT Hub メッセージの保存][Sv_IoT_Msg_Stor]                      | Azure テーブル ストレージ               |
| [センサー データの視覚化][Vis_Data]             | Microsoft Power BI、Azure Web Apps |
| [センサー データによる気象予測][Weather_Forecast] | Azure Machine Learning             |
| [異常自動検出および対応][Anomaly_Detect]    | Azure Logic Apps                   |

## <a name="next-steps"></a>次のステップ

このチュートリアルを完了したあとは、「[開発者ガイド][lnk-dev-guide]」で IoT Hub の機能の詳細を確認できます。 その他のチュートリアルは、[使用方法] [lnk-how-to]についてのセクションにあります。


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
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
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
