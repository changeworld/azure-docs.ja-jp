---
title: Azure IoT Central アプリケーションで X.509 証明書を使用するデバイスを接続する
description: IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する方法
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9a93602327b5c5294d6c17c1804c04c6603dcf37
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999873"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する方法

IoT Central では、デバイスとアプリケーションの間の通信をセキュリティで保護するために、共有アクセス署名 (SAS) と X.509 証明書の両方がサポートされています。 SAS は、「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device-nodejs.md)」チュートリアルで使用されます。 この記事では、X.509 を使用するようにコード サンプルを変更する方法について説明します。  運用環境では、X.509 証明書はお勧めしません。 詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を参照してください。

この記事では、X.509 の 2 つの使用方法を示します。通常は運用環境で使用される[グループ登録](how-to-connect-devices-x509.md#use-a-group-enrollment)と、テストに役立つ[個別登録](how-to-connect-devices-x509.md#use-an-individual-enrollment)です。

## <a name="prerequisites"></a>前提条件

- 「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Node.js)](./tutorial-connect-device-nodejs.md)」チュートリアルの完了。
- [Git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/) のダウンロードとインストール。 Windows を使用している場合は、[SourceForge の OpenSSL ページ](https://sourceforge.net/projects/openssl/)からバイナリを使用できます。

## <a name="use-a-group-enrollment"></a>グループ登録を使用する

運用環境のグループ登録では X.509 証明書を使用します。 グループ登録では、IoT Central アプリケーションにルートまたは中間の X.509 証明書が追加されます。 ルートまたは中間の証明書から派生したリーフ証明書を使用するデバイスをアプリケーションに接続できます。

## <a name="generate-root-and-device-cert"></a>ルートとデバイスの証明書を生成する

このセクションでは、X.509 証明書を使用して、登録グループの証明書から派生した証明書を使用してデバイスを接続します。これにより、IoT Central アプリケーションに接続できます。

> [!WARNING]
> この X.509 証明書の生成方法は、テストのみを目的としています。 運用環境では、証明書を生成するためのセキュリティで保護された正式なメカニズムを使用する必要があります。

1. コマンド プロンプトを開きます。 GitHub リポジトリの証明書生成スクリプトを複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. 証明書ジェネレーター スクリプトに移動し、必要なパッケージをインストールします。

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. ルート証明書を作成してから、スクリプトを実行してデバイス証明書を取得します。 証明書の名前には、小文字の英数字とハイフンのみを使用します。

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

これらのコマンドによって、ルートとデバイス証明書用のファイルがそれぞれ 3 つ生成されます。

filename | 目次
-------- | --------
\<name\>_cert.pem | X509 証明書の公開部分
\<name\>_key.pem | X509 証明書の秘密キー
\<name\>_fullchain.pem | X509 証明書のキーチェーン全体

## <a name="create-a-group-enrollment"></a>グループ登録を作成する

1. IoT Central アプリケーションを開き、左側のペインで **[管理]** に移動し、 **[デバイスの接続]** を選択します。

1. **[+ 登録グループの作成]** を選択し、構成証明書の種類として **証明書 (X.509)** を使用する、_MyX509Group_ という名前の新しい登録グループを作成します。

1. 作成した登録グループを開き、 **[プライマリの管理]** を選択します。

1. ファイル オプションを選択し、先ほど生成した _mytestrootcert_cert.pem_ という名前のルート証明書ファイルをアップロードします。

    ![証明書のアップロード](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 検証を完了するには、確認コードを生成し、それをコピーした後、それを使用してコマンド プロンプトで X.509 検証証明書を作成します。

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 署名された検証証明書 _verification_cert.pem_ をアップロードして検証を完了します。

    ![検証済みの証明書](./media/how-to-connect-devices-x509/verified.png)

これで、このプライマリ ルート証明書から派生した X.509 証明書があるデバイスを接続できます。 登録グループを保存した後、ID スコープをメモしておきます。

## <a name="run-sample-device-code"></a>サンプル デバイス コードを実行する

1. Azure IoT Central アプリケーションで、 **[デバイス]** を選択し、**環境センサー** デバイス テンプレートから **デバイス ID** として _mytestdevice_ を使用する新しいデバイスを作成します。

1. _mytestdevice_key.pem_ と _mytestdevice_cert.pem_ ファイルを、_environmentalSensor.js_ アプリケーションが含まれているフォルダーにコピーします。 このアプリケーションは、[デバイスの接続 (Node.js) に関するチュートリアル](./tutorial-connect-device-nodejs.md)の完了時に作成されています。

1. environmentalSensor.js アプリケーションが含まれているフォルダーに移動し、次のコマンドを実行して X.509 パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. **environmentalSensor.js** ファイルを編集します。
    - `idScope` 値を、前に書き留めておいた **ID スコープ**に置き換えます。
    - `registrationId` 値を `mytestdevice` に置き換えます。

1. `require` ステートメントを次のように編集します。

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. クライアントを作成するセクションを次のように編集します。

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

1. 接続を開くセクションを次のように編集します。

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    node environmentalSensor.js
    ```

    テレメトリがダッシュボードに表示されることを確認することもできます。

    ![デバイス テレメトリを確認する](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>個別登録を使用する

個別登録の X.509 証明書を使用して、デバイスとソリューションをテストします。 個別登録では、IoT Central アプリケーション内にルートまたは中間の X.509 証明書は存在しません。 デバイスでは、自己署名された X.509 証明書を使用してアプリケーションに接続します。

## <a name="generate-self-signed-device-cert"></a>自己署名されたデバイス証明書を生成する

このセクションでは、自己署名された X.509 証明書を使用して、個別登録用のデバイスを接続します。これらは、単一のデバイスを登録するために使用されます。 自己署名証明書はテスト目的専用です。

スクリプトを実行して、自己署名された X.509 デバイス証明書を作成します。 証明書の名前には、小文字の英数字とハイフンのみを使用します。

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>個別登録を作成する

1. Azure IoT Central アプリケーションで、 **[デバイス]** を選択し、環境センサー デバイス テンプレートから**デバイス ID** として _mytestselfcertprimary_ を使用する新しいデバイスを作成します。 **ID スコープ**の値をメモしておきます。後で使用します。

1. 作成したデバイスを開き、 **[接続]** を選択します。

1. 次のように、 **[接続方法]** として **[個別登録]** を、[メカニズム] として **[証明書 (X.509)]** を選択します。

    ![個別加入](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. プライマリ用のファイル オプションを選択し、先ほど生成した _mytestselfcertprimary_cert.pem_ という名前の証明書ファイルをアップロードします。

1. セカンダリ証明書用のファイル オプションを選択し、_mytestselfcertsecondary_cert.pem_ という名前の証明書ファイルをアップロードします。 次に、 **[保存]** を選択します。

    ![個別登録証明書のアップロード](./media/how-to-connect-devices-x509/individual-enrollment.png)

これで、デバイスに X.509 証明書がプロビジョニングされました。

## <a name="run-a-sample-individual-enrollment-device"></a>サンプルの個別登録デバイス実行する

1. _mytestselfcertprimary_key.pem_ と _mytestselfcertprimary_cert.pem_ ファイルを、environmentalSensor.js アプリケーションが含まれているフォルダーにコピーします。 このアプリケーションは、[デバイスの接続 (Node.js) に関するチュートリアル](./tutorial-connect-device-nodejs.md)の完了時に作成されています。

1. **environmentalSensor.js** ファイルを次のように編集して保存します。
    - `idScope` 値を、前に書き留めておいた **ID スコープ**に置き換えます。
    - `registrationId` 値を `mytestselfcertprimary` に置き換えます。
    - **var deviceCert** を次のように置き換えます。

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    node environmentalSensor.js
    ```

    テレメトリがダッシュボードに表示されることを確認することもできます。

    ![テレメトリの個別登録](./media/how-to-connect-devices-x509/telemetry-primary.png)

_mytestselfcertsecondary_ 証明書に対して、上記の手順を繰り返すことができます。

## <a name="next-steps"></a>次のステップ

これで、X.509 証明書を使用するデバイスを接続する方法を学習できました。推奨される次の手順は、[Azure CLI を使用してデバイスの接続を監視する](howto-monitor-devices-azure-cli.md)方法を確認することです。
