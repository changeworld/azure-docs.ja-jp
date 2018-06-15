---
title: Azure IoT Hub を使用したデバイス ファームウェアの更新 (Python) | Microsoft Docs
description: Azure IoT Hub でデバイス管理を使用してデバイス ファームウェアの更新を開始する方法。 Azure IoT SDK for Python を使用して、シミュレートされたデバイス アプリと、ファームウェアの更新をトリガーするサービス アプリを実装します。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: d2ebdf54e595c2f02464c0c2446a6e5f5feefb9c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634643"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>デバイス管理を使用してデバイス ファームウェアの更新を開始する (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

[デバイス管理の開始][lnk-dm-getstarted]に関するチュートリアルでは、[デバイス ツイン][lnk-devtwin]と[ダイレクト メソッド][lnk-c2dmethod] プリミティブを使用してリモートでデバイスを再起動する方法を説明しました。 このチュートリアルでは、同じ IoT Hub プリミティブを使用して、エンド ツー エンドでシミュレートされたファームウェア更新を実行する方法を示します。  このパターンは、Intel Edison デバイス サンプルのファームウェア更新プログラムの実装で使用されます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* シミュレート対象デバイス アプリで firmwareUpdate ダイレクト メソッドを IoT ハブ経由で呼び出す Python コンソール アプリを作成します。
* **firmwareUpdate** ダイレクト メソッドを実装するシミュレート対象デバイス アプリを作成します。 このメソッドは、ファームウェア イメージをダウンロードするまで待機し、ファームウェア イメージをダウンロードし、最後にファームウェア イメージを適用する、多段階のプロセスを開始します。 更新の各段階中、デバイスは、報告されるプロパティを使用して進捗状況を報告します。

このチュートリアルの最後には、次の 2 つの Python コンソール アプリが完成します。

**dmpatterns_fwupdate_service.py**: シミュレート対象デバイス アプリでダイレクト メソッドを呼び出し、応答を表示して、更新された報告されるプロパティを定期的に表示します (500 ミリ秒ごと)。

**dmpatterns_fwupdate_device.py**: IoT ハブと既に作成したデバイス ID をつなげ、firmwareUpdate ダイレクト メソッドを受信し、イメージのダウンロードの待機、新しいイメージのダウンロード、イメージの適用というマルチステートメント プロセスを経由してファームウェアの更新をシミュレーションします。

このチュートリアルを完了するには、以下が必要です。

* [Python 2.x または 3.x][lnk-python-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用する場合は、[*pip* (Python パッケージ管理システム) をインストールまたはアップグレード][lnk-install-pip]することが必要な場合があります。
* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ][lnk-visual-c-redist]。これは、Python からネイティブ DLL を使用できるようにするためです。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用して、デバイス上でリモート ファームウェア更新を開始する
このセクションでは、デバイスでリモート ファームウェア更新を開始する Python コンソール アプリを作成します。 アプリケーションは、ダイレクト メソッドを使用して更新を開始し、デバイス ツイン クエリを使用してアクティブなファームウェア更新の状態を定期的に取得します。

1. コマンド プロンプトで次のコマンドを実行して **azure-iothub-service-client** パッケージをインストールします。
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. テキスト エディターを使用して、作業ディレクトリで **dmpatterns_getstarted_service.py** ファイルを作成します。

1. **dmpatterns_getstarted_service.py** ファイルの先頭に、次の 'import' ステートメントと変数を追加します。 `IoTHubConnectionString` と `deviceId` を、前に説明した実際の値で置き換えます。
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. ダイレクト メソッドを呼び出して firmwareUpdate の reported プロパティの値を表示する次の関数を追加します。 `main` ルーチンも追加もします。
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. **dmpatterns_fwupdate_service.py** ファイルを保存して閉じます。


## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成
このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Python コンソール アプリを作成します。
* シミュレートされたファームウェアの更新をトリガーする
* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスと最後にファームウェアの更新が完了した日時を識別できるようにする

1. コマンド プロンプトで次のコマンドを実行して **azure-iothub-device-client** パッケージをインストールします。
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. テキスト エディターを使用して、**dmpatterns_fwupdate_device.py** ファイルを作成します。

1. **dmpatterns_fwupdate_device.py** ファイルの先頭に、次の 'import' ステートメントと変数を追加します。 `deviceConnectionString` を IoT ハブのデバイス接続文字列で置き換えます。
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. reported プロパティを更新し、ダイレクト メソッドを実装するために使用する次の関数を追加します。
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. ファームウェア イメージのダウンロードと適用をシミュレートする次の関数を追加します。
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. デバイス ツインの reported プロパティを初期化し、ダイレクト メソッドが呼び出されるまで待機する次の関数を追加します。 `main` ルーチンも追加もします。
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理](https://msdn.microsoft.com/library/hh675232.aspx)に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 


## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. 別のコマンド プロンプトで、次のコマンドを実行してデバイス ツインのリモート再起動とクエリをトリガーして最後の再起動時刻を検索します。
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。 ファームウェアの更新プログラム全体における reported プロパティの変更に注意してください。

    ![プログラムの出力][1]


## <a name="next-steps"></a>次の手順
このチュートリアルでは、ダイレクト メソッドを使用してデバイス上でリモートのファームウェア更新を開始し、報告されるプロパティを使用してファームウェア更新の進捗状況を確認しました。

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
