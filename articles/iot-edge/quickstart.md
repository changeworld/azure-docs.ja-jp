---
title: "Azure IoT Edge + Windows クイック スタート | Microsoft Docs"
description: "シミュレートされたエッジ デバイスで分析を実行して Azure IoT Edge を試します"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 803b0bbff12c8ce471c0bff5e22e24601b8ce07f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>クイック スタート: 初めての IoT Edge モジュールを Azure Portal から Windows デバイスに展開する - プレビュー

このクイック スタートでは、Azure IoT Edge クラウド インターフェイスを使用して、事前作成されたコードを IoT Edge デバイスにリモートで展開します。 このタスクを実行するには、最初に Windows デバイスを使用して IoT Edge デバイスをシミュレートし、モジュールを展開します。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][lnk-account]を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Windows を実行しているコンピューターまたは仮想マシンを使用して、モノのインターネット デバイスがシミュレートされていることを前提としています。 仮想マシンで Windows を実行している場合は、[入れ子になった仮想化][lnk-nested]を有効にして、少なくとも 2 GB のメモリを割り当てます。 

1. サポートされている Windows バージョンを使用していることを確認します。
   * Windows 10 
   * Windows Server
2. [Docker for Windows][lnk-docker] をインストールし、実行します。
3. [Python 2.7 を Windows][lnk-python] にインストールし、pip コマンドを使用できるようにします。
4. 次のコマンドを実行して、IoT Edge 制御スクリプトをダウンロードします。

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge は、Windows コンテナーまたは Linux コンテナーを実行できます。 Windows コンテナーを使用するには、次のいずれかを実行する必要があります。
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (ビルド 16299)
>    * x64 ベース デバイス上の Windows IoT Core (ビルド 16299)
>
> Windows IoT Core については、[Windows IoT Core への IoT Edge ランタイムのインストール][lnk-install-iotcore]に関するページの手順に従ってください。 それ以外の場合は、[Windows コンテナーを使用するように Docker を構成][lnk-docker-containers]し、必要に応じて次の PowerShell コマンドを使用して、前提条件を検証します。
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Azure CLI を使用して IoT ハブを作成する

IoT ハブを Azure サブスクリプションで作成します。 このクイック スタートでは無料レベルの IoT Hub を使用できます。 IoT Hub を以前に使用したことがあり、無料のハブを作成済みである場合は、このセクションをスキップして「[IoT Edge デバイスを登録する][anchor-register]」に進むことができます。 各サブスクリプションで使用できる無料 IoT ハブは 1 つのみです。 

1. [Azure Portal][lnk-portal] にサインインします。 
1. **[Cloud Shell]** ボタンを選択します。 

   ![[Cloud Shell] ボタン][1]

1. リソース グループを作成します。 次のコードにより、**IoTEdge** というリソース グループが **米国西部**リージョンに作成されます。

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. IoT ハブを新しいリソース グループに作成します。 次のコードにより、**MyIotHub** という無料の **F1** ハブがリソース グループ **IoTEdge** に作成されます。

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>IoT Edge ランタイムを構成する

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 2 つのモジュールから構成されます。 まず、IoT Edge エージェントは、IoT Edge デバイスでのモジュールの展開と監視を容易にします。 次に、IoT Edge ハブは、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。 

前のセクションで保存した IoT Edge デバイス接続文字列を使用してランタイムを構成します。

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、IoT Edge モジュールをプッシュし、デバイス自体を変更せずにモジュールをデバイスで実行しました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

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
## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した IoT ハブが不要になったら、[az iot hub delete][lnk-delete] コマンドを使用して、リソースとそれに関連付けられているデバイスを削除できます。

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>次の手順

IoT Edge モジュールを IoT Edge デバイスに展開する方法について学習しました。 エッジでデータを分析できるように、さまざまな種類の Azure サービスをモジュールとして展開してみてください。 

* [Azure 関数をモジュールとして展開する](tutorial-deploy-function.md)
* [Azure Stream Analytics をモジュールとして展開する](tutorial-deploy-stream-analytics.md)
* [独自のコードをモジュールとして展開する](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
