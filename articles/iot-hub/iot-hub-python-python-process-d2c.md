---
title: Azure IoT Hub を使用したメッセージのルーティング (Python) | Microsoft Docs
description: 他のバックエンド サービスにメッセージをディスパッチするルーティング規則とカスタム エンドポイントを使用して、Azure IoT Hub デバイスからクラウドへのメッセージを処理する方法について説明します。
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 98216328e970af5f8acf04c7e7c3ca855f7065f1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>IoT Hub でのメッセージのルーティング (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

このチュートリアルは、チュートリアル「[IoT Hub の概要]」に基づいています。  このチュートリアルでは以下を行います。

* ルーティング規則を使用して、簡単な構成ベースの方法でデバイスからクラウドにメッセージをディスパッチする方法について説明します。
* さらに処理するために早急な対応を必要とする対話型メッセージを、ソリューションのバックエンドから分離する方法を示します。  たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。  これに対して、温度テレメトリなどのデータポイント メッセージは、分析エンジンにフィードされます。

このチュートリアルの最後に、次の 3 つの Python コンソール アプリを実行します。

* **SimulatedDevice.py** は [IoT Hub の概要] に関するチュートリアルで作成したアプリを変更したもので、デバイスからクラウドへのデータ ポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージをランダムな間隔で送信します。 このアプリでは、IoT Hub との通信に MQTT プロトコルを使用します。
* **ReadCriticalQueue.py** は、IoT hub に接続された Service Bus キューから重大なメッセージをデキューします。

> [!NOTE]
> IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのデバイスを物理デバイスに置き換える方法と、IoT Hub にデバイスを接続する方法の手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルを完了するには、以下が必要です。

* [IoT Hub の概要] に関するチュートリアルで完成させたアプリ。
* [Python 2.x または 3.x][lnk-python-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用する場合は、[*pip* (Python パッケージ管理システム) をインストールまたはアップグレード][lnk-install-pip]することが必要な場合があります。
* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ][lnk-visual-c-redist]。これは、Python からネイティブ DLL を使用できるようにするためです。
* [Node.js 4.0 以降][lnk-node-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 これは、[IoT Hub エクスプローラー ツール][lnk-iot-hub-explorer]をインストールするために必要です。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[Azure Storage] と [Azure Service Bus] の記事も読むことをお勧めします。


## <a name="send-interactive-messages-from-a-device-app"></a>デバイス アプリからの対話型メッセージの送信
このセクションでは、[IoT Hub の概要]に関するチュートリアルで作成したデバイス アプリを変更して、すぐに処理する必要があるメッセージをランダムに送信するようにします。

1. テキスト エディターを使用して **SimulatedDevice.py** ファイルを開きます。 このファイルには、 [IoT Hub の概要] のチュートリアルで作成した **SimulatedDevice** アプリのコードが含まれています。

2. **iothub_client_telemetry_sample_run** 関数を次のコードに置き換えます。

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    このメソッドは、デバイスによって送信されたメッセージに `"level": "critical"` と `"level": "storage"` のプロパティをランダムに追加して、アプリケーションのバックエンドによる早急な対応を必要とするメッセージまたは永続的に保存する必要があるメッセージをシミュレートします。 アプリケーションは、メッセージ本文に基づいたメッセージのルーティングをサポートしています。
   
   > [!NOTE]
   > メッセージのプロパティを使用したメッセージのルーティングは、ここで示すホット パスの例に加え、コールド パス処理などのさまざまなシナリオで使用できます。

1. **SimulatedDevice.py** ファイルを保存し、閉じます。

    > [!NOTE]
    > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>IoT hub へのキューの追加とキューへのメッセージのルーティング

このセクションでは、Service Bus キューとストレージ アカウントの両方を作成して IoT hub に接続し、メッセージのプロパティの有無に基づいてこのキューにメッセージを送信し、すべてのメッセージをストレージ アカウントに送信するように IoT hub を構成します。 Service Bus キューのメッセージを処理する方法については、[キューの概要][lnk-sb-queues-node]に関するページを参照してください。また、ストレージの管理方法については、[Azure Storage の概要][Azure Storage] に関するページを参照してください。

1. 「[Service Bus キューの使用][lnk-sb-queues-node]」の説明に従って、Service Bus キューを作成します。 名前空間とキューの名前をメモしておきます。

    > [!NOTE]
    > IoT Hub エンドポイントとして使用される Service Bus のキューとトピックでは、**セッション**または**重複データ検出**が有効になっていてはいけません。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に**到達不能**として表示されます。

1. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。

    ![IoT Hub 内のエンドポイント][30]

1. **[エンドポイント]** ブレードで、上部にある **[追加]** をクリックして、IoT Hub にキューを追加します。 エンドポイントに「**CriticalQueue**」という名前を付け、ドロップダウン リストを使用して、**Service Bus キュー**、キューを配置する Service Bus 名前空間、およびキューの名前を選択します。 完了したら、下部にある **[OK]** をクリックします。  

    ![エンドポイントの追加][31]

1. 次に、IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 

    ![ルートの追加][34]

    データのソースとして名前を入力し、**[デバイス メッセージ]** を選択します。 ルーティング規則エンドポイントとしてカスタム エンドポイントの **CriticalQueue** を選択し、クエリ文字列として `level="critical"` を入力します。 下部にある **[保存]** をクリックします。

    ![ルートの詳細][32]

    フォールバック ルートが **[オン]** に設定されていることを確認します。 この設定は IoT Hub の既定の構成です。

    ![フォールバック ルート][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(省略可能) キュー エンドポイントからの読み取り

このセクションでは、重大なメッセージを IoT Service Bus から読み込む Python コンソール アプリケーションを作成します。 詳細については、[キューの使用][lnk-sb-queues-node]に関するページを参照してください。 

1. 新しいコマンド プロンプトを開き、次のように Azure IoT Hub Device SDK for Python をインストールします。 インストールしたら、コマンド プロンプトを閉じます。

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > **azure-servicebus** パッケージのインストールに関する問題や、その他のインストール オプションについては、[Python azure-servicebus パッケージ][lnk-python-service-bus]を参照してください。

1. **ReadCriticalQueue.py** というファイルを作成します。 そのファイルを任意の Python エディターまたは IDE (IDLE など) で開きます。

1. Device SDK から必要なモジュールをインポートする次のコードを追加します。

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. 次のコードを追加し、プレースホルダーを Service Bus の接続データに置き換えます。

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. 次のコードを追加し、Service Bus に接続して読み取ります。 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. 最後に、main 関数を追加します。 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. **ReadCriticalQueue.py** ファイルを保存して閉じます。 これで、アプリケーションを実行する準備が整いました。


## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトを開き、IoT Hub エクスプローラーをインストールします。 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. コマンド プロンプトで次のコマンドを実行して、デバイスからクラウドへのメッセージの監視を開始します。 `--login` の後のプレースホルダーには、IoT ハブの接続文字列を使用します。

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. 新しいコマンド プロンプトを開き、**SimulatedDevice.py** ファイルが含まれているディレクトリに移動します。

1. **SimulatedDevice.py** ファイルを実行します。これにより、テレメトリ データが IoT ハブに定期的に送信されるようになります。 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. **ReadCriticalQueue** アプリケーションを実行するには、コマンド プロンプトまたはシェルで **ReadCriticalQueue.py** ファイルに移動し、次のコマンドを実行します。

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. 前のセクションで IoT Hub エクスプローラーを実行したコマンド プロンプトでデバイス メッセージを確認します。 **ReadCriticalQueue** アプリケーションの `critical` メッセージを観察します。

    ![Python でのデバイスからクラウドへのメッセージ][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(省略可能) IoT ハブにストレージ コンテナーを追加してメッセージをルーティングする

このセクションでは、ストレージ アカウントを作成して IoT Hub に接続し、メッセージのプロパティの有無に基づいてこのアカウントにメッセージを送信するように IoT Hub を構成します。 ストレージを管理する方法の詳細については、[Azure Storage の概要][Azure Storage]に関する記事をご覧ください。

 > [!NOTE]
   > _F1 Free_ レベルで作成される IoT Hub アカウントは、1 つの**エンドポイント**に限定されます。 **エンドポイント**が 1 つに制限されていない場合は、**StorageContainer** と **CriticalQueue** を設定して両方を同時に実行します。

1. [Azure Storage のドキュメント][lnk-storage]の説明に従ってストレージ アカウントを作成します。 アカウント名をメモします。

2. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。

3. **[エンドポイント]** ブレードで **CriticalQueue** エンドポイントを選択し、**[削除]** をクリックします。 **[はい]** をクリックし、**[追加]** をクリックします。 エンドポイントに「**StorageContainer**」と名前を付け、ドロップダウンを使用して **[Azure Storage コンテナー]** を選択し、**[ストレージ アカウント]** と **[ストレージ コンテナー]** を作成します。  名前をメモします。  完了したら、下部にある **[OK]** をクリックします。 

 > [!NOTE]
   > _F1 Free_ レベルで作成される IoT Hub アカウントは、1 つの**エンドポイント**に限定されます。 **エンドポイント**が 1 つに制限されていない場合は、**CriticalQueue** を削除する必要はありません。

4. IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 データのソースとして **[デバイス メッセージ]** を選択します。 条件として「`level="storage"`」を入力し、ルーティング規則エンドポイントとしてカスタム エンドポイントの **StorageContainer** を選択します。 下部にある **[保存]** をクリックします。  

    フォールバック ルートが **[オン]** に設定されていることを確認します。 この設定は IoT Hub の既定の構成です。

1. 前のアプリケーションの **SimulatedDevice.py** がまだ実行されていることを確認します。 

1. Azure Portal でストレージ アカウントに移動し、**[Blob service]** の **[BLOB の参照]** をクリックします。お使いのコンテナーを選択して移動し、JSON ファイルをクリックします。次に **[ダウンロード]** をクリックしてデータを表示します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のメッセージ ルーティング機能を使用して、デバイスからクラウドへのメッセージを確実にディスパッチする方法について説明しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ][lnk-suite]に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

IoT Hub でのメッセージのルーティングの詳細については、[IoT Hub でのメッセージの送受信][lnk-devguide-messaging]に関するページを参照してください。

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[IoT Hub の概要]: iot-hub-python-getstarted.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot

[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus
