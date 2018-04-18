---
title: Azure IoT SDK について | Microsoft Docs
description: 開発者ガイド - デバイス アプリやバックエンド アプリを構築するために使用できる、さまざまな Azure IoT device SDK およびサービス SDK に関する情報とリンク。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2380e72083ec0c9453db991be5fbcc89310cee17
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDK の概要と使用方法

IoT Hub を使用する場合、以下の 2 つのカテゴリのソフトウェア開発キット (SDK) があります。

* **Device SDK** では、IoT デバイス上で実行するアプリを構築できます。 これらのアプリは IoT Hub にテレメトリを送信し、必要に応じて IoT Hub からメッセージ、ジョブ、メソッド、ツインの更新プログラムを受信します。

* **Service SDK** を使用すると、IoT Hub を管理できます。また、必要に応じてメッセージを送信し、ジョブのスケジュールを設定し、ダイレクト メソッドを呼び出し、IoT デバイスに必要なプロパティの更新を送信することができます。

Azure IoT SDK を使用した開発の利点については、[こちら][lnk-benefits-blog]を参照してください。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT device SDK

Microsoft Azure IoT device SDK には、Azure IoT Hub サービスに接続および管理されるデバイスやアプリケーションの作成を容易にするコードが含まれています。

.NET 用 Azure IoT Hub デバイス SDK: 
* [NuGet][lnk-nuget-csharp-device] からインストールする
* [ソース コード][lnk-dotnet-sdk]
* [API リファレンス][lnk-dotnet-ref]

C 用 Azure IoT Hub デバイス SDK: 移植性と広範なプラットフォーム互換性のために、ANSI C (C99) で作成されています。
* [apt-get、MBED、Arduino IDE、または Nuget][lnk-c-package] からインストールする
* [ソース コード][lnk-c-sdk]
* [API リファレンス][lnk-c-ref]

Java 用 Azure IoT Hub デバイス SDK: 
* [Maven][lnk-maven-device] プロジェクトに追加する
* [ソース コード][lnk-java-sdk]
* [API リファレンス][lnk-java-ref]

Node.js 用 Azure IoT Hub デバイス SDK: 
* [npm][lnk-npm-device] からインストールする
* [ソース コード][lnk-node-sdk]
* [API リファレンス][lnk-node-ref]

Python 用 Azure IoT Hub デバイス SDK: 
* [pip][lnk-pip-device] からインストールする
* [ソース コード][lnk-python-sdk]

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS プラットフォームとハードウェアの互換性

特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ][lnk-certified]または個々のリポジトリを参照してください。

## <a name="azure-iot-service-sdks"></a>Azure IoT サービス SDK

Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

.NET 用 Azure IoT Hub サービス SDK:
* [NuGet][lnk-nuget-csharp-service] からダウンロードする
* [ソース コード][lnk-dotnet-sdk]
* [API リファレンス][lnk-dotnet-service-ref]

Java 用 Azure IoT Hub サービス SDK: 
* [Maven][lnk-maven-service] プロジェクトに追加する
* [ソース コード][lnk-java-sdk]
* [API リファレンス][lnk-java-service-ref]

Node.js 用 Azure IoT Hub サービス SDK: 
* [npm][lnk-npm-service] からダウンロードする
* [ソース コード][lnk-node-sdk]
* [API リファレンス][lnk-node-service-ref]

Python 用 Azure IoT Hub サービス SDK: 
* [pip][lnk-pip-service] からダウンロードする
* [ソース コード][lnk-python-sdk]

C 用 Azure IoT Hub サービス SDK: 
* [apt-get、MBED、Arduino IDE、または Nuget][lnk-c-package] からダウンロードする
* [ソース コード][lnk-c-sdk]

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。


## <a name="next-steps"></a>次の手順

この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-devguide-endpoints]
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-devguide-query]
* [クォータと調整][lnk-devguide-quotas]
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]
* [IoT Hub REST API リファレンス][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
