<properties
 pageTitle="Microsoft Azure IoT Hub およびモノのインターネット (IoT) | Microsoft Azure"
 description="サンプル ソリューション アーキテクチャや、それが、Azure IoT Hub、デバイス SDK、および構成済みソリューションとどのように関連するかなど、Azure での IoT の概要。"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## このドキュメントの範囲

Azure および IoT のこれら記事では、Microsoft IoT プラットフォームに基づいて独自の IoT ソリューションを実装するときに役立つ可能性のある 2 種類のリソース コレクションに焦点が当てられています。

- Azure IoT Hub
- Azure IoT デバイス SDK

リモート モニタリング、資産管理、および予測メンテナンスなど、一般的な IoT シナリオに対処するために IoT プロジェクトをすばやく開始し、拡張可能な構成済みのソリューションのコレクションである [Azure IoT Suite][lnk-iot-suite] もご確認ください。

### Azure IoT Hub

IoT Hub は Azure サービスの 1 つであり、これを使用してデバイスからクラウドへのデータをデバイスから規模を拡大して受信し、そのデータをストリーム イベント プロセッサにルーティングできます。IoT Hub は、デバイス固有のキューを使用して特定のデバイスにクラウドからのコマンドを送信することもできます。

また、IoT Hub サービスにはデバイスをプロビジョニングしたり、どのデバイスが IoT ハブに接続できるかを管理したりするのに役立つデバイス ID レジストリが含まれています。

### Azure IoT デバイス SDK

Microsoft では、クライアント アプリケーションを実装して各種デバイス ハードウェア プラットフォームやオペレーティング システムで実行できるようにする IoT デバイス SDK を提供しています。IoT デバイス SDK には、デバイスからクラウドへのテレメトリ データを IoT Hub に送信し、クラウドからデバイスへのコマンドを IoT Hub から受信する操作を容易にするライブラリが含まれています。これらの IoT デバイス SDK を使用して、多数のさまざまなネットワーク プロトコルから選択して Azure IoT Hub と通信することができます。

## 次のステップ

Azure で IoT を開始するには、次のリソースを参照してください。

- [Get started with IoT Hub (IoT Hub の概要)][lnk-getstarted]。
- [Azure IoT Hub][lnk-iot-hub]。
- [Azure IoT Suite][lnk-iot-suite]。  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/

<!---HONumber=Oct15_HO2-->