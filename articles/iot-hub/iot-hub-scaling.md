<properties
 pageTitle="Azure IoT Hub のスケーリング | Microsoft Azure"
 description="Azure IoT Hub のスケーリングについて説明します。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/19/2016"
 ms.author="elioda"/>

# IoT Hub のスケーリング

Azure IoT Hub は、100 万台のデバイスの同時接続をサポートできます。詳細については、[IoT Hub の価格][lnk-pricing]に関するページを参照してください。各 IoT Hub ユニットでは、毎日一定数のメッセージが許可されます。

ソリューションを適切に調整するには、IoT Hub の個別の用途を考慮します。具体的には、次の操作カテゴリに対して必要なピーク スループットを検討してください。

* デバイスからクラウドへのメッセージ
* クラウドからデバイスへのメッセージ
* ID レジストリの操作

このスループット情報に加え、「[IoT Hub のクォータとスロットル][]に関するページを参照して、適切にソリューションを設計してください。

## デバイスからクラウドおよびクラウドからデバイスへのメッセージのスループット

IoT Hub ソリューションのサイズを設定する方法として最適なのは、ユニットごとにトラフィックを評価することです。

デバイスからクラウドへのメッセージは、次の持続スループット ガイドラインに従います。

| レベル | 持続スループット | 持続送信レート |
| ---- | -------------------- | ------------------- |
| S1 | ユニットあたり最大 1111 KB/分<br/>(1.5 GB/日/ユニット) | ユニットあたり平均 278 メッセージ/分<br/>(ユニットあたり 400,000 メッセージ/日) |
| S2 | ユニットあたり最大 16 MB/分<br/>(22.8 GB/日/ユニット) | ユニットあたり平均 4,167 メッセージ/分<br/>(ユニットあたり 600 万メッセージ/日) |
| S3 | ユニットあたり最大 814 MB/分<br/>(1144.4 GB/日/ユニット) | ユニットあたり平均 208,333 メッセージ/分<br/>(ユニットあたり 3 億件のメッセージ/日) |

## ID レジストリ操作のスループット

IoT Hub の ID レジストリの操作は、ほとんどがデバイス プロビジョニングに関連しているため、ランタイム操作にはなりません。

特定のバースト パフォーマンスの数値については、[IoT Hub のクォータとスロットル][]に関するページを参照してください。

## シャーディング

1 つの IoT Hub を数百万のデバイスに拡張できますが、使用しているソリューションに、1 つの IoT Hub では保証できない特定のパフォーマンス特性が必要になる場合があります。その場合は、デバイスを複数の IoT hub にパーティション分割することをお勧めします。複数の IoT hub は、トラフィックの急増を抑え、必要なスループットまたは必要とされる操作の速度を取得します。

## 次のステップ

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub のクォータとスロットル]: iot-hub-devguide.md#throttling

[lnk-design]: iot-hub-guidance.md
[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0921_2016-->