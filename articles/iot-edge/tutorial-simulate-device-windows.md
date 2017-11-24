---
title: "Azure IoT Edge on Windows のシミュレート | Microsoft Docs"
description: "Windows のシミュレートされたデバイスに Azure IoT Edge ランタイムをインストールし、最初のモジュールをデプロイする"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Windows のシミュレートされたデバイスに Azure IoT Edge をデプロイする - プレビュー

Azure IoT Edge は、クラウドの機能をご使用のモノのインターネット (IoT) デバイスでも利用できるようにします。 このチュートリアルでは、センサー データを生成するシミュレートされた IoT Edge デバイスを作成する方法について説明します。 学習内容は次のとおりです。

![チュートリアル アーキテクチャ][2]

このチュートリアルで作成するシミュレートされたデバイスは、温度、湿度、および圧力のデータを生成する風力タービンのモニターです。 タービンは気象条件に応じてさまざまな効率レベルで動作するため、このデータは興味深いものです。 その他の Azure IoT Edge チュートリアルは、ここで行う作業を基盤としており、ビジネスの洞察を得るためにデータを分析するモジュールをデプロイします。 

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Windows を実行しているコンピューターまたは仮想マシンを使用して、モノのインターネット デバイスがシミュレートされていることを前提としています。 

>[!TIP]
>仮想マシンで Windows を実行している場合は、[入れ子になった仮想化][lnk-nested]を有効にして、少なくとも 2 GB のメモリを割り当てます。 

1. サポートされている Windows バージョンを使用していることを確認します。
   * Windows 10 
   * Windows Server
2. [Docker for Windows][lnk-docker] をインストールし、実行します。
3. [Python 2.7 を Windows][lnk-python] にインストールし、pip コマンドを使用できるようにします。
4. 次のコマンドを実行して、IoT Edge 制御スクリプトをダウンロードします。

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge は、Windows コンテナーまたは Linux コンテナーを実行できます。 Windows コンテナーを使用するには、次のいずれかを実行する必要があります。
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (ビルド 16299)
>    * x64 ベース デバイス上の Windows IoT Core (ビルド 16299)
>
> Windows IoT Core については、[Windows IoT Core への IoT Edge ランタイムのインストール][lnk-install-iotcore]に関するページの手順に従ってください。 それ以外の場合は、[Windows コンテナーを使用するように Docker を構成][lnk-docker-containers]し、必要に応じて次の PowerShell コマンドを使用して、前提条件を検証します。
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>IoT Hub の作成

IoT Hub を作成してチュートリアルを開始します。
![IoT Hub を作成する][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT Hub に IoT Edge デバイスを登録します。
![デバイスを登録する][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>IoT Edge ランタイムを構成する

Azure IoT Edge ランタイムをデバイスにインストールして開始します。 
![デバイスを登録する][5]

IoT Edge ランタイムはすべての IoT Edge デバイスにデプロイされます。 このランタイムは 2 つのモジュールから構成されます。 まず、IoT Edge エージェントは、IoT Edge デバイスでのモジュールのデプロイと監視を容易にします。 次に、IoT Edge ハブは、IoT Edge デバイス上のモジュール間、およびデバイスと IoT Hub の間の通信を管理します。 


IoT Edge ランタイムをインストールして開始するには、次の手順を使用します。

1. 前のセクションで保存した IoT Edge デバイス接続文字列を使用してランタイムを構成します。

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. ランタイムを開始します。

   ```
   iotedgectl start
   ```

1. Docker を調べて、IoT Edge エージェントがモジュールとして実行されていることを確認します。

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>モジュールをデプロイする

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールをデプロイします。
![デバイスを登録する][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>生成されたデータを表示する

このクイックスタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、デバイス自体を変更せずにデバイスで IoT Edge モジュールを実行するために、IoT Edge モジュールをプッシュしました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

tempSensor モジュールから送信されているメッセージを確認します。

```cmd/sh
sudo docker logs -f tempSensor
```

また、[IoT Hub エクスプローラー ツール][lnk-iothub-explorer]を使用して、デバイスが送信しているテレメトリを表示することもできます。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、新しい IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスにデプロイしました。 その環境に関する生データを生成するシミュレートされたデバイスができあがりました。 

このチュートリアルは、他のすべての IoT Edge チュートリアルの前提条件です。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでこのデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [C# コードをモジュールとして開発およびデプロイする](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md