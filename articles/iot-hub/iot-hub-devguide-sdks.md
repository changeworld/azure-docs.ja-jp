---
title: Azure IoT SDK について | Microsoft Docs
description: 開発者ガイド - デバイス アプリやバックエンド アプリを構築するために使用できる、さまざまな Azure IoT device SDK およびサービス SDK に関する情報とリンク。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 1eeb0afdd5ffcbe00357914d6a98c8d0b3d452ec
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017961"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDK の概要と使用方法

IoT ハブを操作するための、以下の 3 つのカテゴリーのソフトウェア開発キット (SDK) があります。

* **Device SDK** では、デバイス クライアントまたはモジュール クライアントを使用して、ご利用の IoT デバイス上で実行するアプリを構築できます。 これらのアプリは IoT Hub にテレメトリを送信し、必要に応じて IoT Hub からメッセージ、ジョブ、メソッド、ツインの更新プログラムを受信します。  また、モジュール クライアントを使用して、[Azure IoT Edge ランタイム](../iot-edge/about-iot-edge.md)用の[モジュール](../iot-edge/iot-edge-modules.md)を作成することもできます。

* **Service SDK** を使用すると、ご利用の IoT Hub を管理できます。また、必要に応じてメッセージを送信し、ジョブのスケジュールを設定し、ダイレクト メソッドを呼び出し、ご利用の IoT デバイスまたはモジュールに必要なプロパティの更新情報を送信することができます。

* **Device Provisioning SDK** では、[デバイス プロビジョニング サービス](../iot-dps/about-iot-dps.md)を使用してご利用の IoT Hub にデバイスをプロビジョニングすることができます。

[Azure IoT SDK を使用した開発の利点](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)について、詳細を説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT device SDK

Microsoft Azure IoT デバイス SDK には、Azure IoT Hub サービスに接続および管理されるデバイスやアプリケーションの作成を容易にするコードが含まれています。

.NET 用 Azure IoT Hub デバイス SDK: 

* [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) からインストールする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-csharp)
* [API リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [モジュール リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

移植性と広範なプラットフォーム互換性のために、ANSI C (C99) で記述されている C 用 Azure IoT Hub デバイス SDK:

* [apt-get、MBED、Arduino IDE、または Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) からインストールする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-c)
* [API リファレンス](https://azure.github.io/azure-iot-sdk-c/index.html)
* [モジュール リファレンス](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Java 用 Azure IoT Hub デバイス SDK: 

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) プロジェクトに追加する
* [ソース コード](https://github.com/Azure/azure-iot-sdk-java)
* [API リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [モジュール リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

Node.js 用 Azure IoT Hub デバイス SDK: 

* [npm](https://www.npmjs.com/package/azure-iot-device) からインストールする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-node)
* [API リファレンス](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [モジュール リファレンス](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Python 用 Azure IoT Hub デバイス SDK: 

* [pip](https://pypi.python.org/pypi/azure-iothub-device-client/) からインストールする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-python)
* API のリファレンス: [C API リファレンス](https://azure.github.io/azure-iot-sdk-c/index.html)に関するページを参照してください。

iOS 用 Azure IoT Hub デバイス SDK: 

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) からインストールする
* [サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API のリファレンス: [C API リファレンス](https://azure.github.io/azure-iot-sdk-c/index.html)に関するページを参照してください。

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS プラットフォームとハードウェアの互換性

SDK でサポートされるプラットフォームは、「[Azure IoT SDK プラットフォームのサポート](iot-hub-device-sdk-platform-support.md)」で確認できます。

特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsuite.com/)または個々のリポジトリを参照してください。

## <a name="azure-iot-service-sdks"></a>Azure IoT サービス SDK

Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

.NET 用 Azure IoT Hub サービス SDK:

* [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-csharp)
* [API リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Java 用 Azure IoT Hub サービス SDK: 

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
) プロジェクトに追加する
* [ソース コード](https://github.com/Azure/azure-iot-sdk-java)
* [API リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Node.js 用 Azure IoT Hub サービス SDK: 

* [npm](https://www.npmjs.com/package/azure-iothub) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-node)
* [API リファレンス](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Python 用 Azure IoT Hub サービス SDK: 

* [pip](https://pypi.python.org/pypi/azure-iothub-service-client/) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-python)

C 用 Azure IoT Hub サービス SDK: 

* [apt-get、MBED、Arduino IDE、または Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-c)

iOS 用 Azure IoT Hub サービス SDK: 

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) からインストールする
* [サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。

## <a name="device-provisioning-sdks"></a>Device Provisioning SDK

**Microsoft Azure Provisioning SDK** では、[デバイス プロビジョニング サービス](../iot-dps/about-iot-dps.md)を使用してデバイスをご利用の IoT Hub にプロビジョニングすることができます。

C# 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Provisioning Service Client SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Java 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [Provisioning Service Client SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Node.js 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Provisioning Service Client SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Python 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Provisioning Service Client SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

C 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Provisioning Service Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>次の手順

Azure IoT SDK では、開発に役立つ一連のツールも提供しています。
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): IoT Hub との接続に関連する問題の診断に役立つ、クロスプラットフォーム コマンド ライン ツール。
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): IoT Hub に接続するための Windows デスクトップ アプリケーション。

この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)
* [クォータとスロットル](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md)
* [IoT Hub REST API リファレンス](/rest/api/iothub/)
* [Azure IoT SDK プラットフォームのサポート](iot-hub-device-sdk-platform-support.md)