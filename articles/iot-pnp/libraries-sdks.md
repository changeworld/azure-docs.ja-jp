---
title: IoT プラグ アンド プレイ ライブラリと SDK
description: IoT プラグ アンド プレイが有効なソリューションを開発するために使用できるデバイスとサービス ライブラリに関する情報。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064328"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT プラグ アンド プレイ ライブラリと SDK

IoT プラグ アンド プレイ ライブラリと SDK を使用すると、開発者は、複数のプラットフォーム上でさまざまなプログラミング言語を使用して IoT ソリューションを構築できます。 次の表には、作業を開始するために役立つサンプルとクイックスタートへのリンクが含まれています。

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft がサポートするライブラリと SDK

| プラットフォーム | ライブラリ/パッケージ | ソース コード | サンプル | クイック スタート | リファレンス |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [apt-get 上のデバイス SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [デジタル ツイン クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [IoT Hub への接続](./quickstart-connect-pnp-device-c-linux.md) | [リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Vcpkg 上のデバイス SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [デジタル ツイン クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [IoT Hub への接続](./quickstart-connect-pnp-device-c-windows.md) | [リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [EMBED 上のデバイス SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [デジタル ツイン クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Arduino IDE 内のデバイス SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [デジタル ツイン クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [CocoaPod 上のデバイス SDK](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [デジタル ツイン クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [デジタル ツインのサンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [IoT Hub への接続](./quickstart-connect-pnp-device-csharp.md) | [リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [デジタル ツインのサンプル](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [IoT Hub への接続](./quickstart-connect-pnp-device-java.md) | [リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [デジタル ツインのサンプル](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [IoT Hub への接続](./quickstart-connect-pnp-device-node.md) | [リファレンス](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>IoT Hub のサポート

IoT プラグ アンド プレイ デバイス機能は、[Free レベルと Standard レベルの IoT ハブ](../iot-hub/iot-hub-scaling.md)によってのみサポートされます。

## <a name="next-steps"></a>次のステップ

デバイス SDK とライブラリに加えて、REST API を使用してモデル リポジトリを操作することもできます。