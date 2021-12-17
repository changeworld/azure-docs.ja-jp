---
title: Azure IoT Central に対するデータの変換 | Microsoft Docs
description: IoT デバイスから送信されるデータにはさまざまな形式があり、一部は変換が必要です。 この記事では、データを変換して IoT Central に転送する方法と IoT Central から転送する方法の両方について説明します。ここで説明するシナリオでは、IoT Edge と Azure Functions を使用します。
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e05cbd17dbfe4f61562856f9244f5b4d0fa65dc9
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577386"
---
# <a name="transform-data-externally-for-iot-central"></a>IoT Central の外部でデータを変換する

IoT デバイスから送信されるデータにはさまざまな形式があります。 デバイス データをご自分の IoT Central アプリケーションで使用するには、以下を行うために変換が必要になる場合があります。

- IoT Central アプリケーションと互換性があるデータ形式にする。
- 単位を変換する。
- 新しいメトリックを計算する。
- 他のソースのデータをエンリッチする。

この記事では、イングレスまたはエグレス時のいずれかで、IoT Central の外部でデバイス データを変換する方法について説明します。

次の図は、変換が含まれるデータの 3 つのルートを示しています。

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="イングレスとエグレスの両方のデータ変換ルートの概要" border="false":::

次の表は、3 つの変換の種類の例を示しています。

| 変換 | 説明 | 例  | メモ |
|------------------------|-------------|----------|-------|
| メッセージの形式         | JSON メッセージに変換または操作します。 | CSV から JSON へ  | イングレス時。 IoT Central で行われるのは、値の JSON メッセージの受け入れのみです。 詳細については、「[テレメトリ、プロパティ、およびコマンドのペイロード](concepts-telemetry-properties-commands.md)」を参照してください。 |
| 計算を           | [Azure Functions](../../azure-functions/index.yml) で実行できる数学関数。 | 華氏から摂氏への単位変換。  | IoT Central への直接接続を介して、デバイスのスケーラブルなイングレスを活用するために、エグレス パターンを使用して変換します。 データを変換すると、視覚化やジョブなどの IoT Central 機能を使用できるようになります。 |
| メッセージの強化     | デバイスのプロパティやテレメトリにはない外部データソースからのエンリッチメント。 内部エンリッチメントの詳細については、「[データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする](howto-export-data.md)」を参照してください | デバイスの[場所データ](howto-use-location-data.md)を使用して気象情報をメッセージに追加する。 | IoT Central への直接接続を介して、デバイスのスケーラブルなイングレスを活用するために、エグレス パターンを使用して変換します。 |

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を完了するには、次が必要です。

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="data-transformation-at-ingress"></a>イングレス時のデータ変換

デバイス データをイングレス時に変換するには、次の 2 つのオプションがあります。

- **IoT Edge**: IoT Central アプリケーションにデータを送信する前に、IoT Edge モジュールを使用してダウンストリーム デバイスのデータを変換します。

- **IoT Central デバイス ブリッジ**: [IoT Central デバイス ブリッジ](howto-build-iotc-device-bridge.md)を使って、Sigfox、Particle、The Things Network などの他の IoT デバイス クラウドを IoT Central に接続します。 デバイス ブリッジによって、データの転送に Azure 関数が使用されます。この関数をカスタマイズして、デバイス データを変換することができます。

### <a name="use-iot-edge-to-transform-device-data"></a>IoT Edge を使用してデバイス データを変換する

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="イングレス時に IoT Edge を使用するデータ変換" border="false":::

このシナリオでは、ダウンストリーム デバイスのデータを IoT Central アプリケーションに転送する前に IoT Edge モジュールを使用して変換します。 このシナリオを構成する手順の概要は次のとおりです。

1. **IoT Edge デバイスを設定する**: IoT Edge デバイスをインストールしてゲートウェイとしてプロビジョニングし、このゲートウェイを IoT Central アプリケーションに接続します。

1. **ダウンストリーム デバイスを IoT Edge デバイスに接続する:** ダウンストリーム デバイスを IoT Edge デバイスに接続し、IoT Central アプリケーションにプロビジョニングします。

1. **IoT Edge 内でデバイス データを変換する:** データを変換する IoT Edge モジュールを作成します。 このモジュールを IoT Edge ゲートウェイ デバイスに配置し、ここから、変換されたデバイス データが IoT Central アプリケーションに転送されます。

