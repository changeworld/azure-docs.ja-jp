<properties
 pageTitle="Azure IoT Hub SDK のリスト | Microsoft Azure"
 description="さまざまな Azure IoT Hub デバイスおよびサービス SDK に関する情報とリンク。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/13/2016"
 ms.author="dobett"/>

# IoT Hub SDK

この記事には、さまざまな [Microsoft Azure IoT SDK][] に関する情報と、追加リソースへのリンクが記載されています。

## IoT Hub デバイス SDK

Microsoft Azure IoT デバイス SDK には、Azure IoT Hub サービスに接続および管理されるデバイスやアプリケーションの作成を容易にするコードが含まれています。

次の IoT デバイス SDK は GitHub からダウンロードできます。

- [C 用 Azure IoT デバイス SDK][]。移植性と広範なプラットフォーム互換性を得るために、ANSI C (C99) で作成されています。
- [.NET 用 Azure IoT デバイス SDK][]
- [Java 用 Azure IoT デバイス SDK][]
- [Node.js 用 Azure IoT デバイス SDK][]
- [Python 2.7 用 Microsoft Azure IoT device SDK][]

### OS プラットフォームとハードウェアの互換性

特定のハードウェア デバイスとの互換性の詳細については、次の記事を参照してください。

- [OS プラットフォームとハードウェアの、デバイス SDK との互換性][OS Platforms and hardware compatibility]
- [Microsoft Azure Certified for IoT プログラム][]

## IoT Hub サービス SDK

Microsoft Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

次の IoT サービス SDK は GitHub からダウンロードできます。

- [Node.js 用 Azure IoT サービス SDK][]
- [Java 用 Azure IoT サービス SDK][]

## Azure IoT Gateway SDK

この Azure IoT Gateway SDK には、IoT ゲートウェイ ソリューションを作成するためのインフラストラクチャとモジュールが含まれています。SDK を拡張して、エンド ツー エンドのシナリオに合わせて調整されたゲートウェイを作成できます。

GitHub から [Azure IoT Gateway SDK][] をダウンロードできます。

## オンライン API リファレンス ドキュメント

Azure IoT デバイス、サービス、およびゲートウェイ ライブラリのオンライン API リファレンス ドキュメントへのリンクの一覧を次に示します。

- [Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)][]
- [IoT Hub REST][]
- [Microsoft Azure IoT device SDK for C (C 用 Microsoft Azure IoT デバイス SDK)][]
- [Microsoft Azure IoT device SDK for Java (Java 用 Microsoft Azure IoT デバイス SDK)][]
- [Microsoft Azure IoT service SDK for Java (Java 用 Microsoft Azure IoT サービス SDK)][]
- [Microsoft Azure IoT device SDK for Node.js (Node.js 用 Microsoft Azure IoT デバイス SDK)][]
- [Microsoft Azure IoT service SDK for Node.js (Node.js 用 Microsoft Azure IoT サービス SDK)][]
- [Microsoft Azure IoT Gateway SDK][]

## 次のステップ

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

[Microsoft Azure IoT SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[C 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[.NET 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[Java 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[Java 用 Azure IoT サービス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[Node.js 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[Node.js 用 Azure IoT サービス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[Python 2.7 用 Microsoft Azure IoT device SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[OS Platforms and hardware compatibility]: iot-hub-tested-configurations.md
[Microsoft Azure Certified for IoT プログラム]: iot-hub-tested-configurations.md#microsoft-azure-certified-for-iot
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)]: https://msdn.microsoft.com/library/mt488521.aspx
[Microsoft Azure IoT device SDK for C (C 用 Microsoft Azure IoT デバイス SDK)]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[Microsoft Azure IoT device SDK for Java (Java 用 Microsoft Azure IoT デバイス SDK)]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[Microsoft Azure IoT device SDK for Node.js (Node.js 用 Microsoft Azure IoT デバイス SDK)]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.14/index.html
[IoT Hub REST]: https://msdn.microsoft.com/library/mt548492.aspx
[Microsoft Azure IoT service SDK for Java (Java 用 Microsoft Azure IoT サービス SDK)]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[Microsoft Azure IoT service SDK for Node.js (Node.js 用 Microsoft Azure IoT サービス SDK)]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.16/index.html
[Microsoft Azure IoT gateway SDK]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->