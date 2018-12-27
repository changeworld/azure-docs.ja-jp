---
title: Linux 上に Azure IoT Edge デバイスを作成するクイック スタート | Microsoft Docs
description: このクイック スタートでは、IoT Edge デバイスを作成した後、Azure portal から事前作成されたコードをリモートで展開する方法を学習します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1bc7425d1979b2e1a35884c0800117455aebe9b6
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338059"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>クイック スタート:初めての IoT Edge モジュールを Linux x64 デバイスに展開する

Azure IoT Edge は、クラウドの機能をご使用のモノのインターネット (IoT) デバイスでも利用できるようにします。 このクイック スタートでは、クラウド インターフェイスを使用して、事前作成されたコードを IoT Edge デバイスにリモートで展開する方法について説明します。

このクイック スタートでは、次の方法について説明します。

1. IoT Hub を作成します。
2. IoT Edge デバイスを IoT ハブに登録します。
3. IoT Edge ランタイムをご自身のデバイスにインストールして開始する。
4. モジュールを IoT Edge デバイスにリモートで展開する。

![図 - デバイスとクラウドのクイック スタートのアーキテクチャ](./media/quickstart-linux/install-edge-full.png)

このクイック スタートでは、Linux コンピューターまたは仮想マシンを IoT Edge デバイスに変えます。 その後、モジュールを Azure portal からご自身のデバイスに展開できます。 このクイック スタートで展開するモジュールは、温度、湿度、および圧力のデータを生成するシミュレートされたセンサーです。 その他の Azure IoT Edge チュートリアルは、ここで行う作業を基盤としており、ビジネスに関する分析情報を得るためにシミュレートされたデータを分析するモジュールを展開します。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このクイック スタートの多くの手順は、Azure CLI を使用して実行します。Azure IoT には、追加機能を有効にする拡張機能が用意されています。 

Azure IoT の拡張機能を Cloud Shell インスタンスに追加します。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>前提条件

クラウド リソース: 

* このクイック スタートで使用するすべてのリソースを管理するためのリソース グループです。 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge デバイス: 

* IoT Edge デバイスとして機能する Linux デバイスまたは仮想マシン。 Microsoft 提供の [Azure IoT Edge を Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) 仮想マシン上で使用することをお勧めします。これによって、IoT Edge ランタイムが事前にインストールされます。 次のコマンドを使用してこの仮想マシンを作成します。

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   新しい仮想マシンを作成するときは、create コマンドの出力内容に含まれる **publicIpAddress** を書き留めておいてください。 このクイック スタートの中で、後からこのパブリック IP アドレスを使用して仮想マシンに接続します。

* Azure IoT Edge ランタイムをローカル システム上で実行したい場合は、「[Linux に Azure IoT Edge ランタイムをインストールする (x64)](how-to-install-iot-edge-linux.md)」の手順に従います。