1. **検証**: ダウンストリーム デバイスからゲートウェイにデータを送信し、変換されたデバイス データが IoT Central アプリケーションに到達することを確認します。

以降のセクションで説明する例では、ダウンストリーム デバイスから、次の形式の CSV データを IoT Edge ゲートウェイ デバイスに送信します。

```csv
"<temperature >, <pressure>, <humidity>"
```

IoT Central に送信される前に、IoT Edge モジュールを使用して、データを次の JSON 形式に変換できます。

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

[![コードを参照](media/common/browse-code.svg)](https://github.com/iot-for-all/iot-central-transform-with-iot-edge)

次の手順は、このシナリオを設定して構成する方法を示しています。

### <a name="build-the-custom-module"></a>カスタム モジュールをビルドする

このシナリオでは、IoT Edge デバイスで、ダウンストリーム デバイスのデータを変換するカスタム モジュールを実行します。 IoT Edge デバイスを配置して構成する前に、次のことを行う必要があります。

- カスタム モジュールをビルドします。
- カスタム モジュールをコンテナー レジストリに追加します。

IoT Edge ランタイムを使って、Azure コンテナー レジストリや Docker Hub などのコンテナー レジストリからカスタム モジュールをダウンロードします。 [Azure Cloud Shell](../../cloud-shell/overview.md) には、コンテナー レジストリの作成、モジュールのビルド、モジュールのレジストリへのアップロードに必要なすべてのツールが用意されています。

コンテナー レジストリを作成するには、次のようにします。

1. [Azure Cloud Shell](https://shell.azure.com/) を開き、Azure サブスクリプションにサインインします。

1. 次のコマンドを実行して、Azure コンテナー レジストリを作成します。

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    `username` と `password` の値を書き留めておきます。これらは後で使用します。

[Azure Cloud Shell](https://shell.azure.com/) でカスタム モジュールをビルドするには、次のようにします。

1. [Azure Cloud Shell](https://shell.azure.com/) で、適切なフォルダーに移動します。
1. モジュールのソース コードが含まれている GitHub リポジトリをクローンするため、次のコマンドを実行します。

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. カスタム モジュールをビルドするため、Azure Cloud Shell で次のコマンドを実行します。

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    これらのコマンドの実行には数分かかる場合があります。

### <a name="set-up-an-iot-edge-device"></a>IoT Edge デバイスを設定する

このシナリオでは、IoT Edge ゲートウェイ デバイスを使用して、ダウンストリーム デバイスのデータを変換します。 このセクションでは、IoT Central アプリケーション内でゲートウェイとダウンストリーム デバイス用の IoT Central デバイス テンプレートを作成する方法について説明します。 IoT Edge デバイスにより、配置マニフェストが使用され、そのモジュールが構成されます。

ダウンストリーム デバイス用のデバイス テンプレートを作成するために、このシナリオでは、シンプルなサーモスタット デバイス モデルを使用します。

1. [サーモスタット デバイスのデバイス モデル](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json)をローカル コンピューターにダウンロードします。

1. IoT Central アプリケーションにサインインし、 **[デバイス テンプレート]** ページに移動します。

1. **[+ 新規作成]** を選択し、 **[IoT デバイス]** を選択して、 **[次へ: カスタマイズ]** を選択します。

1. テンプレート名として「*Thermostat*」と入力し、 **[次へ: レビュー]** を選択します。 **[作成]** を選択します。

1. **[モデルのインポート]** を選択し、先ほどダウンロードした *thermostat-2.json* ファイルをインポートします。

1. **[発行]** を選択して、新しいデバイス テンプレートを発行します。

IoT Edge ゲートウェイ デバイス用のデバイス テンプレートを作成するには、次のようにします。

1. 配置マニフェスト [moduledeployment.json](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json) のコピーをローカルの開発用コンピューターに保存します。

1. *moduledeployment.json* マニフェスト ファイルのローカル コピーをテキスト エディターで開きます。

1. `registryCredentials` セクションを見つけて、プレースホルダーを Azure コンテナー レジストリの作成時に書き留めた値に置き換えます。 `address` 値は `<username>.azurecr.io` のようになります。

1. `transformmodule` の `settings` セクションを見つけます。 `<acr or docker repo>` を前の手順で使用したのと同じ `address` 値に置き換えます。 変更を保存します。

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** ページに移動します。

1. **[+ 新規作成]** を選択し、 **[Azure IoT Edge]** を選択して、 **[次へ: カスタマイズ]** を選択します。

1. デバイス テンプレート名として「*IoT Edge gateway device*」と入力します。 **[This is a gateway device]\(これはゲートウェイ デバイスです\)** を選択します。 **[参照]** を選択して、先ほど編集した *moduledeployment.json* 配置マニフェスト ファイルをアップロードします。

1. 配置マニフェストを検証したら、 **[次へ: レビュー]** を選択し、 **[作成]** を選択します。

1. **[モデル]** で、 **[リレーションシップ]** を選択します。 **[+ リレーションシップの追加]** を選択します。 表示名として「*Downstream device*」と入力し、ターゲットとして **Thermostat** を選択します。 **[保存]** を選択します。

1. **[発行]** を選択して、デバイス テンプレートを発行します。

これで、IoT Central アプリケーションに 2 つのデバイス テンプレートが作成されました。 **IoT Edge gateway device** テンプレートと、ダウンストリーム デバイスとして **Thermostat** テンプレートです。

IoT Central にゲートウェイ デバイスを登録するには、次のようにします。

1. IoT Central アプリケーションで、 **[デバイス]** ページに移動します。

1. **IoT Edge gateway device** を選択し、 **[デバイスの作成]** を選択します。 デバイス名として「*IoT Edge gateway device*」と入力し、デバイス ID として「*gateway-01*」と入力し、デバイス テンプレートとして **IoT Edge gateway device** が選択されていることを確認します。 **［作成］** を選択します

1. デバイスの一覧で、**IoT Edge gateway device** をクリックし、 **[接続]** を選択します。

1. **IoT Edge gateway device** の **ID スコープ**、**デバイス ID**、**主キー** の値を書き留めておきます。 これらは後で使用します。

IoT Central にダウンストリーム デバイスを登録するには、次のようにします。

1. IoT Central アプリケーションで、 **[デバイス]** ページに移動します。

1. **Thermostat** を選択し、 **[デバイスの作成]** を選択します。 デバイス名として「*Thermostat*」と入力し、デバイス ID として「*downstream-01*」と入力して、デバイス テンプレートとして **Thermostat** が選択されていることを確認します。 **［作成］** を選択します

1. デバイスの一覧で **Thermostat** を選択し、 **[Attach to Gateway]\(ゲートウェイにアタッチ\)** を選択します。 **IoT Edge gateway device** テンプレートと **IoT Edge gateway device** インスタンスを選択します。 **[接続]** を選択します。

1. デバイスの一覧で、**Thermostat** をクリックし、 **[接続]** を選択します。

1. **Thermostat** デバイスの **ID スコープ**、**デバイス ID**、**主キー** の値を書き留めておきます。 これらは後で使用します。

### <a name="deploy-the-gateway-and-downstream-devices"></a>ゲートウェイとダウンストリーム デバイスを配置する

便宜上、この記事では、Azure 仮想マシンを使用してゲートウェイとダウンストリーム デバイスを実行します。 2 つの Azure 仮想マシンを作成するには、下にある **[Azure に配置する]** ボタンを選択し、次の表の情報を使用して **[カスタム デプロイ]** フォームに入力します。

| フィールド | 値 |
| ----- | ----- |
| Resource group | `ingress-scenario` |
| [DNS Label Prefix Gateway]\(DNS ラベル プレフィックス ゲートウェイ\) | このマシンの一意の DNS 名 (例: `<your name>edgegateway`) |
| [DNS Label Prefix Downstream]\(DNS ラベル プレフィックス ダウンストリーム\) | このマシンの一意の DNS 名 (例: `<your name>downstream`) |
| スコープ ID | 前に書き留めておいた ID スコープ |
| [Device ID IoT Edge Gateway]\(デバイス ID IoT Edge ゲートウェイ\) | `gateway-01` |
| [Device Key IoT Edge Gateway]\(デバイス キー IoT Edge ゲートウェイ\) | 前に書き留めておいた主キーの値 |
| 認証の種類 | パスワード |
| [Admin Password Or Key]\(管理者パスワードまたはキー\) | 両方の仮想マシンの **AzureUser** アカウントのパスワードを選択します。 |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

**[確認および作成]** 、 **[作成]** の順に選択します。 **ingress-scenario** リソース グループに仮想マシンを作成するには数分かかります。

IoT Edge デバイスが正常に動作していることを確認するには、次のようにします。

1. IoT Central アプリケーションを開きます。 次に、 **[デバイス]** ページのデバイスの一覧で、**IoT Edge Gateway device** に移動します。

1. **[モジュール]** タブを選択し、3 つのモジュールの状態を確認します。 仮想マシンで IoT Edge ランタイムが起動するまでに数分かかります。 起動すると、3 つのモジュールの状態が **[実行中]** になります。 IoT Edge ランタイムが起動しない場合は、「[IoT Edge デバイスのトラブルシューティング](../../iot-edge/troubleshoot.md)」を参照してください。

IoT Edge デバイスがゲートウェイとして機能するためには、あらゆるダウンストリーム デバイスに対してその ID を証明するための証明書がいくつか必要です。 この記事ではデモ証明書を使用します。 運用環境では、利用している証明機関からの証明書を使用してください。

デモ証明書を生成してゲートウェイ デバイスにインストールするには、次のようにします。

1. SSH を使用して、ゲートウェイ デバイスの仮想マシンに接続してサインインします。 この仮想マシンの DNS 名は Azure portal で確認できます。 **ingress-scenario** リソース グループ内の **edgegateway** 仮想マシンに移動します。

    > [!TIP]
    > SSH を使用してローカル コンピューターまたは Azure Cloud Shell から接続する前に、場合により、両方の仮想マシンで SSH アクセス用にポート 22 を開く必要があります。

1. 次のコマンドを実行して、IoT Edge リポジトリをクローンし、デモ証明書を生成します。

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    上記のコマンドを実行すると、後続の手順で下記のファイルを使用する準備が整います。

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* - IoT Edge シナリオをテストするための、その他すべてのデモ用証明書を作成するために使用されるルート CA 証明書。
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* - *config.yaml* ファイルから参照されるデバイス CA 証明書。 ゲートウェイのシナリオでは、IoT Edge デバイスはこの CA 証明書によって、ダウンストリーム デバイスに対して自らの ID を検証します。
    - *~/certs/private/iot-edge-device-mycacert.key.pem* - デバイス CA 証明書に関連付けられた秘密キー。

    これらのデモ証明書の詳細については、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](../../iot-edge/how-to-create-test-certificates.md)」を参照してください。

1. テキスト エディターで *config.yaml* ファイルを開きます。 次に例を示します。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `Certificate settings` 設定を探します。 次のように、証明書の設定をコメント解除して変更します。

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    上記の例では、**AzureUser** としてサインイン済みであり、"mycacert" という名前のデバイス CA 証明書が作成済みであると想定しています。

1. 変更を保存して IoT Edge ランタイムを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

変更後に IoT Edge ランタイムが正常に起動すると、 **$edgeAgent** および **$edgeHub** モジュールの状態が **[実行中]** に変わります。 これらの状態値は、IoT Central で、ゲートウェイ デバイスの **[モジュール]** ページで確認できます。

ランタイムが起動しない場合は、*config.yaml* で行った変更を確認し、「[IoT Edge デバイスのトラブルシューティング](../../iot-edge/troubleshoot.md)」を参照してください。

### <a name="connect-downstream-device-to-iot-edge-device"></a>ダウンストリーム デバイスを IoT Edge デバイスに接続する

ダウンストリーム デバイスを IoT Edge ゲートウェイ デバイスに接続するには、次のようにします。

1. SSH を使用して、ダウンストリーム デバイスの仮想マシンに接続してサインインします。 この仮想マシンの DNS 名は Azure portal で確認できます。 **ingress-scenario** リソース グループ内の **leafdevice** 仮想マシンに移動します。

    > [!TIP]
    > SSH を使用してローカル コンピューターまたは Azure Cloud Shell から接続する前に、場合により、両方の仮想マシンで SSH アクセス用にポート 22 を開く必要があります。

1. サンプル ダウンストリーム デバイスのソース コードを使用して GitHub リポジトリをクローンするため、次のコマンドを実行します。

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. ゲートウェイ デバイスから必要な証明書をコピーするため、次の`scp` コマンドを実行します。 この `scp` コマンドにより、ホスト名 `edgegateway` を使用してゲートウェイ仮想マシンが識別されます。 パスワードの入力を求められます。

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. *leafdevice* フォルダーに移動し、必要なパッケージをインストールします。 次に、`build` および `start` スクリプトを実行して、デバイスをプロビジョニングしてゲートウェイに接続します。

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. 先ほど作成したダウンストリーム デバイスのデバイス ID、スコープ ID、および SAS キーを入力します。 ホスト名に「`edgegateway`」と入力します。 コマンドの出力は次のようになります。

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>確認

シナリオが実行されていることを確認するには、IoT Central で **IoT Edge gateway device** に移動します。

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="デバイス ページに変換されたデータが示されているスクリーンショット。":::

- **[モジュール]** を選択します。 3 つの IoT Edge モジュール **$edgeAgent**、 **$edgeHub**、および **transformmodule** が実行されていることを確認します。
- **[Downstream Devices]\(ダウンストリーム デバイス\)** を選択し、ダウンストリーム デバイスがプロビジョニングされていることを確認します。
- **[生データ]** を選択します。 **[モデル化されていないデータ]** 列のテレメトリ データは次のようになります。

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

IoT Edge デバイスによって、ダウンストリーム デバイスのデータが変換されるため、テレメトリは、IoT Central 内で、ゲートウェイ デバイスに関連付けられています。 テレメトリを視覚化するには、新しいバージョンの **IoT Edge gateway device** テンプレートを作成して、テレメトリの種類を定義します。

## <a name="data-transformation-at-egress"></a>エグレス時のデータ変換

デバイスを IoT Central に接続し、デバイス データをコンピューティング エンジンにエクスポートして変換した後、デバイスの管理と分析を行えるように、変換されたデータを IoT Central に戻すことができます。 次に例を示します。

- 位置情報データが、デバイスから IoT Central に送信されます。
- データは IoT Central からコンピューティング エンジンにエクスポートされ、そこで、気象情報を使って位置情報データが拡張されます。
- 拡張されたデータは、コンピューティング エンジンによって IoT Central に戻されます。

コンピューティング エンジンとして [IoT Central デバイス ブリッジ](https://github.com/Azure/iotc-device-bridge)を使用して、IoT Central からエクスポートされたデータを変換できます。

エグレス時にデータを変換する利点は、デバイスが IoT Central に直接接続されることです。これにより、デバイスにコマンドを送信したり、デバイスのプロパティを更新したりすることが簡単になります。 ただし、この方法では、使用するメッセージが毎月の割り当てよりも多くなり、Azure IoT Central の使用コストが増加する可能性があります。

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>IoT Central デバイス ブリッジを使用してデバイス データを変換する

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="エグレス時に IoT Edge を使用するデータ変換" border="false":::

このシナリオでは、IoT Central からエクスポートされたデバイス データが、コンピューティング エンジンによって変換された後、IoT Central アプリケーションに戻されます。 このシナリオを構成する手順の概要は次のとおりです。

1. **コンピューティング エンジンを設定する:** データ変換のためのコンピューティング エンジンとして機能する IoT Central デバイス ブリッジを作成します。

1. **デバイス ブリッジのデバイス データを変換する:** データ変換のユース ケースに合わせてデバイス ブリッジ関数コードを変更して、デバイス ブリッジのデータを変換します。

1. **IoT Central からデバイス ブリッジへのデータ フローを有効にする:** 変換のために IoT Central からデバイス ブリッジにデータをエクスポートします。 次に、変換されたデータを IoT Central に戻します。 データのエクスポートを作成するとき、メッセージ プロパティ フィルターを使用して、変換されていないデータのみをエクスポートします。

1. **確認**: デバイスを IoT Central アプリに接続し、IoT Central で、生のデバイス データと変換されたデータの両方を確認します。

<!-- To Do - doesn't the device send JSON data? -->
以降のセクションで説明する例では、デバイスから、次の形式の CSV データを IoT Edge ゲートウェイ デバイスに送信します。

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

デバイス ブリッジを使用して、以下を行ってデバイス データを変換します。

- 気温の単位を摂氏から華氏に変更する。
- [Open Weather](https://openweathermap.org/) サービスから、緯度と経度の値に対して取得した気象データを使用して、デバイス データをエンリッチする。

変換されたデータは、デバイス ブリッジによって、次の形式で IoT Central に送信されます。

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

次の手順は、このシナリオを設定して構成する方法を示しています。

### <a name="retrieve-your-iot-central-connection-settings"></a>IoT Central 接続設定を取得する

このシナリオを設定する前に、IoT Central アプリケーションからいくつかの接続設定を取得する必要があります。

1. ご使用の IoT Central アプリケーションにサインインします。

1. **[管理] > [デバイスの接続]** に移動します。

1. **ID スコープ** を書き留めておきます。 この値は後で使用します。

1. **SaS-IoT-Devices** 登録グループを選択します。 Shared Access Signature の主キーを書き留めておきます。 この値は後で使用します。

### <a name="set-up-a-compute-engine"></a>コンピューティング エンジンを設定する

このシナリオでは、IoT Central デバイス ブリッジと同じ Azure Functions 配置を使用します。 デバイス ブリッジを配置するには、下にある **[Azure に配置する]** ボタンを選択し、次の表の情報を使用して **[カスタム デプロイ]** フォームに入力します。

| フィールド | 値 |
| ----- | ----- |
| Resource group | `egress-scenario` という名前で新しいリソース グループを作成します |
| リージョン | 最も近いリージョンを選択します。 |
| スコープ ID | 前に書き留めておいた **ID スコープ** を使用します。 |
| [IoT Central SAS Key]\(IoT Central SAS キー\) | **SaS-IoT-Devices** 登録グループの Shared Access Signature の主キーを使用します。 この値は、前に書き留めてあります。 |

[![Azure に配置する](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

**[確認および作成]** 、 **[作成]** の順に選択します。 **egress-scenario** リソース グループに Azure 関数と関連リソースを作成するには数分かかります。

### <a name="transform-device-data-in-the-device-bridge"></a>デバイス ブリッジでデバイス データを変換する

エクスポートされたデバイス データを変換するようにデバイス ブリッジを構成するには、次のようにします。

1. Open Weather サービスからアプリケーション API キーを取得します。 アカウントは無料で、サービスの利用に制限があります。 アプリケーション API キーを作成するには、[Open Weather サービス ポータル](https://openweathermap.org/)でアカウントを作成し、指示に従います。 Open Weather API キーは後で使用します。

1. Azure portal で、**egress-scenario** リソース グループの関数アプリに移動します。

1. 左側のナビゲーションの **[開発ツール]** で、 **[App Service Editor (プレビュー)]** を選択します。

1. **[移動 &rarr;]** を選択して、 **[App Service Editor]** ページを開きます。 次の変更を行います。

    1. *wwwroot/IoTCIntegration/index.js* ファイルを開きます。 このファイル内のすべてのコードを [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js) のコードに置き換えます。

    1. 新しい *index.js* で、先ほど取得した Open Weather API キーを使用して `openWeatherAppId` 変数ファイルを更新します。

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. 関数から IoT Central に送信されるデータにメッセージ プロパティを追加します。 このプロパティは、変換されたデータをエクスポートしないように IoT Central によって使用されます。 この変更を行うには、*wwwroot/IoTCIntegration/lib/engine.js* ファイルを開きます。 次のコードを見つけます。

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        次のコードを前のスニペットのコードのすぐ下に追加します。

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        参考のため、[engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) ファイルの完全な例を確認できます。

1. **App Service Editor** の左側のナビゲーションで、 **[コンソール]** を選択します。 次のコマンドを実行して、必要なパッケージをインストールします。

    ```bash
    cd IoTCIntegration
    npm install
    ```

    このコマンドの実行には、数分かかる場合があります。

1. **[Azure Function Overview]\(Azure 関数の概要\)** ページに戻り、関数を再起動します。

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="関数を再起動する":::

1. 左側のナビゲーションで、 **[関数]** を選択します。 次に、**IoTCIntegration** を選択します。 **[Code + Test]\(コード + テスト\)** を選択します。

1. 関数の URL をメモしておきます。この値は後で必要になります。

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="関数の URL を取得する":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>IoT Central からデバイス ブリッジへのデータ フローを有効にする

このセクションでは、Azure IoT Central アプリケーションを設定する方法について説明します。

まず、[デバイス モデル](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) ファイルをローカル コンピューターに保存します。

IoT Central アプリケーションにデバイス テンプレートを追加するには、IoT Central アプリケーションに移動してから、次のようにします。

1. IoT Central アプリケーションにサインインし、 **[デバイス テンプレート]** ページに移動します。

1. **[+ 新規作成]** を選択し、 **[IoT デバイス]** を選択して、 **[次へ: カスタマイズ]** を選択し、テンプレート名として「*Compute model*」と入力します。 **確認\)** をクリックします。 **[作成]** を選択します。

1. **[モデルのインポート]** を選択し、先にダウンロードした *model.json* ファイルを参照します。

1. モデルをインポートした後、 **[発行]** を選択して **Compute model** デバイス テンプレートを発行します。

データのエクスポートを設定して、デバイス ブリッジにデータを送信するには、次のようにします。

1. IoT Central アプリケーションで、 **[データのエクスポート]** を選択します。

1. **[+ New destination]\(+ 新しい変換先\)** を選択して、デバイス ブリッジで使用する変換先を作成します。 変換先に「*Compute function*」という名前を指定し、 **[ターゲットの種類]** に **[Webhook]** を選択します。 [コールバック URL] には、前に書き留めておいた関数の URL の貼り付けを選択します。 **[認証]** を **[No Auth]\(認証なし\)** のままにします。

1. 変更を保存します。

1. **[+ 新しいエクスポート]** を選択し、「*Compute export*」というデータ エクスポートを作成します。

1. 使用しているデバイス テンプレートのデバイス データのみをエクスポートするフィルターを追加します。 **[+ フィルター]** を選択し、項目 **[デバイス テンプレート]** を選択します。**等号** 演算子を選択し、先ほど作成した **Compute model** デバイス テンプレートを選択します。

1. 変換されたデータと変換されていないデータを区別するメッセージ フィルターを追加します。 このフィルターを使って、変換された値がデバイス ブリッジに戻らないようにします。 **[+ Message property filter]\(+ メッセージ プロパティ フィルター\)** を選択し、名前値「*computed*」を入力して、演算子 **[存在しません]** を選択します。 文字列 `computed` は、デバイス ブリッジのサンプル コードでキーワードとして使用されます。

1. 変換先として、先ほど作成した **Compute function** 変換先を選択します。

1. 変更を保存します。 1 分ほど経過すると、 **[エクスポートの状態]** に **[正常]** が表示されます。

### <a name="verify"></a>確認

シナリオのテストに使用するサンプル デバイスは Node.js で記述されています。 ローカル コンピューターに Node.js と NPM がインストールされていることを確認します。 これらの前提条件をインストールしたくない場合は、それらがプレインストールされている [Azure Cloud Shell](https://shell.azure.com/) を使用します。

シナリオをテストするサンプル デバイスを実行するには、次のようにします。

1. サンプル コードが含まれている GitHub リポジトリをクローンし、次のコマンドを実行します。

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. サンプル デバイスを IoT Central アプリケーションに接続するため、*iot-central-compute/device/device.js* ファイルの接続設定を編集します。 スコープ ID とグループ SAS キーを、前に書き留めておいた値に置き換えます。

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    変更を保存します。

1. 次のコマンドを使用して、必要なパッケージをインストールし、デバイスを実行します。

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. このコマンドの結果は、次の出力のようになります。

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. IoT Central アプリケーションで、**computeDevice** というデバイスに移動します。 **[生データ]** ビューに、2 つの異なるテレメトリ ストリームが約 5 秒ごとに表示されます。 モデル化されていないデータを含むストリームが元のテレメトリ、モデル化されたデータを含むストリームが、関数によって変換されたデータです。

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="元のデータと変換された生データを示すスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このガイドの手順に従って作成した Azure リソースが不要になった場合は、[Azure portal でリソース グループ](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups)を削除します。

このガイドで使用した 2 つのリソース グループは、**ingress-scenario** と **egress-scenario** です。

## <a name="next-steps"></a>次のステップ

この記事では、IoT Central に対して、イングレスとエグレスの両方で、デバイス データを変換するためのさまざまなオプションについて説明しました。 次の 2 つの個別のシナリオについてのチュートリアルを記事で紹介しました。

- IoT Central アプリケーションにデータを送信する前に、IoT Edge モジュールを使用してダウンストリーム デバイスのデータを変換します。
- Azure Functions を使用して、IoT Central の外部でデータを変換します。 このシナリオでは、IoT Central で、データのエクスポートを使用して、変換される Azure 関数に受信データを送信します。 変換されたデータは、関数によって、IoT Central アプリケーションに戻されます。

Azure IoT Central アプリケーションの外部でデバイス データを変換する方法を説明したので、次は、[IoT Central で分析を使用してデバイス データを分析する方法](howto-create-analytics.md)について説明します。
