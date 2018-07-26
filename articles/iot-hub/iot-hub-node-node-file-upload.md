---
title: ノードを使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Node.js の Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 936063e1419d5e2261033ea74d75687eade928e8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187326"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-node-node-c2d.md)に関するチュートリアル内のコードに基づいて、[IoT Hub のファイル アップロード機能](iot-hub-devguide-file-upload.md)を使用して [Azure Blob Storage](../storage/index.yml) にファイルをアップロードする方法を示しています。 このチュートリアルでは、次の操作方法について説明します。

- ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。
- IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

「[Azure IoT Hub の使用](quickstart-send-telemetry-node.md)」チュートリアルでは、IoT Hub のデバイスからクラウドへの基本的なメッセージング機能が説明されています。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 例: 

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティを信頼性を使用できます。

このチュートリアルの最後に、次の 2 つの Node.js コンソール アプリを実行します。

* **SimulatedDevice.js**IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。
* **ReadFileUploadNotification.js**: IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT Device SDK を介して多数のデバイス プラットフォームと言語 (C、.NET、Javascript、Python、および Java) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 4.0.x 以降。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](http://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、IoT Hub にファイルをアップロードするデバイス アプリを作成します。

1. ```simulateddevice``` という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、```simulateddevice``` フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

1. コマンド プロンプトで、```simulateddevice``` フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. テキスト エディターを使用して、```simulateddevice``` フォルダーに **SimulatedDevice.js** ファイルを作成します。

1. **SimulatedDevice.js** ファイルの先頭に、次の ```require``` ステートメントを追加します。

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. ```deviceconnectionstring``` 変数を追加し、それを使用して **Client** インスタンスを作成します。  「_IoT Hub の作成_」セクションで作成したデバイスの名前で ```{deviceconnectionstring}``` を置き換えます。

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > わかりやすくするため、接続文字列がコードに組み入れられています。これは推奨される方法ではありません。お使いのユース ケースやアーキテクチャに応じて、このシークレットを格納するより安全な方法を検討できます。

1. 次のコードを追加して、クライアントに接続します。

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. コールバックを作成し、**uploadToBlob** 関数を使用してファイルをアップロードします。

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. **SimulatedDevice.js** ファイルを保存して閉じます。

1. イメージ ファイルを `simulateddevice` フォルダーにコピーし、名前を `myimage.png` に変更します。

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Node.js コンソール アプリケーションを作成します。

このセクションを完了するために、IoT Hub の **iothubowner** 接続文字列を使用できます。 この接続文字列は、[Azure ポータル](https://portal.azure.com/)の **[共有アクセス ポリシー]** ブレードで確認できます。

1. ```fileuploadnotification``` という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、```fileuploadnotification``` フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

1. コマンド プロンプトで ```fileuploadnotification``` フォルダーに移動し、次のコマンドを実行して **azure-iothub** SDK パッケージをインストールします。

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. テキスト エディターを使用して、```fileuploadnotification``` フォルダーに **FileUploadNotification.js** ファイルを作成します。

1. **FileUploadNotification.js** ファイルの冒頭に次の ```require``` ステートメントを追加します。

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. ```iothubconnectionstring``` 変数を追加し、それを使用して **Client** インスタンスを作成します。  ```{iothubconnectionstring}``` を 「_IoT Hub の作成_」セクションで作成した IoT Hub の接続文字列に置き換えます。

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > わかりやすくするため、接続文字列がコードに組み入れられています。これは推奨される方法ではありません。お使いのユース ケースやアーキテクチャに応じて、このシークレットを格納するより安全な方法を検討できます。

1. 次のコードを追加して、クライアントに接続します。

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. クライアントを開き、**getFileNotificationReceiver** 関数を使用してステータスの更新を受け取ります。

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. **FileUploadNotification.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

`fileuploadnotification` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
node FileUploadNotification.js
```

`simulateddevice` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
node SimulatedDevice.js
```

次のスクリーン ショットは、**SimulatedDevice** アプリからの出力を示しています。

![simulated-device アプリからの出力](./media/iot-hub-node-node-file-upload/simulated-device.png)

次のスクリーン ショットは、**FileUploadNotification** アプリからの出力を示しています。

![read-file-upload-notification アプリからの出力](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

![アップロードされたファイル](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成][lnk-create-hub]
* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

<!-- Links -->
[Azure IoT デベロッパー センター]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
