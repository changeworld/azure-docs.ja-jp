---
title: IoT プラグ アンド プレイの現在のリリース | Microsoft Docs
description: IoT プラグ アンド プレイの現在のリリースに含まれる内容について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580791"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>IoT プラグ アンド プレイの現在のリリースに含まれる内容について

この記事では、IoT プラグ アンド プレイの現在のリリースをサポートしているツール、SDK、および API の概要について説明します。 表示されるバージョン番号は、IoT プラグ アンド プレイが一般公開された時点のバージョン番号を反映しています。 バージョン番号は、リリース後に増加する場合があります。

## <a name="modeling-language"></a>モデリング言語

[Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl)。

IoT プラグ アンド プレイ デバイスが DTDL と連携する方法の詳細については、「[IoT プラグ アンド プレイ規則](concepts-convention.md)」を参照してください。

## <a name="tools-and-utilities"></a>ツールとユーティリティ

- Azure IoT エクスプローラー 0.12.0。

    詳細については、「[Azure IoT エクスプローラーをインストールして使用する](howto-use-iot-explorer.md)」を参照してください。

- VS Code 拡張機能 1.0.0。

    詳細については、「[DTDL オーサリング ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)」を参照してください。

- Visual Studio 2019 拡張機能 1.0.0。

    詳細については、「[DTDL オーサリング ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)」を参照してください。

- Azure CLI IoT 拡張機能 0.10.0。

    詳細については、「[Azure CLI 用 Azure IoT 拡張機能をインストールして使用する](howto-use-iot-pnp-cli.md)」を参照してください。

    > [!TIP]
    > Azure IoT 拡張機能には、デバイスの認定に役立つコマンドが含まれています。 以下を参照してください。`az iot product -h`



## <a name="libraries-and-sdks"></a>ライブラリと SDK

ライブラリと SDK の詳細については、「[IoT プラグ アンド プレイ用の Microsoft SDK](libraries-sdks.md)」を参照してください。

- C デバイス SDK [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- .NET デバイス SDK [NuGet Microsoft.Azure.Devices.Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Java デバイス SDK [Maven iot-device-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python デバイス SDK [Pip azure-iot-device v2.2.0](https://pypi.org/project/azure-iot-device/)
- Node.js デバイス SDK [npm azure-iot-device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET - IoT Hub サービス [NuGet Microsoft.Azure.Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java - IoT Hub サービス [Maven iot-service-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js - IoT Hub サービス [npm azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python - IoT Hub/Digital Twins サービス [Pip azure-iot-hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- DTDL モデル パーサー [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)。

### <a name="preview"></a>[プレビュー]

- Azure SDK for Embedded [1.0.0-preview.5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Azure RTOS IoT ミドルウェア [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>REST API

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub)。

詳細については、[IoT プラグ アンド プレイの開発者ガイド](concepts-developer-guide-service.md)を参照してください。

## <a name="iot-hub"></a>IoT Hub

IoT プラグ アンド プレイは、すべてのリージョンの IoT Hub でサポートされています。 IoT プラグ アンド プレイは、Standard または Free レベルの IoT ハブでのみサポートされています。

## <a name="announcements"></a>アナウンス

現在および以前の IoT プラグ アンド プレイのアナウンスについては、以下のブログ投稿を参照してください。

- [パブリック プレビューの更新 (2020 年 8 月 29 日投稿)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [IoT プラグ アンド プレイ用にデバイスを準備して認定する (2020 年 8 月 26 日投稿)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT プラグ アンド プレイがプレビューで使用可能に (2019 年 8 月 22 日投稿)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Azure IoT Central と IoT プラグ アンド プレイで構築する (2019 年 5 月 7 日投稿)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

