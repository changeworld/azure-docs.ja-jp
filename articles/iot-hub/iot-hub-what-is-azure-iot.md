<properties
 pageTitle="モノのインターネット用の Azure ソリューション | Microsoft Azure"
 description="サンプル ソリューション アーキテクチャや、それが、Azure IoT Hub、デバイス SDK、および構成済みソリューションとどのように関連するかなど、Azure での IoT の概要。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## 次のステップ

Azure IoT Hub を使用して独自のソリューション バックエンドの実装が行えます。Azure IoT Hub は、テレメトリをデバイスから大量に受信し、そのデータをストリーム イベント プロセッサにルーティングする Azure のサービスです。IoT Hub は、特定のデバイスにクラウドからのコマンドを送信することもできます。また、IoT Hub には、デバイスをプロビジョニングしたり、どのデバイスがハブに接続できるかを管理するために使用できるデバイス ID レジストリが含まれています。詳細については、次を参照してください。

- [IoT Hub とは][lnk-iot-hub]
- [IoT Hub の概要][lnk-getstarted]

クライアント アプリケーションを各種デバイス ハードウェア プラットフォームやオペレーティング システムに実装するために、IoT デバイス SDK を使用できます。IoT デバイス SDK には、テレメトリを IoT Hub に送信し、クラウドからデバイスへのコマンドを受信する操作を容易にするライブラリが含まれています。これらの SDK を使用すると、いくつかのネットワーク プロトコルのうちのいずれかを選択して IoT Hub と通信することができます。詳細については、「[デバイス SDK とは][lnk-device-sdks]」を参照してください。

リモート監視、資産管理、予測メンテナンスなど、一般的な IoT シナリオに対処するために IoT プロジェクトをすばやく開始し、拡張可能な構成済みのソリューションのコレクションである [Azure IoT Suite][lnk-iot-suite] もご確認ください。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Nov15_HO3-->