---
title: IoT プラグ アンド プレイ ブリッジ | Microsoft Docs
description: IoT プラグ アンド プレイ ブリッジについて、およびそれを使用して Windows または Linux ゲートウェイに接続されている既存のデバイスを IoT プラグ アンド プレイ デバイスとして接続する方法について説明します。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0435fe3946118d59d786dd3e6cec350a5ab4eee4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046453"
---
# <a name="iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジ

IoT プラグ アンド プレイ ブリッジは、Windows または Linux ゲートウェイに接続されている既存のデバイスを IoT プラグ アンド プレイ デバイスとして接続するためのオープンソース アプリケーションです。 Windows または Linux マシンにアプリケーションをインストールして構成した後、それを使用して、接続されているデバイスを IoT ハブに接続することができます。 このブリッジを使用して、接続されているデバイスによって送信されるテレメトリに IoT プラグ アンド プレイ インターフェイスをマップしたり、デバイスのプロパティを操作したり、コマンドを呼び出したりできます。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左側には、IoT プラグ アンド プレイ ブリッジを含む Windows または Linux PC に接続されている既存のセンサー (有線とワイヤレスの両方) がいくつかあります。IoT プラグ アンド プレイ ブリッジは、右側の IoT ハブに接続されます":::

IoT プラグ アンド プレイ ブリッジは、Windows 10 または Linux を実行している任意の IoT デバイス、業務用 PC、サーバー、またはゲートウェイにスタンドアロンの実行可能ファイルとしてデプロイできます。 また、アプリケーション コードにコンパイルすることもできます。 単純な構成 JSON ファイルにより、接続されているどのデバイスまたは周辺機器を Azure に公開するかが、IoT プラグ アンド プレイ ブリッジに指示されます。

## <a name="supported-protocols-and-sensors"></a>サポートされているプロトコルとセンサー

IoT プラグ アンド プレイ ブリッジでは、次の種類の周辺機器が既定でサポートされており、アダプターのドキュメントへのリンクがあります。

|周辺装置|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |はい|いいえ|
|[カメラ](https://aka.ms/iot-pnp-bridge-camera)               |はい|いいえ|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |はい|はい|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |はい|はい|
|[シリアル](https://aka.ms/iot-pnp-bridge-serial)                |はい|はい|
|[Windows USB 周辺機器](https://aka.ms/iot-pnp-bridge-usb)  |はい|適用しない|

>[!Important]
>開発者は、IoT プラグ アンド プレイ ブリッジを拡張して、 **[ここにある IoT プラグ アンド プレイ ブリッジ開発者向けドキュメント](https://aka.ms/iot-pnp-bridge-dev-doc)** の手順に従って、追加のデバイス プロトコルをサポートすることができます。

## <a name="prerequisites"></a>前提条件

### <a name="os-platform"></a>OS プラットフォーム

次の OS プラットフォームおよびバージョンがサポートされています。

|プラットフォーム  |サポートされているバージョン  |
|---------|---------|
|Windows 10 |     すべての Windows SKU がサポートされています。 次に例を示します。IoT Enterprise、Server、Desktop、IoT Core。 "*カメラの正常性の監視機能については、20H1 以降のビルドをお勧めします。その他のすべての機能は、すべての Windows 10 ビルドで使用できます。* "  |
|Linux     |Ubuntu 18.04 でテストおよびサポートされています。他のディストリビューションの機能はテストされていません。         |
||

### <a name="hardware"></a>ハードウェア

- 上記の OS SKU およびバージョンをサポートできるすべてのハードウェア プラットフォーム。
- シリアル、USB、Bluetooth、およびカメラの周辺機器およびセンサーは、ネイティブでサポートされています。 IoT プラグ アンド プレイ ブリッジは、任意のカスタム周辺機器またはセンサーをサポートするように拡張できます ([周辺機器に関する上記のセクションを参照](#iot-plug-and-play-bridge))。

### <a name="development-environment"></a>開発環境

IoT プラグ アンド プレイ ブリッジのビルド、拡張、開発を行うには、次のものが必要です。  

- C++ のコンパイルをサポートする次のような開発環境: [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/): Visual Studio をインストールするときに、[C++ によるデスクトップ開発] ワークロードを必ず含めてください。
- [CMake](https://cmake.org/download/): CMake をインストールするときに、`Add CMake to the system PATH` オプションを選択します。
- Windows でビルドする場合は、Windows 17763 SDK もダウンロードする必要があります: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。
- [Azure IoT Hub デバイス C SDK](https://github.com/Azure/azure-iot-sdk-c): このリポジトリに含まれているビルド スクリプトによって、必要な Azure IoT C SDK が自動的にクローンされます。

### <a name="azure-iot-products-and-tools"></a>Azure IoT の製品とツール

- **Azure IoT Hub**: Azure サブスクリプションには、デバイスを接続する [Azure IoT Hub](../iot-hub/index.yml) が必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 IoT ハブがない場合は、[こちらの手順に従って作成します](../iot-hub/iot-hub-create-using-cli.md)。

> [!Note]
> 現在、IoT プラグ アンド プレイは、米国中部、北ヨーロッパ、東日本の各リージョンで作成された IoT ハブで利用できます。 IoT プラグ アンド プレイ サポートは、Basic レベルの IoT ハブには含まれていません。 IoT プラグ アンド プレイ デバイスを操作するには、Azure IoT エクスプローラー ツールを使用できます。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT プラグ アンド プレイ ブリッジのアーキテクチャ

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左側には、IoT プラグ アンド プレイ ブリッジを含む Windows または Linux PC に接続されている既存のセンサー (有線とワイヤレスの両方) がいくつかあります。IoT プラグ アンド プレイ ブリッジは、右側の IoT ハブに接続されます":::

## <a name="download-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジをダウンロードする

「[IoT プラグ アンド プレイ ブリッジのリリース](https://aka.ms/iot-pnp-bridge-releases)」で、サポートされているアダプターを含む事前ビルドされたバージョンのブリッジをダウンロードし、最新のリリースの資産の一覧を拡張することができます。 ご利用のオペレーティング システム用の最新バージョンのアプリケーションをダウンロードします。

[GitHub の IoT プラグ アンド プレイ ブリッジ](https://aka.ms/bridge)のソース コードをダウンロードして表示することもできます。

## <a name="next-steps"></a>次の手順

ここでは、IoT プラグ アンド プレイ ブリッジのアーキテクチャの概要を示しました。次の手順では以下の詳細を参照してください。

- [IoT プラグ アンド プレイ ブリッジの使用方法](./howto-use-iot-pnp-bridge.md)
- [IoT プラグ アンド プレイ ブリッジの GitHub 開発者向けリファレンスを参照する](https://aka.ms/iot-pnp-bridge-dev-doc)
- [GitHub の IoT プラグ アンド プレイ ブリッジ](https://aka.ms/iotplugandplaybridge)