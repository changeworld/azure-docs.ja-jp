---
title: Azure IoT Edge on Linux のシミュレート | Microsoft Docs
description: このクイック スタートでは、事前作成されたコードを IoT Edge デバイスにリモートで展開する方法について説明します。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: dfbe931bbe5887e9c0545558c4d2b2565718dd0a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578492"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>クイック スタート: 初めての IoT Edge モジュールを Linux x64 デバイスに展開する

Azure IoT Edge を使用すると、すべてのデータをクラウドにプッシュしなくても、デバイスで分析とデータ処理を実行することができます。 IoT Edge チュートリアルでは、さまざまな種類のモジュールをデプロイする方法について説明しますが、まずテストするデバイスが必要になります。 

このクイック スタートでは、次の方法について説明します。

1. IoT Hub を作成します。
2. IoT Edge デバイスを IoT ハブに登録します。
3. IoT Edge ランタイムを開始する。
4. モジュールを IoT Edge デバイスにリモートで展開する。

![チュートリアル アーキテクチャ][2]

このクイック スタートでは、Linux コンピューターまたは仮想マシンを IoT Edge デバイスに変えます。 その後、モジュールを Azure portal からご自身のデバイスに展開できます。 このクイック スタートで展開するモジュールは、温度、湿度、および圧力のデータを生成するシミュレートされたセンサーです。 その他の Azure IoT Edge チュートリアルは、ここで行う作業を基盤としており、ビジネスに関する分析情報を得るためにシミュレートされたデータを分析するモジュールを展開します。 

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][lnk-account]を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、Linux マシンを IoT Edge デバイスとして使用しています。 テスト用に用意していない場合は、[Azure portal で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-portal.md)方法の手順に従って作成してください。 
* Web サーバーをインストールして実行する手順に従う必要はありません。 仮想マシンに接続する手順までで止めることができます。  
* 新しいリソース グループに仮想マシンを作成します。このリソース グループは、このクイック スタートの他の Azure リソースを作成するときに使用できます。 *IoTEdgeResources* など、わかりやすい名前に変更します。 
* IoT Edge をテストするために非常に大きな仮想マシンは必要ありません。 **B1ms** などのサイズで十分です。 

## <a name="create-an-iot-hub"></a>IoT Hub の作成

