---
title: Azure IoT Hub デバイス ツインのプロパティの使用 (Python) | Microsoft Docs
description: Azure IoT Hub デバイス ツインを使用してデバイスを構成する方法。 Azure IoT SDK for Python を使用して、シミュレートされたデバイス アプリと、デバイス ツインを使用してデバイスの構成を変更するサービス アプリを実装します。
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: kgremban
ms.openlocfilehash: f6e002eb570ade7fc4008cc69e6042bd3dd97f7e
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>必要なプロパティを使用してデバイスを構成する (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

このチュートリアルの最後には、次の 2 つの Python コンソール アプリが完成します。

* **SimulateDeviceConfiguration.py**: 必要な構成の更新を待機し、シミュレートされた構成の更新プロセスの状態を報告する、シミュレートされたデバイス アプリです。
* **SetDesiredConfigurationAndQuery.py**: デバイス上に必要な構成を設定し、構成更新プロセスのクエリを実行する、Python バックエンド アプリです。

> [!NOTE]
> デバイス アプリケーションとバックエンド アプリの両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDKs (Azure IoT SDK)][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* [Python 2.x または 3.x][lnk-python-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用する場合は、[*pip* (Python パッケージ管理システム) をインストールまたはアップグレード][lnk-install-pip]することが必要な場合があります。
* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ][lnk-visual-c-redist]。これは、Python からネイティブ DLL を使用できるようにするためです。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[デバイス ツインの概要][lnk-twin-tutorial]に関するチュートリアルを行った場合は、既に IoT ハブと、**myDeviceId** というデバイス ID があるため、「[シミュレートされたデバイス アプリを作成する][lnk-how-to-configure-createapp]」セクションに進んでください。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>シミュレートされたデバイスのアプリを作成する
このセクションでは、**myDeviceId** としてハブに接続し、必要な構成の更新を待機して、シミュレートされた構成の更新プロセスの情報を報告する、Python コンソール アプリを作成します。

1. コマンド プロンプトで次のコマンドを使用して、**Azure IoT Python デバイス SDK** をインストールします。
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. テキスト エディターを使って、新しい **SimulateDeviceConfiguration.py** ファイルを作成します。

1. **SimulateDeviceConfiguration.py** ファイルに次のコードを追加し、**{device connection string}** プレースホルダーを、**myDeviceId** のデバイス ID の作成時にコピーしたデバイス接続文字列で置き換えます。

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    **CLIENT** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 コードではさらに、必要なプロパティで更新のハンドラーをアタッチし、configId を比較して実際の構成変更要求があることを確認するハンドラーを追加します。これはさらに、構成変更を開始するメソッドを呼び出します。 わかりやすくするため、前のコードでは初期構成にハードコーディングされた既定値を使用しています。 実際のアプリでは、おそらくローカル ストレージから構成を読み込みます。
   
   > [!IMPORTANT]
   > 目的のプロパティの変更イベントは常にデバイスの接続時に発行されるため、何らかの操作を行う前に、目的のプロパティに実際に変更点があることをかならずご確認ください。
   > 

1. **SimulateDeviceConfiguration.py** ファイルの末尾に次のコードを追加します。

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    **device_twin_callback** メソッドでは、構成の更新要求を使用してローカル デバイス ツイン オブジェクトの報告されるプロパティを更新し、_configId_ を設定します。 デバイス ツインが正常に更新された後で、更新メッセージが出力されます。 帯域幅を節約するため、報告されたプロパティの更新は、ドキュメント全体を置き換えるのではなく、変更するプロパティのみを指定して行われます (上のコードでは **TWIN_PAYLOAD**)。
   
   > [!NOTE]
   > このチュートリアルでは、同時実行の構成の更新動作はシミュレートしません。 一部の構成更新プロセスでは、更新の実行中に対象の構成に変更を加えることができますが、場合によってはキューに入れる必要がある場合や、エラー条件で拒否されることがあります。 お使いの構成プロセスに必要な動作を考慮し、構成の変更を開始する前に適切なロジックを追加するようにしてください。
   > 

1. **SimulateDeviceConfiguration.py** ファイルの末尾に次のコードを追加します。 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. デバイス アプリを実行する:
   
    ```cmd/sh
    python SimulateDeviceConfiguration.py
    ```
   
    `Device twins updated.` というメッセージが表示されます。 そのままアプリを実行します。


## <a name="create-the-service-app"></a>デバイス アプリを作成する
このセクションでは、新しいテレメトリの構成オブジェクトを使用して、**myDeviceId** に関連付けられたデバイス ツインの "*必要なプロパティ*" を更新する、Python コンソール アプリを作成します。 その後このアプリでは、IoT Hub に格納されているデバイス ツインにクエリを実行し、デバイスの必要な構成と報告される構成の違いを示します。

1. コマンド プロンプトで次のコマンドを使用して、**Azure IoT Python Service SDK** をインストールします。
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. テキスト エディターを使用して、新しい **SetDesiredAndQuery.py** ファイルを作成します。

1. **SetDesiredAndQuery.py** ファイルに次のコードを追加し、**{IoTHubConnectionString}** プレースホルダーを、ハブの作成時にコピーした IoT Hub 接続文字列で置き換え、**{deviceId}** プレースホルダーをデバイスの名前で置き換えます。

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. **SetDesiredAndQuery.py** ファイルの末尾に次のコードを追加します。

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. **SimulateDeviceConfiguration.py** が実行されている状態で、新しいコマンド プロンプトで次のコマンドを使用してアプリケーションを実行します。

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    reported 構成 ID が、24 時間ではなく 5 分の新しいアクティブな送信頻度で、**1** から **2** に変わります。


## <a name="next-steps"></a>次の手順
このチュートリアルでは、バックエンド アプリから必要な構成を "*必要なプロパティ*" として設定し、その変更を検出してデバイス ツインに "*報告されるプロパティ*" として状態を報告する更新プロセスをシミュレートするための、シミュレートされたデバイス アプリを記述しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 多数のデバイスで操作をスケジュールまたは実行するには、[ジョブのスケジュールとブロードキャスト][lnk-schedule-jobs]に関するチュートリアルをご覧ください。
* [ダイレクト メソッドの使用][lnk-methods-tutorial]に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
