---
title: Azure IoT Hub (ノード) を使用したメッセージのルーティング | Microsoft Docs
description: 他のバックエンド サービスにメッセージをディスパッチするルーティング規則とカスタム エンドポイントを使用して、Azure IoT Hub デバイスからクラウドへのメッセージを処理する方法について説明します。
services: iot-hub
documentationcenter: node
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: 02edb06d2d871cffac717358e33a6720c444a9b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>IoT Hub (ノード) でのメッセージのルーティング

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

このチュートリアルは、チュートリアル「[SimulatedDevice]」に基づいています。  このチュートリアルでは以下を行います。

* ルーティング規則を使用して、簡単な構成ベースの方法でデバイスからクラウドにメッセージをディスパッチする方法について説明します。
* さらに処理するために早急な対応を必要とする対話型メッセージを、ソリューションのバックエンドから分離する方法を示します。  たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。  これに対して、温度テレメトリなどのデータポイント メッセージは、分析エンジンにフィードされます。

このチュートリアルの最後に、次の 3 つの Node.js コンソール アプリを実行します。

* **SimulatedDevice.js** は [SimulatedDevice] に関するチュートリアルで作成したアプリを変更したもので、デバイスからクラウドへのデータ ポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージをランダムな間隔で送信します。 このアプリでは、IoT Hub との通信に MQTT プロトコルを使用します。
* **ReadDeviceToCloudMessages.js** は、デバイス アプリから送信されたテレメトリを表示します。
* **ReadCriticalQueue.js** は、IoT hub に接続された Service Bus キューから重大なメッセージをデキューします。

> [!NOTE]
> IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのデバイスを物理デバイスに置き換える方法と、IoT Hub にデバイスを接続する方法の手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルを完了するには、以下が必要です。

* [SimulatedDevice] に関するチュートリアルで完成させたアプリ。
* Node.js バージョン 4.0.x 以降。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[Azure Storage] と [Azure Service Bus] の記事も読むことをお勧めします。

## <a name="send-interactive-messages-from-a-device-app"></a>デバイス アプリからの対話型メッセージの送信
このセクションでは、[SimulatedDevice]に関するチュートリアルで作成したデバイス アプリを変更して、すぐに処理する必要があるメッセージをランダムに送信するようにします。

1. テキスト エディターを使用して **simulateddevice\SimulatedDevice.js** ファイルを開きます。 このファイルには、 **IoT Hub の概要** のチュートリアルで作成した [SimulatedDevice] アプリのコードが含まれています。

2. **connectCallback** 関数内のコードを次のコードに置き換えます。

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    このメソッドは、デバイスによって送信されたメッセージに `"level": "critical"` と `"level": "storage"` のプロパティをランダムに追加して、アプリケーションのバックエンドによる早急な対応を必要とするメッセージまたは永続的に保存する必要があるメッセージをシミュレートします。 アプリケーションは、メッセージ本文に基づいたメッセージのルーティングをサポートしています。
   
   > [!NOTE]
   > メッセージのプロパティを使用したメッセージのルーティングは、ここで示すホット パスの例に加え、コールド パス処理などのさまざまなシナリオで使用できます。

2. **simulateddevice\SimulatedDevice.js** ファイルを保存して閉じます。

    > [!NOTE]
    > MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することを強くお勧めします。

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>IoT hub への Service Bus キューの追加とキューへのメッセージのルーティング

このセクションでは、Service Bus キューを作成して IoT hub に接続し、メッセージのプロパティの有無に基づいてこのキューにメッセージを送信するように IoT hub を構成します。 Service Bus キューのメッセージを処理する方法の詳細については、「[Service Bus キューの使用][lnk-sb-queues-node]」を参照してください。

1. 「[Service Bus キューの使用][lnk-sb-queues-node]」の説明に従って、Service Bus キューを作成します。 名前空間とキューの名前をメモしておきます。

    > [!NOTE]
    > IoT Hub エンドポイントとして使用される Service Bus のキューとトピックでは、**セッション**または**重複データ検出**が有効になっていてはいけません。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に**到達不能**として表示されます。

2. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。

    ![IoT Hub 内のエンドポイント][30]

3. **[エンドポイント]** ブレードで、上部にある **[追加]** をクリックして、IoT Hub にキューを追加します。 エンドポイントに「**CriticalQueue**」という名前を付け、ドロップダウン リストを使用して、**Service Bus キュー**、キューを配置する Service Bus 名前空間、およびキューの名前を選択します。 完了したら、下部にある **[OK]** をクリックします。  

    ![エンドポイントの追加][31]