このクイック スタートでは、最初に Azure portal で IoT ハブを作成します。
![IoT Hub を作成する][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT Hub に IoT Edge デバイスを登録します。
![デバイスを登録する][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールして開始する

Azure IoT Edge ランタイムをデバイスにインストールして開始します。 
![デバイスを登録する][5]

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 3 つのコンポーネントで構成されます。 **IoT Edge セキュリティ デーモン**は、Edge デバイスが起動するたびに開始され、IoT Edge エージェントを起動してデバイスをブートストラップします。 **IoT Edge エージェント**は、IoT Edge ハブなど、IoT Edge デバイス上のモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。 

このクイック スタートで準備した Linux マシンまたは VM で、次の手順を実行します。 

### <a name="register-your-device-to-use-the-software-repository"></a>デバイスを登録してソフトウェア リポジトリを使用する

IoT Edge ランタイムの実行に必要なパッケージは、ソフトウェア リポジトリで管理されます。 このリポジトリにアクセスできるようにご自身の IoT Edge デバイスを構成します。 

このセクションの手順は、**Ubuntu 16.04** が実行されているデバイスを対象としています。 その他のバージョンの Linux でソフトウェア リポジトリにアクセスするには、「[Install the Azure IoT Edge runtime on Linux (x64) (Linux (x64) で Azure IoT Edge ランタイムをインストールする)](how-to-install-iot-edge-linux.md)」または「[Install Azure IoT Edge runtime on Linux (ARM32v7/armhf) (Linux (ARM32v7/armhf) で Azure IoT Edge ランタイムをインストールする)](how-to-install-iot-edge-linux-arm.md)」を参照してください。

1. IoT Edge デバイスとして使用しているマシンで、リポジトリの構成をインストールします。

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. リポジトリにアクセスするための公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>コンテナー ランタイムをインストールする

IoT Edge ランタイムは一連のコンテナーであり、ご自身の IoT Edge デバイスに展開するロジックは、コンテナーとしてパッケージ化されています。 これらのコンポーネント用にデバイスを準備するには、コンテナー ランタイムをインストールします。

**apt-get** を更新します。

   ```bash
   sudo apt-get update
   ```

コンテナー ランタイムの **Moby** をインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

Moby の CLI コマンドをインストールします。 

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールして構成する

デバイスが起動するたび IoT Edge ランタイムが起動するように、セキュリティ デーモンは、システム サービスとしてインストールされます。 インストールには **hsmlib** バージョンも含まれるため、このセキュリティ デーモンは、デバイスのハードウェア セキュリティと対話できます。 

1. IoT Edge セキュリティ デーモンをダウンロードしてインストールします。 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. IoT Edge 構成ファイルを開きます。 このファイルは保護されているため、アクセスするには昇格された特権の使用が必要になる場合があります。
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. IoT Edge デバイスの接続文字列を追加します。 変数 **device_connection_string** を見つけて、値をデバイスの登録後にコピーした文字列に更新します。

4. ファイルを保存して閉じます。 

   `CTRL + X`、`Y`、`Enter`

4. IoT Edge セキュリティ デーモンを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

5. Edge セキュリティ デーモンがシステム サービスとして実行されていることを確認します。

   ```bash
   sudo systemctl status iotedge
   ```

   ![Edge デーモンがシステム サービスとして実行されていることを確認する](./media/quickstart-linux/iotedged-running.png)

   次のコマンドを実行して、Edge セキュリティ デーモンのログを確認することもできます。

   ```bash
   journalctl -u iotedge
   ```

6. ご自身のデバイスで実行されているモジュールを表示します。 

   >[!TIP]
   >最初に *sudo* を使用して `iotedge` コマンドを実行する必要があります。 マシンからサインアウトし、サインインし直してアクセス許可を更新すると、昇格された特権を使用せずに `iotedge` コマンドを実行できます。 

   ```bash
   sudo iotedge list
   ```

   ![ご自身のデバイス上の 1 つのモジュールを表示する](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>モジュールを展開する

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールを展開します。
![デバイスを登録する][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、IoT Edge モジュールをプッシュし、デバイス自体を変更せずにモジュールをデバイスで実行しました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

シミュレートされたデバイスを実行しているコンピューターで、もう一度コマンド プロンプトを開きます。 IoT Edge デバイスで、クラウドからデプロイされたモジュールが実行されていることを確認します。

   ```bash
   sudo iotedge list
   ```

   ![ご利用のデバイスの 3 つのモジュールを表示する](./media/quickstart-linux/iotedge-list-2.png)

tempSensor モジュールから送信されているメッセージを確認します。

  ```bash
   sudo iotedge logs tempSensor -f 
   ```

一度ログオフしてからログインすると、上記のコマンドで *sudo* は不要になります。

![モジュールからのデータを表示する](./media/quickstart-linux/iotedge-logs.png)

ログに表示されている最後の行が `Using transport Mqtt_Tcp_Only` の場合、温度センサー モジュールは、Edge ハブに接続されるのを待っている可能性があります。 モジュールを中止し、Edge エージェントによる再起動を試みてください。 中止するには、`sudo docker stop tempSensor` コマンドを使用します。

[Visual Studio Code 用の Azure IoT Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、デバイスが送信している利用統計情報を表示することもできます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

IoT Edge のチュートリアルに進む場合は、このクイック スタートで登録および設定したデバイスを使用できます。 それ以外の場合は、作成した Azure リソースを削除し、デバイスから IoT Edge ランタイムを削除することができます。 

### <a name="delete-azure-resources"></a>Azure リソースを削除する

新しいリソース グループで仮想マシンと IoT ハブを作成した場合、そのグループと関連するすべてのリソースを削除できます。 リソース グループ内に残したいリソースがある場合は、クリーン アップする個々のリソースのみを削除します。 

リソース グループを削除するには、次の手順を実行します。 

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。
2. **[名前でフィルター処理してください]** ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。 
3. 結果一覧のでリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。
4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再度入力し、**[削除]** をクリックします。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

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
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しい IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスにデプロイしました。 その環境に関する生データを生成するシミュレートされたデバイスができあがりました。 

このクイック スタートは、すべての IoT Edge チュートリアルの前提条件です。 引き続きいずれかのチュートリアルを実行し、Azure IoT Edge が、エッジでこのデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Azure Function を使用してセンサー データをフィルター処理する](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-account]: https://azure.microsoft.com/free
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
