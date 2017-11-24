---
title: "Azure IoT Edge on Linux のシミュレート | Microsoft Docs"
description: "Linux のシミュレートされたデバイスに Azure IoT Edge ランタイムをインストールし、最初のモジュールをデプロイする"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Linux のシミュレートされたデバイスに Azure IoT Edge をデプロイする - プレビュー

Azure IoT Edge を使用すると、すべてのデータをクラウドにプッシュしなくても、デバイスで分析とデータ処理を実行することができます。 IoT Edge チュートリアルでは、Azure サービスまたはカスタム コードからビルドされたさまざまな種類の モジュールをデプロイする方法について説明しますが、まずテストするデバイスが必要になります。 

このチュートリアルでは、シミュレートされた IoT Edge デバイスを作成してから、センサー データを生成するモジュールをデプロイする方法について説明します。 学習内容は次のとおりです。

![チュートリアル アーキテクチャ][2]

このチュートリアルで作成するシミュレートされたデバイスは、温度、湿度、および圧力のデータを生成するモニターです。 その他の Azure IoT Edge チュートリアルは、ここで行う作業を基盤としており、ビジネスの洞察を得るためにデータを分析するモジュールをデプロイします。 

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Linux を実行しているコンピューターまたは仮想マシンを使用して、モノのインターネット デバイスがシミュレートされていることを前提としています。 IoT Edge デバイスを正しくデプロイするには、以下のサービスが必要です。

- [Docker を Linux にインストールし][lnk-docker-ubuntu]、稼働していることを確認します。 
- Ubuntu を含め、ほとんどの Linux ディストリビューションには既に Python 2.7 がインストールされています。 次のコマンドを使用して、pip がインストールされていることを確認してください: `sudo apt-get install python-pip`

## <a name="create-an-iot-hub"></a>IoT Hub の作成

IoT Hub を作成してチュートリアルを開始します。
![IoT Hub を作成する][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT Hub に IoT Edge デバイスを登録します。
![デバイスを登録する][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールして開始する

Azure IoT Edge ランタイムをデバイスにインストールして開始します。 
![デバイスを登録する][5]

IoT Edge ランタイムはすべての IoT Edge デバイスにデプロイされます。 このランタイムは 2 つのモジュールから構成されます。 まず、IoT Edge エージェントは、IoT Edge デバイスでのモジュールのデプロイと監視を容易にします。 次に、IoT Edge ハブは、IoT Edge デバイス上のモジュール間、およびデバイスと IoT Hub の間の通信を管理します。 

IoT Edge ランタイムをインストールして開始するには、次の手順を使用します。

1. IoT Edge デバイスを実行するマシン上に、IoT Edge 制御スクリプトをダウンロードします。

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. 前のセクションで保存した IoT Edge デバイス接続文字列を使用してランタイムを構成します。

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. ランタイムを開始します。

   ```
   sudo iotedgectl start
   ```

1. Docker を調べて、IoT Edge エージェントがモジュールとして実行されていることを確認します。

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>モジュールをデプロイする

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールをデプロイします。
![デバイスを登録する][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>生成されたデータを表示する

このクイックスタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、デバイス自体を変更せずにデバイスで IoT Edge モジュールを実行するために、IoT Edge モジュールをプッシュしました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

tempSensor モジュールから送信されているメッセージを確認します。

```cmd/sh
docker logs -f tempSensor
```

また、[IoT Hub エクスプローラー ツール][lnk-iothub-explorer]を使用して、デバイスが送信しているテレメトリを表示することもできます。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、新しい IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスにデプロイしました。 その環境に関する生データを生成するシミュレートされたデバイスができあがりました。 

このチュートリアルは、他のすべての IoT Edge チュートリアルの前提条件です。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでこのデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [C# コードをモジュールとして開発およびデプロイする](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