* Raspberry Pi など ARM32 ベースのデバイスを使用したい場合は、「[Linux に Azure IoT Edge ランタイムをインストールする (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)」の手順に従います。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

このクイック スタートでは、最初に Azure CLI で IoT Hub を作成します。

![図 - クラウドで IoT ハブを作成する](./media/quickstart-linux/create-iot-hub.png)

このクイック スタートでは無料レベルの IoT Hub を使用できます。 IoT Hub を以前に使用したことがあり、無料のハブを作成済みである場合は、その IoT ハブを使用できます。 各サブスクリプションで使用できる無料 IoT ハブは 1 つのみです。 

次のコードにより、無料の **F1** ハブがリソース グループ **IoTEdgeResources** に作成されます。 *{hub_name}* は、IoT ハブの一意の名前に置き換えてください。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   サブスクリプションに無料のハブが既に 1 つあるためにエラーが発生する場合は、SKU を **S1** に変更します。 IoT ハブの名前が利用できないというエラーが発生した場合、自分以外のだれかが既にその名前のハブを所有していることを意味します。 新しい名前を試してください。 

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT ハブに IoT Edge デバイスを登録します。
![図 - IoT ハブ ID でデバイスを登録する](./media/quickstart-linux/register-device.png)

お使いの IoT ハブと通信できるようにシミュレートされたデバイスのデバイス ID を作成します。 デバイス ID はクラウドに置かれるので、デバイスの一意の接続文字列を使用して、物理デバイスとデバイス ID とを関連付けることになります。 

IoT Edge デバイスは、一般的な IoT デバイスとは異なる動作をし、別に管理できるため、この ID は IoT Edge デバイス用として `--edge-enabled` フラグで宣言します。 

1. Azure Cloud Shell で、次のコマンドを入力して、**myEdgeDevice** という名前のデバイスをハブに作成します。

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   iothubowner ポリシー キーに関するエラーが表示された場合は、Cloud Shell で最新バージョンの azure-cli-iot-ext 拡張機能が実行されていることを確認してください。 

2. デバイスの接続文字列を取得します。この接続文字列により、IoT Hub 内で物理デバイスとその ID をリンクさせます。 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. 接続文字列をコピーして保存します。 次のセクションで、IoT Edge ランタイムを構成するときにこの値を使用します。 

## <a name="connect-the-iot-edge-device-to-iot-hub"></a>IoT Edge デバイスを IoT Hub に接続する

Azure IoT Edge ランタイムを IoT Edge デバイスにインストールして開始します。 
![図 - デバイスでランタイムを開始する](./media/quickstart-linux/start-runtime.png)

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 3 つのコンポーネントで構成されます。 **IoT Edge セキュリティ デーモン**は、Edge デバイスが起動するたびに開始され、IoT Edge エージェントを起動してデバイスをブートストラップします。 **IoT Edge エージェント**は、IoT Edge ハブなど、IoT Edge デバイス上のモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。 

ランタイムの構成中に、デバイスの接続文字列を入力します。 Azure CLI から取得した文字列を使用してください。 この文字列によって、Azure 内の IoT Edge デバイス ID と物理デバイスとが関連付けられます。 

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>IoT Edge デバイスに接続文字列を設定する

* Azure IoT Edge を Ubuntu 仮想マシンで使用している場合は、前にコピーしたデバイス接続文字列を使用して、IoT Edge デバイスをリモートで構成します。

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

   この後の手順では、作成コマンドによって出力されたパブリック IP アドレスを取得します。 パブリック IP アドレスは、Azure portal の仮想マシンの概要ページでも確認できます。 次のコマンドを使用して、仮想マシンに接続します。 **{publicIpAddress}** は、実際のマシンのアドレスに置き換えてください。 

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

* ローカル マシンまたは ARM32 デバイス上で IoT Edge を実行している場合は、/etc/iotedge/config.yaml にある構成ファイルを開いて、**device_connection_string** 変数を前にコピーした値で更新します。その後、IoT Edge セキュリティ デーモンを再起動して、変更内容を適用します。

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>`iotedge` コマンドの実行には、昇格された特権が必要です。 IoT Edge ランタイムのインストール後に初めてマシンにサインインし直すと、アクセス許可は自動的に更新されます。 それまでは、コマンドの前に **sudo** を使用します。 

### <a name="view-the-iot-edge-runtime-status"></a>IoT Edge ランタイムの状態を確認する

ランタイムが正常にインストールされ、構成されていることを確認します。

1. Edge セキュリティ デーモンがシステム サービスとして実行されていることを確認します。

   ```bash
   sudo systemctl status iotedge
   ```

   ![Edge デーモンがシステム サービスとして実行されていることを確認する](./media/quickstart-linux/iotedged-running.png)

2. サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。 

   ```bash
   journalctl -u iotedge
   ```

3. ご自身のデバイスで実行されているモジュールを表示します。 

   ```bash
   sudo iotedge list
   ```

   ![ご自身のデバイス上の 1 つのモジュールを表示する](./media/quickstart-linux/iotedge-list-1.png)

IoT Edge デバイスの構成はこれで完了です。 クラウドからモジュールをデプロイして実行することができます。 

## <a name="deploy-a-module"></a>モジュールを展開する

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールを展開します。
![図 - クラウドからデバイスにモジュールを展開する](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、IoT Edge モジュールをプッシュし、デバイス自体を変更せずにモジュールをデバイスで実行しました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。

再び IoT Edge デバイスでコマンド プロンプトを開くか、Azure CLI から SSH 接続を使用します。 IoT Edge デバイスで、クラウドからデプロイされたモジュールが実行されていることを確認します。

   ```bash
   sudo iotedge list
   ```

   ![ご利用のデバイスの 3 つのモジュールを表示する](./media/quickstart-linux/iotedge-list-2.png)

tempSensor モジュールから送信されているメッセージを確認します。

   ```bash
   sudo iotedge logs tempSensor -f
   ```

![モジュールからのデータを表示する](./media/quickstart-linux/iotedge-logs.png)

ログに表示されている最後の行が `Using transport Mqtt_Tcp_Only` の場合、温度センサー モジュールは、Edge ハブに接続されるのを待っている可能性があります。 モジュールを中止し、Edge エージェントによる再起動を試みてください。 中止するには、`sudo docker stop tempSensor` コマンドを使用します。

[Visual Studio Code 用の Azure IoT Hub Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (旧称: Azure IoT Toolkit 拡張機能) を使用して、IoT ハブに到着したメッセージを監視することもできます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

IoT Edge のチュートリアルに進む場合は、このクイック スタートで登録および設定したデバイスを使用できます。 それ以外の場合は、作成した Azure リソースを削除し、デバイスから IoT Edge ランタイムを削除することができます。

### <a name="delete-azure-resources"></a>Azure リソースを削除する

新しいリソース グループで仮想マシンと IoT ハブを作成した場合、そのグループと関連するすべてのリソースを削除できます。 リソース グループの内容を再確認して、残しておくべきものがないことを確認してください。 グループ全体を削除したくない場合は、リソースを個別に削除してもかまいません。

**IoTEdgeResources** グループを削除します。

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>IoT Edge ランタイムを削除する

デバイスからインストールを削除するには、次のコマンドを使用します。  

IoT Edge ランタイムを削除します。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示します。

   ```bash
   sudo docker ps -a
   ```

IoT Edge ランタイムによってデバイス上に作成されたコンテナーを削除します。 tempSensor コンテナーを別の名前で呼ぶ場合は、名前を変更します。 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

コンテナー ランタイムを削除します。

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>次の手順

このクイック スタートは、すべての IoT Edge チュートリアルの前提条件です。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでこのデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Azure Function を使用してセンサー データをフィルター処理する](tutorial-deploy-function.md)
