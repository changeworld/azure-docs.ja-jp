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

Azure IoT Hub は Azure サービスの 1 つであり、デバイスからのテレメトリを規模を拡大して受信し、そのデータをストリーム イベント プロセッサにルーティングします。IoT Hub を使用して、独自のソリューション バックエンドを実装できます。IoT Hub は、クラウドからデバイスへのコマンドを特定のデバイスに送信することもできます。また、IoT Hub には、デバイスをプロビジョニングし、どのデバイスがハブに接続できるかを管理するために使用できるデバイス ID レジストリが含まれています。詳細については、次を参照してください。

- [IoT Hub とは][lnk-iot-hub]
- [IoT Hub の概要][lnk-getstarted]

クライアント アプリケーションを各種デバイス ハードウェア プラットフォームやオペレーティング システムに実装するために、IoT デバイス SDK を使用できます。IoT デバイス SDK には、テレメトリを IoT Hub に送信し、クラウドからデバイスへのコマンドを受信する操作を容易にするライブラリが含まれています。これらの SDK を使用すると、多数のネットワーク プロトコルのうちのいずれかを選択して IoT Hub と通信することができます。詳細については、[デバイス SDK に関する情報][lnk-device-sdks]を参照してください。

構成済みのソリューションのコレクションである [Azure IoT Suite][lnk-iot-suite] もご確認ください。IoT Suite を使用すると、リモート モニタリング、資産管理、予測メンテナンスなど、一般的な IoT シナリオに対処するための IoT プロジェクトをすばやく開始してスケーリングできます。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->