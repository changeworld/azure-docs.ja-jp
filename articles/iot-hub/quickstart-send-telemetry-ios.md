---
title: Azure IoT Hub への利用統計情報の送信に関するクイック スタート | Microsoft Docs
description: このクイック スタートでは、サンプルの iOS アプリケーションを実行して、IoT Hub にシミュレートされた利用統計情報を送信し、クラウドで処理するために IoT Hub から利用統計情報を読み取ります。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/20/2018
ms.author: kgremban
ms.openlocfilehash: aecb9a1819060e0da6338e8e16bf681fad42dd22
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161919"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>クイック スタート: デバイスから IoT ハブへの利用統計情報の送信 (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量の利用統計情報をクラウドに取り込むことを可能にする Azure サービスです。 この記事では、シミュレートされたデバイス アプリケーションから IoT Hub に利用統計情報を送信します。 その後、バックエンド アプリケーションからデータを表示できます。 

この記事では、事前に作成済みの Swift アプリケーションを使用して利用統計情報を送信し、CLI ユーティリティを使用して IoT Hub から利用統計情報を読み取ります。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- [Azure サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)のページからコード サンプルをダウンロードします 
- iOS SDK の最新バージョンを実行している最新バージョンの [XCode](https://developer.apple.com/xcode/)。 このクイック スタートは、XCode 9.3 と iOS 11.3 でテストされました。
- 最新バージョンの [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure CLI を使用して、シミュレートされたデバイスを登録します。

1. IoT Hub CLI 拡張機能を追加し、デバイス ID を作成します。 `{YourIoTHubName}` を IoT ハブの名前に置き換えます。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

    デバイスに別の名前を選択した場合は、サンプル アプリケーションを実行する前に、その中でのバイス名を更新してください。

1. 次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   `Hostname=...=` のようなデバイス接続文字列をメモしておきます。 この値は、この記事の後の方で使用します。

## <a name="send-simulated-telemetry"></a>シミュレートされた利用統計情報の送信

このサンプル アプリケーションは、iOS デバイス上で実行されます。サンプル アプリケーションは、IoT ハブ上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度および湿度利用統計情報を送信します。 

### <a name="install-cocoapods"></a>CocoaPods のインストール

CocoaPods は、サードパーティ製のライブラリを使用する iOS プロジェクトの依存関係を管理します。

ターミナル ウィンドウで、前提条件としてダウンロードした Azure-IoT-Samples-iOS フォルダーに移動します。 その後、サンプル プロジェクトに移動します。

```sh
cd quickstart/sample-device
```

XCode が終了していることを確認し、次のコマンドを実行して、**podfile** ファイルで宣言されている CocoaPods をインストールします。

```sh
pod install
```

インストール コマンドでは、プロジェクトに必要なポッドをインストールすると共に、依存関係にポッドを使用するように既に構成されている XCode ワークスペース ファイルも作成されます。 

### <a name="run-the-sample-application"></a>サンプル アプリケーションの実行 

1. XCode で、サンプル ワークスペースを開きます。

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT Client Sample**  プロジェクトを展開し、次に同じ名前のフォルダーを展開します。  
3. XCode で編集するために **ViewController.swift** を開きます。 
4. **connectionString** 変数を検索し、前の手順でメモしたデバイス接続文字列で値を更新します。
5. 変更を保存します。 
6. デバイス エミュレーターで、**[ビルド/実行]** ボタンまたは **Command + r** キーの組み合わせを使用してプロジェクトを実行します。 

   ![プロジェクトを実行する](media/quickstart-send-telemetry-ios/run-sample.png)

7. エミュレーターが開いたら、サンプル アプリで **[Start]** を選択します。

次のスクリーンショットは、アプリケーションが IoT ハブにシミュレートされた利用統計情報を送信する際の出力例を示しています。

   ![シミュレートされたデバイスを実行する](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る

XCode エミュレーターで実行したサンプル アプリは、デバイスから送信されたメッセージに関するデータを表示します。 データの受信時に IoT ハブを経由するデータを表示することもできます。 IoT Hub CLI 拡張機能は、IoT ハブ上のサービス側 **Events** エンドポイントに接続できます。 この拡張機能は、シミュレートされたデバイスから送信されたデバイスとクラウドの間のメッセージを受信します。 通常、IoT Hub のバックエンド アプリケーションはクラウド内で実行され、デバイスとクラウドの間のメッセージ受信して処理します。

次の Azure CLI コマンドを実行します。`{YourIoTHubName}` は IoT ハブの名前に置き換えます。

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

次のスクリーンショットは、シミュレートされたデバイスからハブに送信された利用統計情報を拡張機能が受信したときの出力を示しています。

次のスクリーンショットは、ターミナル ウィンドウに表示される利用統計情報の種類を示しています。

![利用統計情報データを表示する](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

この記事では、IoT ハブを設定し、デバイスを登録し、iOS デバイスからハブにシミュレートされた利用統計情報を送信し、ハブから利用統計情報を読み取りました。 

バックエンド アプリケーションからシミュレートされたデバイスを制御する方法を学習するには、次のクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT ハブに接続されたデバイスを制御する](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
