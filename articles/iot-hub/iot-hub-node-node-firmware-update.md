---
title: "ファームウェア更新の実行方法 | Microsoft Docs"
description: "このチュートリアルでは、ファームウェアの更新プログラムを実行する方法を示します"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 632b9b38808e033b1fee2676b353f2649c4a282c


---
# <a name="tutorial-how-to-do-a-firmware-update"></a>チュートリアル: ファームウェアを更新する方法
## <a name="introduction"></a>はじめに
[デバイス管理の開始][lnk-dm-getstarted]に関するチュートリアルでは、[デバイス ツイン][lnk-devtwin]と[ダイレクト メソッド][lnk-c2dmethod] プリミティブを使用してリモートでデバイスを再起動する方法を説明しました。 このチュートリアルでは、同じ IoT Hub プリミティブを使用して、エンド ツー エンドでシミュレートされたファームウェア更新を実行する方法を示します。  このパターンは、Intel Edison デバイス サンプルのファームウェア更新プログラムの実装で使用されます。

このチュートリアルでは、次の操作方法について説明します。

* シミュレートされたデバイス アプリで firmwareUpdate ダイレクト メソッドを IoT ハブ経由で呼び出すコンソール アプリケーションを作成します。
* firmwareUpdate ダイレクト メソッドを実装するシミュレートされたデバイス アプリを作成します。これは、ファームウェア イメージのダウンロードを待機し、ファームウェア イメージをダウンロードしてから、ファームウェア イメージを適用するというマルチステージ プロセスを経由します。  各ステージの実行中、デバイスは報告されるプロパティを使用して進捗状況を更新します。

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリケーションが完成します。

**dmpatterns_fwupdate_service.js**: シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、応答を表示して、更新された報告されるプロパティを定期的に表示します (500 ミリ秒ごと)。

**dmpatterns_fwupdate_device.js**: IoT Hub とすでに作成したデバイス ID をつなげ、farmwareUpdate ダイレクト メソッドを受信し、イメージのダウンロードの待機、新しいイメージのダウンロード、イメージの適用というマルチステートメント プロセスを経由してファームウェアの更新をシミュレーションします。

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.12.x 以降。 <br/>  「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

「[デバイス管理の開始](iot-hub-node-node-device-management-get-started.md)」の記事を参照して、IoT Hub を作成し、接続文字列を取得します。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Node.js コンソール アプリを作成します。
* シミュレートされたファームウェアの更新をトリガーする
* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスと最後にファームウェアの更新が完了した日時を識別できるようにします。

1. 「**manageddevice**」という名前の新しい空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**manageddevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**manageddevice** フォルダーに新しい **dmpatterns_fwupdate_device.js** ファイルを作成します。
4. **dmpatterns_fwupdate_device.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 変数を追加し、それを使用してデバイス クライアントを作成します。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 報告されるプロパティの更新に使用する、次の関数を追加します。
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. ファームウェア イメージのダウンロードと適用をシミュレートする、次の関数を追加します。
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. 報告されるプロパティを介してファームウェアの更新状態を "ダウンロードの待機中" に更新する、次の関数を追加します。  通常、利用可能な更新プログラムがあるとデバイスに通知され、管理者が定義したポリシーによって更新のダウンロードと適用が開始されます。  ここで、そのポリシーを実行するためのロジックが働きます。  わかりやすいように、4 秒間の遅延を生じさせてから、ファームウェア イメージのダウンロードに進みます。 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. 報告されるプロパティを介してファームウェアの更新状態を "ファームウェア イメージのダウンロード中" に更新する、次の関数を追加します。  続いてファームウェアのダウンロードをシミュレートし、ファームウェア更新の状態を更新してダウンロードが成功したかどうかを知らせます。
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. 報告されるプロパティを介してファームウェアの更新状態を "ファームウェア イメージの適用中" に更新する、次の関数を追加します。  続いてファームウェア イメージの適用をシミュレートし、ファームウェア更新の状態を更新して、適用が成功したかどうかを知らせます。
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. firmwareUpdate メソッドを処理してマルチステージのファームウェア更新プロセスを開始する、次の関数を追加します。
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. 最後に、IoT Hub にデバイスとして接続する、次のコードを追加します。 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用して、デバイス上でリモート ファームウェア更新を開始する
このセクションでは、ダイレクト メソッドを使用してリモート ファームウェア更新を開始し、デバイス ツイン クエリを使用してデバイス上のアクティブなファームウェア更新の状態を定期的に取得する Node.js コンソール アプリを作成します。

1. 「**triggerfwupdateondevice**」という名前の新しい空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**triggerfwupdateondevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**triggerfwupdateondevice** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-hub --save
    ```
3. テキスト エディターを使用して、**triggerfwupdateondevice** フォルダーに新しい **dmpatterns_getstarted_service.js** ファイルを作成します。
4. **dmpatterns_getstarted_service.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 次の変数宣言を追加して、プレース ホルダーの値を置き換えます。
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. 次の関数を追加して、firmwareUpdate の Reported puropathy の値を見つけて表示します。
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 次の関数を追加して、firmwareUpdate メソッドを呼び出してターゲット デバイスを再起動します。
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 最後に、コードに次の関数を追加して、ファームウェア更新シーケンスを開始し、報告されるプロパティの定期的な表示を開始します。
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. **dmpatterns_fwupdate_service.js** ファイルを保存して閉じます。

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. コマンド プロンプトで、**triggerfwupdateondevice** フォルダーに移動し、次のコマンドを実行してデバイス ツインのリモート再起動とクエリを開始して最後の再起動時刻を検索します。
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ダイレクト メソッドを使用してデバイス上でリモートのファームウェア更新を開始し、報告されるプロパティを定期的に使用してファームウェア更新プロセスの進捗状況を確認しました。  

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Nov16_HO5-->


