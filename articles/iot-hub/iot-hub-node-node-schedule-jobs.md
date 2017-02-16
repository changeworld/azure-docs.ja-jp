---
title: "ジョブをスケジュールする方法 | Microsoft Docs"
description: "このチュートリアルでは、ジョブのスケジュールを設定する方法について説明します"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 197414101ea86a68db901744c11a3de110a1eba3


---
# <a name="tutorial-schedule-and-broadcast-jobs"></a>チュートリアル: ジョブのスケジュールとブロードキャスト

## <a name="introduction"></a>はじめに
Azure IoT Hub は、数百万台のデバイスをスケジュールおよび更新するジョブのアプリケーション バックエンドによる作成や追跡を可能にする完全に管理されたサービスです。  ジョブは次のアクションに使用できます。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

概念的には、ジョブはこれらのアクションのいずれかをラップし、デバイス ツイン クエリで定義される一連のデバイスに対して実行の進行状況を追跡します。  たとえば、ジョブを使用すると、アプリケーション バックエンドは 10,000 台のデバイスに対して reboot メソッドを呼び出すことができます。これは、デバイス ツイン クエリで指定され、将来の時刻にスケジュールされます。  次に、このアプリケーションを使用して、これらの各デバイスが reboot メソッドを受信し実行する進行状況を追跡できます。

これらの各機能について詳しくは、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインの概要][lnk-get-started-twin]に関する記事と[デバイス ツインのプロパティの使用方法に関するチュートリアル][lnk-twin-props]
* ダイレクト メソッド: [ダイレクト メソッドに関する開発者ガイド][lnk-dev-methods]と[ダイレクト メソッドに関するチュートリアル][lnk-c2d-methods]

このチュートリアルでは、次の操作方法について説明します。

* アプリケーション バックエンドから呼び出すことができ、**lockDoor** を可能にするダイレクト メソッドを持つ、シミュレートされたデバイス アプリを作成します。
* ジョブを使用してシミュレートされたデバイス アプリで **lockDoor** ダイレクト メソッドを呼び出し、デバイス ジョブを使用して必要なプロパティを更新するコンソール アプリケーションを作成します。

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリケーションが完成します。

**simDevice.js**。デバイス ID で IoT ハブに接続し、**lockDoor** ダイレクト メソッドを受信します。

**scheduleJobService.js**。シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、ジョブを使用してデバイス ツインの必要なプロパティを更新します。

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.12.x 以降。 <br/>  「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、クラウドで呼び出されたダイレクト メソッドに応答する Node.js コンソール アプリを作成します。このアプリによってシミュレートされたデバイスの再起動が開始され、報告されるプロパティを使用してデバイスと最後の再起動時間をデバイス ツインのクエリで識別できるようになります。

1. **simDevice** という名前の新しい空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**simDevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**simDevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**simDevice** フォルダーに新しい **simDevice.js** ファイルを作成します。
4. **simDevice.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 変数を追加し、それを使用してデバイス クライアントを作成します。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. **lockDoor** メソッドを処理する次の関数を追加します。
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. 次のコードを追加して、**lockDoor** メソッドのハンドラーを登録します。
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. **simDevice.js** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>ダイレクト メソッドを呼び出し、デバイス ツインのプロパティを更新するジョブのスケジュール
このセクションでは、ダイレクト メソッドを使用してデバイスでリモート **lockDoor** を開始する Node.js コンソール アプリを作成し、デバイス ツインのプロパティを更新します。

1. **scheduleJobService** という名前の新しい空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**scheduleJobService** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**scheduleJobService** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iothub uuid --save
    ```
3. テキスト エディターを使用して、**scheduleJobService** フォルダーに新しい **scheduleJobService.js** ファイルを作成します。
4. **dmpatterns_gscheduleJobServiceetstarted_service.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. 次の変数宣言を追加して、プレース ホルダーの値を置き換えます。
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. ジョブの実行を監視するために使用される次の関数を追加します。
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. 次のコードを追加して、デバイス メソッドを呼び出すジョブをスケジュールします。
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. 次のコードを追加して、デバイス ツインを更新するようにジョブをスケジュールします。
   
    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. **scheduleJobService.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトで、**simDevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```
    node simDevice.js
    ```
2. コマンド プロンプトで、**scheduleJobService** フォルダーに移動し、次のコマンドを実行して、リモート再起動のトリガーと、最後の再起動時刻を検出するデバイス ツインのクエリを実行します。
   
    ```
    node scheduleJobService.js
    ```
3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

ファームウェアのリモートでのワイヤレス更新などの IoT Hub による他のデバイス管理パターンを確認するには、次の記事を参照してください。

[チュートリアル: ファームウェアを更新する方法][lnk-fwupdate]

引き続き IoT Hub の使用方法を確認するには、[IoT Gateway SDK の使用][lnk-gateway-SDK]に関する記事を参照してください。

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Nov16_HO5-->


