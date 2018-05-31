---
title: Azure IoT Edge on Windows のシミュレート | Microsoft Docs
description: Windows のシミュレートされたデバイスに Azure IoT Edge ランタイムをインストールし、最初のモジュールをデプロイする
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 213a0e7cebda6a8b89ef460799cbec477b487a64
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166116"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Windows のシミュレートされたデバイスに Azure IoT Edge をデプロイする - プレビュー

Azure IoT Edge を使用すると、すべてのデータをクラウドにプッシュしなくても、デバイスで分析とデータ処理を実行することができます。 IoT Edge チュートリアルでは、Azure サービスまたはカスタム コードからビルドされたさまざまな種類の モジュールをデプロイする方法について説明しますが、まずテストするデバイスが必要になります。 

このチュートリアルで学習する内容は次のとおりです。

1. IoT Hub の作成
2. IoT Edge デバイスを登録する
3. IoT Edge ランタイムを開始する
4. モジュールを展開する

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
3. [Python を Windows][lnk-python] にインストールし、pip コマンドを使用できるようにします。 このチュートリアルは、Python バージョン 2.7.9 以降および 3.5.4 でテストされました。  
4. 次のコマンドを実行して、IoT Edge 制御スクリプトをダウンロードします。

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge は、Windows コンテナーまたは Linux コンテナーを実行できます。 次のいずれかのバージョンの Windows を実行している場合は、Windows コンテナーを使うことができます。
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (ビルド 16299)
>    * x64 ベース デバイス上の Windows IoT Core (ビルド 16299)
>
> Windows IoT Core については、[Windows IoT Core への IoT Edge ランタイムのインストール][lnk-install-iotcore]に関するページの手順に従ってください。 それ以外の場合は、単に [Windows コンテナーを使うように Docker を構成][lnk-docker-containers]します。 次のコマンドを使って、前提条件を検証します。
>    ```powershell
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

IoT Edge ランタイムはすべての IoT Edge デバイスにデプロイされます。 これは 2 つのモジュールから構成されます。 **IoT Edge エージェント**は、IoT Edge デバイスでのモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。 新しいデバイスでランタイムを構成するときは、最初に IoT Edge エージェントだけが開始します。 IoT Edge ハブは、後でモジュールをデプロイすると開始します。 


前のセクションで保存した IoT Edge デバイス接続文字列を使用してランタイムを構成します。

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

ランタイムを開始します。

```cmd
iotedgectl start
```

Docker を調べて、IoT Edge エージェントがモジュールとして実行されていることを確認します。

```cmd
docker ps
```

![Docker で edgeAgent を確認する](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>モジュールを展開する

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールをデプロイします。
![デバイスを登録する][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>生成されたデータを表示する

このチュートリアルでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、IoT Edge モジュールをプッシュし、デバイス自体を変更せずにモジュールをデバイスで実行しました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

シミュレートされたデバイスを実行しているコンピューターで、もう一度コマンド プロンプトを開きます。 IoT Edge デバイスで、クラウドからデプロイされたモジュールが実行されていることを確認します。 

```cmd
docker ps
```

![ご利用のデバイスで 3 つのモジュールを表示する](./media/tutorial-simulate-device-windows/docker-ps2.png)

tempSensor モジュールからクラウドに送信されているメッセージを確認します。 

```cmd
docker logs -f tempSensor
```

![モジュールからのデータを表示する](./media/tutorial-simulate-device-windows/docker-logs.png)

また、[IoT Hub エクスプローラー ツール][lnk-iothub-explorer]を使用して、デバイスが送信しているテレメトリを表示することもできます。 

## <a name="next-steps"></a>次の手順

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