---
title: "Node.js を使用してデバイスを接続する | Microsoft Docs"
description: "Node.js で記述されたアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 02247d679c9bffa01b8d2827ea57eba38894107e
ms.openlocfilehash: 698384bb5079feb937f423f9b6365dff20cfa3be


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>デバイスをリモート監視構成済みソリューションに接続する (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>node.js サンプル ソリューションのビルドと実行
1. *Microsoft Azure IoT SDK for Node.js* GitHub リポジトリのクローンを作成し、デスクトップ環境にインストールするには、「[Prepare your development environment (開発環境を準備する)][lnk-github-prepare]」の手順に従います。
2. [azure-iot-sdk-node][lnk-github-repo] リポジトリの device/samples フォルダーのローカル コピーから、次の&2; つのファイルをデバイス上のフォルダーにコピーします。
   
   * package.json
   * remote_monitoring.js
3. remote_monitoring.js ファイルを開き、次の変数を探します。
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. **[IoT Hub device connection string]** を、デバイスの接続文字列に置き換えます。 IoT Hub のホスト名、デバイス ID およびデバイス キーの値は、リモート監視ソリューション ダッシュボードにあります。 デバイスの接続文字列は、次の形式にする必要があります。
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    IoT ハブのホスト名が **contoso**、デバイス ID が **mydevice** の場合、接続文字列は以下のようになります。
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. ファイルを保存します。 コマンド プロンプトで、これらのファイルが含まれているフォルダーで次のコマンドを実行して必要なパッケージをインストールし、サンプル アプリケーションを実行します。
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md



<!--HONumber=Dec16_HO3-->


