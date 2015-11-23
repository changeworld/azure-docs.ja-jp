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
 ms.date="10/01/2015"
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

### OS プラットフォームとハードウェアの互換性

この[ドキュメント][OS Platforms and hardware compatibility]では、デバイス SDK とさまざまな OS プラットフォームの互換性と、[Microsoft Azure Certified for IoT プログラム][]に含まれている特定のデバイス構成について説明します。

## IoT Hub サービス SDK

Microsoft Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

次の IoT サービス SDK は GitHub からダウンロードできます。

- [Node.js 用 Azure IoT サービス SDK][]

## オンライン API リファレンス ドキュメント

Azure IoT デバイス ライブラリのオンライン API リファレンス ドキュメントへのリンクの一覧を次に示します。

- [Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)][]
- [Microsoft Azure IoT device SDK for C (C 用 Microsoft Azure IoT デバイス SDK)][]
- [Microsoft Azure IoT device SDK for Java (Java 用 Microsoft Azure IoT デバイス SDK)][]
- [Microsoft Azure IoT device SDK for Node.js (Node.js 用 Microsoft Azure IoT デバイス SDK)][]

Azure IoT サービス ライブラリのオンライン API リファレンス ドキュメントへのリンクの一覧を次に示します。

- [Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)][]
- [IoT Hub REST][]


[Microsoft Azure IoT SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[C 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[.NET 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[Java 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[Node.js 用 Azure IoT デバイス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[Node.js 用 Azure IoT サービス SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/
[OS Platforms and hardware compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Microsoft Azure Certified for IoT プログラム]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md#certified

[Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)]: https://msdn.microsoft.com/library/mt488521.aspx
[Microsoft Azure IoT device SDK for C (C 用 Microsoft Azure IoT デバイス SDK)]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[Microsoft Azure IoT device SDK for Java (Java 用 Microsoft Azure IoT デバイス SDK)]: http://azure.github.io/azure-iot-sdks/java/api_reference/index.html
[Microsoft Azure IoT device SDK for Node.js (Node.js 用 Microsoft Azure IoT デバイス SDK)]: http://azure.github.io/azure-iot-sdks/node/api_reference/index.html
[IoT Hub REST]: https://msdn.microsoft.com/library/mt548492.aspx

<!---HONumber=Nov15_HO3-->