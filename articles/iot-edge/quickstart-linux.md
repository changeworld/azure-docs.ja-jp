---
title: "Azure IoT Edge + Linux クイック スタート | Microsoft Docs"
description: "シミュレートされたエッジ デバイスで分析を実行して Azure IoT Edge を試します"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb93efcf00cb7b165c497d7ef38685f80bce84c0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>クイック スタート: 初めての IoT Edge モジュールを Azure Portal から Linux デバイスに展開する - プレビュー

Azure IoT Edge は、クラウドの機能をご使用のモノのインターネット (IoT) デバイスでも利用できるようにします。 このトピックでは、クラウド インターフェイスを使用して、事前作成されたコードを IoT Edge デバイスにリモートで展開する方法について説明します。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][lnk-account]を作成してください。

## <a name="prerequisites"></a>前提条件

この作業を完了するには、コンピューターまたは仮想マシンを使用してモノのインターネット デバイスをシミュレートします。 IoT Edge デバイスを正しく展開するには、以下のサービスが必要です。

- [Docker を Linux にインストールし][lnk-docker-ubuntu]、稼働していることを確認します。 
- Ubuntu を含め、多くの Linux ディストリビューションには既に Python 2.7 がインストールされています。 次のコマンドを使用して、pip がインストールされていることを確認してください: `sudo apt-get install python-pip`

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

お使いの IoT ハブと通信できるように、シミュレートされたデバイスのデバイス ID を作成します。 IoT Edge デバイスは、一般的な IoT デバイスとは異なる動作をし、別々に管理できるので、最初から IoT Edge デバイスであることを宣言します。 

1. Azure Portal で、IoT ハブに移動します。
1. **[IoT Edge (preview)]\(IoT Edge (プレビュー)\)** を選択します。
1. **[Add IoT Edge device]\(IoT Edge デバイスの追加\)** を選択します。
1. シミュレートされたデバイスに一意のデバイス ID を付与します。
1. **[保存]** を選択して、お使いのデバイスを追加します。
1. デバイスの一覧から、お使いの新しいデバイスを選択します。 
1. **[接続文字列 - 主キー]** の値をコピーして保存します。 この値は、次のセクションで IoT Edge ランタイムを構成するために使用します。 

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールして開始する

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 2 つのモジュールから構成されます。 まず、IoT Edge エージェントは、IoT Edge デバイスでのモジュールの展開と監視を容易にします。 次に、IoT Edge ハブは、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。 

IoT Edge デバイスを実行するマシン上に、IoT Edge 制御スクリプトをダウンロードします。
```python
sudo pip install -U azure-iot-edge-runtime-ctl
```

前のセクションで保存した IoT Edge デバイス接続文字列を使用してランタイムを構成します。
```python
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

ランタイムを開始します。
```python
sudo iotedgectl start
```

Docker を調べて、IoT Edge エージェントがモジュールとして実行されていることを確認します。
```python
sudo docker ps
```

## <a name="deploy-a-module"></a>モジュールを展開する

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、IoT Edge モジュールをプッシュし、デバイス自体を変更せずにモジュールをデバイスで実行しました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

tempSensor モジュールから送信されているメッセージを確認します。

```cmd/sh
sudo docker logs -f tempSensor
```

また、[IoT Hub エクスプローラー ツール][lnk-iothub-explorer]を使用して、デバイスが送信しているテレメトリを表示することもできます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した IoT ハブが不要になったら、[az iot hub delete][lnk-delete] コマンドを使用して、リソースとそれに関連付けられているデバイスを削除できます。

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>次のステップ

IoT Edge モジュールを IoT Edge デバイスに展開する方法について学習しました。 エッジでデータを分析できるように、さまざまな種類の Azure サービスをモジュールとして展開してみてください。 

* [独自のコードをモジュールとして展開する](tutorial-csharp-module.md)
* [Azure 関数をモジュールとして展開する](tutorial-deploy-function.md)
* [Azure Stream Analytics をモジュールとして展開する](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
