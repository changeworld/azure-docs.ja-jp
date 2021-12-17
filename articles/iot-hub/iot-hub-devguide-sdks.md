---
title: Azure IoT Hub SDK | Microsoft Docs
description: デバイス アプリとバックエンド アプリのビルドに使用できる Azure IoT Hub SDK へのリンク。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: e8bfc95d80aa58b9781258c1b87902c2e8d0c37f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458588"
---
# <a name="azure-iot-hub-sdks"></a>Azure IoT Hub SDK

IoT Hub を使用する場合、以下の 2 つのカテゴリのソフトウェア開発キット (SDK) があります。

* [**IoT Hub Service SDK**](#azure-iot-hub-service-sdks) を利用すると、IoT ハブを管理するバックエンド アプリケーションをビルドでき、必要に応じて、メッセージを送信する、ジョブのスケジュールを設定する、ダイレクト メソッドを呼び出す、IoT デバイスまたはモジュールに必要なプロパティの更新情報を送信することができます。

* [**IoT Hub Device SDK**](../iot-develop/about-iot-sdks.md) を利用すると、デバイス クライアントまたはモジュール クライアントを使用して、IoT デバイス上で実行するアプリをビルドできます。 これらのアプリは IoT Hub にテレメトリを送信し、必要に応じて IoT Hub からメッセージ、ジョブ、メソッド、ツインの更新を受信します。 これらの SDK を使用して、[Azure IoT プラグ アンド プレイ](../iot-develop/overview-iot-plug-and-play.md)の規則およびモデルを使用するデバイス アプリをビルドし、IoT プラグ アンド プレイ対応アプリケーションにその機能を提供することができます。 また、モジュール クライアントを使用して、[Azure IoT Edge ランタイム](../iot-edge/about-iot-edge.md)用の[モジュール](../iot-edge/iot-edge-modules.md)を作成することもできます。

また、[デバイス プロビジョニング サービス](../iot-dps/about-iot-dps.md)を操作するための一連の SDK も利用できます。

* **Device SDK をプロビジョニング** すると、IoT デバイス上で動作して、デバイス プロビジョニング サービスと通信するアプリを構築できます。

* **Service SDK をプロビジョニング** すると、デバイス プロビジョニング サービスで自分の登録を管理するバックエンド アプリケーションを構築できます。

[Azure IoT SDK を使用した開発の利点](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)について、詳細を説明します。

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub サービス SDK

Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

| プラットフォーム  | Package | コード リポジトリ | サンプル |  リファレンス |
|---|---|---|---|---|
| .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [リファレンス](/dotnet/api/microsoft.azure.devices) |
| Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | [リファレンス](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node | [npm](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [リファレンス](/javascript/api/azure-iothub/) |
| Python | [pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [リファレンス](/python/api/azure-iot-hub) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-common) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [リファレンス](/javascript/api/azure-iothub/) |

iOS 用 Azure IoT Hub サービス SDK:

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) からインストールする
* [サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios)

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure Provisioning SDK

**Microsoft Azure Provisioning SDK** では、[デバイス プロビジョニング サービス](../iot-dps/about-iot-dps.md)を使用してデバイスをご利用の IoT Hub にプロビジョニングすることができます。

| プラットフォーム | パッケージ | ソース コード | リファレンス |
| -----|-----|-----|-----|
| .NET|[Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/)、[Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) |[GitHub](https://github.com/Azure/azure-iot-sdk-csharp/)|[参照](/dotnet/api/microsoft.azure.devices.provisioning.client) |
| C|[apt-get、MBED、Arduino IDE、または iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)|[GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning\_client)|[リファレンス](/azure/iot-hub/iot-c-sdk-ref/) |
| Java|[Maven](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk)|[GitHub](https://github.com/Azure/azure-iot-sdk-java/blob/main/provisioning)|[リファレンス](/java/api/com.microsoft.azure.sdk.iot.provisioning.device) |
| Node.js|[Device SDK](https://badge.fury.io/js/azure-iot-provisioning-device)、[Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) |[GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)|[リファレンス](/javascript/api/overview/azure/iothubdeviceprovisioning) |
| Python|[Device SDK](https://pypi.org/project/azure-iot-device/)、[Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/)|[GitHub](https://github.com/Azure/azure-iot-sdk-python)|[デバイス リファレンス](/python/api/azure-iot-device/azure.iot.device.provisioningdeviceclient)、[サービス リファレンス](/python/api/azure-mgmt-iothubprovisioningservices) |

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub デバイス SDK

Microsoft Azure IoT device SDK には、Azure IoT Hub サービスに接続および管理されるアプリケーションの構築を容易にするコードが含まれています。

IoT Hub Device SDK の詳細については、[IoT デバイス開発に関するドキュメント](../iot-develop/about-iot-sdks.md)を参照してください。

## <a name="sdk-and-hardware-compatibility"></a>SDK とハードウェアの互換性

デバイス SDK の選択の詳細については、「[Azure IoT device SDK の概要](../iot-develop/about-iot-sdks.md)」を参照してください。

特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ](https://devicecatalog.azure.com/)または個々のリポジトリを参照してください。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="next-steps"></a>次のステップ

Azure IoT SDK を使用した開発に関連するドキュメント

* IoT Hub デバイス SDK を使用して[接続と信頼できるメッセージングを管理する方法](iot-hub-reliability-features-in-sdks.md)に関する記事を参照してください。
* iOS や Android などの[モバイル プラットフォーム向けの開発](iot-hub-how-to-develop-for-mobile-devices.md)方法に関する記事を参照してください。
* [IoT デバイス開発のドキュメント](../iot-develop/about-iot-sdks.md)

この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)
* [クォータとスロットル](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md)
* [IoT Hub REST API リファレンス](/rest/api/iothub/)
