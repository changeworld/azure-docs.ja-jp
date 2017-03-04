---
title: "Azure IoT SDK について | Microsoft Docs"
description: "開発者ガイド - デバイス アプリやバックエンド アプリを構築するために使用できる、さまざまな Azure IoT device SDK およびサービス SDK に関する情報とリンク。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 96cf60b5ba67b7027f6527b03d8f522c73a70802
ms.openlocfilehash: 88b6ad23aa57498bbfbc0ae92fc290e8e7affa4a
ms.lasthandoff: 01/18/2017


---
# <a name="azure-iot-sdks"></a>Azure IoT SDK
## <a name="azure-iot-device-sdk"></a>Azure IoT device SDK
Microsoft Azure IoT device SDK には、Azure IoT Hub サービスに接続および管理されるデバイスやアプリケーションの作成を容易にするコードが含まれています。

次の Azure IoT device SDK は GitHub からダウンロードできます。

* [C 用 Azure IoT device SDK。][lnk-c-device-sdk]移植性と広範なプラットフォーム互換性を得るために、ANSI C (C99) で作成されています。
* [.NET 用 Azure IoT device SDK][lnk-dotnet-device-sdk]
* [Java 用 Azure IoT device SDK][lnk-java-device-sdk]
* [Node.js 用 Azure IoT device SDK][lnk-node-device-sdk]
* [Python 用 Azure IoT device SDK][lnk-python-device-sdk]

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。
> 
> 

## <a name="os-platform-and-hardware-compatibility"></a>OS プラットフォームとハードウェアの互換性
特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ][lnk-certified]を参照してください。

## <a name="azure-iot-service-sdk"></a>Azure IoT サービス SDK
Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

次の Azure IoT サービス SDK は GitHub からダウンロードできます。

* [.NET 用 Azure IoT サービス SDK][lnk-dotnet-service-sdk]
* [Node.js 用 Azure IoT サービス SDK][lnk-node-service-sdk]
* [Java 用 Azure IoT サービス SDK][lnk-java-service-sdk]
* [Python 用 Azure IoT サービス SDK][lnk-python-service-sdk]


> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。
> 
> 

## <a name="azure-iot-gateway-sdk"></a>Azure IoT Gateway SDK
この Azure IoT Gateway SDK には、IoT ゲートウェイ ソリューションを作成するためのインフラストラクチャとモジュールが含まれています。 SDK を拡張して、エンド ツー エンドのシナリオに合わせて調整されたゲートウェイを作成できます。

GitHub から [Azure IoT Gateway SDK][lnk-gateway-sdk] をダウンロードできます。

## <a name="online-api-reference-documentation"></a>オンライン API リファレンス ドキュメント
Azure IoT デバイス、サービス、およびゲートウェイ ライブラリのオンライン API リファレンス ドキュメントへのリンクの一覧を次に示します。

* [Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)][lnk-dotnet-ref]
* [IoT Hub REST][lnk-rest-ref]
* [C 用 Azure IoT device SDK][lnk-c-ref]
* [Java 用 Azure IoT device SDK][lnk-java-ref]
* [Java 用 Azure IoT サービス SDK][lnk-java-service-ref]
* [Node.js 用 Azure IoT device SDK][lnk-node-ref]
* [Node.js 用 Azure IoT サービス SDK][lnk-node-service-ref]
* [Azure IoT Gateway SDK][lnk-gateway-ref]

## <a name="next-steps"></a>次のステップ
この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-devguide-endpoints]
* [デバイス ツインとジョブの IoT Hub クエリ言語][lnk-devguide-query]
* [クォータと調整][lnk-devguide-quotas]
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/azure-iot-device/1.1.6/index.html
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/azure-iothub/1.1.6/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