4. 次に、IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 データのソースとして **[デバイス メッセージ]** を選択します。 条件として「`level="critical"`」を入力し、ルーティング規則エンドポイントとしてカスタム エンドポイントの **CriticalQueue** を選択します。 下部にある **[保存]** をクリックします。  

    ![ルートの追加][32]

    フォールバック ルートが **[オン]** に設定されていることを確認します。 この設定は IoT Hub の既定の構成です。

    ![フォールバック ルート][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(省略可能) キュー エンドポイントからの読み取り

このセクションでは、重大なメッセージを IoT Service Bus から読み込む Node.js コンソール アプリケーションを作成します。 詳細については、[キューの使用][lnk-sb-queues-node]に関するページを参照してください。 

1. `readcriticalqueue` という名前の空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、`readcriticalqueue` フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

2. コマンド プロンプトで `readcriticalqueue` フォルダーに移動し、次のコマンドを実行して **azure** パッケージをインストールします。

    ```cmd/sh
    npm install azure --save
    ```

3. テキスト エディターを使用して、`readcriticalqueue` フォルダーに **ReadCriticalQueue.js** ファイルを作成します。

4. **ReadCriticalQueue.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. 次の変数宣言を追加し、プレースホルダーの値を IoT Service Bus 接続文字列とキュー名に置き換えます。

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. コンソールに出力する次の 2 つの関数を追加します。

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. **ReadCriticalQueue.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、3 つのアプリケーションを実行する準備が整いました。

1. **ReadDeviceToCloudMessages.js** アプリケーションを実行するには、コマンド プロンプトまたはシェルで readdevicetocloudmessages フォルダーに移動し、次のコマンドを実行します。

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![read-d2c-messages の実行][readd2c]

2. **ReadCriticalQueue.js** アプリケーションを実行するには、コマンド プロンプトまたはシェルで readdevicetocloudmessages フォルダーに移動し、次のコマンドを実行します。

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![read-critical-messages の実行][readqueue]

3. コマンド プロンプトまたはシェルで simulated-device フォルダーに移動して次のコマンドを実行し、**SimulatedDevice.js** アプリを実行します。

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![simulateddevice][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(省略可能) IoT ハブにストレージ コンテナーを追加してメッセージをルーティングする

このセクションでは、ストレージ アカウントを作成して IoT Hub に接続し、メッセージのプロパティの有無に基づいてこのアカウントにメッセージを送信するように IoT Hub を構成します。 ストレージを管理する方法の詳細については、[Azure Storage の概要][Azure Storage]に関する記事をご覧ください。

 > [!NOTE]
   > **エンドポイント**が 1 つに制限されていない場合は、**StorageContainer** と **CriticalQueue** を設定して両方を同時に実行します。

1. [Azure Storage のドキュメント][lnk-storage]の説明に従ってストレージ アカウントを作成します。 アカウント名をメモします。

2. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。

3. **[エンドポイント]** ブレードで **CriticalQueue** エンドポイントを選択し、**[削除]** をクリックします。 **[はい]** をクリックし、**[追加]** をクリックします。 エンドポイントに「**StorageContainer**」と名前を付け、ドロップダウンを使用して **[Azure Storage コンテナー]** を選択し、**[ストレージ アカウント]** と **[ストレージ コンテナー]** を作成します。  名前をメモします。  完了したら、下部にある **[OK]** をクリックします。 

 > [!NOTE]
   > **エンドポイント**が 1 つに制限されていない場合は、**CriticalQueue** を削除する必要はありません。

4. IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 データのソースとして **[デバイス メッセージ]** を選択します。 条件として「`level="storage"`」を入力し、ルーティング規則エンドポイントとしてカスタム エンドポイントの **StorageContainer** を選択します。 下部にある **[保存]** をクリックします。  

    フォールバック ルートが **[オン]** に設定されていることを確認します。 この設定は IoT Hub の既定の構成です。

1. 前のアプリ、**SimulatedDevice.js** がまだ実行されていることを確認します。 

1. Azure Portal でストレージ アカウントに移動し、**[Blob service]** の **[BLOB の参照...]** をクリックします。お使いのコンテナーを選択して移動し、JSON ファイルをクリックします。次に **[ダウンロード]** をクリックしてデータを表示します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のメッセージ ルーティング機能を使用して、デバイスからクラウドへのメッセージを確実にディスパッチする方法について説明しました。

[IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d]に関するページでは、ソリューション バックエンドからデバイスにメッセージを送信する方法を説明しています。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT Suite][lnk-suite] に関するドキュメントを参照してください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

IoT Hub でのメッセージのルーティングの詳細については、[IoT Hub でのメッセージの送受信][lnk-devguide-messaging]に関するページを参照してください。

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[SimulatedDevice]: iot-hub-node-node-getstarted.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Transient Fault Handling (一時的な障害の処理) (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/