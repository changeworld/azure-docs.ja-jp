---
title: Azure IoT Hub にデバイス テレメトリを送信するクイックスタート (Node.js)
description: このクイックスタートでは、Node.js 用 Azure IoT Hub デバイス SDK を使用して、デバイスから IoT ハブにテレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 3d42ac814678136c2f6342cd1064e3c3ff394507
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777241"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>クイックスタート: デバイスから IoT ハブにテレメトリを送信する (Node.js)

**適用対象**: [デバイス アプリケーション開発](about-iot-develop.md#device-application-development)

このクイックスタートでは、基本的な IoT デバイス アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して、Azure IoT ハブとシミュレートされたデバイスを作成した後、Azure IoT Node.js SDK を使用してデバイスにアクセスし、テレメトリをハブに送信します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- Azure CLI。 このクイックスタートのすべてのコマンドは、ブラウザーで実行されるインタラクティブ CLI シェルである Azure Cloud Shell を使用して実行できます。 Cloud Shell を使用する場合は、何もインストールする必要はありません。 CLI をローカルで使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.76 以降が必要です。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」をご覧ください。
- [Node.js 10 以上](https://nodejs.org)。 Azure Cloud Shell を使用している場合は、インストールされている Node.js のバージョンを更新しないでください。 Azure Cloud Shell には、最新の Node.js バージョンが既に含まれています。

    開発マシン上の Node.js の現在のバージョンを、次のコマンドを使って確認します。

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Node.js SDK を使用してメッセージを送信する
このセクションでは、Node.js SDK を使用して、シミュレートされたデバイスから IoT ハブにメッセージを送信します。 

1. 新しいターミナル ウィンドウを開きます。 このターミナルを使用して Node.js SDK をインストールし、Node.js のサンプル コードを操作します。 これで 2 つのターミナルを開いたことになります。1 つは Node.js を操作するために今開いたターミナル、もう 1 つは、前のセクションで Azure CLI コマンドを入力するために使用した CLI シェルです。

1. [Azure IoT Node.js SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)をローカル コンピューターにコピーします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. *azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Azure IoT Node.js SDK および必要な依存関係をインストールします。

    ```console
    npm install
    ```

    このコマンドを実行すると、*package.json* ファイルに指定されている適切な依存関係が device/samples ディレクトリにインストールされます。

1. シミュレートされたデバイスを Azure IoT に接続できるよう、次の両方の環境変数を設定します。
    * `IOTHUB_DEVICE_CONNECTION_STRING` という環境変数を設定します。 変数の値には、前のセクションで保存したデバイス接続文字列を使用します。
    * `IOTHUB_DEVICE_SECURITY_TYPE` という環境変数を設定します。 変数には、リテラル文字列値 `connectionString` を使用します。

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Windows CMD では、各変数の文字列値を囲む引用符は入力しません。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux または Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. 開いている CLI シェルで、[az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) コマンドを実行し、シミュレートされた IoT デバイス上のイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. インストールされたサンプル ファイル *simple_thermostat.js* のコードを Node.js ターミナルで実行します。 このコードは、シミュレートされた IoT デバイスにアクセスして、IoT ハブにメッセージを送信します。

    Node.js サンプルをターミナルから実行するには、次のようにします。
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT PnP の利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../iot-pnp/overview-iot-plug-and-play.md)」を参照してください。

シミュレートされたテレメトリ メッセージを Node.js コードがデバイスから IoT ハブに送信すると、イベントを監視している CLI シェルにそのメッセージが表示されます。

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

デバイスが安全に接続され、Azure IoT Hub にテレメトリを送信するようになりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成した Azure リソースが不要になった場合は、Azure CLI を使用して削除できます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

名前でリソース グループを削除するには、以下の手順を実行します。
1. [az group delete](/cli/azure/group#az_group_delete) コマンドを実行します。 このコマンドにより、作成したリソース グループ、IoT Hub、デバイスの登録が削除されます。

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az_group_list) コマンドを実行して、リソース グループが削除されていることを確認します。  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure CLI を使用して、IoT ハブとシミュレートされたデバイスを作成した後、Azure IoT Node.js SDK を使用してデバイスにアクセスし、テレメトリをハブに送信します。 

次のステップとして、アプリケーション サンプルを通じて Azure IoT Node.js SDK を詳しく見てみましょう。

- [その他の Node.js サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): このディレクトリには、IoT Hub のシナリオを紹介する多数のサンプルが Node.js SDK リポジトリから取り上げられています。