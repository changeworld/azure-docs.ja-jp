---
title: Azure IoT device SDK の選択肢の概要
description: 開発上の役割とタスクに応じて、どの Azure IoT device SDK を使用すべきかについて説明します。
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: e4f5d99caadaedf6389215fccb753aedd6620917
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983971"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Azure IoT device SDK の概要

Azure IoT device SDK には、デバイス クライアント ライブラリ、サンプル、ドキュメントが含まれています。 device SDK を使用すると、プログラムでデバイスを Azure IoT に接続するプロセスが簡素化されます。 SDK は、さまざまなプログラミング言語で使用できます。また、組み込みデバイス用の複数の RTOS がサポートされています。

## <a name="why-use-an-azure-iot-device-sdk"></a>Azure IoT device SDK を使用する理由

カスタム接続レイヤーを構築した場合と比べて Azure IoT device SDK を使用した場合の利点を以下に示します。

| | カスタム接続レイヤー | Azure IoT device SDK |
| :-- | :-- | :-- |
| **サポート** | ソリューションをサポートして文書化する必要がある | Microsoft サポート (GitHub、Microsoft Q&A、Microsoft Docs、カスタマー サポート チーム) にアクセスする |
| **新機能** | 新しい Azure の機能を手動で追加する必要がある | 追加された新機能をすぐに利用できる |
| **投資金** | カスタム バージョンを設計、構築、テスト、保守するための組み込み開発に何百時間も費やす必要がある | 無料のオープンソース ツールを利用できる。 SDK に関連したコストは学習曲線のみです。 |

## <a name="which-sdk-should-i-use"></a>使用する SDK

SDK を選択する際の主な考慮事項は、デバイス独自のハードウェアです。 PC や携帯電話のような一般的なコンピューティング デバイスには、マイクロプロセッサ ユニット (MPU) が含まれているので、コンピューティングとメモリのリソースが比較的大きくなります。 センサーや他の特殊な目的の役割として使用される特殊なクラスのデバイスには、マイクロコントローラー ユニット (MCU) が含まれているので、コンピューティングとメモリのリソースは比較的限られます。 リソースに制約のあるこれらのデバイスには、特殊な開発ツールと SDK が必要です。 次の表は、異なるクラスのデバイスと、デバイス開発に使用する SDK をまとめたものです。

|デバイス クラス|説明|例|SDK|
|-|-|-|-|
|組み込みデバイス|コンピューティングとメモリに制限がある特殊な目的の MCU ベースのデバイス|Sensors|[組み込みデバイスの SDK](#embedded-device-sdks)|
|その他|より大規模なコンピューティングとメモリのリソースを備えた汎用の MPU ベースのデバイスが含まれます|PC、スマートフォン、Raspberry Pi|[デバイスの SDK](#device-sdks)|

> [!Note] 
> デバイスに最適な SDK を選択できるように、さまざまなデバイス カテゴリの詳細を確認するには、[Azure IoT デバイスの種類](concepts-iot-device-types.md)に関するページを参照してください。

## <a name="device-sdks"></a>デバイスの SDK

これらの SDK は、PC、タブレット、スマートフォン、Raspberry Pi などの一般的な MPU ベースのコンピューティング デバイスで実行できます。 SDK では、C と、最新のマネージド言語 (C#、Node.JS、Python、Java など) での開発がサポートされます。

SDK は **複数の言語** で利用できるため、チームとシナリオに最適な SDK を柔軟に選択できます。

| Language | Package | source | クイックスタート | サンプル | リファレンス |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **.NET** | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-csharp) | [サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [リファレンス](/dotnet/api/microsoft.azure.devices.client) |
| **Python** | [pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-python) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | [リファレンス](/python/api/azure-iot-device) |
| **Node.js** | [npm](https://www.npmjs.com/package/azure-iot-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-nodejs) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) | [リファレンス](/javascript/api/azure-iot-device/) |
| **Java** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-java) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples) | [リファレンス](/java/api/com.microsoft.azure.sdk.iot.device) |
| **C** | [packages](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#getting-the-sdk) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-ansi-c) | [サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) | [リファレンス](/azure/iot-hub/iot-c-sdk-ref/) |

> [!WARNING]
> 上記の **C SDK** は、そのメモリ管理とスレッド モデルが原因で、埋め込みアプリケーションには適して **いません**。 組み込みデバイスについては、「[組み込みデバイスの SDK](#embedded-device-sdks)」を参照してください。

## <a name="embedded-device-sdks"></a>組み込みデバイスの SDK

これらの SDK は、コンピューティングとメモリのリソースが限られているデバイスで実行するように設計および作成されており、C 言語を使用して実装されています。

組み込みデバイスの SDK は **複数のオペレーティング システム** で使用できるため、チームとシナリオに最適な SDK を柔軟に選択できます。

| RTOS | SDK | source | サンプル | リファレンス |
| :-- | :-- | :-- | :-- | :-- | 
| **Azure RTOS** | Azure RTOS ミドルウェア | [GitHub](https://github.com/azure-rtos/netxduo) | [クイック スタート](quickstart-devkit-mxchip-az3166.md) | [リファレンス](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot) | 
| **FreeRTOS** | FreeRTOS ミドルウェア | [GitHub](https://github.com/Azure/azure-iot-middleware-freertos) | [サンプル](https://github.com/Azure-Samples/iot-middleware-freertos-samples) | [リファレンス](https://azure.github.io/azure-iot-middleware-freertos) |
| **ベアメタル** | Azure SDK for Embedded C | [GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) | [サンプル](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md) | [リファレンス](https://azure.github.io/azure-sdk-for-c) |

## <a name="next-steps"></a>次の手順

Azure IoT デバイス SDK を使用して一般の MPU ベースのデバイスを接続する方法の詳細については、次の記事を参照してください。

* [クイックスタート: テレメトリを IoT Central に送信する](quickstart-send-telemetry-central.md)
* [クイックスタート: テレメトリを IoT Hub に送信する](quickstart-send-telemetry-iot-hub.md)

Azure IoT の組み込みデバイスの SDK を使用して、リソースに制約のある MCU ベースのデバイスを接続する方法の詳細については、次の記事を参照してください。
* [クイックスタート: MXCHIP AZ3166 Devkit を IoT Central に接続する](quickstart-devkit-mxchip-az3166.md)
