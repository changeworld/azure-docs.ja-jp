---
title: ノードを使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Node.js の Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: b94cd4cc5c76495cb08f743b1a6849b6eb6c77b2
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005935"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルでは、次の操作方法について説明します。

* ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。

* IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-node.md)に関するクイックスタートでは、IoT Hub のデバイスからクラウドへの基本的なメッセージング機能が示されます。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 次に例を示します。

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ。

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティを信頼性を使用できます。

このチュートリアルの最後に、次の 2 つの Node.js コンソール アプリを実行します。

* **FileUpload.js**: IoT ハブによって提供された SAS URI を使用して、ストレージにファイルをアップロードします。

* **ReadFileUploadNotification.js**: IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT Device SDK を介して多数のデバイス プラットフォームと言語 (C、.NET、Javascript、Python、および Java) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、Azure IoT デベロッパー センターを参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

* Node.js バージョン 10.0.x 以降。 「[Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、IoT ハブにファイルをアップロードするために、GitHub からデバイス アプリをコピーします。

1. GitHub では、Node.js 用に 2 つのファイル アップロード サンプルを入手できます。基本的なサンプルと、より高度なサンプルです。 基本サンプルは、[こちら](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/upload_to_blob.js)のリポジトリからコピーします。 高度なサンプルは、[こちら](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/upload_to_blob_advanced.js)にあります。   

2. ```fileupload``` という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、```fileupload``` フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

3. コマンド プロンプトで、```fileupload``` フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

4. テキスト エディターを使用して ```fileupload``` フォルダーに **FileUpload.js** ファイルを作成し、そこに基本的なサンプルをコピーします。

    ```javascript
    // Copyright (c) Microsoft. All rights reserved.
    // Licensed under the MIT license. See LICENSE file in the project root for full license information.

    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var fs = require('fs');

    var deviceConnectionString = process.env.ConnectionString;
    var filePath = process.env.FilePath;

    var client = Client.fromConnectionString(deviceConnectionString, Protocol);
    fs.stat(filePath, function (err, fileStats) {
      var fileStream = fs.createReadStream(filePath);

      client.uploadToBlob('testblob.txt', fileStream, fileStats.size, function (err, result) {
        if (err) {
          console.error('error uploading file: ' + err.constructor.name + ': ' + err.message);
        } else {
          console.log('Upload successful - ' + result);
        }
        fileStream.destroy();
      });
    });
    ```

5. お使いのデバイスの接続文字列と、アップロードするファイルへのパスの環境変数を追加します。

6. **FileUpload.js** ファイルを保存して閉じます。

7. イメージ ファイルを `fileupload` フォルダーにコピーし、`myimage.png` などの名前を付けます。 ファイルへのパスを `FilePath` 環境変数に設定します。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、作成した IoT ハブからファイル アップロード通知メッセージを受け取るバックエンド サービスを作成します。 ファイル アップロード通知メッセージを受信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Node.js コンソール アプリケーションを作成します。

このセクションを完了するために、IoT Hub の **iothubowner** 接続文字列を使用できます。 この接続文字列は、[Azure ポータル](https://portal.azure.com/)の **[共有アクセス ポリシー]** ブレードで確認できます。

1. ```fileuploadnotification``` という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、```fileuploadnotification``` フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

2. コマンド プロンプトで ```fileuploadnotification``` フォルダーに移動し、次のコマンドを実行して **azure-iothub** SDK パッケージをインストールします。

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、`fileuploadnotification` フォルダーに **FileUploadNotification.js** ファイルを作成します。

4. **FileUploadNotification.js** ファイルの冒頭に次の `require` ステートメントを追加します。

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. `iothubconnectionstring` 変数を追加し、それを使用して **Client** インスタンスを作成します。  プレースホルダー `{iothubconnectionstring}` の値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > わかりやすくするため、接続文字列がコードに組み込まれています。これは推奨される方法ではありません。ユース ケースやアーキテクチャに応じて、このシークレットを格納するさらに安全な方法を検討することが必要な場合があります。

6. 次のコードを追加して、クライアントに接続します。

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. クライアントを開き、**getFileNotificationReceiver** 関数を使用してステータスの更新を受け取ります。

    ```javascript
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

8. **FileUploadNotification.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

`fileuploadnotification` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
node FileUploadNotification.js
```

`fileupload` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
node FileUpload.js
```

次のスクリーン ショットは、**FileUpload** アプリからの出力を示しています。

![simulated-device アプリからの出力](./media/iot-hub-node-node-file-upload/simulated-device.png)

次のスクリーン ショットは、**FileUploadNotification** アプリからの出力を示しています。

![read-file-upload-notification アプリからの出力](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

![アップロードされたファイル](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
