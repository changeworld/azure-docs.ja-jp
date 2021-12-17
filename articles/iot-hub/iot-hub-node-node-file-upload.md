---
title: ノードを使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Node.js の Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: a95737cd9d15b0ee21249f0db8d1bbb96fb8a3b2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710086"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルでは、次の操作方法について説明します。

* ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。

* IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

[デバイスから IoT ハブへのテレメトリの送信](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)に関するクイックスタートでは、IoT Hub のデバイスからクラウドへの基本的なメッセージング機能が示されます。 ただし、一部のシナリオでは、デバイスが送信するデータを、IoT Hub が受け付けるデバイスからクラウドへの比較的小さなメッセージには簡単にマップできません。 次に例を示します。

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ。

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティを信頼性を使用できます。

この記事の最後に、次の 2 つの Node.js コンソール アプリを実行します。

* **FileUpload.js**: IoT ハブによって提供された SAS URI を使用して、ストレージにファイルをアップロードします。

* **ReadFileUploadNotification.js**: IoT ハブからファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を通して、多数のデバイス プラットフォームと言語 (C、Java、Python、JavaScript を含む) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

* Node.js バージョン 10.0.x 以降。 LTS バージョンが推奨されます。 Node.js は [nodejs.org](https://nodejs.org) からダウンロードできます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、IoT ハブにファイルをアップロードするデバイス アプリを作成します。 このコードは、[Azure IoT node.js SDK](https://github.com/Azure/azure-iot-sdk-node) デバイス サンプルの [upload_to_blob_advanced.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/upload_to_blob_advanced.js) サンプルにあるコードに基づいています。

1. `fileupload` という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、`fileupload` フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

1. コマンド プロンプトで、`fileupload` フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK、**azure-iot-device-mqtt**、 **@azure/storage-blob** の各パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt @azure/storage-blob --save
    ```

1. テキスト エディターを使用して `fileupload` フォルダーに **FileUpload.js** ファイルを作成し、そこに次のコードをコピーします。

    ```javascript
    'use strict';

    const Client = require('azure-iot-device').Client;
    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    const errors = require('azure-iot-common').errors;
    const path = require('path');

    const {
      AnonymousCredential,
      BlockBlobClient,
      newPipeline
    } = require('@azure/storage-blob');

    // make sure you set these environment variables prior to running the sample.
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    const localFilePath = process.env.PATH_TO_FILE;
    const storageBlobName = path.basename(localFilePath);

    async function uploadToBlob(localFilePath, client) {
      const blobInfo = await client.getBlobSharedAccessSignature(storageBlobName);
      if (!blobInfo) {
        throw new errors.ArgumentError('Invalid upload parameters');
      }

      const pipeline = newPipeline(new AnonymousCredential(), {
        retryOptions: { maxTries: 4 },
        telemetry: { value: 'HighLevelSample V1.0.0' }, // Customized telemetry string
        keepAliveOptions: { enable: false }
      });

      // Construct the blob URL to construct the blob client for file uploads
      const { hostName, containerName, blobName, sasToken } = blobInfo;
      const blobUrl = `https://${hostName}/${containerName}/${blobName}${sasToken}`;

      // Create the BlockBlobClient for file upload to the Blob Storage Blob
      const blobClient = new BlockBlobClient(blobUrl, pipeline);

      // Setup blank status notification arguments to be filled in on success/failure
      let isSuccess;
      let statusCode;
      let statusDescription;

      try {
        const uploadStatus = await blobClient.uploadFile(localFilePath);
        console.log('uploadStreamToBlockBlob success');

        // Save successful status notification arguments
        isSuccess = true;
        statusCode = uploadStatus._response.status;
        statusDescription = uploadStatus._response.bodyAsText;

        // Notify IoT Hub of upload to blob status (success)
        console.log('notifyBlobUploadStatus success');
      }
      catch (err) {
        isSuccess = false;
        statusCode = err.code;
        statusDescription = err.message;

        console.log('notifyBlobUploadStatus failed');
        console.log(err);
      }

      await client.notifyBlobUploadStatus(blobInfo.correlationId, isSuccess, statusCode, statusDescription);
    }

    // Create a client device from the connection string and upload the local file to blob storage.
    const deviceClient = Client.fromConnectionString(deviceConnectionString, Protocol);
    uploadToBlob(localFilePath, deviceClient)
      .catch((err) => {
        console.log(err);
      })
      .finally(() => {
        process.exit();
      });
    ```

1. **FileUpload.js** ファイルを保存して閉じます。

1. イメージ ファイルを `fileupload` フォルダーにコピーし、`myimage.png` などの名前を付けます。

1. お使いのデバイスの接続文字列と、アップロードするファイルへのパスの環境変数を追加します。 [デバイスを IoT ハブ に登録した](#register-a-new-device-in-the-iot-hub)際に、デバイスの接続文字列が取得されています。
    
    - Windows の場合:

        ```cmd
        set DEVICE_CONNECTION_STRING={your device connection string}
        set PATH_TO_FILE={your image filepath}
        ```

    - Linux/Bash の場合:

        ```bash
        export DEVICE_CONNECTION_STRING="{your device connection string}"
        export PATH_TO_FILE="{your image filepath}"
        ```

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、作成した IoT ハブからファイル アップロード通知メッセージを受け取るバックエンド サービスを作成します。 ファイル アップロード通知メッセージを受信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Node.js コンソール アプリケーションを作成します。

1. `fileuploadnotification` という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、`fileuploadnotification` フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

1. コマンド プロンプトで `fileuploadnotification` フォルダーに移動し、次のコマンドを実行して **azure-iothub** SDK パッケージをインストールします。

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. テキスト エディターを使用して、`fileuploadnotification` フォルダーに **FileUploadNotification.js** ファイルを作成します。

1. **FileUploadNotification.js** ファイルの冒頭に次の `require` ステートメントを追加します。

    ```javascript
    'use strict';

    const Client = require('azure-iothub').Client;
    ```

1. 環境から IoT ハブの接続文字列を読み取ります。

    ```javascript
    const connectionString = process.env.IOT_HUB_CONNECTION_STRING;
    ```

1. 次のコードを追加して、接続文字列からサービス クライアントを作成します。

    ```javascript
    const serviceClient = Client.fromConnectionString(connectionString);
    ```

1. クライアントを開き、**getFileNotificationReceiver** 関数を使用してステータスの更新を受け取ります。

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

1. **FileUploadNotification.js** ファイルを保存して閉じます。

1. IoT ハブ接続文字列の環境変数を追加します。 この文字列は、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしたものです。
    
    - Windows の場合:

        ```cmd
        set IOT_HUB_CONNECTION_STRING={your iot hub connection string}
        ```

    - Linux/Bash の場合:

        ```bash
        export IOT_HUB_CONNECTION_STRING="{your iot hub connection string}"
        ```

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

アップロードが完了した後の **FileUpload** アプリからの出力を次に示します。

```output
uploadStreamToBlockBlob success
notifyBlobUploadStatus success
```

アップロードが完了した後の **FileUploadNotification** アプリからのサンプル出力を次に示します。

```output
Service client connected
File upload from device:
{"deviceId":"myDeviceId","blobUri":"https://{your storage account name}.blob.core.windows.net/device-upload-container/myDeviceId/image.png","blobName":"myDeviceId/image.png","lastUpdatedTime":"2021-07-23T23:27:06+00:00","blobSizeInBytes":26214,"enqueuedTimeUtc":"2021-07-23T23:27:07.2580791Z"}
```

## <a name="verify-the-file-upload"></a>ファイルのアップロードを確認する

ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

1. Azure portal のストレージ アカウントに移動します。
1. ストレージ アカウントの左ペインで、 **[コンテナー]** を選択します。
1. ファイルをアップロードしたコンテナーを選択します。
1. デバイスにちなんだ名前のフォルダーを選択します。
1. ファイルをアップロードした BLOB を選択します。 この記事では、ファイルと同じ名前の BLOB です。  

    :::image type="content" source="./media/iot-hub-node-node-file-upload/view-uploaded-file.png" alt-text="アップロードされたファイルが表示されている Azure portal のスクリーンショット。":::

1. 開いたページで BLOB のプロパティを表示します。 **[ダウンロード]** を選択 してファイルをダウンロードし、その内容をローカルで表示できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
