---
title: "開発者ガイド - Azure IoT SDK | Microsoft Docs"
description: "Azure IoT Hub 開発者ガイド - さまざまな Azure IoT device SDK およびサービス SDK に関する情報とリンク。"
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58dc546ee2db9a0925ecf0fc074795b531323e03


---
# <a name="azure-iot-sdks"></a>Azure IoT SDK
## <a name="azure-iot-device-sdk"></a>Azure IoT device SDK
Microsoft Azure IoT device SDK には、Azure IoT Hub サービスに接続および管理されるデバイスやアプリケーションの作成を容易にするコードが含まれています。

次の Azure IoT device SDK は GitHub からダウンロードできます。

* [C 用 Azure IoT device SDK。][lnk-c-device-sdk]移植性と広範なプラットフォーム互換性を得るために、ANSI C (C99) で作成されています。
* [.NET 用 Azure IoT device SDK][lnk-dotnet-device-sdk]
* [Java 用 Azure IoT device SDK][lnk-java-device-sdk]
* [Node.js 用 Azure IoT device SDK][lnk-node-device-sdk]
* [Python 2.7 用 Microsoft Azure IoT device SDK][lnk-python-device-sdk]

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。
> 
> 

## <a name="os-platforms-and-hardware-compatibility"></a>OS プラットフォームとハードウェアの互換性
特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ][lnk-certified]を参照してください。

## <a name="azure-iot-service-sdk"></a>Azure IoT サービス SDK
Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

次の Azure IoT サービス SDK は GitHub からダウンロードできます。

* [.NET 用 Azure IoT サービス SDK][lnk-dotnet-service-sdk]
* [Node.js 用 Azure IoT サービス SDK][lnk-node-service-sdk]
* [Java 用 Azure IoT サービス SDK][lnk-java-service-sdk]

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

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


