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
 ms.date="01/20/2016"
 ms.author="elioda"/>

# IoT Hub のスケーリング

IoT Hub では、IoT Hub S1 または S2 ユニット数を 2.000 に増やすことで、最大 100 万の同時接続デバイスをサポートできます。詳細については、[IoT Hub の価格][lnk-pricing]に関するページを参照してください。

各 IoT Hub ユニットはレジストリ内で許可される特定の数のデバイスを決定します。これらのデバイスのすべてを同時に接続できます。各ユニットは、許可される毎日のメッセージの数も決定します。

ソリューションを適切に調整するには、IoT Hub の特定の用途を考慮します。具体的には、次の操作カテゴリに対して必要なピーク スループットを検討してください。

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
| S2 | ユニットあたり最大 16 MB/分<br/>(22.8 GB/日/ユニット) | ユニットあたり平均 4167 メッセージ/分<br/>(ユニットあたり 6,000,000 メッセージ/日) |

クラウドからデバイスへのメッセージのパフォーマンスはデバイスごとに調整され、デバイスごとに 1 分あたり最大 5 つのメッセージを受信します。

## ID レジストリ操作のスループット

IoT Hub の ID レジストリの操作は、ほとんどがデバイス プロビジョニングに関連しているため、ランタイム操作にはなりません。

特定のバースト パフォーマンスの数値については、[IoT Hub のクォータとスロットル][]に関するページを参照してください。

## シャーディング

1 つの IoT Hub を数百万のデバイスに拡張できますが、使用しているソリューションに、1 つの IoT Hub では保証できない特定のパフォーマンス特性が必要になる場合があります。その場合は、デバイスを複数の IoT hub にパーティション分割することをお勧めします。複数の IoT hub は、トラフィックの急増を抑え、必要なスループットまたは必要とされる操作の速度を取得します。

## 次のステップ

Azure IoT Hub についてさらに学習するには、次のリンクを使用してください。

- [IoT Hub の使用 (チュートリアル)][lnk-get-started]
- [What is Azure IoT Hub? (Azure IoT Hub とは)][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub のクォータとスロットル]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub? (Azure IoT Hub とは)]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0128_2016-->