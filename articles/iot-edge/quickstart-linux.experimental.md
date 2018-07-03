---
title: Azure IoT Edge on Linux のシミュレート | Microsoft Docs
description: このクイック スタートでは、事前作成されたコードを IoT Edge デバイスにリモートで展開する方法について説明します。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6b63c10a8c092d6568f8caf9842f007a5dc9c027
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049164"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>クイック スタート: 初めての IoT Edge モジュールを Linux x64 デバイスに展開する

Azure IoT Edge を使用すると、すべてのデータをクラウドにプッシュしなくても、デバイスで分析とデータ処理を実行することができます。 IoT Edge チュートリアルでは、さまざまな種類のモジュールを展開する方法について説明しますが、まずテストするデバイスが必要になります。 

このクイック スタートでは、次の方法について説明します。

1. IoT Hub を作成します。
2. IoT Edge デバイスを IoT ハブに登録する。
3. IoT Edge ランタイムを開始する。
4. モジュールを IoT Edge デバイスにリモートで展開する。

![チュートリアル アーキテクチャ][2]

このクイック スタートでは、Linux コンピューターまたは仮想マシンを IoT Edge デバイスに変えます。 その後、モジュールを Azure portal からご自身のデバイスに展開できます。 このクイック スタートで展開するモジュールは、温度、湿度、および圧力のデータを生成するシミュレートされたセンサーです。 その他の Azure IoT Edge チュートリアルは、ここで行う作業を基盤としており、ビジネスに関する分析情報を得るためにシミュレートされたデータを分析するモジュールを展開します。 

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][lnk-account]を作成してください。

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

Moby、コンテナー ランタイム、およびその CLI コマンドをインストールします。 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールして構成する

デバイスが起動するたび IoT Edge ランタイムが起動するように、セキュリティ デーモンは、システム サービスとしてインストールされます。 インストールには **hsmlib** バージョンも含まれるため、このセキュリティ デーモンは、デバイスのハードウェア セキュリティと対話できます。 

1. IoT Edge セキュリティ デーモンをダウンロードしてインストールします。 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. IoT Edge 構成ファイルを開きます。 このファイルは保護されているため、アクセスするには昇格された特権を使用する必要があります。
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. ご自身のデバイスを登録したときにコピーした IoT Edge デバイスの接続文字列を追加します。 このクイック スタートで前にコピーした変数 **device_connection_string** の値を置き換えます。

4. Edge セキュリティ デーモンを再起動します。

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

   ```bash
   iotedge list
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
   iotedge list
   ```

   ![ご利用のデバイスの 3 つのモジュールを表示する](./media/quickstart-linux/iotedge-list-2.png)

tempSensor モジュールから送信されているメッセージを確認します。

   ```bash
   iotedge logs tempSensor -f 
   ```

![モジュールからのデータを表示する](./media/quickstart-linux/iotedge-logs.png)

ログに表示されている最後の行が `Using transport Mqtt_Tcp_Only` の場合、温度センサー モジュールは、Edge ハブに接続されるのを待っている可能性があります。 モジュールを中止し、Edge エージェントによる再起動を試みてください。 中止するには、`sudo docker stop tempSensor` コマンドを使用します。

[IoT Hub エクスプローラー ツール][lnk-iothub-explorer]または [Visual Studio Code 用の Azure IoT Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、デバイスが送信している利用統計情報を表示することもできます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

IoT Edge のチュートリアルに進む場合は、このクイック スタートで登録および設定したデバイスを使用できます。 デバイスからインストールを削除するには、次のコマンドを使用します。  

IoT Edge ランタイムを削除します。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

ご自身のデバイスに作成されたコンテナーを削除します。 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

コンテナー ランタイムを削除します。

   ```bash
   sudo apt-get remove --purge moby
   ```

このクイック スタートで作成した Azure IoT ハブまたは IoT Edge デバイスが不要になった場合は、Azure portal で削除できます。 ご自身の IoT ハブの概要ページに移動し、**[削除]** を選択します。 

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しい IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスに展開しました。 その環境に関する生データを生成するシミュレートされたデバイスができあがりました。 

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
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
